# NI-Day1-Code-and-Data-Repositories


## Session Overview

Modern research projects are difficult to reproduce when code, data, and analysis steps are scattered across folders, renamed files, or undocumented local changes. This unit introduces version control as a practical foundation for transparent, collaborative, and reproducible neuroscience research.

In the homework, you will begin with online repositories for code and data. You will create and edit a GitHub repository, inspect its commit history, fork an existing project, and open a pull request. You will also create an OSF project, add metadata, choose a license, organize files and components, and link OSF with GitHub.

The first in-person session focuses on Git itself. You will learn how to initialize a local repository, track files, stage and commit changes, push work to GitHub, pull remote changes, inspect previous commits, compare versions, and undo changes using checkout, revert, and reset. Bonus material introduces .gitignore files and branches as tools for keeping exploratory work organized.

The next session extends version control from code to data. Using DataLad and git-annex, you will work with datasets whose files can be tracked by Git without storing all file contents directly in the repository. You will clone a dataset, retrieve and drop file contents, save changes, publish siblings to OSF and GitHub, and create local backups.

The final bonus session introduces provenance tracking for analysis pipelines. Instead of manually recording every command, you will use datalad run to capture commands, inputs, outputs, and commit history, making it possible to rerun individual steps or rebuild an analysis pipeline from earlier versions.

The aim of this unit is not to make you memorize every Git or DataLad command. Rather, it is to give you a working mental model of how research projects can be tracked, shared, restored, and reproduced from beginning to end.
