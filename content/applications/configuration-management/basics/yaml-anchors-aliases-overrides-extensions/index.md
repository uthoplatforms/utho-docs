---
slug: "Unlocking YAML Magic: Anchors, Aliases, and Extensions"
description: 'Unlock the potential of YAML language with examples illustrating anchors, aliases, and overrides. These features ensure code modularity and maintainability, adhering to the DRY'
keywords: ['YAML anchors']
tags: ['docker', 'kubernetes']
published: 2021-07-02
modified_by:
  name: Utho
title: "Exploring YAML Anchors, Aliases, and Overrides"
title_meta: "YAML Examples: Anchors, Aliases, and Overrides"
authors: ["Pawan Kumar"]
---

YAML anchors, aliases, overrides, and extensions streamline YAML file organization by minimizing data repetition. This guide delves into these advanced YAML features, offering insights beyond the fundamentals covered in the [YAML Syntax Reference](/docs/guides/yaml-reference/) guide.

## YAML Anchors and Aliases

Imagine you're utilizing [Docker Compose](https://docs.docker.com/compose/) to define a specific WordPress customization. Docker provides an [example specification](https://docs.docker.com/compose/wordpress/) that currently starts as follows:

    {{< file >}}
    version: "3.9"

    services:
    db:
    image: mysql:5.7
    volumes:
    - db_data:/var/lib/mysql
    restart: always
    ...
    {{< /file >}}

Docker's documentation shows how to use a `docker-compose.yml` file to set up a simple blog with a data store mounted on `/var/lib/mysql`.

However, in a professional setup, you often require more than just one WordPress instance. You might need a production instance for real users and a separate test instance to validate functionality before making it live. One approach to managing multiple instances is to define them separately in your configuration file.

    {{< file >}}
    version: "3.9"

    services:
    production-db:
    image: mysql:5.7
    volumes:
    - db_data:/var/lib/mysql
    restart: always
    environment:
    MYSQL_ROOT_PASSWORD: somewordpress
    MYSQL_DATABASE: wordpress
    MYSQL_USER: wordpress
    MYSQL_PASSWORD: wordpress
    ...
    test-db:
    image: mysql:5.7
    volumes:
    - db_data:/var/lib/mysql
    restart: always
    environment:
    MYSQL_ROOT_PASSWORD: somewordpress
    MYSQL_DATABASE: wordpress
    MYSQL_USER: wordpress
    MYSQL_PASSWORD: wordpress
    {{< /file >}}

An anchor (&) and alias (*) condense these definitions as follows:

    {{< file >}}
    version: "3.9"

    services:
    production-db: &database-definition
    image: mysql:5.7
    volumes:
    - db_data:/var/lib/mysql
    restart: always
    environment:
    MYSQL_ROOT_PASSWORD: somewordpress
    MYSQL_DATABASE: wordpress
    MYSQL_USER: wordpress
    MYSQL_PASSWORD: wordpress
    ...
    test-db: *database-definition
    {{< /file >}}

In this example, `&database-definition` serves as an anchor, while `*database-definition` is its corresponding alias.

Using aliases streamlines YAML content, making it more concise and easier to digest for humans. This compactness not only reduces file size but also enhances readability by emphasizing essential details. Additionally, employing anchor-alias pairs simplifies maintenance tasks. For instance, if `MYSQL_USER` requires updating from `wordpress` to `special_wordpress_account`, modifying the anchor suffices, automatically propagating the change to all corresponding aliases. This eliminates the need to manually update each occurrence, reducing the risk of errors.

Aliases are particularly effective in condensing complex YAML specifications, often reducing them to half or even smaller fractions of their original sizes.## YAML Overrides

In certain cases, sections of a YAML file may share most of their contents but differ in specific aspects. For instance, in the WordPress scenario, databases could be configured similarly, except for unique passwords for each instance. YAML's overrides feature addresses this scenario.

    {{< file >}}
    version: "3.9"

    services:
    production-db: &database-definition
    image: mysql:5.7
    volumes:
    - db_data:/var/lib/mysql
    restart: always
    environment: &environment-definition
    MYSQL_ROOT_PASSWORD: somewordpress
    MYSQL_DATABASE: wordpress
    MYSQL_USER: wordpress
    MYSQL_PASSWORD: production-password
    ...
    test-db:
    <<: *database-definition
    environment:
    <<: *environment-definition
    MYSQL_PASSWORD: test-password
    ...
    {{</ file >}}

The << symbolizes a special override syntax, enabling the modification of values associated with an alias.

## Economy of Expression

Anchors, aliases, and overrides streamline your YAML configuration files, ensuring they remain concise while adhering to the principle of DRY (Don't Repeat Yourself). These YAML features condense your files, enhancing readability and simplifying maintenance tasks.
