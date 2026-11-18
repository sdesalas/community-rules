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

## Releases

The `main` branch holds the latest release. Each release is one commit with a tag `main/v<package version>`, for
example `main/v0.0.1`, so a consumer can map a package version to a commit. A consumer can list tags to find
releases, compare the `rules/` trees of two commits to see what changed, and fetch single files without cloning.

## Using it with Kibana

Nothing in Kibana reads this repo by default. It is used to test delivering rules from a git provider, alongside the
prebuilt rules library.
