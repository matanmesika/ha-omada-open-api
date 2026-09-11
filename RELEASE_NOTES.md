## What's Changed in v1.10.1

This is a bug-fix release for two regressions introduced in v1.10.0, plus a
security hardening fix.

### Fixed

- **Local controllers with self-signed certificates can connect again** (#52,
  #53, #54). v1.10.0 enforced TLS certificate verification on the OpenAPI
  session with no way to opt out, so self-hosted controllers and OC200/OC300
  gateways using the factory self-signed certificate failed to set up. OpenAPI
  sessions now honor a per-entry "Verify TLS certificate" setting:
  - Existing local entries created before this option default to unverified, so
    they reconnect after upgrading without any user action.
  - New local setups default to verification ON. If your controller uses the
    factory self-signed certificate, clear "Verify TLS certificate" during
    setup, or use Reconfigure to turn it off afterwards. See
    TROUBLESHOOTING.md.
  - Cloud controllers always verify TLS, regardless of the stored value, and
    the toggle is hidden for cloud reconfiguration.
  - Certificate failures now surface an actionable setup error instead of a
    generic connection error.
- **Button platform no longer fails at startup** (#58). When a gateway's WAN
  speed-test coordinator had no data yet at Home Assistant startup, setting up
  the button platform raised `AttributeError: 'NoneType' object has no attribute
  'get'` and no buttons were created. Setup now tolerates missing data and
  registers the port buttons automatically once the data becomes available.

### Security

- Refresh-token requests no longer place credentials in the URL query string.

### Thanks

- @oralallen82 for the detailed report and the overlapping fix that prompted
  the TLS verification option (PR #55).

---

**Upgrade note:** after updating, existing local controller entries reconnect
automatically. If a local setup still reports a certificate error, open the
integration's configure/reconfigure dialog and clear **Verify TLS certificate**,
or re-add the controller with the checkbox cleared.
