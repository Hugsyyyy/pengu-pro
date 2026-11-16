# Security fixes in this fork

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
