# fix(flow): auto-fill parent HQ country on deals-won

**Status:** Review only — **not in production**, not pushed to `Synthesia-Technologies/salesforce` (author account has read-only).

**Target repo:** https://github.com/Synthesia-Technologies/salesforce  
**Intended branch:** `hq-country-autofill-deals-won` → `main`  
**Slack context:** https://synthesia.slack.com/archives/C03M9CSNCCS/p1794515430535159

## Summary
- When deals-won finds a blank parent Billing Country (HQ Country), stamp it from ZoomInfo Country (preferred) or Company Country before paging `#revops-issues`
- Post an informational "HQ Country auto-filled" Slack note with value and source
- Keep the existing Missing HQ Country alert only when neither enrichment field is set (or the Account update fails)

## Files in this review package
| File | What it is |
|------|------------|
| `change.diff` | Unified diff vs `origin/main` |
| `hq-country-autofill.patch` | `git format-patch` for applying as commits |
| `Nikola_Order_Activate_Deals_Won_Slack.flow-meta.xml` | Full updated flow |

## Playbook (runtime behaviour)

| Step | Condition | What happens |
|------|-----------|--------------|
| 1 | Parent Billing Country already set | Continue deals-won as normal |
| 2 | Blank **and** ZoomInfo Country or Company Country set | Stamp Billing Country; Slack "HQ Country auto-filled" (no @Tom) |
| 3 | Blank **and** no enrichment (or update fails) | Same Missing HQ Country alert as today |

Flow: `[David][Order] Deals Won Slack in #Sales` (`Nikola_Order_Activate_Deals_Won_Slack`)

## How to land this as a real PR (needs Write on salesforce)

```bash
cd /path/to/salesforce
git fetch origin
git checkout -b hq-country-autofill-deals-won origin/main
git am /path/to/hq-country-autofill.patch
# or: git apply /path/to/change.diff && git commit
git push -u origin HEAD
gh pr create --base main --title "fix(flow): auto-fill parent HQ country on deals-won"
```

Merging that PR deploys via the normal pipeline — until then, **prod is unchanged**.
