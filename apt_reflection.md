# apt Reflection - 03/07/2026

## Overview

This file reviews the sudo and apt commands that will be used to download yaz-client and documents my experience using them to the best of my memory.

## Environment

- VM Provider: Google Cloud
- OS: Ubuntu 24.04 LTS x86/64

## Commands

Note: sudo is used when making changes to the system outside the home directory.

### Update and Upgrade 

```
sudo apt update
```

Using this commands checks the software on the system and checks to see if it is the most current version. It will inform you if it is not.

```
sudo apt upgrade
```

If there are updates to be made, running this command next will upgrade your programs to the latest version.
Running both of these commands went fine, no issues.

### Search and Install

```
apt search PACKAGE NAME
```

Searches for a program by its package name.

```
apt show PACKAGE NAME
```

Shows a more detailed description of the program.

```
sudo apt install PACKAGE NAME
```

Installs the package.

I had no issues using these commands to install yaz-client.

### Removal

```
sudo apt remove PACKAGE NAME
```

Removes package.

```
sudo apt --purge remove PACKAGE NAME
```

Removes package and its configuration files

```
sudo apt autoremove
```

Removes dependencies that were installed with packages that are now unistalled

```
sudo apt clean
```

Removes cached package files.

## Reflection
I had no difficulties running these commands or downloading yaz-client. I haven't tried any of the removal commands yet, as I don't wish to uninstall yaz-client yet.
