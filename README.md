# JMS Worker Plugin - User Guide

Welcome to the JMS Worker Plugin! This guide will help you get the most out of managing your JMS message queues directly from IntelliJ IDEA.

## Table of Contents
1. [Installation & Setup](#installation--setup)
2. [Quick Start](#quick-start)
3. [Managing Connections](#managing-connections)
4. [Browsing Queues](#browsing-queues)
5. [Sending Messages](#sending-messages)
6. [Message Templates](#message-templates)
7. [Topics & Subscriptions](#topics--subscriptions)
8. [Message History](#message-history)
9. [Advanced Operations](#advanced-operations)
10. [Tips & Tricks](#tips--tricks)
11. [Troubleshooting](#troubleshooting)

---

## Installation & Setup

### Installing the Plugin

1. Open IntelliJ IDEA
2. Go to **Preferences/Settings** → **Plugins**
3. Search for "JMS Worker"
4. Click **Install** and restart IDE

Or install manually:
1. Download the plugin JAR file
2. Go to **Preferences/Settings** → **Plugins** → **⚙️** → **Install Plugin from Disk**
3. Select the JAR file
4. Restart IDE

### Accessing the Plugin

Once installed, you'll see a **JMS Worker** tool window at the bottom of your IDE. Click on it to activate.

---

## Quick Start

### Step 1: Add a Connection

1. Click the **+** button in the Connections toolbar
2. Fill in connection details:
- **Name**: Give your connection a meaningful name (e.g., "Dev Artemis")
- **Type**: Select "Artemis", "ActiveMQ 5", or "IBM MQ"
- **Host**: Broker hostname
- **Port**: Broker port (usually 61616 for Artemis/ActiveMQ 5, 1414 for IBM MQ)
- **Username/Password**: Leave empty if not required
3. Click **Test Connection** to verify
4. Click **OK** to save

### Step 2: Browse a Queue

1. In the JMS Worker tool window, you'll see your connections in a tree
2. **Click the arrow** to expand a connection (queues load on expand)
3. Click on a queue to browse its messages
4. Messages appear in the table on the right
5. Click a message to see details in the split panel below

### Step 3: Send a Message

1. Right-click on a queue → **Send Message**
2. Or click the **Send** button in toolbar
3. **Select connection and queue** from dropdowns (only connected servers shown)
4. Fill in the message body
5. Click **Send**

---

## Managing Connections

### Connection Types

#### Apache ActiveMQ Artemis
- **Host/Port**: Direct TCP connection
- **Credentials**: Optional username/password
- **Jolokia** (optional): Enable for queue/topic creation/deletion and topic discovery

#### ActiveMQ Classic 5.x
- **Host/Port**: OpenWire connection (usually 61616)
- **Credentials**: Optional for JMS, often required for web/Jolokia management
- **Jolokia**: Use for queue/topic discovery and broker admin actions
- **Default Jolokia path**: `/api/jolokia`

#### IBM MQ
- **Host/Port**: Queue manager connection
- **Queue Manager**: Queue manager name
- **Channel**: Channel name (usually "DEV.APP.SVRCONN")
- **CCSID**: Character encoding (usually 819 for UTF-8)
- **Credentials**: Often required

### Connect and Disconnect

- **Expanding** a connection node (click the arrow) connects if not yet connected and loads the queue list.
- **Double-clicking** a disconnected connection node reconnects and loads queues immediately.
- **Right-click → Disconnect** explicitly disconnects. Once disconnected, the plugin will not reconnect in the background — the "Disconnected" state is sticky until you explicitly reconnect.
- The connection node collapses on disconnect and shows a "Loading..." placeholder so the expand arrow stays visible for the next connect.

### Lazy Loading

Connections now use **lazy loading**:
- All connections appear immediately in the tree
- Queues and topics load only when you **expand** a connection
- Each connection loads independently (no waiting for slow servers)
- Loading indicator shows "Loading..." while fetching queues

### Testing Connections

Before saving, always test your connection:
1. Fill in the connection details
2. Click **Test Connection**
3. You'll see "Connection successful" or an error message
4. Only save if the test passes

### Context Menu Actions

Right-click on a **connection** for:
- **Refresh Queues** — Reload queue list
- **Edit Connection** — Modify settings
- **Create Queue** — Create new queue (Artemis or ActiveMQ 5 + Jolokia)
- **Create Topic** — Create new topic (Artemis or ActiveMQ 5 + Jolokia)
- **Send Message to...** — Open send dialog
- **Connect/Disconnect** — Toggle connection state
- **Delete Connection** — Remove from settings

Right-click on a **queue** for:
- **Browse Queue** — Load messages
- **Send Message** — Send to this queue
- **Delete Queue** — Remove queue (Artemis or ActiveMQ 5 + Jolokia)
- **Purge Queue** — Delete all messages

Right-click on a **topic** for:
- **Subscribe to Topic** — Opens subscribe dialog with pre-filled topic name
- **Publish to Topic** — Opens send dialog in topic mode
- **Delete Topic** — Remove topic (Artemis or ActiveMQ 5 + Jolokia)

---

## Browsing Queues

### Tree Navigation

The left panel shows a tree structure with Queues and Topics as separate categories:
```
📁 Connections
  ├── 🟢 Dev Artemis (7)
  │   ├── 📋 Queues (5)
  │   │   ├── orders.queue (42)
  │   │   ├── payments.queue (0)
  │   │   └── ⚠️ DLQ (3)
  │   └── 📡 Topics (2)
  │       ├── news.topic
  │       └── events.topic ●
  └── 🔴 Prod IBM MQ (not connected)
```

- 🟢 Green = Connected
- 🔴 Red = Disconnected
- ⚠️ Warning = DLQ with messages
- ● Green dot = Topic has active subscription
- Numbers in parentheses = message count (auto-refreshed every 2 seconds)

### Tree Stays Expanded

Queue count refresh now updates individual queue labels without collapsing the tree. Your expanded connections, queues, and topics categories stay open.

### Simplified Queue Names

Queue names are simplified for readability:
- `orders::orders` displays as just `orders`
- `address::queue` displays as `queue` (when address equals queue name)
- Full name is used for API calls

### Message Count vs Loaded

The header shows actual vs loaded count:
- **"Queue: orders (580 messages, showing 100)"**
- Click **"Load more (480 remaining)..."** to load additional messages

### Filtering Messages

Use the filter field above the message table:
- Type to filter by message ID, correlation ID, body content
- Results update in real-time
- Clear to see all messages

### Speed Search in Tree

Just start typing while the tree is focused:
- Matches queue names and topic names
- Press Enter to select match
- Press Escape to cancel

### Message Details

Click any message to see the **split detail panel**:

**Left side - Body:**
- Syntax highlighted (JSON/XML auto-detected)
- Format buttons: JSON | XML
- Copy button copies **formatted** content

**Right side - Headers & Properties:**
- Message ID, Correlation ID, Timestamp
- Type, Priority, Delivery Mode
- Body type (TEXT/BYTES) and size
- Custom properties

### BYTES Messages

BYTES messages are **automatically decoded**:
- Base64 content converted to readable text
- Original encoding respected (UTF-8, CP912, etc.)
- Headers show both Base64 and decoded size

---

## Sending Messages

### Connection & Queue Selection

The send dialog includes **dropdowns**:
- **Connection**: Only shows connected servers
- **Destination**: Switch between Queue and Topic mode
- **Queue/Topic**: Dynamically loads items for selected connection
- You can also type a name manually

### Basic Message

1. Select connection and destination (queue or topic)
2. Choose **Message Format**: TextMessage or BytesMessage
3. Type your message body
4. Click **Send**

### Message Headers

Expand **Message Headers** section:
- **Correlation ID**: For request/reply patterns
  - ☑️ **Convert ASCII to HEX**: For IBM MQ on AS/400
- **Type**: JMS message type identifier
- **Priority**: 0-4 (normal), 5-9 (expedited)
- **Persistent**: Survives broker restart

### ASCII to HEX Conversion

For IBM MQ on AS/400 that requires HEX correlation IDs:
1. Enter correlation ID as ASCII (e.g., `ORDER123`)
2. Check **"Convert ASCII to HEX"**
3. Plugin converts to HEX before sending (e.g., `4F524445523132333`)

### Extended Options

Expand **Extended Options** section:
- **Reply-To Queue**: Where responses should go
- **Time-To-Live (ms)**: Message expiration (0 = never)
- **Group ID**: For message grouping (JMSXGroupID)

### Custom Properties

Expand **Custom Properties** section:
- One property per line: `key=value`
- Example:
  ```
  environment=production
  version=2.1
  requestId=abc123
  ```

### Message Format & Encoding

- **TextMessage**: Plain text (default)
- **BytesMessage**: Binary with encoding selection
  - UTF-8 (default)
  - UTF-16, ISO-8859-1, ASCII
  - CP1252, CP912, EBCDIC-US (for mainframe)

### Saving to History

Expand **History** section:
- ☑️ **Save to history**: Store for later reuse
- **Label**: Optional name for easy finding

---

## Message Templates

The **Templates** tab lets you save, organize, and reuse frequently sent messages.

### Creating a Template

1. Open the **Templates** tab at the bottom of the tool window
2. Click **+** (New Template) in the toolbar
3. Fill in the template:
   - **Name**: Descriptive name shown in the list
   - **Connection** and **Destination**: Optional — a template without these is portable across environments
   - **Message body**: Can contain variables (see below)
   - Message type, priority, TTL, correlation ID, and custom properties are all saved
4. Click **Save**

### Using a Template

1. In the Templates tab, double-click a template (or right-click → **Use**)
2. If the template has a connection and destination bound, the Send Message dialog opens pre-filled and ready to send
3. If the template is missing a connection or destination, a small target dialog appears asking only for the missing values — fill them in and continue

### Variable Substitution

Template bodies support built-in and custom variables:

| Variable | Value |
|----------|-------|
| `{{uuid}}` | Random UUID generated at send time |
| `{{timestamp}}` | Current ISO-8601 timestamp |
| `{{myVar}}` | Custom variable — prompted at send time |

Example body:
```json
{"orderId": "{{uuid}}", "createdAt": "{{timestamp}}", "customerId": "{{customerId}}"}
```
Custom variables (`{{customerId}}` in the example) are collected in the Variables dialog before sending.

### Managing Templates

Right-click a template for:
- **Use** — open in Send Message dialog
- **Edit** — modify the template
- **Duplicate** — copy for quick variation
- **Delete** — remove
- **Mark as Favorite** — pin to the top of the list (shown with star icon)

---

## Topics & Subscriptions

### Overview

The **Topics** tab provides real-time topic subscription management for Artemis connections. Topics are auto-discovered via Jolokia (MULTICAST addresses) and displayed under each connection in the tree. ActiveMQ Classic 5.x also supports topic discovery, publishing, and topic create/delete via Jolokia.

### Subscribing to a Topic

**From the Topics tab:**
1. Click the **▶ Subscribe** button in the toolbar
2. Select connection and topic from dropdowns
3. Optionally enable **Durable subscription** and enter a subscription name
4. Optionally enter a **message selector** (JMS SQL-like syntax)
5. Click **Subscribe**

**From the connection tree:**
1. Expand a connection → Topics category
2. Right-click on a topic → **Subscribe to Topic**
3. The subscribe dialog opens with the topic pre-filled
4. After subscribing, the plugin switches to the Topics tab automatically

### Subscription Indicators

Active subscriptions are visible in two places:
- **Connection tree**: Topics with active subscriptions show a green **●** indicator
- **Topics tab**: Subscription list shows status icons (● active, ○ stopped, ✖ error)

These indicators synchronize automatically — stopping or removing a subscription from the Topics tab immediately updates the tree, and vice versa.

### Viewing Live Messages

1. In the Topics tab, click on a subscription in the left list
2. Incoming messages appear in the table in real-time
3. Click a message to see full details (body + headers)
4. Use **Auto-scroll** toggle to follow latest messages

### Unread Message Badge

When the Topics tab is not active (you're on Browser or History), incoming messages are counted as unread:
- The tab title changes from **Topics** to **Topics (5)** showing the unread count
- The badge resets automatically when you switch to the Topics tab
- The JMS Worker tool window also activates to draw your attention on the first unread message

### Publishing to a Topic

**From the connection tree:**
1. Right-click on a topic → **Publish to Topic**
2. The Send Message dialog opens in **Topic mode** with the topic pre-selected
3. Enter your message and click **Send**

**From the Send Message dialog:**
1. Change the **Destination** dropdown from "Queue" to "Topic"
2. The destination list switches to available topics
3. Select or type a topic name and send

### Managing Subscriptions

Right-click on a subscription in the Topics tab for:
- **Stop Listening** — Pause the subscription (buffered messages kept)
- **Clear Messages** — Remove buffered messages
- **Remove Subscription** — Stop and remove entirely
- **Remove Durable Subscription from Broker** — Delete the durable subscription on the broker (pending messages lost)

### Topic Management

- **Artemis + Jolokia**:
  - **Create Topic**: Right-click on connection → Create Topic, or right-click on Topics category → Create Topic
  - **Delete Topic**: Right-click on topic → Delete Topic (also removes durable subscriptions)
- **ActiveMQ 5 + Jolokia**:
  - **Create Topic**: Right-click on connection → Create Topic
  - **Delete Topic**: Right-click on topic → Delete Topic

---

## Message History

### Accessing History

Click the **History** tab at the bottom of the tool window.

### Same Detail View

History has the **same split panel** as the queue browser:
- Body on the left with formatting
- Headers on the right
- JSON/XML format buttons
- Copy formatted content

### BYTES in History

BYTES messages are properly decoded:
- **Format** column shows TEXT or BYTES
- Detail view shows decoded content
- Edit & Send uses decoded body

### Filtering History

- **Connection**: Filter by source connection
- **Queue**: Filter by target queue
- **Search**: Find by body, label, or correlation ID

### Resending Messages

1. Select a message
2. Click **Resend** → Confirms and sends immediately
3. Or click **Edit & Send** → Opens in send dialog for modifications

### Managing History

- **Set Label**: Add/edit label for organization
- **Delete**: Remove single message
- **Clear Old**: Remove messages older than N days
- **Clear All**: Delete entire history

---

## Advanced Operations

### Creating Queues

Requires Jolokia enabled in connection settings:

1. Right-click on connection → **Create Queue...**
2. Enter queue name (e.g., `my.new.queue`)
3. Click OK
4. Queue appears in tree immediately (targeted reload, no tree collapse)

### Creating Topics

Requires Jolokia enabled:

1. Right-click on connection → **Create Topic...**
2. Enter topic name (e.g., `my.new.topic`)
3. Click OK
4. Topic appears under the Topics category immediately

### Deleting Queues/Topics

1. Right-click on queue/topic → **Delete Queue** / **Delete Topic**
2. Confirm deletion
3. ⚠️ Messages in queue/topic are lost!

### Moving Messages

1. Select one or more messages (Ctrl+click for multi-select)
2. Right-click → **Move to Queue...**
3. Select target queue
4. Messages removed from source, added to target

### Copying Messages

1. Select messages
2. Right-click → **Copy to Queue...**
3. Select target queue
4. Original stays, copy created in target

### Purging Queues

1. Right-click on queue → **Purge Queue**
2. Confirm deletion of ALL messages
3. ⚠️ Cannot be undone!

### Drag & Drop

Drag messages from table to queue in tree:
- Moves messages to target queue
- Visual feedback during drag

---

## Tips & Tricks

### Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| F5 | Refresh current view |
| Ctrl+F | Focus filter/search field |
| Delete | Delete selected (in history) |
| Enter | View message details |
| Ctrl+C | Copy selected message body |
| Ctrl+A | Select all messages |

### Splitter Customization

Drag the splitters to resize panels:
- Hover over splitter → shows accent color
- Drag to resize
- Double-click to reset to default

### Organizing with Labels

Use descriptive labels:
- `BUG-123: order processing error`
- `TEST: batch import scenario 5`
- `TEMPLATE: standard order request`

### Message Templates

Use the **Templates** tab for messages you send repeatedly:
- Save with variable placeholders (`{{uuid}}`, `{{timestamp}}`, custom vars) to get fresh values on every send
- Leave connection/destination unbound for payloads you use across environments — the target dialog asks for them at send time
- Mark frequently used templates as Favorites so they stay at the top of the list

### Performance Tips

For large queues (1000+ messages):
- Use filters to narrow results
- Don't load more than needed
- Close unused connections
- Periodically clean old history

### Working with Multiple Environments

Create connections for each environment:
- `DEV-Artemis` → development server
- `TEST-IBM-MQ` → testing queue manager
- `PROD-Artemis` → production (read-only recommended)

Color-code with labels to avoid mistakes!

---

## Troubleshooting

### Connection Issues

**"Cannot connect to broker"**
- ✔ Check hostname and port
- ✔ Verify network connectivity
- ✔ Check firewall rules
- ✔ Try Test Connection button

**"Authentication failed"**
- ✔ Verify username and password
- ✔ Check account is enabled on broker
- ✔ For IBM MQ: verify CCSID and channel

**Connection loads slowly**
- This is normal for remote/slow brokers
- Each connection loads independently
- Other connections remain usable

### Queue Issues

**"Cannot see queues"**
- ✔ Click arrow to expand connection
- ✔ Wait for "Loading..." to complete
- ✔ Check connection is still active
- ✔ Try Refresh Queues from context menu

**"Queue shows wrong count"**
- Counts auto-refresh every 2 seconds in the background
- Click on a queue to browse and get exact count
- Try Refresh Queues from context menu for immediate update

**"IBM MQ queues show 0 messages (mTLS / app-only channel)"**
- The plugin tries the fast PCF admin path first; if that channel does not have access to `SYSTEM.ADMIN.COMMAND.QUEUE`, it automatically falls back to a direct `MQOO_INQUIRE` open on each queue
- The fallback requires only BROWSE and INQUIRE rights on the queues, no PCF admin rights
- If counts still show 0, confirm the JMS user has at least BROWSE+INQUIRE on those queues

### Topic Issues

**"No topics visible"**
- ✔ Topics require broker management support with Jolokia enabled
- ✔ Check Jolokia URL in connection settings
- ✔ Artemis topics must use MULTICAST routing type on the broker

**"Publish to Topic does nothing"**
- ✔ Ensure the Send Message dialog shows "Topic" in the destination dropdown
- ✔ Verify the connection is active
- ✔ Right-click → Publish to Topic automatically selects topic mode

**"Subscription indicator not updating"**
- The tree syncs automatically with the Topics tab
- Try expanding/collapsing the connection to force refresh

### Message Issues

**"BYTES message shows Base64"**
- Should auto-decode in detail view
- Check encoding matches source
- Try different encoding in settings

**"Special characters corrupted"**
- Select correct encoding when sending
- CP912 for Cyrillic
- EBCDIC-US for mainframe

**"Copy gives wrong content"**
- Format first (JSON/XML button)
- Then use Copy button
- Copies formatted content

### Create Queue/Topic Fails

**"Jolokia required"**
- Enable Jolokia in connection settings
- Provide Jolokia URL (e.g., `http://localhost:8161/console/jolokia` for Artemis or `http://localhost:8161/api/jolokia` for ActiveMQ 5)
- Requires broker configuration

**"Invalid JSON request"**
- Update to latest plugin version
- Check Jolokia endpoint is accessible

### Database Issues

**"History not working"**
- Close all connections
- Delete: `~/.config/JetBrains/IntelliJIdea*/jms-worker/message-history.db`
- Restart plugin (recreates database)
- ⚠️ Deletes all history!

---

## What's New in v1.3.0

- **Disconnect is now truly final**: After clicking Disconnect, background polling and queue count refresh can no longer silently re-establish the connection. The Disconnected state is sticky until you explicitly reconnect.
- **Double-click to reconnect**: Double-clicking a disconnected connection node now reconnects and loads queues, matching the initial-connect behaviour.
- **Right-click no longer triggers browse**: Right-clicking a queue or topic only updates the selection context for the context menu — it no longer switches to the Browser tab or triggers a queue browse.
- **Clean tree reset on disconnect**: The connection node collapses and resets to a "Loading..." placeholder on disconnect so the expand arrow stays visible and reconnect always works on the first try.
- **Failed queue load retries**: A node stuck on "Error: ..." no longer blocks future load attempts — the next expand triggers a fresh retry.
- **IBM MQ queue counts on mTLS and app-only channels**: Channels that do not have PCF admin access now fall back to a direct INQUIRE open, which requires only per-queue BROWSE+INQUIRE rights.

## What's New in v1.2.0 / v1.2.1

- **Message Templates**: Save, reuse, and parameterize frequently sent messages. Templates support variable substitution (`{{uuid}}`, `{{timestamp}}`, custom variables), connection/destination binding, and favorites. See the [Message Templates](#message-templates) section.
- **Template target dialog**: Using a template without a bound connection or destination opens a lightweight dialog that asks only for the missing values at send time.
- **Fixed**: Sending to a sorted paginated queue no longer resets the loaded range or makes new messages disappear.
- **Fixed**: Newly sent messages keep a valid Message ID in the browser via broker send receipts.
- **Fixed**: Artemis move message and topic publish indefinite block.
- **Improved**: Reduced IDE freeze risk — tree search uses cached data, History reads moved off EDT, topic refresh throttled.

## What's New in v1.1.0

- **ActiveMQ Classic 5.x support**: Dedicated connection type with JMS and Jolokia-based management
- **ActiveMQ Classic discovery**: Auto-discovery of queues and topics from broker management MBeans
- **Classic admin actions**: Create/delete queues and topics, queue count, and purge via Jolokia
- **No more blocking dialogs**: Connection test, send, move, and subscription operations no longer freeze the IDE
- **Message body search**: Context-menu find with highlighted matches in Browser, Detail, History, and Send Message
- **Unified body tools**: Consistent Plain / JSON / XML actions and bytes views across all message viewers

## What's New in v1.0.3

- **Artemis topic support**: Auto-discovery, subscribe (durable/non-durable), live message feed
- **Unread badge**: Topics tab shows count of new messages when not active (e.g., "Topics (5)")
- **Publish to topics**: Right-click → Publish to Topic from the connection tree
- **Subscription sync**: Active subscriptions reflected in the tree with indicator, synced in real-time
- **Topic management**: Create/delete topics, manage durable subscriptions via Jolokia

---

## Getting Help

### In the IDE
- **Status bar**: Shows operation status
- **Tooltips**: Hover over fields
- **Context menus**: Right-click for actions

### Online
- **GitHub Issues**: Report bugs
- **Documentation**: Full technical docs
- **Changelog**: Version history

---

**Happy message browsing! 🚀**

For questions or issues, please check the plugin's GitHub repository.
