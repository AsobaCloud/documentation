# Project Issue Workflow Guide - Template

Complete guide for managing issues in GitHub Projects using the GitHub CLI and GraphQL API.

> **Customization Required**: Replace all `[PLACEHOLDER]` values with your actual project details.
> See `SETUP_INSTRUCTIONS.md` for how to discover your project's IDs.

## Table of Contents

1. [Project Metadata Reference](#project-metadata-reference)
2. [Creating Issues](#creating-issues)
3. [Adding Issues to Project](#adding-issues-to-project)
4. [Updating Issue Metadata](#updating-issue-metadata)
5. [Progressing Issues Through Workflow](#progressing-issues-through-workflow)
6. [Querying Issues](#querying-issues)
7. [Complete Scripts](#complete-scripts)
8. [Customization Guide](#customization-guide)

---

## Project Metadata Reference

### Project Details

**CUSTOMIZE THESE VALUES:**

```bash
# Your project details (see SETUP_INSTRUCTIONS.md to find these)
OWNER="[ORGANIZATION or USERNAME]"           # e.g., "myorg" or "myusername"
REPO="[REPOSITORY]"                          # e.g., "myproject"
PROJECT_NUMBER="[PROJECT_NUMBER]"            # e.g., "1"
PROJECT_ID="[PROJECT_ID]"                    # e.g., "PVT_kwDOAbc123..."

# Project type (uncomment the one you're using)
# PROJECT_TYPE="organization"  # For organization projects
# PROJECT_TYPE="user"          # For user projects
```

### Available Fields

GitHub Projects v2 supports custom fields. Common fields include:

#### 1. Status (Common to all projects)
**Field ID**: `[FIELD_ID_STATUS]`

| Option | Option ID | Description |
|--------|-----------|-------------|
| Backlog | `[OPTION_ID_BACKLOG]` | New issues awaiting prioritization |
| Ready | `[OPTION_ID_READY]` | Prioritized and ready to start |
| In progress | `[OPTION_ID_IN_PROGRESS]` | Actively being worked on |
| In review | `[OPTION_ID_IN_REVIEW]` | Code review or testing phase |
| Done | `[OPTION_ID_DONE]` | Completed and closed |

#### 2. Priority (Optional - Customize or Remove)
**Field ID**: `[FIELD_ID_PRIORITY]`

| Option | Option ID | Description |
|--------|-----------|-------------|
| P0 | `[OPTION_ID_P0]` | Critical - blocks deployment/users |
| P1 | `[OPTION_ID_P1]` | High - important features/fixes |
| P2 | `[OPTION_ID_P2]` | Medium - nice to have improvements |

#### 3. Domain/Area (Optional - Customize to Your Project)
**Field ID**: `[FIELD_ID_DOMAIN]`

Example options (customize for your project):

| Option | Option ID | Description |
|--------|-----------|-------------|
| Frontend | `[OPTION_ID_DOMAIN_FRONTEND]` | UI, web interfaces |
| Backend | `[OPTION_ID_DOMAIN_BACKEND]` | API, server-side logic |
| Infrastructure | `[OPTION_ID_DOMAIN_INFRA]` | DevOps, deployment, cloud |
| Documentation | `[OPTION_ID_DOMAIN_DOCS]` | Docs, guides, READMEs |

#### 4. Issue Type (Optional - Customize)
**Field ID**: `[FIELD_ID_TYPE]`

| Option | Option ID | Description |
|--------|-----------|-------------|
| Feature | `[OPTION_ID_TYPE_FEATURE]` | New functionality |
| Bug | `[OPTION_ID_TYPE_BUG]` | Fixes for broken behavior |
| Documentation | `[OPTION_ID_TYPE_DOCS]` | Documentation improvements |
| Refactor | `[OPTION_ID_TYPE_REFACTOR]` | Code cleanup, restructuring |

---

## Creating Issues

### Basic Issue Creation

```bash
gh issue create \
  --repo [OWNER]/[REPO] \
  --title "Your Issue Title" \
  --body "Detailed description of the issue" \
  --assignee "[USERNAME]" \
  --label "bug"
```

**Common Labels**: `bug`, `enhancement`, `documentation`, `good first issue`

### Issue Template

```markdown
## Problem
Brief description of what's broken or needed

## Current Behavior
What currently happens

## Expected Behavior
What should happen

## Implementation Steps
1. Step one
2. Step two
3. Step three

## Testing
- [ ] Test case 1
- [ ] Test case 2

## Related Documentation
References to docs, PRs, or issues
```

---

## Adding Issues to Project

### Step 1: Get Issue Node ID

```bash
issue_id=$(gh api graphql -f query='
query {
  repository(owner: "[OWNER]", name: "[REPO]") {
    issue(number: ISSUE_NUMBER) {
      id
    }
  }
}' --jq '.data.repository.issue.id')
```

### Step 2: Add to Project

**For Organization Projects:**
```bash
gh api graphql -f query="
mutation {
  addProjectV2ItemById(input: {
    projectId: \"[PROJECT_ID]\"
    contentId: \"$issue_id\"
  }) {
    item {
      id
    }
  }
}"
```

**For User Projects:**
```bash
gh api graphql -f query="
mutation {
  addProjectV2ItemById(input: {
    projectId: \"[PROJECT_ID]\"
    contentId: \"$issue_id\"
  }) {
    item {
      id
    }
  }
}"
```

*(Note: The mutation is the same, but how you query the project differs)*

### Combined Script

```bash
#!/bin/bash
# add-to-project.sh ISSUE_NUMBER

ISSUE_NUM=$1
OWNER="[OWNER]"
REPO="[REPO]"
PROJECT_ID="[PROJECT_ID]"

# Get issue node ID
issue_id=$(gh api graphql -f query="
query {
  repository(owner: \"$OWNER\", name: \"$REPO\") {
    issue(number: $ISSUE_NUM) {
      id
    }
  }
}" --jq '.data.repository.issue.id')

# Add to project
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

---

## Updating Issue Metadata

### Get Project Item ID

Before updating any field, you need the project item ID (not the issue ID):

**For Organization Projects:**
```bash
item_id=$(gh api graphql -f query='
query {
  organization(login: "[ORGANIZATION]") {
    projectV2(number: [PROJECT_NUMBER]) {
      items(first: 100) {
        nodes {
          id
          content {
            ... on Issue {
              number
            }
          }
        }
      }
    }
  }
}' --jq '.data.organization.projectV2.items.nodes[] | select(.content.number == ISSUE_NUMBER) | .id')
```

**For User Projects:**
```bash
item_id=$(gh api graphql -f query='
query {
  user(login: "[USERNAME]") {
    projectV2(number: [PROJECT_NUMBER]) {
      items(first: 100) {
        nodes {
          id
          content {
            ... on Issue {
              number
            }
          }
        }
      }
    }
  }
}' --jq '.data.user.projectV2.items.nodes[] | select(.content.number == ISSUE_NUMBER) | .id')
```

### Update Status

```bash
gh api graphql -f query="
mutation {
  updateProjectV2ItemFieldValue(input: {
    projectId: \"[PROJECT_ID]\"
    itemId: \"$item_id\"
    fieldId: \"[FIELD_ID_STATUS]\"
    value: {singleSelectOptionId: \"[OPTION_ID_BACKLOG]\"}
  }) {
    projectV2Item { id }
  }
}"
```

### Update Priority (If Using Custom Priority Field)

```bash
# Set to P0 (Critical)
gh api graphql -f query="
mutation {
  updateProjectV2ItemFieldValue(input: {
    projectId: \"[PROJECT_ID]\"
    itemId: \"$item_id\"
    fieldId: \"[FIELD_ID_PRIORITY]\"
    value: {singleSelectOptionId: \"[OPTION_ID_P0]\"}
  }) {
    projectV2Item { id }
  }
}"
```

### Update Custom Field (Domain, Type, etc.)

```bash
gh api graphql -f query="
mutation {
  updateProjectV2ItemFieldValue(input: {
    projectId: \"[PROJECT_ID]\"
    itemId: \"$item_id\"
    fieldId: \"[FIELD_ID_DOMAIN]\"
    value: {singleSelectOptionId: \"[OPTION_ID_DOMAIN_FRONTEND]\"}
  }) {
    projectV2Item { id }
  }
}"
```

---

## Progressing Issues Through Workflow

### Standard Workflow States

```
Backlog → Ready → In progress → In review → Done
```

### Move to Ready

```bash
#!/bin/bash
# move-to-ready.sh ISSUE_NUMBER

ISSUE_NUM=$1
OWNER="[OWNER]"
PROJECT_NUMBER="[PROJECT_NUMBER]"
PROJECT_ID="[PROJECT_ID]"
FIELD_ID_STATUS="[FIELD_ID_STATUS]"
OPTION_ID_READY="[OPTION_ID_READY]"

# Determine project type and query accordingly
# For organization projects:
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

# For user projects, replace 'organization' with 'user' in the query above

gh api graphql -f query="
mutation {
  updateProjectV2ItemFieldValue(input: {
    projectId: \"$PROJECT_ID\"
    itemId: \"$item_id\"
    fieldId: \"$FIELD_ID_STATUS\"
    value: {singleSelectOptionId: \"$OPTION_ID_READY\"}
  }) {
    projectV2Item { id }
  }
}" > /dev/null

echo "✓ Moved issue #$ISSUE_NUM to Ready"
```

### Move to In Progress

```bash
# Status: In progress
gh api graphql -f query="
mutation {
  updateProjectV2ItemFieldValue(input: {
    projectId: \"[PROJECT_ID]\"
    itemId: \"$item_id\"
    fieldId: \"[FIELD_ID_STATUS]\"
    value: {singleSelectOptionId: \"[OPTION_ID_IN_PROGRESS]\"}
  }) {
    projectV2Item { id }
  }
}"
```

### Move to In Review

```bash
# Status: In review
gh api graphql -f query="
mutation {
  updateProjectV2ItemFieldValue(input: {
    projectId: \"[PROJECT_ID]\"
    itemId: \"$item_id\"
    fieldId: \"[FIELD_ID_STATUS]\"
    value: {singleSelectOptionId: \"[OPTION_ID_IN_REVIEW]\"}
  }) {
    projectV2Item { id }
  }
}"
```

### Move to Done

```bash
# Status: Done
gh api graphql -f query="
mutation {
  updateProjectV2ItemFieldValue(input: {
    projectId: \"[PROJECT_ID]\"
    itemId: \"$item_id\"
    fieldId: \"[FIELD_ID_STATUS]\"
    value: {singleSelectOptionId: \"[OPTION_ID_DONE]\"}
  }) {
    projectV2Item { id }
  }
}"
```

---

## Querying Issues

### List All Backlog Issues

**For Organization Projects:**
```bash
gh api graphql -f query='
query {
  organization(login: "[ORGANIZATION]") {
    projectV2(number: [PROJECT_NUMBER]) {
      items(first: 100) {
        nodes {
          fieldValues(first: 20) {
            nodes {
              ... on ProjectV2ItemFieldSingleSelectValue {
                field { ... on ProjectV2SingleSelectField { name } }
                name
              }
            }
          }
          content {
            ... on Issue {
              number
              title
              state
            }
          }
        }
      }
    }
  }
}' --jq '.data.organization.projectV2.items.nodes[] | select(.fieldValues.nodes[] | select(.field.name == "Status") | .name == "Backlog") | {number: .content.number, title: .content.title}'
```

**For User Projects:** Replace `organization(login: "[ORGANIZATION]")` with `user(login: "[USERNAME]")`

### List Issues by Priority

```bash
# List all P0 (Critical) issues
gh api graphql -f query='
query {
  organization(login: "[ORGANIZATION]") {
    projectV2(number: [PROJECT_NUMBER]) {
      items(first: 100) {
        nodes {
          fieldValues(first: 20) {
            nodes {
              ... on ProjectV2ItemFieldSingleSelectValue {
                field { ... on ProjectV2SingleSelectField { name } }
                name
              }
            }
          }
          content {
            ... on Issue {
              number
              title
            }
          }
        }
      }
    }
  }
}' --jq '.data.organization.projectV2.items.nodes[] | select(.fieldValues.nodes[] | select(.field.name == "Priority") | .name == "P0") | {number: .content.number, title: .content.title}'
```

### List In Progress Issues

```bash
gh api graphql -f query='
query {
  organization(login: "[ORGANIZATION]") {
    projectV2(number: [PROJECT_NUMBER]) {
      items(first: 100) {
        nodes {
          fieldValues(first: 20) {
            nodes {
              ... on ProjectV2ItemFieldSingleSelectValue {
                field { ... on ProjectV2SingleSelectField { name } }
                name
              }
            }
          }
          content {
            ... on Issue {
              number
              title
              assignees(first: 5) {
                nodes { login }
              }
            }
          }
        }
      }
    }
  }
}' --jq '.data.organization.projectV2.items.nodes[] | select(.fieldValues.nodes[] | select(.field.name == "Status") | .name == "In progress") | {number: .content.number, title: .content.title, assignees: [.content.assignees.nodes[].login]}'
```

### Get Full Issue Details

```bash
#!/bin/bash
# get-issue-details.sh ISSUE_NUMBER

ISSUE_NUM=$1
OWNER="[ORGANIZATION]"
PROJECT_NUMBER="[PROJECT_NUMBER]"

gh api graphql -f query="
query {
  organization(login: \"$OWNER\") {
    projectV2(number: $PROJECT_NUMBER) {
      items(first: 100) {
        nodes {
          id
          fieldValues(first: 20) {
            nodes {
              ... on ProjectV2ItemFieldSingleSelectValue {
                field { ... on ProjectV2SingleSelectField { name } }
                name
              }
            }
          }
          content {
            ... on Issue {
              number
              title
              state
              url
            }
          }
        }
      }
    }
  }
}" --jq ".data.organization.projectV2.items.nodes[] | select(.content.number == $ISSUE_NUM) | {
  number: .content.number,
  title: .content.title,
  url: .content.url,
  status: (.fieldValues.nodes[] | select(.field.name == \"Status\") | .name),
  priority: (.fieldValues.nodes[] | select(.field.name == \"Priority\") | .name),
  domain: (.fieldValues.nodes[] | select(.field.name == \"Domain\") | .name),
  type: (.fieldValues.nodes[] | select(.field.name == \"Issue Type\") | .name)
}"
```

---

## Complete Scripts

### Full Issue Creation with Metadata

```bash
#!/bin/bash
# create-issue-full.sh "Title" "Description" PRIORITY DOMAIN TYPE

TITLE="$1"
BODY="$2"
PRIORITY="$3"      # P0, P1, or P2
DOMAIN="$4"        # e.g., "Frontend"
TYPE="$5"          # e.g., "Bug"

# Configuration
OWNER="[OWNER]"
REPO="[REPO]"
PROJECT_ID="[PROJECT_ID]"
ASSIGNEE="[ASSIGNEE]"

# Map priority to option ID (customize these)
case $PRIORITY in
  P0) PRIORITY_ID="[OPTION_ID_P0]" ;;
  P1) PRIORITY_ID="[OPTION_ID_P1]" ;;
  P2) PRIORITY_ID="[OPTION_ID_P2]" ;;
  *) echo "Invalid priority. Use P0, P1, or P2"; exit 1 ;;
esac

# Map domain to option ID (customize for your project)
case $DOMAIN in
  "Frontend") DOMAIN_ID="[OPTION_ID_DOMAIN_FRONTEND]" ;;
  "Backend") DOMAIN_ID="[OPTION_ID_DOMAIN_BACKEND]" ;;
  "Infrastructure") DOMAIN_ID="[OPTION_ID_DOMAIN_INFRA]" ;;
  "Documentation") DOMAIN_ID="[OPTION_ID_DOMAIN_DOCS]" ;;
  *) echo "Invalid domain"; exit 1 ;;
esac

# Map type to option ID (customize for your project)
case $TYPE in
  "Feature") TYPE_ID="[OPTION_ID_TYPE_FEATURE]" ;;
  "Bug") TYPE_ID="[OPTION_ID_TYPE_BUG]" ;;
  "Documentation") TYPE_ID="[OPTION_ID_TYPE_DOCS]" ;;
  "Refactor") TYPE_ID="[OPTION_ID_TYPE_REFACTOR]" ;;
  *) echo "Invalid type"; exit 1 ;;
esac

# Create issue
ISSUE_URL=$(gh issue create \
  --repo $OWNER/$REPO \
  --title "$TITLE" \
  --body "$BODY" \
  --assignee $ASSIGNEE \
  --json url --jq '.url')

ISSUE_NUM=$(echo "$ISSUE_URL" | sed 's/.*\/issues\///')

echo "Created issue #$ISSUE_NUM: $ISSUE_URL"

# Get issue node ID
issue_id=$(gh api graphql -f query="
query {
  repository(owner: \"$OWNER\", name: \"$REPO\") {
    issue(number: $ISSUE_NUM) {
      id
    }
  }
}" --jq '.data.repository.issue.id')

# Add to project
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

echo "Added to project. Item ID: $item_id"

# Set metadata (customize field IDs)
gh api graphql -f query="mutation { updateProjectV2ItemFieldValue(input: {projectId: \"$PROJECT_ID\" itemId: \"$item_id\" fieldId: \"[FIELD_ID_STATUS]\" value: {singleSelectOptionId: \"[OPTION_ID_BACKLOG]\"}}) { projectV2Item { id } }}" > /dev/null
echo "✓ Status: Backlog"

gh api graphql -f query="mutation { updateProjectV2ItemFieldValue(input: {projectId: \"$PROJECT_ID\" itemId: \"$item_id\" fieldId: \"[FIELD_ID_PRIORITY]\" value: {singleSelectOptionId: \"$PRIORITY_ID\"}}) { projectV2Item { id } }}" > /dev/null
echo "✓ Priority: $PRIORITY"

gh api graphql -f query="mutation { updateProjectV2ItemFieldValue(input: {projectId: \"$PROJECT_ID\" itemId: \"$item_id\" fieldId: \"[FIELD_ID_DOMAIN]\" value: {singleSelectOptionId: \"$DOMAIN_ID\"}}) { projectV2Item { id } }}" > /dev/null
echo "✓ Domain: $DOMAIN"

gh api graphql -f query="mutation { updateProjectV2ItemFieldValue(input: {projectId: \"$PROJECT_ID\" itemId: \"$item_id\" fieldId: \"[FIELD_ID_TYPE]\" value: {singleSelectOptionId: \"$TYPE_ID\"}}) { projectV2Item { id } }}" > /dev/null
echo "✓ Type: $TYPE"

echo ""
echo "Issue created and configured successfully!"
echo "URL: $ISSUE_URL"
```

### Batch Update Status for Multiple Issues

```bash
#!/bin/bash
# batch-update-status.sh STATUS ISSUE_NUM1 ISSUE_NUM2 ...

STATUS="$1"
shift
ISSUES=("$@")

OWNER="[ORGANIZATION]"
PROJECT_NUMBER="[PROJECT_NUMBER]"
PROJECT_ID="[PROJECT_ID]"
FIELD_ID_STATUS="[FIELD_ID_STATUS]"

# Map status to option ID (customize these)
case $STATUS in
  "Backlog") STATUS_ID="[OPTION_ID_BACKLOG]" ;;
  "Ready") STATUS_ID="[OPTION_ID_READY]" ;;
  "In progress") STATUS_ID="[OPTION_ID_IN_PROGRESS]" ;;
  "In review") STATUS_ID="[OPTION_ID_IN_REVIEW]" ;;
  "Done") STATUS_ID="[OPTION_ID_DONE]" ;;
  *) echo "Invalid status"; exit 1 ;;
esac

for ISSUE_NUM in "${ISSUES[@]}"; do
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

  gh api graphql -f query="
  mutation {
    updateProjectV2ItemFieldValue(input: {
      projectId: \"$PROJECT_ID\"
      itemId: \"$item_id\"
      fieldId: \"$FIELD_ID_STATUS\"
      value: {singleSelectOptionId: \"$STATUS_ID\"}
    }) {
      projectV2Item { id }
    }
  }" > /dev/null

  echo "✓ Issue #$ISSUE_NUM → $STATUS"
done
```

---

## Customization Guide

### Step 1: Find Your Project IDs

See `SETUP_INSTRUCTIONS.md` for detailed instructions on discovering:
- Project ID
- Field IDs
- Option IDs

### Step 2: Update Configuration

Replace all `[PLACEHOLDER]` values in this file with your actual values.

Create a configuration file for your scripts:

```bash
# project-config.sh
export OWNER="[YOUR_ORGANIZATION_or_USERNAME]"
export REPO="[YOUR_REPOSITORY]"
export PROJECT_NUMBER="[YOUR_PROJECT_NUMBER]"
export PROJECT_ID="[YOUR_PROJECT_ID]"

# Field IDs
export FIELD_ID_STATUS="[YOUR_FIELD_ID]"
export FIELD_ID_PRIORITY="[YOUR_FIELD_ID]"

# Option IDs for Status
export OPTION_ID_BACKLOG="[YOUR_OPTION_ID]"
export OPTION_ID_READY="[YOUR_OPTION_ID]"
export OPTION_ID_IN_PROGRESS="[YOUR_OPTION_ID]"
export OPTION_ID_IN_REVIEW="[YOUR_OPTION_ID]"
export OPTION_ID_DONE="[YOUR_OPTION_ID]"

# Add more fields/options as needed
```

Source this in your scripts:
```bash
source project-config.sh
```

### Step 3: Customize Fields

Your project may have different fields. Common customizations:

1. **Rename fields** - Update field names in queries
2. **Add fields** - Add new custom fields to your project
3. **Remove fields** - Delete sections for fields you don't use
4. **Change workflow** - Adjust status options to match your workflow

---

## Best Practices

### Issue Lifecycle

1. **Create** → Status: `Backlog`, assign Priority/Domain/Type
2. **Triage** → Review and adjust Priority, move to `Ready` when prioritized
3. **Start Work** → Move to `In progress`, assign to developer
4. **Submit PR** → Move to `In review`, link PR to issue
5. **Complete** → Move to `Done`, close issue

### Priority Guidelines

- **P0 (Critical)**: Blocks deployment, breaks production, security issues
- **P1 (High)**: Important features, significant bugs, user-impacting issues
- **P2 (Medium)**: Improvements, minor bugs, technical debt

---

## Troubleshooting

### Issue Not Found in Project

```bash
# Verify issue exists
gh issue view ISSUE_NUMBER --repo [OWNER]/[REPO]

# Add to project if missing
# (see "Adding Issues to Project" section)
```

### Permission Errors

Ensure your GitHub token has the following scopes:
- `repo` - Full repository access
- `read:project` - Read project data
- `write:project` - Modify project items (may be bundled with `project` scope)

Update token at: https://github.com/settings/tokens

### Field IDs Changed

If field IDs change, retrieve current IDs (see `SETUP_INSTRUCTIONS.md`).

---

## Quick Reference

### Common Commands

```bash
# Create issue
gh issue create --repo [OWNER]/[REPO] --title "..." --body "..." --assignee [USER]

# Add to project
./add-to-project.sh ISSUE_NUM

# Move through workflow
./move-to-ready.sh ISSUE_NUM
./move-to-in-progress.sh ISSUE_NUM
./move-to-in-review.sh ISSUE_NUM
./move-to-done.sh ISSUE_NUM
```

---

**Last Updated**: 2025-12-27
**Template Version**: 1.0
**See**: `SETUP_INSTRUCTIONS.md` for customization guide
