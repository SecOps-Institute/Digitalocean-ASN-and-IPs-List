# DigitalOcean ASN and IP Lists

The networks operated by DigitalOcean, a cloud hosting provider.

## Files

| File | Contents | Raw URL |
|---|---|---|
| `digitalocean_asn_list.lst` | DigitalOcean's ASNs (`AS12345` format) | https://raw.githubusercontent.com/SecOps-Institute/Digitalocean-ASN-and-IPs-List/master/digitalocean_asn_list.lst |
| `digitalocean_ip_cidr_blocks.lst` | **IPv4 ranges merged into the fewest non-overlapping CIDR blocks. Use this one for firewalls and allowlists.** | https://raw.githubusercontent.com/SecOps-Institute/Digitalocean-ASN-and-IPs-List/master/digitalocean_ip_cidr_blocks.lst |
| `digitalocean_ip_list.lst` | IPv4 routes as registered (these can overlap) | https://raw.githubusercontent.com/SecOps-Institute/Digitalocean-ASN-and-IPs-List/master/digitalocean_ip_list.lst |
| `digitalocean_ipv6_list.lst` | IPv6 routes as registered | https://raw.githubusercontent.com/SecOps-Institute/Digitalocean-ASN-and-IPs-List/master/digitalocean_ipv6_list.lst |
| `Last-Checked-At` | Time of the last successful check (IST) | |

Each file has one entry per line. Entries are normalized, deduplicated and sorted numerically,
with no comments.

## How it is built

DigitalOcean doesn't publish an official IP list, so this one is derived from public internet registry
data:

1. **ASNs:** search [RIPEstat](https://stat.ripe.net/) for Autonomous Systems whose registered
   name contains "DigitalOcean". An ASN is kept only if the registry description really names the company.
2. **Routes:** for each ASN, get its `route` / `route6` objects from the
   [RADb](https://www.radb.net/) routing registry.
3. **Remove stale routes:** keep a route only if it falls inside a prefix that the company's ASNs
   have announced in BGP in the last ~14 days (RIPEstat announced-prefixes). Registries keep many
   old records, and this drops them.
4. **Merge:** combine the IPv4 routes into the CIDR blocks file.

## Update schedule

**mirabot**, an automated bot, checks **every hour**:
- `Last-Checked-At` is updated on every successful check.
- The list files change only when the company's registered or announced networks change, so this
  repo's commit history doubles as a change log.

Before publishing, the bot checks each update for problems: minimum size, malformed data, and
any sudden drop of more than half the entries. If a check fails, the previous list stays in
place. If `Last-Checked-At` is more than a few hours old, an update is being held back or the
bot is down.

## Notes

- **Non-exhaustive.** Networks the company never registers in RADb, or announces from ASNs not
  named after it, won't appear.
- Almost all of these addresses are **customer Droplets**. The usual use is to flag or rate-limit traffic coming from cloud hosting, not to trust it.
- DigitalOcean also publishes an official geofeed, https://digitalocean.com/geo/google.csv. All of its IPv4 ranges fall inside this list.
- Older versions of this repo (before November 2026) wrongly included six ASNs belonging to other companies whose names contain "digital". Those were removed, along with their address ranges.

## License

MIT for this repository. Derived from public RIPEstat and RADb data.

---
Updated hourly by **mirabot**, sponsored by [endlesstokens.ai](https://endlesstokens.ai) — *Pay for Time not Tokens*.

Maintained by [SecOps Institute](https://github.com/SecOps-Institute) ·
[LinkedIn](https://www.linkedin.com/in/aarvee11) · [Blog](https://amisafe.secops.in)
