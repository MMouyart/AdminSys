# Commonly used packet managers
## Apt-get and related commands
### Apt-get
install package
```apt-get install <package name>```

download only specific package
```apt-get download <package name>```

remove package
```apt-get remove <package name>```

remove installed dependencies
```apt-get autoremove <package name optionnal>```

upgrade package
```apt-get install <package name>```

upgrade all packages
```apt-get upgrade```

search for package
```bash
apt-cache search <package name>
apt search <package name>
```

apt-get common options :
- autoclean       Removes unused packages
- check           Check db for issues
- clean           Clean the DB, you can do a clean all to clean everything and start afresh
- dist-upgrade    Checks for new versions of the OS; Major upgrade
- install         Install or upgrade packages
- remove          Removes a package
- source          Install the source of a package
- update          Updates the information about packages from repositories
- upgrade         Upgrades all packages

### Apt-cache
apt-cache common options :
- depends         Show dependencies
- pkgnames        Shows all installed packages
- search          Search
- showpkg         Show information about a package
- stats           Show statistics
- unmet           Show unmet dependencies for all installed packages or the one you specified

### DPKG
Reconfigure .deb package
```dpkg-reconfigure <packet>```

dpkg common options :
- -c or --contents        Show the contents of a package
- -C or --audit           Search for broken installed packages and propose solutions
- --configure             Reconfigure an installed package
- -i or --install         Install or Upgrade a package; Wont resolve / install dependencies
- -I or --info            Show Info
- -l or --list            List all installed packages
- -L or --listfiles       List all files related to this package
- -P or --purge           Remove the package and its configuration files
- -r or --remove          Remove the package; Keep the configurations
- -s or --status          Display status of a package
- -S or --search          Search and see which package owns this file

## Yum
Yum common commands :
- update          Updates the repositories and update the names of packages, or all if nothing is named
- install         Install a package
- reinstall       Reinstall a package
- list            Show a list of packages
- info            Show information about a package
- remove          Removes an installed package
- search          Searches repositories for packages
- provides        Check which packages provide a specific file
- upgrade         Upgrades packages and removes the obsolete ones
- localinstall    Install from a local rpm file
- localupdate     Updates from a local rpm file
- check-update    Checks repositories for updates to the installed packages
- deplist         Shows dependencies of a package
- groupinstall    Install a group, say "KDE Plasma Workspaces"
- history         Show history of the usage

download packages without installing them
```yumdownloader --resolver <package name>```

## RPM
Rpm common actions :
- -i      --install       Installs a package
- -e      --erase Removes a package
- -U      --upgrade       Installs/Upgrades a package
- -q      --query Checks if the package is installed
- -F      --freshen       Only update if it's already installed
- -V      --verify        Check the integrity of the installation
- -K      --checksig      Checks the integrity of an rpm package

Rpm common options :
- -c      --configfiles   Show the packages configuration files
- -i      --info  Detailed info about a pacakge
- -a      --all   Show all Installed packages
- --whatprovides  Shows what packages provide this file
- -l      --list  Query the list of files a package installs
- -R      --requires      Show dependencies of a package
- -f      --file  Query package owning file

## Zypper
Zypper common commands :
- help            General help
- install         Installs a package
- info            Displays information of a package
- list-updates    Shows available updates
- lr              Shows repository information
- packages        List all available packages or packages from a specific repo
- what-provides   Show the owner of a file
- refresh         Refreshes the repositories information
- remove          Removes a package from the system
- search          Searches for a package
- update          Checks the repositories and updates the installed packages
- verify          Checks a package and its dependencies
