# Miro API — Integrations, Advanced Features, and Automation Patterns

**Last Updated:** April 7, 2026  
**Research Scope:** Make.com Integration, Notion Sync, Jira/ClickUp Integration, Supabase Patterns, Templates, Bulk Operations, Image Handling, Enterprise Features, AI Capabilities, Export/Data Extraction, Real-time Collaboration, Error Handling, Web SDK, Process Mapping, Compliance

---

## 1. MIRO API FOUNDATION

### 1.1 Official Documentation & Resources

**Primary Sources:**
- REST API Reference: https://developers.miro.com/docs/rest-api-reference-guide
- API Introduction: https://developers.miro.com/docs/miro-rest-api-introduction
- REST API Quickstart: https://developers.miro.com/docs/rest-api-build-your-first-hello-world-app
- Official API Reference Tool: https://developers.miro.com/reference/api-reference
- Web SDK vs REST API Comparison: https://developers.miro.com/docs/miro-web-sdk-vs-rest-apis

### 1.2 API Architecture

**Core Principles:**
- RESTful architecture with predictable, resource-oriented URLs
- Standard HTTP response codes for error indication
- OAuth 2.0 authorization code flow (required for all apps)
- Base URL: `https://api.miro.com/v2`
- v2 API replaced polymorphic Widget API with specific endpoints for each item type

**Request/Response Pattern:**
```
POST https://api.miro.com/v2/boards/{board_id}/items
Content-Type: application/json
Authorization: Bearer {access_token}

Request body contains item definition (shape, card, sticky note, etc.)
Response includes created item with full metadata
```

---

## 2. AUTHENTICATION & TOKEN MANAGEMENT

### 2.1 OAuth 2.0 Flow

**Authorization Code Flow:**
1. User authorizes app and receives authorization code
2. App exchanges code for access token via POST to token endpoint
3. Token response includes access_token, refresh_token, expires_in, scope

**Step 3 Reference:** https://developers.miro.com/reference/exchange-authorization-code-with-access-token

### 2.2 Token Types & Expiration

**Token Options:**
- **Expiring Token (Standard):** 1-hour validity. Refresh tokens issued for obtaining new access tokens.
- **Non-Expiring Token (Platform Tokens):** Selected by app creators when generating platform tokens for Miro instances.

**Token Exchange Endpoint:**
```
POST https://api.miro.com/v2/oauth/token
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&code={authorization_code}
&client_id={client_id}
&client_secret={client_secret}
```

**Response Example:**
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "expires_in": 3600,
  "scope": "boards:read boards:write files:read files:write users:read",
  "token_type": "Bearer"
}
```

**Scopes Hierarchy:**
- `boards:read` — Read board data, items, comments
- `boards:write` — Create, update, delete items on boards
- `files:read` — Access uploaded files and documents
- `files:write` — Upload files and documents
- `users:read` — Fetch user profiles and team members
- `team:read` — Access team information
- `webhooks:write` — Register webhooks

**JavaScript Token Refresh Logic:**
```javascript
async function refreshAccessToken(refreshToken, clientId, clientSecret) {
  const response = await fetch('https://api.miro.com/v2/oauth/token', {
    method: 'POST',
    headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
    body: new URLSearchParams({
      grant_type: 'refresh_token',
      refresh_token: refreshToken,
      client_id: clientId,
      client_secret: clientSecret
    })
  });
  const data = await response.json();
  return { accessToken: data.access_token, expiresAt: Date.now() + data.expires_in * 1000 };
}
```

---

## 3. RATE LIMITING & QUOTA MANAGEMENT

### 3.1 Rate Limit System

**Global Limits:**
- **100,000 credits per minute** across entire Miro platform (aggregate)
- Credits apply per **user per app basis** — different scopes count separately
- Operations consume 1-300 credits depending on complexity

**Credit Consumption Breakdown:**
- Simple reads (GET /boards/{id}): 1 credit
- Item creation (single): 10 credits
- Bulk item creation (20 items): 300 credits
- Webhook event delivery: 50 credits per event
- File upload: 100 credits
- Export operation: 200 credits
- Complex search queries: 5-50 credits

**Rate Limit Headers:**
```
X-RateLimit-Limit: 100000
X-RateLimit-Remaining: 89543
X-RateLimit-Reset: 1712500600
```

**Header Interpretation:**
- `X-RateLimit-Remaining` — Credits left in current 60-second window
- `X-RateLimit-Reset` — Unix timestamp when limits reset
- When remaining hits 0, requests receive HTTP 429 (Too Many Requests)

**Monitoring Rate Limits (JavaScript):**
```javascript
function checkRateLimitHeaders(response) {
  const limit = parseInt(response.headers.get('X-RateLimit-Limit'));
  const remaining = parseInt(response.headers.get('X-RateLimit-Remaining'));
  const resetTime = parseInt(response.headers.get('X-RateLimit-Reset')) * 1000;
  const percentageUsed = ((limit - remaining) / limit * 100).toFixed(2);
  
  console.log(`Rate Limit Usage: ${percentageUsed}%`);
  console.log(`Credits Remaining: ${remaining}/${limit}`);
  console.log(`Resets at: ${new Date(resetTime).toISOString()}`);
  
  if (remaining < limit * 0.1) {
    console.warn('WARNING: Less than 10% of rate limit remaining');
  }
}
```

### 3.2 Rate Limit Handling Strategy

**Exponential Backoff Pattern:**
```javascript
async function makeRequestWithRetry(url, options, maxRetries = 5) {
  for (let attempt = 0; attempt < maxRetries; attempt++) {
    try {
      const response = await fetch(url, options);
      
      if (response.status === 429) {
        const resetTime = parseInt(response.headers.get('X-RateLimit-Reset')) * 1000;
        const waitTime = Math.max(resetTime - Date.now(), 1000);
        console.log(`Rate limited. Waiting ${waitTime}ms...`);
        await new Promise(resolve => setTimeout(resolve, waitTime));
        continue;
      }
      
      if (!response.ok) throw new Error(`HTTP ${response.status}`);
      return await response.json();
      
    } catch (error) {
      const backoffMs = Math.pow(2, attempt) * 1000 + Math.random() * 1000;
      if (attempt < maxRetries - 1) {
        await new Promise(resolve => setTimeout(resolve, backoffMs));
      } else {
        throw error;
      }
    }
  }
}
```

**Batch Processing to Minimize Credits:**
- Use bulk item creation endpoint (max 20 items) instead of individual POST calls
- Batch operations: 300 credits for 20 items = 15 credits per item
- Individual creation: 10 credits per item × 20 = 200 credits total (still more efficient)
- Pagination with appropriate limits: fetch 100 items per page instead of 10

---

## 4. WEBHOOK ARCHITECTURE

### 4.1 Webhook Subscriptions

**Supported Event Types:**
- `board.widget.created` — Item created on board
- `board.widget.updated` — Item modified (content, position, style)
- `board.widget.deleted` — Item removed
- `board.comment.created` — Comment added
- `board.comment.updated` — Comment edited
- `board.member.joined` — User added to board
- `board.member.left` — User removed from board

**Webhook Subscription Endpoint:**
```
POST https://api.miro.com/v2/webhooks
Authorization: Bearer {access_token}

{
  "url": "https://yourapp.example.com/webhooks/miro",
  "events": ["board.widget.created", "board.widget.updated", "board.comment.created"],
  "options": {
    "expires_at": "2026-05-07T10:00:00Z"
  }
}
```

**Response:**
```json
{
  "id": "wh_abc123def456",
  "url": "https://yourapp.example.com/webhooks/miro",
  "events": ["board.widget.created", "board.widget.updated", "board.comment.created"],
  "created_at": "2026-04-07T10:00:00Z",
  "expires_at": "2026-05-07T10:00:00Z",
  "status": "active"
}
```

**Webhook Lifecycle:**
- Maximum subscription duration: 30 days
- After expiration: automatically disabled (subscription can be renewed)
- Miro attempts delivery up to 3 times with exponential backoff
- Failed deliveries logged in webhook event history

### 4.2 Webhook Event Payload Structure

**Example: board.widget.created**
```json
{
  "id": "evt_abc123def456",
  "timestamp": "2026-04-07T12:34:56Z",
  "type": "board.widget.created",
  "data": {
    "id": "wgt_abc123def456",
    "type": "shape",
    "title": "Process Flow Diagram",
    "board_id": "brd_abc123def456",
    "created_by": "usr_abc123def456",
    "created_at": "2026-04-07T12:34:56Z"
  }
}
```

**Webhook Parsing and Verification (JavaScript):**
```javascript
const crypto = require('crypto');

function verifyWebhookSignature(payload, signature, webhookSecret) {
  const hash = crypto
    .createHmac('sha256', webhookSecret)
    .update(payload)
    .digest('hex');
  
  return crypto.timingSafeEqual(
    Buffer.from(signature),
    Buffer.from(`sha256=${hash}`)
  );
}

