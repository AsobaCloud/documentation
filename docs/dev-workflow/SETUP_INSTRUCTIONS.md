# GitHub Project Setup Instructions

This guide shows you how to discover the IDs needed to customize `PROJECT_ISSUE_WORKFLOW.md` for your GitHub project.

## Prerequisites

1. **GitHub CLI installed**:
   ```bash
   # Install gh CLI
   brew install gh        # macOS
   # or: https://cli.github.com/

   # Verify installation
   gh --version
   ```

2. **Authenticate**:
   ```bash
   gh auth login

   # Ensure you have project scope
   gh auth refresh -s project
   ```

3. **Create a GitHub Project**:
   - Go to your GitHub organization or user profile
   - Click "Projects" → "New project"
   - Choose "Table" or "Board" view
   - Add custom fields as needed

---

## Step 1: Find Your Project ID

### For Organization Projects

```bash
# Replace [ORGANIZATION] with your org name
gh api graphql -f query='
query {
  organization(login: "[ORGANIZATION]") {
    projectsV2(first: 20) {
      nodes {
        id
        number
        title
      }
    }
  }
}' --jq '.data.organization.projectsV2.nodes[] | {id, number, title}'
```

**Example Output:**
```json
{
  "id": "PVT_kwDOAbc123def456",
  "number": 1,
  "title": "My Project"
}
```

### For User Projects

```bash
# Replace [USERNAME] with your GitHub username
gh api graphql -f query='
query {
  user(login: "[USERNAME]") {
    projectsV2(first: 20) {
      nodes {
        id
        number
        title
      }
    }
  }
}' --jq '.data.user.projectsV2.nodes[] | {id, number, title}'
```

**Copy these values:**
- `id` → This is your `[PROJECT_ID]`
- `number` → This is your `[PROJECT_NUMBER]`

---

## Step 2: Find Field IDs

Once you have your Project ID, find all field IDs:

### For Organization Projects

```bash
# Replace [ORGANIZATION] and [PROJECT_NUMBER]
gh api graphql -f query='
query {
  organization(login: "[ORGANIZATION]") {
    projectV2(number: [PROJECT_NUMBER]) {
      fields(first: 20) {
        nodes {
          ... on ProjectV2Field {
            id
            name
            dataType
          }
          ... on ProjectV2SingleSelectField {
            id
            name
            dataType
            options {
              id
              name
            }
          }
        }
      }
    }
  }
}' --jq '.data.organization.projectV2.fields.nodes[]'
```

### For User Projects

```bash
# Replace [USERNAME] and [PROJECT_NUMBER]
gh api graphql -f query='
query {
  user(login: "[USERNAME]") {
    projectV2(number: [PROJECT_NUMBER]) {
      fields(first: 20) {
        nodes {
          ... on ProjectV2Field {
            id
            name
            dataType
          }
          ... on ProjectV2SingleSelectField {
            id
            name
            dataType
            options {
              id
              name
            }
          }
        }
      }
    }
  }
}' --jq '.data.user.projectV2.fields.nodes[]'
```

**Example Output:**
```json
{
  "id": "PVTSSF_lADOAbc123...",
  "name": "Status",
  "dataType": "SINGLE_SELECT",
  "options": [
    {"id": "abc123", "name": "Backlog"},
    {"id": "def456", "name": "In progress"},
    {"id": "ghi789", "name": "Done"}
  ]
}
```

**Copy these values:**
- Field `id` → Use as `[FIELD_ID_STATUS]`, `[FIELD_ID_PRIORITY]`, etc.
- Option `id` values → Use as `[OPTION_ID_BACKLOG]`, `[OPTION_ID_P0]`, etc.

---

## Step 3: Create Configuration File

Create a file to store all your IDs:

