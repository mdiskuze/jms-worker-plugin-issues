# jms-worker-plugin-issues
Issues for Idea jms-worker-plugin

# JMS Worker Plugin - User Guide

Welcome to the JMS Worker Plugin! This guide will help you get the most out of managing your JMS message queues directly from IntelliJ IDEA.

## Table of Contents
1. [Installation & Setup](#installation--setup)
2. [Quick Start](#quick-start)
3. [Managing Connections](#managing-connections)
4. [Browsing Queues](#browsing-queues)
5. [Sending Messages](#sending-messages)
6. [Message History](#message-history)
7. [Advanced Operations](#advanced-operations)
8. [Tips & Tricks](#tips--tricks)
9. [Troubleshooting](#troubleshooting)

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

1. Open **Preferences/Settings** → **JMS Worker** → **Connections**
2. Click **Add** (+ button)
3. Fill in connection details:
  - **Name**: Give your connection a meaningful name (e.g., "Dev Artemis")
  - **Type**: Select "Artemis" or "IBM MQ"
  - **Host**: Broker hostname
  - **Port**: Broker port (usually 61616 for Artemis, 1414 for IBM MQ)
  - **Username/Password**: Leave empty if not required
4. Click **Test Connection** to verify
5. Click **OK** to save

### Step 2: Browse a Queue

1. In the JMS Worker tool window, select your connection from the dropdown
2. Select a queue from the queue dropdown
3. You'll see all messages in the queue listed in the table
4. Click a message to see its full content in the detail pane

### Step 3: Send a Message

1. Right-click on a queue in the queue list
2. Select **Send Message** (or use the send button in toolbar)
3. Fill in the message details:
  - **Queue**: Target queue (pre-filled if you right-clicked)
  - **Message Format**: TextMessage or BytesMessage
  - **Body**: Your message content
  - **Headers** (optional): Correlation ID, Type, Priority, etc.
4. Click **Send**
5. You'll see a confirmation message

---

## Managing Connections

### Connection Types

#### Apache ActiveMQ Artemis
- **Host/Port**: Direct TCP connection
- **Credentials**: Optional username/password
- **Jolokia** (optional): Management API URL for advanced operations

#### IBM MQ
- **Host/Port**: Queue manager connection
- **Queue Manager**: Queue manager name
- **Channel**: Channel name (usually "DEV.APP.SVRCONN")
- **CCSID**: Character encoding (usually 819 for UTF-8)
- **Credentials**: Often required

### Testing Connections

Before saving, always test your connection:
1. Fill in the connection details
2. Click **Test Connection**
3. You'll see "Connection successful" or an error message
4. Only save if the test passes

### Editing Connections

1. Go to **Preferences/Settings** → **JMS Worker** → **Connections**
2. Select a connection and click **Edit**
3. Modify the settings
4. Test the connection again
5. Click **OK** to save

### Removing Connections

1. Go to **Preferences/Settings** → **JMS Worker** → **Connections**
2. Select a connection
3. Click **Remove** (X button)
4. Confirm the deletion

---

## Browsing Queues

### Filtering Messages

The JMS Worker tool window has a search bar at the top:
- **Search by**: Message body, label, correlation ID, or queue name
- Type your search term and results update in real-time
- Leave empty to see all messages

### Message Details

Click on any message to see:
- **Sent Time**: When the message was sent
- **Message Headers**: Correlation ID, Type, Priority, Delivery Mode, TTL, etc.
- **Properties**: Custom message properties
- **Body**: Full message content (with syntax highlighting for JSON/XML)

### Large Messages

If a message is very large:
- The table preview shows the first 100 characters
- Click the message to see the full content
- Copy button is available in the context menu

### Navigating Messages

The status bar shows: `N messages shown (M total in history)`
- Scroll to load more messages if there are many in the queue
- Use Ctrl+F to find within the detail pane

---

## Sending Messages

### Basic Message

1. Click **Send Message** button (or right-click queue → Send Message)
2. Fill in required fields:
  - **Queue**: Target queue (required)
  - **Body**: Message content (required)
3. Click **Send**

### With Headers

In the **Message Headers** section:
- **Correlation ID**: For request/reply patterns (optional)
- **Type**: JMS message type identifier (optional)
- **Priority**: 0-4 (normal), 5-9 (expedited), default 4
- **Persistent**: Check if message should survive broker restart

### Advanced Options

Click the **Extended Options** arrow to expand:
- **Reply-To Queue**: Queue where responses should be sent
- **Time-To-Live (ms)**: How long the message lives before expiring (0 = no expiration)
- **Group ID**: For message grouping across broker

### Custom Properties

In the **Custom Properties** section:
- One property per line in format: `key=value`
- Example:
  ```
  environment=production
  version=2.1
  ```

### Message Format

Select the message format:
- **TextMessage**: Plain text (default)
- **BytesMessage**: Binary data with encoding
  - Choose encoding: UTF-8, UTF-16, ISO-8859-1, ASCII, UTF-32, CP1252, CP912, EBCDIC-US
  - Useful for special character sets (e.g., CP912 for Cyrillic)

### Saving to History

Check **Save to history** to store this message for later reuse:
- Add a **Label** for easy identification
- Message is saved with all properties
- Resend anytime from the History tab

---

## Message History

### Accessing History

Click the **Message History** tab at the bottom of the tool window.

### Filtering History

- **Connection**: Filter by which connection the message was sent from
- **Queue**: Filter by target queue
- **Search**: Find messages by body content, label, or correlation ID

### Resending Messages

1. Select a message from the history table
2. Click **Resend** button
3. Confirm the action
4. Message is sent with original properties

### Editing Before Resend

1. Select a message
2. Click **Edit & Send**
3. Modify any details you want to change
4. Click **Send**
5. Original message remains in history; new one is added

### Labeling Messages

1. Select a message
2. Click **Set Label**
3. Enter a meaningful label (e.g., "Test order #123")
4. Press Enter

### Deleting Messages from History

1. Right-click a message
2. Select **Delete**
3. Confirm deletion (cannot be undone)

### Cleaning Up History

To remove old messages and keep history manageable:
1. Click **Clear Old** button
2. Enter number of days (e.g., 30)
3. All messages older than that date are deleted

To delete everything:
1. Click **Clear All** button
2. Confirm deletion (cannot be undone)

---

## Advanced Operations

### Moving Messages Between Queues

1. Select a message in the queue browser
2. Right-click → **Move to Queue**
3. Select target queue
4. Message is removed from source and added to target

### Copying Messages

1. Select a message
2. Right-click → **Copy to Queue**
3. Select target queue
4. Message stays in source, copy is in target

### Queue Management

For the selected queue:
- **Get Message Count**: Shows how many messages are in the queue
- **Purge Queue**: Remove all messages (⚠️ cannot be undone!)

---

## Tips & Tricks

### Keyboard Shortcuts

- **Ctrl+F**: Find within message body
- **Ctrl+C**: Copy selected message body
- **Delete**: Delete selected message from history
- **Double-click**: Edit & resend message from history

### Organizing with Labels

Use labels creatively:
- `BUG-123: order processing error`
- `TEST: batch import #5`
- `PROD: migration script v2.3`

### Batch Testing

1. Create a template message in history
2. Duplicate it with different test data:
  - Edit & Send with variations
  - All versions stay in history for comparison

### Message Templates

Store common messages in history:
- Order creation requests
- Payment notifications
- System alerts
- Test data

Resend anytime without retyping.

### Performance Tips

For large queues:
- Use filters to narrow down results
- Don't browse more messages than necessary
- Close unused connections to free resources
- Periodically clean up old history entries

### Debugging Messages

1. Send a message and keep the confirmation dialog open
2. Check the broker logs simultaneously
3. Use status bar messages to track operations
4. Check history to verify what was actually sent

---

## Troubleshooting

### Connection Issues

**Problem**: "Cannot connect to broker"
- ✓ Check hostname and port are correct
- ✓ Verify network connectivity to broker
- ✓ Check firewall rules
- ✓ Ensure credentials are correct (if required)
- ✓ Try test connection first

**Problem**: "Authentication failed"
- ✓ Verify username and password
- ✓ Check if account is enabled on broker
- ✓ For IBM MQ, check CCSID and channel name

### Message Issues

**Problem**: "Cannot see my messages"
- ✓ Check you're connected to correct broker
- ✓ Select the correct queue from dropdown
- ✓ Try clicking Refresh button
- ✓ Check filter isn't hiding messages

**Problem**: "Message sending failed"
- ✓ Check queue exists and is accessible
- ✓ Verify message body is not empty
- ✓ Check broker has space available
- ✓ Try sending simpler test message first

**Problem**: "Special characters not displaying correctly"
- ✓ For BytesMessage, select correct encoding (e.g., CP912 for Cyrillic)
- ✓ Check message format in history matches source
- ✓ Try UTF-8 encoding first (most compatible)

### Performance Issues

**Problem**: "UI freezing when browsing large queue"
- ✓ Use search/filter to narrow messages
- ✓ Don't request more than 500 messages at once
- ✓ Increase IDE memory: Edit configuration → increase Xmx

**Problem**: "History database growing too large"
- ✓ Click **Clear Old** to remove messages older than N days
- ✓ Delete specific labels/connections you no longer need
- ✓ Regular maintenance keeps UI responsive

### Database Issues

**Problem**: "History not working or showing errors"
- ✓ Close the plugin (disconnect all)
- ✓ Go to **~/.config/JetBrains/IntelliJIDEA*/system/jms-worker/**
- ✓ Delete **message-history.db** file
- ✓ Reopen plugin (database recreates automatically)
- ⚠️ This deletes all history!

---

## Getting Help

### In the IDE

- **Status bar**: Shows current operation status and errors
- **Log messages**: Right-click queue/connection for context menus
- **Tool tips**: Hover over fields for inline help

### Online Resources

- **GitHub Issues**: Report bugs or request features
- **Documentation**: Check the plugin documentation
- **FAQ**: Common questions and solutions

---

## What's Next?

- Explore the **Message History** tab to organize your sent messages
- Create connection profiles for all your environments (dev, test, prod)
- Use labels to track important or test messages
- Try the advanced operations (move, copy, purge) on test queues first

---

**Happy message browsing! 🚀**

For questions or issues, please check the plugin's GitHub repository or contact support.
