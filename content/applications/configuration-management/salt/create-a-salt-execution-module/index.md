---
slug: "Boost Your Game: Crafting a Salt Execution Module"
description: 'This guide walks you through creating a Salt execution module, a Python tool that works on a Salt minion. Follow simple steps to get it done '
keywords: ['salt','execution module','saltstack']
published: 2024-04-01
modified: 2024-04-01
modified_by:
  name: Utho
title: "Crafting Your Salt Execution Module"
tags: ["automation","salt"]
authors: ["Utho"]
---
A Salt execution module is a Python tool that runs on a Salt minion. It handles tasks and sends back data to the Salt master. In this tutorial, you'll learn how to create and install an execution module. This module will access the [US National Weather Service API](https://forecast-v3.weather.gov/documentation) and retrieve the current temperature from a specific weather station. You can adapt this example to access other APIs easily.

## Before You Begin

If you haven't already, set up a Salt master and at least one Salt minion. You can follow the first few steps of our [Getting Started with Salt - Basic Installation and Setup](/docs/guides/getting-started-with-salt-basic-installation-and-setup/) guide.

Before you begin, make sure you have set up a Salt master and at least one Salt minion. You can follow the initial steps of our [Getting Started with Salt - Basic Installation and Setup](/docs/guides/getting-started-with-salt-basic-installation-and-setup/) guide.

{{< note respectIndent=false >}}
Please note that the steps in this guide require root privileges. You should run the commands with the `sudo` prefix. For more details on privileges, refer to our [Users and Groups](/docs/guides/linux-users-and-groups/) guide.
{{< /note >}}

## Prepare Salt

The files you'll create in the following steps will be located in the `/srv/salt` directory. If you've modified Salt's default [`file_roots`](https://docs.saltproject.io/en/latest/ref/configuration/master.html#std:conf_master-file_roots) configuration, use that directory instead.

1. First, create the `/srv/salt` directory if it doesn't exist already. This is where you'll put your top file and your Salt state file.

        mkdir /srv/salt

1.  To start configuring Salt, create a top file in the `/srv/salt` directory. This file will be Salt's entry point for our configuration.
        {{< file "/srv/salt/top.sls" yaml >}}
        base:
        '*':
        - weather
        {{< /file >}}

1. Next, make a state file called `weather.sls`. Instruct Salt to ensure that our minions have PIP installed and the necessary Python library.

       {{< file "/srv/salt/weather.sls" yaml >}}
       python-pip:
       pkg.installed

       requests:
       pip.installed:
       - require:
       - pkg: python-pip
       {{< /file>}}

1.  Execute these state changes:

        salt '*' state.apply

1.  Finally, create the `/srv/salt/_modules` directory which will contain our execution module:

        mkdir /srv/salt/_modules

## Create the Execution Module

1. Generate a file named `weather.py` within the `/srv/salt/_modules` directory. Then, include the subsequent lines to configure Salt logging and import the requests module.

       {{< file "/srv/salt/_modules/weather.py" python >}}
       import logging
       try:
       import requests
       HAS_REQUESTS = True
       except ImportError:
       HAS_REQUESTS = False

       log = logging.getLogger(__name__)

       . . .
       {{< /file >}}

1. Integrate the `__virtualname__` variable and the `__virtual__` function.

       {{< file "/srv/salt/_modules/weather.py" python>}}
       . . .

       __virtualname__ = 'weather'

       def __virtual__():
       '''
       Only load weather if requests is available
       '''
       if HAS_REQUESTS:
       return __virtualname__
       else:
       return False, 'The weather module cannot be loaded: requests package unavailable.'

       . . .
       {{< /file >}}

The `__virtual__` function either delivers the module's virtual name and loads it, or returns False alongside an error message, indicating that the module is not loaded. The `if HAS_REQUESTS` conditional is associated with the try/except block established in the previous step via the `HAS_REQUESTS` variable.

1. Incorporate the public `get()` function along with the private `_make_request()` function.

        {{< file "/srv/salt/_modules/weather.py" python >}}
        . . .

        def get(signs=None):
        '''
        Gets the Current Weather

        CLI Example::

        salt minion weather.get KPHL

        This module also accepts multiple values in a comma separated list::

        salt minion weather.get KPHL,KACY
        '''
        log.debug(signs)
        return_value = {}
        signs = signs.split(',')
        for sign in signs:
        return_value[sign] = _make_request(sign)
        return return_value

        def _make_request(sign):
        '''
        The function that makes the request for weather data from the National Weather Service.
        '''
        request = requests.get('https://api.weather.gov/stations/{}/observations/current'.format(sign))
        conditions = {
        "description:": request.json()["properties"]["textDescription"],
        "temperature": round(request.json()["properties"]["temperature"]["value"], 1)
        }
        return conditions
        {{< /file >}}

The get() function accepts one or more weather station call signs as a comma-separated list. It utilizes the `_make_request()` function to make the HTTP request and then returns a text description of the current weather and the temperature.

The `_make_request()` function is designated as private by adding an underscore at the beginning. This means it's not directly accessible through the Salt command line or a state file.

Here's the complete file:

    {{< file "/srv/salt/_modules/weather.py" python >}}
    import logging
    try:
    import requests
    HAS_REQUESTS = True
    except ImportError:
    HAS_REQUESTS = False

    log = logging.getLogger(__name__)

    __virtual_name__ = 'weather'

    def __virtual__():
    '''
    Only load weather if requests is available
    '''
    if HAS_REQUESTS:
        return __virtual_name__
    else:
    return False, 'The weather module cannot be loaded: requests package unavailable.'

    def get(signs=None):
    '''
    Gets the Current Weather

    CLI Example::

    salt minion weather.get KPHL

    This module also accepts multiple values in a comma seperated list::

    salt minion weather.get KPHL,KACY
    '''
    log.debug(signs)
    return_value = {}
    signs = signs.split(',')
    for sign in signs:
    return_value[sign] = _make_request(sign)
    return return_value

    def _make_request(sign):
    '''
    The function that makes the request for weather data from the National Weather Service.
    '''
    request = requests.get('https://api.weather.gov/stations/{}/observations/current'.format(sign))
    conditions = {
    "description:": request.json()["properties"]["textDescription"],
    "temperature": round(request.json()["properties"]["temperature"]["value"], 1)
    }
    return conditions
    {{< /file >}}

## Run the Execution Module

1. To execute the execution module, you'll need to sync it with your minions first. You can achieve this by invoking a highstate with `state.apply`, which will also attempt to apply the state changes specified earlier in the weather.sls state file. Since the `weather.sls` state was already applied in the [Preparing Salt](#preparing-salt) section, utilize the `saltutil.sync_modules` function:

        salt '*' saltutil.sync_modules

1. Run the execution module on your Salt master:

        salt '*' weather.get KPHL

You should observe an output similar to the following:      

    {{< output >}}
    salt-minion:
    ----------
    KPHL:
    ----------
    description::
    Cloudy
    temperature:
    17.2
    {{< /output >}}

1. Alternatively, you can execute the Salt execution module locally on your Salt minion by typing:

        salt-call weather.get KVAY,KACY

You should receive output similar to this:        

    {{< output >}}
    local:
    ----------
    KACY:
    ----------
    description::
    Cloudy
    temperature:
    18.9
    KVAY:
    ----------
    description::
    Cloudy
    temperature:
    16.7
    {{< /output >}}

Congratulations! You've successfully created and installed a Salt execution module.
