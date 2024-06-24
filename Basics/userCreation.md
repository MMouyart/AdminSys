# User creation and account management
## To create user with default parameters :
	useradd <user>
	By default the user will have its own home folder and its group will be created as well.

## To create user with default parameters and without its own group : 
	useradd -n <user>

## To create user with custom shell : 
	useradd -s /bin/<shell> <user>

## To create user with custom home directory : 
	useradd -d <directory> <user>

## To create user with custom groups : 
	useradd -g <group1>[,group1,...] <user>

## To create user with a comment : 
	useradd -c "comment" <user>

## To create user with a default set password :
	useradd -p <password> <user>

## Example of user with custom config : 
	useradd -s /bin/csh -m -d /home/king -c "LeBron James" -g root lebron
	creating user lebron with a custom shell /bin/csh with a home folder located at /home/king with a comment "LeBron James" which will belong to the root group

## To create a user from file with defined parameters. Use a file containing list of users with their creations parameters : 
	File follows the structure : loginname:password:uid:gid:comment:home_dir:shell
	The creation is done using the command : newusers <filename>
	Example of file : 
		homer:HcZ600a9:1008:1000:Homer Simpson:/home/homer:/bin/bash
		marge:1enz733N:1009:1000:Marge Simpson:/home/marge:/bin/csh
		bart:1y5eJr8K:1010:1000:Bart Simpson:/home/bart:/bin/ksh
		lisa:VGz638i9:1011:1000:Lisa Simpson:/home/lisa:/bin/sh
		maggie:5lj3YGQo:1012:1000:Maggie Simpson:/home/maggie:/bin/bash
	Example of creation : 
		newusers userslist.txt

## To view the default parameters : 
	useradd -D
## To modify default parameters : 
	useradd -D -<option> <value>
If the user has not been assigned a password, it can be set like that : 
	passwd <user>

## To modify a user's name but not its home directory : 
	usermod --login <new username> <old username> 

## To modify a user and its home directory : 
	usermod --login <new username> --move-home --home <path to the new home dir> <old username>
