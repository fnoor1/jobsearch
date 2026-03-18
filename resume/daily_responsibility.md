# Daily Responsibilities — Farhan Noor
## L2 Software Support Engineer, Honeywell (LenelS2)

---

## Primary Role

Provide Level 2 technical support for LenelS2 NetBox physical access control systems deployed at enterprise customer sites worldwide. Troubleshoot complex software, database, network, and integration issues by remotely accessing Linux-based controllers via SSH, running diagnostic queries against PostgreSQL databases, analyzing system logs, and coordinating with field technicians and Value-Added Resellers (VARs).

---

## Daily Responsibilities

### Remote System Troubleshooting
- SSH into customer NetBox controllers (Ubuntu Linux) to diagnose and resolve escalated issues
- Analyze system logs (syslog, catalina, scpdebuglog, activity logs) to identify root causes
- Collect diagnostic files using shell scripts for deeper investigation
- Manage and restart system services and custom daemons (`systemctl`, `s2daemons`) as part of troubleshooting workflows
- Troubleshoot upgrade failures by analyzing upgrade logs, repairing broken packages (`dpkg`, `apt-get`), and manually installing components when needed
- Monitor and manage Tomcat sessions — check active session counts (`session_util summary`), clear session files when session overflow causes system instability
- Monitor system health tables (`s2nchealth`, `s2nchealthmonitoringpoint`) and reset health alerts to resolve dashboard warnings
- Manage disk space — identify large files with `du`/`find`, check disk quotas (`repquota`), clean up overgrown logs and temp files
- Edit controller configuration files (`s2nc.conf`) to adjust session TTL, SSL requirements, and same-IP enforcement settings
- Investigate potentially compromised systems — check for malicious processes (`s2defender`), inspect suspicious files in system directories, escalate findings to senior engineers

### Database Administration & Troubleshooting
- Query and modify PostgreSQL production databases (`psql`) across multiple schemas (s2config, s2dbo, s2dbui) to resolve access control issues
- Fix credential and card format problems by analyzing and correcting binary fieldmask data, parity bit configurations, and encoded number offsets
- Repair broken user permissions by tracing relationships across person, role, and permission tables
- Clean corrupted data (special characters, line breaks) using `REGEXP_REPLACE` across multiple tables
- Perform simulated card reads by inserting test records into Mercury tables for debugging access denied issues
- Manage idProducer badge printing database (SQL Server LocalDB) — clear stuck print queues, remove ghost printers, back up databases, and query/clean up records via SQL Server Management Studio
- Troubleshoot idProducer Print Dispatcher service issues — stop duplicate instances, configure as Windows service, manage startup entries, edit XML config files
- Diagnose idProducer installation failures (disk sector size, Visual C++ Redistributable, .NET prerequisites) using Windows Event Viewer, SQL Server error logs, and `fsutil`
- Manage PostgreSQL triggers — drop and recreate triggers to resolve constraint errors on workflow state tables
- Clear stuck badge print jobs via PostgreSQL (`idp_badging_print_jobs` table) and manage badge templates
- Monitor person record purge queues and verify purge completion status via database queries
- Perform bulk credential format migrations by moving hundreds of users between card formats via SQL UPDATE statements
- Run Data Ops bulk operations using formatted CSV files for mass credential changes

### Network & Security Administration
- Generate CSRs, assemble PEM certificate chains, and upload SSL certificates to controllers
- Troubleshoot network connectivity between controllers and peripheral devices using `tcpdump`, `nc`, `ifconfig`, and port testing
- Diagnose MTU issues affecting node-to-controller communication
- Configure and troubleshoot NAS/SMB backup shares, NTP time sync, and SMTP email settings
- Manage UFW firewall rules on controllers
- Test network connectivity to cloud endpoints (Cumulus, HID Origo) for cloud-managed features

### Access Control System Configuration & Debugging
- Configure and troubleshoot credential formats (Wiegand 26-bit, 34-bit, 37-bit HID, 48-bit Corporate 1000, BCD, Magstripe Track 2) including parity bit definitions and facility code offsets
- Debug Mercury panel communication by analyzing SCP debug logs, correlating Access Control Reader (ACR) IDs, and interpreting access denied reason codes (BIT MISMATCH, NOT IN NODE, PASSBACK VIOLATION, TIME, UNKNOWN, DISABLED, EXPIRED, HOLIDAY, LOCATION, TAILGATE VIOLATION, THREAT LEVEL, WRONG DAY, etc.)
- Troubleshoot Allegion Engage NDE wireless locks and gateways — site creation, lock commissioning, key downloads, database sync, firmware updates
- Configure ASSA ABLOY POE/WiFi locks and Aperio locks, including Double Card Presentation setup
- Set up and troubleshoot HID Mobile credential integration via Origo portal and Cumulus cloud
- Program Blue Diamond readers for OSDP mode using wallet configuration cards
- Configure Card/PIN/Cipher access modes for S2 nodes and Mercury panels
- Debug broken UI pages using Chrome Developer Tools to identify special characters or line breaks causing syntax errors in the web interface
- Troubleshoot network node connectivity using port scanning (`pscan`) and `netstat` for monitoring TCP connections to controllers
- SSH into M1-3200 and Micronode Plus network nodes for advanced debugging — telnet sessions (port 7262), BusyBox shell access, debug mode cycling, firmware reload, factory reset
- Manage remote lockset licensing — configure license counts for Aperio, ASSA WiFi/PoE, NDE, and DK lock types

