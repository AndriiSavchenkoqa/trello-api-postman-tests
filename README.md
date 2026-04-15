# Trello API Testing - Postman Collection

A comprehensive Postman collection for testing the main functionality of the Trello REST API. Designed for **Manual QA engineers** who need to validate core Trello API operations with both positive and negative test scenarios.

## What's Included

### Test Folders & Scenarios

| Folder | Methods Tested | Positive Tests | Negative Tests |
|--------|---------------|----------------|----------------|
| **Health Check** | GET | 2 | - |
| **Boards** | POST, GET, PUT, DELETE | 6 | 4 |
| **Lists** | POST, GET, PUT | 9 | 3 |
| **Cards** | POST, GET, PUT, DELETE | 12 | 4 |
| **Checklists** | POST, GET, PUT, DELETE | 8 | 2 |
| **Labels** | POST, GET, PUT, DELETE | 8 | 3 |
| **Members** | GET | 4 | 1 |
| **Search** | GET | 3 | 1 |
| **Cleanup** | DELETE, GET | 4 | - |
| **Total** | | **56** | **18** |

### Test Flow

The collection follows a logical end-to-end workflow:

```
Health Check (verify credentials)
    |
    v
Create Board --> Create Lists (To Do, In Progress, Done)
    |                |
    v                v
Create Cards --> Move Cards between Lists
    |                |
    v                v
Create Checklists --> Add/Complete CheckItems
    |
    v
Create Labels --> Assign Labels to Cards
    |
    v
Search (Boards, Cards, Members)
    |
    v
Cleanup (Delete all test data)
```

### What Each Test Validates

- **Status codes** - Correct HTTP status for success and error cases
- **Response body** - Required fields, correct values, data types
- **Business logic** - Cards move between lists, labels attach to cards, checkitems toggle state
- **Error handling** - Invalid IDs, missing required fields, non-existent resources
- **Response time** - Key endpoints respond within 5 seconds

## Prerequisites

1. **Postman** - Download from [postman.com](https://www.postman.com/downloads/)
2. **Trello Account** - Sign up at [trello.com](https://trello.com)
3. **Trello API Credentials**:
   - **API Key** - Get it from [trello.com/power-ups/admin](https://trello.com/power-ups/admin) (click on "New" or use existing Power-Up, then find your API Key)
   - **API Token** - After getting your API Key, generate a Token by clicking "Token" link on the same page

## Quick Start

### 1. Clone the Repository

```bash
git clone <repository-url>
cd trello-api-testing
```

### 2. Import into Postman

1. Open Postman
2. Click **Import** (top left)
3. Drag and drop or browse to select:
   - `collections/Trello_API_Tests.postman_collection.json`
   - `environments/Trello_API_Testing.postman_environment.json`

### 3. Configure Your Credentials

1. In Postman, click the **Environment** dropdown (top right) and select **"Trello API Testing"**
2. Click the **eye icon** next to the environment dropdown
3. Set the **Current Value** for:
   - `apiKey` - Your Trello API Key
   - `apiToken` - Your Trello API Token
4. Click **Save**

> **Important:** Only fill in `apiKey` and `apiToken`. All other variables are populated automatically during the test run.

### 4. Run the Collection

**Option A: Run the full collection (recommended)**
1. Click on **"Trello API Testing"** collection in the sidebar
2. Click **"Run"** button
3. Make sure the **"Trello API Testing"** environment is selected
4. Click **"Run Trello API Testing"**
5. Tests will execute in order, each building on the previous

**Option B: Run individual folders**
1. Right-click any folder (e.g., "Boards")
2. Click **"Run folder"**
3. Note: Some folders depend on data from previous folders

**Option C: Run individual requests**
1. Click any request
2. Click **"Send"**
3. View test results in the **"Test Results"** tab

## Project Structure

```
trello-api-testing/
├── README.md                          # This file
├── collections/
│   └── Trello_API_Tests.postman_collection.json   # Main test collection
├── environments/
│   └── Trello_API_Testing.postman_environment.json # Environment variables
├── docs/
│   └── test-cases.md                  # Detailed test case documentation
└── .gitignore
```

## Environment Variables Reference

| Variable | Set By | Description |
|----------|--------|-------------|
| `baseUrl` | Pre-configured | `https://api.trello.com/1` |
| `apiKey` | **User (required)** | Your Trello API Key |
| `apiToken` | **User (required)** | Your Trello API Token |
| `boardId` | Auto (tests) | ID of the test board |
| `boardName` | Auto (tests) | Name of the test board |
| `todoListId` | Auto (tests) | ID of the "To Do" list |
| `inProgressListId` | Auto (tests) | ID of the "In Progress" list |
| `doneListId` | Auto (tests) | ID of the "Done" list |
| `cardId` | Auto (tests) | ID of the test card |
| `cardWithDueDateId` | Auto (tests) | ID of card with due date |
| `checklistId` | Auto (tests) | ID of the test checklist |
| `checkItemId` | Auto (tests) | ID of checklist item 1 |
| `checkItemId2` | Auto (tests) | ID of checklist item 2 |
| `labelId` | Auto (tests) | ID of the "Bug" label |
| `labelId2` | Auto (tests) | ID of the "Feature" label |
| `commentId` | Auto (tests) | ID of the test comment |
| `memberId` | Auto (tests) | ID of the authenticated member |
| `memberUsername` | Auto (tests) | Username of the authenticated member |

## Running Order & Dependencies

The collection is designed to run **top-to-bottom in sequence**. Each folder builds on data created by previous folders:

1. **Health Check** - Validates credentials, sets `memberId` and `memberUsername`
2. **Boards** - Creates test board, sets `boardId`
3. **Lists** - Creates lists on the board, sets list IDs
4. **Cards** - Creates cards in lists, sets card IDs
5. **Checklists** - Creates checklists on cards, sets checklist IDs
6. **Labels** - Creates labels, attaches to cards
7. **Members** - Read-only tests using member data
8. **Search** - Searches for created test data
9. **Cleanup** - Deletes all test data and clears variables

> **Note:** Negative tests within each folder are independent and can be run in any order.

## Cleanup

The **Cleanup** folder at the end:
- Deletes all test cards
- Deletes the test board (which cascades to lists, checklists, and labels)
- Clears all auto-generated environment variables

If a test run is interrupted, you can manually clean up by:
1. Going to [trello.com](https://trello.com)
2. Finding and deleting any boards named "Test Board ..." or "Updated Board ..."

## Troubleshooting

| Issue | Solution |
|-------|----------|
| `401 Unauthorized` | Check that `apiKey` and `apiToken` are correctly set in the environment |
| `invalid id` errors | Make sure to run the collection in order (top to bottom) |
| Tests fail with empty variables | Run the Health Check and Boards folders first |
| Rate limiting (429) | Add a small delay between requests in the Collection Runner settings |

## API Documentation

- [Trello REST API Documentation](https://developer.atlassian.com/cloud/trello/rest/)
- [Trello API Introduction](https://developer.atlassian.com/cloud/trello/guides/rest-api/api-introduction/)
- [Authentication & Authorization](https://developer.atlassian.com/cloud/trello/guides/rest-api/authorization/)
