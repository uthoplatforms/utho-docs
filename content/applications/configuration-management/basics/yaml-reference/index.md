---
slug: "Ultimate YAML Guide: Your Go-To Reference"
description: 'This guide offers a concise introduction to the YAML programming language, covering the essentials to help you effectively work with YAML files'
keywords: ['yaml reference']
published: 2024-05-20
modified_by:
  name: Utho
title: "A YAML Syntax reference."
title_meta: "A YAML Syntax Reference"
tags: ["automation"]
authors: ["Pawan Kumar"]
---


YAML is a versatile data interchange language widely used in configuration files. It's integral to tools like Ansible for configuration management and Kubernetes for container orchestration. As a superset of JSON, YAML 1.2 supports custom data types, making it highly extensible. Due to its popularity in automated builds and continuous delivery, YAML files are prevalent across many public GitHub repositories. This reference guide introduces YAML and includes examples to illustrate its key features.

Here's a snippet from a Kubernetes YAML file for context:

       {{< file "my-apache-pod.yaml">}}
       apiVersion: v1
       kind: Pod
       metadata:
       name: apache-pod
       labels:
       app: web
       {{</ file >}}

This YAML file specifies the API version, the type of Kubernetes resource, and metadata about the resource. Even without prior knowledge of Kubernetes, you can easily grasp the general purpose of each configuration due to YAML's human-readable format. This readability is one of YAML's key advantages over formats like XML or JSON.

## Getting Started with YAML

YAML is compatible across operating systems, virtual environments, and data platforms, making it a popular choice for system configuration. For example, you can use YAML to configure GitHub Actions, where the metadata in the YAML file specifies the inputs and outputs for tasks in your GitHub repository.

Additionally, YAML is used for data interchange. It can serve as a data format for transmitting invoices, recording the current state of a long-running game, or facilitating communication between subsystems in complex machinery.

### Three Basic Rules

You can start using YAML by following a few basic rules. Begin by focusing on these three key areas:

- indentation
- colons
- dashes

#### Indentation

YAML represents data in hierarchical relationships using indentation, with a set number of spaces indicating each level. Here's an example of a GitHub Actions YAML file to illustrate this:

      {{< file "test.yaml">}}
      ...
      jobs:
      blueberry:
       runs-on: ubuntu-latest
       steps:
       - uses: actions/checkout@v2
       - name: Set up Python
       ...
       {{</ file >}}


The example file demonstrates that runs-on and steps belong to the same block, indicating they share the same scope. Conventionally, two spaces are used for indentation, while tabs are discouraged.

## Colons

Colons are used to separate keys and their values. While YAML formally allows any number of spaces after a colon, conventionally, a single space is preferred. For instance:

       {{< file "test.yaml">}}
        ...
        runs-on: ubuntu-latest
        ...
        {{</ file >}}

#### Dashes

Hyphens (-) indicate a list. The subsequent example is sourced from Utho's API v4, utilizing the OpenAPI 3 specification.

          {{< file "openapi.yaml" >}}
           ...
           requestBody:
           description: Information about the OAuth Client to create.
           content:
           application/json:
           schema:
           required:
           - label
           - redirect_uri
            ...
            {{</ file >}}

The `required` list outlines the essential properties of an object. In this instance, the required properties are `label` and `redirect_uri`. Subsequent sections will provide further examples of indentation, colons, and dashes.

## YAML Basic Data Types

YAML comprises three fundamental data types:

- scalars
- lists or sequences
- associative arrays or dictionaries

### Scalars

A scalar represents a single value in YAML and can take the form of a numeric value, a text string, or a boolean value such as true or false. Additionally, YAML allows the expression of a null value, indicating absence or unknownness.


        {{< file "openapi.yaml" >}}
        ...
        properties:
        address:
        type: string
        format: ip
        description: "The IP address."
        example: 97.107.142.141
        ...
        {{</ file >}}

        In the provided YAML example, scalar values are utilized for the address property. Notably, the description property is enclosed in quotes, while the format property is not. The use of single ' ' or double " " quotes enables the inclusion of special reserved YAML characters within strings without encountering parsing errors.


### Lists

