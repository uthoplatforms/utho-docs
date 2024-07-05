---
title: "An introduction to the Linux alternatives command"
date: "2022-11-23"
title_meta: "An introduction to the Linux alternatives command"
description: "Learn  altenatives of the Linux command"
keywords: ["ssh", "linux", "mac"]
tags: ["linux", "ssh"]
icon: "linux"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Linux/miscellaneous/an-introduction-to-the-linux-alternatives-command']
tab: true
---

<figure>

![An introduction to the Linux alternatives command](images/An-introduction-to-the-Linux-alternatives-command-1024x576.png)

<figcaption>

An introduction to the Linux alternatives command

</figcaption>

</figure>

This tutorial is to about the introduction of to the [Linux](https://en.wikipedia.org/wiki/Linux) alternatives command. Alternatives is a program that adds, deletes, updates, and displays data on the symbolic links that make up the alternatives system.

Because many users don't care how their computer completes a job as long as it is successful, abstractions may be useful to users. In reality, as long as all of an application's system calls are properly responded, it doesn't always matter how anything is accomplished. Theoretically, a Linux system administrator may provide a wide range of system utilities based on their functions rather than the precise name of the executable, but doing so often requires a lot of symlinking and version management. Unless you use the alternatives command, that is.

It's important to note that the alternatives command first existed as a substitute. This was originally an update-alternatives convenience tool from the Debian Linux project, written in Perl. The command was rewritten by Red Hat without the use of Perl, and it has since spread across Fedora-based distributions like Red Hat and CentOS as well as other distributions that rely on Red Hat to provide the functional definition of the Linux Standard Base (LSB).

## When to use the alternatives command

An easy-to-understand and practical example is the em command on your computer, which starts a simple text editor. It has long been the editor of choice for your user base; many individuals have built processes around it, and some people keep very specific configurations for it. Although em doesn't support Unicode, your user base lately made it apparent to you that they can't function without emoji functionality in their terminal editors.

You discover nem, a Unicode-capable branch of em, but you are aware that 99% of your customers are already used to em, have scripts that call em, and will never stop thinking of any alternative programme as em. The simple answer to this problem, as well as many others like it, is to provide the new nem binary as the preferred substitute for the em binary whenever the em command is used.

Although manually constructing a symlink could seem alluring, it is neither centralised nor immune to changes from a package management.

When handling "generic" application names, the alternatives command is most helpful. As "generic" isn't usually generic in the UNIX world, just as "Kleenex" or "Xerox" isn't always generic in the actual world, terminology like java, (x)emacs(-nox), (n)vi(m), whois, and iptables are often among of the first to have replacements specified for them. The alternatives command is not seen as a suitable option for really general names, such as EDITOR and CC. Because they are environment variables and need to be specified in /etc or $HOME/.profile, those phrases.

## Creating a new alternative

It is simple to generate an alternative entry using the example situation of an old binary named em being replaced by the new variation nem. Although in most cases this isn't required since the command isn't precisely what people assume it is in the first place (java, for example, is seldom found under /usr/bin directly), you must rename the original command in this example. If you do need to rename a binary, the proper place to do so is in the binary's RPM spec file so that the command to install the RPM supplies the binary with the new name.

In a pinch, particularly if you're planning to phase the command out anyway, you could remove it from the system's package management and just maintain a customised version of the old command accessible via /opt or something like.

Take the original editor, which you have called em2, for this example (denoting its latest version number).

Now, you may associate two binaries (em2 and nem) with the command em. Assume that em and nem are both little Emacs-style editors to help keep the terminology distinctions clear. In that instance, for the sake of simplicity, a very general word for the new alternative would be emacs (using the symbol to signify "micro") or uemacs.

Offer the following as a replacement for the original:

- a location for the "generic" symlink
- a name for the alternative (I've decided upon `uemacs`, to make it clear that it's a reference name rather than a specific binary)
- the binary to be executed when these symlinks are called
- a priority level to indicate which alternative is preferred

```
alternatives --install /usr/bin/em uemacs /opt/em-legacy/em2 1
```
Since the new binary is your preferred binary, develop a high priority replacement:

```
alternatives --install /usr/bin/em uemacs /usr/local/bin/nem 99
```
With the help of these commands, a new symbolic link, /usr/bin/em, is created that points to /etc/alternatives/em, which in turn can point to either /opt/em-legacy/em2 or /usr/local/bin/nem. The systems administrator chose the descriptive and memorable term uemacs as the human-friendly name for this collection of alternatives at random. Using the —config option, you can select the alternate symlink's destination:

```
alternatives --config uemacs
```
```
There are 2 programs which provide 'uemacs'.

  Selection    Command
-----------------------------------------------
*+ 1           /usr/local/bin/nem
   2           /opt/em-legacy/em2
```

## Removing an alternative

Alternatives may assist users or administrators move to something new or they might be long-term solutions. Using the —remove-all option and the alternative's "generic" name will eliminate all alternatives if you need to for whatever reason. Uemacs is used as an example here:

```
alternatives --remove-all uemacs
```
This deletes the symlink in /usr/bin and /etc/alternatives as well as everything else related to uemacs from the alternatives subsystem. Use the —remove option with the alternative name (in this case, uemacs) and the path of the choice you wish to drop if you only want to remove one option from an alternative:

```
alternatives --remove uemacs /opt/em-legacy/em2
```
## Check Alternatives

Other advantages of the alternatives command include the ability to symlink dependent components when a certain option is selected. Alternatives keeps your discrepancies consolidated while while doing a lot of the hard work. To avoid having to always remember that emacs is really emacs-26.3 or that java is actually /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.232.b09-0, etc., you may check in on what alternatives are available:

```
alternatives --list
```
Now, you have a learned the introduction of Linux alternatives command. The alternatives command is an illustration of how both users and administrators can profit from some deft manipulation since Linux is all about choice and flexibility.

**Must Read:** [What are Runlevels in Linux and its understanding](https://utho.com/docs/tutorial/what-are-runlevels-in-linux-and-its-understanding/).
