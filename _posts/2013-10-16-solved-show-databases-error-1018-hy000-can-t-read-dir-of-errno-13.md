---
layout: default
title: "[SOLVED] SHOW databases; ERROR 1018 (HY000)- Can't read dir of '.' (errno- 13)"
tags : [mysql, ubuntu, apparmor]
---
<p><small>{{ page.date | date_to_long_string }}</small></p>

We recently migrated our database to new server. The server packed with SSD, which is sweet :) We used [Percona XtraBackup](http://www.percona.com/software/percona-xtrabackup) to take a live backup of the database, copied it over to new server and sync back to current database using master-slave replication.

To take advantage of SSD, we've configured MySQL to store data in SSD instead of default location. We've edited `/etc/mysql/my.cnf` with this value `datadir = /example_ssd_partition/mysql/data`. Everything went fine until I ran `SHOW databases` command. I got this error:

{% highlight sql %}
  SHOW databases;
  ERROR 1018 (HY000): Can't read dir of '.' (errno: 13)
{% endhighlight %}

I searched the error message and it yielded bunch of similiar results. The common theme is permission error. [Here's one of many results telling it's a permission problem and I just need to `chown` and `chmod` the directory](http://stackoverflow.com/questions/11066411/mysql-error-error-1018-hy000-cant-read-dir-of-errno-13). I did that and triple confirmed everything belongs to mysql user. But I still can't `SHOW databases`. In fact, everything that require a query to `information_schema` will raise `Can't read dir of '.'` error.

Here's the gotcha: Ubuntu comes with [AppArmor](https://wiki.ubuntu.com/AppArmor). AppArmor's security model is to bind access control attributes to programs rather than to users. In my `/etc/apparmor.d/usr.sbin.mysqld` file I've made these changes:

{% highlight %}
  ...
  # Disable these two lines
  /var/lib/mysql/ r,
  /var/lib/mysql/** rwk,

  # Added these two lines
  /example_ssd_partition/mysql/data r,
  /example_ssd_partition/mysql/data/** rwk,
  ...
{% endhighlight %}

While it seems correct, there's actually a typo in `/example_ssd_partition/mysql/data r,`, it should be `/example_ssd_partition/mysql/data/ r,` instead. I missed out a forward slash after /example_ssd_partition/mysql/data. Because of that, while everything in `/example_ssd_partition/mysql/data/` owned by mysql user, the program itself has no access to the content of the directory. I've changed it to:

{% highlight %}
  ...
  /example_ssd_partition/mysql/data/ r,
  /example_ssd_partition/mysql/data/** rwk,
  ...
{% endhighlight %}

A quick AppArmor restart and now I can view all databases again!