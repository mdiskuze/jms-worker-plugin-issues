# JMS Worker Plugin - User Guide

Welcome to the JMS Worker Plugin! This guide will help you get the most out of managing your JMS message queues directly from IntelliJ IDEA.

## Table of Contents
1. [Installation & Setup](#installation--setup)
2. [Quick Start](#quick-start)
3. [Managing Connections](#managing-connections)
4. [Browsing Queues](#browsing-queues)
5. [Sending Messages](#sending-messages)
6. [Topics & Subscriptions](#topics--subscriptions)
7. [Message History](#message-history)
8. [Advanced Operations](#advanced-operations)
9. [Tips & Tricks](#tips--tricks)
10. [Troubleshooting](#troubleshooting)

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
- **Type**: Select "Artemis" or "IBM MQ"
- **Host**: Broker hostname
- **Port**: Broker port (usually 61616 for Artemis, 1414 for IBM MQ)
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

#### IBM MQ
- **Host/Port**: Queue manager connection
- **Queue Manager**: Queue manager name
- **Channel**: Channel name (usually "DEV.APP.SVRCONN")
- **CCSID**: Character encoding (usually 819 for UTF-8)
- **Credentials**: Often required

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
- **Create Queue** — Create new queue (Artemis + Jolokia only)
- **Create Topic** — Create new topic (Artemis + Jolokia only)
- **Send Message to...** — Open send dialog
- **Connect/Disconnect** — Toggle connection state
- **Delete Connection** — Remove from settings

Right-click on a **queue** for:
- **Browse Queue** — Load messages
- **Send Message** — Send to this queue
- **Delete Queue** — Remove queue (Artemis + Jolokia only)
- **Purge Queue** — Delete all messages

Right-click on a **topic** for:
- **Subscribe to Topic** — Opens subscribe dialog with pre-filled topic name
- **Publish to Topic** — Opens send dialog in topic mode
- **Delete Topic** — Remove topic (Artemis + Jolokia only)

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

## Topics & Subscriptions

### Overview

The **Topics** tab provides real-time topic subscription management for Artemis connections. Topics are auto-discovered via Jolokia (MULTICAST addresses) and displayed under each connection in the tree.

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

### Topic Management (Artemis + Jolokia)

- **Create Topic**: Right-click on connection → Create Topic, or right-click on Topics category → Create Topic
- **Delete Topic**: Right-click on topic → Delete Topic (also removes durable subscriptions)

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

### Creating Queues (Artemis)

Requires Jolokia enabled in connection settings:

1. Right-click on connection → **Create Queue...**
2. Enter queue name (e.g., `my.new.queue`)
3. Click OK
4. Queue appears in tree immediately (targeted reload, no tree collapse)

### Creating Topics (Artemis)

Requires Jolokia enabled:

1. Right-click on connection → **Create Topic...**
2. Enter topic name (e.g., `my.new.topic`)
3. Click OK
4. Topic appears under the Topics category immediately

### Deleting Queues/Topics (Artemis)

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

Store common messages in history:
1. Send a message with "Save to history" checked
2. Add a meaningful label like "TEMPLATE: order request"
3. Later: Find in history → Edit & Send with modifications

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

### Topic Issues

**"No topics visible"**
- ✔ Topics require Artemis with Jolokia enabled
- ✔ Check Jolokia URL in connection settings
- ✔ Topics must use MULTICAST routing type on the broker

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
- Provide Jolokia URL (e.g., `http://localhost:8161/console/jolokia`)
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

## What's New in v1.0.3

- 📡 **Artemis topic support**: Auto-discovery, subscribe (durable/non-durable), live message feed
- 🔔 **Unread badge**: Topics tab shows count of new messages when not active (e.g., "Topics (5)")
- 📤 **Publish to topics**: Right-click → Publish to Topic from the connection tree
- 🔄 **Subscription sync**: Active subscriptions reflected in the tree with ● indicator, synced in real-time
- 🏗️ **UI refactoring**: Monolithic panel split into ConnectionTreeManager, TopicPanel, TreeModels
- 🔧 **Tree stability**: Queue count refresh no longer collapses the tree
- 🎯 **Targeted reload**: Creating/deleting queues or topics refreshes only the affected connection
- 📡 **Topic management**: Create/delete topics, manage durable subscriptions via Jolokia

## What's New in v1.0.2

- 🔧 **Fixed queue creation**: Artemis queues now correctly created as ANYCAST
- 🔄 **Live queue counts**: Message counts refresh every 2 seconds in background (Artemis + IBM MQ)
- 🎨 **Proper tool window icons**: Light/dark theme support with correct selected state

## What's New in v1.0.1

- 🚀 **Lazy loading**: Connections appear instantly
- 📊 **Load more**: Pagination for large queues
- 🔌 **Connection selector**: Choose server in send dialog
- 🔄 **ASCII to HEX**: For IBM MQ correlation IDs
- 🔍 **BYTES decoding**: Readable content everywhere
- 🎨 **Accent splitters**: Matches IDEA theme
- 📋 **Copy formatted**: Copies JSON/XML as shown
- ➕ **Create/Delete queues**: Via Jolokia API

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