```bash
# project-config.sh
# Save this in your repository and source it in scripts

# Project Details
export OWNER="myorg"                          # Your organization or username
export REPO="myproject"                       # Your repository name
export PROJECT_NUMBER="1"                     # Your project number
export PROJECT_ID="PVT_kwDOAbc123def456"     # Your project ID
export PROJECT_TYPE="organization"            # "organization" or "user"

# Field IDs (get from Step 2)
export FIELD_ID_STATUS="PVTSSF_lADOAbc123status"
export FIELD_ID_PRIORITY="PVTSSF_lADOAbc123priority"
export FIELD_ID_DOMAIN="PVTSSF_lADOAbc123domain"
export FIELD_ID_TYPE="PVTSSF_lADOAbc123type"

# Status Options (get from Step 2)
export OPTION_ID_BACKLOG="abc123"
export OPTION_ID_READY="def456"
export OPTION_ID_IN_PROGRESS="ghi789"
export OPTION_ID_IN_REVIEW="jkl012"
export OPTION_ID_DONE="mno345"

# Priority Options (customize for your project)
export OPTION_ID_P0="p0abc123"
export OPTION_ID_P1="p1def456"
export OPTION_ID_P2="p2ghi789"

# Domain Options (customize for your project)
export OPTION_ID_DOMAIN_FRONTEND="frontend123"
export OPTION_ID_DOMAIN_BACKEND="backend456"
export OPTION_ID_DOMAIN_INFRA="infra789"
export OPTION_ID_DOMAIN_DOCS="docs012"

# Type Options (customize for your project)
export OPTION_ID_TYPE_FEATURE="feature123"
export OPTION_ID_TYPE_BUG="bug456"
export OPTION_ID_TYPE_DOCS="docs789"
export OPTION_ID_TYPE_REFACTOR="refactor012"

# Default assignee
export ASSIGNEE="myusername"
```

**Usage in scripts:**
```bash
#!/bin/bash
source project-config.sh

# Now you can use these variables
gh issue create --repo $OWNER/$REPO --title "..." --assignee $ASSIGNEE
```

---

## Step 4: Automated Discovery Script

Create a script to discover all IDs automatically:

```bash
#!/bin/bash
# discover-project-ids.sh [OWNER] [PROJECT_NUMBER] [TYPE]
#
# TYPE: "organization" or "user"
#
# Example:
#   ./discover-project-ids.sh myorg 1 organization
#   ./discover-project-ids.sh myusername 1 user

OWNER="$1"
PROJECT_NUMBER="$2"
PROJECT_TYPE="$3"

if [ -z "$OWNER" ] || [ -z "$PROJECT_NUMBER" ] || [ -z "$PROJECT_TYPE" ]; then
  echo "Usage: $0 [OWNER] [PROJECT_NUMBER] [organization|user]"
  exit 1
fi

echo "=== Discovering Project IDs ==="
echo "Owner: $OWNER"
echo "Project Number: $PROJECT_NUMBER"
echo "Type: $PROJECT_TYPE"
echo ""

# Get Project ID
echo "=== Project Details ==="
if [ "$PROJECT_TYPE" == "organization" ]; then
  gh api graphql -f query="
  query {
    organization(login: \"$OWNER\") {
      projectV2(number: $PROJECT_NUMBER) {
        id
        number
        title
      }
    }
  }" --jq '.data.organization.projectV2 | "PROJECT_ID=\(.id)\nPROJECT_NUMBER=\(.number)\nPROJECT_TITLE=\"\(.title)\""'
else
  gh api graphql -f query="
  query {
    user(login: \"$OWNER\") {
      projectV2(number: $PROJECT_NUMBER) {
        id
        number
        title
      }
    }
  }" --jq '.data.user.projectV2 | "PROJECT_ID=\(.id)\nPROJECT_NUMBER=\(.number)\nPROJECT_TITLE=\"\(.title)\""'
fi

echo ""
echo "=== Field IDs and Options ==="

if [ "$PROJECT_TYPE" == "organization" ]; then
  gh api graphql -f query="
  query {
    organization(login: \"$OWNER\") {
      projectV2(number: $PROJECT_NUMBER) {
        fields(first: 20) {
          nodes {
            ... on ProjectV2Field {
              id
              name
              dataType
            }
            ... on ProjectV2SingleSelectField {
              id
              name
              dataType
              options {
                id
                name
              }
            }
          }
        }
      }
    }
  }" --jq '
  .data.organization.projectV2.fields.nodes[] |
  "# \(.name) (\(.dataType))\nFIELD_ID_\(.name | ascii_upcase | gsub(" "; "_"))=\"\(.id)\"\n" +
  (if .options then
    (.options | map("OPTION_ID_\(.name | ascii_upcase | gsub(" "; "_"))=\"\(.id)\"") | join("\n")) + "\n"
  else "" end)
  '
else
  gh api graphql -f query="
  query {
    user(login: \"$OWNER\") {
      projectV2(number: $PROJECT_NUMBER) {
        fields(first: 20) {
          nodes {
            ... on ProjectV2Field {
              id
              name
              dataType
            }
            ... on ProjectV2SingleSelectField {
              id
              name
              dataType
              options {
                id
                name
              }
            }
          }
        }
      }
    }
  }" --jq '
  .data.user.projectV2.fields.nodes[] |
  "# \(.name) (\(.dataType))\nFIELD_ID_\(.name | ascii_upcase | gsub(" "; "_"))=\"\(.id)\"\n" +
  (if .options then
    (.options | map("OPTION_ID_\(.name | ascii_upcase | gsub(" "; "_"))=\"\(.id)\"") | join("\n")) + "\n"
  else "" end)
  '
fi

echo ""
echo "=== Save these to project-config.sh ==="
```

