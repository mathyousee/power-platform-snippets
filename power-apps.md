# Power Apps

Little snippets, functions, and code that I want to remember later.

## Format currency

EN-US $2,000.00

```
Text( currencyfield, "[$-en-US]$ #,###.00" )
```

## Format with a custom date format

The key to this one is to pass in a legit date/datetime value. If the source field provides the field as a *Date*, you're good to go. Use the DateValue() or DateTimeValue() function as needed to adjust the source from *Text* to *Date*

```
Text(date or datetime field, "[$-en-US] yyyy-mm-dd hh:mm:ss.fff AM/PM" )

(adjust the yyyy-mm-dd... part as appropriate)
```

More comprehensive details on [docs.microsoft.com](https://docs.microsoft.com/en-us/powerapps/maker/canvas-apps/show-text-dates-times)

## Create a Collection by hand

When I have an app where I want to reference specific fields, but I haven't attached to a data source, I like to use a Collection as a placeholder. This allows me to use similar data to get things looking right. 

The following example is from an app where I later populated a URL list based on a search query. Simple 2-column blank table:

```
ClearCollect(flowSiteURL,{url:"",name:""});
```

The format with sample data and additional rows can look like this:

```
ClearCollect(flowSiteURL,{url:"https://make.powerapps.com",name:"Power Platform Maker Portal"},{url:"https://connectingthedata.com",name:"Connecting the Data website"});
```

## Collect response values from a Flow

At the end of a Power Automate Flow, you can return data to the app. When calling the Flow, call it as the table value for a Collection.

```
ClearCollect(varFlowResponse,FlowName.Run())
```

This won't behave correctly if you modify the Response format in the Flow after it's been added to the Power App. The collection will become a single value of *True* even though the full payload is there. The fix for this is to remove all references to the Flow, save/close the Power App, then open and re-add the Flow.

## Add Columns to a Collection using a LookUp to another table

In the following, I'm creating a local Collection, which includes a few columns from a related table.

I've already pulled the related table into a local collection *LocalFoods* so the LookUps don't get chatty with network calls to the data source.

```
ClearCollect(LocalInventory,
    AddColumns(Filter(Inventory,Gone=false),
        "FoodName", LookUp(LocalFoods,UPC = ParentUPC).Title,
        "FoodSize", LookUp(LocalFoods,UPC = ParentUPC).Size,
        "FoodSearchTags", LookUp(LocalFoods,UPC = ParentUPC).SearchTags,
        "FoodUnits", LookUp(LocalFoods,UPC = ParentUPC).UnitsInPackage
        )
    );
```

Things to remember: for the LookUp, the first parameter is the "parent" table, the second parameter starts with he column I'm comparing in the second table...this gets the record. Then I returmn the specific **.Field** as the result for the column.

```
LookUp(LocalFoods,UPC = ParentUPC).Title
         ^         ^           ^       ^
         |         |            \       `----.
[Parent Table] [Parent Field] [Match value] [Returned Field] 
```

## Define and call reusable functions inside of a canvas Power App

Sometimes I find myself copy/pasting the same formula (or set of formulas) to multiple components inside of a canvas Power App. I ofetn think *it's going to be a pain if I have to remember all of the places where I used this same formula if I need to update it later, I wish I could make the formula change in just one place and have the rest be magically updated*. There are different approaches to this, but I find this to be the most straightforward.

### Creating the user defined function

- Add a **Toggle** control to the Power App (I add to a screen that the User can't get to, similar to this approach) and set some properties:
  - .Name: *fncMyFunction* - Set whatever function name you like
  - .Default:  *varMyFunction* - Set the Default value to a variable name that's prefixed with *var* in place of *pre*
  - .OnChange: [your formulas go here] - This is the part that will run every time the function is triggered
- Wherever you want  to "call" the function, use the following:

```
Set(varMyFunction,!varMyFunction)
```

When triggered, the variable is flipped like a switch, which will trigger the *OnChange* Action property of the *fncMyFunction* Toggle control.

### Updating the user defined function

In the future, if you need to update the formulas, simply change the *fncMyFunction.OnChange* Action property. All of the rest of the references can stay as they are.

Credit: [Code re-usability in Power Apps](https://powerusers.microsoft.com/t5/News-Announcements/Code-re-usability-in-PowerApps-using-User-Defined-functions/ba-p/672998#)

## Links

[Canvas Power Apps formula reference](https://docs.microsoft.com/en-us/powerapps/maker/canvas-apps/formula-reference)

[Northwind Traders Sample Data](https://docs.microsoft.com/en-us/powerapps/maker/canvas-apps/northwind-install)
