# CVE Remediation Demo Automations

These prompts are designed to be **reusable across hundreds of repositories**.
Each automation is broken into multiple prompts for reliability and debuggability.

---

## Automation 1: Remediate CVE by ID

**Name:** Remediate CVE by ID  
**Description:** Generic automation that fixes any CVE given its ID

### Prompt 1: Identify and Assess
```
You are a security engineer. Assess whether this repository is affected by:

CVE ID: {{CVE_ID}}

Instructions:
1. Research what this CVE affects (package name, vulnerable versions, vulnerability type)
2. Identify the package manager used in this repository (npm, maven, pip, go mod, cargo, etc.)
3. Check if this repository uses the affected package by examining dependency files
4. If the vulnerable package is present, note:
   - Current version installed
   - Whether it's in the vulnerable range
   - Direct dependency or transitive
5. Summarize your findings:
   - AFFECTED: Package found at vulnerable version, needs remediation
   - NOT AFFECTED: Package not found or already at safe version
   - TRANSITIVE ONLY: Vulnerability is in a transitive dependency

Do not make any changes yet.
```

### Prompt 2: Apply Fix
```
Based on your assessment, remediate the vulnerability:

1. Update the affected package to a patched version
2. Run the appropriate install command to update lock files:
   - npm install / yarn install / pnpm install
   - mvn install / gradle build
   - pip install / poetry install
   - go mod tidy
3. Search the codebase for usage of this package
4. Identify any breaking API changes between the old and new versions
5. Update code to handle breaking changes if needed

Do not run tests yet. Do not commit yet.
```

### Prompt 3: Test and Iterate
```
Verify the fix works:

1. Check if this repository has existing tests:
   - Look for test directories (test/, tests/, __tests__, spec/)
   - Look for test scripts in package.json, Makefile, etc.

2. If tests exist:
   - Run the test suite
   - If tests fail due to your changes, fix the issues
   - Iterate until tests pass

3. If NO tests exist:
   - Write basic tests for the code that uses the updated package
   - Focus on testing the integration points with the updated dependency
   - Run your new tests to verify the fix doesn't break functionality

4. Verify the application builds/compiles successfully
```

### Prompt 4: Commit and PR
```
Finalize the remediation:

1. Create a commit with message: "fix(security): remediate {{CVE_ID}}"
   - Include in the commit body: affected package, old version, new version

2. Create a pull request:
   - Title: "Security: Remediate {{CVE_ID}}"
   - Body should include:
     - CVE ID and severity
     - Package updated and version change
     - Summary of any code changes required
     - Test results

If the repository was NOT AFFECTED, do not create a commit or PR. Simply report "{{CVE_ID}} does not affect this repository."
```

---

## Automation 2: Update Vulnerable Package

**Name:** Update Vulnerable Package  
**Description:** Updates a specific package to a secure version across any repo

### Prompt 1: Detect Package Usage
```
Check if this repository uses the following package:

Package: {{PACKAGE_NAME}}
Minimum Safe Version: {{SAFE_VERSION}}

Instructions:
1. Identify the package manager used in this repository
2. Search dependency files for {{PACKAGE_NAME}}
3. If found, note:
   - Current version
   - Whether it's below {{SAFE_VERSION}}
   - All locations where it's declared (dependencies, devDependencies, etc.)
4. Search the codebase for import/require statements using this package
5. List all files that use {{PACKAGE_NAME}}

Report your findings. Do not make changes yet.
```

### Prompt 2: Apply Update
```
Update {{PACKAGE_NAME}} to {{SAFE_VERSION}} or later:

1. Update the version in all dependency files
2. Run the package manager install command
3. Review the changelog/migration guide for {{PACKAGE_NAME}} between the old and new versions
4. Identify breaking changes that affect this codebase
5. Update all usages in the codebase to handle breaking changes

Do not run tests yet.
```

### Prompt 3: Test and Iterate
```
Verify the update works:

1. Check for existing tests in this repository

2. If tests exist:
   - Run the full test suite
   - If tests fail, analyze the failure
   - Fix issues related to the {{PACKAGE_NAME}} update
   - Re-run tests until they pass

3. If NO tests exist for code using {{PACKAGE_NAME}}:
   - Identify the files that import/use {{PACKAGE_NAME}}
   - Write unit tests covering the basic functionality of those files
   - Run your new tests

4. Attempt to start/build the application to verify it works
```

### Prompt 4: Commit and PR
```
Create the pull request:

1. If package was found and updated:
   - Commit: "fix(security): update {{PACKAGE_NAME}} to {{SAFE_VERSION}}+"
   - PR title: "Security: Update {{PACKAGE_NAME}} to {{SAFE_VERSION}}"
   - PR body: version change, breaking changes addressed, test results

2. If package was not found or already safe:
   - Report "No action needed - {{PACKAGE_NAME}} not found or already at safe version"
   - Do not create a commit or PR
```

---

## Automation 3: Scan and Fix All Critical CVEs

**Name:** Remediate All Critical Vulnerabilities  
**Description:** Scans any repository and fixes all critical/high severity vulnerabilities

### Prompt 1: Audit and Inventory
```
Perform a security audit of this repository:

1. Identify the package manager(s) used
2. Run the appropriate audit command:
   - Node.js: npm audit --json
   - Python: pip-audit --format json OR safety check
   - Java/Maven: mvn dependency-check:check
   - Go: govulncheck ./...
   - Ruby: bundle audit

3. Parse the results and create an inventory:
   - List each vulnerability found
   - Note: CVE ID, severity, affected package, current version, fixed version
   - Prioritize: CRITICAL first, then HIGH

4. Report the total count and list of vulnerabilities to fix

Do not make any changes yet.
```

