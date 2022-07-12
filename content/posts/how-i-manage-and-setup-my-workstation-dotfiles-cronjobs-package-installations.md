---
title: "How I Manage & Setup My Workstation (dotfiles, cronjobs, package installations, and so on)"
date: 2021-08-17T05:34:52+08:00
draft: false
---

I have been an Arch user for about a year, and since I haven't looked back. Though as for all operating systems, rebuilding your system can be a lot of hassle. Especially if you are using a minimalist distribution like Arch, whose users tend to make much more configuration than an average Linux user.

After getting frustrated enough by not taking a backup and building an automation, I finally got to the work of doing so.
In this post, I will introduce you to my automation script, which is not rocket science, though it might give you an insight if you ever decide to build your own.

<!--more-->

I won't go into much details on the usage of the programs I will mention, such as GNU Stow. The reason is, my aim being, like I mentioned above, to give you an idea on how I handle things, and I believe contents that prepared solely to introduce those programs would be more beneficial.

---

## 1-) Dotfiles management: GNU Stow

(GNU Stow)[https://www.gnu.org/software/stow/] is a symlinks factory which we can have individual directories for each program in a directory we track via a version control system, and create symbolic links to their places with one simple command.

![The directory for my dotfiles in the home directory](/dotfiles_ls.png)

Let's look into bash directory:

![bash directory in ~/.dotfiles/](/dotfiles_bash_tree.png)

> Info: tree is a one nice command that you can list contents of directories in a tree-like format.

We have .bash_profile file, and a bash directory in .config directory. Looks the way how they would be placed in home directory, right? That's the working methodology of GNU Stow. When in your dotfiles directory, you run the following command for the program whose files you want to be symlinked to:

`stow bash`

What the command above does is symlinking directories and files of the selected program accordingly to its hierarchical structure, taking starting point as the parent folder of the .dotfiles directory, which in this case is the home directory. And the final result is:

![](/dotfiles_ls.png)

![](/dotfiles_bashprofile_link.png)

Of course, by using options, dotfiles directory and target directory can be specified so that you won't have to cd into it:

`stow --dir=$HOME/.dotfiles/ --target=$HOME/ bash`

That's pretty much all I want to mention about GNU Stow in this post. In the past, I used Git Bare Repository method, but I like the structure and usage of Stow more. Also, its unstowing option is really good when/if you don't want to use a program anymore but still want to keep its configuration files, or testing out different configurations and want to easily switch between them. 

## 2-) Before abandoning the ship: Running the checkout script

One other problem I used to face was the realization that I forgot to backup something. So I have written a basic shell script for it: checkout. What it does is basically checking a few repositories I specified for any changes that are not committed & pushed. It also reminds me a few tasks I need to do manually:

![](/checkout_script.png)

[Click here](https://github.com/memreyagci/dotfiles/blob/master/personal_scripts/.local/bin/scripts/checkout) to check out the script.

## 3-) Finally, the script for automated setup

You partitioned your disks, setup the encryption and all the other things. You booted up the computer, and here comes the boring part: Pulling your dotfiles, placing them to their correct places, then installing the programs you need only to find out later that you forgot to some necessary stuff in a moment that the time is running out for you. Tap-to-click for touchpad doesn't work? Gotta hit to stackoverflow to solve the issue again.

..unless you note down all the necessary stuff for your system and build an automation script.

What the script basically does is:

* Creating a user which can have root privileges with password.
* Enabling [Chaotic AUR](https://aur.chaotic.cx/) repository.
* Refreshing package database and installing necessary programs. I store the package names in a csv file that indicates name, installation method, purpose of the program.
* Installing my dotfiles.
* Enabling background services with systemctl.

---

>Tip: When one wants to start a newly-installed background service, most users tend to do the following:
```
systemctl enable SERVICE_NAME
systemctl start SERVICE_NAME
```
>The start command is to start the service without needing to reboot. Did you know that you could do the same thing providing the enable command with --now flag?:
```
systemctl enable --now SERVICE_NAME
```
>In one command, you enabled & also started the service.

---

* Setting cronjobs.
* Installing my build of suckless utilities (window manager, statusbar, terminal emulator, lockscreen, menu application)
* Some other small tasks like enabling tap-to-click, setting default user shell to ZSH, and so on.

---

To run the script:

```
$ git clone https://github.com/memreyagci/workstation-installation-script /tmp/workstation-installation-script

$ cd /tmp/workstation-installation-script

$ ./installation-script.sh
```

And finally reboot your computer after the installation has finished.

To be honest, I wrote the code quite clumsily. Except the installations from AUR repository via yay (which is only 2 programs during the preparation of this post), everything works, though I already have a set of goals for improvements.

---

Goals:

* Fixing installation of AUR packages.
* Better error handling.
* Creating a log file.
* More aesthetically written user messages.
* Handling backups automatically. What I have in mind is improving the checkout script in a way that it will be able to take a backup of the directories I specified and move them to an external drive. I might even use encryption while doing so by using veracrypt.
* More and more automation.
