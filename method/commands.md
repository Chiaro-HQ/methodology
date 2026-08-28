# The evidence-pull commands

The 72 commands we ask a client's AI to run to pull configuration
evidence, across 22 systems. Generated from the live library, so this
is what an engagement actually asks for.

Each command names the Trust Services Criteria it helps satisfy. The output
is captured verbatim, with the command itself as the first line of the file:
see `collection-rules.md` for the input and output contract every evidence
item has to meet.

**Read-only, and least privilege.** Every command here reads configuration
and changes nothing. No credential appears in this library as a value: a
command reads its token from the environment (`$SENTRY_AUTH_TOKEN`), or marks
where one goes with a placeholder. Where a `{placeholder}` names a credential,
put an environment variable in its place rather than the literal value: a
token typed into a command lands in the shell history and outlives the audit
that needed it. The other `{braces}` are identifiers to fill in, an org slug,
a project reference, a service id.

This list is a floor, not a ceiling. A stack we have not met yet gets the
same treatment: the criterion decides what has to be shown, and the command
is whatever shows it.

System headings are the library's own keys, so they read as identifiers
rather than product names: a `command_ref` in
`framework/test_attributes.json` that names one of these systems points at
its heading here.

## anthropic

### access management

| command | what it shows | criteria |
|---|---|---|
| `curl -s -H "x-api-key: $ANTHROPIC_ADMIN_KEY" -H 'anthropic-version: 2023-06-01' 'https://api.anthropic.com/v1/organizations/users?limit=100'` | Organization members and their roles | CC6.1, CC6.2, CC6.3 |
| `curl -s -H "x-api-key: $ANTHROPIC_ADMIN_KEY" -H 'anthropic-version: 2023-06-01' 'https://api.anthropic.com/v1/organizations/api_keys?limit=100'` | API keys with status, creator and workspace. Returns metadata only — the key values are not retrievable, which is the point | CC6.1, CC6.2 |
| `curl -s -H "x-api-key: $ANTHROPIC_ADMIN_KEY" -H 'anthropic-version: 2023-06-01' 'https://api.anthropic.com/v1/organizations/workspaces?limit=100'` | Workspaces — the boundary between prod and non-prod model access | CC6.1, CC6.3 |

### vendor management

| command | what it shows | criteria |
|---|---|---|
| `curl -s -H "x-api-key: $ANTHROPIC_ADMIN_KEY" -H 'anthropic-version: 2023-06-01' 'https://api.anthropic.com/v1/organizations/workspaces/{workspace_id}/members'` | Who can reach the model provider from this workspace. Pair with the org's own statement of what customer data is sent | CC9.2 |

## auth0

### access management

| command | what it shows | criteria |
|---|---|---|
| `curl -s -H 'Authorization: Bearer {mgmt_token}' 'https://{domain}.auth0.com/api/v2/guardian/factors'` | Auth0 MFA factor configuration | CC6.1 |
| `curl -s -H 'Authorization: Bearer {mgmt_token}' 'https://{domain}.auth0.com/api/v2/connections'` | Auth0 connection and SSO configuration | CC6.1 |

## aws

### access management

| command | what it shows | criteria |
|---|---|---|
| `aws iam get-credential-report --output text --query Content \| base64 -d` | IAM credential report (MFA, key age, last login) | CC6.1, CC6.2 |
| `aws iam list-users --query 'Users[*].[UserName,CreateDate,PasswordLastUsed]' --output table` | List IAM users with last login | CC6.1 |
| `aws iam get-account-summary --output json` | Account-level MFA and access key summary | CC6.1 |

### backup recovery

| command | what it shows | criteria |
|---|---|---|
| `aws rds describe-db-instances --query 'DBInstances[*].[DBInstanceIdentifier,BackupRetentionPeriod,MultiAZ]' --output table` | RDS backup and high availability config | CC9.1 |
| `aws backup list-backup-plans --output json` | AWS Backup plans | CC9.1 |

### encryption

| command | what it shows | criteria |
|---|---|---|
| `aws kms list-keys --region {region} --output json` | List KMS encryption keys | CC6.7 |
| `aws rds describe-db-instances --query 'DBInstances[*].[DBInstanceIdentifier,StorageEncrypted,KmsKeyId]' --output table` | Check RDS encryption status | CC6.7 |
| `aws s3api list-buckets --query 'Buckets[*].Name' --output text \| while read b; do echo "$b: $(aws s3api get-bucket-encryption --bucket $b 2>&1)"; done` | Check S3 bucket encryption | CC6.7 |
| `aws ec2 describe-volumes --query 'Volumes[*].[VolumeId,Encrypted,KmsKeyId,State]' --output table` | Check EBS volume encryption | CC6.7 |

