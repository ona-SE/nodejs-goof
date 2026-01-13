# CVE Remediation Demo Automations

Reusable automation prompts for CVE remediation at scale.

---

## Automation 1: Remediate CVE by ID

**Name:** Remediate CVE by ID  
**Description:** Fixes any CVE given its ID - works across any repository

### Prompt 1: Research CVE
```
CVE ID: {{CVE_ID}}

Research what this CVE affects (package name, vulnerable versions, vulnerability type). Identify the package manager used in this repository. Check if this repository uses the affected package by examining dependency files. If the vulnerable package is present, note the current version installed, whether it's in the vulnerable range, and if it's a direct or transitive dependency.

Summarize findings as:
- AFFECTED: Package found at vulnerable version, needs remediation
- NOT AFFECTED: Package not found or already at safe version
- TRANSITIVE ONLY: Vulnerability is in a transitive dependency

Do not make any changes yet.
```

### Prompt 2: Analyze Dependency Tree
```
Check if this repository has the vulnerable library. Look at dependency files and analyze the full dependency tree including transitive dependencies. Report the version and whether it's direct or transitive. If not found or already patched, say so and skip remaining work. Do not analyze source code yet. Output: one sentence status only.
```

### Prompt 3: Check Reachability
```
If the vulnerable library was found: search source code to find if our code calls the vulnerable functions. Show the call chain. If the library was not found or not reachable, skip this work. Do not modify any files yet. Output: one sentence status only.
```

### Prompt 4: Apply Fix and Test
```
If the vulnerability is reachable: update the dependency to the fixed version, adjust code if APIs changed. Run tests if they exist. If no tests exist for the affected code paths, write basic tests to verify the fix doesn't break functionality, then run them. If the vulnerability was not found or not reachable, skip this work. Do not create a PR yet. Output: one sentence status only.
```

### *PR creation inserted here*

### Prompt 5: Security Report
```
Generate a security analysis report using all information from previous steps. Format:

**Result:** [Vulnerable/Not Vulnerable/Already Patched] - one line summary

**Impact:** CVSS score and worst-case scenario

**Evidence:** Call chain showing reachability (or why not reachable)

**Fix:** What was changed (if applicable)

**Verification:** Test results (if applicable)

Put the outcome first. Be concise.
```

---

## Automation 2: Update Vulnerable Package

**Name:** Update Vulnerable Package  
**Description:** Updates a specific package to a secure version across any repo

### Prompt 1: Detect Package
```
Package: {{PACKAGE_NAME}}
Minimum Safe Version: {{SAFE_VERSION}}

Check if this repository uses {{PACKAGE_NAME}} by examining dependency files and the full dependency tree. Report the current version and whether it's direct or transitive. If not found or already at {{SAFE_VERSION}} or higher, say so and skip remaining work. Do not make changes yet. Output: one sentence status only.
```

### Prompt 2: Check Usage
```
If {{PACKAGE_NAME}} was found at a vulnerable version: search source code for all imports and usages of this package. List the files and functions that use it. If the package was not found, skip this work. Do not modify files yet. Output: one sentence status only.
```

### Prompt 3: Apply Update and Test
```
If {{PACKAGE_NAME}} needs updating: update to {{SAFE_VERSION}} or later, run install to update lock files, adjust code if APIs changed between versions. Run tests if they exist. If no tests cover the affected code, write basic tests then run them. If no update needed, skip this work. Do not create a PR yet. Output: one sentence status only.
```

### *PR creation inserted here*

### Prompt 4: Summary Report
```
Generate a summary report. Format:

**Result:** [Updated/Not Found/Already Safe] - one line summary

**Change:** Package version change (if applicable)

**Code Changes:** Files modified to handle API changes (if applicable)

**Verification:** Test results (if applicable)

Be concise.
```

---

## Automation 3: Scan and Fix All Critical CVEs

**Name:** Remediate All Critical Vulnerabilities  
**Description:** Scans any repository and fixes all critical/high severity vulnerabilities

### Prompt 1: Audit
```
Run a security audit of this repository. Identify the package manager and run the appropriate audit command. List all CRITICAL and HIGH severity vulnerabilities found with CVE ID, affected package, current version, and fixed version. If no vulnerabilities found, say so and skip remaining work. Do not make changes yet.
```

### Prompt 2: Analyze Reachability
```
For each vulnerability found: check if the vulnerable code path is reachable from our source code. Prioritize by reachability and severity. If no vulnerabilities were found, skip this work. Do not modify files yet. Output: brief status for each CVE.
```

### Prompt 3: Apply Fixes and Test
```
For each reachable vulnerability: update the affected package to the fixed version, adjust code if APIs changed. After all updates, run tests if they exist. If no tests exist, write basic tests for the most critical code paths affected, then run them. If no vulnerabilities to fix, skip this work. Do not create a PR yet. Output: one sentence status only.
```

### *PR creation inserted here*

### Prompt 4: Security Report
```
Generate a security remediation report. Format:

**Summary:** X vulnerabilities found, Y fixed, Z not reachable

**Fixed:**
- CVE-XXXX: package X.X.X → Y.Y.Y (severity)
- ...

**Not Reachable:** (list any skipped and why)

**Could Not Fix:** (list any that failed and why)

**Verification:** Test results

Be concise.
```

---

## Automation 4: Node.js Version Upgrade

**Name:** Upgrade Node.js Version  
**Description:** Upgrades any Node.js project to a target version

### Prompt 1: Assess
```
Target Version: {{TARGET_NODE_VERSION}}

Check if this is a Node.js project. If not, say "Not a Node.js repository" and skip remaining work. If yes, identify current Node.js version from package.json engines, .nvmrc, or CI configs. List potential compatibility issues with Node {{TARGET_NODE_VERSION}}. Do not make changes yet. Output: brief assessment.
```

### Prompt 2: Update Configuration
```
If this is a Node.js project: update all version configuration (package.json engines, .nvmrc, .node-version, CI configs, devcontainer.json) to Node {{TARGET_NODE_VERSION}}. Remove legacy flags like --openssl-legacy-provider from scripts. If not a Node.js project, skip this work. Do not run tests yet. Output: one sentence status only.
```

### Prompt 3: Update Dependencies and Test
```
Update any dependencies incompatible with Node {{TARGET_NODE_VERSION}}. Fix deprecated API usage (Buffer constructor, etc.). Run install, then run tests if they exist. If no tests exist, write a basic smoke test that verifies the application starts, then run it. If not a Node.js project, skip this work. Do not create a PR yet. Output: one sentence status only.
```

### *PR creation inserted here*

### Prompt 4: Summary Report
```
Generate an upgrade report. Format:

**Result:** [Upgraded/Not a Node.js Project] - one line summary

**Version Change:** Node X → {{TARGET_NODE_VERSION}}

**Config Updated:** List of files changed

**Dependencies Updated:** List of packages updated for compatibility

**Verification:** Test results

Be concise.
```

---

## Demo Tips for Norstella

1. **Show Automation 1** with a real CVE ID (e.g., `CVE-2021-23337` for lodash)

2. **Highlight the reachability check** - "We don't just find the package, we check if your code actually calls the vulnerable functions"

3. **Show scale** - Run against multiple projects, PRs created in parallel

4. **Address their pain points:**
   - "For repos with no tests, Ona writes tests first"
   - "For legacy code, Ona traces the call chain to find what's actually affected"
   - "One sentence outputs so you can see status at a glance across 70 repos"
