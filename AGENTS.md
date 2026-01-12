# nodejs-goof - Vulnerable Demo Application

This is an intentionally vulnerable Node.js application for demonstrating CVE remediation with Ona.

## Project Overview

A todo list application with multiple known security vulnerabilities in its dependencies.

## Common Commands

- `npm install` - Install dependencies
- `npm start` - Start the application (requires MongoDB)
- `npm run dev` - Start with nodemon for development
- `npm audit` - Check for known vulnerabilities

## Key Vulnerable Dependencies

This application intentionally uses outdated packages with known CVEs:

| Package | Current Version | CVEs |
|---------|----------------|------|
| lodash | 4.17.4 | CVE-2019-10744, CVE-2020-8203, CVE-2021-23337 (Prototype Pollution) |
| ejs | 1.0.0 | CVE-2022-29078 (Template Injection, CVSS 9.8) |
| express | 4.12.4 | Multiple security issues |
| mongoose | 4.2.4 | CVE-2022-2564 (Prototype Pollution) |
| marked | 0.3.5 | ReDoS vulnerabilities |
| adm-zip | 0.4.7 | CVE-2018-1002204 (Path Traversal) |
| moment | 2.15.1 | CVE-2022-31129 (ReDoS) |

## CVE Remediation Guidelines

When remediating CVEs in this application:

1. **Update one package at a time** to isolate breaking changes
2. **Run `npm install`** after each package.json change
3. **Check for breaking API changes** - major version updates often have them
4. **Test the application** by starting it with `npm start`
5. **Create clear commit messages** listing the CVE(s) fixed

## Architecture

- **app.js** - Main Express application entry point
- **mongoose-db.js** - MongoDB connection setup
- **routes/** - Express route handlers
- **views/** - EJS templates
- **public/** - Static assets

## Database

Requires MongoDB running on localhost:27017. In Ona environments, this is started automatically via the automations.yaml service.
