---
title: tlcl
slug: tlcl
author: [jianchengwang]
date: 2020-07-18
excerpt: "最近学习Linux Command ，瞄了下下TLCL文档，这里做个记录，感兴趣的可以去看下"
draft: false
tags: [linux]
---

最近学习**Linux Command** ，瞄了下下 **TLCL** 文档，这里做个记录，感兴趣的可以去看下

[原版地址](http://linuxcommand.org/tlcl.php)

[翻译版](http://billie66.github.io/TLCL/book/)

### I/O redirection

```shell
ls -l /usr/bin -> ls-output.txt
> ls-output.txt
ls -l >> /usr/bin >> ls-output.txt

## While we have referred to the first three of these file streams as standard input, output and error, the shell references them internally as file descriptors zero, one and two
ls -l /bin/usr 2> ls-error.txt
ls -l /bin/usr > ls-output.xtx 2>&1 or ls -l /bin/usr &> ls-output.txt

## The bit bucket is an ancient Unix concept and due to its universality, has appeared in many parts of Unix culture. When someone says he/she is sending your comments to /dev/null, now you know what it means. For more examples, see the Wikipedia article on “/dev/null”.
ls -l /bin/usr 2> /dev/null
```

### Pipelines

```shell
ls -l /usr/bin | sort| uniq -d | less
ls /usr/bin | sort | uniq | wc -l
ls /bin /usr/bin | sort | uniq | grep zip
ls /usr/bin | tail -n 5
tail -f /var/log/message
ls /usr/bin | tee ls.txt | grep zip

```

### Expansions and Quoting 

```shell
echo *
echo {Z..A}
echo $USER
echo $((1+2))
echo $(ls)
ls -l $(which cp)
ls -l "two words.txt"
echo "$USER $((2+2)) $(cal)"
echo '$USER $((2+2)) $(cal)'
echo "The balance for user $USER is: \$5.00"
```

### Permission

```shell
id
less /etc/passwd
less /etc/group
less /etc/shadow
chmod 777  foo.txt
chmod u+x,g=rx,o-x foo.txt
umask
chmod u+s program
chmod g+s dir
chmod +t dir
/etc/sudoers
su -l
su -
su -c 'ls -l /root'
sudo ls -l /root
chown bob foo.txt
chown ob:users foot.txt
chmod :users foo.txt
chmod bob: foo.txt
chgrp
passwd bob
adduser
useradd
groupadd
```

### Process

```shell
ps aux
top
Ctrl-c
Ctrl-z
vim &
jobs
fg %1
bg %1
kill [-signal] PID
kill -l
killall
pstree
vmstat
```

### vi

```shell
hjkl
0^$
wWbB
G
:q!
:wq
iaAo
u
x
3x
dd
5dd
dW
d$
d0
d^
dG
d20G
y
p
J
f
fa
/
:%s/Line/line/gc
#
:	The colon character starts an ex command.
%	Specifies the range of lines for the operation. % is a shortcut meaning from the first line to the last line. Alternately, the range could have been specified 1,5 (since our file is five lines long), or 1,$ which means “from line 1 to the last line in the file.” If the range of lines is omitted, the operation is only performed on the current line.
s	Specifies the operation. In this case, substitution (search and replace).
/Line/line	The search pattern and the replacement text.
g	This means “global” in the sense that the search and replace is performed on every instance of the search string in the line. If omitted, only the first instance of the search string on each line is replaced.
#
:n
:N
:buffers
:r foo.txt
:w foo1.txt
```

### Package Management

```shell
pacman -S package_name1 package_name2 # add soft
pacman -R package_name # remove soft
pacman -Syu # update system and soft
pacman -Ss string1 string2 # search
```

### Storage Devices

```shell
/etc/fstab
mount
umount
fsck
fdisk
mkfs
fdformat
dd
dd if=/dev/sdb of=/dev/sdc
wodim dev=/dev/cdrw image.iso
mkisofs
md5sum
df
```

### Network

```shell
ping
traceroute
netstat
netstat -ie
ftp
wget
ssh root@remote-sys
scp root@remote-sys:document.txt
sftp root@remote-sys
lcd Desktop
get ubuntu-8.04-desktop-i386.iso
bye

```

### Find File

```shell
locate /bin/zip
locate zip | grep bin
updatedb
# You may notice that, on some distributions, locate fails to work just after the system is installed, but if you try again the next day, it works fine. What gives? The locate database is created by another program named updatedb. Usually, it is run periodically as a cron job; that is, a task performed at regular intervals by the cron daemon. Most systems equipped with locate run updatedb once a day. Since the database is not updated continuously, you will notice that very recent files do not show up when using locate. To overcome this, it’s possible to run the updatedb program manually by becoming the superuser and running updatedb at the prompt.

find ~ -type d | wc -l
find ~ -type f | wc -l
type: b,c,d,f,l
find ~ -type f -name "*.JPG" -size +1M | wc -l
size: b,c,w,k,M,G

-cmin n	Match files or directories whose content or attributes were last modified exactly n minutes ago. To specify less than n minutes ago, use -n and to specify more than n minutes ago, use +n.
-cnewer file	Match files or directories whose contents or attributes were last modified more recently than those of file.
-ctime n	Match files or directories whose contents or attributes were last modified n*24 hours ago.
-empty	Match empty files and directories.
-group name	Match file or directories belonging to group. group may be expressed as either a group name or as a numeric group ID.
-iname pattern	Like the -name test but case insensitive.
-inum n	Match files with inode number n. This is helpful for finding all the hard links to a particular inode.
-mmin n	Match files or directories whose contents were modified n minutes ago.
-mtime n	Match files or directories whose contents were modified n*24 hours ago.
-name pattern	Match files and directories with the specified wild card pattern.
-newer file	Match files and directories whose contents were modified more recently than the specified file. This is very useful when writing shell scripts that perform file backups. Each time you make a backup, update a file (such as a log), then use find to determine which files that have changed since the last update.
-nouser	Match file and directories that do not belong to a valid user. This can be used to find files belonging to deleted accounts or to detect activity by attackers.
-nogroup	Match files and directories that do not belong to a valid group.
-perm mode	Match files or directories that have permissions set to the specified mode. mode may be expressed by either octal or symbolic notation.
-samefile name	Similar to the -inum test. Matches files that share the same inode number as file name.
-size n	Match files of size n.
-type c	Match files of type c.
-user name	Match files or directories belonging to user name. The user may be expressed by a user name or by a numeric user ID.

find ~ \( -type f -not -perm 0600 \) -or \( -type d -not -perm 0700 \)
-and	Match if the tests on both sides of the operator are true. May be shortened to -a. Note that when no operator is present, -and is implied by default.
-or	Match if a test on either side of the operator is true. May be shortened to -o.
-not	Match if the test following the operator is false. May be abbreviated with an exclamation point (!).
()	Groups tests and operators together to form larger expressions. This is used to control the precedence of the logical evaluations. By default, find evaluates from left to right. It is often necessary to override the default evaluation order to obtain the desired result. Even if not needed, it is helpful sometimes to include the grouping characters to improve readability of the command. Note that since the parentheses characters have special meaning to the shell, they must be quoted when using them on the command line to allow them to be passed as arguments to find. Usually the backslash character is used to escape them.
# However, since the parentheses have special meaning to the shell, we must escape them to prevent the shell from trying to interpret them. Preceding each one with a backslash character does the trick.

find ~ -type f -name '*.BAK' -delete
-delete	Delete the currently matching file.
-ls	Perform the equivalent of ls -dils on the matching file. Output is sent to standard output.
-print	Output the full pathname of the matching file to standard output. This is the default action if no other action is specified.
-quit	Quit once a match has been made.

-exec command {} ;
find ~ -type f -name 'foo*' -ok ls -l '{}' ';'
find ~ -type f -name 'foo*' -exec ls -l '{}' +

# Again, since the brace and semicolon characters have special meaning to the shell, they must be quoted or escaped.

find ~ -type f -name 'foo*' -print | xargs ls -l
find ~ -iname ‘*.jpg’ -print0 | xargs –null ls -l

mkdir -p playground/dir-{00{1..9},0{10..99},100}
touch playground/dir-{00{1..9},0{10..99},100}/file-{A..Z}

stat playground/timestamp

-depth	Direct find to process a directory’s files before the directory itself. This option is automatically applied when the -delete action is specified.
-maxdepth levels	Set the maximum number of levels that find will descend into a directory tree when performing tests and actions.
-mindepth levels	Set the minimum number of levels that find will descend into a directory tree before applying tests and actions.
-mount	Direct find not to traverse directories that are mounted on other file systems.
-noleaf	Direct find not to optimize its search based on the assumption that it is searching a Unix-like file system. This is needed when scanning DOS/Windows file systems and CD-ROMs.

http://www.gnu.org/software/findutils/

```

### Compression and Archiving

```shell
gzip foo.txt
gunzip foo.txt.gz
bzip2 foo.txt
bunzip2 foo.txt.bz2
tar cf playground.tar playgroud
tar tvf playground.tar
tar xf ../playground.tar
zip -r playground.zip playground
unzip
rsync
rsync options source destination
rsync -av playground foo
sudo rsync -av --delete /etc /home /usr/local /media/BigDisk/backup
alias backup='sudo rsync -av --delete /etc /home /usr/local /media/BigDisk/backup'
sudo rsync -av --delete --rsh=ssh /etc /home /usr/local remote-sys:/backup
rsync -av -delete rsync://rsync.gtlib.gatech.edu/fedora-linux-
 core/development/i386/os fedora-devel
```

### Regular Expression

```shell
grep [options] regex [file...]
-i	Ignore case. Do not distinguish between upper and lower case characters. May also be specified --ignore-case.
-v	Invert match. Normally, grep prints lines that contain a match. This option causes grep to print every line that does not contain a match. May also be specified --invert-match.
-c	Print the number of matches (or non-matches if the -v option is also specified) instead of the lines themselves. May also be specified --count.
-l	Print the name of each file that contains a match instead of the lines themselves. May also be specified --files-with-matches.
-L	Like the -l option, but print only the names of files that do not contain matches. May also be specified --files-without-match.
-n	Prefix each matching line with the number of the line within the file. May also be specified --line-number.
-h	For multi-file searches, suppress the output of filenames. May also be specified --no-filename.

^ $ . [ ] { } - ? * + ( ) | \

grep -h '.zip' dirlist*.txt
grep -h '^zip' dirlist*.txt
grep -h 'zip$' dirlist*.txt
grep -h '[bg]zip' dirlist*.txt
grep -h '[^bg]zip' dirlist*.txt
grep -h '^[A-Z]' dirlist*.txt
grep -h '^[A-Za-z0-9]' dirlist*.txt
grep -h '[-AZ]' dirlist*.txt

[:alnum:]	The alphanumeric characters. In ASCII, equivalent to: [A-Za-z0-9]
[:word:]	The same as [:alnum:], with the addition of the underscore (\_) character.
[:alpha:]	The alphabetic characters. In ASCII, equivalent to: [A-Za-z]
[:blank:]	Includes the space and tab characters.
[:cntrl:]	The ASCII control codes. Includes the ASCII characters zero through thirty-one and 127.
[:digit:]	The numerals zero through nine.
[:graph:]	The visible characters. In ASCII, it includes characters 33 through 126.
[:lower:]	The lowercase letters.
[:punct:]	The punctuation characters. In ASCII, equivalent to:[-!"#$%&'()*+,./:;<=>?@[\\\]_`{|}~]
[:print:]	The printable characters. All the characters in [:graph:] plus the space character.
[:space:]	The whitespace characters including space, tab, carriage return, newline, vertical tab, and form feed. In ASCII, equivalent to: [ \t\r\n\v\f]
[:upper:]	The upper case characters.
[:xdigit:]	Characters used to express hexadecimal numbers. In ASCII, equivalent to: [0-9A-Fa-f]

echo "AAA" | grep -E 'AAA|BBB'
grep -Eh '^(bz|gz|zip)' dirlist*.txt

^\(?[0-9][0-9][0-9]\)?  [0-9][0-9][0-9]-[0-9][0-9][0-9][0-9]$
^\(?[0-9]{3}\)?  [0-9]{3}-[0-9]{4}$

## Text Processing
cat > foo.txt
cat -A foo.txt
cat -ns foo.txt
sort > foo.txt
du -s /usr/share/* | head
du -s /usr/share/* | sort -nr | head
ls -l /usr/bin | head
ls -l /usr/bin | sort -nr -k 5 | head
sort foo.txt | uniq
cut -f 3 distros.txt
expand distros.txt | cut -c 23-
cut -d ':' -f 1 /etc/passwd | head
paste distros-dates.txt distros-versions.txt
join distros-key-names.txt distros-key-vernums.txt | head
comm file1.txt file2.txt
comm -12 file1.txt file2.txt
diff file1.txt file2.txt
diff -Naur old_file new_file > diff_file
patch < diff_file
echo "lowercase letters" | tr a-z A-Z
echo "lowercase letters" | tr [:lower:] A
tr -d '\r' < dos_file > unix_file
echo “secret text”	tr a-zA-Z n-za-mN-ZA-M
echo “frperg grkg”	tr a-zA-Z n-za-mN-ZA-M
echo "aaabbbccc" | tr -s ab
echo "front" | sed 's/front/back/'
echo "front" | sed 's_front_back_'
echo "front" | sed '1s/front/back/'
sed -n '1,5p' distros.txt
sed -n '/SUSE/p' distros.txt
sed -n '/SUSE/!p' distros.txt
sed 's/regexp/replacement/' distros.txt
sed 's/([0-9]{2})/([0-9]{2})/([0-9]{4})$/\3-\1-\2/' distros.txt
echo "aaabbbccc" | sed 's/b/B/
echo "aaabbbccc" | sed 's/b/B/g'
aspell
nl distros.txt | head
cat -n
echo "The quick brown fox jumped over the lazy dog." | fold -w 12
fmt -w 50 fmt-info.txt | head
pr -l 15 -w 65 distros.txt
printf “format” arguments
printf "I formatted the string: %s\n" foo
printf "%s\t%s\t%s\n" str1 str2 str3

```

### Compile

```shell

./configure  --prefix=/usr/local/test
make
make install

```