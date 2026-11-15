
# Security Advisory: Log4Shell (CVE-2021-44228)

**Vulnerability:** Apache Log4j2 Remote Code Execution  
**CVE ID:** CVE-2021-44228  
**Severity:** Critical  
**CVSS Score:** 10.0 (CVSS 3.1)  
**Affected Component:** Apache Log4j2 Core  
**Published Date:** December 10, 2021  
**Advisory Type:** Remote Code Execution (RCE)

---

## 1. Executive Summary

Log4Shell (CVE-2021-44228) is a critical remote code execution vulnerability affecting Apache Log4j2, a widely used Java logging library.

The vulnerability occurs because certain versions of Log4j2 process attacker-controlled input through Java Naming and Directory Interface (JNDI) lookups without adequate restrictions.

Under vulnerable conditions, an attacker could send specially crafted input that triggers a lookup to an attacker-controlled external service. This could result in remote code execution on the affected system.

Successful exploitation may allow attackers to compromise applications, access sensitive information, modify data, or disrupt services.

The vulnerability received a CVSS 3.1 score of 10.0, indicating Critical severity.

Organisations using affected versions of Apache Log4j2 should identify vulnerable components, apply appropriate security updates, and investigate potentially compromised systems.

## 2. Vulnerability Overview

### 2.1 What is Apache Log4j2?

Apache Log4j2 is an open-source logging framework maintained by the Apache Software Foundation.

Logging is the process of recording events that occur while an application is running. Applications use logging to record information such as:

- User activity
- Application errors
- System events
- Database connection failures
- Debugging information

Log4j2 helps Java applications generate, format, and manage these log messages.

### 2.2 What is Log4Shell?

Log4Shell is the name commonly given to the vulnerability identified as `CVE-2021-44228`.

The vulnerability exists in the JNDI lookup functionality of affected Log4j2 versions.

When an application processes attacker-controlled input through vulnerable logging functionality, specially crafted input may trigger an external JNDI lookup.

This can potentially allow an attacker to execute arbitrary code on the affected system.

## 3. Technical Details

### 3.1 Vulnerability Identification

| Property | Details |
|---|---|
| CVE ID | CVE-2021-44228 |
| Vulnerability Name | Log4Shell |
| Vendor | Apache Software Foundation |
| Product | Apache Log4j2 |
| Affected Component | `log4j-core` |
| Vulnerability Type | Remote Code Execution |
| CVSS Version | 3.1 |
| CVSS Score | 10.0 |
| Severity | Critical |

### 3.2 Understanding JNDI

Java Naming and Directory Interface (JNDI) is a Java API that allows applications to locate and access resources and services through naming and directory services.

It supports different service providers, including LDAP.

In the context of Log4Shell, vulnerable JNDI lookup processing could allow attacker-controlled input to trigger external lookups.

When these lookups are handled insecurely, they may result in the loading and execution of attacker-controlled code.

### 3.3 How the Vulnerability Works

The vulnerability can be understood through the following sequence:

1. An attacker sends specially crafted input to an application.
2. The application processes the input and records it using a vulnerable Log4j2 component.
3. Log4j2 interprets the input as a JNDI lookup expression.
4. The vulnerable lookup functionality may contact an attacker-controlled external service.
5. Under vulnerable conditions, malicious code may be loaded and executed on the affected system.

This can result in remote code execution without requiring the attacker to have an existing authenticated session.

**Important:** Successful exploitation depends on the application's configuration, exposure, and execution environment. Not every application using Log4j2 is automatically exploitable.

## 4. Affected Versions

According to the National Vulnerability Database (NVD), the vulnerability affects Apache Log4j2 versions from `2.0-beta9` through `2.15.0`, excluding the security releases `2.12.2`, `2.12.3`, and `2.3.1`.

The vulnerability specifically affects the `log4j-core` component.

| Component | Affected Versions |
|---|---|
| Apache Log4j2 Core | `2.0-beta9` through `2.15.0`, subject to the listed security-release exceptions |
| Log4j API | Not directly affected by this CVE |

The Log4j 1.x branch is not affected by this specific CVE, but it is end-of-life and has separate security concerns.

Organisations should check the actual Log4j dependencies included in their applications rather than relying only on the application's declared primary dependencies.

## 5. Potential Security Impact

Successful exploitation of Log4Shell may have serious consequences.

### 5.1 Remote Code Execution

An attacker may execute arbitrary code on the affected system, potentially gaining control over the application or server.

### 5.2 Confidentiality Impact

