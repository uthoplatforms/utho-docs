---
slug: "Master Your Tasks: Setting Up Taskwarrior on Ubuntu 16.10"
deprecated: true
description: 'Effortlessly Manage Tasks: Installing and Configuring Taskwarrior on Ubuntu 16.10'
keywords: ["Install Taskwarrior", "Taskwarrior on Ubuntu", "Taskwarrior server"]
tags: ["ubuntu"]
modified: 2024-05-14
modified_by:
  name: Pawan Kumar
published: 2024-05-14
title: 'Install Taskwarrior on Ubuntu 16.10'
authors: ["Pawan Kumar"]
---

[Taskwarrior](https://taskwarrior.org/) is a powerful open-source command-line tool designed for efficient task management. Known for its speed and versatility, Taskwarrior is written in C, regularly updated, and compatible with virtually every platform. This guide walks you through the process of installing Taskwarrior on a Utho running Ubuntu 16.10.

## Before You Begin

If you haven't already, create a Utho account and set up a Compute Instance using our Getting Started with Utho and Creating a Compute Instance guides.

Follow our Setting Up and Securing a Compute Instance guide to ensure your system is up-to-date. You may also want to adjust the timezone, configure your hostname, create a limited user account, and enhance SSH access security.

## Install Taskwarrior

Install Taskwarrior using the command:

    sudo apt install task

Once the installation is complete, execute the command task to initiate Taskwarrior.

The system will prompt you to create a configuration file for your user.

Answer `yes`.

You'll find the sample configuration file at `~/.taskrc`. For detailed information on configuring `task.rc`, refer to the [official documentation](https://taskwarrior.org/docs/configuration.html).

## Manage Tasks with Taskwarrior

Taskwarrior offers a straightforward set of commands to manage your tasks efficiently. It's recommended to prioritize your tasks manually before automating them with the following commands.

### Add a Task

To add a task, simply execute the command: [task add](https://taskwarrior.org/docs/commands/add.html).

For example:

    task add Add block storage volume to my Utho

will return:

    Created task 1.

Upon running `task` again, you'll receive job information. Taskwarrior assigns a unique ID to each newly added task and keeps track of the elapsed time since the command was executed.

    taskwarrior@localhost:~$ task
    [task next]

     ID Age Description                             Urg
     1 14s Add block storage volume to my Utho    0

     1 task

Feel free to add as many tasks as you need.

    task add Attend Linux Users Group
    Created task 2.

    task add buy groceries
    Created task 3.

### Complete a Task

Once a task is completed, mark it as "done" using the done command (https://taskwarrior.org/docs/commands/done.html). Simply use the syntax `task <task_number> done`.

    taskwarrior@localhost:~$ task 1 done
    Completed task 1 'Add block storage volume to my Utho'.
    Completed 1 task.

### Remove a Task

To delete a task, execute the command `task <task_number> delete`.

    taskwarrior@localhost:~$ task 2 delete
    Permanently delete task 2 'Attend Linux Users group'? (yes/no) yes
    Deleting task 2 'Attend Linux Users group'.
    Deleted 1 task.

### Assign Tasks a Due Date

Utilize the `due` argument to assign a due date for a task:

    task add write Taskwarrior guide for the Utho community due:tomorrow

    taskwarrior@localhost:~$ task
    [task next]

    ID Age   Due Description                                      Urg
      2 11s    7h write Taskwarrior guide for the Utho community 8.65
      1 16min     buy groceries                                       0

    2 tasks

The `due` [argument](https://taskwarrior.org/docs/dates.html#due)  offers extensive flexibility in input. Explore more about its possibilities in [the official documentation.](https://taskwarrior.org/docs/dates.html).

Taskwarrior supports r[recurring tasks](https://taskwarrior.org/docs/recurrence.html) through the `recur` argument. The example below demonstrates creating a daily task, with the first occurrence due 23 hours from creation time:

    task add update ubuntu recur:daily due:daily

    taskwarrior@localhost:~$ task
    [task next]

    ID Age   Recur Due Description                                      Urg
     2 11min        7h write Taskwarrior guide for the Utho community 8.65
     4         P1D 23h update ubuntu                                    8.34
     1 28min           buy groceries                                       0

    3 tasks
    Creating recurring task instance 'update ubuntu'

### Visualization

Taskwarrior extends beyond simple task listing on the command line. The [burndown](https://taskwarrior.org/docs/commands/burndown.html feature generates graphical representations of your Taskwarrior workflow.

The `calendar` feature presents a calendar showcasing all your tasks and their due dates.

### Next Steps with Taskwarrior

To further integrate Taskwarrior into your routine, consider installing task server on your Utho. Since Taskwarrior can synchronize across all your devices, including your phone, a central server for data syncing is essential. Taskwarrior provides DIY documentation for setting up such a task server: [Installing Taskserver](https://taskwarrior.org/docs/taskserver/setup.html).
