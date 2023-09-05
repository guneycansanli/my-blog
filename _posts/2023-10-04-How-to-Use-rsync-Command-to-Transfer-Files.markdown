---
title: "How to Use the rsync Command to Transfer Files"
layout: post
date: 2023-04-10 14:20
image: ../assets/images/rsync/rsyncmain.jpg
headerImage: true
tag:
    - linux
    - ryscn
    - command
category: blog
author: guneycansanli
description: How to Use the rsync Command to Transfer Files
---

# What is rsync command?

- rsync is a fast and versatile command-line utility for synchronizing files and directories between two locations over a remote shell, rsync is similar to other file transfer commands like scp, stfp(protocol), cp. It basiclly send or receive files form remote host. rsync has different features as well. rsync can synchronize files and directories between two locations over a remote shell and It can be usefull for tkaing backup and rsync can sycn the source (file modified dates, renamed, deleted ... )

- The conclusion is, rsync is good for incremental transfers and for taking the backup while scp is good while securely pushing or pulling the small file from or to the remote nodes.

- rsync does not sycn your files for the nodes. It is more of a one-direction utility, not like mounting or using shared network folders.


# Install rsync

- If you don't have rsync you can install with your packer manager (apt, apt-get, yum. dnf ...)
{% highlight raw %}
apt install rsync -y 
{% endhighlight %}
- You can verify tha You have rscyn
{% highlight raw %}
   $whereis rsync
   $command -v rsync
   $rscyn --help
{% endhighlight %}

- Make sure that You have SSH access to the node because rsync uses SSH connection by defaultlike SCP.

# Example rscyn Using

1. I will sycn my local PC (Mac Book) test folder to QEMU Debian 10 VM's backup folder.
2. Create the folders.
3. Create sample text files on Your source.

  ![rsync][1]
You should have ^^^

4. Now We are ready to test rsync command from test/ -> /home/guney/backup/
5. rsync has --dry-run feature ,It is really usefull beacuse You can see output before actual changes. We are going to test with dry run first. We can say It is a demo mode.
6. We should use -r (recursive) option If You want to copy folder as We do.
{% highlight raw %}
   $ rsync -r --dry-run test guney@192.168.64.15:/home/guney/backup
{% endhighlight %}
7. Here you can see our command but after run this command You will relaize that nothing change or You don't have any output. Like below
  ![rsync1][2]

8. The reason of you did not see anything, firdt you need to use -v (verbose) flag for see the output and sure You used --dry-run It is not going to perform the changes.
9. Let's add the -v to command and see the output.
  ![rsync2][3]

10. Yes, We can see the dry run output now and If We good to go then We can remove --dry-run, run it again and Let's see What happens.
  ![rsync3][4]
11. We succesfully sync test file to our target.
12. Let's try another important feature of rsync. If you renamed file/s on your source directory then What rsync Would do. Let's test it.
13. Renamne a file then run the last command.
  ![rsync4][5]
14. Here as You can see there is a problem I renamed test1.txt to test4.txt and run same command after that my backup folder did not updated rsync just send my files over it. It just copied the file and did not replace the original one, It's not a true sync, rsync revome anything from target default.
15. Now I just wanted to mention this because again, It is not a sync utility despite the name, e you can sync directories but it is not a sync solution in way that We think of them now.
16. We should use --delete for delete anything in the target that does not exist at the source directory.
  ![rsync6][6]
17. Both directory have same concents, but it's still not completly the same. If you check the files modified date. They won't match and that bring us to the most popular rsync option of all, the dash A option.
18. -A option is archive mode. It is using for same metadatas
{% highlight raw %}
   $ rsync -rva --delete test guney@192.168.64.15:/home/guney/backup
{% endhighlight %}

19. Another option which I want to mentioned is zip option -z, If you use -z option compress the files as they transfer You can use When you have slower connection.
{% highlight raw %}
   $ rsync -rvaz --delete test guney@192.168.64.15:/home/guney/backup
{% endhighlight %}

20. Lastly I want to mention --remove-source-files option uses for removed source files after sync.
{% highlight raw %}
   $ rsync -rva --remove-source-files test guney@192.168.64.15:/home/guney/backup
{% endhighlight %}

That's all fellas , Hope It helps your works.

---


> :metal: :metal: :metal: :metal: :metal: :metal: :metal:

---

> :memo: Thanks for reading.

Guneycan Sanli.

---

[1]: ../assets/images/rsync/rsync1.jpg
[2]: ../assets/images/rsync/rsync2.jpg
[3]: ../assets/images/rsync/rsync3.jpg
[4]: ../assets/images/rsync/rsync4.jpg
[5]: ../assets/images/rsync/rsync5.jpg
[6]: ../assets/images/rsync/rsync6.jpg
