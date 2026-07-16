# Source: https://metricpanel-web.vercel.app/docs/billing-team

Browse documentation

# [Billing and team](https://metricpanel-web.vercel.app/docs/billing-team#billing-and-team)

MetricPanel separates organization-level ownership and billing from website-level analytics access. Review both layers when a teammate cannot see a website or should not be able to change its settings.

## [Organization and website scope](https://metricpanel-web.vercel.app/docs/billing-team#organization-and-website-scope)

- The organization owns websites, subscription, usage allowance, and organization settings.
- Each website has its own member list and invitations.
- Website access controls analytics, installation settings, alerts, API credentials, revenue connections, and imports for that website.

Do not share one account between teammates. Invite each person so access can be reviewed and removed independently.

## [Website roles](https://metricpanel-web.vercel.app/docs/billing-team#website-roles)

| Role | Typical access | | ------ | ------------------------------------------------------------------------------- | | Owner | Original website ownership and full management | | Admin | View analytics and manage website settings, members, goals, funnels, and alerts | | Viewer | View analytics without website management actions |

Only owners and admins can invite members or change website management settings. An existing owner is not converted through the standard admin/viewer role control.

## [Invite a website member](https://metricpanel-web.vercel.app/docs/billing-team#invite-a-website-member)

1. Open the website's **Settings**, then choose **Team**.
2. Select **Invite Member**.
3. Enter the teammate's email address and choose Admin or Viewer.
4. Send the invitation and confirm it appears as pending.
5. After acceptance, review the member's role in the same table.

Email invitations require email delivery to be configured for the environment. When delivery is unavailable, do not assume a pending invitation reached the recipient.

## [Change or remove access](https://metricpanel-web.vercel.app/docs/billing-team#change-or-remove-access)

Owners and admins can change non-owner members between Admin and Viewer or remove them from the website. Removing website access does not necessarily delete the person's MetricPanel account or access to other organizations and websites.

Review pending invitations as well as active members. Cancel invitations that are no longer needed before their expiration.

## [Billing and usage](https://metricpanel-web.vercel.app/docs/billing-team#billing-and-usage)

Open **Billing** to review the current plan, billing interval, checkout or portal availability, and plan changes. Open **Usage** to review the current event allowance and any configured pause-tracking behavior.

Billing checkout and customer portal actions are available only when the MetricPanel environment has Stripe billing configured. If the controls report that billing is unavailable, contact the operator or use the written agreement associated with the account instead of retrying payment details.

## [Access review checklist](https://metricpanel-web.vercel.app/docs/billing-team#access-review-checklist)

- Every teammate uses their own account.
- Admin access is limited to people who manage settings or other members.
- Pending invitations still have a current business purpose.
- Former teammates are removed from each website they could access.
- Billing contacts and operational alert destinations are monitored.
- API tokens and webhook secrets are rotated separately when someone with credential access leaves.