Attackers may access sensitive information, including:

- Application credentials
- User information
- Configuration files
- Internal system data

### 5.3 Integrity Impact

An attacker may modify application files, configurations, or stored information.

### 5.4 Availability Impact

Compromised systems may experience service disruptions, malicious processes, or other actions that affect availability.

### 5.5 Wider Network Compromise

If the affected system has access to other internal services, an attacker may attempt to use the compromised system as a starting point for further attacks.

## 6. Detection and Identification

Organisations should identify whether their applications contain vulnerable Log4j2 components.

Recommended identification steps include:

1. Review application dependencies and build files.
2. Check Java application packages for bundled Log4j2 libraries.
3. Inspect deployed application archives and containers.
4. Use software composition analysis (SCA) tools to identify vulnerable dependency versions.
5. Review security logs and network activity for suspicious JNDI-related lookups.

Common dependency files that may require inspection include:

- `pom.xml` for Maven projects.
- `build.gradle` for Gradle projects.
- Packaged JAR and WAR files containing application dependencies.

Detection should include both directly declared and transitive dependencies.

A transitive dependency is a library included indirectly through another software dependency.

## 7. Mitigation and Remediation

The primary remediation is to upgrade affected Log4j2 components to a supported, secure release.

### 7.1 Apply Security Updates

Organisations should:

- Identify all affected Log4j2 deployments.
- Upgrade to a currently supported and secure Log4j release.
- Follow the official Apache security guidance.
- Verify that vulnerable dependencies have been removed from deployed applications.

For historical reference, Log4j 2.16.0 removed message lookup functionality associated with this vulnerability. However, it should not be treated as a recommended current target version. Use a currently supported release and check Apache's security guidance for additional vulnerabilities.

### 7.2 Temporary Mitigation

If immediate patching is not possible, organisations should follow vendor-approved temporary mitigation guidance.

Historical mitigation measures included removing the vulnerable `JndiLookup` class from affected deployments.

Temporary mitigation should not replace upgrading to a secure version.

### 7.3 Verify Remediation

After applying updates, organisations should:

- Re-scan application dependencies.
- Confirm that affected Log4j2 components have been updated.
- Rebuild and redeploy affected applications where necessary.
- Review logs for evidence of previous exploitation.
- Monitor systems for suspicious activity.

### 7.4 Incident Response

If exploitation is suspected, organisations should:

1. Isolate affected systems where appropriate.
2. Preserve relevant logs and forensic evidence.
3. Investigate suspicious processes, connections, and account activity.
4. Rotate potentially exposed credentials.
5. Follow the organisation's incident response procedures.

## 8. Recommendations for Organisations

Organisations using Java-based applications should adopt the following security practices:

- Maintain an inventory of third-party software dependencies.
- Regularly scan dependencies for known vulnerabilities.
- Apply security patches promptly.
- Monitor application logs and network traffic.
- Follow secure coding and input-handling practices.
- Maintain an incident response plan.
- Review vendor security advisories regularly.

## 9. Conclusion

Log4Shell (CVE-2021-44228) demonstrated how a vulnerability in a widely used third-party software library can create significant security risks across many applications.

The vulnerability highlighted the importance of secure dependency management, timely patching, and continuous monitoring.

Organisations should identify affected Log4j2 components, apply appropriate security updates, verify remediation, and investigate signs of potential exploitation.

## 10. References

The following sources were used to prepare this advisory:

1. **National Vulnerability Database (NVD)** – CVE-2021-44228  
   [https://nvd.nist.gov/vuln/detail/CVE-2021-44228](https://nvd.nist.gov/vuln/detail/CVE-2021-44228)

2. **Apache Software Foundation** – Log4j Security Vulnerabilities  
   [https://logging.apache.org/log4j/2.x/security.html](https://logging.apache.org/log4j/2.x/security.html)

3. **Cybersecurity and Infrastructure Security Agency (CISA)** – Mitigating Log4Shell and Other Log4j-Related Vulnerabilities  
   [https://www.cisa.gov/news-events/cybersecurity-advisories/aa21-356a](https://www.cisa.gov/news-events/cybersecurity-advisories/aa21-356a)

4. **GitHub Advisory Database** – CVE-2021-44228  
   [https://github.com/advisories/GHSA-jfh8-c2jp-5v3q](https://github.com/advisories/GHSA-jfh8-c2jp-5v3q)

---

*Disclaimer: This document is an educational security advisory prepared for technical-writing portfolio purposes. It is not an official advisory issued by Apache, NIST, or CISA.*
