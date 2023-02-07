# Dataverse

Details and reminders for working with Dataverse as a platform component, data store, the transaction pipeline, and custom APIs exposed through Dataverse.

**In this article**

- [Dataverse](#dataverse)
  - [Security Roles](#security-roles)
    - [Minimum privilege security role](#minimum-privilege-security-role)
  - [Links](#links)

## Security roles

### Minimum privilege security role

When creating a net new security role, the best practice is to start with the "minimum privilege security role" and making a copy, rather than starting from a truly blank role.

Slightly more nuanced approach is that every user must have the minimum privileges, but additional roles can be created from blank, which can be layered as needed.

The base role can be downloaded here: [Official download link](https://download.microsoft.com/download/6/5/5/6552A30E-05F4-45F0-AEE3-9BB01E13118A/MinprivilegeSecRole_1_0_0_2.zip) [Local copy in this repo from Feb 2023](./artifacts/MinprivilegeSecRole_1_0_0_2.zip)

To use: Download the solution, import to your dev environment, then select Copy Role.

The full details can be found here: [MS Learn](https://learn.microsoft.com/en-us/power-platform/admin/database-security#minimum-privileges-to-run-an-app)


## Links

[Connectors Documentation on docs.microsoft.com](https://docs.microsoft.com/en-us/connectors/)
'
> Effective November 2020:
> - Common Data Service (CDS) has been renamed to Microsoft Dataverse. [Learn more](https://aka.ms/PAuAppBlog)
> - Some terminology in Microsoft Dataverse has been updated. For example, *entity* is now *table* and *field* is now *column*. [Learn more](https://go.microsoft.com/fwlink/?linkid=2147247)
