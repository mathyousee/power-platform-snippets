# Dataverse

Details and reminders for working with Dataverse as a platform component, data store, the transaction pipeline, and custom APIs exposed through Dataverse.

**In this article**

- [Dataverse](#dataverse)
  - [Security Roles](#security-roles)
    - [Minimum privilege security role](#minimum-privilege-security-role)
  - [Standard APIs](#standard-apis)
  - [Custom APIs](#custom-apis)
    - [Dataverse Healthcare APIs](#dataverse-healthcare-apis)
  - [Dataverse Offline Mode](#dataverse-offline-mode-for-canvas)
  - [Links](#links)

## Security roles

### Minimum privilege security role

When creating a net new security role, the best practice is to start with the "minimum privilege security role" and making a copy, rather than starting from a truly blank role.

Slightly more nuanced approach is that every user must have the minimum privileges, but additional roles can be created from blank, which can be layered as needed.

The base role can be downloaded here: [Official download link](https://download.microsoft.com/download/6/5/5/6552A30E-05F4-45F0-AEE3-9BB01E13118A/MinprivilegeSecRole_1_0_0_2.zip) [Local copy in this repo from Feb 2023](./artifacts/MinprivilegeSecRole_1_0_0_2.zip)

To use: Download the solution, import to your dev environment, then select Copy Role.

The full details can be found here: [MS Learn](https://learn.microsoft.com/en-us/power-platform/admin/database-security#minimum-privileges-to-run-an-app)

## Standard APIs

[Dataverse API 101](/dataverse-api-101/readme.md)

## Custom APIs

Developers can create their own custom APIs to expose through Dataverse. Some solutions from Microsoft make use of this in order to offer scenario-specific functionality.

### Dataverse Healthcare APIs

| API             | Action | URI                                                   | Purpose                                                                                                             |
|-----------------|--------|-------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------|
| Upsert Bundle   | POST   | [Organization URI]/api/data/v9.1/msind_UpsertBundle   | Accepts a FHIR bundle, insert/updates values in one or more Dataverse tables based on the mapping template.         |
| Retrieve Bundle | POST   | [Organization URI]/api/data/v9.1/msind_RetrieveBundle | Produces a FHIR bundle for the selected resource, based on the values stored in Dataverse and the mapping template. |

Blog and video for how to get started: [Getting started with the Dataverse Healthcare API](https://techcommunity.microsoft.com/t5/healthcare-and-life-sciences/getting-started-with-the-dataverse-healthcare-api/ba-p/3713587)

Github Gists for how to get started (referenced in the blog/video): [Dataverse Healthcare API samples](https://gist.github.com/mathyousee/3678a14fe5599cb9526428b9e1a6ed24)

## Dataverse offline mode for Canvas

### Power Fx for Dataverse offline mode

**Power Fx send notification based on ConnectionSync value**

``` Power Fx
Switch(
   Connection.Sync,
   ConnectionSync.Connected,
   Notify("Your device is connected to the network, and your app is ready to work offline.",NotificationType.Success), 
   ConnectionSync.ConnectedWithWarning,
   Notify(Connection.LastSyncMessage,NotificationType.Warning),
   ConnectionSync.ConnectedPendingUpsync,
   Notify("Some data on your device must be synchronized with the server.",NotificationType.Warning),
   ConnectionSync.ConnectedError,
   Notify(Connection.LastSyncMessage,NotificationType.Error),
   ConnectionSync.ConnectedRefresh,
   Notify("Your app is currently synchronizing data with the server.",NotificationType.Information),
   ConnectionSync.NotConnected,
   Notify("Your device is not connected to the network, but you can keep using this app.",NotificationType.Success), 
   ConnectionSync.NotConnectedWithWarning, 
   Notify(Connection.LastSyncMessage, NotificationType.Warning),
   ConnectionSync.NotConnectedPendingUpsync,
   Notify("Some data on your device must be synchronized with the server. Reconnect to the network to synchronize.", NotificationType.Warning),
   ConnectionSync.NotConnectedSyncError, 
   Notify(Connection.LastSyncMessage,NotificationType.Error)
)
```

## Links

> Effective November 2020:
> - Common Data Service (CDS) has been renamed to Microsoft Dataverse. [Learn more](https://aka.ms/PAuAppBlog)
> - Some terminology in Microsoft Dataverse has been updated. For example, *entity* is now *table* and *field* is now *column*. [Learn more](https://go.microsoft.com/fwlink/?linkid=2147247)
