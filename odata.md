# Odata expressions

References for Odata filters

## Filter results

 Use the `$filter` system query option to set criteria for which entities will be returned.  
  
<a name="bkmk_buildInFilterOperators"></a>

### Standard filter operators

 The Web API supports the standard OData filter operators listed in the following table.  
  
|Operator|Description|Odata Filter field example|  
|--------------|-----------------|-------------|  
|**Comparison Operators**|||  
|`eq`|Equal|`revenue eq 100000`|  
|`ne`|Not Equal|`revenue ne 100000`|  
|`gt`|Greater than|`revenue gt 100000`|  
|`ge`|Greater than or equal|`revenue ge 100000`|  
|`lt`|Less than|`revenue lt 100000`|  
|`le`|Less than or equal|`revenue le 100000`|  
|**Logical Operators**|||  
|`and`|Logical and|`revenue lt 100000 and revenue gt 2000`|  
|`or`|Logical or|`contains(name,'(sample)') or contains(name,'test')`|  
|`not`|Logical negation|`not contains(name,'sample')`|  
|**Grouping Operators**|||  
|`( )`|Precedence grouping|`(contains(name,'sample') or contains(name,'test')) and revenue gt 5000`|  
  
> [!NOTE]
>  This is a sub-set of the [11.2.5.1.1 Built-in Filter Operations](https://docs.oasis-open.org/odata/odata/v4.0/errata02/os/complete/part1-protocol/odata-v4.0-errata02-os-part1-protocol-complete.html). Arithmetic operators and the comparison has operator are not supported in the Web API.  
  
<a name="bkmk_buildInQueryFunctions"></a>

### Standard query functions  
 
The Web API supports these standard OData string query functions:
 
|Function|Odata Filter field example|  
|--------------|-------------|  
|`contains`|`contains(name,'(sample)')`|  
|`endswith`|`endswith(name,'Inc.')`|  
|`startswith`|`startswith(name,'a')`|  
  
> [!NOTE]
>  This is a sub-set of the [11.2.5.1.2 Built-in Query Functions](https://docs.oasis-open.org/odata/odata/v4.0/errata02/os/complete/part1-protocol/odata-v4.0-errata02-os-part1-protocol-complete.html). `Date`, `Math`, `Type`, `Geo` and other string functions aren’t supported in the web API.  
  
### Common Data Service Web API query functions
 
Common Data Service provides a number of special functions that accept parameters, return Boolean values, and can be used as filter criteria in a query. See <xref:Microsoft.Dynamics.CRM.QueryFunctionIndex> for a list of these functions. The following is an example of the <xref href="Microsoft.Dynamics.CRM.Between?text=Between Function" /> searching for accounts with a number of employees between 5 and 2000.  
  
```http 
GET [Organization URI]/api/data/v9.1/accounts?$select=name,numberofemployees
&$filter=Microsoft.Dynamics.CRM.Between(PropertyName='numberofemployees',PropertyValues=["5","2000"])  
```  
  
More information: [Compose a query with functions](use-web-api-functions.md#bkmk_composeQueryWithFunctions). 

<a name="bkmk_LambdaOperators"></a>

### Use Lambda operators

The Web API allows you to use two lambda operators, which are `any` and `all` to evaluate a Boolean expression on a collection.

<a name ="bkmk_anyoperator"></a>

### `any` operator

The `any` operator returns `true` if the Boolean expression applied is `true` for any member of the collection, otherwise it returns `false`. The `any` operator without an argument returns `true` if the collection is not empty.

## Aggregate and Grouping results

By using `$apply` you can aggregate and group your data dynamically.  Possible use cases with `$apply`:

|Use Case|Example|
|--------------|-------------| 
|List of unique statuses in the query|`accounts?$apply=groupby((statuscode))`|
|Aggregate sum of the estimated value|`opportunities?$apply=aggregate(estimatedvalue with sum as total)`|
|Average size of the deal based on estimated value and status|`opportunities?$apply=groupby((statuscode),aggregate(estimatedvalue with average as averagevalue)`|
|Sum of estimated value based on status|`opportunities?$apply=groupby((statuscode),aggregate(estimatedvalue with sum as total))`|
|Total opportunity revenue by Account name|`opportunities?$apply=groupby((parentaccountid/name),aggregate(estimatedvalue with sum as total))`|
|Primary contact names for accounts in 'WA'|`accounts?$apply=filter(address1_stateorprovince eq 'WA')/groupby((primarycontactid/fullname))`|
|Last created record date and time|`accounts?$apply=aggregate(createdon with max as lastCreate)`|
|First created record date and time|`accounts?$apply=aggregate(createdon with min as firstCreate)`|

The aggregate functions are limited to a collection of 50,000 records.  Further information around using aggregate functionality with Common Data Service can be found here: [Use FetchXML to construct a query](../use-fetchxml-construct-query.md).

Additional details on OData data aggregation can be found here: [OData Extension for Data Aggregation Version 4.0](https://docs.oasis-open.org/odata/odata-data-aggregation-ext/v4.0/cs01/odata-data-aggregation-ext-v4.0-cs01.html).  Note that Common Data Service supports only a sub-set of these aggregate methods.

## Videos

[Odata introduction - Jon Levesque and Ahmad Najjar on YouTube](https://youtu.be/Kj8M_hXWc88?list=PLN-cZRQeAiDWT0J1NW9sBDEXX3jkKkyjP)

- What is Odata
- What things do you use it for
- How does this apply to Flow?
- Operators ([29:26](https://youtu.be/Kj8M_hXWc88?list=PLN-cZRQeAiDWT0J1NW9sBDEXX3jkKkyjP&t=1766))
- Functions ([30:05](https://youtu.be/Kj8M_hXWc88?list=PLN-cZRQeAiDWT0J1NW9sBDEXX3jkKkyjP&t=1805))

## Links

[Odata website](https://www.odata.org/)

[Web API Documentation on docs](https://docs.microsoft.com/en-us/powerapps/developer/common-data-service/webapi/query-data-web-api)
