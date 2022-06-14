---
title: "The Most-Used Linux Commands"
layout: post
date: 2022-06-13 21:50
image: ../assets/images/linux/linux-1.png
headerImage: true
tag:
- linux
- cli
- devOps
- commands
category: blog
author: guneycansanli
description: What are the most used Linux command and what are they doing or why We are using them?
---

# Introduction

[Linux][1]  is an operating system built around a set of values. If you agree with those values, that alone can be ample reason to give Linux a go. But there are many pragmatic reasons to switch to Linux.

Linux is freely available for anyone to download and use for any purpose, and so are most of the apps on it.

Unlike proprietary software, this is software that you actually get to take ownership over, giving you genuine control over your computer. Use it to do what you wish. Take it apart and tinker. Put it back together. Learn from it. Keep your machine running as long as possible. You can turn your Linux knowledge into a career, or you can use Linux as a stable foundation for the career of your choice.

---

## Wyh we are using Linux commands?

The **Linux** command is a utility of the Linux operating system. All basic and advanced tasks can be done by executing commands. The commands are executed on the **Linux** terminal. The terminal is a command-line interface to interact with the system, which is similar to the command prompt in the **Windows OS**. Commands in **Linux** are case-sensitive.

**Linux** provides a powerful **command-line interface** (CLI) compared to other operating systems such as Windows and MacOS. We can do basic work and advanced work through its terminal. We can do some basic tasks such as creating a file, deleting a file, moving a file, and more. In addition, we can also perform advanced tasks such as administrative tasks (including package installation, user management), networking tasks (ssh connection), security tasks, and many more.

**Linux** terminal is a user-friendly terminal as it provides various support options. To open the **Linux** terminal, press **"CTRL + ALT + T"** keys together, and execute a command by pressing the **'ENTER'** key.

### CD (Change Directory) 

The cd command is vital to navigating the Linux platform. It can be used by any user, whether you’re an administrator or a regular user.

{% highlight raw %}
 $ cd /directory/subdirectory
{% endhighlight %}

The cd command will change your current working directory to /directory/subdirectory.

Use the following command to switch back to the parent directory:

{% highlight raw %}
 $ cd ..
{% endhighlight %}

Now use the following command to go directly to the home directory:

{% highlight raw %}
 $ cd ~
{% endhighlight %}

## ls(List)

The ls Linux command is probably the most used. You use the ls command to list all the directory contents, but that’s not all. You can pair it with other commands to view hidden files, sort files, etc.

{% highlight raw %}
 $ ls
{% endhighlight %}

This command gives you the names of all the files residing in your directory. However, it does not provide you with metadata about the files, such as type, size, etc.

{% highlight raw %}
 $ localhost:~# ls -l
 total16
 -rw-r--r –    1 root     root           114 Jul  6 03:47 bench.py
-rw-r--r –    1 root     root            76 Jul  3 19:15 hello.c
{% endhighlight %}

The “ls -l” command gives you a list with extra details about the directory contents. For example, the resultant list includes the: modified date, size, time, owner name, owner permissions, and file name.

{% highlight raw %}
 $ localhost:~# localhost:~$ ls -a
{% endhighlight %}

![lscommand][2]

You use the “ls -a” command to view all the hidden files. It displays a list of all the files, including hidden files. The files starting with a ‘.’ are used to identify hidden files.

## MKDIR (Make Directory)

You use the mkdir command to make single or many directories. You can also set permissions for each directory you create. Remember that not every user has permission to create directories. Therefore, you must have the correct privileges before you create any directory, or you’ll receive a “permission denied” error.


{% highlight raw %}
 -# mkdir Test
 -# mkdir sample1 sample2 sample3 sample4
{% endhighlight %}

First command for creating a directory and second command for creating multiple directory.

## RM (Remove)

You can use the rm command to delete directories or files. Note, however, that you can’t get the file back once you delete it. There are ways to delete files and folders recursively, but let us stick to the basics first.

{% highlight raw %}
 -# rm Test
 -# rm sample1 sample2 sample3 sample4
{% endhighlight %}

Using is same like mkdir command. **rmdir** also another command.


## PWD (Print Working Directory)

The pwd Linux command prints out the current working directory.

{% highlight raw %}
 -# pwd
{% endhighlight %}

![pwdcommand][3]

This simple command returns the current working directory. For different users, it displays a different directory. In addition, it shows the directory at which the user is currently present.

Also The **pwd -L** command prints the value of pwd if it names the current working directory. You use the **“pwd -P”** command to print the physical directory without any symbolic links. It avoids all symbolic links.

## CAT (Concatenate)

The cat command concatenates different files. For example, the cat command allows you to create a view that contains a file, single file, multiple files, redirect output in files, and concatenate files.

{% highlight raw %}
 -# cat test.txt
{% endhighlight %}

![catcommand][4]

{% highlight raw %}
localhost:~$ cat sample1 sample2
This is some text
This is some text
{% endhighlight %}

This for multiple CAT for 2 files.

{% highlight raw %}
cat -n file_name
{% endhighlight %}

This is for CAT wiht line numbers.

{% highlight raw %}
cat file1 > file2
{% endhighlight %}

Replace file1 with the name of the file whose content you want to copy, and replace file 2 with the destination file’s name. File1 will overwrite the data on file2.

Now, what if you want to keep the contents of a file and copy data into the same file? The ‘cat file1 > file2’ command does not allow you to append data. Don’t worry. There’s another command which allows you to append data to the destination file. Use the following command:

**cat file1 >> file2**

## CP (Copy)

The cp command is one of the most used commands in Linux. You can use the cp command to copy a file, many files, or even directories. In this post, we’ll teach you how to use the cp command for files and directories.

{% highlight raw %}
cp source_file destination_file
{% endhighlight %}

You must have a source file filled with data from where you wish to copy. A destination file is not necessary. If a destination file exists, the command will override its existing data. If the destination file does not exist, the command will first create the file and then copy the data into it.

{% highlight raw %}
cp sourcefile1 sourcefile2 sourcefile3 Documents
{% endhighlight %}

The abovementioned cp command will copy all **three** files into the directory Documents.

{% highlight raw %}
cp -R Documents Test
{% endhighlight %}

Sometimes you may need to copy a whole directory into another. This command will copy all the contents of a directory to another directory. For example, the documents directory and its contents will be copied to the Test directory when you run this command.

## MV (Move)

Now it’s time to learn how to move directories and files around. Mv stands for the move, and this command moves files and directories from one place to another in Linux. It serves another function. You can also rename a file with the mv command. Let’s look into how you can use this command.

{% highlight raw %}
mv test.txt hello.txt
{% endhighlight %}

You can use the mv command in this sequence to rename a file. Here we are renaming the file called “tax” to a new name, “record.” This command doesn’t rename a file, but rather it moves the old file and renames it as the new file under the same directory. Now you’ll find “record.txt” under your current directory.

![mvcommand][5]

{% highlight raw %}
mv hello.txt PATH
{% endhighlight %}

![mvcommand2][6]

If you wish to move a file to a particular directory, then follow the above command. Use the mv command followed by the file’s name you wish to move and then write the directories name to move the file. Here we are moving the file names “test” to the directory called PATH.

---

[1]: https://www.linux.org/
[2]: ../assets/images/docker-command/lscommand.PNG
[3]: ../assets/images/docker-command/pwdcommand.PNG
[4]: ../assets/images/docker-command/catcommand.PNG
[5]: ../assets/images/docker-command/mvcommand.PNG
[6]: ../assets/images/docker-command/mvcommand2.PNG