### logging monitoring

| command | what it shows | criteria |
|---|---|---|
| `aws cloudtrail describe-trails --output json` | CloudTrail configuration (audit logging) | CC7.1, CC7.2 |
| `aws cloudtrail get-trail-status --name {trail_name}` | CloudTrail logging status (active/stopped) | CC7.1 |
| `aws guardduty list-detectors --output json` | GuardDuty threat detection status | CC7.1, CC7.2 |

### network security

| command | what it shows | criteria |
|---|---|---|
| `aws ec2 describe-security-groups --query 'SecurityGroups[*].[GroupId,GroupName,IpPermissions]' --output json` | Security group rules (firewall) | CC6.6 |
| `aws ec2 describe-vpcs --output table` | VPC configuration | CC6.6 |
| `aws elbv2 describe-load-balancers --query 'LoadBalancers[*].[LoadBalancerName,Scheme,Type]' --output table` | Load balancer configuration | CC6.6 |

## bitwarden

### access management

| command | what it shows | criteria |
|---|---|---|
| `curl -s -H "Authorization: Bearer $BW_ORG_TOKEN" https://api.bitwarden.com/public/members` | Organization members, their type and 2FA status. Membership and policy only — never vault contents | CC6.1, CC6.2, CC6.3 |
| `curl -s -H "Authorization: Bearer $BW_ORG_TOKEN" https://api.bitwarden.com/public/policies` | Enterprise policies: two-step login required, master-password strength, single-org enforcement | CC6.1, CC6.2 |
| `bw status` | CLI alternative when there is no organization API key: confirms the vault server and the logged-in account, nothing else | CC6.1 |

## cloudflare

### network security

| command | what it shows | criteria |
|---|---|---|
| `curl -s -H 'Authorization: Bearer {api_token}' 'https://api.cloudflare.com/client/v4/zones/{zone_id}/settings' \| python3 -c "import sys,json; settings=json.load(sys.stdin)['result']; [print(f'{s[\"id\"]}: {s[\"value\"]}') for s in settings if s['id'] in ['ssl','min_tls_version','always_use_https','waf']]"` | Cloudflare security settings (TLS, WAF, HTTPS) | CC6.6, CC6.7 |

## crowdstrike

### data protection

| command | what it shows | criteria |
|---|---|---|
| `curl -s -X POST 'https://api.crowdstrike.com/oauth2/token' -d 'client_id={client_id}&client_secret={client_secret}' \| python3 -c "import sys,json; t=json.load(sys.stdin)['access_token']; import urllib.request as r; req=r.Request('https://api.crowdstrike.com/devices/queries/devices/v1?limit=5', headers={'Authorization': f'Bearer {t}'}); print(r.urlopen(req).read().decode())"` | CrowdStrike managed endpoint count | CC6.8 |

## datadog

### logging monitoring

| command | what it shows | criteria |
|---|---|---|
| `curl -s -H 'DD-API-KEY: {api_key}' -H 'DD-APPLICATION-KEY: {app_key}' 'https://api.datadoghq.com/api/v1/monitor' \| python3 -c "import sys,json; monitors=json.load(sys.stdin); print(f'Total monitors: {len(monitors)}'); [print(f'{m[\"name\"]}: {m[\"overall_state\"]}') for m in monitors[:15]]"` | Datadog monitors and alert status | CC7.1, CC7.2 |

## github

### access management

| command | what it shows | criteria |
|---|---|---|
| `gh api orgs/{org}/members --jq '.[].login'` | GitHub org members | CC6.1 |
| `gh api repos/{owner}/{repo}/collaborators --jq '.[] \| {login, role_name}'` | Repo collaborators and permission levels | CC6.1, CC6.3 |

### change management

| command | what it shows | criteria |
|---|---|---|
| `gh api repos/{owner}/{repo}/branches/main/protection` | Branch protection rules (PR required, approvals, force push) | CC8.1 |
| `gh api repos/{owner}/{repo}/actions/workflows --jq '.workflows[] \| {name, state, path}'` | CI/CD workflow definitions | CC8.1 |
| `gh pr list --state merged --limit 10 --json number,title,mergedAt,reviewDecision,author` | Recent merged PRs with review status | CC8.1 |

