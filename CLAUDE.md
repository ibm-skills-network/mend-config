# mend-config

## Mend Renovate's schedule is coupled to Mend auto-remediation

`repo-config.json` `remediateSettings.schedule` (weekdays 00:00-07:59 and 18:00-23:59
Toronto, all weekend) is what the remediation tool in
`ibm-skills-network/security-compliance-automation` is scheduled around: its remediate job
runs at `0 13 * * 1-5` (09:00 Toronto, weekdays), after this window. Do not change this
schedule or `timezone` without checking that cron (see that repository's `CLAUDE.md`).

Why: Renovate and the remediation tool edit the same `package.json` and lockfiles in the
same repositories.

- Remediation running after Renovate writes its overrides against the base Renovate's
  upgrades already moved, so fewer are written and fewer later become obsolete.
- Overlapping runs push branches that conflict with each other, which leaves remediation
  drafts stale and blocks them from merging.

The override entries the remediation tool writes are caret ranges on purpose, so Renovate
can keep them current within the range. `:disableMajorUpdates` in `extends` is also relied
on elsewhere: the org's `dependency-auto-merge.yml` has no major-version gate for Renovate
because of it.
