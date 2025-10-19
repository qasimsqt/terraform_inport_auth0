You’ll just run the ones that apply to your tenant.

⚙️ Auth0 Terraform Import Commands — Full List
🧱 1. Applications (Clients)

These are your apps (like SPA, M2M, Native, etc.)

terraform import auth0_client.default_app nKOqzkFxgHkhYu5PyJWJnXlpxgYM7byC
terraform import auth0_client.m2m_app B2XPoerfHs2PmC97bQfdc2ldhJ7xyzZC
terraform import auth0_client.frontend_spa 7HgtP2xya8jQr9Db8YJ13pRfs3Wv2abc


🔍 Where to find IDs:
Go to Auth0 Dashboard → Applications → your app → copy the Client ID

🔐 2. APIs (Resource Servers)

These define backend APIs and scopes.

terraform import auth0_resource_server.api_server 68f420a69642a3ee0c07248d
terraform import auth0_resource_server.analytics_api 2d5412cd223e34708c16abc2


🔍 Where to find IDs:
Dashboard → Applications → APIs → click API → ID is in the URL after /apis/

👥 3. Connections

These are your login sources (DB, Google, GitHub, etc.)

terraform import auth0_connection.db_connection "Username-Password-Authentication"
terraform import auth0_connection.google_connection "google-oauth2"
terraform import auth0_connection.github_connection "github"


🔍 Where to find names:
Dashboard → Authentication → Database / Social → copy Connection Name

🧩 4. Rules

These are custom JavaScript snippets that run during login.

terraform import auth0_rule.add_metadata_rule "Add metadata on login"
terraform import auth0_rule.check_email_verified_rule "Check Email Verified"


🔍 Where to find names:
Dashboard → Auth Pipeline → Rules → copy Rule Name

🧬 5. Actions

The new “Rules v2”. These can be in Flows like Login, Post-Login, etc.

terraform import auth0_action.post_login_action act_MqU84X8K8zxVqNw3
terraform import auth0_action.send_welcome_email_action act_cUw54GzN9pQiZo7b


🔍 Where to find IDs:
Dashboard → Actions → Library → click action → look at URL → ID starts with act_

🧮 6. Roles

RBAC roles with assigned permissions.

terraform import auth0_role.admin_role rol_wI0zgkDeUhEIXCjS
terraform import auth0_role.viewer_role rol_Fw3Ls3ypB9Nv4N3E


🔍 Where to find IDs:
Dashboard → User Management → Roles → click role → URL ends with the Role ID

🧰 7. Resource Server (API) Scopes (optional, often imported automatically with API)

Terraform handles them under the API, so you usually don’t import them separately.

👤 8. Organizations (if you use them)

For multi-tenant setups.

terraform import auth0_organization.customer_org org_EpJ1Hq7CbHbtk5f8
terraform import auth0_organization.partner_org org_QW9zJf62xqZHyD3k


🔍 Where to find IDs:
Dashboard → Organizations → click org → ID starts with org_

🧾 9. Custom Domains
terraform import auth0_custom_domain.primary_custom_domain cdn_7BoOQ11W9uX9sTql


🔍 Where to find ID:
Dashboard → Custom Domains → click domain → check URL (ends with cdn_)

📩 10. Email Provider
terraform import auth0_email_provider.default_email_provider ""


(Empty string since only one provider exists per tenant.)

💌 11. Email Templates
terraform import auth0_email_template.verify_email_template "verify_email"
terraform import auth0_email_template.welcome_email_template "welcome_email"
terraform import auth0_email_template.reset_email_template "reset_email"

🧱 12. Branding (logo, color, etc.)
terraform import auth0_branding.branding ""


(Empty string, because there’s only one branding per tenant.)

🛡️ 13. Guardian (MFA)
terraform import auth0_guardian.guardian ""

🧾 14. Tenant Settings
terraform import auth0_tenant.tenant ""


This is your entire tenant-level configuration (company name, support URL, etc.)

🚀 Final Checklist

When you’re done importing, run:

terraform plan


✅ If you see:

No changes. Your infrastructure matches the configuration.


That means your Terraform state now fully represents your live Auth0 tenant —
you’ve successfully “Terraform-ized” an existing manual setup 🎯
