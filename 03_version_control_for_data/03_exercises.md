# Working with a DataLad Dataset

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1HiPpeOz2KTOP0OuXIlch3xcvagKDwn5e?usp=sharing)

Git is great for tracking changes in code, but it struggles with large data files. While you can commit large files to Git, doing so will make it incredibly slow (any file greater than 50 MB will notably slow things down). This is where DataLad comes in: DataLad stores the content of large files and just commits a tiny pointer with a unique hash to Git. This way, Git knows about our files without having to handle the large files itself. You can think of DataLad as an extension to Git that allows it to track large files, enabling version control for scientific data. All of Git's features, like reverting to older versions or creating branches, work with DataLad projects as well.

In this notebook, you are going to learn the basics of DataLad: you are going to clone an existing dataset, make modifications, and then create additional copies, or siblings, of the dataset on online repositories and your local file system.

## Setup  

Activate the pixi environment which contains DataLad and its dependencies and run `git config` to load the Datalad Next extension.


```python
pixi shell
git config --global --add datalad.extensions.load next
```

## Working with DataLad Datasets

### Background

The first section of this notebook will introduce you to DataLad by cloning and working with an existing dataset. When DataLad clones a dataset, it does not actually download the contents of large files. Instead, it just downloads the file pointers stored in Git. This makes cloning very fast, even if there are terabytes of data! To get the actual file content, we have to call `datalad get`. The reverse operation is `datalad drop`, which removes the file content while keeping the pointers. The ability to get and drop file content while keeping the folder structure intact is very useful when you are working with large datasets: you can clone the dataset, get the files you need for your script, run the script, and then use `datalad drop` to remove the file contents again.

**Note for Windows users**: When you clone a DataLad dataset, you will get a message that says *"detected a crippled filesystem"*. Don't worry, this does not mean that there is anything wrong with your computer - it just means DataLad is working slightly differently on Windows (more on this later).

### Exercises

The following exercises will introduce you to the DataLad command line interface (CLI). While the DataLad commands are the same on all operating systems, commands for listing folders and printing files differ between Linux/macOS and Windows. If you are on Windows and want to use Linux terminal commands, you can use **Git Bash** as your terminal, which comes with the Git installation on Windows. Below are the Linux/macOS and Windows commands as well as the DataLad command required in this section:

**Terminal Commands**
| Linux/macOS | Windows Command Prompt | Description |
| --- | --- | --- |
| `ls` | `dir` | List the content of the current directory |
| `ls -a` | `dir /a` | List the content of the current directory (including hidden files) |
| `ls -a data` | `dir /a data` | List the content of the `data` directory |
| `cd code/` | `cd code/` | Move to the `code/` directory |
| `cd ..` | `cd ..` | Move to the parent of the current directory |
| `cat folder/file.txt` | `type folder/file.txt` | Print the content of `file.txt` |

**DataLad Commands**
| Code | Description |
| --- | --- |
| `datalad clone <url>` | Clone the dataset at the given URL into a new directory |
| `datalad get folder/file.csv` | Get the content of `file.csv` |
| `datalad get folder/` | Get the content of all annexed files in `folder/` |
| `datalad drop folder/file.csv` | Drop the content of `file.csv` |
| `datalad drop folder/` | Drop the content of all annexed files in `folder/` |
| `datalad get *` | Get the content of the entire dataset |
| `datalad drop *` | Drop the content of the entire dataset |

**Exercise**: Use `datalad clone` to clone the dataset from `https://hub.datalad.org/edu/penguins.git`.


```python
datalad clone https://hub.datalad.org/edu/penguins.git
```

**Exercise**: Change the working directory to the `penguins` folder and list its contents


```python
cd penguins
ls
```

**Exercise**: List the contents of the `adelie/` folder


```python
ls adelie/
```

**Exercise**: Try to print the content of `adelie/table_219.csv` using the `cat` command (or `type` on Windows). You should see an error because, by default, DataLad does not download the actual file content


```python
cat adelie/table_219.csv
```

