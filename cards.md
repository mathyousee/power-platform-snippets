# Cards

References for Cards in Power Platform.

**In this article**

- [Cards](#cards)
  - [Navigate and Slect a Record](#navigate-and-select-a-record)

## Navigate and Select a Record

Using a Collection (array) for a data set, a repeating row to display the records on the card, and an Index (integer) to select a record.

Note, there are more elegant ways to display and select these, but this was a fun activity to familiarize myself with some of the nuances when working with the Cards preview functionality.

``` JSON
{
  "name": "DataverseCard",
  "description": "DataverseCard",
  "author": "System Administrator",
  "screens": {
    "main": {
      "template": {
        "type": "AdaptiveCard",
        "body": [
          {
            "type": "TextBlock",
            "size": "Medium",
            "weight": "bolder",
            "text": "Review Your Accounts",
            "id": "textLabel1"
          },
          {
            "type": "TextBlock",
            "id": "textLabel6",
            "wrap": true,
            "$data": "=varAllAccounts",
            "weight": "=If(ThisItem.Account=Index(varAllAccounts,ActiveRow).Account,\\"bolder\\",\\"default\\")",
            "text": "=If(ThisItem.Account=Index(varAllAccounts,ActiveRow).Account,\\"->\\"&ThisItem.'Account Name',ThisItem.'Account Name')",
            "isSubtle": false
          }
        ],
        "actions": [
          {
            "type": "Action.Execute",
            "id": "button2",
            "title": "<",
            "associatedInputs": "auto",
            "verb": "onSelect_Button4"
          },
          {
            "type": "Action.Execute",
            "id": "viewDetails",
            "title": "View Details",
            "associatedInputs": "auto",
            "verb": "onSelect_Button1"
          },
          {
            "type": "Action.Execute",
            "id": "button3",
            "title": ">",
            "associatedInputs": "auto",
            "verb": "onSelect_Button5"
          }
        ],
        "$schema": "<http://adaptivecards.io/schemas/1.4.0/adaptive-card.json>",
        "version": "1.4"
      },
      "verbs": {
        "onSelect_Button5": "If(ActiveRow<CountRows(varAllAccounts), Set(ActiveRow,ActiveRow+1))",
        "submit": "echo",
        "onSelect_Button1": "Navigate(DetailsScreen)",
        "onSelect_Button4": "If(ActiveRow>1, Set(ActiveRow,ActiveRow-1))"
      }
    },
    "DetailsScreen": {
      "template": {
        "type": "AdaptiveCard",
        "body": [
          {
            "type": "TextBlock",
            "size": "Medium",
            "weight": "bolder",
            "text": "=\\"Account: \\"&Index(varAllAccounts,ActiveRow).'Account Name'",
            "id": "textLabel1"
          },
          {
            "type": "TextBlock",
            "text": "=\\"Account: \\"&Index(varAllAccounts,ActiveRow).Description",
            "wrap": true,
            "id": "textLabel2"
          },
          {
            "type": "Input.Text",
            "id": "textInput1",
            "label": "Account Description",
            "text": "",
            "placeholder": "only add something here if you want to update the value"
          }
        ],
        "actions": [
          {
            "type": "Action.Execute",
            "id": "button1",
            "title": "Back",
            "associatedInputs": "auto",
            "verb": "onSelect_Button2"
          },
          {
            "type": "Action.Execute",
            "id": "Button3",
            "title": "Update Account Description",
            "associatedInputs": "auto",
            "verb": "onSelect_Button3"
          }
        ],
        "$schema": "<http://adaptivecards.io/schemas/1.4.0/adaptive-card.json>",
        "version": "1.4"
      },
      "verbs": {
        "onSelect_Button3": "Patch(Accounts, Index(varAllAccounts,ActiveRow), {Description:textInput1})",
        "onSelect_Button2": "Back()"
      }
    }
  },
  "sampleData": {
    "main": {},
    "DetailsScreen": {}
  },
  "connections": {
    "redacted_account": {
      "apiName": "shared_commondataserviceforapps",
      "name": "shared-commondataser-500a8cee-a057-418d-a9e4-ba66f0141153",
      "apiId": "/providers/Microsoft.PowerApps/apis/shared_commondataserviceforapps",
      "apiDisplayName": "Microsoft Dataverse",
      "tablePluralName": "Accounts",
      "logicalName": "account",
      "environment": "redactedguid",
      "hasParameters": true
    }
  },
  "variables": {
    "varAllAccounts": {
      "type": "object",
      "title": "",
      "description": "",
      "default": "=Accounts",
      "x-ms-cardapp": {
        "scope": "session"
      },
      "loadPowerFx": true
    },
    "ActiveRow": {
      "type": "integer",
      "title": "",
      "description": "",
      "default": 1,
      "x-ms-cardapp": {
        "scope": "session"
      }
    }
  },
  "flows": {},
  "locale": "en-US"
}

```