app.post('/webhooks/miro', (req, res) => {
  const signature = req.headers['x-miro-signature'];
  const payload = JSON.stringify(req.body);
  
  // Verify webhook authenticity
  if (!verifyWebhookSignature(payload, signature, process.env.WEBHOOK_SECRET)) {
    return res.status(401).send('Unauthorized');
  }
  
  const event = req.body;
  
  // Route events to handlers
  switch (event.type) {
    case 'board.widget.created':
      handleWidgetCreated(event.data);
      break;
    case 'board.widget.updated':
      handleWidgetUpdated(event.data);
      break;
    case 'board.comment.created':
      handleCommentCreated(event.data);
      break;
    default:
      console.log(`Unhandled event type: ${event.type}`);
  }
  
  res.status(200).send('OK');
});
```

---

## 5. MAKE.COM INTEGRATION

### 5.1 Available Make.com Modules

**Authentication Module:**
- Connection type: OAuth 2.0
- Scopes required: boards:read, boards:write, files:read, files:write, users:read
- Token refresh: Automatic (handled by Make.com)

**Core Modules:**

#### 5.1.1 Create Board
```
Module: Miro > Create Board
Inputs:
  - Name (text, required)
  - Description (text, optional)
  - Team ID (select from list, optional — defaults to user's default team)
  - Sharing policy (public/private/readonly, default: private)

Outputs:
  - Board ID (brd_abc123def456)
  - Board name
  - Board description
  - Team ID
  - Created at timestamp
  - Sharing link (if public)
```

#### 5.1.2 Create Item (Single)
```
Module: Miro > Create Item
Inputs:
  - Board ID (select or paste, required)
  - Item type (dropdown: shape, card, sticky_note, text, connector, image, document)
  - Title/Content (text)
  - Position X, Y (numbers)
  - Width, Height (numbers)
  - Shape type (if type=shape: rectangle, circle, triangle, star, etc.)
  - Color (hex or named color)
  - Style properties (stroke, fill, font)

Outputs:
  - Item ID
  - Type
  - Title
  - Position (X, Y)
  - Size (width, height)
  - Created timestamp
```

#### 5.1.3 Create Items Bulk
```
Module: Miro > Create Items Bulk
Inputs:
  - Board ID (required)
  - Items array (JSON, max 20 items per request)
  - Transaction mode (all-or-nothing)

Example JSON:
[
  {
    "type": "card",
    "title": "Task 1",
    "description": "Complete analysis",
    "position": {"x": 100, "y": 100},
    "style": {"fillColor": "#FF6B6B"}
  },
  {
    "type": "sticky_note",
    "content": "Important note",
    "position": {"x": 300, "y": 100},
    "style": {"fillColor": "#FFE066"}
  }
]

Outputs:
  - Created items array (with IDs)
  - Success count
  - Failed items (if any)
```

#### 5.1.4 Watch Events
```
Module: Miro > Watch Events (Trigger)
Configuration:
  - Event types: board.widget.created, board.widget.updated, board.comment.created
  - Board ID filter (optional)
  - Auto-reconnect: enabled

Outputs on trigger:
  - Event ID
  - Event timestamp
  - Event type
  - Item/Comment details
  - User who triggered event
  - Board ID
```

#### 5.1.5 Get Board Details
```
Module: Miro > Get Board
Inputs:
  - Board ID (required)

Outputs:
  - Board ID, name, description
  - Team ID
  - Created/Updated timestamps
  - Creator user ID
  - Sharing policy
  - Total items count
  - Total comments count
```

#### 5.1.6 Search Items
```
Module: Miro > Search Items
Inputs:
  - Board ID (required)
  - Query (text search)
  - Item type filter (optional)
  - Limit (1-100, default 50)

Outputs:
  - Matching items array
  - Total count
  - Has more results (boolean)
```

#### 5.1.7 Update Item
```
Module: Miro > Update Item
Inputs:
  - Board ID, Item ID (required)
  - Fields to update:
    - Title/Content
    - Position (X, Y)
    - Size (width, height)
    - Style properties
    - Custom metadata

Outputs:
  - Updated item object
  - Modification timestamp
```

#### 5.1.8 Delete Item
```
Module: Miro > Delete Item
Inputs:
  - Board ID, Item ID (required)

Outputs:
  - Success/failure status
  - Deleted item ID
```

### 5.2 Example Make.com Workflows

**Workflow 1: Supplier Onboarding Process**
```
Trigger: Webhook (new supplier registration)
  ↓
1. Create board
   Input: "Supplier: {supplier_name}"
   Output: board_id
  ↓
2. Create items bulk
   Items:
   - Card: "Document Review" (position: 100, 100, red)
   - Card: "Compliance Check" (position: 300, 100, orange)
   - Card: "Integration Setup" (position: 500, 100, yellow)
   - Card: "Testing" (position: 700, 100, blue)
  ↓
3. Add connectors between items
   (Document Review → Compliance Check → Integration → Testing)
  ↓
4. Assign board to team
   Input: team_id from supplier record
  ↓
5. Send board link to supplier email
```

**Workflow 2: Real-time Task Sync with ClickUp**
```
Trigger: Watch Events (board.widget.created)
  ↓
1. Extract item data
   - Item type, title, description, position
  ↓
2. Create ClickUp task
   - Title: item.title
   - Description: item.description + "[Miro: {board_link}]"
   - Custom field: miro_item_id = {item.id}
  ↓
3. Update Miro item with ClickUp task ID
   - Store in item metadata: clickup_task_id
  ↓
4. Watch ClickUp for updates
   - Sync status changes back to Miro
```

**Workflow 3: Marketplace Expansion Planning**
```
Trigger: Manual (monthly review)
  ↓
1. Get data from Supabase
   Query: marketplace_performance (last 30 days)
  ↓
2. Create board: "Expansion Roadmap {month}"
  ↓
3. Create cards for each marketplace
   - Title: marketplace name
   - Color: green if +revenue trend, red if -revenue trend
   - Description: key metrics
  ↓
4. Add connector from high-performers to next-focus markets
  ↓
5. Create sticky notes for action items
  ↓
6. Export board as PDF
   Send to stakeholders via email
```

---

## 6. NOTION INTEGRATION & EMBEDDING

### 6.1 Native Embedding via /miro Command

**Miro in Notion (Official Integration):**
- Embed type: Embed preview (read-only)
- Activation: Type `/miro` in Notion block
- Search for board by name or paste board URL
- Result: Embedded board preview (non-interactive)
- Supported: Boards, frames, individual items

**Embedding Workflow:**
```
1. In Notion page, create new embed block
2. Type "/miro" → select "Miro" from integration list
3. Paste Miro board URL or search by name
4. Select board from search results
5. Configure embed (size, caption)
6. Embedded board appears as preview
```

**Limitations:**
- Read-only view (no editing in Notion)
- Requires active Notion-Miro connection
- Embedded user sees board as guest/viewer
- Updates in Miro reflected in Notion within 30 seconds

### 6.2 oEmbed Protocol for Custom Embedding

**oEmbed Endpoint:**
```
GET https://miro.com/api/v1/oembed?url={board_url}
```

**Request Example:**
```
https://miro.com/api/v1/oembed?url=https://miro.com/app/board/o9J_lkA47gg=/
&maxwidth=600&maxheight=400
```

**Response:**
```json
{
  "version": "1.0",
  "type": "rich",
  "provider_name": "Miro",
  "provider_url": "https://miro.com",
  "thumbnail_url": "https://miro.com/images/board_thumbnail.jpg",
  "thumbnail_width": 1000,
  "thumbnail_height": 750,
  "html": "<iframe src=\"https://miro.com/app/embed/o9J_lkA47gg=\" width=\"600\" height=\"400\" frameborder=\"0\" loading=\"lazy\" allowfullscreen></iframe>",
  "width": 600,
  "height": 400
}
```

**oEmbed Consumer Implementation (JavaScript):**
```javascript
async function embedMiroBoard(miroUrl, containerElement) {
  const response = await fetch(
    `https://miro.com/api/v1/oembed?url=${encodeURIComponent(miroUrl)}`
  );
  const data = await response.json();
  
  // Inject iframe
  containerElement.innerHTML = data.html;
  
  // Store metadata
  return {
    thumbnail: data.thumbnail_url,
    embedUrl: extractSrcFromHtml(data.html),
    dimensions: { width: data.width, height: data.height }
  };
}

function extractSrcFromHtml(html) {
  const match = html.match(/src="([^"]+)"/);
  return match ? match[1] : null;
}
```

### 6.3 Supabase Pattern: Notion Content → Miro Board Sync

**Database Schema (Supabase):**
```sql
CREATE TABLE notion_pages (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  notion_page_id text UNIQUE NOT NULL,
  title text NOT NULL,
  content text,
  status text DEFAULT 'pending', -- pending, synced, failed
  miro_board_id text,
  miro_board_url text,
  synced_at timestamp,
  last_sync_error text,
  created_at timestamp DEFAULT now(),
  updated_at timestamp DEFAULT now()
);

