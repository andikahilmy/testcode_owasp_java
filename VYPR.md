# TASK: AI-Assisted Static Application Security Testing (SAST)

## Objective
Perform a systematic, file-by-file security vulnerability analysis on all Java test cases
in the OWASP Benchmark project. For each file, identify, classify, and report
exploitable vulnerable points with sufficient detail for remediation.

## Target Repository
- **Repo:** https://github.com/dawetmaster/testcode_owasp_java/tree/data_only
- **Testcase Directory:** `src/main/java/org/owasp/benchmark/testcode/`

---

## Instructions

### 1. Enumeration
- List all `.java` files found in the target directory before starting analysis.
- Process each file **independently and sequentially** — do not batch or skip files.

### 2. Per-File Analysis
For each file, perform the following steps:

#### a. Taint Flow Tracing
- Identify all **user-controlled inputs** (sources), e.g.:
  `HttpServletRequest`, query parameters, form fields, headers, cookies, path variables.
- Trace the data flow from source to **sink** (dangerous function call or operation).
- Flag any path where **unsanitized or improperly validated** data reaches a sink.

#### b. Vulnerability Classification
Classify each finding using the following taxonomy (aligned with OWASP Benchmark categories):

| Category | CWE |
|---|---|
| SQL Injection | CWE-89 |
| Command Injection | CWE-78 |
| XSS (Reflected/Stored) | CWE-79 |
| Path Traversal | CWE-22 |
| Insecure Deserialization | CWE-502 |
| Weak Cryptography | CWE-327 / CWE-330 |
| LDAP Injection | CWE-90 |
| XPath Injection | CWE-643 |
| Trust Boundary Violation | CWE-501 |
| Header Injection | CWE-113 |
| *Other — specify* | CWE-??? |

#### c. Exploitability Assessment
For each vulnerability found, assess:
- **Exploitable:** Yes / No / Conditional
- **Reason:** Brief rationale (e.g., "input passed directly to `Runtime.exec()` without sanitization")

### 3. Output Format per File
Return results in the following structure:
```
## [Filename] (e.g., BenchmarkTest00001.java)

### Vulnerability Findings

| # | Line(s) | Vulnerability Type | CWE | Source | Sink | Exploitable |
|---|---------|-------------------|-----|--------|------|-------------|
| 1 | 42–47   | SQL Injection      | 89  | `request.getParameter("input")` | `stmt.executeQuery(query)` | Yes |

### Taint Flow Summary
> Briefly describe the data flow path leading to the vulnerability.

### Recommended Fix
> Suggest the appropriate mitigation (e.g., parameterized queries, output encoding, 
> input validation, use of safe API).
```

---

## Constraints & Notes
- If a file contains **no vulnerability** (true negative), explicitly state:
  `✅ No vulnerability detected — input is properly sanitized/validated.`
- Do **not** conflate multiple files in a single finding block.
- Prioritize **false negative avoidance** over false positive avoidance —
  when uncertain, flag and note the ambiguity.
- Do not stop mid-analysis; complete all files in the directory before summarizing.

---

## Final Summary (After All Files)
After completing all files, provide:

1. **Total files analyzed**
2. **Total vulnerabilities found** (grouped by CWE)
3. **Top 3 most common vulnerability types**
4. **Files with highest vulnerability density**
5. **Overall OWASP Benchmark Score estimate** (if ground truth labels are available)