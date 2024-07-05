---
slug: "Mastering Salt: Unveiling the Magic of Jinja Templates"
description: 'Unlock the Power of Jinja: Dive into Salt Configuration Management with Hands-on Examples.'
keywords: ['salt','jinja','configuration management']
published: 2024-04-02
modified: 2024-04-02
modified_by:
  name: Utho
title: "Jinja Gems: Unveiling Salt's Template Magic"
tags: ["automation","salt"]
authors: ["Utho"]
---
## Introduction to Templating Languages

Jinja is a versatile templating language designed for Python, capable of generating various text-based formats such as HTML, XML, and YAML. With Jinja, you can seamlessly insert data into structured formats. Moreover, it enables you to embed logic or control-flow statements, enhancing reusability and modularity. Jinja's template engine processes code within templates, ultimately producing the desired output in the form of text-based documents.

While templating languages are commonly associated with creating web pages in a Model View Controller architecture, they extend beyond web development. Salt, a popular Python-based configuration management tool, incorporates Jinja to facilitate abstraction and reuse within Salt state files and regular files.

This guide offers an introduction to Jinja, focusing primarily on its application within Salt. If you're new to Salt concepts, it's advisable to review the [Beginner's Guide to Salt](/docs/guides/beginners-guide-to-salt/) before proceeding. Although this guide won't involve creating Salt states, familiarity with the Getting Started with [Getting Started with Salt - Basic Installation and Setup](/docs/guides/getting-started-with-salt-basic-installation-and-setup/) guide can also be beneficial.

## Jinja Basics
This section offers a beginner-friendly overview of Jinja syntax and concepts, accompanied by Jinja and Salt state examples. For a comprehensive understanding of Jinja, refer to the official Jinja [Template Designer Documentation](http://jinja.pocoo.org/docs/2.10/templates/).

Applications like Salt can establish default behaviors for the Jinja templating engine. All examples in this guide adhere to Salt's default Jinja environment options. However, these settings are customizable in the Salt master configuration file.

    {{< file "/etc/salt/master" yaml >}}

Default Jinja environment settings for all templates except sls templates.

    jinja_env:
    block_start_string: '{%'
    block_end_string: '%}'
    variable_start_string: '{{'
    variable_end_string: '}}'
    comment_start_string: '{#'
    comment_end_string: '#}'
    line_statement_prefix:
    line_comment_prefix:
    trim_blocks: False
    lstrip_blocks: False
    newline_sequence: '\n'
    keep_trailing_newline: False

Jinja environment configurations for sls templates.

    jinja_sls_env:  
    block_start_string: '{%'
    block_end_string: '%}'
    variable_start_string: '{{'
    variable_end_string: '}}'
    comment_start_string: '{#'
    comment_end_string: '#}'
    line_statement_prefix:
    line_comment_prefix:
    trim_blocks: False
    lstrip_blocks: False
    {{</ file >}}

{{< note respectIndent=false >}}
Before incorporating Jinja into your Salt states, take a moment to explore the [Salt and Jinja Best Practices](#salt-and-jinja-best-practices) section of this guide. This ensures you're crafting maintainable and comprehensible Salt states. Furthermore, you can employ advanced Salt tools and concepts to improve the modularity and reusability of the Jinja and Salt state examples provided in this guide.
{{< /note >}}

### Delimiters
Templating language delimiters mark the separation between the templating language and other data formats such as HTML or YAML. Jinja employs the following delimiters:

| Delimiter Syntax | Usage       |
| ---------------- |-------------|
| `{% ... %}`      | Control structures |
| `{{ ... }}`      | Evaluated expressions that will print to the template output |
| `{# ... #}`      | Comments that will be ignored by the template engine |
| `#  ... ##`      | Line statements |

In this sample Salt state file, you can distinguish the Jinja syntax from YAML by the `{% ... %}` delimiters enclosing the if/else conditionals.

    {{< file "/srv/salt/webserver/init.sls" yaml >}}
    {% if grains['group'] == 'admin' %}
    America/Denver:
    timezone.system:
    {% else %}
    Europe/Minsk:
    timezone.system:
    {% endif %}
    {{</ file >}}

Refer to the  [control structures](#control-structures) section for further details on conditionals.

### Template Variables

Template variables are accessible through a template's context dictionary, which is automatically generated during the template's evaluation stages. You can access these variables using dot notation:

    {{ foo.bar }}

Or using subscript syntax:

    {{ foo['bar'] }}

Salt offers several context variables by default, which are accessible to any Salt state file or file template.

  - **Salt**: The salt variable offers a robust set of [Salt library functions](https://docs.saltproject.io/en/latest/ref/modules/all/index.html#all-salt-modules).

        {{ salt['pw_user.list_groups']('jdoe') }}

You can execute `salt '*' sys.doc` on the Salt master to display a list of all available functions.

  - **Opts**: The `opts` variable is a dictionary that grants access to the content of a Salt minion's [configuration file](https://docs.saltproject.io/en/latest/ref/internals/opts.html):

        {{ opts['log_file'] }}

The configuration file for a minion is located at `/etc/salt/minion`.

  - **Pillar**: The `pillar` variable is a dictionary used to access Salt's [pillar data](https://docs.saltproject.io/en/latest/topics/tutorials/pillar.html):

        {{ pillar['my_key'] }}

While you can directly access pillar keys and values, it's advisable to utilize Salt's `pillar.get` variable library function. This enables you to specify a default value, which proves handy when a value is absent in the pillar:

        {{ salt['pillar.get']('my_key', 'default_value') }}

- **Grains**: The grains variable is a dictionary that allows access to minions' [grains data](https://docs.saltproject.io/en/latest/topics/grains/):

        {{ grains['shell'] }}

You can utilize Salt's `grains.get` variable library function to retrieve grain data.

        {{ salt['grains.get']('shell') }}

- **Saltenv**: In a Salt master's top file, you can define multiple salt environments for minions, such as `base`, `prod`, `dev`, and `test`. The `saltenv` variable offers a means to access the current Salt environment within a Salt state file. Note that this variable is exclusively available within Salt state files.

        {{ saltenv }}

- **SLS**: Using the `sls` With a variable, you can obtain the reference value for the current state file. (e.g., `apache`, `webserver`, etc.). This value aligns with what's used in a top file to map minions to state files or via the include option in state files:

        {{ sls }}

- **Slspath**: This variable supplies the path to the current state file:

        {{ slspath }}

### Variable Assignments

You can set a value to a variable using the `set` tag, following this delimiter and syntax:

    {% set var_name = myvalue %}

Ensure adherence to [Python naming conventions](https://www.python.org/dev/peps/pep-0008/?#naming-conventions) when naming variables. If a variable is assigned at the top level of a template, it's exported and can be imported by other templates.

Any value generated by a Salt [template variable](#template-variables) library function can be assigned to a new variable.

    {% set username = salt['user.info']('username') %}

### Filters

Filters can be applied to any template variable using a | character. Filters can be chained and may accept optional arguments within parentheses. When chaining filters, the output of one filter serves as input for the next filter.

    {{ '/etc/salt/' | list_files | join('\n') }}

These chained filters will produce a comprehensive list of all files in the `/etc/salt/` directory, with each item separated by a new line.

    {{< output >}}
    /etc/salt/master
    /etc/salt/proxy
    /etc/salt/minion
    /etc/salt/pillar/top.sls
    /etc/salt/pillar/device1.sls
    {{</ output >}}

For a comprehensive list of built-in Jinja filters, consult the [Jinja Template Design documentation](http://jinja.pocoo.org/docs/2.10/templates/#builtin-filters). Additionally, Salt's official documentation provides a [list of custom Jinja filters](https://docs.saltproject.io/en/latest/topics/jinja/index.html#filters).

### Macros

Macros are small, reusable templates designed to reduce repetition in state creation. Create macros within Jinja templates to represent frequently used elements and utilize them across state files for reuse.

    {{< file "/srv/salt/mysql/db_macro.sls" jinja >}}
    {% macro mysql_privs(user, grant=select, database, host=localhost) %}
    {{ user }}_exampledb:
    mysql_grants.present:
    - grant: {{ grant }}
    - database: {{ database }}
    - user: {{user}}
    - host: {{ host }}
    {% endmacro %}
    {{</ file >}}

    {{< file "db_privs.sls" yaml >}}
    {% import "/srv/salt/mysql/db_macro.sls" as db -%}

    db.mysql_privs('jane','exampledb.*','select,insert,update')
    {{</ file >}}

The `mysql_privs()` macro is defined within the `db_macro.sls file`. Afterwards, the template is imported into the `db` variable within the `db_privs.sls` state file, where it's utilized to generate a MySQL grants state for a particular user.

For further details on importing templates and variables, refer to the [Imports and Includes](#imports-and-includes) section.

### Imports and Includes

**Imports**

Importing in Jinja is akin to importing in Python. You have the option to import an entire template, a specific state, or a macro defined within a file.

    {% import '/srv/salt/users.sls' as users %}

For instance, in the given example, the state file `users.sls` is imported into the variable `users`. Subsequently, all states and macros defined within the template become accessible using dot notation.

Moreover, it's possible to import a specific state or macro from a file.

    {% from '/srv/salt/user.sls' import mysql_privs as grants %}

In this import statement, the macro `mysql_privs`, defined within the `user.sls` state file, is targeted. It becomes accessible within the current template through the grants variable.

**Includes**

The `{% include %}` tag incorporates the output of another template at the position where the include tag is placed. When using this tag, the context of the included template is passed to the invoking template.

    {{< file "/srv/salt/webserver/webserver_users.sls" >}}
    include:
    - groups

    {% include 'users.sls' %}
    {{</ file >}}

{{< note respectIndent=false >}}
When referencing a file with the Jinja `include` tag, it must be specified using its [absolute path from Salt's `file_roots` setting](https://github.com/saltstack/salt/issues/15863#issuecomment-57823633); otherwise, using a relative path from the current state file will result in an error. To include a file in the same directory as the current state file:

    {% include slspath + "/users.sls" %}

Additionally, it's important to note that [Salt has its own native `include` declaration](https://docs.saltproject.io/en/latest/ref/states/include.html), which operates independently of Jinja's `include`.
{{< /note >}}

**Import Context Behavior**


By default, an import won't carry the context of the imported template because imports are cached. However, you can override this behavior by including `with context` in your import statements.

    {% from '/srv/salt/user.sls' import mysql_privs with context %}

Similarly, if you wish to exclude the context from an `{% include %}`, simply add `without context`.

    {% include 'users.sls' without context %}

### Whitespace Control

Jinja offers various methods for controlling whitespace in its rendered output. By default, Jinja removes single trailing new lines but retains other whitespace such as tabs, spaces, and multiple new lines. You can adjust how Salt's Jinja template engine manages whitespace in the output [Salt master configuration file](#jinja-basics). Some available environment options for whitespace control include:


- `trim_blocks`: When enabled (set to True), Jinja automatically removes the first newline after a template tag. By default, this is set to False in Salt.
- `lstrip_blocks`: When activated (set to True), Jinja eliminates tabs and spaces from the beginning of a line up to the start of a block. If other characters precede the block's start, nothing is stripped. By default, this is set to False in Salt.
- `keep_trailing_newline`: When turned on (set to True), Jinja retains single trailing newlines. By default, this is set to False in Salt.

To prevent YAML syntax errors, ensure that you consider Jinja's whitespace rendering behavior when inserting templating markup into Salt states. Remember, Jinja must generate valid YAML. When using control structures or macros, it may be necessary to remove whitespace from the template block to produce valid YAML.

To maintain the whitespace of contents within template blocks, you can set both the `trim_blocks` and `lstrip_block` options to True in the master configuration file. Alternatively, you can manually enable and disable the white space environment options within each template `block`. Use - to set `trim_blocks` and `lstrip_blocks` to `False`, and + to set these options to `True` for the block:


For instance, to remove whitespace after the start of a control structure, add a `-` character before the closing `%}`:

    {% for item in [1,2,3,4,5] -%}
        {{ item }}
    {% endfor %}

This will display the numbers `12345` without any leading whitespace. Omitting the `-` character would retain the spacing defined within the block.

### Control Structures

Jinja offers common control structures found in many programming languages, including loops, conditionals, macros, and blocks. Integrating these control structures into Salt states allows for precise control over state execution flow.

**For Loops**

Loops, for example, enable iteration through a list of items, executing the same code or configuration for each item. They effectively reduce repetition within Salt states.

    {{< file "/srv/salt/users.sls" yaml >}}
    {% set groups = ['sudo','wheel', 'admins'] %}
    include:
    - groups

    jane:
    user.present:
    - fullname: Jane Doe
    - shell: /bin/zsh
    - createhome: True
    - home: /home/jane
    - uid: 4001
    - groups:
    {%- for group in groups %}
      - {{ group }}
    {%- endfor -%}
    {{</ file >}}

In the preceding example, the for loop assigns the user `jane` to all the groups in the groups list defined at the top of the `users.sls` file.

**Conditionals**


Conditional expressions evaluate to either `True` or `False` and guide program flow based on the result. Jinja's conditional expressions, prefixed with `if/elif/else`, are enclosed within `{% ... %}` delimiters.

    {{< file "/srv/salt/users.sls" yaml >}}
    {% set users = ['anna','juan','genaro','mirza'] %}
    {% set admin_users = ['genaro','mirza'] %}
    {% set admin_groups = ['sudo','wheel', 'admins'] %}
    {% set org_groups = ['games', 'webserver'] %}

    include:
    - groups

    {% for user in users %}
    {{ user }}:
    user.present:
    - shell: /bin/zsh
    - createhome: True
    - home: /home/{{ user }}
    - groups:
    {% if user in admin_users %}
    {%- for admin_group in admin_groups %}
      - {{ admin_group }}
    {%- endfor -%}
    {% else %}
    {%- for org_group in org_groups %}
      - {{ org_group }}
    {% endfor %}
    {%- endif -%}
    {% endfor %}
    {{</ file >}}

In the given example, if a user is found in the `admin_users` list, the state assigns specific groups for that user. For further guidance on utilizing conditionals and control flow statements within state files, refer to the [Salt Best Practices](#salt-and-jinja-best-practices) section.

### Template Inheritance

Template inheritance allows you to define a base template that can be reused by child templates. Child templates have the ability to override blocks designated by the base template.

Define areas of a base template that can be overridden using the `{% block block_name %}` tag with a block name.

    {{< file "/srv/salt/users.jinja" >}}
    {% block user %}jane{% endblock %}:
    user.present:
    - fullname: {% block fullname %}{% endblock %}
    - shell: /bin/zsh
    - createhome: True
    - home: /home/{% block home_dir %}
    - uid: 4000
    - groups:
    - sudo
    {{</ file >}}

In this scenario, a base user state template is created. Any value containing a `{% block %}` tag can be overridden by a child template with its own value.

To utilize a base template within a child template, employ the `{% extends "base.sls"%}` tag along with the location of the base template file.

    {{< file "/srv/salt/webserver_users.sls" yaml >}}
    {% extends "/srv/salt/users.jinja" %}

    {% block fullname %}{{ salt['pillar.get']('jane:fullname', '') }}{% endblock %}
    {% block home_dir %}{{ salt['pillar.get']('jane:home_dir', 'jane') }}{% endblock %}
    {{</ file >}}

In the `webserver_users.sls` state file, the `users.jinja` template is extended, and values for the `fullname` and `home_dir` blocks are defined. These values are generated using the [`salt` context variable](#template-variables) and pillar data. The remainder of the state will be rendered as defined in the parent `user.jinja` template.

## Salt and Jinja Best Practices

While Jinja offers significant power and flexibility, excessive use can lead to unmaintainable Salt state files that are challenging to comprehend. Here are some best practices to ensure effective Jinja usage:

- Limit the use of Jinja within state files. It's preferable to separate data from the states utilizing it. This separation enables updates to data without requiring modifications to states.
- Avoid excessive use of conditionals and loops within state files. Overuse can lead to difficulty in understanding and maintaining states.
- Utilize dictionaries of variables and serialize them directly into YAML instead of attempting to create valid YAML within a template. You can incorporate your logic within the dictionary and retrieve the necessary variable within your states.

The `{% load_yaml %}` tag is used to deserialize strings and variables provided to it.

         {% load_yaml as example_yaml %}
             user: jane
             firstname: Jane
             lastname: Doe
         {% endload %}

         {{ example_yaml.user }}:
            user.present:
              - fullname: {{ example_yaml.firstname }} {{ example_yaml.lastname }}
              - shell: /bin/zsh
              - createhome: True
              - home: /home/{{ example_yaml.user }}
              - uid: 4001
              - groups:
                - games

Employ `{% import_yaml %}` to bring in external data files, making the data accessible as a Jinja variable.

         {% import_yaml "users.yml" as users %}

- Utilize Salt [Pillars](https://docs.saltproject.io/en/latest/topics/tutorials/pillar.html) for storing general or sensitive data as variables. Access these variables within state files and template files.
