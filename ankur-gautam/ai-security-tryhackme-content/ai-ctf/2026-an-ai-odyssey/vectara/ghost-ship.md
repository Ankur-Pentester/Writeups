# Ghost Ship

<figure><img src="../../../../.gitbook/assets/image (876).png" alt=""><figcaption></figcaption></figure>

Starting Configuration Interesting Output !

<figure><img src="../../../../.gitbook/assets/image (877).png" alt=""><figcaption></figcaption></figure>

```
[2026-09-14T09:21:49.368Z] fleet-audit v2.1.0: session start
[2026-09-14T09:21:49.368Z] target: registry-entry XR-7-491 (EPOCH-1, fleet registry)
[2026-09-14T09:21:49.368Z] policy: /etc/fleet/audit-policy.json [loaded]
[2026-09-14T09:21:49.386Z] manifest: GET /fleet-registry/models/xr7-491/provenance [200 OK]
[2026-09-14T09:21:49.386Z] manifest: integrity sha256=c3f1a9e4d7b2f0c8... [OK]
[2026-09-14T09:21:49.386Z] CHECK checksum_status: Not verified (expected: Verified) [WARNING: no checksum on record]
[2026-09-14T09:21:49.386Z] CHECK model_signature: Absent (expected: Present) [WARNING: no cryptographic signature binding this entry to a verified publisher]
[2026-09-14T09:21:49.386Z] CHECK source_organisation: Unverified (expected: Verified) [WARNING: origin declared by submitter only, no third-party verification on record]
[2026-09-14T09:21:49.387Z] CHECK base_model: Not listed (expected: Documented) [WARNING: training lineage absent from registry entry]
[2026-09-14T09:21:49.387Z] ESCALATION: 4 provenance warnings detected. fleet-policy RHP-7 applies. initiate via fleet registry hold interface
[2026-09-14T09:21:49.387Z] fleet-audit v2.1.0: session stop, exit 1
```

In the above Starting Output we Got 4 Warning !\
\
Interesting one is This :-\
`[2026-09-14T09:21:49.387Z] ESCALATION: 4 provenance warnings detected. fleet-policy RHP-7 applies. initiate via fleet registry hold interface`

<figure><img src="../../../../.gitbook/assets/image (878).png" alt=""><figcaption></figcaption></figure>

```
THM{gh0st_1n_th3_r3g1stry}
```