**Exercise**: Use `datalad get` to download the content of `adelie/table_219.csv`, then print it again.


```python
datalad get adelie/table_219.csv
cat adelie/table_219.csv
```

**Exercise**: Use `datalad drop` to remove the content of `adelie/table_219.csv` again.


```python
datalad drop adelie/table_219.csv
```

**Exercise**: Use `datalad get` to download the contents of the entire `adelie/` folder, then use `datalad drop` to remove the contents again.


```python
datalad get adelie/
datalad drop adelie/
```

**Exercise**: Use `datalad get *` to download the contents of all files in this dataset.


```python
datalad get *
```

## Modifying Content and Tracking Changes

### Background

Whenever we make changes to our dataset, DataLad records them in the Git commit history. The ability to capture provenance - information about which activity, initiated by which entity, produced which outputs, given a set of parameters, a computational environment, and any input data - is great for research projects because it enables transparency and reproducibility. Since DataLad is built on top of Git, you don't need to learn anything new here: the Git commit history works exactly the same for DataLad as it does for Git.

Because DataLad wants to make sure that we don't accidentally overwrite our files once they are committed, it locks them to make them unmodifiable.
This is why we have to use `datalad unlock` before we can modify them.
When we run `datalad save` to save our changes, the file will be locked again.
Even though DataLad reports unlocking as a file modification, it will only create a new entry in the commit history if the file actually changed.

**Note for Windows**: The Windows file system does not support file locking in the same way that Linux/macOS does. Instead, Windows duplicates the data and keeps one copy in the working directory and one backup copy for safety in the `.git` folder. This has the advantage that you don't need to unlock files before modifying them, but it also makes your dataset twice as big!
Another consequence is that DataLad is creating a separate commit for this adjustment, so the most recent entry in your `git log` will always show the message *"git-annex adjusted branch"*. This means that, to get the most recent commit you made to the dataset, you have to look at the second-to-last entry in `git log`.

### Exercises

In the following exercises, we are going to inspect the `git log` to view the history of our dataset. We are also going to modify existing files by unlocking them and saving the changes to the commit history. Here are the commands you need to know:

| Code | Description |
| --- | --- |
| `git log` | Display the commit history of the repository |
| `code file.txt` | Open `file.txt` in VSCode |
| `datalad unlock data/` | Unlock the file content of the `data/` folder |
| `datalad unlock file.txt` | Unlock the file content of `file.txt` |
| `datalad status` | Show changes in the current dataset |
| `datalad save` | Save untracked changes and lock unlocked file contents |
| `datalad save -m "message"` | Save untracked changes with a commit message |

**Exercise**: Display the `git log` to view the commit history of the dataset


```python
git log
```

**Exercise**: Use `datalad unlock` to unlock the file `adelie/table_219.csv`, then check `datalad status`.


```python
datalad unlock adelie/table_219.csv
datalad status
```

**Exercise**: Open the file in VSCode (`code adelie/table_219.csv`), delete some rows, save the file, then use `datalad save` to save the changes with a message (`-m`) and use `git log` to see your commit.


```python
code adelie/table_219.csv
datalad save -m "Edit adelie/table_219.csv: remove some rows"
git log
```

**Exercise**: Unlock `gentoo/table_220.csv` and run `datalad save`. Check `datalad status` and `git log`. The status should be clean and there should be no new entry in the commit history if the file wasn't modified.


```python
datalad unlock gentoo/table_220.csv
datalad save
datalad status
git log
```

## Creating Siblings on Online Repositories

### Background

One of DataLad's most useful features is the ability to manage multiple copies, or **siblings** of the same dataset. Siblings allow you to share your data and function as backups. When a dataset has multiple siblings not every sibling has to contain all of the data. When downloading a file, DataLad will simply try all of the siblings until it finds one that has the data.

In this section, you will create sibling repositories on the Open Science Framework (OSF) and GitHub. These siblings are complementary: OSF provides the storage for the dataset and all of its versions, while the GitHub repository can be browsed and shared easily. 

