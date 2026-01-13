# CVE Remediation Demo Automations

These prompts are designed to be **reusable across hundreds of repositories**.
They are generic and will work on any repo with the specified vulnerability type.

---

## Automation 1: Fix Specific CVE by ID

**Name:** Remediate CVE by ID
**Description:** Generic automation that fixes any CVE given its ID

### Prompt:
```
You are a security engineer. Remediate the following CVE in this repository:

CVE ID: {{CVE_ID}}

Instructions:
1. Research what this CVE affects (package, versions, vulnerability type)
2. Check if this repository uses the affected package by examining package.json, pom.xml, requirements.txt, go.mod, or other dependency files
3. If the vulnerable package is present:
   - Update it to a patched version
   - Run the package manager install command (npm install, mvn install, pip install, etc.)
   - Check for breaking API changes and fix any compatibility issues
   - Run available tests to verify the fix
4. If the vulnerable package is NOT present, report "CVE does not apply to this repository"
5. Create a commit with message: "fix(security): remediate {{CVE_ID}}"
6. Create a pull request titled "Security: Remediate {{CVE_ID}}"
```

**Usage:** Replace `{{CVE_ID}}` with the actual CVE (e.g., CVE-2021-23337)

---

## Automation 2: Fix Vulnerable Package

**Name:** Update Vulnerable Package
**Description:** Updates a specific package to a secure version across any repo that uses it

### Prompt:
```
You are a security engineer. Update the following package to remediate known vulnerabilities:

Package: {{PACKAGE_NAME}}
Minimum Safe Version: {{SAFE_VERSION}}

Instructions:
1. Check if this repository uses {{PACKAGE_NAME}} in its dependencies
2. If found and version is below {{SAFE_VERSION}}:
   - Update to {{SAFE_VERSION}} or later
   - Run the appropriate install command for this project
   - Search the codebase for usage of this package
   - Fix any breaking changes from the version update
   - Run available tests
   - Create a commit: "fix(security): update {{PACKAGE_NAME}} to {{SAFE_VERSION}}+"
   - Create a pull request
3. If package not found or already at safe version, report "No action needed"
```

**Usage:** Replace `{{PACKAGE_NAME}}` and `{{SAFE_VERSION}}` (e.g., lodash, 4.17.21)

---

## Automation 3: Scan and Fix All Critical CVEs

**Name:** Remediate All Critical Vulnerabilities
**Description:** Scans any repository and fixes all critical/high severity vulnerabilities

### Prompt:
```
You are a security engineer performing a vulnerability remediation sweep.

Instructions:
1. Identify the package manager used in this repository (npm, maven, pip, go mod, etc.)

2. Run the appropriate audit command:
   - Node.js: npm audit
   - Python: pip-audit or safety check
   - Java/Maven: mvn dependency-check:check
   - Go: govulncheck ./...

3. For each CRITICAL or HIGH severity vulnerability found:
   - Update the affected package to a patched version
   - Run install to update lock files
   - Check for and fix any breaking changes
   - Run available tests

4. After all updates:
   - Run the audit command again to verify fixes
   - Document any vulnerabilities that cannot be fixed (and why)

5. Create a commit listing all CVEs remediated

6. Create a pull request with:
   - Title: "Security: Remediate critical vulnerabilities"
   - Body: List of CVEs fixed and any remaining issues
```

---

## Automation 4: Node.js Version Upgrade

**Name:** Upgrade Node.js Version
**Description:** Upgrades any Node.js project to a target version

### Prompt:
```
You are a platform engineer upgrading this repository to a newer Node.js version.

Target Version: {{TARGET_NODE_VERSION}}

Instructions:
1. Check if this is a Node.js project (look for package.json)

2. If Node.js project:
   - Update or add "engines" field in package.json to specify >= {{TARGET_NODE_VERSION}}
   - Remove any legacy Node.js flags (--openssl-legacy-provider, etc.) from scripts
   - Update .nvmrc or .node-version if present
   - Update any CI/CD configs (.github/workflows, .gitlab-ci.yml) to use Node {{TARGET_NODE_VERSION}}
   - Update devcontainer.json if present

3. Check for deprecated APIs and update:
   - Replace deprecated Buffer() constructor with Buffer.from() or Buffer.alloc()
   - Update any other deprecated Node.js APIs

4. Update dependencies that are incompatible with Node {{TARGET_NODE_VERSION}}

5. Run npm install and npm test

6. Create a commit: "chore: upgrade to Node.js {{TARGET_NODE_VERSION}}"

7. Create a pull request

If not a Node.js project, report "Not a Node.js repository"
```

**Usage:** Replace `{{TARGET_NODE_VERSION}}` (e.g., 20)

---

## Automation 5: Dependency Upgrade by Type

**Name:** Upgrade All Dependencies of Type
**Description:** Upgrades all packages of a certain type (e.g., all security-related, all testing frameworks)

### Prompt:
```
You are an engineer performing a dependency upgrade sweep.

Upgrade Type: {{UPGRADE_TYPE}}

Instructions based on upgrade type:

If "security" - Update all packages with known vulnerabilities to patched versions
If "major" - Update all dependencies to latest major versions
If "minor" - Update all dependencies to latest minor versions (non-breaking)
If "outdated" - Update any dependency more than {{AGE}} behind latest

For each upgrade:
1. Update the dependency
2. Run install command
3. Fix breaking changes
4. Run tests
5. If tests fail, attempt to fix or rollback that specific upgrade

Create a pull request summarizing all upgrades made.
```

---

## Demo Tips

**For Norstella demo:**

1. **Show scale** - Create the automation once, then run it against multiple projects simultaneously

2. **Use generic prompts** - Demonstrate that the same automation works across different repos

3. **Parameterize** - Show how `{{CVE_ID}}` or `{{PACKAGE_NAME}}` can be swapped for different vulnerabilities

4. **Real-world scenario:**
   - "Your security team identifies CVE-2021-23337 in lodash"
   - "You run this automation against all 70 repos"
   - "Ona creates PRs for every affected repo in parallel"
   - "Developers just review and merge"