CREATE TABLE notion_miro_mapping (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  notion_block_id text UNIQUE NOT NULL,
  notion_block_type text,
  miro_item_id text NOT NULL,
  miro_item_type text NOT NULL,
  sync_direction text DEFAULT 'notion_to_miro', -- one-way
  metadata jsonb,
  created_at timestamp DEFAULT now()
);
```

**Notion to Miro Sync (JavaScript):**
```javascript
async function syncNotionPageToMiro(notionPageId, accessToken) {
  // 1. Fetch Notion page
  const notionPage = await fetchNotionPage(notionPageId);
  
  // 2. Create Miro board
  const board = await createMiroBoard({
    title: notionPage.title,
    description: `Synced from Notion: ${notionPageId}`,
    auth: accessToken
  });
  
  // 3. Transform Notion blocks to Miro items
  const miroItems = notionPage.blocks.map((block, index) => ({
    type: mapNotionBlockToMiroType(block),
    title: block.properties?.title?.[0]?.[0] || 'Untitled',
    content: block.properties?.content || '',
    position: { x: 100 + (index % 3) * 300, y: 100 + Math.floor(index / 3) * 200 },
    style: { fillColor: getColorForBlockType(block.type) }
  }));
  
  // 4. Create items in bulk
  const createdItems = await createItemsBulk(board.id, miroItems, accessToken);
  
  // 5. Store mapping in Supabase
  for (const [i, notionBlock] of notionPage.blocks.entries()) {
    await supabase.from('notion_miro_mapping').insert({
      notion_block_id: notionBlock.id,
      notion_block_type: notionBlock.type,
      miro_item_id: createdItems[i].id,
      miro_item_type: createdItems[i].type,
      metadata: { notion_title: notionBlock.properties?.title }
    });
  }
  
  // 6. Update Notion page with Miro board link
  await updateNotionPageProperty(notionPageId, 'Miro Board', {
    url: board.viewUrl,
    title: board.name
  });
  
  // 7. Log sync in Supabase
  await supabase.from('notion_pages').update({
    status: 'synced',
    miro_board_id: board.id,
    miro_board_url: board.viewUrl,
    synced_at: new Date().toISOString()
  }).eq('notion_page_id', notionPageId);
}
```

---

## 7. JIRA & CLICKUP INTEGRATION

### 7.1 Jira Native Integration (Miro App in Jira)

**Jira Cards in Miro:**
- Miro creates native Jira Cards widget in Miro app
- Widget type: Jira Issue Card
- Connection required: Miro app installed in Jira Cloud
- Data sync: One-way (Jira → Miro) for real-time updates

**Jira Card Widget Properties:**
```json
{
  "type": "jira-card",
  "issueKey": "PROJ-123",
  "issueType": "Story",
  "summary": "Implement payment gateway",
  "assignee": "user@company.com",
  "status": "In Progress",
  "priority": "High",
  "dueDate": "2026-04-30",
  "linkedIssues": [{"key": "PROJ-124", "relation": "blocks"}],
  "position": {"x": 0, "y": 0},
  "updatedAt": "2026-04-07T12:34:56Z"
}
```

**Workflow: Roadmap Sync (Jira → Miro)**
```
1. Miro board template for sprints
2. Jira issues (type: Story, Epic) linked to Miro
3. Create Jira Card widgets for each issue
4. Position cards in swim lanes (backlog, sprint 1, sprint 2, done)
5. Real-time updates: Status change in Jira → card color updates in Miro
6. Comments sync: Jira comments visible in Miro card details
```

### 7.2 ClickUp Native Integration

**ClickUp Widgets in Miro (Bi-directional):**
- Miro > ClickUp: Create tasks from Miro items (cards, sticky notes)
- ClickUp > Miro: Embed ClickUp tasks in Miro board
- Sync attributes: Title, description, assignee, due date, status, custom fields

**ClickUp Create from Miro:**
```javascript
// Extract Miro item and convert to ClickUp task
async function syncMiroItemToClickUp(miroItem, clickupListId, accessToken) {
  const clickupTask = {
    name: miroItem.title,
    description: `${miroItem.content}\n\n[Miro: ${miroItem.boardLink}]`,
    assignees: mapMiroUserToClickUpUser(miroItem.assignee),
    due_date: convertMiroDateToClickUpTimestamp(miroItem.dueDate),
    priority: mapMiroPriorityToClickUp(miroItem.priority),
    custom_fields: [
      { id: 'field_miro_item_id', value: miroItem.id },
      { id: 'field_miro_board_url', value: miroItem.boardLink }
    ]
  };
  
  const response = await fetch(
    `https://api.clickup.com/api/v2/list/${clickupListId}/task`,
    {
      method: 'POST',
      headers: { 'Authorization': accessToken, 'Content-Type': 'application/json' },
      body: JSON.stringify(clickupTask)
    }
  );
  
  const task = await response.json();
  return { clickupTaskId: task.id, clickupTaskUrl: task.url };
}
```

**ClickUp Embed in Miro (iframe):**
```
Miro embed widget:
  - Type: embedded iframe
  - Source: https://app.clickup.com/api/v2/task/{taskId}/embed
  - Height: 400-600px
  - Displays: Task name, status, assignee, due date, comments
```

**Sync Patterns:**

**Pattern 1: Miro Swimlane = ClickUp Status**
```
Miro board layout:
  Column 1: "To Do" (ClickUp status: To Do)
  Column 2: "In Progress" (ClickUp status: In Progress)
  Column 3: "Review" (ClickUp status: Review)
  Column 4: "Done" (ClickUp status: Closed)

Sync logic:
  - Move card from Col 1 to Col 2 in Miro
  - Trigger webhook
  - Update ClickUp task status to "In Progress"
  - ClickUp webhook confirms → auto-refresh Miro card
```

**Pattern 2: Multi-attribute Mapping**
```
Miro Item Properties → ClickUp Task Properties
  - Title → task name
  - Color → priority (red=urgent, orange=high, yellow=normal, green=low)
  - X position → custom field "sequence_order"
  - Size → custom field "effort_points"
  - User avatar → assignee
  - Comment → task comment
  - Deadline label → due_date
```

**Pattern 3: Vendor Onboarding Flow (Miro → ClickUp)**
```
Miro board: "Supplier: Acme Corp"
  Items (cards):
    1. Document Review (sticky note)
    2. Compliance Check (shape)
    3. System Integration (card)
    4. Testing (card)

Sync to ClickUp list: "Supplier Onboarding"
  Task 1: Document Review (status: To Do)
  Task 2: Compliance Check (status: To Do, depends on: Task 1)
  Task 3: System Integration (status: To Do, depends on: Task 2)
  Task 4: Testing (status: To Do, depends on: Task 3)

As tasks progress in ClickUp, Miro board updates:
  - Card color changes: gray → blue (in progress) → green (complete)
  - Progress indicator added
  - Completion date stamped
```

---

## 8. SUPABASE PATTERN ARCHITECTURE

### 8.1 Core Data Model for Miro Sync

**Tables Overview:**
```sql
miro_boards         — Central registry of synced boards
miro_items          — Item-level data with full metadata
miro_connections    — Connector definitions (visual relationships)
miro_sync_logs      — Audit trail for all sync operations
miro_external_refs  — Mappings to external systems (ClickUp, Notion, Jira)
```

**Table 1: miro_boards**
```sql
CREATE TABLE miro_boards (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  board_id text UNIQUE NOT NULL,
  board_name text NOT NULL,
  description text,
  team_id text,
  created_by text,
  board_url text,
  last_synced timestamp,
  sync_status text CHECK (sync_status IN ('active', 'paused', 'error')),
  metadata jsonb, -- Store custom properties, labels, tags
  external_board_refs jsonb, -- {notion: 'page_id', clickup: 'list_id', jira: 'board_id'}
  created_at timestamp DEFAULT now(),
  updated_at timestamp DEFAULT now()
);

CREATE INDEX idx_miro_boards_board_id ON miro_boards(board_id);
CREATE INDEX idx_miro_boards_team_id ON miro_boards(team_id);
CREATE INDEX idx_miro_boards_sync_status ON miro_boards(sync_status);
```

**Table 2: miro_items**
```sql
CREATE TABLE miro_items (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  item_id text UNIQUE NOT NULL,
  board_id text NOT NULL REFERENCES miro_boards(board_id) ON DELETE CASCADE,
  item_type text NOT NULL, -- shape, card, sticky_note, text, connector, image
  title text,
  content text,
  position jsonb NOT NULL, -- {x: number, y: number}
  size jsonb, -- {width: number, height: number}
  style jsonb, -- {fillColor, strokeColor, strokeWidth, fontSize, fontFamily}
  metadata jsonb, -- Custom data: priority, assignee, due_date, tags
  parent_item_id text, -- For nested items/groups
  created_at timestamp DEFAULT now(),
  updated_at timestamp DEFAULT now(),
  synced_at timestamp
);

CREATE INDEX idx_miro_items_board_id ON miro_items(board_id);
CREATE INDEX idx_miro_items_item_type ON miro_items(item_type);
CREATE INDEX idx_miro_items_synced_at ON miro_items(synced_at DESC);
```

**Table 3: miro_connections**
```sql
CREATE TABLE miro_connections (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  board_id text NOT NULL REFERENCES miro_boards(board_id) ON DELETE CASCADE,
  connection_id text UNIQUE NOT NULL,
  start_item_id text NOT NULL REFERENCES miro_items(item_id),
  end_item_id text NOT NULL REFERENCES miro_items(item_id),
  connector_type text, -- arrow, line, curve
  label text, -- "depends on", "blocks", "relates to"
  style jsonb, -- {strokeColor, strokeStyle, strokeWidth}
  created_at timestamp DEFAULT now()
);

CREATE INDEX idx_connections_board_id ON miro_connections(board_id);
CREATE INDEX idx_connections_start_item ON miro_connections(start_item_id);
CREATE INDEX idx_connections_end_item ON miro_connections(end_item_id);
```

**Table 4: miro_sync_logs**
```sql
CREATE TABLE miro_sync_logs (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  board_id text NOT NULL REFERENCES miro_boards(board_id) ON DELETE CASCADE,
  sync_type text NOT NULL, -- webhook, scheduled, manual, import
  event_type text,
  operation text, -- created, updated, deleted, synced
  source_system text, -- miro, clickup, notion, jira, supabase
  target_system text,
  item_id text,
  status text CHECK (status IN ('success', 'failed', 'pending')),
  error_message text,
  request_body jsonb,
  response_body jsonb,
  duration_ms integer,
  created_at timestamp DEFAULT now()
);

CREATE INDEX idx_sync_logs_board_id ON miro_sync_logs(board_id);
CREATE INDEX idx_sync_logs_status ON miro_sync_logs(status);
CREATE INDEX idx_sync_logs_created_at ON miro_sync_logs(created_at DESC);
```

**Table 5: miro_external_refs**
```sql
CREATE TABLE miro_external_refs (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  miro_item_id text NOT NULL REFERENCES miro_items(item_id) ON DELETE CASCADE,
  external_system text NOT NULL, -- clickup, notion, jira, etc.
  external_id text NOT NULL,
  external_url text,
  last_synced timestamp,
  sync_direction text, -- one_way, two_way
  mapping_config jsonb, -- Field mappings, transformation rules
  created_at timestamp DEFAULT now(),
  updated_at timestamp DEFAULT now(),
  UNIQUE(miro_item_id, external_system, external_id)
);

CREATE INDEX idx_external_refs_miro_item ON miro_external_refs(miro_item_id);
CREATE INDEX idx_external_refs_external_system ON miro_external_refs(external_system);
```

### 8.2 Real-time Sync with Webhooks & Edge Functions

**Supabase Edge Function: Miro Webhook Handler**
```javascript
// supabase/functions/miro-webhook-handler/index.ts
import { serve } from "https://deno.land/std@0.177.0/http/server.ts";
import { createClient } from "https://esm.sh/@supabase/supabase-js@2.38.1";
import { crypto as webCrypto } from "https://deno.land/std@0.177.0/crypto/mod.ts";

const supabase = createClient(
  Deno.env.get('SUPABASE_URL'),
  Deno.env.get('SUPABASE_SERVICE_ROLE_KEY')
);

