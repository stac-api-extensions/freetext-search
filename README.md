# STAC API - Free-Text Search

- **Title:** Free-Text Search
- **OpenAPI specification:** [openapi.yaml](openapi.yaml)
- **Conformance Classes:**
  - <https://api.stacspec.org/v1.0.0-rc.1/item-search#free-text>
  - <https://api.stacspec.org/v1.0.0-rc.1/collection-search#free-text>
  - <https://api.stacspec.org/v1.0.0-rc.1/ogcapi-features#free-text>
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

This extension sits somewhere between the basic API and the [Filter Extension](https://github.com/stac-api-extensions/filter) providing
more powerful query features than the raw API but without the flexibility and increased operators of the filter plugin. This should mostly be used to provide
the ability to search for keywords across all properties with more complex queries and faceted search being provided by the filter extension.

This extension is aligned with [OGC API - Records](https://docs.ogc.org/DRAFTS/20-004.html#_parameter_q) and can be implemented against any of the search endpoints (Collection Search, Item Search, Features).

## HTTP GET / POST

**Conformance Classes:**
  - Item Search (global): <https://api.stacspec.org/v1.0.0-rc.1/item-search#free-text>
  - Collection Search: <https://api.stacspec.org/v1.0.0-rc.1/collection-search#free-text>
  - Features (item search per collection): <https://api.stacspec.org/v1.0.0-rc.1/ogcapi-features#free-text>

The basic free-text search adds a `q` query parameter to the request URI.
See <https://docs.ogc.org/DRAFTS/20-004.html#_parameter_q> for details.

The search works case-insensitive and spaces have no special meaning.
Any of the search terms must be present in the set of text fields (OR operaror).

- In HTTP `GET` requests, all search terms must be separated by a comma. For example, if you want to search for "Earth Observation" or "EO", your query parameter should be as follows: `q=EO,Earth Observation`.
- In HTTP `POST` requests, an array of search terms must be provided, for example: `{"q": ["EO", "Earth Observation"]}`.
