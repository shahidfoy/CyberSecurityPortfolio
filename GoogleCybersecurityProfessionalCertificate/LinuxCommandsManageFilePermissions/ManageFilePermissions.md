# Linux Manage File Permissions

Checking and updating file permissions can help with keeping systems secure and implement least privileged access to files and directories. Included is a detailed explaination on Linux file permissions and how they can be managed with `chmod`.

## Check file permissions
File permissions can be checked by utilzing the -l and -la arguments when listing files in a directory.

```bash
ls -l
```
to list files and directories in a parent directory.

or
```bash
ls -la
```
to list all files and directories in a parent directory including hidden files.

This will provide out put similar to the following:

```console
total 32
drwxr-xr-x 3 researcher2 research_team 4096 Aug  4 01:28 .
drwxr-xr-x 3 researcher2 research_team 4096 Aug  4 01:31 ..
-rw--w---- 1 researcher2 research_team   46 Aug  4 01:28 .project_x.txt
drwx--x--- 2 researcher2 research_team 4096 Aug  4 01:28 drafts
-rw-rw-rw- 1 researcher2 research_team   46 Aug  4 01:28 project_k.txt
-rw-r----- 1 researcher2 research_team   46 Aug  4 01:28 project_m.txt
-rw-rw-r-- 1 researcher2 research_team   46 Aug  4 01:28 project_r.txt
-rw-rw-r-- 1 researcher2 research_team   46 Aug  4 01:28 project_t.txt
```

## Permission String Explained (drwxrwxrwx)
On Linux machines all directories and folders have permissions that can set to ensure least privileged access to files. 

These permissions may look like the following examples:
- drwxrwxrwx
- drwxr-xr-x
- -rw-rw-r--

This string can be broken up into four sections 
|File type |User           |Group          |Other          |
|----------|---------------|---------------|---------------|
|d         |rwx            |rwx            |rwx            |


The following table descripes the purpose of each character:

| **Character** | **Example**       | **Meaning**                                                                      |
|---------------|-------------------|----------------------------------------------------------------------------------|
| 1st           | [**d**]rwxrwxrwx  | file type  `d` for directory `-` for regular file                                |
| 2nd           | d[**r**]wxrwxrwx  | read permissions for User `r` read permissions `-` no read permissions           |
| 3rd           | dr[**w**]xrwxrwx  | write permissions for User `w` write permissions `-` no write permissions        |
| 4th           | drw[**x**]rwxrwx  | execute permissions for User `x` execute permissions `-` no execute permissions  |
| 5th           | drwx[**r**]wxrwx  | read permissions for Group `r` read permissions `-` no read permissions          |
| 6th           | drwxr[**w**]xrwx  | write permissions for Group `w` write permissions `-` no write permissions       |
| 7th           | drwxrw[**x**]rwx  | execute permissions for Group `x` execute permissions `-` no execute permissions |
| 8th           | drwxrwx[**r**]wx  | red permissions for Other `r` read permissions `-` no read permissions           |
| 9th           | drwxrwxr[**w**]x  | write permissions for Other `w` write permissions `-` no write permissions       |
| 10th          | drwxrwxrw[**x**]  | execute permissions for Other `x` execute permissions `-` no execute permissions |

## Change File Permissions
The command `chmod` can be used to modify file permissions. For example if we wanted to remove the write permissions in `project_k.txt` folder for the Other section of the permission string we can use the following command:

```bash
chmod o-w project_k.txt
```

These modifications can be chained together like so:

```bash
chmod u+rwx,g-w,o-rx project_k.txt
```

The `=` can be used to overwrite existing permissions

```bash
chmod u=r,g=r,o=r project_k.txt
```

The following table provides details on the arguments used with `chmod`:

| **Character** | **Description**                                     |
|---------------|-----------------------------------------------------|
| u             | indicates changes will be made to user permissions  |
| g             | indicates changes will be made to group permissions |
| o             | indicates changes will be made to other permissions |
| +             | adds permissions to the user, group, or other       |
| -             | removes permissions from the user, group, or other  |
| =             | assigns permissions for the user, group, or other   |

Note: Directories and hidden files on Linux can also be modified using the `chmod` command
