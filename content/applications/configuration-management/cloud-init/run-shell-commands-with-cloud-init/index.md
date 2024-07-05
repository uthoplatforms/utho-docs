---
slug: "Shell Your Way to Success: Unleash Commands with Cloud Init"
title: "Power Up Your Boot: Maximize Cloud-Init to Execute Commands and Bash Scripts"
description: "Unlock the Secrets of Cloud-Init: Learn How to Execute Shell Commands and Bash Scripts Upon Server Boot-Up in this Step-by-Step Guide"
keywords: ['cloud-init','cloudinit','bash','shell script']
authors: ["Pawan Kumar"]
published: 2023-11-15
modified_by:
  name: Pawan Kumar
---

[Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) simplifies the process of setting up new servers across different platforms. It taps into your cloud provider's metadata and allows you to customize server setup with user-defined scripts.

This guide demonstrates how to utilize cloud-init to execute shell commands during server deployment. Whether you need to run a single command or a comprehensive Bash script, cloud-init automates the process upon the server's initial boot.

To begin, refer to our comprehensive guide on Using [Use Cloud-Init to Automatically Configure and Secure Your Servers](/docs/guides/configure-and-secure-servers-with-cloud-init/). for creating a cloud-config file, a prerequisite for following this guide. Once your cloud-config is ready, simply follow the instructions provided in the linked guide to deploy it effectively.

## Execute Commands with the `runcmd` Directive

The `runcmd` option is your go-to tool for running shell commands in cloud-config. With runcmd, you can specify a list of commands to be executed one after the other during server initialization.

You can provide commands to `runcmd` in two formats: as a string or as a list. When entered as a string, the command is written just as you would type it into the shell. If entered as a list, the first item is the command itself, and subsequent items are options for that command, following the same pattern as [execve(3)](https://linux.die.net/man/3/execve).

For example, below is a demonstration using both approaches within a `runcmd`. Each command performs a similar action, appending a line to `/run/test.txt`.

```file {title="cloud-config.yaml" lang="yaml"}
runcmd:
  - echo 'First command executed successfully!' >> /run/testing.txt
  - [ sh, -c, "echo 'Second command executed successfully!' >> /run/testing.txt" ]
```

Note: Cloud-init advises avoiding writing files to the `/tmp/` directory in your cloud-config because it's often cleared during boot processes. Instead, it suggests utilizing the `/run/` directory for temporary files, as demonstrated in the example above and in subsequent examples.
{{< /note >}}

## Run Commands with `bootcmd` Directive

Cloud-init provides another option for executing commands called `bootcmd`. Similar to runcmd, you specify a list of commands within the cloud-config file, which can be either strings or lists themselves.

However, `bootcmd` has two distinct features. Firstly, commands specified in bootcmd are executed early in the boot process, among the initial tasks when the system boots up. Secondly, these commands run every time the system boots. Unlike `runcmd`, which runs only during initialization, `bootcmd` commands become a permanent part of your system's boot process, repeating with each boot.

To utilize bootcmd, the setup within the cloud-config file only differs in the option name. Here's an example:

```file {title="cloud-config.yaml" lang="yaml"}
bootcmd:
  - echo 'Boot command executed successfully!'
```

If you require running a command early in the boot process without it executing with each boot, you can still utilize `bootcmd`. You can achieve this by using cloud-init's `cloud-init-per` utility, which allows you to define the execution frequency.

In the following example, an `echo` command is executed similar to before, but with `cloud-init-per` specifying that the command should only run during instance initialization (`instance`). The parameter `exampleinstanceecho` labels the command, followed by the actual command.

```file {title="cloud-config.yaml" lang="yaml"}
bootcmd:
  - [ cloud-init-per, instance, example-instance-echo, echo, "Instance initialization command executed successfully!" ]
```

## Run a Bash Script

Beyond executing individual commands, cloud-init's runcmd feature can also handle the execution of entire shell scripts. To do this, you need to transfer the script to the new server and then execute it using a shell command through runcmd.

If your script is hosted remotely and accessible, the simplest approach is to use a wget command to download it. Then, you can employ a runcmd to run the script. Object Storage can serve as a reliable option for hosting script files.

However, in many cases, it's preferable to include the shell script directly as part of the cloud-init initialization, rather than hosting it elsewhere. In such scenarios, you can utilize cloud-init's write_files option to create the script file during initialization. To delve deeper into writing files with cloud-init, refer to [Use Cloud-Init to Write to a File](/docs/guides/write-files-with-cloud-init/) which covers all the write_files options mentioned here.

cloud-config to generate and execute a shell script. `write_files` creates a straightforward script file at `/run/scripts/test-script.sh` and assigns it executable permissions. The script content is executed with Bash, appending a line to the `/run/testing.txt` file. Finally, `runcmd` executes the test-script.sh file as a command. This example utilizes the `sh` command to run the shell script:

```file {title="cloud-config.yaml" lang="yaml"}
write_files:
  - path: /run/scripts/test-script.sh
    content: |
      #!/bin/bash

      echo 'Script executed successfully!' >> /run/testing.txt
    permissions: '0755'

runcmd:
  - [ sh, "/run/scripts/test-script.sh" ]
```

## Ensuring Execution: Confirming Command or Script Completion

Once your instance is deployed using cloud-init, it's essential to confirm that the commands have executed successfully. The method for verification depends on the commands' nature. However, since many commands generate shell output by default, the most reliable approach is to review the output in the cloud-init log file, typically found at `/var/log/cloud-init-output.log`.

For example, the `bootcmd` commands mentioned earlier each utilize `echo` to produce output in the terminal. Therefore, you can validate these commands by inspecting the cloud-init logs for the corresponding output. To simplify the search process, you can use tools like `grep` to filter the logs and locate lines containing the expected output text from the commands.

```command
sudo cat /var/log/cloud-init-output.log | grep 'executed successfully!'
```

```output
Boot command executed successfully!
Instance initialization command executed successfully!
```

Many command-line tools provide output directly to the terminal, simplifying verification through cloud-init logs. However, some commands generate output to separate log files. Alternatively, you can log actions using `echo` commands alongside your primary commands. For instance, the other commands executed via `runcmd` in the examples above output text to a `/run/testing.txt` file.

This scenario may apply to other commands executed via cloud-init as well. The command you're using might automatically generate logs, or you might choose to integrate logging manually, as demonstrated in the example commands. Regardless, reviewing the content of the relevant log file can confirm cloud-init's successful execution of the commands:

```command
sudo cat /run/testing.txt
```

```output
First command executed successfully!
Second command executed successfully!
Script executed successfully!
```