## gitlab

### access management

| command | what it shows | criteria |
|---|---|---|
| `curl -s -H 'PRIVATE-TOKEN: {token}' 'https://gitlab.com/api/v4/projects/{project_id}/members/all'` | GitLab project members and access levels | CC6.1, CC6.3 |

### change management

| command | what it shows | criteria |
|---|---|---|
| `curl -s -H 'PRIVATE-TOKEN: {token}' 'https://gitlab.com/api/v4/projects/{project_id}/protected_branches'` | GitLab protected branch rules | CC8.1 |

## google-workspace

### access management

| command | what it shows | criteria |
|---|---|---|
| `curl -s -H "Authorization: Bearer $(gcloud auth print-access-token)" 'https://admin.googleapis.com/admin/directory/v1/users?customer=my_customer&maxResults=200&projection=full&fields=users(primaryEmail,suspended,isEnrolledIn2Sv,isEnforcedIn2Sv,isAdmin,lastLoginTime,creationTime)'` | Every user with 2-step verification enrolment and enforcement, admin flag and last login. This is the single highest-value pull for a Google-Workspace shop | CC6.1, CC6.2, CC6.3 |
| `gam print users fields primaryemail,suspended,isenrolledin2sv,isenforcedin2sv,isadmin,lastlogintime > workspace-users.csv` | GAM equivalent of the above, written straight to a CSV export | CC6.1, CC6.2 |
| `curl -s -H "Authorization: Bearer $(gcloud auth print-access-token)" 'https://admin.googleapis.com/admin/directory/v1/customer/my_customer/roleassignments'` | Admin role assignments — who holds privileged access | CC6.1, CC6.3 |

### logging monitoring

| command | what it shows | criteria |
|---|---|---|
| `curl -s -H "Authorization: Bearer $(gcloud auth print-access-token)" 'https://admin.googleapis.com/admin/reports/v1/activity/users/all/applications/login?maxResults=200'` | Login audit events (success and failure) | CC7.1, CC7.2 |
| `curl -s -H "Authorization: Bearer $(gcloud auth print-access-token)" 'https://admin.googleapis.com/admin/reports/v1/activity/users/all/applications/token?maxResults=200'` | Third-party OAuth grants — which external apps hold access to company data, and who granted them | CC6.1, CC9.2 |

## jira

### change management

| command | what it shows | criteria |
|---|---|---|
| `curl -s -u {email}:{api_token} 'https://{domain}.atlassian.net/rest/api/3/project' \| python3 -c "import sys,json; [print(p['key'], p['name']) for p in json.load(sys.stdin)]"` | List Jira projects for change tracking | CC8.1 |
| `curl -s -u {email}:{api_token} 'https://{domain}.atlassian.net/rest/api/3/search?jql=project={project_key}+ORDER+BY+created+DESC&maxResults=10' \| python3 -c "import sys,json; issues=json.load(sys.stdin)['issues']; [print(i['key'], i['fields']['summary'][:60], i['fields']['status']['name']) for i in issues]"` | Recent Jira tickets (change tracking evidence) | CC8.1 |

## linear

### change management

| command | what it shows | criteria |
|---|---|---|
| `curl -s -X POST 'https://api.linear.app/graphql' -H 'Authorization: {api_key}' -H 'Content-Type: application/json' -d '{"query": "{ teams { nodes { name key } } }"}'` | Linear teams (project tracking) | CC8.1 |

## macos

### access management

| command | what it shows | criteria |
|---|---|---|
| `defaults read com.apple.screensaver askForPassword; defaults read com.apple.screensaver askForPasswordDelay` | Screen-lock password requirement and its delay, in seconds | CC6.1 |

### encryption

| command | what it shows | criteria |
|---|---|---|
| `fdesetup status` | FileVault full-disk encryption state on this Mac | CC6.7 |

### network security

| command | what it shows | criteria |
|---|---|---|
| `/usr/libexec/ApplicationFirewall/socketfilterfw --getglobalstate --getstealthmode` | Application firewall state and stealth mode | CC6.6 |

### vulnerability management

| command | what it shows | criteria |
|---|---|---|
| `defaults read /Library/Preferences/com.apple.SoftwareUpdate AutomaticCheckEnabled AutomaticDownload CriticalUpdateInstall AutomaticallyInstallMacOSUpdates 2>/dev/null; softwareupdate --schedule` | Automatic update settings, including security updates | CC7.1 |

