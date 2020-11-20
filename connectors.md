# Connectors

References for Power Platform connectors

## Connector Authentication Patterns

Power Apps and Power Automate authenticate with connectors to create a connection environment. It is that environment that contains the specific configuration information necessary for the app or flow to talk to the connector API that is used in each interaction. Connectors could choose to use no authentication, basic authentication, API key authentication or OAuth 2.0. The most common are OAuth and API Key.

OAuth if you aren't familiar with it is an authorization framework that allows external applications to obtain controlled access to a target service. Many APIs support it including Dataverse, Facebook and Twitter to name a few. The goal of authentication is to allow the user to sign in to a familiar login dialog, consent to the application using the service, and then setup to allow tokens to be acquired. It is the tokens that are used on each request to prove who the user is and their right to use the API. In the Power Apps and Power Automate usage, a Consent Server is involved that helps manage the tokens and their lifecycle including storing the renewal token in the Consent Server and handling the refresh cycle. The following is a step by step look at what happens when you authenticate a connection using OAuth.

> ![](https://github.com/MicrosoftDocs/power-platform/blob/master/power-platform/admin/media/oauth-consent-flow.png?raw=true "OAUTH Consent flow")


The API Key is a little less complex as it typically involves the API assigning a key that is passed on each request. That key is provided when the connection is established for the connector and is stored in the environment with the other connection information in a secure way. An example of an API Key authentication connector is the Azure Storage Blob. As you can see below it wants the Storage Account Name as well as the Access Key.

> ![](https://github.com/MicrosoftDocs/power-platform/blob/master/power-platform/admin/media/azure-blob-storage-connection.png?raw=true "Azure blob storage connection")


When on-premises gateways are involved the process is even a little more complex. The following diagrams what happens when you establish a connection with the gateway data source.

> ![](https://github.com/MicrosoftDocs/power-platform/blob/master/power-platform/admin/media/architecture-connections.png?raw=true "Connection architecture")

Source: <https://github.com/MicrosoftDocs/power-platform/blob/master/power-platform/admin/wp-connectors.md#connector-authentication-patterns>

## Links

[Connectors Documentation on docs.microsoft.com](https://docs.microsoft.com/en-us/connectors/)
'
> Effective November 2020:
> - Common Data Service (CDS) has been renamed to Microsoft Dataverse. [Learn more](https://aka.ms/PAuAppBlog)
> - Some terminology in Microsoft Dataverse has been updated. For example, *entity* is now *table* and *field* is now *column*. [Learn more](https://go.microsoft.com/fwlink/?linkid=2147247)
