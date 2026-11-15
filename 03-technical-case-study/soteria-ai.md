# Soteria-AI: A Context-Aware Dependency Risk Analyzer

**Project type:** Personal software project\
**Role:** Independent developer\
**Status:** In development; locally runnable\
**Version documented:** 0.5.0 (not yet tagged as a release)\
**Repository:**
[github.com/marvelcodeX/project-dependency-risk-analyzer](https://github.com/marvelcodeX/project-dependency-risk-analyzer)

------------------------------------------------------------------------

## 1. Project Overview

Soteria-AI is a dependency risk analyzer designed to help developers
understand and prioritise security vulnerabilities in the third-party
packages used by their applications.

Modern applications often rely on large dependency trees. A
vulnerability scanner may identify a known issue in a package, but a
list of CVEs does not always explain how important that issue is to a
particular project. A vulnerable package may be several levels deep in
the dependency tree, may not be imported by the application, or may
already have a fix available. Developers still need to investigate each
alert and decide what to address first.

Soteria-AI adds project-specific context to dependency vulnerability
findings. It combines vulnerability severity, exploitation signals,
package reachability, fix availability, and dependency depth into a
composite risk score. It also provides optional plain-language
explanations using a local language model.

The application can be run locally through a web dashboard, a
command-line interface (CLI), or Docker. It is designed to support both
individual developers reviewing a project and automated dependency
checks in CI workflows.

## 2. The Problem

Traditional dependency scanners are useful for identifying packages with
known vulnerabilities. However, the resulting findings can be difficult
to prioritise because a vulnerability's severity score alone does not
describe its relevance to the application being scanned.

The project was built around three practical problems:

1.  **Alert prioritisation:** A long list of vulnerabilities does not
    necessarily show which issues deserve attention first.
2.  **Limited project context:** A finding is more useful when
    developers can understand whether the affected package is imported
    by their code, how it entered the dependency tree, and whether a
    fixed version is available.
3.  **Difficult-to-interpret advisories:** Security advisories often
    contain technical details that developers may need help translating
    into an actionable explanation. Sending source code to a cloud-based
    AI service may also be unsuitable for projects with privacy
    requirements.

Soteria-AI addresses these problems by combining multiple risk signals,
analysing source imports without executing the application, and offering
explanations through a locally hosted LLM.

## 3. Goals and Design Approach

The main goals were to:

-   Scan dependency manifests from multiple ecosystems.
-   Identify known vulnerabilities using public vulnerability data.
-   Distinguish direct and transitive dependencies where the available
    manifest or lockfile provides that information.
-   Analyse source imports to estimate package reachability for
    supported languages.
-   Prioritise findings using a transparent, weighted risk model.
-   Explain vulnerability findings in plain language without sending
    project source code to a cloud AI service.
-   Provide both interactive and automation-friendly ways to run scans.
-   Export findings in formats that can be consumed by other security
    and development tools.

The design favours local execution and makes the risk score explainable
through its individual components.

## 4. How Soteria-AI Works

A scan begins with a dependency manifest supplied as a file upload or
pasted text. The scanner identifies the manifest format and parses its
dependencies. Where supported, it also builds dependency relationships.

For unpinned Python and npm dependencies, Soteria-AI queries the
relevant package registry to resolve a version satisfying the declared
constraint. It then checks the resolved dependencies against
vulnerability information from the Open Source Vulnerabilities (OSV)
database.

The scanner also retrieves exploitation information from the CISA Known
Exploited Vulnerabilities (KEV) catalogue and checks for public exploit
references. If source files are supplied, it analyses imports to
estimate whether vulnerable packages are reachable from the project's
code.

Finally, the scanner calculates a risk score for each finding. The web
API stores scan results in the configured database, while the CLI can
produce reports and return an exit code suitable for CI pipelines.

### Scan pipeline

``` text
Dependency manifest
        |
        v
Manifest detection and parsing
        |
        v
Dependency and version resolution
        |
        v
Source import / reachability analysis
        |
        v
OSV vulnerability lookup
        |
        v
CISA KEV and public exploit enrichment
        |
        v
Composite risk scoring
        |
        +----------------------+
        |                      |
        v                      v
Web dashboard / history    CLI reports / CI checks
        |
        v
Optional local LLM explanation
```

The same core scan pipeline is shared by the API and CLI.


## 5. Dependency and Vulnerability Coverage

The current implementation supports four ecosystems:

| Ecosystem | Input Files | Dependency Information |
|---|---|---|
| Python | `requirements.txt` | Parses declared dependencies; the format is treated as a flat list. |
| JavaScript / npm | `package.json`, `package-lock.json` | Lockfile v2/v3 provides the dependency graph; v1 provides direct and transitive dependency information. |
| Go | `go.mod` | Distinguishes direct and `// indirect` dependencies. |
| Java / Maven | `pom.xml` | Reads declared dependencies; `dependencyManagement` entries are not treated as declared dependencies. |

### 5.1 Vulnerability Information

Vulnerability information is retrieved from OSV. The scanner collects available advisory details such as:

- CVSS severity
- CWE identifiers
- Fixed versions
- Reference links

Duplicate advisories that refer to the same issue are merged using their aliases.

### 5.2 Additional Vulnerability Enrichment

Soteria-AI also enriches findings with:

- Whether the vulnerability appears in the CISA KEV catalogue.
- Whether a public exploit or proof-of-concept reference is available.
- The dependency's position in the dependency tree.
- Whether a fixed version is available.

### 5.3 Dependency Version Resolution

For unpinned Python and npm dependencies, the application resolves the newest stable, non-yanked version that satisfies the declared constraint before checking for vulnerabilities.

Resolved versions are marked as `latest` in the scan context.


## 6. Reachability Analysis

A vulnerability in a dependency is not automatically proof that the
vulnerable code path is used by an application. To provide more context,
Soteria-AI performs static import analysis on supplied source files.

The analysis does not execute the project's source code.

-   **Python:** Uses the standard-library `ast` module to identify
    imports and map import names to package names where known. For
    example, the import name `yaml` maps to the `PyYAML` package.
-   **JavaScript and TypeScript:** Checks `import`, `export ... from`,
    `require()` and dynamic `import()` statements. Type-only imports and
    Node.js built-ins are ignored.
-   **Transitive dependencies:** Reachability can be propagated through
    the dependency graph when the required graph information is
    available.


Each package receives one of four reachability states:

| State | Meaning |
|---|---|
| `REACHABLE` | The project's source code imports the package directly. |
| `POTENTIALLY_REACHABLE` | The package is pulled in by a package the project imports, or the import name could not be confirmed. |
| `UNREACHABLE` | No imports from the analysed source lead to the package. |
| `UNKNOWN` | There is not enough source or dependency information to determine reachability. |


The scanner avoids classifying a transitive package as unreachable
unless the available dependency graph supports that conclusion.

This is package-level reachability analysis. It does not establish
whether a particular vulnerable function is called at runtime.

## 7. Composite Risk Scoring

Soteria-AI calculates a risk score from 0 to 100 for each vulnerability
finding. Instead of relying only on CVSS severity, the model combines
five signals.

| Component | Maximum Points | Scoring Rule |
|---|---:|---|
| Severity | 40 | CVSS base score multiplied by 4. |
| Exploitation | 25 | 25 points if listed in CISA KEV; otherwise, 20 points if a public exploit is identified. |
| Reachability | 20 | 20 points for reachable; 10 points for potentially reachable or unknown; 0 points for unreachable. |
| Fix Availability | 10 | 10 points when no fixed version is available. |
| Dependency Depth | 5 | 5 points for direct dependencies; 3 points for up to two hops; 1 point for deeper dependencies. |


The resulting score is mapped to a risk level:

| Risk Level | Score |
|---|---:|
| Critical | 70–100 |
| High | 40–69 |
| Medium | 20–39 |
| Low | Below 20 |


The project also receives an overall risk score.

The purpose of this model is to provide a consistent prioritisation aid.
It is not a formal guarantee that a vulnerability is exploitable or that
a low-scoring finding is harmless. Developers still need to review the
advisory and their application's actual use of the affected package.

## 8. Local AI Explanations

Soteria-AI includes an optional explanation feature powered by a local
language model through Ollama.

For an individual finding, the model generates an explanation covering:

1.  What the vulnerability is.
2.  How it could be exploited.
3.  The potential impact on the project.
4.  How the developer can address it.

The explanation prompt includes context such as the package, version,
CWE, reachability result, and exploitation status. Explanations are
cached in the database using advisory, package, version, context, and
prompt-version information.

The model is optional. The dependency scanner and its other features can
still run when Ollama is unavailable. Because the model runs locally,
project source code and manifests do not need to be sent to a
cloud-based AI service for these explanations.

## 9. Application Architecture

Soteria-AI uses a React frontend and a Python FastAPI backend.

``` text
Browser
   |
   | HTTP / JSON
   v
React + TypeScript frontend
   |
   | REST API (/api/...)
   v
FastAPI backend
   |
   +---- Dependency parsers and scan pipeline
   |
   +---- OSV API
   |
   +---- CISA KEV feed
   |
   +---- PyPI / npm registries
   |
   +---- Ollama (local LLM, optional)
   |
   +---- SQLAlchemy
             |
             v
        SQLite / PostgreSQL
```

### Backend

The backend is built with Python, FastAPI, Pydantic, and asynchronous
SQLAlchemy. It contains the API routes, manifest parsers,
vulnerability-data clients, reachability analysis, risk-scoring logic,
local LLM integration, database operations, and report generation.

The backend exposes endpoints for uploading or submitting manifests,
retrieving scan history, viewing individual scan results, exporting
reports, checking supported ecosystems, checking local AI availability,
and requesting AI explanations.

### Frontend

The dashboard is built with React, TypeScript, Vite, and Tailwind CSS.
It provides:

-   An overview of the project's risk score and key scan metrics.
-   Findings grouped by severity and reachability.
-   Search, filtering, and sorting for vulnerability findings.
-   Finding details, including CWE, exploitation status, dependency
    path, fix command, and AI explanation.
-   An expandable dependency tree that prioritises vulnerable paths.
-   Scan history and risk trends.
-   Light and dark themes, with responsive layouts.

### Data storage

SQLite is the default database. PostgreSQL is also supported through the
optional `asyncpg` driver. SQLAlchemy manages database access, and
Alembic migrations run automatically at application startup.

## 10. Reports and CI Integration

The project supports several report formats:

-   JSON
-   SARIF 2.1.0
-   CycloneDX 1.5 SBOM
-   Markdown
-   Printable HTML

SARIF output can be uploaded to GitHub code scanning. CycloneDX output
provides a software bill of materials with vulnerability information.
The SARIF and CycloneDX outputs are validated in tests against their
respective official schemas.

The CLI uses the same scan pipeline as the web application. It can scan
manifests in supplied paths, generate reports, and return an exit code
based on the configured risk threshold:

| Exit Code | Meaning |
|---|---|
| `0` | Scan passed the configured threshold. |
| `1` | Findings met or exceeded the configured threshold. |
| `2` | Scan error. |


A GitHub Action wraps the CLI so repositories can run dependency checks
during push and pull-request workflows. It can fail a workflow when the
selected risk threshold is reached and upload SARIF results to GitHub
code scanning.

The project also includes GitHub Actions workflows for CI, dependency
self-scanning, and release automation.

## 11. Testing and Local Execution

The project includes automated backend and frontend tests. As documented
in the project overview, the current test suite contains:

-   **202 backend tests**
-   **33 frontend tests**

The tests pass offline, with external network calls mocked. The CI
workflow also checks supported Python versions, frontend linting and
builds, and Docker image construction.

The application has been run locally in development mode, as a backend
serving the built frontend, and through Docker. Docker build, run,
restart, and data persistence have been tested on the development
machine.

## 12. Key Engineering Decisions

Several implementation choices shaped the project:

### Using OSV for vulnerability data

The project uses OSV for vulnerability matching and advisory
information. The original plan included the NVD API, but the
implementation uses CWE information supplied through OSV instead. This
avoids requiring an NVD API key and avoids depending on its rate limits
for this data.

### Combining multiple risk signals

CVSS severity is important, but it does not describe the full context of
a dependency in a particular project. The composite score adds
exploitation, reachability, fix availability, and dependency depth to
make the prioritisation more context-aware.

### Static analysis instead of executing project code

Reachability is estimated by parsing source imports. This avoids
executing the scanned application's code, which is important when
analysing an unfamiliar project. The trade-off is that import-level
analysis cannot prove whether a vulnerable function is actually invoked
at runtime.

### Running the explanation model locally

Ollama provides an option to generate explanations without sending
project source code to a cloud AI service. The trade-off is that users
need a compatible local model installed, and explanation availability
depends on the local Ollama service.

### Sharing one scan pipeline between the API and CLI

The API and CLI use the same scanner service. This keeps the core
scanning behaviour consistent across interactive use and CI automation.

## 13. Current Limitations

Soteria-AI is an in-development personal project, not a hosted
production service. Its current limitations include:

-   **No authentication:** The application is intended for local use or
    a trusted network, not direct exposure as a public service.
-   **Synchronous scans:** A scan runs within the HTTP request; there is
    no background job queue.
-   **Package-level reachability:** The scanner checks imports and
    dependency relationships, not calls to specific vulnerable
    functions.
-   **Limited ecosystem reachability:** Reachability analysis is
    currently available for Python and JavaScript/TypeScript, not Go or
    Java.
-   **No repository URL scanning:** Users provide manifests and,
    optionally, source files; scanning a Git repository URL is not
    implemented.
-   **PDF reporting is limited:** Reports can be printed from HTML and
    saved as PDF, but a dedicated PDF report is not implemented.
-   **Deployment work remains:** Docker Compose, a hosted demo, and a
    demo video are still pending.
-   **Release status:** Version 0.5.0 is present in the code but had not
    yet been tagged as a release in the project status document. The
    first GHCR release image is therefore still pending that release
    step.

These limitations define the current scope and help distinguish
implemented capabilities from planned work.

## 14. Work Remaining

The next development steps identified in the project overview are:

1.  Commit the current changes and tag version `v0.5.0`, triggering the
    release workflow.
2.  Complete Git repository URL scanning.
3.  Implement a dedicated PDF report.
4.  Add Docker Compose support.
5.  Decide on an authentication or read-only approach before hosting a
    public demo.
6.  Prepare a demo video.
7.  Explore function-level reachability and reachability support for Go
    and Java.
8.  Consider additional lockfile formats and streaming AI explanations.

## 15. Outcome

Soteria-AI has developed from a dependency-scanning concept into a
locally runnable application with multi-ecosystem manifest parsing,
vulnerability enrichment, import-level reachability analysis, composite
risk scoring, an interactive dashboard, local AI explanations, report
exports, a CLI, a GitHub Action, Docker support, and automated tests.

The central design goal is to make dependency vulnerability findings
more useful by adding project context rather than presenting severity
scores in isolation. The current implementation provides a foundation
for that workflow, while its documented limitations make clear where
further validation and development are needed.

## 16. Project Links

-   **Source code:** [Soteria-AI on
    GitHub](https://github.com/marvelcodeX/project-dependency-risk-analyzer)
-   **Portfolio:**
    [marvelcodex.github.io](https://marvelcodex.github.io/)

------------------------------------------------------------------------

*This case study describes the implementation and project status
documented on 15 November 2026. Features and release status may change
as development continues.*
