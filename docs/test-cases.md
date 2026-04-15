# Trello API - Detailed Test Cases

## TC-001: Health Check

### TC-001.1: Verify API is accessible
- **Method:** GET `/members/me`
- **Expected:** 200 OK, response contains `id`, `username`, `email`
- **Validates:** API connectivity and credential validity

### TC-001.2: Verify invalid key returns 401
- **Method:** GET `/members/me` with invalid key/token
- **Expected:** 401 Unauthorized
- **Validates:** Authentication mechanism rejects bad credentials

---

## TC-002: Boards

### Positive Tests

#### TC-002.1: Create a Board
- **Method:** POST `/boards/`
- **Params:** `name`, `desc`, `defaultLists=false`
- **Expected:** 200 OK, board created with matching name/desc, `closed=false`
- **Validates:** Board creation, all required fields in response (`id`, `name`, `desc`, `url`, `shortUrl`, `prefs`)

#### TC-002.2: Get a Board
- **Method:** GET `/boards/{boardId}`
- **Expected:** 200 OK, returned board matches created board
- **Validates:** Board retrieval, response schema (`id`, `name`, `desc`, `closed`, `idOrganization`, `url`, `prefs`, `labelNames`)

#### TC-002.3: Update a Board
- **Method:** PUT `/boards/{boardId}`
- **Params:** `name`, `desc`
- **Expected:** 200 OK, updated values reflected in response
- **Validates:** Board update functionality

#### TC-002.4: Get Board Members
- **Method:** GET `/boards/{boardId}/members`
- **Expected:** 200 OK, array with at least 1 member (the creator)
- **Validates:** Member listing, each member has `id`, `fullName`, `username`

#### TC-002.5: Get Board Labels
- **Method:** GET `/boards/{boardId}/labels`
- **Expected:** 200 OK, array of default labels
- **Validates:** Default label creation on new board

#### TC-002.6: Get a Field on a Board
- **Method:** GET `/boards/{boardId}/name`
- **Expected:** 200 OK, response contains `_value` field
- **Validates:** Single-field retrieval endpoint

### Negative Tests

#### TC-002.7: Get a Board with invalid ID
- **Method:** GET `/boards/invalid_id_12345`
- **Expected:** 400/404
- **Validates:** Invalid ID format handling

#### TC-002.8: Create a Board without name
- **Method:** POST `/boards/`
- **Params:** (none)
- **Expected:** 400/422
- **Validates:** Required field validation

#### TC-002.9: Update a non-existent Board
- **Method:** PUT `/boards/000000000000000000000000`
- **Expected:** 400/404
- **Validates:** Non-existent resource handling

#### TC-002.10: Delete a non-existent Board
- **Method:** DELETE `/boards/000000000000000000000000`
- **Expected:** 400/404
- **Validates:** Delete on non-existent resource

---

## TC-003: Lists

### Positive Tests

#### TC-003.1: Create List - To Do
- **Method:** POST `/lists`
- **Params:** `name=To Do`, `idBoard`
- **Expected:** 200 OK, list created with correct name, belongs to board, `closed=false`
- **Validates:** List creation, response fields (`id`, `name`, `idBoard`, `closed`, `pos`)

#### TC-003.2: Create List - In Progress
- **Method:** POST `/lists`
- **Params:** `name=In Progress`, `idBoard`
- **Expected:** 200 OK
- **Validates:** Multiple lists on same board

#### TC-003.3: Create List - Done
- **Method:** POST `/lists`
- **Params:** `name=Done`, `idBoard`
- **Expected:** 200 OK
- **Validates:** Third list creation

#### TC-003.4: Get a List
- **Method:** GET `/lists/{listId}`
- **Expected:** 200 OK, correct list returned
- **Validates:** List retrieval, response schema

#### TC-003.5: Update a List
- **Method:** PUT `/lists/{listId}`
- **Params:** `name=Backlog`
- **Expected:** 200 OK, name updated
- **Validates:** List rename

#### TC-003.6: Rename List back
- **Method:** PUT `/lists/{listId}`
- **Params:** `name=To Do`
- **Expected:** 200 OK
- **Validates:** Successive updates

#### TC-003.7: Get Lists on Board
- **Method:** GET `/boards/{boardId}/lists`
- **Expected:** 200 OK, array of 3 lists with correct names
- **Validates:** Board-level list retrieval

#### TC-003.8: Archive a List
- **Method:** PUT `/lists/{listId}/closed`
- **Params:** `value=true`
- **Expected:** 200 OK, `closed=true`
- **Validates:** List archiving