**Usage:**
```bash
chmod +x discover-project-ids.sh

# For organization project
./discover-project-ids.sh myorg 1 organization > project-config.sh

# For user project
./discover-project-ids.sh myusername 1 user > project-config.sh

# Review the generated config
cat project-config.sh
```

---

## Step 5: Customize PROJECT_ISSUE_WORKFLOW.md

1. **Open the file**:
   ```bash
   vim PROJECT_ISSUE_WORKFLOW.md
   ```

2. **Replace placeholders**:
   - Find all `[OWNER]` → Replace with your organization/username
   - Find all `[REPO]` → Replace with your repository name
   - Find all `[PROJECT_ID]` → Replace with your project ID
   - Find all `[PROJECT_NUMBER]` → Replace with your project number
   - Find all field/option IDs → Replace with values from Step 2

3. **Or use sed** (automated):
   ```bash
   # Source your config
   source project-config.sh

   # Replace placeholders
   sed -i '' \
     -e "s/\[OWNER\]/$OWNER/g" \
     -e "s/\[REPO\]/$REPO/g" \
     -e "s/\[PROJECT_ID\]/$PROJECT_ID/g" \
     -e "s/\[PROJECT_NUMBER\]/$PROJECT_NUMBER/g" \
     PROJECT_ISSUE_WORKFLOW.md

   # Replace field IDs (example - do this for all fields)
   sed -i '' \
     -e "s/\[FIELD_ID_STATUS\]/$FIELD_ID_STATUS/g" \
     -e "s/\[OPTION_ID_BACKLOG\]/$OPTION_ID_BACKLOG/g" \
     PROJECT_ISSUE_WORKFLOW.md
   ```

---

## Step 6: Test Your Configuration

### Test 1: Create an Issue

```bash
source project-config.sh

gh issue create \
  --repo $OWNER/$REPO \
  --title "Test Issue" \
  --body "Testing project workflow" \
  --assignee $ASSIGNEE
```

### Test 2: Add to Project

```bash
# Save as test-add-to-project.sh
source project-config.sh

ISSUE_NUM=$1

issue_id=$(gh api graphql -f query="
query {
  repository(owner: \"$OWNER\", name: \"$REPO\") {
    issue(number: $ISSUE_NUM) {
      id
    }
  }
}" --jq '.data.repository.issue.id')

item_id=$(gh api graphql -f query="
mutation {
  addProjectV2ItemById(input: {
    projectId: \"$PROJECT_ID\"
    contentId: \"$issue_id\"
  }) {
    item {
      id
    }
  }
}" --jq '.data.addProjectV2ItemById.item.id')

echo "Added issue #$ISSUE_NUM to project. Item ID: $item_id"
```

