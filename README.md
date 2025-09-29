# STAC API - Free-Text Search

- **Title:** Free-Text Search
- **OpenAPI specification:** [openapi.yaml](openapi.yaml)
- **Conformance Classes:**
  - <https://api.stacspec.org/v1.0.0-rc.1/item-search#free-text>
  - <https://api.stacspec.org/v1.0.0-rc.1/item-search#advanced-free-text>
  - <https://api.stacspec.org/v1.0.0-rc.1/collection-search#free-text>
  - <https://api.stacspec.org/v1.0.0-rc.1/collection-search#advanced-free-text>
  - <https://api.stacspec.org/v1.0.0-rc.1/ogcapi-features#free-text>
  - <https://api.stacspec.org/v1.0.0-rc.1/ogcapi-features#advanced-free-text>
- **Scope:**
  - STAC API - Collection Search
  - STAC API - Item Search
  - STAC API - Features
- **[Extension Maturity Classification](https://github.com/radiantearth/stac-api-spec/tree/main/README.md#maturity-classification):** Proposal
- **Dependencies:** (any of)
  - [STAC API - Collection Search](https://github.com/stac-api-extensions/collection-search)
  - [STAC API - Item Search](https://github.com/radiantearth/stac-api-spec/tree/v1.0.0/item-search)
  - [STAC API - Features](https://github.com/radiantearth/stac-api-spec/tree/v1.0.0/ogcapi-features)
- **Owner**: @agstephens
  
This defines a new parameter, `q` that allows the user to perform free-text queries against STAC metadata. 
The value of the parameter is a string and is passed to the underlying backend for free-text searches.
The specific set of text fields to which the parameter is applied is left to the discretion of the implementation,
but a recommendation is to at least consider:
- Collections: `title`, `description` and `keywords`
- Catalog: `title`, `description`
- Item: all relevant textual properties
It is also allowed to query against all text fields.

This extension sits somewhere between the basic API and the [Filter Extension](https://github.com/radiantearth/stac-api-spec/tree/master/fragments/filter) providing
more powerful query features than the raw API but without the flexibility and increased operators of the filter plugin. This should mostly be used to provide
the ability to search for keywords across all properties with more complex queries and faceted search being provided by the filter extension.

This extension defines two flavors of free text search. 
A simple one that is aligned with [OGC API - Records](https://docs.ogc.org/is/20-004r1/20-004r1.html#core-query-parameters-q) and 
a more advanced one.
Both variants can be implemented against any of the search endpoints (Collection Search, Item Search, Features).

## Basic

### HTTP GET / POST

**Conformance Classes:**
  - Item Search (global): <https://api.stacspec.org/v1.0.0-rc.1/item-search#free-text>
  - Collection Search: <https://api.stacspec.org/v1.0.0-rc.1/collection-search#free-text>
  - Features (item search per collection): <https://api.stacspec.org/v1.0.0-rc.1/ogcapi-features#free-text>

The basic free-text search adds a `q` query parameter to the request URI.
See <https://docs.ogc.org/is/20-004r1/20-004r1.html#core-query-parameters-q> for details.

The search works case-insensitive and spaces have no special meaning.
Any of the search terms must be present in the set of text fields (OR operaror).

- In HTTP `GET` requests, all search terms must be separated by a comma. For example, if you want to search for "Earth Observation" or "EO", your query parameter should be as follows: `q=EO,Earth Observation`.
- In HTTP `POST` requests, an array of search terms must be provided, for example: `{"q": ["EO", "Earth Observation"]}`.

## Advanced

**Conformance Classes:**
  - Item Search (global): <https://api.stacspec.org/v1.0.0-rc.1/item-search#advanced-free-text>
  - Collection Search: <https://api.stacspec.org/v1.0.0-rc.1/collection-search#advanced-free-text>
  - Features (item search per collection): <https://api.stacspec.org/v1.0.0-rc.1/ogcapi-features#advanced-free-text>

**OpenAPI document:** [openapi.yaml](openapi.yaml)

### HTTP GET

**Examples:**

| URL Example | Summary | Detail |
| ----------- | ------- | ------ |
| `/search?q=sentinel` | Free-text query against all properties | This will search for any matching items that CONTAIN `"sentinel"` |
| `/search?q="climate model"` | Free-text search using exact | This will search for any matching items that CONTAIN the exact phrase `"climate model"` |
|`/search?q=climate model`| Using `OR` term match (**Default**) | This will search for any matching items that CONTAIN `"climate"` OR `"model"`|
|`/search?q=climate OR model`| Using `OR` term match (**Default**) | This will search for any matching items that CONTAIN `"climate"` OR `"model"`|
|`/search?q=climate AND model`| Using `AND` term match | This will search for any matching items that CONTAIN `"climate"` AND `"model"`|
| `/search?q=(quick OR brown) AND fox` | Parentheses can be used to group terms | This will search for matching items that CONTAIN `"quick"` OR `"brown"` AND `"fox"` |
| `/search?q=quick +brown -fox` | Indicate included and excluded terms using `+`/`-` | This will search for items that INCLUDES `"brown"` EXCLUDES `"fox"` OR CONTAIN `"quick"` |

### HTTP POST

When calling the relevant endpoint using `POST` with `Content-Type: application/json`, this adds an attribute `q` with an object value to the core JSON search request body.

The syntax for the q attribute is:

```json
{
  "q": "your query string"
}
```

See [examples](#examples) section for more complex queries