### Daemon & Service Management
- Monitor and manage 15+ custom application daemons (Tomcat, CherryPy, webs, AppAPIDaemon, nncomm, ncommander, merccomm, ndecomm, ldapcomm, assacomm, and others)
- Adjust daemon log levels (debug 0-5) to capture detailed diagnostics, then reset to production levels after troubleshooting
- Deploy hotfixes by transferring updated binaries via SCP, stopping services, backing up originals, replacing files, setting correct permissions and ownership (`chmod 755/775`, `chown`), and restarting
- Edit systemd service unit files to fix service startup issues (e.g., patching `ExecStartPre` paths), followed by `systemctl daemon-reload`
- Edit application configuration files (`reports.conf`, `s2nc.conf`) to apply workarounds for known bugs

### API Testing & Integration Support
- Test NetBox API (NBAPI v1/v2) using Postman and curl — XML-based requests for login, person search, credential management
- Debug API issues by enabling API debug logging on controllers and analyzing catalina logs
- Enable and configure API debug levels via curl PUT commands
- Enable and verify Data Integration settings on controllers for API connectivity

### LDAP / Active Directory Integration
- Troubleshoot LDAP sync issues between NetBox and customer Active Directory environments
- Manage Java keystores (`keytool`) for LDAPS certificate trust
- Configure ldapcomm daemon settings, search filters, and logging (logback.xml)
- Use LDAP REST API endpoints for troubleshooting sync issues — connection testing, sync start/stop, error analysis, person mapping verification
- Test LDAP authentication via NBAPI curl commands and analyze catalina.out for authentication errors
- Use `openssl s_client` and `openssl x509` to verify LDAPS certificate chains and troubleshoot SSL connection issues

### Hardware Troubleshooting & RMA Submissions
- Diagnose hardware issues on controllers, Mercury panels, NDE locks, network nodes, and readers to determine RMA eligibility
- Submit RMA requests with supporting diagnostic evidence and part numbers
- Verify upgrade package integrity using SHA256 checksums (`sha256sum` on Linux, `certutil -hashfile` on Windows) before deployment

### Knowledge Base Authoring & Documentation
- Author structured KB articles for the team OneNote knowledge base following a standardized format (Title, Overview, Procedure Steps, Applies To, Additional Information)
- Document new troubleshooting procedures, workarounds for known bugs, and integration guides
- Topics authored include: tcpdump installation guides, bulk file removal procedures, timezone fix procedures, SSL certificate workarounds, badge layout SQL migrations, OSDP programming guides, and more

### Case Management & Escalation
- Manage support cases in Salesforce (NEX) — track status, update findings, document resolutions
- Validate callers and VARs before providing support
- Escalate complex issues to L3/senior engineers with detailed diagnostic findings
- Reference known bugs and workarounds tracked in Salesforce (SF-XXXXX), JIRA, and Bugzilla (BZ) defect systems
- Communicate process updates and collaborate with team via Slack

---

## Tools & Technologies Used Daily

| Category | Tools |
|----------|-------|
| Remote Access | SSH, PuTTY, PSCP/SCP |
| Operating Systems | Ubuntu Linux (16/18/24), Windows (for IDP badge printing) |
| Databases | PostgreSQL (psql), SQL Server (LocalDB, SSMS) |
| Web Servers | Apache2, Tomcat (Java), CherryPy (Python) |
| Networking | tcpdump, ifconfig, nc (netcat), netstat, pscan, telnet, ufw, NTP |
| API Testing | Postman, curl (XML-based NBAPI) |
| Certificates | NetBox UI (CSR generation), PEM assembly, Java keytool (LDAPS), OpenSSL (SSL/LDAPS verification) |
| Package Management | dpkg, apt-get |
| Case Management | Salesforce (NEX) |
| Bug Tracking | JIRA, Bugzilla |
| Documentation | OneNote, SharePoint |
| Communication | Slack, Phone queue |
| Scripting | Bash shell scripts, PowerShell (port testing) |
| Debugging | Chrome Developer Tools (UI syntax errors) |
| Text Editors | nano (config files on controller) |
| Windows Tools | Event Viewer, fsutil, certutil, Task Manager, Windows Services (MMC) |

---

## Products Supported

- **NetBox** — Primary product. Linux-based physical access control network controller
- **OnGuard / OnGuard Cloud** — Enterprise access control platform
- **Elements** — Access control product line
- **idProducer (IDP)** — Badge/credential printing system
- **Allegion Engage** — NDE wireless locks and gateways
- **HID Mobile Credentials** — Mobile access via Origo portal
- **Mercury Panels** — Third-party access control panels
- **Cumulus** — LenelS2 cloud portal
- **ASSA ABLOY** — POE/WiFi locks and Aperio locks
- **Blue Diamond** — Readers and mobile app
- **Milestone XProtect** — Video management system integration
- **OKTA** — SSO integration