### Prompt 2: Apply Fixes
```
Fix all CRITICAL and HIGH severity vulnerabilities from your audit:

For each vulnerable package:
1. Update to the patched version noted in the audit
2. If multiple packages need updates, do them all

After all updates:
1. Run the package manager install to update lock files
2. Note any packages that could not be updated (and why)

Do not run tests yet.
```

### Prompt 3: Handle Breaking Changes
```
Address breaking changes from the updates:

1. For each package that was updated to a new major version:
   - Research the breaking changes
   - Search the codebase for affected usage patterns
   - Update code to use new APIs

2. Ensure the application compiles/builds successfully

3. List all code changes made to handle breaking changes
```

### Prompt 4: Test and Iterate
```
Verify all fixes work:

1. If the repository has tests:
   - Run the full test suite
   - For each failing test, determine if it's related to your updates
   - Fix update-related failures
   - Iterate until all tests pass (or document tests that fail for unrelated reasons)

2. If the repository has NO tests:
   - Identify the most critical code paths that use updated packages
   - Write basic integration tests for those paths
   - Run your tests to verify functionality

3. Run the audit command again to verify vulnerabilities are resolved
```

### Prompt 5: Commit and PR
```
Create the pull request:

1. Create a commit with message: "fix(security): remediate critical vulnerabilities"
   - In the commit body, list all CVEs fixed

2. Create a pull request:
   - Title: "Security: Remediate critical vulnerabilities"
   - Body:
     - Summary of vulnerabilities fixed (CVE IDs, packages, severity)
     - Breaking changes addressed
     - Any vulnerabilities that could NOT be fixed (and why)
     - Test results

3. If no vulnerabilities were found, report "No critical vulnerabilities found" and do not create a PR
```

---

## Automation 4: Node.js Version Upgrade

**Name:** Upgrade Node.js Version  
**Description:** Upgrades any Node.js project to a target version

### Prompt 1: Assess Current State
```
Assess this repository for Node.js version upgrade to {{TARGET_NODE_VERSION}}:

1. Confirm this is a Node.js project (look for package.json)
   - If NOT a Node.js project, report "Not a Node.js repository" and stop

2. Identify current Node.js version requirements:
   - Check "engines" field in package.json
   - Check .nvmrc or .node-version files
   - Check CI/CD configs for Node version

3. Identify potential compatibility issues:
   - List dependencies that may not support Node {{TARGET_NODE_VERSION}}
   - Note any usage of deprecated Node.js APIs
   - Check for --openssl-legacy-provider or similar legacy flags

4. Report your findings and upgrade plan
```

### Prompt 2: Update Configuration
```
Update all Node.js version configuration:

1. Update package.json:
   - Set "engines": { "node": ">={{TARGET_NODE_VERSION}}" }
   - Remove legacy flags from scripts (--openssl-legacy-provider, etc.)

2. Update version files:
   - .nvmrc: {{TARGET_NODE_VERSION}}
   - .node-version: {{TARGET_NODE_VERSION}}

3. Update CI/CD configurations:
   - .github/workflows/*.yml
   - .gitlab-ci.yml
   - Jenkinsfile
   - Update Node version to {{TARGET_NODE_VERSION}}

4. Update devcontainer.json if present:
   - Use Node {{TARGET_NODE_VERSION}} image

Do not update dependencies yet.
```

### Prompt 3: Update Dependencies
```
Update dependencies for Node {{TARGET_NODE_VERSION}} compatibility:

1. Check each dependency for Node {{TARGET_NODE_VERSION}} support
2. Update any dependencies that require newer versions for compatibility
3. Run npm install (or yarn/pnpm) to update lock file
4. Fix any deprecated Node.js API usage in the codebase:
   - Buffer() constructor → Buffer.from() or Buffer.alloc()
   - Other deprecated APIs

Do not run tests yet.
```

### Prompt 4: Test and Iterate
```
Verify the Node.js upgrade works:

1. If tests exist:
   - Run the full test suite
   - Fix any failures related to the Node.js upgrade
   - Iterate until tests pass

2. If NO tests exist:
   - Write basic smoke tests that verify the application starts
   - Test critical functionality
   - Run your tests

3. Verify the application starts successfully
```

### Prompt 5: Commit and PR
```
Create the pull request:

1. Commit: "chore: upgrade to Node.js {{TARGET_NODE_VERSION}}"
   - Body: list configuration changes and dependency updates

2. PR title: "Upgrade to Node.js {{TARGET_NODE_VERSION}}"
   - Body:
     - Reason for upgrade
     - Configuration changes made
     - Dependencies updated
     - Breaking changes addressed
     - Test results
```

---

## Demo Flow for Norstella

**Recommended sequence:**

1. **Show the Automation UI** - Create one automation with multiple prompts

2. **Run against nodejs-goof first** - Single repo to show the flow

3. **Then show scale** - Run the same automation against multiple projects
   - "This same automation can run against your 70+ repos in parallel"
   - "Each repo gets its own isolated environment"
   - "PRs are created automatically for affected repos"

4. **Highlight the multi-prompt structure:**
   - "Step 1 assesses - doesn't change anything"
   - "Step 2 applies fixes"
   - "Step 3 tests and iterates until passing"
   - "Step 4 creates the PR only if changes were needed"

5. **Address their specific pain:**
   - "For repos with no tests, Ona writes tests first"
   - "For legacy code nobody knows, Ona figures out the breaking changes"
   - "Your developers just review and merge"
