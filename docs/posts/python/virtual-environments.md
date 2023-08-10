---
title: Python and Virtual Environments
slug: python-virtual-envs
date: 2023-08-09
draft: true
categories:
  - Python
---

  [virtualenvwrapper]: https://virtualenvwrapper.readthedocs.io/en/latest/install.html
  [Material for Mkdocs]: https://squidfunk.github.io/mkdocs-material/
  [could not find virtualenv]: https://stackoverflow.com/questions/31274642/error-virtualenvwrapper-could-not-find-virtualenv-in-your-path

# Python and Virtual Environments
Setting up virtual environments, which is a necessity with Python has always been a pain in my
<!-- more -->

my arse. I have tried at least 3 different methods of keeping track of the virtual environments and have recently settled with [virtualenvwrapper] as my environment of choice.

In a another post, I mentioned how I had switched laptops and therefore setting up my dev environment from scratch again. A part of my development environment has been what others refer to knowledge management. For my knowledge management process, I use this blog which in turn uses [Material for Mkdocs] as I have found that this framework works and looks great for people who blog about code. 

Setting up [virtualenvwrapper] was a bit more of a pain than I feel it should have been and therefore I am documenting my steps to get it installed on Ubuntu:

1. I only had Python3 installed and so I made a link in /usr/bin/ for 'Python' to /usr/bin/pyhton3.10.
```bash
ln -s /usr/bin/python3.10 /usr/bin/python
````
2. Edited my .bashrc by adding the following lines:
````bash
export WORKON_HOME=$HOME/.virtualenvs
export PROJECT_HOME=$HOME/.virtualenvs_projects
export VIRTUALENVWRAPPER_VIRTUALENV=/home/user/.local/bin/virtualenv
source /home/user/local/bin/virtualenvwrapper.sh
````
!!! note
	The VIRTUALENVWRAPPER_VIRTUALENV export was needed to when encountering [could not find virtualenv] error.
````bash title="Create mkdocs material enviroment"
source /home/user/.bashrc
mkvirtualenv blog
pip install mkdocs-material
````
3. For me, I already my blog backed up on Github and so I just cloned it, and then if I'm not already in the python environment:
````bash
workon blog
mkdocs serve
````

