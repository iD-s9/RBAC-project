# RBAC-project
# Azure RBAC Least-Privilege Access Control

## Problem

By default, Azure accounts often rely on a single "Owner" identity with full control over every resource in the subscription. This is a common real-world security risk: if that one identity is compromised, an attacker gains unrestricted access to everything. This project rebuilds access control from that default state using least-privilege principles, so different roles only get the exact permissions they need and nothing more.

## What I built

- Created two Entra ID (Azure AD) test users and grouped them by function: a **Developers** group and an **Auditors** group, instead of relying on a single all-powerful account.
- Wrote a **custom RBAC role** (`VM Operator - Start/Stop Only`) that permits starting and stopping virtual machines but explicitly excludes delete or configuration-change permissions. See [`policies/vm-operator-role.json`](./Policies/VM-Operator.json).
- Scoped the custom role's assignable scope to a single resource group (`rg-security-project`), rather than the whole subscription, to limit blast radius.
- Assigned the built-in **Reader** role to the Auditors group, giving read-only visibility with no ability to modify resources.
- Enabled **MFA via Security Defaults** at the tenant level, enforcing multi-factor authentication on every account verified by signing in as the test-developer account and confirming the Microsoft Authenticator prompt appeared.

## Before / After
| State | Screenshot |
|---|---|
| Before: single Owner role, no team structure | [`before-owner-role.png`](./Screenshots/before-owner-role.png) |
| Before: Entra ID with only one user | [`before-etra id-users.png`](./Screenshots/before-etra%20id-users.png) |
| After: Developers and Auditors groups created | [`After creating the Entra ID users.png`](./Screenshots/After%20creating%20the%20Entra%20ID%20users.png) |
| After: scoped role assignments (VM Operator, Reader) | [`After role assignments.png`](./Screenshots/After%20role%20assignments.png) |
| MFA enforcement confirmed on test account | [`mfa-proof.png`](./Screenshots/mfa-proof.png) |

## What I learned / next steps

Writing the custom role definition made it clear how granular Azure permissions actually are a single Azure resource action (like starting a VM) maps to a specific, narrow permission string, which is very different from just picking a broad built-in role like a Contributor. 

A natural next step would be automating this kind of least-privilege audit with a script (e.g. using Azure CLI or PowerShell to periodically check for over-permissioned role assignments), rather than reviewing them manually in the console.

## Tools used

Microsoft Azure (Free Tier), Microsoft Entra ID, Azure RBAC, Microsoft Defender for Cloud (Access recommendations), Security Defaults (MFA)
