# Power Fx

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

## Quotation marks in text

```
"I would like ""this"" to be in quotes." // Reads: I would like "this" to be in quotes.
```

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

### Updating the user defined function

In the future, if you need to update the formulas, simply change the *fncMyFunction.OnChange* Action property. All of the rest of the references can stay as they are.

Credit: [Code re-usability in Power Apps](https://powerusers.microsoft.com/t5/News-Announcements/Code-re-usability-in-PowerApps-using-User-Defined-functions/ba-p/672998#)

## Simple theming

The [Power Platform Creator Kit](https://docs.microsoft.com/en-us/power-platform/guidance/creator-kit/overview) includes a theme designer (which is verrrrrry similar to this [Fluent UI Theme Designer](https://fluentuipr.z22.web.core.windows.net/heads/master/theming-designer/index.html)).

To use these in the app, I set a variable with multiple properties:

```
Set(AppTheme,
  { palette: 
    { 
        themePrimary: "#0078d4", 
        themeLighterAlt: "#eff6fc", 
        themeLighter: "#deecf9", 
        themeLight: "#c7e0f4", 
        themeTertiary: "#71afe5", 
        themeSecondary: "#2b88d8", 
        themeDarkAlt: "#106ebe", 
        themeDark: "#005a9e", 
        themeDarker: "#004578", 
        neutralLighterAlt: "#faf9f8", 
        neutralLighter: "#f3f2f1", 
        neutralLight: "#edebe9", 
        neutralQuaternaryAlt: "#e1dfdd", 
        neutralQuaternary: "#d0d0d0", 
        neutralTertiaryAlt: "#c8c6c4", 
        neutralTertiary: "#a19f9d", 
        neutralSecondary: "#605e5c", 
        neutralPrimaryAlt: "#3b3a39", 
        neutralPrimary: "#323130", 
        neutralDark: "#201f1e", 
        black: "#000000", 
        white: "#ffffff"
    }
  }
)
```

Then instead of specifying specific colors in my app, I'll reference AppTheme.pallete.themePrimary or AppTheme.pallete.neutralPrimary instead of the RBGA value (or color name). Also, this works with the Template apps.

Then, later, if I want to modify the colors in my app later, I can update this one variable and it's immediately reflected throughout the app.

Those who have followed this resource probably saw a variant on this with far fewer colors. While this is definitely a lot more to choose from, it's not really so complex. I explored some more of the Fluent UI theme designer web app "semantic slots" detail and came up with my most important subset of colors:

- themePrimary - Used for links, button backgrounds, icons, and headers
- neutralPrimary - used for primary text
- white - used for backgrounds, button text,  (note this is note necessarily white, but instead is the "Background Color" in the theme designer, but black always seems to be Black)
- neutralLight - used for many element borders, also menu/list item backgrounds on hover
- themeDark - used for button hover/press colors
- neutralTertiary - used for many disabled elements. Note, this is an oversimplification but close enough for my apps

I use the same approach for fonts, though admittedly I'm still trying to find the sweet spot of how much to define/automate this way. Note, I haven't updated the AppTheme variable to include fonts, but the template apps aren't expecting it either.

```

Set(MyFont,
  {
    FaceHeader: Font.'Lato Black',
    FaceBody: Font.'Lato Light',
    SizeH1: 28,
    SizeH2: 24,
    SizeLarge: 16,
    SizeStandard: 14,
    SizeTiny: 12
  }
)

```

This approach is also useful for other general properties that need to be set for various controls. I borrowed the target list from the Center of Excellence theme editor.

```

Set(myProperties,
  {
    TextBoxHeight: 80,
    RadiusValue: 5,
    PaddingValue: 8,
    BorderThicknessValue: 0,
    FocusedBorderThicknessValue: 0
  }
)

```

To save time on setting these properties, I try to use copy/paste of a similar control that I've already treated (in favor of adding a new one from the control library). That said, if I have multiple components to update at once, I'll multi-select these from the navigation view, then use the formula bar to choose he property (such as Font) then set all of the selected components to MyFont.FaceBody.

## Navigation Commands

JavaScript is supported as well, so there are some functions included below as well.

### Navigate to Page from Grid or Record

There's probably a more elegant way to use a single JavaScript function, but hey, it works :)

The following must be uploaded as a JavaScript web resource. It hard codes the page name for simple build, but I'm sure there's something more elegant that someone can come up with to pass it in as a parameter to the function.

``` js
function openFullPageFromGrid(selectedItems)
{
    let selectedItem = selectedItems[0];

    if (selectedItem) {     
        let pageInput = {
            pageType: "custom",
            name: "ctd_custompagelogicalname_35171",
            entityName: selectedItem.TypeName,
            recordId: selectedItem.Id,
        };
        let navigationOptions = {
            target: 1
        };
        Xrm.Navigation.navigateTo(pageInput, navigationOptions)
            .then(
                function () {
                    // Handle success
                }
            ).catch(
                function (error) {
                    // Handle error
                }
            );
    }
}

function openFullPageFromItem(selectedItem)
{
// Inline Page
var pageInput = {
    pageType: "custom",
    name: "ctd_custompagelogicalname_35171",
    entityName: "ctd_table",
    recordId: selectedItem,
};
var navigationOptions = {
    target: 1
};
Xrm.Navigation.navigateTo(pageInput, navigationOptions)
    .then(
        function () {
            // Called when page opens
        }
    ).catch(
        function (error) {
            // Handle error
        }
    );

}
```

Calling from the **Main grid**:

- Open the Navigation editor
- *Make sure the Power Fx formula for **OnSelect** is cleared out*
- Choose action type of JavaScript
- Set the appropriate library
- Set the function name `openFullPageFromGrid`
- Add a parameter: *SelectedControlSelectedItemRecord*

Note, I'm also including a rule to only show the button when a single record is selected.

``` PowerFx
CountRows(Self.Selected.AllItems) = 1
```

Calling from the **Record form**:

- Open the Navigation editor
- *Make sure the Power Fx formula for **OnSelect** is cleared out*
- Choose action type of JavaScript
- Set the appropriate library
- Set the function name `openFullPageFromItem`
- Add a parameter: *FirstPrimaryItemId*

### Handle command navigation parameter in Canvas Page

The parameter that gets passed to the page has a slightly different format, it may include curly braces {}, so when consuming the parameter, make sure to strip out the braces in most cases. Example formula is below.

```
Set(RecordItem, 
    If(IsBlank(Param("recordId")),
        First('Table'),
        LookUp('Table', 'RecordId' = GUID(Substitute(Substitute(Param("recordId"),"{",""),"}",""))))
    )
```

## Links

[Power Fx overview](https://docs.microsoft.com/en-us/power-platform/power-fx/overview)

[Canvas Power Apps formula reference](https://docs.microsoft.com/en-us/powerapps/maker/canvas-apps/formula-reference)