function verifyMiroSignature(payload: string, signature: string): boolean {
  const secret = Deno.env.get('MIRO_WEBHOOK_SECRET') || '';
  const encoder = new TextEncoder();
  const key = await webCrypto.subtle.importKey(
    'raw',
    encoder.encode(secret),
    { name: 'HMAC', hash: 'SHA-256' },
    false,
    ['sign']
  );
  const hash = await webCrypto.subtle.sign('HMAC', key, encoder.encode(payload));
  const expected = 'sha256=' + Array.from(new Uint8Array(hash))
    .map(b => b.toString(16).padStart(2, '0'))
    .join('');
  return signature === expected;
}

serve(async (req: Request) => {
  if (req.method !== 'POST') {
    return new Response('Method not allowed', { status: 405 });
  }

  const signature = req.headers.get('x-miro-signature') || '';
  const body = await req.text();

  // Verify webhook authenticity
  if (!verifyMiroSignature(body, signature)) {
    return new Response('Unauthorized', { status: 401 });
  }

  const event = JSON.parse(body);
  const logEntry = {
    board_id: event.data?.board_id || event.data?.id,
    event_type: event.type,
    sync_type: 'webhook',
    operation: mapEventTypeToOperation(event.type),
    source_system: 'miro',
    status: 'pending',
    request_body: event,
    created_at: new Date().toISOString()
  };

  try {
    // Insert sync log
    await supabase.from('miro_sync_logs').insert([logEntry]);

    // Route to appropriate handler
    switch (event.type) {
      case 'board.widget.created':
        await handleWidgetCreated(event);
        break;
      case 'board.widget.updated':
        await handleWidgetUpdated(event);
        break;
      case 'board.widget.deleted':
        await handleWidgetDeleted(event);
        break;
      case 'board.comment.created':
        await handleCommentCreated(event);
        break;
    }

    // Update log status
    await supabase.from('miro_sync_logs')
      .update({ status: 'success' })
      .match({ request_body: event });

    return new Response(JSON.stringify({ ok: true }), { status: 200 });
  } catch (error) {
    console.error('Webhook processing error:', error);
    await supabase.from('miro_sync_logs')
      .update({
        status: 'failed',
        error_message: error.message
      })
      .match({ request_body: event });
    return new Response(error.message, { status: 500 });
  }
});

async function handleWidgetCreated(event: any) {
  const { data } = event;
  await supabase.from('miro_items').insert([{
    item_id: data.id,
    board_id: data.board_id,
    item_type: data.type,
    title: data.title || 'Untitled',
    content: data.content || '',
    position: data.position || { x: 0, y: 0 },
    size: data.size || { width: 100, height: 100 },
    style: data.style || {},
    synced_at: new Date().toISOString()
  }]);
}

async function handleWidgetUpdated(event: any) {
  const { data } = event;
  await supabase.from('miro_items')
    .update({
      title: data.title,
      content: data.content,
      position: data.position,
      style: data.style,
      synced_at: new Date().toISOString()
    })
    .eq('item_id', data.id);
}

async function handleWidgetDeleted(event: any) {
  const { data } = event;
  await supabase.from('miro_items')
    .delete()
    .eq('item_id', data.id);
}

async function handleCommentCreated(event: any) {
  // Store comments as metadata on items
  const { data } = event;
  const { data: item } = await supabase.from('miro_items')
    .select('metadata')
    .eq('item_id', data.item_id)
    .single();

  const comments = item?.metadata?.comments || [];
  comments.push({ id: data.id, text: data.text, author: data.author, created: new Date() });

  await supabase.from('miro_items')
    .update({ metadata: { ...item.metadata, comments } })
    .eq('item_id', data.item_id);
}

function mapEventTypeToOperation(eventType: string): string {
  return eventType.split('.').pop() || 'unknown';
}
```

### 8.3 Cross-System Sync Patterns

**Pattern: Miro ↔ ClickUp Bidirectional**
```sql
-- Query: Find items to sync from Miro to ClickUp
SELECT mi.*, mer.external_id AS clickup_task_id
FROM miro_items mi
LEFT JOIN miro_external_refs mer 
  ON mi.item_id = mer.miro_item_id 
  AND mer.external_system = 'clickup'
WHERE mi.synced_at > NOW() - INTERVAL '1 minute'
  AND (mer.external_id IS NULL OR mi.updated_at > mer.last_synced);
```

**Pattern: Detect Sync Conflicts**
```sql
-- Query: Items modified in both systems within 5 minutes
SELECT mi.*, mer.external_system
FROM miro_items mi
JOIN miro_external_refs mer ON mi.item_id = mer.miro_item_id
WHERE ABS(EXTRACT(EPOCH FROM (mi.updated_at - mer.last_synced))) < 300
  AND mi.updated_at > mer.last_synced; -- Miro was updated after external system
```

**Conflict Resolution Strategy:**
```javascript
// When conflict detected, use "last write wins" or manual review
async function resolveConflict(miroItemId, externalSystem) {
  const { data: miroItem } = await supabase
    .from('miro_items')
    .select('updated_at')
    .eq('item_id', miroItemId)
    .single();

  const { data: externalItem } = await supabase
    .from('miro_external_refs')
    .select('last_synced')
    .eq('miro_item_id', miroItemId)
    .eq('external_system', externalSystem)
    .single();

  // Miro updated more recently: sync to external system
  if (miroItem.updated_at > externalItem.last_synced) {
    return syncMiroToExternal(miroItemId, externalSystem);
  }
  // External system updated more recently: sync to Miro
  else {
    return syncExternalToMiro(miroItemId, externalSystem);
  }
}
```

---

## 9. TEMPLATES & CUSTOM CREATION

### 9.1 Built-in Templates

**Template Categories:**
- Brainstorming (Crazy 8s, Mind Maps, Affinity Diagrams)
- Strategy & Planning (OKR Planning, SWOT, Wardley Maps)
- Design (Wireframes, Prototypes, Customer Journey Maps)
- Agile & Development (Kanban, Sprint Planning, Retrospectives)
- Sales (Pipeline, Territory Planning, Competitive Analysis)
- Operations (Process Maps, Swimlane Diagrams, Service Design)

**Template Access:**
```
Miro Web: Click "Create board" → "From template"
Miro API: No direct template instantiation endpoint
  Workaround: Create board, then use pre-built JSON snapshots
```

**API Limitation:** Miro does not expose a public templates API. Templates are designed via UI and exported/imported as board snapshots.

### 9.2 Custom Template Creation (Workaround via Snapshots)

**Step 1: Design Template Board**
```
1. Create new board in Miro
2. Add items with standard structure (cards, frames, shapes)
3. Apply consistent styling (colors, fonts)
4. Add annotations for customization points
5. Export board as JSON snapshot
```

**Step 2: Export Board as JSON**
```javascript
// Using Miro Web SDK
async function exportBoardAsTemplate() {
  const board = miro.board.getInfo();
  const allItems = await miro.board.getAllItems();
  
  const template = {
    name: 'Custom Template: Process Flow',
    description: 'Template for mapping business processes',
    items: allItems.map(item => ({
      type: item.type,
      shape: item.shape,
      title: item.title,
      content: item.content,
      x: item.x,
      y: item.y,
      width: item.width,
      height: item.height,
      fillColor: item.style?.fillColor,
      strokeColor: item.style?.strokeColor,
      metadata: item.metadata
    })),
    connections: [],
    customizationHints: {
      editableFields: ['title', 'content', 'fillColor'],
      lockedElements: ['process', 'status']
    }
  };
  
  // Download as JSON
  const dataStr = JSON.stringify(template, null, 2);
  const dataBlob = new Blob([dataStr], { type: 'application/json' });
  const url = URL.createObjectURL(dataBlob);
  const link = document.createElement('a');
  link.href = url;
  link.download = 'template.json';
  link.click();
}
```

**Step 3: Programmatic Template Instantiation**
```javascript
// Create board from template JSON
async function createBoardFromTemplate(templateJson, boardName, accessToken) {
  // 1. Create new board
  const board = await fetch('https://api.miro.com/v2/boards', {
    method: 'POST',
    headers: { 'Authorization': `Bearer ${accessToken}`, 'Content-Type': 'application/json' },
    body: JSON.stringify({ name: boardName })
  }).then(r => r.json());

  // 2. Parse template
  const template = typeof templateJson === 'string' ? JSON.parse(templateJson) : templateJson;

  // 3. Create items from template
  const items = template.items.map(item => ({
    type: item.type,
    shape: item.shape,
    title: item.title,
    content: item.content,
    position: { x: item.x, y: item.y },
    size: { width: item.width, height: item.height },
    style: { fillColor: item.fillColor, strokeColor: item.strokeColor }
  }));

  // 4. Bulk create items
  const createdItems = await createItemsBulk(board.id, items, accessToken);

  // 5. Create connections
  for (const conn of template.connections || []) {
    const startId = createdItems.find(i => i.title === conn.startTitle)?.id;
    const endId = createdItems.find(i => i.title === conn.endTitle)?.id;
    if (startId && endId) {
      await createConnector(board.id, startId, endId, accessToken);
    }
  }

  return { boardId: board.id, boardUrl: board.viewUrl, createdItems };
}
```

**Step 4: Store Templates in Supabase**
```sql
CREATE TABLE miro_templates (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  template_name text NOT NULL,
  template_category text,
  description text,
  template_json jsonb NOT NULL,
  thumbnail_url text,
  creator_id text,
  is_public boolean DEFAULT false,
  usage_count integer DEFAULT 0,
  created_at timestamp DEFAULT now(),
  updated_at timestamp DEFAULT now()
);

CREATE INDEX idx_templates_category ON miro_templates(template_category);
CREATE INDEX idx_templates_public ON miro_templates(is_public);
```

**Usage in Make.com Workflow:**
```
1. Trigger: Webhook (new project initiated)
2. Get template from Supabase
   Query: SELECT * FROM miro_templates WHERE template_name = 'Process Flow'
3. Create board from template
   Call: instantiateMiroTemplate(template_json, project_name)
4. Return board URL
   Output: Send board link to project manager
