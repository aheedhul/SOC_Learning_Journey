# Certificate Analysis Discovery

## What I Found
College ERP portal uses HTTPS with certificate for erp.msec.edu.in
But server is accessible via raw IP: 115.98.66.146

## Why Certificate Shows "Not Secure" for IP
- Certificate is issued for domain "erp.msec.edu.in"
- Browser checks if certificate matches what you're visiting
- When visiting by IP (115.98.66.146), it doesn't match
- Result: Browser warns "Not Secure"

## Security Implication
- Domain access (HTTPS): Encrypted + Verified = SECURE ✅
- IP access (HTTP): No encryption + No verification = NOT SECURE ❌

## For Threat Hunting
- Monitor for certificate mismatches (indicates MITM)
- Track IP-based connections (malware often uses IPs)
- Certificate domain mismatch = RED FLAG