#### TC-003.9: Unarchive a List
- **Method:** PUT `/lists/{listId}/closed`
- **Params:** `value=false`
- **Expected:** 200 OK, `closed=false`
- **Validates:** List unarchiving

### Negative Tests

#### TC-003.10: Create List without board ID
- **Method:** POST `/lists`
- **Params:** `name` only
- **Expected:** 400/422
- **Validates:** Required board ID validation

#### TC-003.11: Create List without name
- **Method:** POST `/lists`
- **Params:** `idBoard` only
- **Expected:** 400/422
- **Validates:** Required name validation

#### TC-003.12: Get non-existent List
- **Method:** GET `/lists/000000000000000000000000`
- **Expected:** 400/404
- **Validates:** Non-existent resource handling

---

## TC-004: Cards

### Positive Tests

#### TC-004.1: Create a Card
- **Method:** POST `/cards`
- **Params:** `name`, `desc`, `idList`
- **Expected:** 200 OK, card created with matching data, `closed=false`
- **Validates:** Card creation, response fields (`id`, `name`, `desc`, `idList`, `idBoard`, `url`, `shortUrl`, `pos`)

#### TC-004.2: Create Card with due date
- **Method:** POST `/cards`
- **Params:** `name`, `idList`, `due`
- **Expected:** 200 OK, `due` field is set
- **Validates:** Due date assignment

#### TC-004.3: Get a Card
- **Method:** GET `/cards/{cardId}`
- **Expected:** 200 OK, all expected properties present
- **Validates:** Card retrieval, full response schema

#### TC-004.4: Update a Card
- **Method:** PUT `/cards/{cardId}`
- **Params:** `name`, `desc`
- **Expected:** 200 OK, values updated
- **Validates:** Card update

#### TC-004.5: Move Card to another List
- **Method:** PUT `/cards/{cardId}`
- **Params:** `idList` (In Progress)
- **Expected:** 200 OK, `idList` matches new list
- **Validates:** Card movement between lists

#### TC-004.6: Move Card to Done
- **Method:** PUT `/cards/{cardId}`
- **Params:** `idList` (Done)
- **Expected:** 200 OK, `idList` matches Done list
- **Validates:** Full workflow card movement

#### TC-004.7: Add a Comment
- **Method:** POST `/cards/{cardId}/actions/comments`
- **Params:** `text`
- **Expected:** 200 OK, `type=commentCard`, text matches
- **Validates:** Comment creation

#### TC-004.8: Update a Comment
- **Method:** PUT `/cards/{cardId}/actions/{commentId}/comments`
- **Params:** `text`
- **Expected:** 200 OK, text updated
- **Validates:** Comment editing

#### TC-004.9: Delete a Comment
- **Method:** DELETE `/cards/{cardId}/actions/{commentId}/comments`
- **Expected:** 200 OK
- **Validates:** Comment deletion

#### TC-004.10: Get Cards on Board
- **Method:** GET `/boards/{boardId}/cards`
- **Expected:** 200 OK, array, all cards belong to board
- **Validates:** Board-level card retrieval

#### TC-004.11: Get Cards in List
- **Method:** GET `/lists/{listId}/cards`
- **Expected:** 200 OK, moved card is in the list
- **Validates:** List-level card retrieval

#### TC-004.12: Archive a Card
- **Method:** PUT `/cards/{cardId}`
- **Params:** `closed=true`
- **Expected:** 200 OK, `closed=true`
- **Validates:** Card archiving

### Negative Tests

#### TC-004.13: Create Card without list ID
- **Method:** POST `/cards`
- **Params:** `name` only
- **Expected:** 400/422
- **Validates:** Required list ID validation

#### TC-004.14: Get non-existent Card
- **Method:** GET `/cards/000000000000000000000000`
- **Expected:** 400/404

#### TC-004.15: Update Card with invalid ID
- **Method:** PUT `/cards/invalid_id`
- **Expected:** 400/404

#### TC-004.16: Delete non-existent Card
- **Method:** DELETE `/cards/000000000000000000000000`
- **Expected:** 400/404

---

## TC-005: Checklists

### Positive Tests

#### TC-005.1: Create a Checklist
- **Method:** POST `/checklists`
- **Params:** `idCard`, `name`
- **Expected:** 200 OK, checklist created, belongs to card
- **Validates:** Checklist creation, response fields

#### TC-005.2: Get a Checklist
- **Method:** GET `/checklists/{checklistId}`
- **Expected:** 200 OK, correct data

#### TC-005.3: Update a Checklist
- **Method:** PUT `/checklists/{checklistId}`
- **Params:** `name`
- **Expected:** 200 OK, name updated

