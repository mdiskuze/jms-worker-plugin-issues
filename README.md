# JMS Worker Plugin — Issues

Public issue tracker for the **JMS Worker** IntelliJ IDEA plugin.

The plugin lets you browse, send, move, and subscribe to messages on Apache ActiveMQ Artemis, ActiveMQ Classic 5.x, and IBM MQ brokers directly from IntelliJ IDEA.

| Where | Link |
|---|---|
| Plugin on JetBrains Marketplace | <https://plugins.jetbrains.com/plugin/com.mira.jmsworker> |
| Source code | <https://github.com/mdiskuze/jms-worker-plugin> |
| Releases (with downloadable `.zip`) | <https://github.com/mdiskuze/jms-worker-plugin/releases> |
| User guide | <https://github.com/mdiskuze/jms-worker-plugin/blob/master/README-USER.md> |
| Changelog | <https://github.com/mdiskuze/jms-worker-plugin/blob/master/CHANGELOG.md> |

## Reporting a Bug or Requesting a Feature

The fastest path is the in-IDE **Report a Problem** action — it pre-fills the diagnostics block (plugin / IDE / JVM / OS versions, configured connection types) and the failing connection/error context if you triggered it from an error notification:

1. **From a connection-error balloon** — click **Report this**.
2. **From the JMS Worker tool window** — click the globe icon next to Settings.

The dialog opens a pre-filled GitHub *New Issue* page in your browser. Sign in with your GitHub account and submit. The plugin itself does not call any GitHub API and never sends credentials.

If you'd rather skip the in-IDE form, [open a new issue here](https://github.com/mdiskuze/jms-worker-plugin-issues/issues/new) and include:

- **Plugin version** — Settings → Plugins → JMS Worker
- **IDE** — IntelliJ IDEA Ultimate / Community, version, build number (Help → About)
- **OS and JVM** — `Help → About → Copy and Paste`
- **Broker** — Apache Artemis / ActiveMQ Classic 5.x / IBM MQ; version; auth mode (None / Basic / mTLS / Basic + mTLS for IBM MQ)
- **Steps to reproduce**
- **Expected vs actual behaviour**
- **IDE log** — `Help → Show Log in Files` (the relevant `idea.log`); attach or paste the lines around the failure

Sensitive values you should **not** paste verbatim: passwords, keystore files, broker hostnames you'd rather keep private. The plugin never includes those in the diagnostics block; if you copy log lines manually, scrub them before posting.

## Discussions

For questions that aren't bugs ("how do I do X", "should I be using …"), use the [Discussions tab](https://github.com/mdiskuze/jms-worker-plugin-issues/discussions) if enabled, or open an issue with the `question` label.

## Labels

The maintainer applies labels to triage incoming reports:

| Label | Meaning |
|---|---|
| `user-report` | Auto-applied by the in-IDE Report a Problem action. |
| `bug` | Confirmed defect. |
| `enhancement` | Feature request or improvement idea. |
| `question` | Usage question, not a bug. |
| `wontfix` | Acknowledged but out of scope or against design intent. |
| `needs-info` | Waiting for more details from the reporter. |

## Security

If you find a security issue (credential leak, code execution path, anything that an attacker could use), please **do not** open a public issue. Instead contact the maintainer directly via the email shown on the [plugin's Marketplace page](https://plugins.jetbrains.com/plugin/com.mira.jmsworker) or via a GitHub private message. We'll coordinate disclosure.

## License

Plugin source is licensed under [the LICENSE in the main repo](https://github.com/mdiskuze/jms-worker-plugin/blob/master/LICENSE.txt).
