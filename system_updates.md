---
sort: 4
---

# System Updates

## Rolling Updates
*in short:* same instances number, gradual update.

Gradual update and monitoring of each instance. No downtime needed.


## Blue-Gree Deployments
*in short:* both version installed, traffic redirected.

Blue is the current version, Green the next one. Both version are installed but the traffic is only on the blue version. Once installed the green, smoke test!. If everything is ok, traffic is redirected on the green version. Then check monitoring. In case of problem fallback on the blue. If everything is ok, remove the blue.


## Canary Releases
*in short:* both version installed, traffic progressively redirected.

Both versions are installed and the traffic is progressively switched. Monitoring let to continue or rollback.






