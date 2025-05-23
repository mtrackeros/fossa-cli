# `fossa sbom`

The `fossa sbom` subcommand uploads SBOM files to FOSSA for analysis (`fossa sbom analyze`) and then tests to see if there were any issues raised from that analysis (`fossa sbom test`).

This feature is only available in a FOSSA Enterprise subscription. Contact [sales@fossa.com](mailto:sales@fossa.com) for more details.

FOSSA supports SPDX (JSON only) and CycloneDX (XML and JSON) SBOM files. For more information about the specific versions supported and required fields, see our [documentation on SBOM import via the web UI](https://docs.fossa.com/docs/sbom-import)

## `fossa sbom analyze <path to sbom file>`

`fossa sbom analyze <path to sbom file>` will upload the SBOM file to FOSSA and Analyze it. The name of the project will be the name of the SBOM file with extensions of ".xml" or ".json" removed. The revision will be derived from the current time. For example, if you run this command:

```
fossa sbom analyze /path/to/sampleCycloneDX.json
```

Then the project will be named "sampleCycloneDX" and the revision will be a timestamp based on the current time.

You can override the project name and revision using the `--project` and `--revision` flags, described below in [Common FOSSA Project Flags](#common-fossa-project-flags).

In addition to the [usual FOSSA project flags](#common-fossa-project-flags) supported by all commands, the `sbom analyze` command supports the following flags:


| Name                                  | Short | Description                                                                         |
| ------------------------------------- | ----- | ----------------------------------------------------------------------------------- |
| `--team 'team name'` | `-T` | Specify a team within your FOSSA organization. If you only have team-scoped permissions, you must specify a team of which you are a member. |
| `--force-rescan`  | | Force the SBOM file to be rescanned, even if this exact revision has been previously uploaded |

### Team Permissions

When using `fossa sbom analyze`, the command respects team-scoped permissions:
- If you have organization-wide permissions, you can upload SBOMs without specifying a team
- If you only have team-scoped permissions, you must use the `--team` flag to specify a team of which you are a member
- The behavior matches that of `fossa analyze --team`

## `fossa sbom test <path to sbom file>`

The `sbom test` command checks whether the most-recent scan of your FOSSA project raised license-policy or vulnerability issues. This command is usually run immediately after `fossa sbom analyze`.

While `fossa sbom analyze` uploads and processes an SBOM file, `fossa sbom test` verifies the analysis results against your organization's license and security policies. The key differences:

- `sbom analyze` - Uploads and scans an SBOM file, creating a build in FOSSA
- `sbom test` - Checks if that scan found any policy violations or security issues

The test command has the following behavior:
- If there are issues, it prints them to stderr and fails with an exit code of 1
- If there are no issues, it prints nothing and succeeds with an exit code of 0

These commands are typically used together in a workflow:
1. Run `fossa sbom analyze` to upload and analyze your SBOM
2. Run `fossa sbom test` to verify the analysis against your organization's policies

`fossa sbom test` supports the [Common FOSSA Project Flags](./analyze.md#common-fossa-project-flags).

`fossa sbom test` also supports the [flags supported by `fossa test`](./test.md).

## Common FOSSA Project Flags

`fossa sbom` commands support the following FOSSA-project-related flags:

| Name                               | Short | Description                                                                                                                                            |
| ---------------------------------- | ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `--project 'some project'`         | `-p`  | Override the detected project name                                                                                                                     |
| `--revision 'some revision'`       | `-r`  | -Override the detected project revision                                                                                                                |
| `--fossa-api-key 'my-api-key'`     |       | An alternative to using the `FOSSA_API_KEY` environment variable to specify a FOSSA API key                                                            |
| `--endpoint 'https://example.com'` | `-e`  | Override the FOSSA API server base URL                                                                                                                 |
| `--config /path/to/file`           | `-c`  | Path to a [configuration file](../files/fossa-yml.md) including filename. By default we look for `.fossa.yml` in base working directory. |
