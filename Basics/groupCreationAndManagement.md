# Basic operations for user account and group managements
## To create a group : 
	addgroup <group>

## To add user to a group (assuming both the user and the group exist) : 
	usermod -a -G <group>[,group...] <user>

## To modify group permission on files and folders : 
	### To change the group ownership of a folder or a file : 
		chmod -R :<group> <folder or file>
	### To modify permissions of a group on a folder or a file : 
		chmod -R <permissions> <folder>

Link for chmod cheat sheet https://quickref.me/chmod.html

## To modify group permission on files and folders using ACL : 
	setfacl -m g:<group>:<permissions> -R <folder or file>
	Link for setfacl cheat sheet https://www.computerhope.com/unix/usetfacl.htm

## To remove a user from a group : 
	gpasswd -d <user> <group>

## To add a user to a group :
	gpasswd -a <user> <group>
 
## To remove user from all supplementary groups :
	usermod -G "" <user>

## To delete a group (if it does not belong to a user primary group) : 
	groupdel <group>

## To change a group name : 
	groumod --new-name <new group name> <old group name>
