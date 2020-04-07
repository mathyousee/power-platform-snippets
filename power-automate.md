# Power Automate

Little snippets, functions, and code that I want to remember later.

## Format utcNow date results

YYYY-MM-dd

```
formatDateTime(utcNow(), 'yyyy-MM-dd')
```

## Query CDS for current user

1. O365 connector Get My Profile V2
2. Query **User** entity, filter query is:

```
azureactivedirectoryobjectid eq '[O365id]'
```

## Get first record from a CDS List Records action

Next up is a scope that includes list and compose actions. A list users action will get us the value for systemuserid. This is a user’s id/GUID in CDS. A filter query is used to narrow the results down to just a single record when azureactivedirectoryobjectid matches id from the Get my profile‘s output. Normally Flow will throw in an apply to each loop, when dynamic content from a list records action is used. To get around this, a compose action with a first expression is used. There is only a single record that we’re after so this trick works well. The expression used is:

```
first(body('List_Users_to_get_GUID')?['value']). systemuserid
```

## Did the body output contain data?

Next, a condition is used to check whether or not any RAs are returned or not. If a user doesn’t have any RAs that meet our criteria, the Flow is cancelled as succeeded. If RAs are found, we’re jumping into the Flow’s first apply to each loop to iterate through the previous list records action for RAs. Imagine if those first expressions were not used in the previous steps. We’d have a pretty loopy Flow by now. The empty expression used in the Does List my RAs return values condition is:

```
empty(outputs('List_my_Resource_Assignments')?['body/value'])
```

## Get primary entity and related record information in CDS flow step

Use the "Expand Query" property in the CDS connector

Expand N:1

```
primarycontactid($select=contactid,fullname)
```

Expand 1:N

```
account_tasks($select=name)
```

## Relate Records action in Common Data Service (Current Environment) connector

This works with both one to many and many to many relationships when working in the Common Data Service.

```
[Environment URL]/api/data/v9.0/[Entity Schema Name]([GUID for the target record you’re associating])
```

Example URL payload in the attribute, using an environment "myorg" and relationship called "ctd_contact_game". The Contact is specified in a different attribute and the game GUID is "00000000-0000-0000-0000-000000000001":

```
https://myorg.crm.dynamics.com/api/data/v9.0/ctd_contact_game(00000000-0000-0000-0000-000000000001)
```

Action documentation from docs.microsoft.com - <https://docs.microsoft.com/en-us/Connectors/commondataserviceforapps/#relate-records>

Web API documentation from docs.microsoft.com - <https://docs.microsoft.com/en-us/powerapps/developer/common-data-service/webapi/associate-disassociate-entities-using-web-api>

## First item from Sub-array

I faced a challenge with an array, where we were selecting fields to return in a flat table. One of the fields was an array, so we looked for an easy way to return just the first value from that sub-array.

When using the **Data Operations - Select** action, this expression will return the first value from a sub-array

```
@item()?['subArrayFieldName'][0]
```

This is used as the value in an individual row of the Select action.


### Links

<https://daytodaydynamics365.com/listing-a-users-active-resource-assignments-in-project-for-the-web-and-d365-psa-with-power-automate/>

<https://tattooedcrmgirl.com/2019/10/30/microsoft-flow-the-relate-records-action-demystified>
