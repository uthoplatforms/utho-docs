---
slug: mastering-secrets-with-salt
description: 'Unlock the full potential of SaltStack for secret management with this comprehensive guide'
keywords: ['salt','saltstack','secret','secure','management','sdb','gpg','vault']
tags: ["security","automation","salt"]
license: '[CC BY-ND 4.0](https://creativecommons.org/licenses/by-nd/4.0)'
published: 2024-04-02
modified: 2024-04-02
modified_by:
  name: Utho
title: "Secrets Management with Salt"
authors: ["Utho"]
---
Salt is a robust tool for managing server configurations using state files. These files, which define server setups, can be easily shared and versioned with tools like Git.

However, managing sensitive data like API keys and passwords in these state files can pose security risks, especially if they're plain-text and stored in version control. This guide explores ways to securely handle secrets in Salt.

## Salt Pillar
One common approach is to use Salt's Pillar feature. [*Pillar*](https://docs.saltproject.io/en/latest/topics/pillar/) stores secrets and other variables on the Salt master and distributes them to specific minions. By keeping secrets separate from state files and storing them in Pillar, you can avoid exposing them in version control.

{{< note respectIndent=false >}}
Salt Pillar isn't just for secrets; it can also manage non-sensitive data like package versions for your minions. This means you might still want to track some Pillar files in version control.

To keep sensitive data separate, you can create a special directory at `/srv/pillar/secrets` and tell your version control system to ignore it (for Git, add it to your `.gitignore` file). Put all your sensitive information in Pillar files within this directory, while keeping non-sensitive data in Pillar files elsewhere, like in `/srv/pillar` or another subfolder.
{{< /note >}}

### Anatomy of Pillar Data Files
Pillar data is stored in `.sls` files, which use the same YAML syntax as states. Typically, these files are located within `/srv/pillar` on the Salt master, but you can configure this location using the `pillar_roots` option in your master's configuration.

For instance, suppose your minion operates an application that interacts with the Utho API. In this case, the following example pillar file stores your API token in a variable named `utho_api_token`:

    {{< file "/srv/pillar/app_secrets.sls" >}}
    utho_api_token: YOUR_API_TOKEN
    {{< /file >}}

Similar to state files, a top file (distinct from your states’ top file) links pillar data to minions. In this example, the top file associates your `app_secrets` pillar data with your application server:

    {{< file "/srv/pillar/top.sls" >}}
    base:
    'appserver':
    - app_secrets
    {{< /file >}}

{{< note respectIndent=false >}}
You might consider creating a `pillar.example` file, similar to those often provided by Salt formulas. This file can list all the known variable keys for your pillar but without containing the actual secrets. By including this file in your version control, other users who clone your states' repository can easily replicate this example pillar file and set up their own deployments more quickly.
{{< /note >}}

### Accessing Pillar Data inside Salt States

To incorporate pillar data into your states, utilize Salt's Jinja template syntax. Although Salt employs the YAML syntax for state and pillar files, these files are initially interpreted as Jinja templates (by default).

Here's an example state that embeds the API token in a file on your Utho; the data is accessed through the `pillar` dictionary:

    {{< file "/srv/salt/setup_app.sls" >}}
    api_token:
    file.managed:
    - name: /var/your_app/api_token
    - contents: {{ pillar['utho_api_token'] }}
    {{< /file >}}

{{< note type="alert" respectIndent=false >}}
At times, pillar data might appear in Salt's output, such as when `file.managed` shows diffs of a modified file. To prevent these diffs from being displayed, you can set the `show_diff` flag of `file.managed` to false.
{{< /note >}}

### Passing Pillar Data at the Command Line

Additionally, you can provide pillar values as a dictionary via the command line, and these values will take precedence over any values set in your pillar files. For instance, this command would apply the `A_DIFFERENT_API_TOKEN` value instead of the original `YOUR_API_TOKEN` from the previous example:

    salt '*' state.apply pillar='{"utho_api_token": "A_DIFFERENT_API_TOKEN"}'

## Environment Variables

Yet another approach to safeguard sensitive values from version control is by utilizing environment variables. The process of passing environment variables to your states mirrors how pillar data can be passed via the command line. The environment variable precedes your salt command, as illustrated in this example:

    LINODE_API_TOKEN="YOUR_API_TOKEN" salt 'appserver' state.apply setup_app

To reference the environment variable within a Salt state file, you can use the syntax `salt['environ.get']('ENVIRONMENT_VARIABLE_NAME')`. For instance, the previous `setup_app` example state can be adjusted to incorporate an environment variable like so:

    {{< file "/srv/salt/setup_app.sls" >}}
    api_token:
    file.managed:
    - name: /var/your_app/api_token
    - contents: {{ salt['environ.get']('LINODE_API_TOKEN') }}
    {{< /file >}}

Similar to the previous example with pillar data, it's advisable to prevent `file.managed` from displaying its diffs on screen when handling sensitive information. You can achieve this by setting `show_diff: false`. For more details, refer to [Using Environment Variables in SLS Modules](https://docs.saltproject.io/en/latest/topics/tutorials/states_pt3.html#using-environment-variables-in-sls-modules).

## GPG Encryption
You can utilize Salt's [GPG renderer](https://docs.saltproject.io/en/latest/ref/renderers/all/salt.renderers.gpg.html) to decrypt GPG ciphers within your pillar files. This decryption process occurs before your pillar data is transmitted to your minions. Consequently, you can encrypt any value in a pillar file, enabling secure storage of your pillar files in version control.

To implement this method, ensure that the GPG secret key is available on your Salt master. Additionally, it's recommended to include the public key in version control, allowing your team members to encrypt new data for your pillar files.

## SDB
Salt includes a database interface known as SDB, initially designed to store non-minion-specific data like passwords. It connects to modules such as Salt's [*keyring*](https://docs.saltproject.io/en/latest/ref/sdb/all/salt.sdb.keyring_db.html), but other options like [Consul](https://docs.saltproject.io/en/latest/ref/sdb/all/salt.sdb.consul.html) and [Vault](https://docs.saltproject.io/en/latest/ref/sdb/all/salt.sdb.vault.html#module-salt.sdb.vault). are also available.

Configuration profiles for these databases are set up in `/srv/salt/master.d`. To access data, you use an `sdb://` URL, such as `password: sdb://mysecrets/mypassword`. For more details on SDB, refer to the S[Salt SDB documentation](https://docs.saltproject.io/en/latest/topics/sdb/).

{{< note respectIndent=false >}}
Salt also provides a module that allows [pillar data to be stored in Vault](https://docs.saltproject.io/en/latest/ref/pillar/all/salt.pillar.vault.html), as well as an execution module that includes [functions to interact with Vault](https://docs.saltproject.io/en/latest/ref/modules/all/salt.modules.vault.html#vault-setup).

Salt also offers a module to store [pillar data to be stored in Vault](https://docs.saltproject.io/en/latest/ref/pillar/all/salt.pillar.vault.html), along with an execution module containing [functions to interact with Vault](https://docs.saltproject.io/en/latest/ref/modules/all/salt.modules.vault.html#vault-setup).
{{< /note >}}
