# Blocking Specific URLs in MS365

*Posted on March 26, 2026*

Tags: `#powershell` `#security` `#compliance`

---

This is my summary experience for blocking specific URLs in MS365.

The first thing you need to decide is the most suitable method for your environment.
There are two main approaches:

- Device-based
- User-based

## Device-based

### Using Microsoft Defender for Endpoint (MDE)

With the following methods, you can block or allow any **onboarded devices** from accessing
applications, URLs, domains, or IP addresses.

- Web content filtering
- Indicators
- Cloud app catalog

> You may need the appropriate license to apply policies to specific devices.
> Without it, policies may apply to all devices only.

#### Indicator

<img src="https://github.com/Penguinsecq/penguinsecq.github.io/raw/main/docs/images/1.png" alt="Indicator 1" style="border: 1px solid #ccc; border-radius: 4px;">
<img src="../images/2.png" alt="Indicator 2" style="border: 1px solid #ccc; border-radius: 4px;">

#### Web Content Filtering

<img src="../images/3.png" alt="Web Filtering 1" style="border: 1px solid #ccc; border-radius: 4px;">
<img src="../images/4.png" alt="Web Filtering 2" style="border: 1px solid #ccc; border-radius: 4px;">

#### Cloud Apps

<img src="../images/5.png" alt="Cloud Apps" style="border: 1px solid #ccc; border-radius: 4px;">

### Using Microsoft Intune

With Devices → Configuration → Policies, you can block users from accessing specific URLs in Chrome and Edge.

> Third-party browsers can still bypass the blocking rules. If you want to block them,
> you must enable network protection. See:
> [Enable Network Protection](https://learn.microsoft.com/en-us/defender-endpoint/network-protection)

<img src="../images/6.png" alt="Intune Policy" style="border: 1px solid #ccc; border-radius: 4px;">

## User-based

To block or allow specific URLs based on user accounts, you need to use
**Global Secure Access** with Conditional Access policies.

> For setup guidance, see:
> [How to configure Global Secure Access web content filtering](https://learn.microsoft.com/en-us/entra/global-secure-access/how-to-configure-web-content-filtering?tabs=microsoft-entra-admin-center)

## Filter Format used for URL list-based policies

| Syntax | Meaning | Example (will block) |
|---|---|---|
| * | Wildcard (any characters) | Anything |
| [*.] | All subdomains | example.com<br>www.example.com<br>mail.example.com<br>anything.example.com |
| `example.com` | Exact domain | http://example.com<br>https://example.com |
| `example.com/path` | Specific path | https://example.com/path<br>https://example.com/path/abc |
| *:// | Any protocol | ftp://example.com<br>http://example.com |
| badsite | Block keyword in URL | https://abc.com/badsite<br>https://badsite.com |

## Reference

https://www.anoopcnair.com/block-urls-on-google-chrome-and-microsoft-edge

https://learn.microsoft.com/en-us/DeployEdge/edge-learnmmore-url-list-filter%20format

---

> **Execution Scope Reminder**
> Always review local execution restrictions before performing administrative infrastructure baseline testing queries.

## Technical Implementation

You can run structural deployment loops cleanly without sacrificing readability parameters inside the code containers:

```powershell
# Check system auditing states
Get-AuditPolicy -Target "Application"
```