To define a list in YAML, each list item is prefaced by a dash -, followed by a space and the value. Additional values should not be placed on the same line.

          {{< file "openapi.yaml" >}}
           ...
           status:
           type: string
           enum:
          - disabled
          - pending
          - ok
          - problem
          ...
          {{</ file >}}

The example snippet illustrates the use of a list under the enum key to specify possible values for the status property. YAML also supports nested lists, allowing for complex data structures.

         {{<  file "openapi.yaml" >}}
         security:
         - personalAccessToken: []
         - oauth:
         - account:read_only
           {{</ >}}

### Dictionaries

YAML supports associative arrays or dictionaries, where data is organized as key-value pairs. For instance, in the provided examples, the description key corresponds to the value "The IP address.".

          {{< file "openapi.yaml" >}}
           ...
           properties:
           address:
           type: string
           format: ip
           description: "The IP address."
           example: 97.107.142.141
           ...
           {{</ file >}}

           Dictionaries in YAML gain versatility when combined with other data types. For example, a dictionary value may itself be a list, and the values of a list could be another dictionary.

## Comparing YAML with other data formats

A clear understanding of YAML is aided by comparing it to other familiar formats. Such comparisons help to delineate the advantages and disadvantages of each format, facilitating informed usage.

### YAML vs JSON

JavaScript Object Notation (JSON) is an openly standardized file format frequently employed for browser configuration and communication purposes.

- YAML encompasses JSON content, serving as a superset of JSON. However, YAML boasts a more lenient syntax compared to JSON.

- YAML allows the usage of unquoted string keys, a feature not permitted in JSON. Additionally, YAML permits the use of single quotes for strings, whereas JSON mandates double quotes.

- YAML enables the inclusion of comments within files, a feature absent in JSON.

- YAML offers a specialized syntax for defining custom data types, allowing for extensions beyond its basic definition.

- YAML surpasses JSON with its support for anchors, aliases, directives, and merge keys.

### YAML vs XML

YAML and XML can both represent the same data, leading to the support of both languages in various applications. However, YAML and XML possess distinct syntactic structures.

Generally, XML tends to be more verbose, making it preferable for expressing content resembling documents, whereas YAML offers a more concise format. Below is an example illustrating how each language might express identical data:

       {{< file "catalogue.xml">}}
         <?xml version=”1.0”?>
         <catalogue>
         <book>
         <author>Homer</author>
         <title>Illiad</title>
         <genre>epic poem</genre>
         </book>
         <book>
         <author>William Gilbert</author>
        <title>On the Magnet and Magnetic Bodies ...</title>
        <genre>natural philosophy</genre>
        </book>
        </catalogue>
        {{</ file >}}

        {{< file "catalogue.yaml">}}
        ---
        catalogue:
        -
        author: Homer
        genre: "epic poem"
        title: Illiad
        -
        author: "William Gilbert"
        genre: "natural philosophy"
        title: "On the Magnet and Magnetic Bodies …"
        {{</ file >}}

### Double and Single Quotes

YAML's syntax includes colons, quotes, commas, and other punctuation marks. Special care is required when utilizing them within string values.

Quotes are unnecessary when using string values in YAML, although they can be employed to treat a numerical value as a string.

Quotation marks ensure that special characters are not parsed.

Double quotes are used to interpret escape codes such as \n, whereas single quotes prevent the parsing of escape codes.

## Diving Deeper

For a deeper understanding of YAML's syntax, the official repository for the YAML 1.2 specification is publicly maintained on GitHub, along with the grammar for YAML 1.2.

The complete YAML 1.2 specification is quite extensive, comprising 211 grammatical rules and a four-part specification. More detailed versions, such as 1.5 and 2.0, are planned for the future. While mastering every aspect of YAML may seem daunting, grasping its key foundational elements, some of which have been discussed in this guide, is sufficient to start working with it effectively.

### YAML Tools

Various tools cater to newcomers exploring YAML. These include automatic YAML linters like YAML Lint. Additionally, tools such as converters between different formats and YAML, such as the YAML to JSON extension for VSCode, as well as YAML prettifiers like the VSCode Prettier - Code formatter extension, can be valuable aids.

For a comprehensive list of tools to assist with YAML-related tasks, the Online YAML tools reference is a valuable resource.
