<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [character](#character)
  - [metacharacter](#metacharacter)
- [Brace Expansion](#brace-expansion)
  - [Scp Multipule Folder/File to Target Server](#scp-multipule-folderfile-to-target-server)
- [Basic Comamnds](#basic-comamnds)
  - [`du`](#du)
- [CentOS](#centos)
  - [Yum](#yum)
    - [`File "/usr/libexec/urlgrabber-ext-down", line 28`](#file-usrlibexecurlgrabber-ext-down-line-28)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# character
## [metacharacter](https://www.grymoire.com/Unix/Quote.html)

| Character        | Where                | Meaning                                       |
|:----------------:|----------------------|-----------------------------------------------|
| `<RETURN>`       | csh, sh              | Execute command                               |
| `#`              | csh, sh, ASCII files | Start a comment                               |
| `<SPACE>`        | csh, sh              | Argument separator                            |
| ```              | csh, sh              | Command substitution                          |
| `"`              | csh, sh              | Weak Quotes                                   |
| `'`              | csh, sh              | Strong Quotes                                 |
| `\`              | csh, sh              | Single Character Quote                        |
| `variable`       | sh, csh              | Variable                                      |
| `variable`       | csh, sh              | Same as variable                              |
| `\`              | csh, sh              | Pipe character                                |
| `^`              | sh                   | Pipe Character                                |
| `&`              | csh, sh              | Run program in background                     |
| `?`              | csh, sh              | Match one character                           |
| `*`              | csh, sh              | Match any number of characters                |
| `;`              | csh, sh              | Command separator                             |
| `;;`             | sh                   | End of Case statement                         |
| `~`              | csh                  | Home Directory                                |
| `~user`          | csh                  | User's Home Directory                         |
| `!`              | csh                  | History of Commands                           |
| `-`              | Programs             | Start of optional argument                    |
| `$#`             | csh, sh              | Number of arguments to script                 |
| `$*`             | csh, sh              | Arguments to script                           |
| `$@`             | sh                   | Original arguments to script                  |
| `$-`             | sh                   | Flags passed to shell                         |
| `$?`             | sh                   | Status of previous command                    |
| `$$`             | sh                   | Process identification number                 |
| `$!`             | sh                   | PID of last background job                    |
| `&&`             | sh                   | Short-circuit AND                             |
| `||`             | sh                   | Short-circuit OR                              |
| `.`              | csh, sh              | Typ. filename extension                       |
| `.`              | sh                   | Source a file and execute as command          |
| `:`              | sh                   | Nothing command                               |
| `:`              | sh                   | Separates Values in environment variables     |
| `:`              | csh                  | Variable modifier                             |
| `Character`      | Where                | Meaning                                       |
| `[ ]`            | csh, sh              | Match range of characters                     |
| `[ ]`            | sh                   | Test                                          |
| `%job`           | csh                  | Identifies job Number                         |
| `(cmd;cmd)`      | csh. sh              | Runs cmd;cmd as a sub-shell                   |
| `{ }`            | csh                  | In-line expansions                            |
| `{cmd;cmd }`     | sh                   | Like (cmd;cmd ) without a subshell            |
| `>ofile`         | csh, sh              | Standard output                               |
| `>>ofile`        | csh, sh              | Append to standard output                     |
| `<ifile`         | csh, sh              | Standard Input                                |
| `<<word`         | csh, sh              | Read until word, substitute variables         |
| `<<\word`        | csh, sh              | Read until word, no substitution              |
| `<<-word`        | sh                   | Read until word, ignoring TABS                |
| `>>!file`        | csh                  | Append to file, ignore error if not there     |
| `>!file`         | csh                  | Output to new file, ignore error if not there |
| `>&file`         | csh                  | Send standard & error output to file          |
| `<&digit`        | sh                   | Switch Standard Input to file                 |
| `<&-`            | sh                   | Close Standard Input                          |
| `>&digit`        | sh                   | Switch Standard Output to file                |
| `>&-`            | sh                   | Close Standard Output                         |
| `digit1<&digit2` | sh                   | Connect digit2 to digit1                      |
| `digit<&-`       | sh                   | Close file digit                              |
| `digit2>&digit1` | sh                   | Connect digit2 to digit1                      |
| `digit>&-`       | sh                   | Close file digit                              |


# [Brace Expansion](https://www.gnu.org/software/bash/manual/html_node/Brace-Expansion.html)
## Scp Multipule Folder/File to Target Server

```bash
$ scp -r `echo dir{1..10}` user@target.server:/target/server/path/
```
# Basic Comamnds
## `du`
- top biggest directories under _[path]_
  ```bash
  $ du -a [path] | sort -n -r | head -n 5
  ```
- display the largest files according to human-readable format
  ```bash
  $ du -hs * | sort -rh | head -5
  ```
- display the largest folders/files including the sub-directories
  ```bash
  $ du -Sh | sort -rh | head -5
  ```
- biggest file sizes
  ```bash
  $ find -type f -exec du -Sh {} + | sort -rh | head -n 5

  or
  $ find [PATH] -type f -printf "%s %p\n" | sort -rn | head -n 5
  ```


# CentOS
## Yum
### `File "/usr/libexec/urlgrabber-ext-down", line 28`
- Error

        File "/usr/libexec/urlgrabber-ext-down", line 28
        except OSError, e:
                      ^

- Solution

    ```bash
    $ sudo update-alternatives --remove python /usr/bin/python3
    $ realpath /usr/bin/python
    /usr/bin/python2.7
    ```

OR

    ```bash
    $ sudo update-alternatives --config python

    There are 3 programs which provide 'python'.

      Selection    Command
    -----------------------------------------------
    *+ 1           /usr/bin/python3
       2           /usr/bin/python2.7
       3           /usr/bin/python2

    Enter to keep the current selection[+], or type selection number: 3
    ```


- [Reason](https://www.linuxquestions.org/questions/linux-newbie-8/yum-upgrading-error-4175632414/) 

    ```bash
    $ ls -l /usr/bin/python*
    lrwxrwxrwx 1 root root    24 Jul 10 02:29 /usr/bin/python -> /etc/alternatives/python
    lrwxrwxrwx 1 root root     9 Dec  6  2018 /usr/bin/python2 -> python2.7
    -rwxr-xr-x 1 root root  7216 Oct 30  2018 /usr/bin/python2.7
    lrwxrwxrwx 1 root root     9 Mar  7  2019 /usr/bin/python3 -> python3.4
    -rwxr-xr-x 2 root root 11392 Feb  5  2019 /usr/bin/python3.4
    -rwxr-xr-x 2 root root 11392 Feb  5  2019 /usr/bin/python3.4m

    $ ls -altrh /etc/alternatives/python
    lrwxrwxrwx 1 root root 16 Jul 10 02:29 /etc/alternatives/python -> /usr/bin/python3
    ```

- reference
  - [Yum install error file "/usr/bin/yum", line 30](http://www.programmersought.com/article/3242669414/)
  - [failed at yum update and how to fix it](http://wenhan.blog/2018/02/18/failed-at-yum-update-and-how-to-fix-it/)
  - [Upgraded Python, and now I can't run “yum upgrade”](https://unix.stackexchange.com/questions/524552/upgraded-python-and-now-i-cant-run-yum-upgrade)
  - [How to Find Out Top Directories and Files (Disk Space) in Linux](https://www.tecmint.com/find-top-large-directories-and-files-sizes-in-linux/)