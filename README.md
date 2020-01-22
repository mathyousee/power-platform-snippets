# Snippets

Little snippets, functions, and code that I want to remember later.

## Power Automate

### Format utcNow date results

YYYY-MM-dd

```
formatDateTime(utcNow(), 'yyyy-MM-dd')
```

### Query CDS for current user

1. O365 connector Get My Profile V2
2. Query **User** entity, filter query is:

```
azureactivedirectoryobjectid eq '[O365id]'
```

### Get first record from a CDS List Records action

Next up is a scope that includes list and compose actions. A list users action will get us the value for systemuserid. This is a user’s id/GUID in CDS. A filter query is used to narrow the results down to just a single record when azureactivedirectoryobjectid matches id from the Get my profile‘s output. Normally Flow will throw in an apply to each loop, when dynamic content from a list records action is used. To get around this, a compose action with a first expression is used. There is only a single record that we’re after so this trick works well. The expression used is:

```
first(body('List_Users_to_get_GUID')?['value']). systemuserid
```

### Did the body output contain data?

Next, a condition is used to check whether or not any RAs are returned or not. If a user doesn’t have any RAs that meet our criteria, the Flow is cancelled as succeeded. If RAs are found, we’re jumping into the Flow’s first apply to each loop to iterate through the previous list records action for RAs. Imagine if those first expressions were not used in the previous steps. We’d have a pretty loopy Flow by now. The empty expression used in the Does List my RAs return values condition is:

For 
```
empty(outputs('List_my_Resource_Assignments')?['body/value'])
```

### Get primary entity and related record information in CDS flow step

Use the "Expand Query" property in the CDS connector

Expand N:1

```
primarycontactid($select=contactid,fullname)
```

Expand 1:N

```
account_tasks($select=name)
```

### Links

https://daytodaydynamics365.com/listing-a-users-active-resource-assignments-in-project-for-the-web-and-d365-psa-with-power-automate/


## Power Apps
