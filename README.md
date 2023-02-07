# STAC API - Free-Text Search Extension Specification

- **Title:** Free-Text Search Extension Specification
- **OpenAPI specification:** [openapi.yaml](openapi.yaml)
- **Conformance Classes:**
  - <https://api.stacspec.org/v1.0.0-beta.2/item-search#free-text-search>
- **Scope:** STAC API - Core
- **[Extension Maturity Classification](https://github.com/radiantearth/stac-api-spec/tree/main/README.md#maturity-classification):** Proposal
- **Dependencies:**
  - [STAC API - Core](https://github.com/radiantearth/stac-api-spec/tree/v1.0.0-rc.2/core)
- **Owner**: @agstephens
  
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
| `/search?q=sentinel` | Free-text query against all properties | This will search for any matching items where `properties.*` CONTAINS `"sentinel"` |
| `/search?q="climate model"` | Free-text search using exact | This will search for any matching items where `properties.*` CONTAINS the exact phrase `"climate model"` |
|`/search?q=climate model`| Using `OR` term match (**Default**) | This will search for any matching items where `properties.*` CONTAINS `"climate"` OR `"model"`|
|`/search?q=climate OR model`| Using `OR` term match (**Default**) | This will search for any matching items where `properties.*` CONTAINS `"climate"` OR `"model"`|
|`/search?q=climate AND model`| Using `AND` term match | This will search for any matching items where `properties.*` CONTAINS `"climate"` AND `"model"`|
| `/search?q=(quick OR brown) AND fox` | Parentheses can be used to group terms | This will search for matching items where `properties.*` CONTAINS `"quick"` OR `"brown"` AND `"fox"` |
| `/search?q=quick +brown -fox` | Indicate included and excluded terms using `+`/`-` | This will search for items where `properties.*` INCLUDES `"brown"` EXCLUDES `"fox"` OR CONTAINS `"quick"` |

## HTTP POST

When calling the relevant endpoint using POST withContent-Type: application/json, this adds an attribute q with an object value to the core JSON search request body.

The syntax for the q attribute is:

```json
{
  "q": "your query string"
}
```

See [examples](#examples) section for more complex queries
