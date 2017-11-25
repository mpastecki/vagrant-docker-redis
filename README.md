# Redis on Vagrant

This is for a study related project for which we need to show quick overview of Redis.

# How to run it?

You must have vagrant and virtualbox installed. 

If you are Windows user, before running following commands, make sure that both Virtualbox and CMD console are running as Administrator.

Then clone the repo, enter the directory with cloned files and run

```sh
vagrant up
```

Then you can connect to manager or workers with this command:

```sh
vagrant ssh <name>
```

So to check names:

```sh
vagrant status
```

to connect to manager:

```sh
vagrant ssh manager
```

# Customize

You should check file Vagrant and verify at least following values:

```sh
# Number of worker nodes - Virtual Machines
numworkers = 2

# Memory for your Virtual Machines
vmmemory = 1024

# Number of vCPU per VM
numcpu = 2
```
