### INV-001 — Kibana–Elasticsearch TLS Trust Failure After IP Re-Addressing

**Date worked:** 2026-08-01
**Source:** Home SOC Lab (self-managed Elastic Stack, Ubuntu Server)
**Category:** Infrastructure / PKI Misconfiguration
**Severity assigned:** Medium — service outage, no data exposure, self-inflicted by planned config change

---

**1. Alert Summary**

As part of re-addressing my lab's IP scheme (moving to a dedicated `10.10.10.0/24` internal network behind pfSense), I updated the IP bindings in both `elasticsearch.yml` and `kibana.yml`. Immediately after, Kibana failed to come up — it accepted browser connections but returned no usable response, and Elasticsearch itself began failing to start entirely partway through troubleshooting.

**2. Initial Triage**

Checked both services independently rather than assuming the problem was in one place:
```
sudo systemctl status kibana
sudo systemctl status elasticsearch
```
Confirmed Kibana's process was technically running (ruling out a simple crash), which narrowed the first problem to network/service-layer rather than a config syntax error. Elasticsearch was healthy at this point — the real fault hadn't surfaced yet.

**3. Investigation Timeline**

| Time | Action | Result |
|---|---|---|
| 17:20 | Generated first replacement HTTP cert zip | Output file only 44 bytes — corrupted/incomplete |
| 17:37 | Confirmed no hung `certutil` process; retried cert generation | New zip generated at expected size |
| 17:53 | Installed new `http.p12`, restarted Elasticsearch | Service failed to start — `keystore password was incorrect` |
| 18:00 | Updated ES secure keystore password entries to match new cert | Elasticsearch started successfully and stayed up |
| 18:34 | Restarted Kibana against healthy Elasticsearch | New error — `self-signed certificate in certificate chain` |
| 18:52 | Extracted CA public cert, pointed Kibana at it via `certificateAuthorities` | Kibana reported active, no further TLS errors |

**4. Investigation**

This turned into a layered investigation, each fix exposing the next problem underneath it:

- **Layer 1 — Reachability:** `curl http://127.0.0.1` on the Kibana host returned a full HTML response, proving the web service itself was healthy. This eliminated "Kibana is broken" and pointed at something between Kibana and Elasticsearch instead.
- **Layer 2 — TLS hostname mismatch:** Kibana's logs showed the real error: `Hostname/IP does not match certificate's altnames` — Elasticsearch's existing TLS cert had been issued for the *old* IP addresses. The new `10.10.10.102` wasn't in the certificate's Subject Alternative Names, so TLS validation correctly rejected the connection.
- **Layer 3 — Cert regeneration, wrong cert type:** Attempted to regenerate the cert. First pass unintentionally generated the **transport-layer** cert (`elastic-certificates.p12`, used for node-to-node traffic) instead of the **HTTP-layer** cert Kibana actually needed — a mistake caught by noticing the command produced no hostname/IP prompt, which the correct `elasticsearch-certutil http` tool does.
- **Layer 4 — Corrupted output file:** An early cert generation attempt produced a 44-byte zip file — far too small to be valid. Traced to an interrupted process (Ctrl+C sent mid-generation). Verified file integrity going forward with `ls -la` before trusting any generated artifact, and independently validated the `.p12` with `openssl pkcs12 -legacy -info -noout` *before* installing it into Elasticsearch — catching problems before they caused a service failure, not after.
- **Layer 5 — Keystore password mismatch:** After installing the new HTTP cert, Elasticsearch failed to start with `keystore password was incorrect`. Root cause: Elasticsearch's internal secure keystore still referenced the *old* cert's password, which no longer matched the newly generated file. Fixed by updating `xpack.security.http.ssl.keystore.secure_password` (and the matching truststore entry) via `elasticsearch-keystore add`.
- **Layer 6 — CA trust gap:** With Elasticsearch finally healthy, Kibana still failed: `self-signed certificate in certificate chain`. Elasticsearch trusted its own cert, but Kibana had no reason to — it needed the CA's public certificate explicitly. Extracted it with `openssl pkcs12 -legacy -clcerts -nokeys` and pointed Kibana at it via `elasticsearch.ssl.certificateAuthorities` in `kibana.yml`.

**5. IOCs (Indicators of Compromise)**

Not applicable — this was a self-inflicted infrastructure misconfiguration, not an adversarial event. No external IPs, hashes, or malicious indicators involved. Included here for completeness/consistency with the template rather than force-fitting unrelated data.

**6. MITRE ATT&CK Mapping**

Not applicable — no adversarial technique was involved. This entry is retained in the log to demonstrate infrastructure and PKI troubleshooting depth, not attacker behavior analysis.

**7. Evidence**

Key log lines that drove each diagnostic step:
```
Hostname/IP does not match certificate's altnames
Caused by: java.io.IOException: keystore password was incorrect
"reason":"missing authentication credentials for REST request [/]"
self-signed certificate in certificate chain
```
File integrity check that caught the corrupted cert before install:
```
-rw-r--r-- 1 root root 44 Aug 1 17:20 elasticsearch-ssl-http.zip   ← too small, invalid
```

**8. Verdict**

**True Positive.** This was a genuine, self-caused outage: changing an IP address invalidated a certificate that was cryptographically bound to the old address, which is TLS working as intended — not a bug, not a false alarm. The extended troubleshooting was due to compounding secondary issues (wrong cert type, corrupted file, stale keystore password) layered on top of the original root cause.

**9. Response / Containment**

Since this was a self-managed lab service (not a live production system), containment meant restoring service safely rather than isolating a host:
1. Verified each new certificate's integrity independently before installing it (`openssl pkcs12 -legacy`)
2. Regenerated the correct HTTP-layer certificate with the current IP in its SAN list
3. Synced the keystore-stored password to match the new cert
4. Extracted and distributed the CA's public certificate to the dependent service (Kibana) so trust was established explicitly rather than assumed
5. Verified end-to-end with `curl -k -u elastic https://<ip>:9200` before touching Kibana again, confirming each layer before moving to the next

**10. Lessons Learned**

- **TLS certificates are bound to IPs/hostnames by design** (SAN field) — any planned re-IP of infrastructure needs certificate regeneration built into the change plan, not discovered after the fact.
- **PKCS12 keystores carry their own password, separate from the OS or service account** — Elasticsearch stores that password in its own secure keystore, and the two can silently drift out of sync if a cert is replaced without updating the stored password.
- **Trust isn't transitive between services by default** — Elasticsearch trusting its own certificate doesn't mean Kibana does; each service that talks TLS needs the CA explicitly configured.
- **Verify artifacts before installing them, not after they fail.** Checking file size and running an independent `openssl` validation on a newly generated cert — before restarting a production-adjacent service on it — would have caught the corrupted-zip issue immediately instead of several troubleshooting cycles later.
- **Layered failures need layered diagnosis.** Each fix in this incident revealed the next problem underneath. Resisting the urge to guess and instead confirming each layer (reachability → cert validity → keystore auth → CA trust) in order was what actually closed this out, rather than any single fix.

---

[← Back to index](../README.md)