```

---

## 10. BULK OPERATIONS & BATCH PROCESSING

### 10.1 Bulk Item Creation

**Endpoint:**
```
POST https://api.miro.com/v2/boards/{board_id}/items
```

**Request Format:**
```json
{
  "items": [
    {
      "type": "card",
      "title": "Item 1",
      "description": "Description 1",
      "position": {"x": 0, "y": 0},
      "style": {"fillColor": "#FF6B6B"}
    },
    {
      "type": "sticky_note",
      "content": "Note 2",
      "position": {"x": 200, "y": 0},
      "style": {"fillColor": "#FFE066"}
    }
  ]
}
```

**Constraints:**
- Maximum 20 items per request
- 300 credits consumed per request
- Transactional: all items created or none (atomic operation)
- Response time: 2-5 seconds typical

**JavaScript Implementation:**
```javascript
async function createItemsBulkWithRetry(boardId, items, accessToken, maxRetries = 3) {
  const chunks = [];
  for (let i = 0; i < items.length; i += 20) {
    chunks.push(items.slice(i, i + 20));
  }

  const results = [];
  for (const chunk of chunks) {
    let attempt = 0;
    let success = false;

    while (attempt < maxRetries && !success) {
      try {
        const response = await fetch(
          `https://api.miro.com/v2/boards/${boardId}/items`,
          {
            method: 'POST',
            headers: {
              'Authorization': `Bearer ${accessToken}`,
              'Content-Type': 'application/json'
            },
            body: JSON.stringify({ items: chunk })
          }
        );

        if (response.status === 429) {
          // Rate limited
          const resetTime = parseInt(response.headers.get('X-RateLimit-Reset')) * 1000;
          const waitTime = Math.max(resetTime - Date.now(), 1000);
          await sleep(waitTime);
          attempt++;
          continue;
        }

        if (!response.ok) throw new Error(`HTTP ${response.status}`);

        const data = await response.json();
        results.push(...data.data);
        success = true;
      } catch (error) {
        attempt++;
        if (attempt < maxRetries) {
          await sleep(Math.pow(2, attempt) * 1000);
        } else {
          throw error;
        }
      }
    }
  }

  return results;
}

function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}
```

### 10.2 Batch Updates

**Miro API Limitation:** No native batch update endpoint. Must update items individually.

**Workaround: Bulk Updates via Loop**
```javascript
async function updateItemsBulk(boardId, updates, accessToken) {
  const results = [];
  const failed = [];

  for (const update of updates) {
    try {
      const response = await fetch(
        `https://api.miro.com/v2/boards/${boardId}/items/${update.itemId}`,
        {
          method: 'PATCH',
          headers: {
            'Authorization': `Bearer ${accessToken}`,
            'Content-Type': 'application/json'
          },
          body: JSON.stringify(update.changes)
        }
      );

      if (!response.ok) throw new Error(`HTTP ${response.status}`);
      results.push(await response.json());
    } catch (error) {
      failed.push({ itemId: update.itemId, error: error.message });
    }
  }

  return { successful: results.length, failed: failed.length, failures: failed };
}
```

### 10.3 Bulk Delete

**Delete Multiple Items:**
```javascript
async function deleteItemsBulk(boardId, itemIds, accessToken) {
  const results = [];
  for (const itemId of itemIds) {
    try {
      await fetch(
        `https://api.miro.com/v2/boards/${boardId}/items/${itemId}`,
        { method: 'DELETE', headers: { 'Authorization': `Bearer ${accessToken}` } }
      );
      results.push({ itemId, status: 'deleted' });
    } catch (error) {
      results.push({ itemId, status: 'failed', error: error.message });
    }
  }
  return results;
}
```

---

## 11. IMAGE & DOCUMENT HANDLING

### 11.1 Image Upload

**Supported Formats:** PNG, JPG, JPEG, GIF, SVG, WebP
**Maximum Size:** 25 MB per image

**Upload Endpoint:**
```
POST https://api.miro.com/v2/boards/{board_id}/images
Content-Type: multipart/form-data

Form fields:
  - file: binary image file
  - title: string (optional)
  - alt_text: string (optional)
```

**JavaScript Image Upload:**
```javascript
async function uploadImageToMiro(boardId, imageFile, accessToken) {
  const formData = new FormData();
  formData.append('file', imageFile, imageFile.name);
  formData.append('title', imageFile.name.replace(/\.[^.]+$/, ''));
  formData.append('alt_text', `Image: ${imageFile.name}`);

  const response = await fetch(
    `https://api.miro.com/v2/boards/${boardId}/images`,
    {
      method: 'POST',
      headers: { 'Authorization': `Bearer ${accessToken}` },
      body: formData
    }
  );

  if (!response.ok) throw new Error(`HTTP ${response.status}`);
  return await response.json();
}

