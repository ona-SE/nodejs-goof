# CVE Remediation Demo Automations

These prompts are designed to be used with Ona's Automations feature (via the UI).
Copy these into the Automations UI when creating new automations for the demo.

---

## Automation 1: Fix Lodash CVEs (Quick Win)

**Name:** Fix Lodash Prototype Pollution CVEs
**Description:** Remediates CVE-2019-10744, CVE-2020-8203, CVE-2021-23337 in lodash

### Prompt:
```
You are a security engineer tasked with remediating CVE vulnerabilities.

The repository has lodash@4.17.4 which contains these critical CVEs:
- CVE-2019-10744: Prototype Pollution (CVSS 9.1)
- CVE-2020-8203: Prototype Pollution via zipObjectDeep
- CVE-2021-23337: Command Injection in template function

Your task:
1. Update lodash to version 4.17.21 or later in package.json
2. Run `npm install` to update package-lock.json
3. Search the codebase for any lodash usage that might be affected
4. Verify the application still starts with `npm start`
5. Create a commit with message: "fix(security): remediate lodash CVEs (CVE-2019-10744, CVE-2020-8203, CVE-2021-23337)"
6. Create a pull request with the fix
```

---

## Automation 2: Fix EJS Template Injection (Critical)

**Name:** Fix EJS Template Injection CVE
**Description:** Remediates CVE-2022-29078 (CVSS 9.8 Critical)

### Prompt:
```
You are a security engineer tasked with remediating a critical CVE vulnerability.

The repository has ejs@1.0.0 which contains:
- CVE-2022-29078: Server-Side Template Injection (CVSS 9.8 Critical)

This is a CRITICAL vulnerability that allows remote code execution.

Your task:
1. Update ejs to version 3.1.9 or later in package.json
2. Run `npm install` to update package-lock.json
3. Check the views/ directory for any EJS templates
4. EJS 3.x has some syntax differences - review templates for compatibility
5. Start the application with `npm start` to verify templates render
6. Create a commit with message: "fix(security): remediate critical EJS CVE-2022-29078"
7. Create a pull request with the fix
```

---

## Automation 3: Fix All Critical CVEs (Full Demo)

**Name:** Remediate All Critical CVEs
**Description:** Comprehensive CVE remediation across all vulnerable dependencies

### Prompt:
```
You are a security engineer tasked with remediating all critical CVE vulnerabilities in this Node.js application.

Analyze package.json - this repo has these vulnerable packages:
- lodash@4.17.4 - Multiple prototype pollution CVEs
- ejs@1.0.0 - Template injection CVE-2022-29078 (CRITICAL)
- mongoose@4.2.4 - Prototype pollution CVE-2022-2564
- marked@0.3.5 - ReDoS vulnerabilities
- adm-zip@0.4.7 - Path traversal CVE-2018-1002204
- moment@2.15.1 - ReDoS CVE-2022-31129

Your task:
1. Update ALL of the above packages to their latest secure versions:
   - lodash: ^4.17.21
   - ejs: ^3.1.9
   - mongoose: ^8.0.0 (note: major breaking changes, use promises not callbacks)
   - marked: ^9.0.0
   - adm-zip: ^0.5.10
   - moment: ^2.29.4 (or consider replacing with date-fns)

2. Run `npm install` to update package-lock.json

3. Fix breaking changes:
   - mongoose 8.x uses promises, not callbacks - update mongoose-db.js
   - ejs 3.x may have template syntax changes - check views/
   - marked 9.x has API changes - check any markdown rendering

4. Verify the application starts with `npm start`

5. Run `npm audit` to confirm vulnerabilities are resolved

6. Create a detailed commit message listing all CVEs remediated

7. Create a pull request titled "fix(security): remediate critical CVEs across dependencies"

Prioritize getting the application working. Document any issues in the PR description.
```

---

## Automation 4: Node.js Version Upgrade

**Name:** Upgrade to Node.js 20 LTS
**Description:** Modernize the application to run on Node.js 20

### Prompt:
```
You are a platform engineer tasked with upgrading this application to Node.js 20 LTS.

The application currently uses legacy Node.js patterns and the --openssl-legacy-provider flag.

Your task:
1. Add an "engines" field to package.json: "node": ">=20"

2. Update the npm scripts to remove NODE_OPTIONS=--openssl-legacy-provider:
   - "dev": "nodemon ./app.js"
   - "start": "node app.js"

3. Update dependencies that may be incompatible with Node 20

4. Review the codebase for deprecated Node.js APIs

5. Update .devcontainer/devcontainer.json to use Node 20 image:
   "image": "mcr.microsoft.com/devcontainers/javascript-node:20"

6. Test that the application starts and runs correctly

7. Create a commit with message: "chore: upgrade to Node.js 20 LTS"

8. Create a pull request with the changes
```

---

## Demo Flow Suggestion

For the Norstella demo, recommend this sequence:

1. **Show the vulnerabilities first**
   - Run `npm audit` to show the CVE list
   - Highlight the critical ones (EJS, lodash)

2. **Run Automation 1 (Lodash)** - Quick win, ~2-3 minutes
   - Simple version bump
   - No breaking changes
   - Shows the basic flow

3. **Run Automation 3 (All Critical)** - Full demo, ~10-15 minutes
   - Shows handling of breaking changes
   - Demonstrates iteration and testing
   - Creates comprehensive PR

4. **Show the results**
   - Open the PR
   - Run `npm audit` again to show resolved CVEs
   - Highlight the time saved vs manual remediation
