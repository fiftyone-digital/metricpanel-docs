# Source: https://nightwatch.laravel.com/docs/organizations

An **Organization** is the top-level container for everything in Nightwatch, including applications, environments, teams, and billing. You can be a member of one or more organizations and can switch between them at any time.

## 

[​](https://nightwatch.laravel.com/docs/organizations#applications)

Applications

An application represents a single Laravel application, such as your web app, API, or internal tool. When creating an application, you’ll choose a **data region**, which determines where all telemetry data for that application is stored. We currently support the following regions:

- **US** (Northern Virginia)
- **EU** (Frankfurt)
- **Australia** (Sydney)

## 

[​](https://nightwatch.laravel.com/docs/organizations#environments)

Environments

An environment represents a specific deployment context within your application, such as Production, Staging, or Development. Each agent you install will be configured to send data to a **single environment**.

## 

[​](https://nightwatch.laravel.com/docs/organizations#team-management)

Team Management

Nightwatch supports two primary roles: **Admin** and **Collaborator**. These roles can be assigned from the organization settings screen. **Collaborators have view-only access** to all resources within the organization. They can view applications, environments, events, issues, and other telemetry data, but cannot make any changes. **Admins have full access** to the organization and can create and modify the organization and its data, including organization details, team invitations, team members, roles, applications, environments, and performance thresholds.

### 

[​](https://nightwatch.laravel.com/docs/organizations#inviting-users)

Inviting Users

You can invite team members to join your organization. Invited users will receive an email invitation that they can accept to join your organization. You can resend an invite at any time, and you can also delete pending invites.

As a user, you can join up to three organizations that are on the Free plan. There’s no limit to the number of organizations you can join on paid plans.

### 

[​](https://nightwatch.laravel.com/docs/organizations#removing-users)

Removing Users

You can remove team members from your organization at any time. When a user is removed, they will lose access to all organization data and resources.

Removing a user is permanent. They will need to be re-invited if you want them to regain access.

### 

[​](https://nightwatch.laravel.com/docs/organizations#enforcing-2fa)

Enforcing 2FA

As an administrator of an organization, you can enforce two-factor authentication (2FA) for all users in the organization. When 2FA enforcement is enabled, all members will be required to configure 2FA on their accounts and provide a verification code in addition to their password when signing in.

## 

[​](https://nightwatch.laravel.com/docs/organizations#delete-an-organization)

Delete an Organization

If you need to delete an organization, you can do so from the organization settings. This action is permanent and cannot be undone.

Deleting an organization will permanently remove all applications including their related events and telemetry data. All team members will lose access to the organization. This action cannot be undone.

Was this page helpful?

YesNo

[Account](https://nightwatch.laravel.com/docs/account) [Subscriptions](https://nightwatch.laravel.com/docs/subscriptions)

⌘I