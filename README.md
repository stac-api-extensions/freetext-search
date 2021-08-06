|   |   |
|---|---|
| **OpenAPI Specification** | [openapi.yaml](openapi.yaml) |
| **Conformance Class** | https://api.stacspec.org/v1.0.0-beta.2/item-search#free-text-search |
| **[Maturity Classification](https://github.com/radiantearth/stac-api-spec/blob/master/extensions.md#extension-maturity)** | Pilot |
| **Dependents** | - Item Search |
  
This defines a new parameter, `q` that allows the user to perform free-text queries against the item properties. 
The value of the parameter is a string and is passed to the underlying backend for free-text searches.
Queries can be executed either against all string property fields or against specific fields and can make use of `AND` and `OR`
operators.

This extension sits somewhere between the basic API and the [Filter Extension](https://github.com/radiantearth/stac-api-spec/tree/master/fragments/filter) providing
more powerful query features than the raw API but without the flexibility and increased operators of the filter plugin. This should mostly be used to provide
the ability to search for keywords across all properties with more complex queries and faceted search being provided by the filter extension.

## HTTP GET

### Examples

| URL Example | Summary | Detail |
| ----------- | ------- | ------ |
| `/search?q=sentinel` | Free-text query against all properties | This will search for any matching items where `properties.*` CONTAINS `sentinel` |
| `/search?q=sat:orbit_state:ascending` | Free-text query against specific property | This will search for any matching items where `properties.sat:obit_state` CONTAINS `ascending` |
| `/search?q="climate model"` | Free-text search using exact | This will search for any matching items where `properties.*` CONTAINS the exact phrase `"climate model"` |
|`/search?q=climate model`| Using `OR` term match (**Default**) | This will search for any matching items where `properties.*` CONTAINS `climate` OR `model`|
|`/search?q=climate+model`| Using `AND` term match | This will search for any matching items where `properties.*` CONTAINS `climate` AND `model`|

## HTTP POST

When calling the relevant endpoint using POST withContent-Type: application/json, this adds an attribute q with an object value to the core JSON search request body.

The syntax for the q attribute is:

```json
{
  "q": "your query string"
}
```

See [examples](#examples) section for more complex queries
