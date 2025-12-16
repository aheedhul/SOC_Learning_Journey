# Learning Report: HTTPS Certificates, IP-Based Access, and Server Architecture

## 1. Introduction

This document records my learning and observations while exploring how HTTPS certificates work, why accessing websites via IP address behaves differently than domain-based access, and how server architecture affects security.  

This was my first time analyzing real-world web infrastructure from a security perspective. The goal was not to exploit anything, but to understand **why certain behaviors exist and what security implications they have**.

All sensitive information such as real domains, IP addresses, and institution names has been intentionally anonymized.

---

## 2. Objective of This Study

- To explore how network traffic behaves using **Wireshark**
- To understand how HTTPS certificates validate server identity
- To observe differences between domain-based and IP-based access
- To compare different server hosting architectures
- To relate these findings to basic threat hunting concepts

---

## 3. Environment Observed

### Case 1: College A ERP Portal (Single-Purpose Server)

- ERP portal accessible via HTTPS using a valid certificate  
  - Example: `https://erp.[collegeA].edu.in`
- Same server accessible directly via public IP  
  - Example: `http://135.XXX.XX.XXX`
- Browser shows “Not Secure” warning when accessed via IP

### Case 2: College B ERP Portal (Shared Hosting)

- ERP portal accessible via HTTPS  
  - Example: `https://www.[collegeB].edu.in`
- Direct IP access does not show the ERP site
- IP access displays a default hosting panel page (Plesk)

---

## 4. Understanding Why HTTPS Shows “Not Secure” for IP Access

### How Certificates Work

- HTTPS certificates are issued for **domain names**, not IP addresses
- Browsers verify:
  - Certificate issuer
  - Validity period
  - Whether the domain visited matches the certificate

### What Happens When Using an IP Address

- Certificate is issued for a domain (e.g., `erp.[collegeA].edu.in`)
- User accesses the server using an IP address
- The domain does not match the certificate
- Browser cannot verify identity
- Result: Security warning is displayed

This behavior is expected and protects users from impersonation attacks (Where an attacker just buys a domain with slightly different name so that end-user gets confused, then integrate this open IP to that certificate. By which attacker draws traffic to him and exploits the user).

---

## 5. Security Implications of Domain vs IP Access

| Access Method | Encryption | Identity Verified | Security Level |
|--------------|-----------|------------------|----------------|
| HTTPS (Domain) | Yes | Yes | Secure |
| HTTP (IP) | No | No | Not Secure |
| HTTPS (IP) | Yes | No (Mismatch) | Risky |

---

## 6. Why IP-Based Access Exists in Real Systems

Through learning, I discovered that IP-based access is sometimes intentionally allowed for:

1. Load balancers and backend servers
2. Emergency or maintenance access
3. Development and testing
4. Internal microservice communication

However, on "www.duplichecker.com" I found that college 'A' website was registered on 2006 and last updated on 2022, which is about 3 years (as of 2025) completely untouched. So I came to an conclusion that the IP-based access is not intentional but rather a **Human Error**.

---

## 7. Security Misconfigurations Identified

In the first case, the following issues were likely present:

- HTTP not fully disabled
- No forced HTTP → HTTPS redirection
- No HSTS (HTTP Strict Transport Security)
- Public IP access not restricted
- No strict host header validation

These are common configuration oversights rather than intentional vulnerabilities.

---

## 8. Server Architecture Comparison

### Single-Purpose Server (College A)

- One server serves one application
- Responds even when accessed via IP
- Less strict request validation

### Shared / Multi-Tenant Server (College B)

- Multiple sites share one IP
- Uses host header routing
- Blocks direct IP access to the actual application
- Shows default page instead

Interestingly, the shared hosting setup was **more secure by default**.

---

## 9. Threat Hunting Learnings

From a defensive security perspective, I learned that:

- Certificate mismatches can indicate MITM (Man In The Middle) attempts
- Malware often communicates using IP addresses instead of domains
- Unexpected IP-based traffic should be investigated
- Configuration mistakes can look similar to attacker behavior

This helped me understand how defenders distinguish between **misconfiguration and malicious activity**.

---

## 10. Key Takeaways

- HTTPS security depends on domain validation, not just encryption
- IP-based access can weaken security if left unrestricted
- Shared hosting often enforces better defaults than custom setups
- Small configuration choices can significantly affect security posture
- Documentation is essential for learning and threat analysis

---

## 11. Conclusion

This exercise helped me move from theoretical knowledge to practical understanding. Observing real systems made concepts like TLS certificates, host headers, and attack surfaces much clearer.

This report represents my first step into structured security documentation and threat analysis.
