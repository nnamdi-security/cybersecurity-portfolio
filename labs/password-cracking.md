# Password Cracking
## Objective
To demostrate how to crack a password using hash file from Windows Security Account Manager(SAM)
## Steps followed
1. Run a CMD commmand(as administrator) to confirm if a copy of the hash file already exist on the system. If a copy exist, proceed to step 3
![powershell](../screenshots/cmd-list.png)
1. Create a shadow copy of the hash file on windows using powershell command running as administrator
![powershell](../screenshots/powershell.png)
3. copy the file to a temporary folder you created in your C: drive
![powershell](../screenshots/cmd-copy.png)
4. Move over to your kali machine and use the tool John the Ripper to extract hash and comapare to known list



   *To be continued...*
