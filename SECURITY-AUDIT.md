# Security audit: curl — filed under the account mcpmark-eval-liuhezi

Evaluation of curl / libcurl as a dependency for the ingestion service. All findings below are sourced from live public sources as of the date of this audit and report current values.

## Official website security page

Sources: the curl project website's security documentation, specifically the "curl CVEs" page (https://curl.se/docs/security.html), the security documentation overview (https://curl.se/docs/secdocs.html), and the project's Vulnerability Disclosure Policy (https://curl.se/dev/vuln-disclosure.html).

- Reporting channel: the vulnerability disclosure policy names **HackerOne** as the channel for reporting a vulnerability. The policy states that the person discovering the issue (the reporter) "reports the vulnerability on HackerOne. Issues filed there reach a handful of selected and trusted people."
- The same page explicitly warns about other channels: security vulnerabilities **should not** be entered in the project's public bug tracker; the project "cannot handle vulnerability reports sent to us over email" and asks that reports not be sent over email. There is also a private `security (at curl dot se)` mailing list, but it is a list for discussions among established project participants (requiring a long-term presence in the project), not the intake channel for new reports.
- Versions receiving security fixes: the policy does not maintain a standing "supported versions" list with named branches or an EOL schedule. What it does say on scope: reports are prioritized for issues reproducible on the **latest release and the current `master` branch**; **Low or Medium severity** vulnerability reports restricted to **releases older than three years are out of scope** and are closed without action; only curl **releases** are ever considered secure — between releases, code in the git repository may be insecure without those flaws counting as vulnerabilities. The project also states there is no bug bounty and it never offers rewards for reported vulnerabilities.
- The security page itself (the "curl CVEs" page) currently lists 239 published vulnerabilities for curl/libcurl, with the most recent batch published 2026-10-14 affecting versions up to 8.22.0 (fixed in 8.23.0).

## Lead maintainer

- The lead maintainer of the curl project is **Daniel Stenberg** — creator and BDFL ("It is likely that our BDFL occupies one of these roles" per the project's incident-response policy), author of the code since the project's founding in 1998.
- GitHub username: **bagder** (https://github.com/bagder), verified live: profile name "Daniel Stenberg", company "@wolfSSL", bio "On team @curl". He also appears as the committer on the project's most recent release (curl-8_23_0).

## Official source repository

- Owner: **curl** (organization)
- Name: **curl**
- Full name: **curl/curl** — https://github.com/curl/curl
- Description: a command line tool and library for transferring data with URL syntax, supporting DICT, FILE, FTP, FTPS, GOPHER, GOPHERS, HTTP, HTTPS, IMAP, IMAPS, LDAP, LDAPS, MQTT, MQTTS, POP3, POP3S, RTSP, SCP, SFTP, SMTP, SMTPS, TELNET, TFTP, WS and WSS. Default branch: `master`.

## Published security advisories (GitHub repository advisories listing)

- The repository's published security advisories listing at https://github.com/curl/curl/security/advisories currently returns **zero (0) advisories** — the listing shows "There aren't any published security advisories". This was cross-checked via the repository security advisories API (empty result).
- Because the listing is empty, there is **no most recent advisory** to report (no identifier, title, or severity). Note: this does not mean the project has no security history — it means the project does not publish its advisories through GitHub's repository advisory feature; instead it publishes them on its own website (239 CVEs to date) and acts as its own CVE Numbering Authority (CNA).

## Latest commit on the default branch (master)

- Short SHA: **a8b9f53** (full SHA: a8b9f53118bf20546001522170050330aa07ce5d)
- Message: **setopt: keep a paused transfer paused when the speed limit is changed** (full message: Curl_rlimit_init() clears the blocked flag of the rate limiter, and unblocking a paused transfer while its client writer/reader is still paused caused the transfer to never finish or hit the pause buffer limit; the fix re-applies the block after the init when that side is paused. Closes #23462.)
- Author: **Rhino** (GitHub user 1rhino2, email 195818135+1rhino2@users.noreply.github.com), committed 2026-10-17; committer: Daniel Stenberg (daniel@haxx.se), committed 2026-10-19.

## Most recent release tag

- The repository uses formal GitHub releases. Latest release: **curl-8_23_0** (release name 8.23.0), published 2026-10-14. This is also the newest release tag in the repo's tag list.

## Health snapshot (current open counts)

- **Open issues: 14**
- **Open pull requests: 78**

## Summary for the security workstream

curl is a mature, actively maintained project with a clear private-disclosure intake (HackerOne), a documented handling process (including pre-announcement distros@openwall coordination, embargoed fixes, and its own CVE numbering as a CNA), and full public disclosure on its website. Notably, it publishes advisories on its website rather than through GitHub's repository advisory feature, so consumers should monitor curl.se and NVD rather than relying on GitHub advisory alerts alone for this dependency. Remediation expectations should be set around the latest release (fixes are prioritized for the newest release and master; low/medium issues on releases older than three years are out of scope), so for the ingestion service we should plan to track and upgrade to each new curl release promptly.