### Exercises

In the following exercises, you will create sibling repositories on GitHub and OSF. For this, DataLad requires **access** tokens for your accounts. The exercises contain screenshots to show how they can be generated.

| Command | Description |
| --- | --- |
| `datalad osf-credentials` | Authenticate DataLad with OSF using an access token |
| `datalad create-sibling-osf --title my-repo` | Create a new OSF repository called `my-repo` |
| `datalad create-sibling-github my-repo` | Create a new GitHub repository called `my-repo` |
| `datalad push --to osf` | Push the dataset content to the sibling named `osf` |
| `datalad push --to github` | Push the dataset content to the sibling named `github` |
| `datalad osf-credentials --reset` | Reset your OSF access token |
| `datalad credentials remove api.github.com` | Reset your GitHub access token |
| `datalad siblings` | List all siblings of the current dataset |
| `datalad update -s github --merge` | Merge new commits from the `github` sibling |


To create an OSF sibling for your dataset, you must first generate an **access token** that allows DataLad to access your OSF account. Log in to [OSF](https://osf.io/), go to "Settings" > "Personal Access Token" (red box), and click on "Create Token" (blue box).

![](img/osf1.png)

Give the token a name of your choice, grant it full read and write permissions, and click on "Create Token".

![](img/osf2.png)

Copy the token. Be careful: you won't be able to see the token again once you close the window!

![](img/osf3.png)

**Exercise**: Run the `datalad osf-credentials` command and paste the access token when prompted. You should see `osf_credentials(ok): [authenticated as <your name>]`



```python
datalad osf-credentials
```

**Exercise**: Use `create-sibling-osf` to publish your dataset to OSF with a `--title` of your choice.


```python
datalad create-sibling-osf --title "penguins-dataset"
```

**Exercise**: Push `--to osf` and inspect your OSF repository in the browser. The OSF repository will not contain the data in a human-readable form. You can push to and pull from this repository, but you can't explore files in the browser (**Note**: only files that you have downloaded with `datalad get` will be pushed).


```python
datalad push --to osf
```

To have a repository that can be easily browsed and shared, we will next create a sibling on GitHub. GitHub can't store the actual file content, but that is not a problem - when we clone from GitHub, DataLad will automatically fetch the file content from another source that has it. To create a GitHub sibling, we need another access token. Log in to [GitHub](https://github.com/), click on your user icon in the top-right, and select "Settings".

![](img/gh1.png)

Then, select "Developer Settings" at the bottom of the menu on the left.

![](img/gh2.png)

Select "Generate New Token (classic)".

![](img/gh3.png)

Grant full access to repositories, create the token, and paste it when prompted. Be careful: you won't be able to see the token again after closing this window.

![](img/gh4.png)

**Exercise**: Create a sibling on GitHub (you do not need the `--title` flag here) and push to it. Then, open the new repository in the browser.


```python
datalad create-sibling-github penguins
datalad push --to github
```

**Exercise**: Use the `datalad siblings` command to list all siblings of the current dataset.

**Exercise**: Open the GitHub sibling in the browser, edit the `README.md` and commit the changes. Then run `datalad update -s github --merge` to merge the changes into your local dataset and inspect the `README.md` to confirm the change was merged.


```python
datalad update -s github --merge
```

**Bonus**: Go to a different folder on your computer, use `datalad clone` to clone the dataset from GitHub, and use `datalad get *` to download all of the file content.


```python
cd /tmp
datalad clone https://github.com/annbuchwald/penguins
cd penguins
datalad get *
```

## Creating Local Backups

### Background

Siblings can not only be used to publish your data online, they can also be used to create backups on an external drive or share data with collaborators via a local server.
To do this, we can simply initialize a `--bare` git repository at the desired location and add it as a sibling to our DataLad dataset. Bare means that the git repository has no working tree - the contents that are normally hidden in the `.git` folder are in the main directory. The absence of a working tree prevents issues of synchronization and accidental overwriting when pushing to and pulling from the repository.
While a bare repository is not suited for editing files directly, it can provide a common endpoint for multiple collaborators.

### Exercises

In the following exercises, you are going to initialize a `--bare` git repository and add it as a sibling to your dataset.
If your setup allows it, you can create the sibling repository on a separate drive, to mimic creating a backup of your data.
Once the sibling is created we can clone it and see how changes can propagate across siblings.
Here are the commands you need to know:

| Command | Description |
| --- | --- |
| `git init --bare ./mydir`| Create a `--bare` repository called `mydir` in the current directory |
| `datalad siblings` | List all siblings of the current dataset |
| `datalad siblings add --name new --url <path>` | Add the repository at the URL as a new sibling with the name `new` |
| `datalad siblings remove --name new` | Remove the sibling with the name `new` |
| `datalad push --to new` | Push the dataset content to the sibling named `new` |
| `datalad save` | Save all untracked changes in the current dataset |
| `datalad update -s new --merge` | Merge updates from sibling `new` |

**Example**: Initialize a `--bare` git repository in a new directory (**Note**: files in `/tmp` are temporary and not suited for actual backups).


```python
git init --bare /tmp/penguins-backup
```

    Initialized empty Git repository in /tmp/penguins-backup/


**Exercise**: Create a `--bare` git repository in another folder (preferably on a different drive).


```python
git init --bare /tmp/penguins-backup-2
```

**Example**: Add `/tmp/penguins-backup` as a sibling to the current penguins dataset with the name `backup`.


```python
datalad siblings add --name backup --url /tmp/penguins-backup
```

    .: backup(-) [/tmp/penguins-backup (git)]


**Exercise**: Add the `--bare` repository you created in a folder of your choice as a sibling to the current penguins dataset with a name of your choice.


```python
datalad siblings add --name backup-2 --url /tmp/penguins-backup-2
```

**Exercise**: List all siblings of the current penguins dataset.


```python
datalad siblings
```

**Exercise**: Push `--to` one of the backup siblings **TWICE**. You need to push twice because on the first push, DataLad initializes the data storage, and the second push transfers the actual file content.


```python
datalad push --to backup-2
datalad push --to backup-2
```

## Bonus: Telling DataLad what to Track

### Background

Under the hood, DataLad uses a program called `git-annex` to handle the content of files. By default, DataLad will use `git-annex` to handle the content of every single file in your dataset. However, this is not always desirable. For example, you may not want to annex small text files like code to avoid having to unlock them for every edit. We can tell DataLad which files should be annexed by editing the `.gitattributes` file. Let's look at the `.gitattributes` file in the penguins dataset:


```python
cat .gitattributes
```

    * annex.backend=MD5E
    **/.git* annex.largefiles=nothing


There are two lines in this file:
- `* annex.backend=MD5E`: tells git-annex to use the `MD5E` backend for generating file hashes
- `**/.git* annex.largefiles=nothing`: tells git-annex not to annex the `.git` folder (because that folder is where the annexed contents are stored)

We usually don't want to edit these default values. Instead, we want to add lines to `.gitattributes` to specify which contents should and shouldn't be annexed. Note that changes in the configuration will not automatically be applied to files that are already tracked. Thus, it is best to configure `.gitattributes` right after initializing the dataset, before data is added.

### Exercises

In the following exercises, we are going to modify our dataset's `.gitattributes` to apply some custom configurations.
Here are the different commands and configuration options that you'll need:

| Code | Description |
| --- | --- |
| `* annex.largefiles=(mimeencoding=binary)` | Only annex files with a `binary` encoding |
| `myfile.pdf annex.largefiles=nothing` | Don't annex `myfile.pdf` |
| `* annex.largefiles=(largerthan=5kb)` | Only annex files whose size exceeds 5KB |
| `* annex.largefiles=((mimeencoding=binary)or(largerthan=5kb))` | Only annex binary files and files greater than 5KB |
| `code .gitattributes` | Open `.gitattributes` in VSCode |
| `git annex unannex <files>` | Unannex the content of the given files |
| `git rm --cached <files>` | Remove files from the Git index while keeping them in the working tree |
| `git annex add <files>` | Add files to git-annex according to the current `.gitattributes` configuration |
| `git annex whereis <files>` | Show the location of the annexed file content (empty if the file isn't annexed) |

**Example**: Get the location of the annexed file content of `gentoo/table_220.csv`. The entry marked with `[here]` refers to the local annexed copy.



```python
git annex whereis gentoo/table_220.csv
```

    whereis gentoo/table_220.csv (5 copies) 
      	34176649-4bdc-4685-84cf-96ba947110b5 -- olebi@iBOTS-7:/tmp/datalad/penguins [here]
      	83f2736d-70a7-412a-89df-01ee16326913 -- jsheunis@bnbmac48.local:~/Documents/psyinf/edu/palmerpenguins
      	92813bb4-4217-4837-b618-85d633eb9565 -- [archivist]
      	9948e337-6ea4-4df5-81a6-ef4709b6121f -- git@2f026f1d3470:/var/lib/gitea/git/repositories/jsheunis/palmerpenguins.git [origin]
      	d510bb94-be2a-41fc-aea5-b19ba3a680fa -- [osf-storage]
    
      archivist: dl+archive:MD5E-s17506--7c5573ab04ac7055a71197909e073894.5.zip#path=table_220.csv&size=18872
    ok


**Exercise**: Get the location of the annexed file content of `examples/gentoo.jpg`.


```python
git annex whereis examples/gentoo.jpg
```

**Exercise**: Open `.gitattributes` in VSCode and add the line
```
**/*.jpg annex.largefiles=nothing
```
to avoid annexing JPG files.


```python
code .gitattributes
```

**Example**: Unannex `examples/gentoo.jpg` and save to apply the changed configuration.


```python
git annex unannex examples/gentoo.jpg
datalad save -m "unannex gentoo.jpg"
```

    unannex examples/gentoo.jpg ok
    (recording state in git...)
    Total: 0.00 datasets [00:00, ? datasets/s]
    Total:   0%|                                   | 0.00/4.81M [00:00<?, ? Bytes/s][A
    [1;1madd[0m([1;32mok[0m): examples/gentoo.jpg ([1;35mfile[0m)             [A
    [1;1madd[0m([1;32mok[0m): .gitattributes ([1;35mfile[0m)                  
    [1;1msave[0m([1;32mok[0m): . ([1;35mdataset[0m)                           
    action summary:                                                                 
      add (ok: 2)
      save (ok: 1)


**Exercise**: Unannex the other JPG files and save to apply the changed configuration (**Hint**: use `examples/*.jpg` for all `.jpg` files).


```python
git annex unannex examples/*.jpg
datalad save -m "unannex all jpg files"
```

**Exercise**: Get the location of the annexed file content of `examples/gentoo.jpg` again. It should not return anything because the file is no longer handled by git-annex.


```python
git annex whereis examples/gentoo.jpg
```

**Exercise**: A configuration that is often useful is to only annex binary files (e.g. data) and leave text files, such as code, tracked directly by Git. Open `.gitattributes` in VSCode and change the last line to
```
* annex.largefiles=(mimeencoding=binary)
```
so that only binary files will be annexed.


```python
code .gitattributes
```

**Exercise**: Unannex all CSV files (`**/*.csv`) and save to apply the changed configuration. Now you should be able to edit CSV files without having to unlock them.


```python
git annex unannex **/*.csv
datalad save -m "unannex all csv files"
```

**Exercise**: Often, large text files, such as large CSV tables, are better left to git-annex. Open `.gitattributes` in VSCode and change the last line to
```
* annex.largefiles=((mimeencoding=binary)or(largerthan=5kb))
```
so that files greater than 5kb (even if they are not binary encoded) will be annexed. Then, run the code below to apply the updated configuration to existing CSV files.


```python
code .gitattributes
```


```python
git annex unannex **/*.csv
git annex add **/*.csv
datalad save -m "reannex large csv files with updated config"
```
