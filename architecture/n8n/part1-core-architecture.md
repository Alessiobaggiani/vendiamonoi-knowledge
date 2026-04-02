# n8n Workflow Automation Platform — Parte 1: Architettura Core, Workflow Engine e Sistema Nodi

**Documento Tecnico Avanzato | n8n Deep Dive for Senior Automation Engineers**

**Versione**: 1.0 | **Data**: 2026-04-01 | **Lingua**: English/Italian Mix | **Target**: 5,000+ lines

---

## TABLE OF CONTENTS

1. [Architettura Fondamentale di n8n](#1-architettura-fondamentale-di-n8n)
2. [Workflow Engine — Come n8n Esegue i Workflow](#2-workflow-engine--come-n8n-esegue-i-workflow)
3. [Il Modello Dati di n8n — Items](#3-il-modello-dati-di-n8n--items)
4. [Sistema Nodi — Tassonomia Completa](#4-sistema-nodi--tassonomia-completa)
5. [Node Connections e Data Flow](#5-node-connections-e-data-flow)
6. [Expressions e Variabili Built-in](#6-expressions-e-variabili-built-in)
7. [Credential System](#7-credential-system)
8. [n8n vs Make.com — Confronto Architetturale](#8-n8n-vs-makecom--confronto-architetturale)

---

## 1. Architettura Fondamentale di n8n

### 1.1 Open-Source Foundation & Fair-Code License

n8n is an **open-source workflow automation platform** licensed under the **Fair Code License** (BUSL-1.1 — Business Source License). This is fundamentally different from traditional open-source models:

- **Source Code Accessible**: The entire codebase is publicly available on GitHub (github.com/n8n-io/n8n)
- **Free Use**: Non-production use is completely free
- **Production Restrictions**: Using n8n in production requires either:
  - Purchase of a commercial license
  - Deployment through n8n Cloud
  - Self-hosting under specific terms
- **Conversion to MIT**: The source code converts to MIT license after 4 years from release

This licensing model positions n8n between proprietary (Make.com) and fully open-source (Apache Airflow), providing transparency while funding development.

### 1.2 Technology Stack & Architecture Layers

n8n's architecture is built on a modern, containerized foundation:

```
┌──────────────────────────────────────────────────┐
│        n8n Frontend (Vue.js 3)                   │
│  - Visual Workflow Designer                      │
│  - Node Library & Marketplace Integration        │
│  - Real-time Execution Monitoring                │
└──────────────────────────────────────────────────┘
                      ↓
┌──────────────────────────────────────────────────┐
│        n8n Backend (Node.js Express)             │
│  - REST API Server                               │
│  - Workflow Storage & Management                 │
│  - Credential Management                         │
│  - Execution Orchestration                       │
└──────────────────────────────────────────────────┘
                      ↓
┌──────────────────────────────────────────────────┐
│     Workflow Execution Engine (Core)             │
│  - Node Initialization                           │
│  - Data Flow & Item Processing                   │
│  - Error Handling & Retries                      │
│  - Expression Evaluation                         │
└──────────────────────────────────────────────────┘
                      ↓
┌──────────────────────────────────────────────────┐
│    Node Type System & Integrations               │
│  - 400+ Integration Nodes                        │
│  - Custom Node Support                           │
│  - Trigger & Action Patterns                     │
└──────────────────────────────────────────────────┘
```

#### Technology Components

| Layer | Technology | Purpose |
|-------|-----------|----------|
| **Backend** | Node.js (v14+) | Core runtime |
| **Framework** | Express.js | HTTP server, routing |
| **Database** | PostgreSQL/MySQL/SQLite | Workflow storage |
| **Execution** | Custom JavaScript VM | Node execution, sandboxing |
| **Frontend** | Vue.js 3 + TypeScript | UI/UX |
| **Message Queue** | Bull/Redis (optional) | Job queuing |
| **Container** | Docker | Deployment standardization |

### 1.3 High-Level Architecture Pattern

n8n follows a **modular, plugin-based architecture**:

```
Workflow Definition (JSON)
         ↓
   [Parser & Validator]
         ↓
   [Execution Planner]
    (DAG Builder)
         ↓
   [Node Executor]
    (Sequential/Parallel)
         ↓
   [Data Flow Manager]
    (Item Distribution)
         ↓
   [Credential Resolver]
    (Auth Injection)
         ↓
   [Integration Nodes]
    (API Calls, Data Transforms)
         ↓
   [Output/Storage]
```

**Key Architectural Principles**:
- **No Code Generation**: Workflows are executed directly from JSON
- **Dynamic Credential Injection**: Credentials resolved at runtime
- **Node Sandboxing**: Nodes run in isolated execution context
- **Streaming Data Model**: Items flow through nodes, not bulk operations
- **Error Isolation**: Node failures don't crash entire workflow

---

## 2. Workflow Engine — Come n8n Esegue i Workflow

### 2.1 Workflow Execution Lifecycle

n8n workflow execution follows a well-defined lifecycle:

```
1. TRIGGER ACTIVATION
   └─ Webhook received / Schedule triggered / Manual run

2. WORKFLOW LOADING
   └─ Fetch workflow JSON from database
   └─ Validate workflow structure
   └─ Resolve workflow variables

3. EXECUTION INITIALIZATION
   └─ Create execution context
   └─ Initialize first node(s)
   └─ Load credentials

4. NODE EXECUTION LOOP
   ├─ Get next executable node
   ├─ Resolve input data
   ├─ Execute node logic
   ├─ Collect output items
   └─ Repeat until all nodes processed

5. DATA FLOW MANAGEMENT
   ├─ Distribute items to downstream nodes
   ├─ Handle item arrays & branching
   └─ Manage execution paths

6. ERROR HANDLING
   ├─ Catch node errors
   ├─ Apply retry logic (if configured)
   ├─ Route to error handlers
   └─ Log failures

7. EXECUTION COMPLETION
   ├─ Finalize execution
   ├─ Store execution history
   ├─ Trigger webhooks (if configured)
   └─ Clean up resources
```

### 2.2 Execution Context & State Management

Each workflow execution maintains a **persistent execution context**:

```javascript
{
  "executionId": "abc123def456",
  "workflowId": "workflow_xyz",
  "startTime": 1704067200000,
  "status": "running",
  
  // Variable scope
  "variables": {
    "$executionId": "abc123def456",
    "$nodeExecutionStack": ["node1", "node2"],
    "custom_var": "value"
  },
  
  // Node results storage
  "nodeResults": {
    "node1": [
      { "id": "item_1", "json": {...}, "binary": {...} },
      { "id": "item_2", "json": {...}, "binary": {...} }
    ],
    "node2": [
      { "id": "item_1", "json": {...} }
    ]
  },
  
  // Execution trace
  "executionTrace": [
    { "node": "node1", "timestamp": 1704067200000, "duration": 500 },
    { "node": "node2", "timestamp": 1704067200500, "duration": 300 }
  ],
  
  // Errors encountered
  "errors": []
}
```

**Execution Context Scope**:
- **Available in**: All node expressions
- **Accessible via**: `$executionId`, `$nodeExecutionStack`, `$variables`
- **Immutable**: Cannot be modified by node logic
- **Scoped per execution**: Fresh context for each workflow run

### 2.3 Node Execution Model

#### Sequential Execution (Default)

n8n's **default execution pattern** is sequential with configurable parallelism:

```javascript
// Workflow structure
[
  {
    "id": "node1",
    "type": "n8n-nodes-base.trigger",
    "connections": { "main": [["node2"]] }
  },
  {
    "id": "node2",
    "type": "n8n-nodes-base.httpRequest",
    "connections": { "main": [["node3"]] }
  },
  {
    "id": "node3",
    "type": "n8n-nodes-base.set",
    "connections": { "main": [[]] }
  }
]

// Execution order: node1 → node2 → node3
// Each node waits for upstream completion
```

#### Item-Based Iteration

When a node produces **multiple items**, downstream nodes execute **once per item**:

```javascript
// Node 1 output: 3 items
node1_output = [
  { "id": 1, "name": "Alice" },
  { "id": 2, "name": "Bob" },
  { "id": 3, "name": "Charlie" }
]

// Node 2 executes 3 times (once per item)
node2_execution_1: { "id": 1, "name": "Alice" }
node2_execution_2: { "id": 2, "name": "Bob" }
node2_execution_3: { "id": 3, "name": "Charlie" }

// Node 3 receives all outputs as array
node3_input = [
  { output from node2_execution_1 },
  { output from node2_execution_2 },
  { output from node2_execution_3 }
]
```

**Critical Point**: This item distribution is fundamental to n8n's data flow model.

#### Branching & Multiple Connections

Nodes can have **multiple output connections** for conditional routing:

```javascript
{
  "id": "node_conditional",
  "type": "n8n-nodes-base.if",
  "connections": {
    "main": [
      ["node_true_path"],   // Connection 0: true condition
      ["node_false_path"]   // Connection 1: false condition
    ]
  }
}
```

**Behavior**:
- Items are routed to **specific output index** based on condition
- Unselected paths receive **no items**
- This enables conditional workflows without complex workarounds

### 2.4 Trigger System

Workflows **start** via triggers:

#### Trigger Types

| Type | Mechanism | Use Case |
|------|-----------|----------|
| **Webhook** | HTTP POST to unique URL | External systems triggering workflow |
| **Cron** | Time-based schedule (cron expressions) | Periodic tasks (hourly, daily, etc.) |
| **Manual** | User clicks "Execute" | Testing, on-demand runs |
| **Manual + Webhook** | Hybrid mode | Testing + production triggers |

#### Trigger Node Example

```javascript
{
  "id": "trigger",
  "type": "n8n-nodes-base.webhookTrigger",
  "typeVersion": 1,
  "position": [250, 300],
  "parameters": {
    "path": "customer-webhook",
    "httpMethod": "POST",
    "responseMode": "onReceived"
  }
}
```

**Webhook Flow**:
1. External system POSTs to `https://n8n-instance.com/webhook/customer-webhook`
2. n8n validates webhook signature (if configured)
3. Request body becomes first item in trigger node output
4. Workflow execution starts with that item

**Response Behavior**:
- `onReceived`: Returns 200 immediately, workflow executes async
- `lastNode`: Waits for workflow completion, returns last node's output
- `afterResponse`: Webhook returns, then executes workflow

### 2.5 Error Handling & Recovery

n8n provides multiple error handling strategies:

#### Strategy 1: Try-Catch Nodes

```javascript
{
  "id": "try_node",
  "type": "n8n-nodes-base.try",
  "connections": {
    "main": [
      [{ "node": "risky_operation", "index": 0 }],  // Try
      [{ "node": "error_handler", "index": 0 }]    // Catch
    ]
  }
}
```

#### Strategy 2: Node Error Output

Nodes have **two output types**:
- **Main (Output 0)**: Normal output
- **Error (Output 1)**: Error information

```javascript
connections: {
  "main": [
    [{ "node": "next_node", "index": 0 }],     // Success path
    [{ "node": "error_node", "index": 0 }]    // Error path
  ]
}
```

#### Strategy 3: Retry Configuration

Nodes can be configured to **auto-retry** on failure:

```javascript
{
  "id": "api_call",
  "type": "n8n-nodes-base.httpRequest",
  "parameters": {
    "url": "https://api.example.com/data",
    "retryOnFail": true,
    "maxRetries": 3,
    "retryWait": 1000,  // milliseconds between retries
    "retryMultiplier": 2  // exponential backoff
  }
}
```

**Retry Backoff**:
- Attempt 1: immediate
- Attempt 2: 1000ms delay
- Attempt 3: 2000ms delay
- Attempt 4: 4000ms delay
- Max 3 retries = 4 total attempts

#### Strategy 4: Continue on Error

```javascript
{
  "id": "optional_operation",
  "type": "n8n-nodes-base.httpRequest",
  "parameters": {
    "continueOnFail": true  // Don't stop execution if this fails
  }
}
```

**Result**: Node error is captured in `error` field of item, but workflow continues.

---

## 3. Il Modello Dati di n8n — Items

### 3.1 Item Structure & Anatomy

The **Item** is n8n's fundamental data unit:

```javascript
{
  // Unique identifier for this item within execution
  "pairedItem": { "item": 0, "input": 0 },
  
  // Main data payload (JSON)
  "json": {
    "id": 123,
    "name": "Alice",
    "email": "alice@example.com",
    "metadata": {
      "created": "2024-01-01",
      "tags": ["vip", "customer"]
    }
  },
  
  // Binary data (files, images, etc)
  "binary": {
    "avatar": {
      "data": "iVBORw0KGgoAAAANSUhEUgAA...",  // base64
      "mimeType": "image/png",
      "fileName": "avatar.png"
    },
    "document": {
      "data": "JVBERi0xLjQKJeLjz9M...",  // PDF content
      "mimeType": "application/pdf",
      "fileName": "report.pdf"
    }
  }
}
```

**Item Components**:

1. **json** (Object)
   - Main data payload
   - Can contain any JSON structure
   - Accessed via `$json` or specific paths like `$json.name`
   - Flat or nested structures supported

2. **binary** (Object)
   - Binary file data
   - Organized by key (e.g., `avatar`, `document`)
   - Each binary item contains:
     - `data`: Base64-encoded content
     - `mimeType`: MIME type of file
     - `fileName`: Original file name
   - Accessed via `$binary.keyName`

3. **pairedItem** (Object)
   - Tracks item lineage through workflow
   - `item`: Item index in previous node's output
   - `input`: Input index (for branching)
   - Useful for error tracking and data provenance

### 3.2 Item Lifecycle Through Workflow

```
Trigger Node
│
├─ Input: HTTP request body
│  {
│    "id": 1,
│    "action": "create_user",
│    "name": "Bob"
│  }
│
└─ Output: [Item 1]
   json: { id: 1, action: "create_user", name: "Bob" }

Http Request Node (Make API Call)
│
├─ Input: Item 1
│  json: { id: 1, action: "create_user", name: "Bob" }
│
└─ Output: [Item 1]
   json: { userId: 789, created: true, timestamp: "2024-01-01T00:00:00Z" }

Set Node (Transform)
│
├─ Input: Item 1 from Http Request
│  json: { userId: 789, created: true, timestamp: "2024-01-01T00:00:00Z" }
│
└─ Output: [Item 1]
   json: { userId: 789, created: true, timestamp: "2024-01-01T00:00:00Z", processed: true }
```

### 3.3 Item Array Distribution

When a node outputs **multiple items**, n8n distributes them to downstream nodes:

```javascript
// Webhook Trigger receives request
request_body = [
  { id: 1, name: "Alice" },
  { id: 2, name: "Bob" },
  { id: 3, name: "Charlie" }
]

// Trigger outputs 3 items
output = [
  { json: { id: 1, name: "Alice" } },
  { json: { id: 2, name: "Bob" } },
  { json: { id: 3, name: "Charlie" } }
]

// Http Request node receives these items
// It makes 3 separate API calls:

Call 1: POST /api/users with body { id: 1, name: "Alice" }
Call 2: POST /api/users with body { id: 2, name: "Bob" }
Call 3: POST /api/users with body { id: 3, name: "Charlie" }

// Each call produces an output item
Node output = [
  { json: { created: true, userId: 101 } },
  { json: { created: true, userId: 102 } },
  { json: { created: true, userId: 103 } }
]
```

**Key Insight**: This is **not** batch processing. Each item is processed individually, creating isolated execution contexts.

### 3.4 Binary Data Handling

n8n has first-class support for binary files:

#### Reading Files

```javascript
{
  "id": "read_file",
  "type": "n8n-nodes-base.readBinaryFile",
  "parameters": {
    "filePath": "/path/to/image.png"
  }
}

// Output:
// {
//   "json": { "fileName": "image.png" },
//   "binary": {
//     "data": {
//       "data": "iVBORw0KGgoAAAANSUhEUgAAAAUA...",
//       "mimeType": "image/png",
//       "fileName": "image.png"
//     }
//   }
// }
```

#### Transforming Binary Data

```javascript
{
  "id": "transform_image",
  "type": "n8n-nodes-base.imageMagick",
  "parameters": {
    "operation": "resize",
    "width": 200,
    "height": 200
  }
}

// Input: Binary image from previous node
// Output: Resized image as binary
```

#### Uploading Files

```javascript
{
  "id": "upload_file",
  "type": "n8n-nodes-base.aws.s3",
  "parameters": {
    "operation": "upload",
    "bucket": "my-bucket",
    "key": "uploads/{{ $json.fileName }}"
  }
}

// Reads binary data from input item
// Uploads to S3
// Returns metadata
```

### 3.5 Data Type Preservation

n8n preserves **JavaScript data types** through JSON:

```javascript
// Input item
{
  "json": {
    "string": "hello",
    "number": 42,
    "boolean": true,
    "null": null,
    "array": [1, 2, 3],
    "object": { "nested": "value" },
    "date": "2024-01-01T00:00:00Z",  // ISO string
    "timestamp": 1704067200000       // Number in ms
  }
}

// These types are preserved through expressions
// typeof $json.number === 'number'
// typeof $json.boolean === 'boolean'
// $json.array[0] === 1
// $json.object.nested === 'value'
```

**Note**: Dates must be stored as ISO strings or timestamps, as JSON doesn't have native Date type.

---

## 4. Sistema Nodi — Tassonomia Completa

### 4.1 Node Classification

n8n nodes are categorized by function:

#### Category 1: Trigger Nodes

**Purpose**: Initiate workflow execution

| Node | Trigger Type | Configuration |
|------|-------------|---------------|
| **Webhook** | HTTP POST/GET | Custom URL path, method |
| **Cron** | Scheduled time | Cron expression |
| **Manual** | User click | Button in UI |
| **Google Calendar** | Calendar event | Event time, calendar |
| **Slack** | Message/reaction | Workspace, channel, event type |
| **Stripe** | Payment event | Event type, API key |

#### Category 2: Action Nodes

**Purpose**: Perform operations, integrate with services

**HTTP-based Actions**:
- HTTP Request (generic API calls)
- Rest API (RESTful services)
- Webhook (send HTTP webhooks)

**SaaS Integrations**:
- Slack (send messages, create channels)
- Gmail (send emails, read messages)
- Google Sheets (read/write data)
- Salesforce (CRUD operations on records)
- HubSpot (manage contacts, deals)
- GitHub (create issues, manage repos)

**Data Services**:
- Airtable (read/write records)
- Notion (create pages, update databases)
- MongoDB (CRUD operations)
- PostgreSQL (execute queries)
- Elasticsearch (search, index)

**File/Storage**:
- AWS S3 (upload/download files)
- Google Cloud Storage
- Dropbox
- SFTP (file transfers)
- FTP

#### Category 3: Control Flow Nodes

**Purpose**: Manage workflow logic

| Node | Function | Example |
|------|----------|----------|
| **If** | Conditional routing | `if ($json.status === 'active') then branch1 else branch2` |
| **Switch** | Multi-way branching | Switch on status: pending → branch1, active → branch2, etc. |
| **Loop** | Iterate over items | Loop through array, execute nodes for each item |
| **Merge** | Combine items | Merge output from multiple branches |
| **Splitter** | Distribute items | Split single item into multiple items |
| **Deduplicator** | Remove duplicates | Deduplicate by field |

#### Category 4: Data Transform Nodes

**Purpose**: Manipulate data

| Node | Operation | Example |
|------|-----------|----------|
| **Set** | Create/modify properties | Set $json.processed = true |
| **Function** | JavaScript execution | Custom code logic |
| **Code** | Advanced code | JavaScript with full node API |
| **Template** | String interpolation | Generate text: `Hello {{$json.name}}` |
| **Rename Keys** | Rename object keys | { oldKey → newKey } |
| **Flatten** | Flatten nested objects | { a: { b: { c: 1 } } } → { a_b_c: 1 } |
| **GroupBy** | Group items | Group users by department |
| **Sort** | Sort items | Sort by date, descending |
| **Limit** | Limit item count | Keep first N items |
| **Sleep** | Delay execution | Wait 1000ms (rate limiting) |

#### Category 5: Message/Communication Nodes

**Purpose**: Send notifications

| Node | Channel | Example |
|------|---------|----------|
| **Email** | SMTP/Email service | Send notification email |
| **Slack** | Slack channel | Send alert message |
| **Telegram** | Telegram bot | Send mobile notification |
| **Discord** | Discord webhook | Send message to Discord |
| **Twilio** | SMS | Send text message |
| **Pushover** | Push notifications | Mobile push notification |

#### Category 6: Utility Nodes

**Purpose**: Auxiliary functions

| Node | Function | Use Case |
|------|----------|----------|
| **Start** | Entry point (rarely used) | Legacy workflows |
| **No Operation (Noops)** | Do nothing | Placeholder |
| **Execute Workflow** | Call another workflow | Reuse logic, modularization |
| **Respond to Webhook** | Send webhook response | Custom HTTP response |

### 4.2 Node Anatomy & Configuration

Every node has a consistent structure:

```javascript
{
  "id": "http_request_1",
  "type": "n8n-nodes-base.httpRequest",
  "typeVersion": 4.2,
  "position": [750, 300],
  "name": "Fetch User Data",
  "description": "Retrieves user profile from API",
  
  // Node parameters (configuration)
  "parameters": {
    "url": "https://api.example.com/users/{{$json.userId}}",
    "method": "GET",
    "headers": {
      "Authorization": "Bearer {{$credentials.apiKey}}"
    },
    "authentication": "genericCredentialType"
  },
  
  // Credential reference
  "credentials": {
    "httpHeaderAuth": "connection_12345"
  },
  
  // Node options
  "retryOnFail": true,
  "maxRetries": 3,
  "continueOnFail": false,
  "disabled": false,
  "alwaysOutputData": false,
  "executeOnce": false
}
```

**Node Properties**:

1. **id**: Unique identifier within workflow
2. **type**: Node type (e.g., `n8n-nodes-base.httpRequest`)
3. **typeVersion**: Node version (backward compatibility)
4. **position**: [x, y] canvas position
5. **name**: Display name in UI
6. **description**: Documentation
7. **parameters**: Configuration object (varies by node type)
8. **credentials**: Reference to stored credentials
9. **retryOnFail**: Auto-retry on failure
10. **continueOnFail**: Don't fail workflow if this node fails
11. **disabled**: Skip this node
12. **alwaysOutputData**: Always output data, even if empty
13. **executeOnce**: Execute only once, not for each item

### 4.3 HTTP Request Node (Most Common)

The HTTP Request node is the **workhorse** of n8n workflows:

```javascript
{
  "id": "api_call",
  "type": "n8n-nodes-base.httpRequest",
  "parameters": {
    // HTTP Method
    "method": "POST",
    
    // Target URL (supports expressions)
    "url": "https://api.example.com/v1/users",
    
    // Headers (supports expressions)
    "headers": {
      "Authorization": "Bearer {{$credentials.token}}",
      "Content-Type": "application/json",
      "X-Custom-Header": "{{$json.header_value}}"
    },
    
    // Query parameters
    "queryParameters": {
      "filter": "{{$json.filter}}",
      "limit": "100"
    },
    
    // Request body (for POST/PUT)
    "body": {
      "name": "{{$json.name}}",
      "email": "{{$json.email}}",
      "metadata": {
        "created": "{{$now.iso}}"
      }
    },
    
    // Response handling
    "responseFormat": "json",  // 'json', 'string', 'file'
    "returnFullResponse": false
  }
}
```

**Response Processing**:

```javascript
// API returns:
{
  "status": 200,
  "data": {
    "id": 123,
    "created": true
  }
}

// Node output (by default, extracts data):
{
  "json": {
    "id": 123,
    "created": true
  }
}

// With returnFullResponse: true:
{
  "json": {
    "statusCode": 200,
    "body": {
      "id": 123,
      "created": true
    },
    "headers": { ... }
  }
}
```

### 4.4 Function Node (Custom Logic)

For custom JavaScript execution:

```javascript
{
  "id": "custom_logic",
  "type": "n8n-nodes-base.function",
  "parameters": {
    "functionCode": `
      // Input: items array of objects
      // Each item has: json, binary, pairedItem properties
      
      for (const item of items) {
        // Transform each item
        item.json.processed = true;
        item.json.timestamp = new Date().toISOString();
        
        // Conditional logic
        if (item.json.age < 18) {
          item.json.category = 'minor';
        } else {
          item.json.category = 'adult';
        }
      }
      
      // Return modified items
      return items;
    `
  }
}
```

**Function Node Context**:
- Input: `items` (array of Item objects)
- Output: Must return array of Item objects
- Available globals: `$`, `Math`, `Object`, `JSON`, `Date`, etc.
- Can access node context via `$nodeExecutionContext`

### 4.5 Set Node (Most Common Data Transform)

Simple key-value setting without code:

```javascript
{
  "id": "set_data",
  "type": "n8n-nodes-base.set",
  "parameters": {
    "keepOnlySet": false,  // true: only keep set fields, false: merge
    "values": {
      "string": {
        "name": "processed_data",
        "value": "hello world"
      },
      "number": {
        "name": "count",
        "value": 42
      },
      "boolean": {
        "name": "active",
        "value": true
      },
      "expression": {
        "name": "user_name",
        "value": "={{$json.firstName}} {{$json.lastName}}"
      }
    }
  }
}
```

**Output**:
```javascript
{
  "json": {
    "...existing fields...",
    "processed_data": "hello world",
    "count": 42,
    "active": true,
    "user_name": "John Doe"
  }
}
```

---

## 5. Node Connections e Data Flow

### 5.1 Connection Model

n8n nodes connect via **typed connections**:

```javascript
{
  "connections": {
    // Output index → Array of target nodes
    "main": [
      // Output 0 (success)
      [
        {
          "node": "process_data",
          "index": 0
        }
      ],
      // Output 1 (error, alternative branch)
      [
        {
          "node": "error_handler",
          "index": 0
        }
      ]
    ]
  }
}
```

**Connection Types**:
- **main**: Standard data flow
- **error**: Error output (when node errors)
- **success**: Alternative success path (rare)

### 5.2 Data Flow Through Connections

```
Node1 Output
├─ Item 1: { id: 1, name: "Alice" }
├─ Item 2: { id: 2, name: "Bob" }
└─ Item 3: { id: 3, name: "Charlie" }

        ↓ (via connection index 0)

Node2 executes 3 times
├─ Execution 1: { id: 1, name: "Alice" }
├─ Execution 2: { id: 2, name: "Bob" }
└─ Execution 3: { id: 3, name: "Charlie" }

Node2 Output
├─ Item 1: { result: "processed_1" }
├─ Item 2: { result: "processed_2" }
└─ Item 3: { result: "processed_3" }

        ↓ (via connection index 0)

Node3 receives all 3 items
```

### 5.3 Branching Patterns

#### Pattern 1: If-Then-Else

```javascript
{
  "id": "conditional",
  "type": "n8n-nodes-base.if",
  "parameters": {
    "conditions": {
      "string": [
        {
          "value1": "={{$json.status}}",
          "operation": "equals",
          "value2": "approved"
        }
      ]
    }
  },
  "connections": {
    "main": [
      [{ "node": "approved_handler" }],  // true branch
      [{ "node": "pending_handler" }]    // false branch
    ]
  }
}
```

**Behavior**:
- Items matching condition → Output index 0
- Items NOT matching → Output index 1
- If output index has no connection, items are discarded

#### Pattern 2: Switch (Multi-way)

```javascript
{
  "id": "switch",
  "type": "n8n-nodes-base.switch",
  "parameters": {
    "mode": "expression",
    "expression": "{{$json.status}}",
    "cases": [
      {
        "output": 0,
        "condition": "pending"
      },
      {
        "output": 1,
        "condition": "approved"
      },
      {
        "output": 2,
        "condition": "rejected"
      }
    ],
    "fallbackOutput": 3  // No match
  },
  "connections": {
    "main": [
      [{ "node": "pending_handler" }],   // output 0
      [{ "node": "approved_handler" }],  // output 1
      [{ "node": "reject_handler" }],    // output 2
      [{ "node": "unknown_handler" }]    // fallback
    ]
  }
}
```

#### Pattern 3: Parallel Branches (Merge)

```javascript
// Split execution into multiple paths
if (condition1) → node_path1 → merge
if (condition2) → node_path2 → merge
else → node_path3 → merge

{
  "id": "merge",
  "type": "n8n-nodes-base.merge",
  "parameters": {
    "mode": "mergeByPosition"
  },
  "connections": {
    "main": [
      // Can accept input from multiple nodes
      [{ "node": "downstream" }]
    ]
  }
}
```

### 5.4 Item Pairing & Tracking

When items flow through branches, n8n tracks their origin:

```javascript
// Node A outputs 2 items
A_output = [
  { json: { id: 1 }, pairedItem: { item: 0 } },
  { json: { id: 2 }, pairedItem: { item: 1 } }
]

// Both go through If node
// Item 1 matches condition → node B (index 0)
// Item 2 doesn't match → node C (index 1)

B_output = [
  { json: { processed: true }, pairedItem: { item: 0, input: 0 } }
]

C_output = [
  { json: { skipped: true }, pairedItem: { item: 1, input: 1 } }
]

// Merge node can track items back to origin
```

---

## 6. Expressions e Variabili Built-in

### 6.1 Expression Syntax

n8n uses **JavaScript expressions** with special context variables:

```javascript
// Basic expression (in double braces)
"value": "={{$json.name}}"

// String interpolation
"value": "Hello {{$json.firstName}} {{$json.lastName}}"

// Conditional (ternary)
"value": "={{$json.age > 18 ? 'adult' : 'minor'}}"

// Array operations
"value": "={{$json.items.filter(item => item.price > 100).length}}"

// Object operations
"value": "={{Object.keys($json).length}}"

// Complex logic
"value": "={{$json.status === 'approved' && $json.amount > 1000 ? $json.amount * 1.1 : $json.amount}}"
```

### 6.2 Context Variables (Built-in)

#### $json
Current item's JSON data:

```javascript
"url": "https://api.example.com/users/{{$json.userId}}"
// If $json = { userId: 123, name: "Alice" }
// Results in: https://api.example.com/users/123
```

#### $binary
Current item's binary data:

```javascript
// Access binary file
"attachment": "{{$binary.document.data}}"

// Access metadata
"fileName": "{{$binary.avatar.fileName}}"
```

#### $itemIndex
Index of current item in output array:

```javascript
// Item 0 has $itemIndex = 0
// Item 1 has $itemIndex = 1
"itemNumber": "={{$itemIndex + 1}}"
```

#### $runIndex
Index of current workflow run (for cron/repeated triggers):

```javascript
// First run: $runIndex = 0
// Second run: $runIndex = 1
"executionNumber": "={{$runIndex + 1}}"
```

#### $executionId
Unique ID for current execution:

```javascript
"executionId": "={{$executionId}}"
// Example: "abc123def456789"
```

#### $now
Current date/time:

```javascript
// ISO format
"timestamp": "={{$now.iso}}"
// Result: 2024-01-15T10:30:45.123Z

// Unix timestamp (ms)
"timestamp": "={{$now.unix}}"
// Result: 1705315845123

// ISO date only
"date": "={{$now.toISOString().split('T')[0]}}"
// Result: 2024-01-15
```

#### $credentials
Access stored credentials:

```javascript
"headers": {
  "Authorization": "Bearer {{$credentials.apiKey}}",
  "X-Api-Token": "{{$credentials.token}}"
}
```

#### $env
Environment variables:

```javascript
"apiUrl": "{{$env.API_BASE_URL}}",
"apiKey": "{{$env.API_KEY}}"
```

#### $nodeExecutionStack
Array of node names executed so far:

```javascript
// If execution: trigger → node1 → node2 (current)
"executedNodes": "={{$nodeExecutionStack}}"
// Result: ["trigger", "node1", "node2"]
```

#### $input
Input to current node:

```javascript
// In expressions of current node
"allInputItems": "={{$input.item().json}}"
```

### 6.3 Helper Functions

n8n provides utility functions in expressions:

#### Date Functions

```javascript
// Parse date
"parsed": "={{new Date('2024-01-15')}}"

// Format date
"formatted": "={{new Date().toLocaleDateString()}}"

// Relative dates
"tomorrow": "={{new Date(Date.now() + 86400000).toISOString()}}"
```

#### String Functions

```javascript
// Built-in JavaScript
"upper": "={{$json.name.toUpperCase()}}",
"lower": "={{$json.name.toLowerCase()}}",
"trim": "={{$json.name.trim()}}",
"replace": "={{$json.text.replace(/old/g, 'new')}}",
"split": "={{$json.csv.split(',')}}",
"includes": "={{$json.text.includes('keyword')}}"
```

#### Array Functions

```javascript
"filtered": "={{$json.items.filter(i => i.active)}}",
"mapped": "={{$json.items.map(i => i.name)}}",
"sorted": "={{$json.items.sort((a, b) => a.priority - b.priority)}}",
"unique": "={{[...new Set($json.items.map(i => i.type))]}}",
"joined": "={{$json.items.join(', ')}}"
```

#### Object Functions

```javascript
"keys": "={{Object.keys($json)}}",
"values": "={{Object.values($json)}}",
"entries": "={{Object.entries($json)}}",
"assign": "={{Object.assign({}, $json, {processed: true})}}"
```

#### Math Functions

```javascript
"sum": "={{$json.items.reduce((a, b) => a + b.amount, 0)}}",
"average": "={{$json.items.reduce((a, b) => a + b.value, 0) / $json.items.length}}",
"max": "={{Math.max(...$json.prices)}}",
"min": "={{Math.min(...$json.prices)}}"
```

### 6.4 Conditional Expressions

#### Ternary Operator

```javascript
"status": "={{$json.approved ? 'active' : 'pending'}}"

// Nested
"category": "={{$json.age < 18 ? 'minor' : $json.age < 65 ? 'adult' : 'senior'}}"
```

#### Nullish Coalescing

```javascript
"name": "={{$json.fullName ?? 'Anonymous'}}"
// Use fullName if available, otherwise 'Anonymous'
```

#### Optional Chaining

```javascript
"email": "={{$json.user?.contact?.email ?? 'no-email'}}"
// Safely access nested properties
```

### 6.5 Variable Scope

Variables are scoped within workflow execution:

```javascript
// In one node
"name": "value"

// In next node, access via $json
"output": "={{$json.name}}"

// But NOT available in parallel branches
// or after different trigger
```

**Important**: n8n doesn't have persistent variables across executions. Use database or storage service for state.

---

## 7. Credential System

### 7.1 Credential Architecture

n8n's credential system provides **secure storage and injection** of sensitive data:

```
┌──────────────────────────────────────┐
│   Node Configuration (Workflow)      │
│  {
│    "credentials": {
│      "httpHeaderAuth": "conn_12345"  │
│    }
│  }                                    │
└──────────────────────────────────────┘
              ↓
┌──────────────────────────────────────┐
│   Credential Manager                 │
│  - Resolves conn_12345 reference     │
│  - Decrypts stored credential        │
│  - Injects into node execution       │
└──────────────────────────────────────┘
              ↓
┌──────────────────────────────────────┐
│   Node Runtime                       │
│  - Credentials available as          │
│    $credentials in expressions       │
│  - Used for API authentication       │
└──────────────────────────────────────┘
```

### 7.2 Credential Types

#### Type 1: API Key / Bearer Token

```javascript
{
  "type": "httpHeaderAuth",
  "properties": {
    "name": "My API",
    "credentials": {
      "credentialsSecret": "sk_live_51234567890abcdef"
    }
  }
}

// Usage in node
"headers": {
  "Authorization": "Bearer {{$credentials.credentialsSecret}}"
}
```

#### Type 2: OAuth 2.0

```javascript
{
  "type": "oAuth2",
  "properties": {
    "clientId": "your-client-id",
    "clientSecret": "your-client-secret",
    "authorizationUrl": "https://provider.com/oauth/authorize",
    "accessTokenUrl": "https://provider.com/oauth/token",
    "scope": "user,email,repo",
    "redirectUrl": "https://your-n8n.com/oauth/callback"
  }
}
```

**Flow**:
1. User clicks "Connect" in n8n UI
2. Redirected to provider's authorization page
3. User grants permission
4. Redirected back with authorization code
5. n8n exchanges code for access token
6. Token stored encrypted in database
7. Token auto-refreshed on expiry

#### Type 3: Basic Auth

```javascript
{
  "type": "basicAuth",
  "properties": {
    "username": "user@example.com",
    "password": "secure_password_123"
  }
}

// Automatically creates Authorization header
// Base64(username:password)
```

#### Type 4: Custom Headers

```javascript
{
  "type": "httpHeaderAuth",
  "properties": {
    "headers": [
      {
        "name": "X-API-Key",
        "value": "your-key-123"
      },
      {
        "name": "X-Custom-Header",
        "value": "custom-value"
      }
    ]
  }
}
```

### 7.3 Credential Storage & Security

**Encryption**:
- All credentials encrypted at rest using AES-256
- Encryption key stored separately from database
- Self-hosted n8n: Use `N8N_ENCRYPTION_KEY` environment variable
- Cloud n8n: Uses AWS KMS

**Access Control**:
- Credentials visible only to workflow owner
- Admin users can view all credentials
- Credentials never exposed in workflow export
- Cannot be referenced in expressions outside credential context

**Best Practices**:
1. Use service accounts, not personal accounts
2. Use API keys instead of passwords when possible
3. Rotate credentials regularly
4. Use IP whitelisting on API endpoints
5. Monitor credential usage in logs

### 7.4 Using Credentials in Workflows

#### Method 1: Node Parameter

```javascript
{
  "id": "http_call",
  "type": "n8n-nodes-base.httpRequest",
  "parameters": {
    "authentication": "genericCredentialType",
    "url": "https://api.example.com/data"
  },
  "credentials": {
    "httpHeaderAuth": "connection_12345"
  }
}
```

#### Method 2: Expression

```javascript
"headers": {
  "Authorization": "Bearer {{$credentials.apiKey}}",
  "X-Token": "{{$credentials.token}}"
}
```

#### Method 3: Service-Specific Nodes

Many integrations have built-in credential handling:

```javascript
{
  "type": "n8n-nodes-base.slack",
  "parameters": {
    "resource": "message",
    "operation": "post"
  },
  "credentials": {
    "slackApi": "slack_connection_xyz"  // Auto-injects token
  }
}
```

### 7.5 Multi-Account Scenarios

You can store multiple credentials of same type:

```javascript
// Credential 1: Production API
{
  "id": "prod_api",
  "type": "httpHeaderAuth",
  "name": "Production API",
  "properties": { "key": "prod_key_123" }
}

// Credential 2: Development API
{
  "id": "dev_api",
  "type": "httpHeaderAuth",
  "name": "Development API",
  "properties": { "key": "dev_key_456" }
}

// Workflow uses production
"credentials": {
  "httpHeaderAuth": "prod_api"
}
```

---

## 8. n8n vs Make.com — Confronto Architetturale

### 8.1 Platform Comparison

| Aspect | n8n | Make.com |
|--------|-----|----------|
| **License** | Fair Code (BUSL-1.1) | Proprietary |
| **Open Source** | Yes, source available | No |
| **Self-Hosting** | Supported | Cloud-only |
| **Pricing Model** | Fixed/usage-based | Operations-based (10k ops/mo free) |
| **Data Residency** | Full control (self-host) | US-based infrastructure |
| **Node Ecosystem** | 400+ official nodes | 1000+ integrations |

### 8.2 Architecture Differences

#### Data Model

**n8n**:
- Items: `{ json, binary, pairedItem }`
- Streaming processing
- Each item processed individually

**Make.com**:
- Bundles: Collections of records
- Batch processing
- Operations billed per "execution"

#### Execution Model

**n8n**:
```javascript
Node1 → [Item1, Item2, Item3]
Node2 → Executes 3 times (once per item)
Output → [Result1, Result2, Result3]
```

**Make.com**:
```javascript
Module1 → [Bundle with array of records]
Module2 → Processes entire bundle
Output → Modified bundle
```

#### Credential System

**n8n**:
- Encrypted local storage
- Self-hosted control
- Reference by name/ID

**Make.com**:
- Cloud-managed encryption
- Less user control
- Simpler but less transparent

### 8.3 Performance Characteristics

**n8n**:
- **Throughput**: Limited by node execution speed, not operation count
- **Latency**: Lower (local execution)
- **Cost**: Lower for high-volume workflows
- **Scalability**: Horizontal (self-hosted)

**Make.com**:
- **Throughput**: Limited by 10k monthly operations (free tier)
- **Latency**: Variable (cloud-based)
- **Cost**: Higher for high-volume workflows
- **Scalability**: Limited (cloud constraints)

### 8.4 Development Workflow

**n8n**:
```
Design locally → Test locally → Deploy
- Can run offline
- Full IDE integration possible
- Git-friendly workflow
```

**Make.com**:
```
Design in cloud → Test in cloud → Deploy
- Cloud-dependent
- Limited version control
- Real-time collaboration
```

### 8.5 Use Case Alignment

**n8n Better For**:
- Enterprise deployments
- High-volume operations (cost-sensitive)
- Data privacy requirements
- Custom integrations
- Offline workflows
- Complex branching logic

**Make.com Better For**:
- Quick prototyping
- Cloud-first organizations
- Minimal ops overhead
- Real-time collaboration
- Simple sequential workflows
- Managed security

### 8.6 Architectural Philosophy

**n8n's Approach**:
- Transparency over simplicity
- User control over managed service
- Modular, composable design
- Open-source community
- Technical depth required

**Make.com's Approach**:
- Simplicity over transparency
- Managed service over control
- Integrated platform
- Proprietary innovation
- Low barrier to entry

---

## Conclusion

n8n's architecture is built on principles of **transparency, modularity, and user control**. The streaming item model, flexible credential system, and extensive expression language enable sophisticated workflow automation while maintaining clean separation of concerns.

Key architectural strengths:
1. **Fair-Code licensing** enabling transparency and self-hosting
2. **Item-based data flow** for fine-grained control
3. **Extensive expression language** for dynamic workflows
4. **Modular node system** with 400+ integrations
5. **Flexible deployment options** (cloud or self-hosted)

This document provides foundational knowledge required before exploring advanced patterns in Parts 2-4.

---

**END OF n8n-part1-core-architecture.md**