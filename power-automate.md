# Power Automate

Little snippets, functions, and code that I want to remember later.

## Format utcNow date results

YYYY-MM-dd

```
formatDateTime(utcNow(), 'yyyy-MM-dd')
```

## Query Dataverse User based on O365 user ID

Here's the situation: you are using Power Automate to do something that involves a User record in Dataverse (like assign a record), but all you know is their O365 profile. The pattern is to 

1. [trigger/action that includes an O365 user record, such as [Get User Profile](https://docs.microsoft.com/en-us/Connectors/office365users/#get-user-profile-(v2)) ]
2. Query **User** entity, filter query is:

```
azureactivedirectoryobjectid eq '[O365id]'
```

## Get first record from a Dataverse List Records action

Next up is a scope that includes list and compose actions. A list users action will get us the value for systemuserid. This is a user’s id/GUID in Dataverse. A filter query is used to narrow the results down to just a single record when azureactivedirectoryobjectid matches id from the Get my profile‘s output. Normally Flow will throw in an apply to each loop, when dynamic content from a list records action is used. To get around this, a compose action with a first expression is used. There is only a single record that we’re after so this trick works well. The expression used is:

```
first(body('List_Users_to_get_GUID')?['value']). systemuserid
```

## Did the body output contain data?

Next, a condition is used to check whether or not any RAs are returned or not. If a user doesn’t have any RAs that meet our criteria, the Flow is cancelled as succeeded. If RAs are found, we’re jumping into the Flow’s first apply to each loop to iterate through the previous list records action for RAs. Imagine if those first expressions were not used in the previous steps. We’d have a pretty loopy Flow by now. The empty expression used in the Does List my RAs return values condition is:

```
empty(outputs('List_my_Resource_Assignments')?['body/value'])
```

## Get primary entity and related record information in Dataverse flow step

Use the "Expand Query" property in the Dataverse connector

Expand N:1

```
primarycontactid($select=contactid,fullname)
```

Expand 1:N

```
account_tasks($select=name)
```

## Relate Records action in Dataverse (Current Environment) connector

This works with both one to many and many to many relationships when working in the Dataverse.

```
[Environment URL]/api/data/v9.0/[Entity Schema Name]([GUID for the target record you’re associating])
```

Example URL payload in the attribute, using an environment "myorg" and relationship called "ctd_contact_game". The Contact is specified in a different attribute and the game GUID is "00000000-0000-0000-0000-000000000001":

```
https://myorg.crm.dynamics.com/api/data/v9.0/ctd_contact_game(00000000-0000-0000-0000-000000000001)
```

Action documentation from docs.microsoft.com - <https://docs.microsoft.com/en-us/Connectors/commondataserviceforapps/#relate-records>

Web API documentation from docs.microsoft.com - <https://docs.microsoft.com/en-us/powerapps/developer/common-data-service/webapi/associate-disassociate-entities-using-web-api>

## Lookup relationships when using Create New Record action in Dataverse (Current Environment) connector

Couldn't find the syntax for how to relate to a record in a standard lookup for a new record. I didn't want to have to create the record, then relate the records in a separate step (stubbornness?). The value format for the lookup field is:

```
enityschemaname(recordGUID)
```

So in my particular example, I had a lookup from *Linked Activity* (Many) to *Application* (One).

Getting this value in a single Flow action proved difficult, though.  I struggled to get the @odata.editLink attribute as part of a First() formula, so I used Data Operations steps to clean up the record:

- Data Operation: Parse JSON - technically this is optional, but I like doing this so I can more easily snag out specific attributes
- Data Operation: Select - all I did here was rename the @odata.editLink field to be editLink
- Data Operation: Compose - I used a first() formula to grab the output of the Select action above

I was left with:

```
ctd_applications(deaef762-d4f7-1a11-a815-000d3a8d1000)
```

...which I could use to fill in the lookup field.

For an activity regarding field, include a forward slash in front of the schema name. For example with a Contact record:

```
/contacts(recordGUID)
```

## First item from Sub-array

I faced a challenge with an array, where we were selecting fields to return in a flat table. One of the fields was an array, so we looked for an easy way to return just the first value from that sub-array.

When using the **Data Operations - Select** action, this expression will return the first value from a sub-array

```
@item()?['subArrayName'][0]
```

This is used as the value in an individual row of the Select action. The sub-array in this use case was a single field. I haven't yet tested but if the sub-array had multiple fields I assume a single field's value could be isoloated with:

```
@item()?['subArrayName'][0]. columnmane
```

## Updating list of required fields in original JSON

If you try to Parse JSON and a required field is null, the Flow will fail when run. In the Parse JSON step, modify the *required* fields by updating the *required* array in the **Schema** attribute 

Here's what it looks like if you still have some required fields:
```
"required": [
  "name",
  "id"
]
```

Here's what it looks like if you want no required fields (provided as an FYI but don't abuse this!):
```
"required": []
```

A Type mismatch will also cause a failure, so consider updating the output schema like the *id* attribute below if the source format is not strict:

```
"name": {string},
"id": {},
"description": {string} 
```
More on this - <https://spmaestro.com/handling-json-in-microsoft-flow/>

## Expand query for Dataverse connector, find related lookup record

Expand lookup fields to get the fields you actually care about. For example, if you list Contacts and want the Parent Account name, you could add this to the expand query field:

``` odata
parentcustomerid_account($select=name)
```

Then let's say, perhaps you listed multiple Contacts (and their related account names), but later in the flow you just want to grab the first one. You can grab the related account name for the first Contact record with an expression like this:

``` odata
first(outputs('List_rows')?['body/value'])?['parentcustomerid_account']?['name']
```

Note, that the "first" is just the row, then outside (after) the first() function. Also, the expanded value is an object and you need to get the specific field, even though you only expanded one field.

Note, a single property didn't need to be selected, in fact all of the related fields could be expanded by just calling the relationship name *without* using the `$select` system query option.

``` odata
parentcustomerid_account()
```

Multiple levels of $expand lookup are possible, but limit of 10 $expand (up or down) per query).

More details are here: [MS Docs](https://docs.microsoft.com/en-us/power-automate/dataverse/list-rows)

## Expand query for Dataverse connector, find related many records



Multiple levels of $expand lookup are possible, but limit of 10 $expand (up or down) per query).

https://docs.microsoft.com/en-us/powerapps/developer/data-platform/webapi/retrieve-related-entities-query

## Parse JSON

Mini Tutorial (YouTube video) <https://www.youtube.com/watch?v=q5CruaqHaHg>

Thesis on Parse JSON action in Power Automate - <http://johnliu.net/blog/2018/6/a-thesis-on-the-parse-json-action-in-microsoft-flow>

## Links

<https://daytodaydynamics365.com/listing-a-users-active-resource-assignments-in-project-for-the-web-and-d365-psa-with-power-automate/>

<https://tattooedcrmgirl.com/2019/10/30/microsoft-flow-the-relate-records-action-demystified>

<https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-workflow-definition-language>

<https://spmaestro.com/handling-json-in-microsoft-flow/>

> Effective November 2020:
> - Common Data Service (CDS) has been renamed to Microsoft Dataverse. [Learn more](https://aka.ms/PAuAppBlog)
> - Some terminology in Microsoft Dataverse has been updated. For example, *entity* is now *table* and *field* is now *column*. [Learn more](https://go.microsoft.com/fwlink/?linkid=2147247)