Run:
```bash
chmod +x test-add-to-project.sh
./test-add-to-project.sh 1  # Replace 1 with your test issue number
```

### Test 3: Update Status

```bash
source project-config.sh

ISSUE_NUM=1  # Your test issue

# Get item ID
if [ "$PROJECT_TYPE" == "organization" ]; then
  item_id=$(gh api graphql -f query="
  query {
    organization(login: \"$OWNER\") {
      projectV2(number: $PROJECT_NUMBER) {
        items(first: 100) {
          nodes {
            id
            content { ... on Issue { number } }
          }
        }
      }
    }
  }" --jq ".data.organization.projectV2.items.nodes[] | select(.content.number == $ISSUE_NUM) | .id")
else
  item_id=$(gh api graphql -f query="
  query {
    user(login: \"$OWNER\") {
      projectV2(number: $PROJECT_NUMBER) {
        items(first: 100) {
          nodes {
            id
            content { ... on Issue { number } }
          }
        }
      }
    }
  }" --jq ".data.user.projectV2.items.nodes[] | select(.content.number == $ISSUE_NUM) | .id")
fi

# Update status
gh api graphql -f query="
mutation {
  updateProjectV2ItemFieldValue(input: {
    projectId: \"$PROJECT_ID\"
    itemId: \"$item_id\"
    fieldId: \"$FIELD_ID_STATUS\"
    value: {singleSelectOptionId: \"$OPTION_ID_IN_PROGRESS\"}
  }) {
    projectV2Item { id }
  }
}"

echo "✓ Moved issue #$ISSUE_NUM to In Progress"
```

---

## Common Project Configurations

### Minimal Configuration (Status Only)

If you only want Status field:

```bash
# project-config.sh (minimal)
export OWNER="myorg"
export REPO="myproject"
export PROJECT_NUMBER="1"
export PROJECT_ID="PVT_kwDOAbc123"
export PROJECT_TYPE="organization"

export FIELD_ID_STATUS="PVTSSF_lADOAbc123status"
export OPTION_ID_BACKLOG="abc123"
export OPTION_ID_IN_PROGRESS="def456"
export OPTION_ID_DONE="ghi789"

export ASSIGNEE="myusername"
```

### Full Configuration (All Fields)

```bash
# project-config.sh (full)
# Include Status, Priority, Domain, Type, Size, etc.
# See Step 3 for complete example
```

---

## Troubleshooting

### "Resource not accessible by integration"

Your GitHub token needs the `project` scope:
```bash
gh auth refresh -s project
```

### "Could not resolve to a ProjectV2"

Check your project number and type (organization vs user):
```bash
# List all your organization projects
gh api graphql -f query='query { organization(login: "[ORG]") { projectsV2(first: 20) { nodes { number title } } } }'

# List all your user projects
gh api graphql -f query='query { user(login: "[USER]") { projectsV2(first: 20) { nodes { number title } } } }'
```

### "Field ID not found"

Field IDs change if you recreate the project or field. Re-run Step 2 to get current IDs.

---

## Next Steps

1. ✅ Run discovery script to get all IDs
2. ✅ Create `project-config.sh` with your values
3. ✅ Customize `PROJECT_ISSUE_WORKFLOW.md`
4. ✅ Test with a sample issue
5. ✅ Create workflow scripts for your team
6. ✅ Document in your project README

---

**Helpful Resources:**
- [GitHub Projects API Docs](https://docs.github.com/en/issues/planning-and-tracking-with-projects/automating-your-project/using-the-api-to-manage-projects)
- [GitHub GraphQL Explorer](https://docs.github.com/en/graphql/overview/explorer)
- [GitHub CLI Manual](https://cli.github.com/manual/)
