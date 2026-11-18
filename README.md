<h1><img src="rules/elastic-logo-color.svg" width="32" height="32"/>  Elastic Community Rules</h1>

A detection rule library in git, with ten sample rules. It shows a second rule source for Elastic Security, next to
the prebuilt rules, delivered the same way as
[`detection-rules-library`](https://github.com/sdesalas/detection-rules-library): as a git repository instead of a
Fleet package.

The rules are sample content for demonstration. They are not tested for production use.

## Layout

```
rules/
  manifest.yml                       package manifest (name, version, kibana.version condition)
  changelog.yml                      one entry per release
  NOTICE.txt                         notice shipped with the package
  README.md                          the package description
  elastic-logo-color.svg             the package icon (the manifest's `icons`)
  windows/<rule file name>.yml       one file per rule
  linux/...
  macos/...
  cross-platform/...
  integrations/aws/...               rules for a data source
exceptions/
  <list name>.ndjson                 one exception list per file, with its items
value-lists/
  <list name>.yml                    one value list per file, with its items
LICENSE.txt                          Elastic License 2.0 (`source.license: Elastic-2.0`)
```

- Every folder under `rules/` holds rule files, and only those. The folder a rule is in says where it belongs.
- The package files stay directly in `rules/`.
- One file per rule. Its git history is its list of versions, so don't add a file per version.

## Rule files

Each file holds one rule as `{ id, type: "security-rule", attributes }`, in YAML. `id` is `<rule_id>_<version>`, and
`attributes.rule_id` and `attributes.version` are the source of truth for identity and version, not the file name.

The files follow the same conventions as the YAML files in `detection-rules-library`: keys in sorted order, multi-line
fields as literal blocks, one document per file, no anchors or aliases. The file name is the rule's name in snake case,
prefixed with its tactic.

| Rule | Folder | Tactic |
|---|---|---|
| PowerShell Encoded Command Execution | `windows` | Execution |
| Scheduled Task Created to Run as SYSTEM | `windows` | Persistence |
| Volume Shadow Copies Deleted via Command Line | `windows` | Impact |
| Manual Memory Dumping via Proc Filesystem | `linux` | Credential Access |
| Cron Job Created by an Unusual Process | `linux` | Persistence |
| Potential Reverse Shell via /dev/tcp | `linux` | Execution |
| Quarantine Attribute Removed via xattr | `macos` | Defense Evasion |
| Launch Agent Created by an Unusual Process | `macos` | Persistence |
| Remote Script Downloaded and Piped to a Shell | `cross-platform` | Command and Control |
| AWS CloudTrail Logging Stopped or Deleted | `integrations/aws` | Defense Evasion |

## Exception lists

`exceptions/` holds exception lists in the format Kibana exports and imports: one list per `.ndjson` file, with the
list on the first line and one item on each following line. Import a file from **Security > Exceptions > Import**, or
with `POST /api/exception_lists/_import`. The `list_id` and `item_id` are fixed, so importing a newer version of a file
with `overwrite` set updates the same list and items.

| List | List id | Items |
|---|---|---|
| Community: Internal IP Ranges | `community-internal-ip-ranges` | `source.ip` is any of `192.168.1.1` to `192.168.1.255` |

## Value lists

`value-lists/` holds value lists, one per YAML file. The attributes are the ones Kibana takes when a value list is
created (`POST /api/lists`), and `items` holds the values.

```yaml
id: compromised-ips.txt         # fixed list id, optional in Kibana
name: "Community: Compromised IPs" # required
description: ...                   # required
type: ip                           # required: ip, ip_range, keyword, text, ...
version: 1                         # optional, defaults to 1
meta:                              # optional, free-form
  source: Elastic Community Rules
items:                             # one value per entry, of the list's type
  - 35.88.9.149
```

To load a file, create the list from the attributes above `items`, then import the items as text, one value per line
(`POST /api/lists/items/_import?list_id=<id>`).

| List | List id | Type | Items |
|---|---|---|---|
| Community: Compromised IPs | `compromised-ips.txt` | `ip` | 581 |

## Releases

The `main` branch holds the latest release. Each release is one commit with a tag `main/v<package version>`, for
example `main/v0.0.1`, so a consumer can map a package version to a commit. A consumer can list tags to find
releases, compare the `rules/` trees of two commits to see what changed, and fetch single files without cloning.

## Using it with Kibana

Nothing in Kibana reads this repo by default. It is used to test delivering rules from a git provider, alongside the
prebuilt rules library.
