---
layout: spec
title: Data Packages
listed: true
version: 1.0.0-beta.17
updated: 23 March 2016
created: 12 November 2007
ietf-keywords: true
author:
 - Rufus Pollock (Open Knowledge Labs)
 - Martin Keegan (Open Knowledge Labs)
summary: A Data Package (or DataPackage) is a coherent collection of data 
 and possibly other assets in a single 'package'. It provides the basis 
 for convenient delivery, installation and management of datasets.
---

<div class="alert" markdown="block">
NOTE: This is a draft specification - though nearing v1.0. If you have comments
or suggestions please file them in the [issue tracker][issues]. If you have
explicit changes please fork the [git repo][repo] and submit a pull request.
</div>

[issues]: https://github.com/dataprotocols/dataprotocols/issues
[repo]: https://github.com/dataprotocols/dataprotocols

### Changelog

- `1.0.0-beta.17`: make resources property required as per [issues #253](https://github.com/dataprotocols/dataprotocols/issues/253)
- `1.0.0-beta.16`: description is markdown formatted as per [issue #152](https://github.com/dataprotocols/dataprotocols/issues/152); MimeType for Data Packages is vnd.datapackage [issue #245](https://github.com/dataprotocols/dataprotocols/issues/245)
- `1.0.0-beta.15`: only one of `url`, `path`, `data` present on as per [issue #223](https://github.com/dataprotocols/dataprotocols/issues/223); remove `base` property as per [issue #232](https://github.com/dataprotocols/dataprotocols/issues/232)
- `1.0.0-beta.14`: drop `licenses` in favour of `license` as per [issue #214](https://github.com/dataprotocols/dataprotocols/issues/214)
- `1.0.0-beta.13`: add support for sharing schemas across resources via schema references as per [issue #71](https://github.com/dataprotocols/dataprotocols/issues/71)
- `1.0.0-beta.12`: remove `datapackage_version` as per [issue #140](https://github.com/dataprotocols/dataprotocols/issues/140)
- `1.0.0-beta.11`: introduce `author`, integrate with `contributors` and remove `maintainers` and `publishers` as per this [issue](https://github.com/dataprotocols/dataprotocols/issues/130)
- `1.0.0-beta.10`: `license` introduced and `licenses` updated as per this [issue](https://github.com/dataprotocols/data-packages/issues/1)
- `1.0.0-beta.8`: `last_modified` and `modified` removed following this [issue](https://github.com/dataprotocols/dataprotocols/issues/83)
- `1.0.0-beta.7`: `dependencies` renamed to `dataDependencies` following this [issue](https://github.com/dataprotocols/dataprotocols/issues/75)
- `1.0.0-beta.5` -> `1.0-beta.6`: Moved `resources` from MUST to MAY

### Table of Contents 
{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}

# Specification

A data package consists of:

* Metadata that describes the structure and contents of the package
* Resources such as data files that form the contents of the package

A valid data package MUST provide a data package "descriptor" file named
`datapackage.json`. This file SHOULD be placed in the top-level directory
(relative to any other resources provided as part of the data package).

The data package descriptor provides general metadata about the data package
and describes the resources that make up its contents. The structure of the
descriptor is specified below.

In addition to this descriptor a data package will include other resources such
as data files. The Data Package specification does NOT impose any requirements
on their form or structure and can therefore be used for packaging **any kind
of data**.

The data included in the package may be provided as:

* Files bundled locally with the package descriptor
* Remote resources, referenced by URL
* "Inline" data (see below) which is included directly in the
  `datapackage.json` file

## Illustrative Structure

A minimal data package on disk would be a directory containing a single file:

    datapackage.json  # (required) metadata and schemas for this data package

Obviously lacking a single piece of actual data would make this of doubtful
use. A slightly less minimal version would be:

    datapackage.json
    # a data file (CSV in this case)
    data.csv

Additional files such as a README, scripts (for processing or analyzing the
data) and other material may be provided. By convention scripts go in a scripts
directory and thus, a more elaborate data package could look like this:

    datapackage.json  # (required) metadata and schemas for this data package
    README.md         # (optional) README in markdown format

    # data files may go either in data subdirectory or in main directory
    mydata.csv
    data/otherdata.csv         

    # the directory for code scripts - again these can go in the base directory
    scripts/my-preparation-script.py

Several exemplar data packages can be found in the [datasets organization on github][datasets], including:

* [World GDP][gdp]
* [ISO 3166-2 country codes][3166] 

[datasets]: https://github.com/datasets
[gdp]: https://github.com/datasets/gdp 
[3166]: https://github.com/datasets/country-codes


## Descriptor (datapackage.json)

`datapackage.json` is the central file in a Data Package. It provides:

* General metadata such as the package's title, license, publisher etc
* A list of the data "resources" that make up the package including their
  location on disk or online and other relevant information (including,
  possibly, schema information about these data resources in a structured form)

The Package descriptor MUST be a valid JSON file. (JSON is defined in [RFC 4627][]).

[RFC 4627]: http://www.ietf.org/rfc/rfc4627.txt

It MAY contain any number of attributes. All attributes at the first level not
otherwise specified here are considered `metadata` attributes.

A valid descriptor MUST contain a `name` attribute. These fields, and additional
metadata attributes, are described in the "Required Fields" section below.

A valid descriptor MAY contain a `resources` attribute.

Here is an illustrative example of a datapackage JSON file:

    {
      # general "metadata" like title, sources etc
      "name" : "a-unique-human-readable-and-url-usable-identifier",
      "title" : "A nice title",
      "license" : "The package's license",
      "sources" : [...],
      # list of the data resources in this data package
      "resources": [
        {
          ... resource info described below ...
        }
      ],
      # optional
      ... additional information ...
    }

## Metadata

### Required Fields

A valid package MUST include the following fields:

* `name` (required) - short url-usable (and preferably human-readable) name of
  the package. This MUST be lower-case and contain only alphanumeric characters
  along with ".", "_" or "-" characters. It will function as a unique
  identifier and therefore SHOULD be unique in relation to any registry in
  which this package will be deposited (and preferably globally unique).

  The name SHOULD be invariant, meaning that it SHOULD NOT change when a data
  package is updated, unless the new package version should be considered a
  distinct package, e.g. due to significant changes in structure or
  interpretation. Version distinction SHOULD be left to the version field. As
  a corollary, the name also SHOULD NOT include an indication of time range
  covered.

* `resources` - a JSON array of zero or more JSON objects. Each JSON object
  describes one of the data resources in the package and MUST follow the
  specification for resource objects in the "Resource Information" section
  below.

### Strongly Recommended Fields

In addition to the above fields, the following fields SHOULD be included in
every package descriptor:

* `license` - is a field specifying the license under which the package is
  provided.
  
  **This property is not legally binding and does not guarantee the package is
  licensed under the terms defined in this property.**
  
  * `license` is an object OR string. The value of the string version SHOULD be
  an [Open Definition license ID][od-licenses] (preferably one that is
  [Open Definition approved][od-approved]. An example of the string version
  is as follows:

        { "license" : "ODC-PDDL-1.0" }

  The object structure MUST contain a `type` property and a `url` property
  linking to the actual text. The `type` SHOULD be an
  [Open Definition license ID][od-license] if an ID exists for the license and
  otherwise may be the general license name or identifier. Here is an example:

        "license": {
          "type": "ODC-PDDL-1.0",
          "url": "http://opendatacommons.org/licenses/pddl/"
        }

[od-licenses]: http://licenses.opendefinition.org/
[od-approved]: http://opendefinition.org/licenses/
[semver]: http://semver.org

### Recommended Fields

The following are commonly used fields that the package descriptor MAY contain:

* `title` - a title or one sentence description for this package
* `description` - a description of the package. The description MUST be
  [markdown][] formatted -- this also allows for simple plain text as plain
  text is itself valid markdown. The first paragraph (up to the first double
  line break) should be usable as summary information for the package.
* `homepage` - URL string for the data packages web site
* `version` - a version string identifying the version of the package. It should conform to the [Semantic Versioning][semver] requirements.
* `sources` - an array of source objects. Each source object may have `name`, `web` and `email` fields. Example:

      "sources": [{
        "name": "World Bank and OECD",
        "web": "http://data.worldbank.org/indicator/NY.GDP.MKTP.CD"
      }]
* `author` and `contributors` - these are fields for describing people or
  organizations who contributed to this Data Package. `author` is a single
  person / organization whilst `contributors` is an array. By convention, the
  first contributor is the original author of the package unless `author` is
  also present - in this sense, `author` is simply a convenience that allows
  for single line entries like the following:

      "author": "Joe Bloggs <joe@bloggs.com>"

  A "person" or "organization" is an object OR string. It MUST contain a `name`
  property and MAY contain `web` and `email`. An example of the object structure
  is as follows:

      {
        "name": "Joe Bloggs",
        "email": "joe@bloggs.com",
        "web": "http://www.bloggs.com"
      }

  The string version has the following structure:
  
      NAME <EMAIL> (WEB)
  
  Example:
  
      Joe Bloggs <joe@bloggs.com> (http://www.bloggs.com/)
  
  Email and web are optional in the string version as well e.g.:
  
      Joe Bloggs
      Joe Bloggs <joe@bloggs.com>
      Joe Bloggs (http://www.bloggs.com)
  
  Note on semantics: use of the "author" field does not imply that that person
  was the original creator of the data in the data package - merely that they
  created and/or maintain the data package. It is common for data packages to
  "package" up data from elsewhere. The original origin of the data can be
  indicated with the `sources` field - see above.
* `keywords` - an Array of string keywords to assist users searching for the
  package in catalogs.

[markdown]: http://commonmark.org/

### Optional Fields

A package descriptor MAY contain the following fields:

* `image` - a link to an image to use for this data package
* `dataDependencies` - Object of prerequisite data packages on which this package
  depends in order to install. Follows same format as CommonJS Packages spec
  v1.1.Each dependency defines the lowest compatible MAJOR[.MINOR[.PATCH]]
  dependency versions (only one per MAJOR version) with which the package has
  been tested and is assured to work. The version may be a simple version
  string (see the version property for acceptable forms), or it may be an object
  group of dependencies which define a set of options, any one of which
  satisfies the dependency. The ordering of the group is significant and
  earlier entries have higher priority. Example:

      "dataDependencies": {
         "country-codes": "",
         "unemployment": "2.1",
         "geo-boundaries": {
           "acmecorp-geo-boundaries": ["1.0", "2.0"],
           "othercorp-geo-boundaries": "0.9.8",
         },
      }
* `schemas`: an Object containing schemas keyed by a name. See the [Resource
  Schemas](#resource-schemas) section below.
 

<div class="alert" markdown="block">
NOTE: A Data Package author MAY add any number of additional fields beyond those
listed in the specification here.  For example, suppose you were storing
time series data and want to list the temporal coverage of the data in the
Data Package you could add a field `temporal` (cf [Dublin Core][dc-temporal]):

    "temporal": {
      "name": "19th Century",
      "start": "1800-01-01",
      "end": "1899-12-31"
    }

This flexibility enables specific communities to extend Data Packages as
appropriate for the data they manage. As an example, the [Tabular Data
Package][tdp] specification extends Data Package to the case
where all the data is tabular and stored in CSV.
</div>

[tdp]: /tabular-data-package/
[dc-temporal]: http://dublincore.org/documents/usageguide/qualifiers.shtml#temporal

## Resource Information

Packaged data resources are described in the `resources` property of the package descriptor. 
This property is an array of values. Each value describes a single resource and 
MUST be a JSON object.

### Required Fields

Resource information MUST contain one (and only one) of the following
attributes which specify the location of the associated data file (either
online, 'relative' (local), or 'inline'):

* `url`: url of this data resource
* `path`: unix-style ('/') relative path to the resource. Path MUST be a relative
  path, that is relative to the directory or URL in which the descriptor file
  (`datapackage.json`) listing this file resides.
* `data`: (inline) a field containing the data directly inline in the
  `datapackage.json` file. Further details below.

<div class="alert" markdown="block">
NOTE: the use of a `url` allows a data package to reference data not
contained locally in the Data Package. The `path` attribute may also
be used for Data Packages located online -- in this case it determines
the resource file's URL relative to the `datapackage.json`'s URL.
</div>

There are NO other required fields. However, there are a variety of common
fields that can be used which we detail below.

### Recommended Fields

It is recommended that a resource SHOULD contain the following fields:

* `name`: a resource SHOULD contain an `name` attribute. The name is a simple name or
  identifier to be used for this resource.

  * If present, the name MUST be unique amongst all resources in this data
    package.
  * The name SHOULD be usable in a url path and SHOULD therefore consist only
    of alphanumeric characters plus ".", "-" and "_".
  * It would be usual for the name to correspond to the file name (minus the
    extension) of the data file the resource describes.

### Optional Fields

A resource MAY contain any number of additional fields. Common fields include:

* `title`: a title or label for the resource.
* `description`: a description of the resource.
* `format`: 'csv', 'xls', 'json' etc. Would be expected to be the standard file
  extension for this type of resource.
* `mediatype`: the mediatype/mimetype of the resource e.g. 'text/csv', 'application/vnd.ms-excel'
* `encoding`: specify the character encoding of the resource's data file. The values should be one of 
 the "Preferred MIME Names" for [a character encoding registered with IANA][iana]. If no 
 value for this key is specified then the default is UTF-8.
* `bytes`: size of the file in bytes
* `hash`: the MD5 hash for this resource. Other algorithms can be indicated by prefixing
  the hash's value with the algorithm name in lower-case. For example:

      "hash": "sha1:8843d7f92416211de9ebb963ff4ce28125932878"

* `schema`: a schema or a pointer to the schema for the resource - see below
  for more on this attribute
* `sources`: as for data package metadata.
* `license`: as for data package metadata. If not specified the resource
  inherits from the data package.

[iana]: http://www.iana.org/assignments/character-sets/character-sets.xhtml

### Inline Data

Resource data rather than being stored in external files can be shipped
'inline' on a Resource using the `data` attribute.

The value of the data attribute  can be any type of data. However, restrictions
of JSON require that the value be a string so for binary data you will need to
encode (e.g. to Base64). Information on the type and encoding of the value of
the data attribute SHOULD be provided by the format (or mediatype) attribute
and the encoding attribute.

Specifically: the value of the data attribute MUST be:

* EITHER: a JSON array or object - the data is then assumed to be JSON data and SHOULD be processed as such
* OR: a JSON string - in this case the format or mediatype attributes MUST be provided.

Thus, a consumer of resource object MAY assume if no format or mediatype
attribute is provided that the data is JSON and attempt to process it as such.

Examples 1 - inline JSON:

    {
       ...
       "resources": [
         {
            "format": "json",
            # some json data e.g. 
            "data": [
               { "a": 1, "b": 2 },
               { .... }
            ]
         }
       ]
    }

Example 2 - inline CSV:

    {
       ...
       "resources": [
         {
            "format": "csv",
            "data": "A,B,C\n1,2,3\n4,5,6"
         }
       ]
    }

### Resource Schemas

The value for the `schema` field on a `resource` MUST be an Object or a string
that "references" an Object as detailed below.

<div class="alert" markdown="block">
NOTE: the Data Package specification places no restrictions on the form of this
Object. This flexibility enables specific communities to define schemas
appropriate for the data they manage. As an example, the [Tabular Data
Package][tdp] specification requires the schema value to conform to [JSON Table
Schema][jts].
</div>

#### Schema References

If `schema` is a string it is a "reference" to an Object and MUST be:

* EITHER: a URL. The URL MUST:

  * EITHER: resolve to a JSON document that is the schema
  * OR: include a fragment identifier which conforms to to [JSON
    Pointer](http://tools.ietf.org/html/rfc6901) notation. The URL must then
    resolve to a JSON document and the schema is obtained by resolving within
    that JSON document using the fragment identifier as the JSON pointer as per
    section 6 of the JSON pointer specification.  URL.

* OR: a simple string name which MUST correspond to the 'name' (key) in the
  `schemas` object in the same datapackage.json file - see next section.

#### `schemas` Property

A Data Package MAY have a `schemas` property. The value of the property MUST be
an Object. Each key in the Object is the name of a schema. A schema name MUST consist only of
lower-case alphanumeric letters, together with
- and \_.

Each value for an entry in the `schemas` Object must be an Object
specifying an appropriate schema.

#### Examples

```
{
  "resources": [
    {
    ...
      "schema": http://url-to/datapackage.json#schemas/schema-name
      }
    }
  ],
...
}
```

```
{
  "resources": [
    {
    ...
      "schema": "xyz-schema"
    }
  ],
  "schemas": {
    "xyz-schema": {
      schema goes here ...
    }
  }
}
```

## MediaType

The MediaType [RFC4288] for a Data Package SHOULD be *application/vnd.datapackage*.


# Appendices

## Aims

* Simple
* Extensible
* Human editable (for metadata)
* Machine usable (easily parsable and editable)
* Based on existing standard formats
* Not linked to a particular language or system

## How It Fits into the Ecosystem

* Minimal wrapping to provide for machine automated sharing and obtaining of
  data
* Data Packages can be registered into and found in indexes (local or remote)
* Tools (based on code libraries) integrate with these indexes (and storage) to
  download and upload material

<img src="https://docs.google.com/drawings/pub?id=1W0s91bQGS-bmGOLm519mMq9zDJvRhP71pwuJtkflRws&w=896&h=660" align="center" alt="Data Packages and the Wider Ecosystem" width="90%" />

## Prior Work

The specification is heavily inspired by various software packaging formats.
[Read the Appendix](/data-packages/appendix/).