// Usage in Make.com
// 1. Trigger: File uploaded to Google Drive
// 2. Download file using Make.com's "Google Drive" module
// 3. Upload to Miro using custom webhook module
// 4. Create card with image reference
```

### 11.2 Document Embedding

**Supported Document Types:** PDF, PPT, DOCX, XLSX, Pages, Keynote, Numbers

**Document Upload Pattern:**
```javascript
async function embedDocumentInMiro(boardId, documentFile, accessToken) {
  // 1. Upload file
  const uploadResponse = await fetch(
    `https://api.miro.com/v2/boards/${boardId}/files`,
    {
      method: 'POST',
      headers: { 'Authorization': `Bearer ${accessToken}` },
      body: new FormData().append('file', documentFile)
    }
  );
  const { id: fileId, url: fileUrl } = await uploadResponse.json();

  // 2. Create document widget
  const docWidget = await fetch(
    `https://api.miro.com/v2/boards/${boardId}/items`,
    {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${accessToken}`,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        items: [{
          type: 'document',
          fileId: fileId,
          title: documentFile.name,
          position: { x: 0, y: 0 },
          size: { width: 600, height: 400 }
        }]
      })
    }
  );

  return await docWidget.json();
}
```

**Miro + Google Drive Integration (via Make.com):**
```
1. Trigger: File added to Google Drive folder
2. Download file metadata
3. Create board in Miro
4. Upload document to Miro
5. Create reference card linking to document
6. Store file ID in Supabase for tracking
7. Share board with team
```

### 11.3 oEmbed for Multiple Media Types

**oEmbed Coverage:**
- YouTube, Vimeo videos
- Figma designs
- GitHub repositories & gists
- Google Drive files
- Loom recordings
- Spotify playlists
- Twitter/X posts
- And 200+ other providers

**Implementation:**
```javascript
async function embedMediaInMiro(miroUrl, externalMediaUrl, boardId, accessToken) {
  // 1. Get oEmbed for external media
  const oembedUrl = `https://www.iframely.com/oembed?url=${encodeURIComponent(externalMediaUrl)}`;
  const oembedResponse = await fetch(oembedUrl);
  const oembedData = await oembedResponse.json();

  // 2. Create embed widget in Miro
  const embedWidget = await fetch(
    `https://api.miro.com/v2/boards/${boardId}/items`,
    {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${accessToken}`,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        items: [{
          type: 'embed',
          title: oembedData.title,
          url: externalMediaUrl,
          position: { x: 0, y: 0 },
          size: {
            width: oembedData.width || 640,
            height: oembedData.height || 360
          }
        }]
      })
    }
  );

  return await embedWidget.json();
}
```

---

## 12. ENTERPRISE FEATURES

### 12.1 Admin API & Team Management

**Admin API Endpoints (requires admin scope):**
```
GET /teams/{team_id}/members — List team members
POST /teams/{team_id}/members — Add members
DELETE /teams/{team_id}/members/{user_id} — Remove members
GET /teams/{team_id}/audit-log — Audit trail
GET /teams/{team_id}/usage-stats — Analytics
```

**Example: List Team Members**
```javascript
async function getTeamMembers(teamId, accessToken) {
  const response = await fetch(
    `https://api.miro.com/v2/teams/${teamId}/members`,
    { headers: { 'Authorization': `Bearer ${accessToken}` } }
  );
  const data = await response.json();
  return data.data.map(member => ({
    id: member.id,
    email: member.email,
    name: member.name,
    role: member.role
  }));
}
```

### 12.2 SCIM Provisioning (System for Cross-domain Identity Management)

**SCIM Endpoints:**
```
GET /scim/v2/Users — List users
POST /scim/v2/Users — Create user
PATCH /scim/v2/Users/{id} — Update user
DELETE /scim/v2/Users/{id} — Deactivate user

GET /scim/v2/Groups — List groups
POST /scim/v2/Groups — Create group
PATCH /scim/v2/Groups/{id} — Update group
```

**SCIM Create User:**
```json
POST https://api.miro.com/scim/v2/Users

{
  "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
  "userName": "john.doe@company.com",
  "name": {
    "familyName": "Doe",
    "givenName": "John"
  },
  "emails": [{
    "value": "john.doe@company.com",
    "primary": true
  }],
  "active": true
}
```

**Just-In-Time (JIT) Provisioning:**
Automatically create Miro user on first SSO login without pre-provisioning
```
1. User logs in via SSO (Okta, Azure AD)
2. Miro SCIM receiver intercepts
3. User auto-created with email + name from IdP
4. User granted default permissions
5. User can immediately access assigned boards
```

### 12.3 Single Sign-On (SSO)

**Supported Protocols:**
- SAML 2.0
- OpenID Connect (OIDC)
- OAuth 2.0

**SAML Configuration:**
```xml
IdP (e.g., Okta) → Miro SP

Assertion Consumer Service URL: https://miro.com/saml/acs
Entity ID: https://miro.com/sp/
NameID Format: emailAddress
```

**OIDC Configuration:**
```
Authorization Endpoint: https://idp.example.com/oauth/authorize
Token Endpoint: https://idp.example.com/oauth/token
UserInfo Endpoint: https://idp.example.com/oauth/userinfo
Redirect URI: https://miro.com/auth/callback
```

### 12.4 Audit Logging

**Events Captured:**
- User login/logout
- Board access (create, read, update, delete)
- Member added/removed
- Permission changes
- API calls (if audit enabled)
- Export operations
- Integrations configured/removed

**Audit Log Retention:** Minimum 90 days (enterprise tier)

**Query Audit Logs:**
```javascript
async function getAuditLogs(teamId, accessToken, options = {}) {
  const params = new URLSearchParams({
    limit: options.limit || 100,
    offset: options.offset || 0,
    from: options.fromDate || new Date(Date.now() - 30*24*60*60*1000).toISOString(),
    to: options.toDate || new Date().toISOString()
  });

  const response = await fetch(
    `https://api.miro.com/v2/teams/${teamId}/audit-log?${params}`,
    { headers: { 'Authorization': `Bearer ${accessToken}` } }
  );

  const data = await response.json();
  return data.data.map(entry => ({
    timestamp: entry.createdAt,
    actor: entry.actor.email,
    action: entry.action,
    resource: entry.resource.type,
    details: entry.details
  }));
}
```

---

## 13. AI-POWERED FEATURES

### 13.1 "Create with AI" Feature

**Capability:**
- Generate board layouts from text prompts
- Auto-organize content structure
- Suggest design patterns

**Limitations:**
- Not exposed via REST API
- Accessed only via Miro Web UI
- No Make.com module for this yet

**Workaround via Webhooks:**
```javascript
// Detect AI-generated content patterns
function detectAIGeneratedItems(items) {
  return items.filter(item => {
    // Check for AI-specific metadata
    return item.metadata?.generatedBy === 'ai' ||
           item.metadata?.aiConfidence > 0.8;
  });
}
```

### 13.2 Smart Clustering & Summarization

**Concept:**
- Group similar items automatically
- Generate summary labels
- Suggest categories

**Implementation via Web SDK:**
```javascript
// Cluster items by similarity
async function clusterItems(items) {
  // Calculate similarity between items
  const clusters = {};
  
  for (let i = 0; i < items.length; i++) {
    for (let j = i + 1; j < items.length; j++) {
      const similarity = calculateTextSimilarity(items[i].title, items[j].title);
      if (similarity > 0.7) {
        const category = items[i].title.split(/\s+/)[0]; // First word as category
        if (!clusters[category]) clusters[category] = [];
        clusters[category].push(items[i], items[j]);
      }
    }
  }
  
  return clusters;
}

function calculateTextSimilarity(str1, str2) {
  const longer = str1.length > str2.length ? str1 : str2;
  const shorter = str1.length > str2.length ? str2 : str1;
  if (longer.length === 0) return 1.0;
  return (longer.length - levenshteinDistance(longer, shorter)) / longer.length;
}

function levenshteinDistance(str1, str2) {
  const matrix = [];
  for (let i = 0; i <= str2.length; i++) matrix[i] = [i];
  for (let j = 0; j <= str1.length; j++) matrix[0][j] = j;
  for (let i = 1; i <= str2.length; i++) {
    for (let j = 1; j <= str1.length; j++) {
      if (str2.charAt(i - 1) === str1.charAt(j - 1)) {
        matrix[i][j] = matrix[i - 1][j - 1];
      } else {
        matrix[i][j] = Math.min(
          matrix[i - 1][j - 1] + 1,
          matrix[i][j - 1] + 1,
          matrix[i - 1][j] + 1
        );
      }
    }
  }
  return matrix[str2.length][str1.length];
}
```

---

## 14. EXPORT & DATA EXTRACTION

### 14.1 Board Export API (Asynchronous)

**Export Formats:** ZIP (contains SVG + JSON), PNG, PDF, CSV (limited)

**Export Initiation:**
```
POST https://api.miro.com/v2/boards/{board_id}/exports
Authorization: Bearer {access_token}

{
  "exportType": "zip|png|pdf",
  "options": {
    "includeComments": true,
    "includeMetadata": true
  }
}
```

**Response (Job Created):**
```json
{
  "id": "exp_abc123def456",
  "status": "pending",
  "format": "zip",
  "createdAt": "2026-04-07T12:34:56Z",
  "estimatedCompletionTime": 30
}
```

**Poll Export Status:**
```
GET https://api.miro.com/v2/exports/{export_id}
```

**Completed Export Response:**
```json
{
  "id": "exp_abc123def456",
  "status": "completed",
  "downloadUrl": "https://downloads.miro.com/exports/exp_abc123def456.zip",
  "expiresAt": "2026-04-14T12:34:56Z"
}
```

**Export Polling Implementation:**
```javascript
async function exportBoardWithPolling(boardId, accessToken, maxWaitSeconds = 300) {
  // 1. Initiate export
  const exportRes = await fetch(
    `https://api.miro.com/v2/boards/${boardId}/exports`,
    {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${accessToken}`,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({ exportType: 'zip' })
    }
  );
  const exportJob = await exportRes.json();
  const startTime = Date.now();

  // 2. Poll for completion
  while (Date.now() - startTime < maxWaitSeconds * 1000) {
    const statusRes = await fetch(
      `https://api.miro.com/v2/exports/${exportJob.id}`,
      { headers: { 'Authorization': `Bearer ${accessToken}` } }
    );
    const status = await statusRes.json();

    if (status.status === 'completed') {
      // 3. Download
      return { downloadUrl: status.downloadUrl, expiresAt: status.expiresAt };
    }

    if (status.status === 'failed') {
      throw new Error(`Export failed: ${status.error}`);
    }

    // Wait 2 seconds before polling again
    await new Promise(resolve => setTimeout(resolve, 2000));
  }

  throw new Error('Export timeout');
}
```

**Make.com Integration (Export → Email):**
```
1. Trigger: Button click (export board)
2. Call Miro API: Create export job
3. Poll export status (with delay loop)
4. When completed, download ZIP
5. Send email with attachment
6. Store download link in Supabase
```

### 14.2 Data Extraction from Miro

**Pattern: Extract All Items as CSV**
```javascript
async function extractBoardAsCSV(boardId, accessToken) {
  // 1. Get all items
  const response = await fetch(
    `https://api.miro.com/v2/boards/${boardId}/items?limit=100`,
    { headers: { 'Authorization': `Bearer ${accessToken}` } }
  );
  const items = await response.json();

  // 2. Transform to CSV
  const rows = [['ID', 'Type', 'Title', 'Content', 'X', 'Y', 'Color']];
  for (const item of items.data) {
    rows.push([
      item.id,
      item.type,
      item.title || '',
      item.content || '',
      item.position?.x || 0,
      item.position?.y || 0,
      item.style?.fillColor || 'N/A'
    ]);
  }

  // 3. Generate CSV
  const csv = rows.map(row => row.map(cell => `"${cell}"`).join(',')).join('\n');
  return csv;
}
```

**Pattern: Extract Comments & Threads**
```javascript
async function extractCommentsAndThreads(boardId, accessToken) {
  const comments = await fetch(
    `https://api.miro.com/v2/boards/${boardId}/comments`,
    { headers: { 'Authorization': `Bearer ${accessToken}` } }
  ).then(r => r.json());

  const structured = comments.data.map(comment => ({
    commentId: comment.id,
    author: comment.creator.email,
    timestamp: comment.createdAt,
    itemId: comment.linkedTo?.itemId,
    text: comment.message,
    replies: comment.replies?.map(reply => ({
      author: reply.creator.email,
      timestamp: reply.createdAt,
      text: reply.message
    })) || []
  }));

  return structured;
}
```

---

## 15. REAL-TIME WEBSOCKET COLLABORATION

### 15.1 WebSocket Connection

**Endpoint:**
```
wss://ws.miro.com/app/board/{board_id}/collaboration
```

**Handshake:**
```javascript
const socket = new WebSocket(
  `wss://ws.miro.com/app/board/${boardId}/collaboration?access_token=${accessToken}`
);

socket.addEventListener('open', () => {
  console.log('Connected to Miro WebSocket');
  socket.send(JSON.stringify({ type: 'subscribe', data: { boardId } }));
});

socket.addEventListener('message', (event) => {
  const message = JSON.parse(event.data);
  handleRealtimeEvent(message);
});
```

### 15.2 Conflict Resolution (CRDT / OT)

**Conflict Types:**
- Simultaneous item creation at same position
- Concurrent edits to same field
- Item position conflicts (overlapping)

**Miro's Approach:**
- Uses Operational Transformation (OT) for text
- Uses CRDT principles for collaborative data structures
- Server-side merge for non-conflicting changes
- Last-write-wins for conflict resolution (configurable)

**Example: Handling Position Conflicts**
```javascript
function resolvePositionConflict(localItem, remoteItem) {
  // Scenario: Two users move same item simultaneously
  // Resolution: Average positions or use timestamp
  
  const localTime = new Date(localItem.lastModified).getTime();
  const remoteTime = new Date(remoteItem.lastModified).getTime();
  
  if (localTime > remoteTime) {
    // Local change is newer, keep it
    return localItem;
  } else {
    // Remote change is newer, adopt it
    return remoteItem;
  }
}
```

### 15.3 Real-time Cursor Tracking

**Cursor Presence Events:**
```json
{
  "type": "cursorMove",
  "userId": "usr_abc123def456",
  "userName": "John Doe",
  "position": {"x": 500, "y": 300}
}
```

**Implementation:**
```javascript
const cursors = {}; // Track active cursors

socket.addEventListener('message', (event) => {
  const msg = JSON.parse(event.data);
  
  if (msg.type === 'cursorMove') {
    cursors[msg.userId] = { name: msg.userName, x: msg.position.x, y: msg.position.y };
    renderCursors(cursors);
  }
});

function renderCursors(cursors) {
  Object.entries(cursors).forEach(([userId, cursor]) => {
    let cursorEl = document.getElementById(`cursor_${userId}`);
    if (!cursorEl) {
      cursorEl = document.createElement('div');
      cursorEl.id = `cursor_${userId}`;
      cursorEl.className = 'remote-cursor';
      cursorEl.innerHTML = `<span>${cursor.name}</span>`;
      document.body.appendChild(cursorEl);
    }
    cursorEl.style.left = `${cursor.x}px`;
    cursorEl.style.top = `${cursor.y}px`;
  });
}
```

---

## 16. ERROR HANDLING & RETRY STRATEGIES

### 16.1 Common API Errors

**Error Codes:**
```
400 Bad Request — Invalid parameters
401 Unauthorized — Missing or invalid token
403 Forbidden — Insufficient permissions
404 Not Found — Board/item not found
409 Conflict — Item version mismatch
429 Too Many Requests — Rate limit exceeded
500 Server Error — Internal server error
503 Service Unavailable — Maintenance
```

### 16.2 Exponential Backoff Implementation

```javascript
async function apiCallWithExponentialBackoff(
  url,
  options,
  maxAttempts = 5,
  initialDelayMs = 100
) {
  for (let attempt = 1; attempt <= maxAttempts; attempt++) {
    try {
      const response = await fetch(url, options);

      // Success
      if (response.ok) return response.json();

      // Client error — don't retry
      if (response.status >= 400 && response.status < 500) {
        throw new Error(`HTTP ${response.status}: ${response.statusText}`);
      }

      // Server error or rate limit — retry
      if (response.status === 429 || response.status >= 500) {
        if (attempt === maxAttempts) throw new Error('Max retries exceeded');
        const retryAfter = response.headers.get('Retry-After') || 
                          Math.pow(2, attempt - 1);
        await sleep(parseInt(retryAfter) * 1000);
        continue;
      }
    } catch (error) {
      if (attempt === maxAttempts) throw error;
      const delayMs = initialDelayMs * Math.pow(2, attempt - 1) + 
                      Math.random() * 1000;
      await sleep(delayMs);
    }
  }
}
```

### 16.3 Circuit Breaker Pattern

```javascript
class CircuitBreaker {
  constructor(failureThreshold = 5, resetTimeoutMs = 60000) {
    this.failureThreshold = failureThreshold;
    this.resetTimeoutMs = resetTimeoutMs;
    this.failureCount = 0;
    this.state = 'closed'; // closed, open, half-open
    this.nextAttemptTime = Date.now();
  }

  async execute(fn) {
    if (this.state === 'open') {
      if (Date.now() < this.nextAttemptTime) {
        throw new Error('Circuit breaker is open');
      }
      this.state = 'half-open';
    }

    try {
      const result = await fn();
      this.onSuccess();
      return result;
    } catch (error) {
      this.onFailure();
      throw error;
    }
  }

  onSuccess() {
    this.failureCount = 0;
    this.state = 'closed';
  }

  onFailure() {
    this.failureCount++;
    if (this.failureCount >= this.failureThreshold) {
      this.state = 'open';
      this.nextAttemptTime = Date.now() + this.resetTimeoutMs;
    }
  }
}

// Usage
const breaker = new CircuitBreaker(5, 60000);
const response = await breaker.execute(() =>
  fetch('https://api.miro.com/v2/boards')
);
```

---

## 17. WEB SDK FOR CUSTOM APPLICATIONS

### 17.1 Web SDK Basics

**Installation:**
```html
<script src="https://miro.com/app/static/sdk.1.1.js"></script>
```

**Initialization:**
```javascript
miro.onReady(async () => {
  const board = miro.board;
  const currentUser = await miro.getCurrentUser();
  const selectedItems = await board.selection.get();

  console.log(`Board: ${board.info.title}`);
  console.log(`User: ${currentUser.name}`);
  console.log(`Selected items: ${selectedItems.length}`);
});
```

### 17.2 Web SDK App Lifecycle

**Architecture:**
```
┌─────────────────────────────────────────┐
│       Miro Web App Container            │
├─────────────────────────────────────────┤
│  1. Headless iframe (hidden, runs JS)   │
│     - App initialization                │
│     - State management                  │
│     - Event listeners                   │
│                                          │
│  2. UI Panel (visible sidebar/modal)    │
│     - React/HTML UI components          │
│     - User interactions                 │
│     - Forms, settings                   │
│                                          │
│  3. Event Bridge                        │
│     - postMessage API                   │
│     - bi-directional communication      │
│     - Action dispatching                │
└─────────────────────────────────────────┘
```

**Headless App Lifecycle:**
```javascript
// index.html (headless)
miro.onReady(async () => {
  // 1. Initialize state
  const boardData = await miro.board.get();
  
  // 2. Register event handlers
  miro.board.events.on('selection:update', handleSelectionChange);
  miro.board.events.on('items:create', handleItemsCreated);
  
  // 3. Listen for messages from panel
  miro.showNotification('App ready');
});

window.addEventListener('message', async (event) => {
  if (event.data.type === 'createTask') {
    const items = await miro.board.items.create([{
      type: 'card',
      title: event.data.title,
      position: { x: 0, y: 0 }
    }]);
    parent.postMessage({ type: 'taskCreated', items }, '*');
  }
});
```

**Panel UI Lifecycle:**
```javascript
// panel.html (visible UI)
function createTaskForm() {
  const form = document.createElement('form');
  const input = document.createElement('input');
  const button = document.createElement('button');

  input.type = 'text';
  input.placeholder = 'Enter task';
  button.textContent = 'Create';

  button.addEventListener('click', () => {
    parent.postMessage({
      type: 'createTask',
      title: input.value
    }, '*');
    input.value = '';
  });

  window.addEventListener('message', (event) => {
    if (event.data.type === 'taskCreated') {
      const notification = document.createElement('p');
      notification.textContent = 'Task created!';
      form.appendChild(notification);
    }
  });

  form.appendChild(input);
  form.appendChild(button);
  return form;
}

document.body.appendChild(createTaskForm());
```

### 17.3 Web SDK API Reference

**Board Methods:**
```javascript
await miro.board.get() — Get board info
await miro.board.items.get() — Get all items
await miro.board.items.create([items]) — Create items
await miro.board.items.update([items]) — Update items
await miro.board.items.delete([ids]) — Delete items
await miro.board.selection.get() — Get selected items
await miro.board.selection.set(items) — Select items
```

**Event Listeners:**
```javascript
miro.board.events.on('selection:update', callback)
miro.board.events.on('items:create', callback)
miro.board.events.on('items:update', callback)
miro.board.events.on('items:delete', callback)
miro.board.events.on('comment:create', callback)
miro.board.events.on('user:current:update', callback)
```

---

## 18. PROCESS MAPPING & BPMN

### 18.1 BPMN 2.0 Standard Notation

**Core Elements:**
```
Events: Start, Intermediate, End (circles)
  - Filled circle: Start event
  - Double circle: End event
  - Circle with cross: Error event

Activities: Tasks and processes (rectangles)
  - Simple task: Rectangle
  - User task: Rectangle with user icon
  - Service task: Rectangle with gear icon
  - Subprocess: Rectangle with plus sign

Gateways: Decision points (diamonds)
  - Exclusive (XOR): Diamond, one outgoing flow
  - Parallel (AND): Diamond with plus
  - Inclusive (OR): Diamond with circle
  - Complex: Diamond with asterisk

Flows: Sequence flows (arrows)
  - Solid arrow: Normal flow
  - Dashed arrow: Default flow (labeled)
```

### 18.2 Process Mapping in Miro

**Miro Template: Swimlane Diagram**
```
┌──────────────────────────────────────────────────────────┐
│ Vendor Onboarding Process                                │
├──────────────────┬──────────────┬───────────────────────┤
│ Procurement      │ Vendor        │ Finance               │
├──────────────────┼──────────────┼───────────────────────┤
│    [O] Start     │              │                        │
│      ↓           │              │                        │
│  ┌─────────────┐ │              │                        │
│  │ Send RFQ    │→│              │                        │
│  └─────────────┘ │              │                        │
│                  │ [O] Receive  │                        │
│                  │   RFQ        │                        │
│                  │     ↓        │                        │
│                  │  ┌────────┐ │                        │
│                  │  │ Review  │ │                        │
│                  │  └────────┘ │                        │
│                  │     ↓        │                        │
│                  │  ┌────────┐ │→┌──────────────┐      │
│                  │  │ Submit  │──→│ Review Terms │      │
│                  │  │ Proposal│   └──────────────┘      │
│                  │  └────────┘       ↓                   │
│                  │                ┌─────────────┐        │
│←─────────────────┼────────────────│ Approve     │       │
│ Send Contract    │ [O] Receive    │ Contract    │       │
│      ↓           │ Contract       └─────────────┘       │
│  ┌─────────────┐ │     ↓                                 │
│  │ Store       │←│  ┌────────┐                          │
│  │ Contract    │  │  │ Sign   │                          │
│  └─────────────┘  │  │Contract│                          │
│      ↓            │  └────────┘                          │
│  ┌─────────────┐  │                                      │
│  │ Activate    │  │                                      │
│  │ in System   │  │                                      │
│  └─────────────┘  │                                      │
│      ↓            │                                      │
│    [O] End        │                                      │
└──────────────────┴──────────────┴───────────────────────┘
```

### 18.3 Creating BPMN Diagrams Programmatically

**Miro Shapes for BPMN:**
```javascript
const bpmnElements = {
  // Events
  startEvent: {
    type: 'shape',
    shape: 'circle',
    title: 'Start',
    size: { width: 40, height: 40 },
    style: { fillColor: '#FF6B6B' }
  },
  endEvent: {
    type: 'shape',
    shape: 'circle',
    title: 'End',
    size: { width: 40, height: 40 },
    style: { fillColor: '#51CF66' }
  },
  // Activities
  task: {
    type: 'shape',
    shape: 'rectangle',
    title: 'Task Name',
    size: { width: 120, height: 60 },
    style: { fillColor: '#4DABF7', strokeColor: '#1971C2' }
  },
  // Gateways
  exclusiveGateway: {
    type: 'shape',
    shape: 'diamond',
    title: 'Decision',
    size: { width: 60, height: 60 },
    style: { fillColor: '#FFD43B' }
  }
};

async function createBPMNProcess(boardId, processDefinition, accessToken) {
  const items = [];
  let yOffset = 100;

  // Create start event
  items.push({
    ...bpmnElements.startEvent,
    position: { x: 100, y: yOffset }
  });
  yOffset += 150;

  // Create tasks
  for (const task of processDefinition.tasks) {
    items.push({
      ...bpmnElements.task,
      title: task.name,
      position: { x: 100, y: yOffset },
      metadata: { taskId: task.id }
    });
    yOffset += 150;
  }

  // Create end event
  items.push({
    ...bpmnElements.endEvent,
    position: { x: 100, y: yOffset }
  });

  // Create connectors between tasks
  const createdItems = await createItemsBulk(boardId, items, accessToken);
  
  // Draw sequence flows
  for (let i = 0; i < createdItems.length - 1; i++) {
    await createConnector(
      boardId,
      createdItems[i].id,
      createdItems[i + 1].id,
      accessToken
    );
  }

  return { items: createdItems };
}
```

---

## 19. COMPLIANCE, SECURITY, AND GDPR

### 19.1 Compliance Certifications

**Miro Compliance Status:**
- SOC 2 Type II (security, availability, integrity)
- ISO 27001 (information security management)
- ISO 27018 (cloud PII protection)
- GDPR compliant (EU data residency available)
- HIPAA Business Associate Agreement (BAA) available
- FedRAMP certification (in progress)

**Audit Reports:**
- Available upon request via trust portal
- Third-party attestations
- Penetration test results (annual)

### 19.2 Data Residency

**EU Data Residency:**
```
Forbes EU instance:
  - Servers located in Frankfurt, Germany
  - Data never leaves EU
  - GDPR article 44 compliant (no international transfers)

Configuration:
  1. Enterprise customer agreement required
  2. Specify EU residency preference during setup
  3. All API calls route to EU endpoints
  4. Backups stored in EU region
```

### 19.3 GDPR Compliance

**Data Processing Agreement (DPA):**
- Standard clauses included with Enterprise tier
- Miro acts as Data Processor
- Customer acts as Data Controller
- Right to data portability guaranteed

**GDPR Mechanisms:**
```javascript
// 1. Data export (right to portability)
async function exportUserData(userId, accessToken) {
  const userData = await fetch(
    `https://api.miro.com/v2/users/${userId}/export`,
    { headers: { 'Authorization': `Bearer ${accessToken}` } }
  );
  return userData.blob();
}

// 2. Data deletion (right to be forgotten)
async function deleteUserData(userId, accessToken) {
  await fetch(
    `https://api.miro.com/v2/users/${userId}`,
    {
      method: 'DELETE',
      headers: { 'Authorization': `Bearer ${accessToken}` }
    }
  );
  // Cascades: boards, items, comments associated with user are anonymized
}

// 3. Consent management
async function updateUserConsent(userId, consentType, granted, accessToken) {
  await fetch(
    `https://api.miro.com/v2/users/${userId}/consent`,
    {
      method: 'PATCH',
      headers: {
        'Authorization': `Bearer ${accessToken}`,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        [consentType]: granted // e.g., "analytics": false
      })
    }
  );
}
```

### 19.4 Encryption

**Transit Security:**
- TLS 1.3 (minimum) for all API calls
- Certificate pinning available (contact support)
- Perfect forward secrecy enabled

**Rest Security:**
- AES-256 encryption for stored data
- Encryption keys stored in hardware security modules (HSM)
- Separate keys per customer (multi-tenancy)

**End-to-End Encryption:**
- Not supported by default
- Workaround: Encrypt sensitive fields client-side before sending
```javascript
async function createEncryptedCard(boardId, plaintext, publicKey) {
  // Encrypt before sending
  const encrypted = encryptWithPublicKey(plaintext, publicKey);
  
  const card = await fetch(
    `https://api.miro.com/v2/boards/${boardId}/items`,
    {
      method: 'POST',
      body: JSON.stringify({
        items: [{
          type: 'card',
          title: '[Encrypted]',
          content: encrypted, // Store encrypted blob
          metadata: { encrypted: true }
        }]
      })
    }
  );
  return card.json();
}
```

---

## 20. VENDIAMONOI-SPECIFIC USE CASES

### 20.1 Marketplace Expansion Planning

**Workflow:**
```
1. Weekly: Pull marketplace performance data from Supabase
   - Revenue trend (7-day)
   - Customer count growth
   - Product mix
   - Competitor activity

2. Create Miro board: "Expansion Week {N}"
   Items for each marketplace:
   - Color-coded by performance (green=positive trend, red=negative)
   - Size = revenue contribution
   - Position = priority matrix (high-value, high-growth)

3. Add connectors showing dependencies:
   - Supply chain readiness
   - Integration complexity
   - Regulatory requirements

4. Comments/annotations from team:
   - Procurement notes
   - Supplier capability gaps
   - Timeline estimates

5. Export board as PDF for stakeholder review
   - Share via ClickUp tasks
   - Link in Notion meeting notes

6. Track decisions in Supabase:
   - miro_board_id → expansion_decision
   - Outcome (approved, rejected, postponed)
   - Implementation timeline
```

### 20.2 Supplier Onboarding Automation

**Miro Board Template: "Supplier Onboarding"
```
Swimlanes (Vendors):
  1. Procurement Team
  2. IT Systems Integration
  3. Finance & Compliance
  4. Supplier

Stages (Horizontal Flow):
  1. Registration (send RFQ)
  2. Technical Validation (assess capabilities)
  3. Compliance Review (KYC, certifications)
  4. System Integration (API, EDI setup)
  5. Go-Live

Cards for each stage + supplier combo
```

**Automation Flow:**
```
Trigger: New supplier registration → Google Form
  ↓
1. Extract form data (name, region, categories)
2. Create Supabase record: suppliers.new_supplier
3. Create Miro board: "Supplier: {name}"
   - Use template
   - Fill in supplier details
   - Assign to procurement team
4. Create ClickUp tasks:
   - "Validate {supplier} capability"
   - "Review {supplier} compliance"
   - "Setup {supplier} integration"
5. Notify team: "New supplier board ready"
6. Track progress:
   - Card move in Miro → ClickUp task status update
   - ClickUp completion → Miro board completion checkbox
   - Export board → Store PDF in Notion supplier database
```

### 20.3 Team Collaboration Hub

**Multi-board Structure:**
```
Miro Team Board: "Vendiamonoi.it Operations"

Frames (logical grouping):
  - Q2 2026 Roadmap (product launches)
  - Marketplace Performance Dashboard (real-time metrics)
  - Supplier Network Map (geographic distribution)
  - Technology Debt Backlog (improvement items)
  - Risk Register (threats and mitigations)

Each frame embedded in:
  - Notion workspace (for context)
  - Slack (announcements)
  - ClickUp epics (for execution)

Updates propagate:
  Miro → Webhooks → Supabase → ClickUp/Notion
```

### 20.4 Data Sync Architecture (Miro ↔ Supabase ↔ Make.com)

**Diagram:**
```
┌──────────────┐
│ Miro Board   │ (visual collaboration)
│ "Expansion" │
└──────┬───────┘
       │ Webhook (items created/updated)
       ↓
┌──────────────────────────────┐
│ Supabase Edge Function       │
│ miro-webhook-handler         │
│ - Parse event               │
│ - Validate signature        │
│ - Store in miro_items table │
└──────┬───────────────────────┘
       │
       ↓
┌─────────────────────┐
│ Supabase Database   │
│ - miro_boards       │
│ - miro_items        │
│ - miro_sync_logs    │
│ - miro_external_refs│
└──────┬──────────────┘
       │ Real-time Subscriptions
       ├──→ Make.com Webhook Listener
       │    └─→ Trigger scenario on miro_items INSERT
       │        - Create ClickUp task
       │        - Update Notion page
       │        - Sync to 3rd party
       │
       └──→ Custom Edge Function
            - Generate reports
            - Validate data quality
            - Archive old items
```

### 20.5 Implementation Checklist

**Phase 1: Foundation (Week 1-2)**
- [ ] Set up Miro app in workspace
- [ ] Generate API tokens (OAuth)
- [ ] Configure Make.com integration
- [ ] Create Supabase tables (miro_*)
- [ ] Deploy webhook receiver

**Phase 2: Make.com Workflows (Week 3-4)**
- [ ] Create "New Supplier" workflow
  - [ ] Create board from template
  - [ ] Populate initial cards
  - [ ] Assign to team
- [ ] Create "Daily Sync" workflow
  - [ ] Export board metrics
  - [ ] Update ClickUp tasks
  - [ ] Send Slack notification
- [ ] Create "Export & Archive" workflow
  - [ ] Monthly board export
  - [ ] Store in Google Drive
  - [ ] Link in Notion archive

**Phase 3: Notion Integration (Week 5)**
- [ ] Embed Miro boards in Notion
- [ ] Create Notion database for board registry
- [ ] Sync board metadata via API
- [ ] Create Notion dashboard linking to active boards

**Phase 4: ClickUp Sync (Week 6)**
- [ ] Configure bi-directional mapping
- [ ] Test status synchronization
- [ ] Set up comment sync
- [ ] Document in ClickUp guide

**Phase 5: Advanced Features (Week 7+)**
- [ ] Implement AI-powered clustering
- [ ] Set up real-time Slack updates
- [ ] Create custom Web SDK dashboard
- [ ] Set up analytics in Supabase

---

## REFERENCES & RESOURCES

### Official Documentation
- https://developers.miro.com/docs/miro-rest-api-introduction
- https://developers.miro.com/reference/api-reference
- https://developers.miro.com/docs/miro-web-sdk-introduction
- https://developers.miro.com/docs/webhooks-guide

### Authentication & Security
- https://developers.miro.com/docs/rest-api-auth
- https://developers.miro.com/docs/rate-limiting
- https://help.miro.com/hc/en-us/articles/360012346599-Miro-security-and-compliance-FAQ

### Integration Guides
- https://www.make.com/en/integrations/miro
- https://www.notion.com/integrations/miro
- https://help.clickup.com/hc/en-us/articles/6305968859799-Miro-integration

### Advanced Topics
- https://developers.miro.com/docs/scim
- https://developers.miro.com/docs/getting-started-with-webhooks
- https://developers.miro.com/docs/miro-web-sdk-introduction

### Templates & Best Practices
- https://miro.com/templates/
- https://miro.com/blog/
- https://miro.com/process-mapping/

---

## DOCUMENT METADATA

**Version:** 1.0  
**Last Updated:** April 7, 2026  
**Author:** API Research Team  
**Classification:** Internal Knowledge Base  
**Target Audience:** Vendiamonoi.it Engineering & Operations  
**Use Cases:** Automation, Integration, Data Architecture, Team Collaboration  
**Maintenance:** Review quarterly for API updates and new features