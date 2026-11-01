# nsdrift

Nightly drift detection for DNS zones. nsdrift asks every authoritative
nameserver of your zones for the records you care about and compares the
answers with a known-good baseline. If something changed that you didn't
change, it tells your monitoring.

It's a single bash script meant for cron. It needs no provider API
credentials because it only queries the nameservers, the same way the rest
of the internet does. So it works the same for zones at deSEC, INWX, Hetzner
or anywhere else, and it can't change anything.

## What it checks

| Check          | Fails when                                                              |
|----------------|-------------------------------------------------------------------------|
| Record drift   | Any nameserver's answer for a watched name differs from the baseline   |
| SOA serial     | The nameservers of a zone disagree on the serial                        |
| DNSSEC         | Signatures are missing or expired, or the chain from the parent breaks  |
| Wildcards      | A random label under the zone resolves instead of returning NXDOMAIN   |
| Domain expiry  | The registration expires within `EXPIRY_WARN_DAYS`                      |
| Blocklists     | A mail IP is listed on any configured DNSBL                             |

## Why not ...

- **Zonemaster** runs a thorough one-off audit. It tells you whether a zone
  is healthy right now, not whether it changed since last week.
- **dnscontrol / octoDNS** manage your zones through provider APIs. Their
  drift check compares the live zone against the config you gave them. That's
  great if you want DNS as code, but heavy if you only want to know when
  something moved.

nsdrift fills the gap between them: a read-only watcher for zones you edit
by hand, spread across several providers.