#### TC-005.4: Create CheckItem 1
- **Method:** POST `/checklists/{checklistId}/checkItems`
- **Params:** `name`
- **Expected:** 200 OK, `state=incomplete`
- **Validates:** CheckItem creation, default state

#### TC-005.5: Create CheckItem 2
- **Method:** POST `/checklists/{checklistId}/checkItems`
- **Expected:** 200 OK

#### TC-005.6: Mark CheckItem as Complete
- **Method:** PUT `/cards/{cardId}/checkItem/{checkItemId}`
- **Params:** `state=complete`
- **Expected:** 200 OK, `state=complete`
- **Validates:** State toggle

#### TC-005.7: Get Checkitems on Checklist
- **Method:** GET `/checklists/{checklistId}/checkItems`
- **Expected:** 200 OK, 2 items, 1 complete
- **Validates:** Checkitem listing and state

#### TC-005.8: Delete a CheckItem
- **Method:** DELETE `/checklists/{checklistId}/checkItems/{checkItemId2}`
- **Expected:** 200 OK

### Negative Tests

#### TC-005.9: Create Checklist without card ID
- **Expected:** 400/404/422

#### TC-005.10: Get non-existent Checklist
- **Expected:** 400/404

---

## TC-006: Labels

### Positive Tests

#### TC-006.1: Create a Label (Bug - Red)
- **Method:** POST `/labels`
- **Params:** `name=Bug`, `color=red`, `idBoard`
- **Expected:** 200 OK, label created with correct data

#### TC-006.2: Create a second Label (Feature - Green)
- **Method:** POST `/labels`
- **Expected:** 200 OK

#### TC-006.3: Get a Label
- **Method:** GET `/labels/{labelId}`
- **Expected:** 200 OK, correct data

#### TC-006.4: Update a Label
- **Method:** PUT `/labels/{labelId}`
- **Params:** `name=Critical Bug`
- **Expected:** 200 OK, name updated

#### TC-006.5: Add Label to Card
- **Method:** POST `/cards/{cardId}/idLabels`
- **Params:** `value={labelId}`
- **Expected:** 200 OK

#### TC-006.6: Verify Label is on Card
- **Method:** GET `/cards/{cardId}`
- **Expected:** `idLabels` array includes the label

#### TC-006.7: Remove Label from Card
- **Method:** DELETE `/cards/{cardId}/idLabels/{labelId}`
- **Expected:** 200 OK

#### TC-006.8: Delete a Label
- **Method:** DELETE `/labels/{labelId2}`
- **Expected:** 200 OK

### Negative Tests

#### TC-006.9: Create Label without board ID
- **Expected:** 400/422

#### TC-006.10: Create Label with invalid color
- **Params:** `color=rainbow`
- **Expected:** 400/422

#### TC-006.11: Get non-existent Label
- **Expected:** 400/404

---

## TC-007: Members

### Positive Tests

#### TC-007.1: Get Current Member
- **Method:** GET `/members/me`
- **Expected:** Full profile with `id`, `username`, `fullName`, `email`, `url`, `avatarUrl`

#### TC-007.2: Get Member's Boards
- **Method:** GET `/members/me/boards`
- **Expected:** Array containing the test board

#### TC-007.3: Get Member's Organizations
- **Method:** GET `/members/me/organizations`
- **Expected:** 200 OK, array response

#### TC-007.4: Get Member by Username
- **Method:** GET `/members/{username}`
- **Expected:** 200 OK, correct member returned

### Negative Tests

#### TC-007.5: Get non-existent Member
- **Method:** GET `/members/nonexistentuserxyz123456`
- **Expected:** 400/404

---

## TC-008: Search

### Positive Tests

#### TC-008.1: Search for Boards
- **Method:** GET `/search?query=Updated Board&modelTypes=boards`
- **Expected:** 200 OK, response has `boards` array

#### TC-008.2: Search for Cards
- **Method:** GET `/search?query=Updated Card&modelTypes=cards`
- **Expected:** 200 OK, response has `cards` array

#### TC-008.3: Search for Members
- **Method:** GET `/search/members?query={username}`
- **Expected:** 200 OK, array response

### Negative Tests

#### TC-008.4: Search without query
- **Method:** GET `/search`
- **Expected:** 400/422

---

## TC-009: Cleanup

#### TC-009.1: Delete Card
- **Expected:** 200 OK

#### TC-009.2: Delete Card with Due Date
- **Expected:** 200 OK

#### TC-009.3: Delete Board
- **Expected:** 200 OK, all environment variables cleared

#### TC-009.4: Verify Board is Deleted
- **Method:** GET `/boards/{boardId}`
- **Expected:** 400/404
