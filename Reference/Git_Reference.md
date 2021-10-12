## Installation

### Debian/Ubuntu

```bash
$ apt-get install libcurl4-gnutls-dev libexpat1-dev gettext \
  libz-dev libssl-dev
$ apt install git
```

### Centos/RedHat

```bash
$ yum install curl-devel expat-devel gettext-devel \
  openssl-devel zlib-devel
$ yum -y install git-core
```

## Operation

* **Basic**
  * **git init**
    * git init *(Initialize .)*
    * git init dir *(Initialize Certain Directory)*
  * **git clone**
    * git clone [url] *(Clone the Repository to .)*
    * git clone [url] *nickname (Clone the Repository to . with Given Name "nickname")*
  * **git config**
    * git config user.name 'name' *(Apply 'name' to user.name For Current Repository)*
    * git config --global user.name 'name' *(The Same Except being Applied for All Repository)*
    * git config user.email `test@mail.com` *(Apply 'name' to user.name For Current Repository)*
    * git config --global user.email `test@mail.com` *(The Same Except being Applied for All Repository)*
* **Common Use**
  * **git add**
    * git add . *(Add ./*  to Temp Zone)
    * git add [file] [file] ... *(Add Multiple Files to Temp Zone)*
  * **git commit**
    * git commit -m [message] *(Submit Files in Temp Zone to Repository and Store the Given Message)*
    * git commit [file] [file] ... -m [message] *(Submit Given to Repository and Store the Given Message)*
    * git commit -a *(Submit All Files in . to Repository without being Added to Temp Zone)*
  * **git status**
    * git status *(Show Which Added File or New File Status)*
    * git status -s *(Show Which Added File or New File Status for Short)*
  * **git branch**
    * git branch *(List Branches for Current Repository)*
    * git branch branchname *(Create a New Branch for Current Repository with Given Name "branchname")*
    * git branch -d [branch] *(Delete a Branch)*
  * **git checkout**
    * git checkout [branch] *(Switch to Given branch)*
    * git checkout -n [branch] *(Create a New Branch and Switch to the Branch)*
  * **git merge**
    * git merge [branch] *(Merge Given Branch to the Main Branch)*
    * *(Handle Conflicts manually then Add and Commit the Changes)*
  * **git diff**
    * git diff [file/branch] *(List no-Temp-Zone-Synced Changes)*
    * git diff --cached/staged [file/branch] *(List Temp-Zone-Synced Changes)*
    * git diff HEAD [file/branch] *(List All Changes)*
    * git diff [file/branch] -stat *(List Less)*
    * *(Show more Details than @git status)*
  * **git rm**
    * git rm [file] *(Given File is Synced in Workspace and Temp Zone)(Delete Both)
    * git rm -f [file] *(Given File out of Synced in Workspace and Temp Zone)(Delete Both)
    * git rm --cached [file] *(Delete File in Temp Zone Only)
    * git rm -r [dir] *(Delete Directory Recursively)*
  * **git mv**
    * git mv [file] [newfile] *(Move, Rename, Copy Soft Link)*
  * **git log**
    * git log *(Print History of Current Repository)*
    * git log --oneline *(Same but Less)*
    * git log --reverse *(Sort in Reverse)*
    * git log --graph *(Print History in Topology)*
    * git log --author='name' *(Filter Logs with Given Author)*
    * git log -[number] *(Print the Last [number] ones)*
    * git log --no-merges
    * git log --since={date} *(--since={2010-04-18})*
    * git log --before={date}
    * git log --until={time} 
    * git log --after={time} *(--after={3.weeks.ago})*
  * **git blame**
    * git blame [file] *(List Change History for Given File)*

## Github

### Add a Remote Repository

```bash
$ ssh-keygen -t rsa -C "zhaoqifan0106@qq.com"
...
$ vim ~/.ssh/id_rsa.pub
...
##copy all from id_rsa.pub
```

github-->Account-->Settings-->SSH and GPG keys-->New SSH key

paste into 'Key' textarea

```bash
$ ssh -T git@github.com
...
You've successfully authenticated, but GitHub does not provide shell access.##Success
```

Github-->New Repository-->Repository name-->Create repository

```bash
$ mkdir gitdemo
$ cd gitdemo/
$ git init
$ touch test.txt
$ git add test.txt
$ git commit -m "First Commit"
...

$ git remote add [nickname] git@github.com:SinowX/[repository_name].git
$ git push -u [nickname] [branch]
```

### List Remote Repository

```bash
$ git remote
[nickname]

$ git remote -v
[nickname] git@github.com:SinowX/[repository_name].git (fetch)
[nickname] git@github.com:SinowX/[repository_name].git (push)
```

### Fetch Remote Repository

```bash
$ git fetch [nickname] [branch]#master for default
...##Local branch "[nickname]/[branch]" being Updated

$ git merge [nickname]/master
...##Merge branch "[nickname]/master" to Current branch
```

### Push Remote Repository

```bash
$ git push [nickname] [branch]
...##push local [branch] to remote [nickname]
```

### Delete Remote Repository

```bash
$ git remote rm [nickname]
```

