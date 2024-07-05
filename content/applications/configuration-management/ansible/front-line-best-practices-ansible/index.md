---
slug: "Mastering Ansible: Top Tactics for Frontline Success"
title: "Best Practices for Ansible"
title_meta: "Top 12 Ansible Best Practices"
description: 'Discover Ansible best practices and proven techniques for project organization, playbook content, documentation, testing, validation, and security.'
keywords: ['ansible best practices','ansible documentation','ansible testing','ansible playbook']
authors: ["Pawan Kumar"]
published: 2024-05-022
modified_by:
  name: Utho
---
[Ansible](/docs/guides/applications/configuration-management/ansible/) stands as a pivotal open-source automation tool and platform, serving multifaceted roles in configuration management, application deployment, task automation, and the [orchestration](https://www.databricks.com/glossary/orchestration) of intricate workflows.

Its significance in DevOps is undeniable. Empowering both IT administrators and developers, Ansible facilitates the automation of repetitive tasks while optimizing the management and deployment of infrastructure, applications, and services. Noteworthy aspects of Ansible encompass its business and strategic functionalities, contributing to enhanced operational efficiency and streamlined workflows.

- [**Agentless Architecture**](https://www.ansible.com/hubfs/pdfs/Benefits-of-Agentless-WhitePaper.pdf) Operates without the need for agent installations.
- [**Idempotency**](https://docs.ansible.com/ansible/latest/reference_appendices/glossary.html#term-Idempotency) ensures secure and dependable outcomes even with unreliable components.
- [**Portability**](https://www.ansible.com/blog/ansible-and-containers-why-and-how#:~:text=*%20Ansible%20playbooks%20are%20portable.&text=If%20you%20build%20a%20container%20with%20an%20Ansible%20playbook%2C%20you,choice%2C%20or%20on%20bare%20metal.): Operates consistently across different operating systems and into various flavors of cloud environments.

Data centers are essential environments where Ansible, or its counterparts, become indispensable. Enterprises operating at data center scale demand reliability, cost-effectiveness, scalability, and flexibility - precisely the strengths of Ansible. It enhances data center operations, rendering them more cost-effective, predictable, resilient, and agile.

## Ansible Fundamentals
Below is a compilation of essential terms encompassing the core [fundamental components and concepts associated with Ansible](https://docs.ansible.com/ansible/latest/getting_started/basic_concepts.html): and principles linked with Ansible:

_ **Target State**: Ansible operates with a [**declarative**](http://www.it-automation.com/2021/06/05/is-ansible-declarative-or-imperative.html) approach, defining target states for computing systems and managing their achievement. This fosters collaboration between users and Ansible, where users specify desired outcomes and Ansible executes the necessary actions to realize them, a departure from traditional [system administration](/docs/guides/linux-system-administration-basics/) methods.

An essential consideration regarding target state is its application scope. While many practitioners primarily associate Ansible with provisioning and deployment tasks, its utility extends to various other automation scenarios. While adept at initiating new server instances or updating existing ones, Ansible proves invaluable for a myriad of functions that contribute to overall system health. For instance, it efficiently conducts daily audits of certificate expirations or hourly checks to ensure file systems maintain a minimum of 10% free storage. Implementing such target states and validations typically requires only a few lines of Ansible code, showcasing its versatility and effectiveness beyond traditional deployment tasks.

- **Playbooks**: Written in YAML, Ansible [playbooks](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_intro.html) are written in [YAML](/docs/guides/yaml-reference/) outline a series of steps, or "plays," to execute on one or more target systems. Playbooks articulate desired states for systems and delegate the responsibility of achieving those states to Ansible.

-   **Modules**: Ansible [modules](https://docs.ansible.com/ansible/latest/dev_guide/developing_modules_general.html) are the building blocks of playbooks. Modules are discrete units of code that enact specific tasks such as package management, file configuration, or launching services. One of Ansible's great assets is its enormous collection of built–in modules and the ability for users to author custom ones.

- **Modules**: [modules](https://docs.ansible.com/ansible/latest/dev_guide/developing_modules_general.html)  serve as the fundamental components of playbooks, comprising discrete units of code tasked with executing specific actions, such as package management or service deployment. Ansible boasts an extensive library of built-in modules and supports custom module development.

- **Tasks**: [tasks](https://docs.ansible.com/ansible/latest/getting_started/basic_concepts.html#tasks) represent individual actions within a playbook, leveraging modules to perform specific tasks sequentially on target systems to achieve desired states.

- **Roles**: Ansible [roles](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse_roles.html) organize related playbooks, variables, tasks, and other components into reusable units, fostering modularity and promoting code reuse across projects.

- **Inventory**: The[inventory](https://docs.ansible.com/ansible/latest/getting_started/basic_concepts.html#inventory)  file delineates the hosts and systems under Ansible's purview, encompassing details such as hostnames, IP addresses, groups, and variables, and can be static or dynamically generated.

- **Variables**: Ansible facilitates the use of [variables](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_variables.html) to imbue playbooks with dynamism and flexibility, supporting various scopes including global, playbook, role, and task.

- **Facts**: Ansible collects system information using [facts](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_vars_facts.html), gathered by specialized modules during playbook execution, informing decision-making processes.

- **Templates**: Utilizing [Jinja2 syntax](/docs/guides/introduction-to-jinja-templates-for-salt/) syntax, Ansible [templates](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/template_module.html) are structured files with placeholders dynamically populated during playbook execution, commonly employed to generate configuration files and scripts.

- **Handlers**: [handlers](https://docs.ansible.com/ansible/latest/getting_started/basic_concepts.html#handlers) are triggered by specific events, typically post-playbook execution, and are responsible for tasks such as service restarts following configuration changes.

- **Ad-hoc Commands**: While primarily declarative, Ansible integrates imperative mechanisms like [ad–hoc commands](https://docs.ansible.com/ansible/latest/command_guide/intro_adhoc.html) commands for streamlined system health checks, troubleshooting, and isolated remedies.

## Ansible Best Practices

While optimizing runtime efficiency is crucial, implementing best practices also enhances organizational effectiveness. These practices foster collaboration, ensure dependable outcomes with minimal effort, streamline onboarding processes, alleviate maintenance burdens, and mitigate legal risks.

Echoing the sentiment of Abelson and Sussman, who emphasized the importance of code readability for humans, the finest Ansible playbooks serve as invaluable assets for their human readers. Just as software should be comprehensible to programmers, Ansible playbooks should prioritize clarity and accessibility.

Furthermore, it's imperative to recognize that Ansible playbooks and related specifications constitute source [code](https://www.cloudbees.com/blog/configuration-as-code-everything-need-know#). Thus, they warrant the same level of care and attention as any other codebase, including integration into a [version-controlled source code control system](/docs/guides/introduction-to-version-control/) source code management system. This foundational principle, akin to 'best practice zero,' sets the stage for the subsequent top 12 best practices for utilizing Ansible."

### File System Layout

Structure projects using a uniform file system layout. Distinguish playbooks from roles by placing them in separate directories named `playbooks` and `roles`, respectively. The resulting project directory structure should resemble the following example:

```
project/
├── playbooks/
│   └── example_playbook.yml
├── roles/
│   └── example_role/
│       ├── tasks/
│       ├── handlers/
│       ├── templates/
│       └── …
│── ...
├── group_vars/
│   ├── all.yml
│   ├── production.yml
│   └── development.yml
│── ...
├── inventory/
│   ├── production_hosts
│   ├── staging_hosts
│   └── development_hosts
│── ...
├── vault/
│   ├── secret_file.yml
│   └── …
└── ...
```

The spelling of file names and other formal aspects within an Ansible project are primarily cosmetic and do not constitute actual Ansible programming. Therefore, it's advisable to adhere to established common practices, allowing your team to focus on more substantial matters. Collaboration is key in the realm of IT.

This best practice isn't solely about the aesthetics of directory names like `playbooks` versus `playbook`. Rather, it emphasizes the importance of establishing a shared, [common language for the whole team](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3575067/#:~:text=A%20shared%2C%20common%20language%20provides%20a%20focus%20for%20all%20stakeholders.&text=It%20is%20most%20effective%20when,help%20to%20decrease%20project%20costs.). By adopting these best practices, teams can redirect their attention away from [worrying about particular details](https://americanexpress.io/yak-shaving/) and concentrate on collaborating effectively towards overarching business objectives.

### Ansible Configuration

Utilize `ansible.cfg` for global configuration settings. Establish reasonable defaults for inventory and roles paths, and employ syntactic comments to clarify the rationale behind these selections.

Explicitly define `forks` to regulate parallelism and configure `pipelining` to streamline `ssh` operations for enhanced performance. Implement `ControlPath` configuration to facilitate the sharing of ssh connections. Adjust `timeout` and `poll_interval` settings to effectively manage the timeout duration of long-running tasks.

Tailor the verbosity level of logging based on practical experience and performance evaluations of specific playbooks in use. Regularly review the configuration to ensure alignment with established policies and objectives.

### Playbook Design and Structure

Utilize Roles to compartmentalize playbooks, drawing inspiration from [Ansible Galaxy](https://galaxy.ansible.com/ui/) for role definitions. Customize your choices in `requirements.yml` accordingly.

Consider breaking down extensive and intricate playbooks into smaller, focused ones, each dedicated to a specific component or task. This segmentation typically enhances manageability compared to handling a single comprehensive playbook. Alternatively, employ [tags](https://galaxy.ansible.com/ui/) to organize complex playbooks by enabling or disabling specific sections as needed.

Organize inventory, configuration, and variable details into separate files tailored to specific environments.

Adopt a Vault to safeguard *all* sensitive or confidential information. This encompasses passwords, certificates, tokens, keys, or any other client-specific data that Ansible requires. Alternatively, store sensitive data in external files and reference them in Ansible rather than embedding them directly.

Regularly assess and streamline playbook structures to ensure they remain relevant and aligned with project needs.

### Variable Names

Select clear and descriptive variable names, such as `gateway` instead of `gw`. However, prioritize brevity over complexity when naming variables.

Use [snake case](https://www.freecodecamp.org/news/snake-case-vs-camel-case-vs-pascal-case-vs-kebab-case-whats-the-difference/#:~:text=Snake%20case%20separates%20each%20word,letters%20need%20to%20be%20lowercase.&text=Snake%20case%20is%20used%20for%20creating%20variable%20and%20method%20names.), For instance, opt for `database_account` instead of `DatabaseAccount` or any other variations.

Provide comments documenting the purpose, usage, and scope of variables. Examples of helpful comments tailored to specific variables include:

- `# hostname is case-insensitive, so that 'server1' and 'SERVER1' behave identically`
- `# corpus_account must be qualified: 'name@domain.com' is OK, but 'name' is not`

Organize variables hierarchically, leading to names like `database_account`, `database_host`, `database_password`, and `database_priority`. Environment-specific variables should have meaningful prefixes such as `prod_database_account` or `env_database_account`.

Steer clear of shadowing [reserved keywords](https://docs.ansible.com/ansible/latest/reference_appendices/playbooks_keywords.html). Instead of using `item` or `serial`, opt for names like `server_item` or `hardware_serial_number`.

However, don't hesitate to deviate from these guidelines when warranted. For instance, abbreviate `gateway` to `gw` if it aligns with a well-established, longstanding practice within a particular team, extending beyond Ansible to other systems and languages.

Lastly, maintain consistency across playbooks and projects as a fundamental principle.

### Error Handling in Ansible

computing revolves around successful operations, a well-crafted playbook allocates significant attention to handling failures.

Error handling encompasses a range of actions, from simply ignoring errors to logging them, notifying monitoring systems, or initiating diagnostic processes. The key best practice in error handling involves the explicit use of `ignore_errors` and register. When encountering a non-fatal condition, utilize `ignore_errors` to allow the playbook to proceed, accompanied by an appropriate comment explaining the rationale. Additionally, conditionally handle error conditions using when: `task_result.failed`.

For resource management and resolving problematic states, employ the `block-rescue-always` mechanism. Leverage Ansible's `assert` module to verify the availability of services before utilizing them, rather than making assumptions.

To handle errors effectively, familiarize yourself with Ansible's built-in logging, auditing, debugging, and exit code functionalities. These tools equip you to manage errors proficiently and ensure smooth playbook execution.

### Ansible Logging

Ansible logging has several roles. Learn the essentials by practicing with the log and debug modules. In its simplest form, log can deliver a message during playbook execution through a specification such as:

```
- name: Log a single diagnostic
  log:
    msg: "This is the diagnostic logged at this point"
```

Next, delve into configuring `callback_whitelist`, `log_level`, and `log_path` in `ansible.cfg`. Experiment with different log levels—`CRITICAL`, `DEBUG`, `ERROR`, `INFO`, and `WARNING`—to tailor logging to your project's requirements and your personal preferences. Consistency in your chosen logging style is key, whether you opt to log everything for potential usefulness or selectively enable logging for critical operations.

[Ansible's callback plugins](https://docs.ansible.com/ansible/latest/plugins/callback.html) are invaluable for various logging scenarios, including customizing output formats, sending notifications via email, profiling performance metrics, and meeting specific logging needs.

Ensure that your log entries are timestamped and implement log rotation to optimize storage usage. Archive logs for auditing and compliance purposes, treating them as sensitive information with restricted access for authorized users only.

Once you have a grasp of logging fundamentals, explore advanced log management techniques using aggregators like Elasticsearch, Logstash, Kibana, and Splunk. These tools enhance your ability to analyze logs efficiently and scale your log management capabilities.

Establishing a review policy for logs requires careful consideration, as there is no one-size-fits-all approach. It's essential to be pragmatic and realistic in your approach. If stakeholders determine that logs need to be reviewed, it's advisable to allocate dedicated time for this purpose and track the outcomes accordingly.

### Inline Comments

Comments are invaluable yet often overlooked in real-world scenarios. With each line of Ansible code you write, consider what would aid your understanding if you revisited it six months down the line. Ideally, your playbooks should be so clear and intuitive that they require minimal explanation. However, in cases where clarity may be lacking, thorough comments serve as the next best solution, addressing any potential questions that may arise. It's crucial to prioritize writing good comments and encourage your entire team to do the same.

### READMEs

Additionally, include a `README` file in each directory and subdirectory within your project. Even a concise README can provide valuable context, such as:

```file {title="README.md"}
# Variables for the Staging environment

This specification details the Ansible variables which
are specific to actions in the staging environment.
```

Other `README` files can be several hundred words about the architecture and design decisions that a particular directory represents. Three natural best practices applicable to `README` files are:

-   Create exactly one `README.md` for each directory in a project.
-   Format the contents as well-formed [Markdown](https://www.markdownguide.org/getting-started/).
-   Provide high-level "philosophy" and motivation in the README. Leave technical details to source comments. Minimize repetition of the source comments, and instead **refer** to them in `README` files.

Additional `README` files can delve into the architecture and design decisions of a specific directory, often spanning several hundred words. Three key best practices for README files are as follows:

- Ensure there is precisely one `README.md` for each directory in the project.
- Format the contents using well-structured Markdown.
- Offer high-level insights into the "philosophy" and motivation behind the directory's purpose in the README. Reserve technical details for source code comments. Minimize redundancy with source code comments and instead reference them within the README files.

### Playbook Documentation

Create a top-level `README.md` to elucidate the purpose and utilization of the playbook. Offer readers at least one method to test the playbook, clarifying both the procedure and expected outcomes. Include examples, practical applications, and references to pertinent documentation. Incorporate a link to your policy regarding the subject matter. For any intricate aspects of the playbook, it's crucial to provide thorough explanations. Utilize dataflow, state, or entity diagrams as appropriate.

Specify the Ansible versions compatible with the playbook. Additionally, ensure that the README.md contains necessary license and copyright notices. Regularly review the README.md to ensure its alignment with the current state of the playbook. This approach results in a playbook that is more user-friendly and easier to maintain, particularly for individuals not involved in its initial development.

### Use of Vaults for Sensitive Data

Encrypt sensitive data, encompassing variables, configurations, task contents, and entire files. However, refrain from encrypting non-sensitive information. Store sensitive variables in `vars/secrets.yml` and encrypt the file accordingly. Reference the encrypted variables in the playbooks as needed.

Exercise control over access to the Vault, employing measures such as file system permissions. Permit only authorized users to decrypt sensitive data and solely with a valid decryption key. Opt for robust passwords and encryption keys. Establish explicit policies for rotation schedules and ensure adherence through regular rotation practices.

Avoid including passwords in playbooks. Instead, utilize a credential manager or prompt for necessary passwords to be supplied during runtime.

### Secure Communication

Ansible projects generally communicate by way of `ssh`, and `ssh` best practices include the following:

-   Choose strong keys
-   Choose strong ciphers
-   Secure configurations
-   Choose key–based authentication rather than password authentication
-   Distribute keys securely
-   Minimize agent forwarding
-   Define appropriate policies, including a limit on login failures and idle timeouts.
-   Consider configuring access-control, restrictions on authentication methods, and lists of allowed users through `sshd_config`.

Configure Ansible to use SSL for communication between control nodes and managed hosts. Control access to the control node with firewalling and network restrictions. Patch Ansible components regularly and enable two-factor authentication (2FA).

If your project uses [Ansible APIs](https://docs.ansible.com/ansible/latest/api/index.html), configure communication for TLS. If your project uses [Ansible Tower](https://docs.ansible.com/ansible/latest/reference_appendices/tower.html) or [AWX](https://www.ansible.com/products/awx-project/faq), configure HTTPS.

In Ansible projects, communication primarily occurs via ssh, and adhering to ssh best practices is essential:

- Opt for strong keys and ciphers.
- Ensure secure configurations.
- Prioritize key-based authentication over password authentication.
- Securely distribute keys.
- Limit agent forwarding.
- Establish appropriate policies, including restrictions on login failures and idle timeouts.
- Consider configuring access control, authentication method restrictions, and allowed user lists through `sshd_config`.

Configure Ansible to utilize SSL for communication between control nodes and managed hosts. Control access to the control node through firewalling and network restrictions. Regularly patch Ansible components and enable two-factor authentication (2FA).

For projects leveraging [Ansible APIs](https://docs.ansible.com/ansible/latest/api/index.html), configure communication for TLS. If using [Ansible Tower](https://docs.ansible.com/ansible/latest/reference_appendices/tower.html) or [AWX](https://www.ansible.com/products/awx-project/faq), configure HTTPS for secure communication.

### Privilege Escalation and Sudo

Familiarize yourself with Ansible's [`become` feature](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_privilege_escalation.html), specifically designed for secure privilege escalation. Apply `become` judiciously, limiting its use to individual plays rather than entire playbooks. Employ `become_user` as an additional method to refine the precision of an escalation. Study `become_method` to understand the suitability of different escalation methods; for instance, while `sudo` serves as the default, environments equipped with PowerBroker may require the use of `pbrun`. Regularly review the usage of privilege escalation to ensure compliance with security requirements.

## Conclusion

The most significant benefits of adhering to Ansible best practices stem from consistent, non-technical practices. This includes maintaining version control for playbooks and comprehensive documentation, segregating sensitive information from publicly accessible data, distinguishing roles from actions, and separating targets from implementations. Incorporate Ansible updates into a well-defined [software development lifecycle (SDLC)](https://stackify.com/what-is-sdlc/) (SDLC), and utilize tools like [Ansible Lint](https://ansible.readthedocs.io/projects/lint/) appropriately. Ensure that every line of code serves a purpose.

While these practices may not delve deeply into technical aspects, implementing them consistently across your teams yields significant benefits in terms of Ansible's best practices.