## okta

### access management

| command | what it shows | criteria |
|---|---|---|
| `curl -s -H 'Authorization: SSWS {api_token}' 'https://{domain}.okta.com/api/v1/policies?type=MFA_ENROLL'` | Okta MFA enrollment policy | CC6.1 |
| `curl -s -H 'Authorization: SSWS {api_token}' 'https://{domain}.okta.com/api/v1/users?limit=200' \| python3 -c "import sys,json; users=json.load(sys.stdin); print(f'Total users: {len(users)}'); [print(f'{u[\"profile\"][\"email\"]}: {u[\"status\"]}') for u in users[:20]]"` | List Okta users and status (active/suspended/deprovisioned) | CC6.1, CC6.2 |
| `curl -s -H 'Authorization: SSWS {api_token}' 'https://{domain}.okta.com/api/v1/policies?type=PASSWORD'` | Okta password policy configuration | CC6.1 |
| `curl -s -H 'Authorization: SSWS {api_token}' 'https://{domain}.okta.com/api/v1/policies?type=OKTA_SIGN_ON'` | Okta sign-on policy (session, authentication rules) | CC6.1 |
| `curl -s -H 'Authorization: SSWS {api_token}' 'https://{domain}.okta.com/api/v1/groups' \| python3 -c "import sys,json; groups=json.load(sys.stdin); [print(f'{g[\"profile\"][\"name\"]}: {g.get(\"_embedded\",{}).get(\"stats\",{}).get(\"usersCount\",\"N/A\")} users') for g in groups[:20]]"` | Okta groups (role-based access structure) | CC6.3 |

## pagerduty

### logging monitoring

| command | what it shows | criteria |
|---|---|---|
| `curl -s -H 'Authorization: Token token={api_key}' 'https://api.pagerduty.com/escalation_policies' \| python3 -c "import sys,json; policies=json.load(sys.stdin)['escalation_policies']; [print(f'{p[\"name\"]}: {len(p[\"escalation_rules\"])} rules') for p in policies]"` | PagerDuty escalation policies (incident response) | CC7.3, CC7.4 |

## posthog

### access management

| command | what it shows | criteria |
|---|---|---|
| `curl -s -H "Authorization: Bearer $POSTHOG_API_KEY" 'https://{host}/api/organizations/@current/members/'` | PostHog organization members and access levels | CC6.1, CC6.2, CC6.3 |

### data protection

| command | what it shows | criteria |
|---|---|---|
| `curl -s -H "Authorization: Bearer $POSTHOG_API_KEY" 'https://{host}/api/organizations/@current/projects/'` | Projects with session-recording, masking and data-retention settings — what customer behaviour is captured and kept | CC6.5, P4.1, P4.2 |

## render

### access management

| command | what it shows | criteria |
|---|---|---|
| `curl -s -H "Authorization: Bearer $RENDER_API_KEY" 'https://api.render.com/v1/owners/{owner_id}/members'` | Workspace members and the role each one holds — who can deploy, change environment variables, or open a shell on production | CC6.1, CC6.2, CC6.3 |

### change management

| command | what it shows | criteria |
|---|---|---|
| `curl -s -H "Authorization: Bearer $RENDER_API_KEY" 'https://api.render.com/v1/services?limit=100'` | Services with their deploy source, branch and auto-deploy setting | CC8.1 |
| `curl -s -H "Authorization: Bearer $RENDER_API_KEY" 'https://api.render.com/v1/services/{service_id}/deploys?limit=50'` | Deploy history for one service (who deployed what, when) | CC8.1 |

## sentinelone

### data protection

| command | what it shows | criteria |
|---|---|---|
| `curl -s -H 'Authorization: ApiToken {api_token}' 'https://{domain}.sentinelone.net/web/api/v2.1/agents?limit=5'` | SentinelOne managed endpoints | CC6.8 |

## sentry

### access management

| command | what it shows | criteria |
|---|---|---|
| `curl -s -H "Authorization: Bearer $SENTRY_AUTH_TOKEN" 'https://sentry.io/api/0/organizations/{org_slug}/members/'` | Sentry organization members, roles and 2FA status | CC6.1, CC6.2, CC6.3 |

### data protection

| command | what it shows | criteria |
|---|---|---|
| `curl -s -H "Authorization: Bearer $SENTRY_AUTH_TOKEN" 'https://sentry.io/api/0/organizations/{org_slug}/'` | Org settings including dataScrubber, scrubIPAddresses and sensitiveFields — whether customer data can reach error reports | CC6.5, CC6.7, P4.1 |

