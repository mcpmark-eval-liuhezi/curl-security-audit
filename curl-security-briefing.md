# curl Security Briefing — CVE-2023-38545 (and companion CVE-2023-38546)

Short note for the team, sourced from the official curl project advisory, the GitHub
advisory database (GHSA), and the official `curl/curl` GitHub repository.

> Note on delivery: this briefing was intended to be published as a gist named
> `curl-security-briefing.md`. Gist creation was blocked by the current access token
> (403 Resource not accessible by personal access token), so the identical file is
> committed here as the briefing record.

## 1. The main flaw: CVE-2023-38545

**Plain-language description:** When libcurl is asked to send the *host name* to a SOCKS5
proxy (so the proxy resolves it instead of curl), the host name is limited to 255 bytes.
If a longer host name is detected, curl is supposed to switch to resolving it locally and
send only the resolved IP address. Due to this bug, an internal flag could get the wrong
value during a slow SOCKS5 handshake, so curl copied the *too-long host name* into a
fixed-size heap buffer instead — a heap-based buffer overflow. The host name comes from
the URL curl was given, so the attacker controls the overflow content and length. This
could lead to a crash and, in the worst case, arbitrary code execution.

- **Severity:** HIGH per the curl project. The GitHub advisory database
  (GHSA-7xw9-w465-6x42) rates it **Critical, CVSS 3.1 score 9.8**
  (`CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H`), CWE-787 (Out-of-bounds Write).
- **Affected versions:** libcurl **7.69.0 through 8.3.0** (inclusive). Both libcurl and
  the curl tool are affected.
- **Fixed in:** **curl 8.4.0**.
- References: https://curl.se/docs/CVE-2023-38545.html , https://github.com/advisories/GHSA-7xw9-w465-6x42

## 2. The second, smaller flaw: CVE-2023-38546

**Plain-language description:** When a libcurl easy handle with cookies enabled is
duplicated with `curl_easy_duphandle()`, the "cookies enabled" state is cloned but the
cookies themselves are not. If the source handle had never read a cookie file, the clone
records the file name as the literal string `none`. Later use of the cloned handle can
then load cookies from a file named `none` in the program's current directory, letting an
attacker inject cookies into a running program.

- **Severity:** LOW. GitHub advisory database (GHSA-x3qx-m3c2-qfhx) CVSS 3.1 score **3.7**
  (`CVSS:3.1/AV:N/AC:H/PR:N/UI:N/S:U/C:N/I:L/A:N`). Affects libcurl only, not the curl tool.
- **Affected versions:** libcurl **7.9.1 through 8.3.0** (inclusive).
- **Fixed in:** **curl 8.4.0**.
- References: https://curl.se/docs/CVE-2023-38546.html , https://github.com/advisories/GHSA-x3qx-m3c2-qfhx

## 3. Fix verification against the official curl/curl GitHub repository

Both flaws were closed by the **curl 8.4.0** release, cut from the tag **`curl-8_4_0`**:

- **Fixing release:** curl **8.4.0** — https://github.com/curl/curl/releases/tag/curl-8_4_0
- **Release date:** **October 11, 2023** (release published 2023-10-11T06:28:45Z)
- **Exact git tag:** **`curl-8_4_0`**
- **Tag object SHA:** `817204c6e41f66dafbaa704d67f828a4288b3577` (annotated, GPG-signed and
  verified, tagger Daniel Stenberg on 2023-10-11T05:39:40Z)
- **Commit the tag points to:** `d755a5f7c009dd63a61b2c745180d8ba937cbfeb`

## 4. Alert check on my GitHub repositories (HeziLiu)

Repositories enumerated under the account (4 total): `Little-rookie` (own), `Big-Buzzard`
(own), `CodingInterviewChinese2` (fork), `awesome-chatgpt-prompts-zh` (fork).

The check was attempted against **all four repositories** for both code scanning and secret
scanning alerts, and **every single call was explicitly rejected** by the GitHub API with
`403 Resource not accessible by personal access token`:

- **Code scanning alerts:** not retrievable on any of the four repositories — 403 error on
  `Little-rookie`, `Big-Buzzard`, `CodingInterviewChinese2`, and `awesome-chatgpt-prompts-zh`.
- **Secret scanning alerts:** not retrievable on any of the four repositories — the same 403
  error on all four.

**Outcome: the alert status could not be determined via the API with the current
credentials.** No repository was skipped — each one returned a "not accessible" result
rather than an alert list. The check should be repeated manually in the GitHub UI
(Security & analysis / Security tab per repo) or with a token that has the
`security_events` permission.

## 5. Distribution note

The account belongs to one GitHub organization, **`mcpmark-eval-liuhezi`**, but is a member
of **no teams** within it. There is therefore no team-based distribution list to use; the
security note should be circulated directly to the organization / to named individuals
rather than to a team.

## Sources

- curl project advisories: https://curl.se/docs/CVE-2023-38545.html and https://curl.se/docs/CVE-2023-38546.html
- GitHub advisory database: https://github.com/advisories/GHSA-7xw9-w465-6x42 (CVE-2023-38545),
  https://github.com/advisories/GHSA-x3qx-m3c2-qfhx (CVE-2023-38546)
- Official release and tag in curl/curl: https://github.com/curl/curl/releases/tag/curl-8_4_0
- Tracking issue: https://github.com/mcpmark-eval-liuhezi/curl-security-audit/issues/2
