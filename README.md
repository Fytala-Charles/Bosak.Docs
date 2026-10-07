# Bosak.Schema — Integration Guide

**Bosak.Schema** is the commercial XSD complex-type schema-awareness add-on for the
Bosak XPath 3.1 / XSLT engine. It adds `xsl:import-schema`, `validate as`,
`instance of`, and `treat as` against named XSD complex types to the engine's
schema-aware surface, with spec-correct error codes.

This page is the customer-facing integration guide referenced by the license emails.
It is short on purpose — the full API reference ships with the package.

## Activating your license

The package is inert until activated. Call `License.Activate` **once per process**,
early in your application's startup, with the license key from your email:

```csharp
using Bosak.Schema;

Bosak.Schema.License.Activate("BSK1.eyJ2IjoxLCJzdWIiOiLigKY.");
```

- Activation is local and offline — no server contact, works in air-gapped deployments.
- It is one-way per process: there is no deactivation or transfer API.
- A malformed, forged, or expired key throws `LicenseValidationException` and leaves
  the package inert (the engine keeps working; only the schema-aware surface gates).

## Storing the license key

The key is a plain string — keep it wherever you keep application secrets and read it
at startup. Common patterns:

**Environment variable** (containers, CI):

```csharp
Bosak.Schema.License.Activate(Environment.GetEnvironmentVariable("BOSAK_LICENSE_KEY")!);
```

**File** deployed next to (or separately from) your application:

```csharp
Bosak.Schema.License.Activate(File.ReadAllText("bosak-license.key").Trim());
```

**.NET user-secrets** (development only; never commit the key):

```bash
dotnet user-secrets init
dotnet user-secrets set "Bosak:LicenseKey" "BSK1...."
```

```csharp
Bosak.Schema.License.Activate(config["Bosak:LicenseKey"]!);
```

**Windows registry** (Windows services):

```csharp
using Microsoft.Win32;
var key = Registry.CurrentUser.OpenSubKey(@"Software\YourCompany\YourApp")
    ?.GetValue("BosakLicenseKey") as string
    ?? throw new InvalidOperationException("Bosak license key not configured.");
Bosak.Schema.License.Activate(key);
```

Whichever you choose: treat the key like a password (it is the license), and rotate it
promptly if it leaks — email [licensing@fytala.com](mailto:licensing@fytala.com) and we
will re-issue it.

## License tiers and expiry

| Tier | Duration | Notes |
|------|----------|-------|
| Trial | 30 days | Full-featured evaluation, issued instantly via the form below |
| Developer | 1 year, auto-renews | Per-developer subscription; renewal is automatic — no action needed |
| Team | 1 year, auto-renews | Volume bundle of 10 Developer seats |
| Runtime | 1 year, auto-renews | Per-deployment runtime license |
| Community | 1 year | Free, for OSS maintainers / nonprofit employees / students; re-apply to extend |

Expiry behaviour:

- Every key carries an expiry. **14 days past expiry the license enters a grace
  period** — the surface keeps working, so a failed renewal never hard-stops a running
  build over a weekend.
- After the grace period the package inerts through the exact same error path as an
  unactivated one.
- Expiry is re-evaluated on every gated call — a key that expires mid-process inerts
  cleanly, and no network access is ever involved.

## Getting a license

- **Trial** — request a 30-day evaluation key:
  [https://func-fytala-licensing-prod.azurewebsites.net/api/trial](https://func-fytala-licensing-prod.azurewebsites.net/api/trial)
- **Purchase / renewal** — handled by the storefront; keys and renewal notices arrive
  by email automatically (payments, VAT, and renewals are processed by our merchant of
  record, Stripe).
- **Community License** — apply at
  [https://func-fytala-licensing-prod.azurewebsites.net/api/community/apply](https://func-fytala-licensing-prod.azurewebsites.net/api/community/apply)

## Support

Questions about activation, licensing, or renewals:
[licensing@fytala.com](mailto:licensing@fytala.com)

—

*© Fytala — Bosak.Schema licensing*