### incident response

| command | what it shows | criteria |
|---|---|---|
| `curl -s -H "Authorization: Bearer $SENTRY_AUTH_TOKEN" 'https://sentry.io/api/0/organizations/{org_slug}/issues/?statsPeriod=90d&query=is:unresolved'` | Open issues over the last 90 days — evidence that errors are surfaced and triaged rather than accumulating unseen | CC7.2, CC7.3 |

### logging monitoring

| command | what it shows | criteria |
|---|---|---|
| `curl -s -H "Authorization: Bearer $SENTRY_AUTH_TOKEN" 'https://sentry.io/api/0/organizations/{org_slug}/projects/'` | Projects monitored, with platform and data-scrubbing settings | CC7.1, CC7.2 |

## snyk

### vulnerability management

| command | what it shows | criteria |
|---|---|---|
| `curl -s -H 'Authorization: token {api_token}' 'https://api.snyk.io/rest/orgs/{org_id}/projects?version=2024-06-21'` | Snyk monitored projects and vulnerability counts | CC7.1 |

## supabase

### access management

| command | what it shows | criteria |
|---|---|---|
| `curl -s -H "Authorization: Bearer $SUPABASE_ACCESS_TOKEN" https://api.supabase.com/v1/organizations/{org_slug}/members` | Supabase organization members and their roles | CC6.1, CC6.2, CC6.3 |
| `curl -s -H "Authorization: Bearer $SUPABASE_ACCESS_TOKEN" https://api.supabase.com/v1/projects/{project_ref}/config/auth` | Auth configuration: MFA, password policy, session limits, and the redirect allow list | CC6.1, CC6.2 |

### backup recovery

| command | what it shows | criteria |
|---|---|---|
| `curl -s -H "Authorization: Bearer $SUPABASE_ACCESS_TOKEN" https://api.supabase.com/v1/projects/{project_ref}/database/backups` | Backup inventory and point-in-time recovery status. An empty list with PITR off is itself the finding — record it either way | CC9.1, A1.2 |

### data protection

| command | what it shows | criteria |
|---|---|---|
| `curl -s -H "Authorization: Bearer $SUPABASE_ACCESS_TOKEN" -X POST https://api.supabase.com/v1/projects/{project_ref}/database/query -H 'Content-Type: application/json' -d '{"query":"select tablename, rowsecurity from pg_tables where schemaname = '\''public'\'' order by 1"}'` | Row Level Security enabled per table. NOTE: RLS enabled with a permissive policy is the same as no RLS, so pair this with the policy list below | CC6.1, CC6.3 |
| `curl -s -H "Authorization: Bearer $SUPABASE_ACCESS_TOKEN" -X POST https://api.supabase.com/v1/projects/{project_ref}/database/query -H 'Content-Type: application/json' -d '{"query":"select tablename, policyname, roles::text, qual from pg_policies where schemaname = '\''public'\'' order by 1,2"}'` | The RLS policies themselves — a policy whose qual is 'true' and whose roles include anon is a public table whatever its name says | CC6.1, CC6.3 |

### encryption

| command | what it shows | criteria |
|---|---|---|
| `curl -s -H "Authorization: Bearer $SUPABASE_ACCESS_TOKEN" https://api.supabase.com/v1/projects/{project_ref}/ssl-enforcement` | SSL enforcement on database connections | CC6.7 |

### network security

| command | what it shows | criteria |
|---|---|---|
| `curl -s -H "Authorization: Bearer $SUPABASE_ACCESS_TOKEN" https://api.supabase.com/v1/projects/{project_ref}/network-restrictions` | Network restrictions (which CIDRs may reach the database) | CC6.6 |

## upstash

### access management

| command | what it shows | criteria |
|---|---|---|
| `curl -s -u "$UPSTASH_EMAIL:$UPSTASH_API_KEY" https://api.upstash.com/v2/redis/databases` | Databases with TLS, eviction and region settings (no key values) | CC6.1, CC6.7 |

### encryption

| command | what it shows | criteria |
|---|---|---|
| `curl -s -u "$UPSTASH_EMAIL:$UPSTASH_API_KEY" https://api.upstash.com/v2/redis/database/{database_id}` | One database's TLS enforcement and encryption-at-rest state | CC6.7 |
