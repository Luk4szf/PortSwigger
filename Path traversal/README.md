# File Path Traversal, Simple Case

## Lab Description
![image](https://github.com/user-attachments/assets/2df5296a-ce7c-4615-bf8f-78ecfc2faf6a)

First, let's understand **What is a Path Traversal Vulnerability?**

A Path Traversal vulnerability (also known as Directory Traversal) is a common security flaw in web applications. This vulnerability allows attackers to access files and directories on the web server that they are not authorized to access. It can pose significant risks to the web system and user data.

The mechanism of Path Traversal involves attackers using the dot-dot-slash (“../”) character sequence to navigate to parent directories. Once attackers successfully access parent directories, they can continue to access other folders and even potentially reach sensitive files on the web server.

- **Absolute Path**: We access the file `hello.txt` through the path `/usr/bin/hello.txt`.
- **Relative Path**: We use `../../../usr/bin/hello.txt` to go up three levels to the root directory `/` in a Linux OS, and from there, we locate the original path `/usr/bin/hello.txt`.

The steps to solve this exercise are:
1. First, find the accessible path to inspect the source and identify `/image?filename=*.jpg`.
2. Next, use the payload `../../../etc/passwd` to access `/etc/passwd`.
