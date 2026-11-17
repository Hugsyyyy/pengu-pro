# Pengu Pro: security fixes and changes in this fork

Base: upstream mahdiMGF2/mirzabot 0.9.3 (commit fea8178). Found in a full
read-through of the code before hosting one instance per reseller.

| Severity | Where | Fix |
|---|---|---|
| Critical | index.php `confirmaextras`, `confirmserivce` | customers could set traffic to 0 GB (= unlimited) and renew for free by putting JSON into `Processing_value` through `getusernameinfo` / `getdesdisorder` |
| High | admin.php bot builder | the agent bot token was written unescaped into `vpnbot/<bot>/config.php` (PHP code execution by the admin) |
| Medium | payment/iranpay1.php | callback authority not tied to the order |
| Medium | function.php `outputlink()` | any curl protocol (file://) from a panel-supplied URL |

No backdoor was found: no hard-coded Telegram ids or keys, no hidden commands,
no obfuscated or remotely loaded code, nothing sent to third parties except the
payment gateways an admin enables and `demo.mirzabot.com/b.php` (a plain GET for
exchange rates, used only by the crypto gateways).

Not changed here (handled by the hosting setup instead): everything `.htaccess`
protects must be denied in nginx too (`error_log`, `storage/`, `*.txt`, `*.json`,
`*.sql`, `install/`, `table.php`, `vpnbot/*/config.php`); `/sub/<id>` ids are only
32 bits, so rate-limit that path; TLS verification to the panel is off in
request.php.

## Pengu Pro changes

- **Name:** the bot, web panel and mini app are called Pengu Pro; the about text links this source (AGPL-3.0 section 13); copyright notices stay in LICENSE and the code.
- **Panel lock:** with `$pengu_panel_url` / `$pengu_panel_users` in `config.php` (written by the host), the bot connects only to that PasarGuard panel and those usernames: other panel types, addresses and usernames are refused when added or edited (bot and API), and again before every PasarGuard connection (`token_panel`) and for every other panel type (`PanelRegistry` gives no driver). Panel rows can only be written through those checked paths; anyone able to write the database directly could also edit `config.php`, so that is the hosting sandbox's job, not this lock's.
- **No donation popup** after web panel login.
