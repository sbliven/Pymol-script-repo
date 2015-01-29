PyMOL Script Repository
=======================
[![Code Health](https://landscape.io/github/Pymol-Scripts/Pymol-script-repo/master/landscape.svg?style=flat)](https://landscape.io/github/Pymol-Scripts/Pymol-script-repo/master)

This project aims to put collect and maintain useful PyMOL scripts. Scripts were
originally collected from the [PyMOLWiki](http://www.pymolwiki.org), and most
scripts continue to be documented on the wiki. 

For more information, see [git intro](http://www.pymolwiki.org/index.php/Git_intro)
on the wiki. Bugs can be reported via [github](https://github.com/Pymol-Scripts/Pymol-script-repo).


Loading scripts and plugins
============================

This repository contains two types of scripts:

1. Python scripts (.py)
2. Plugin Manager (python module directories)

The Plugin Manager is preferred for new scripts, as it provides an interface for enabling
and disabling individual plugins.


Python scripts
--------------

Python scripts consist of single python modules. They are located in the main
directory of the repository. Python scripts should work with any version of
PyMOL (since at least PyMOL 1.3) on any platform.

Python scripts can be loaded for a single session using the run command:

```bash
# Adds the `com` command
run ~/Pymol-script-repo/center_of_mass.py
```

Most scripts provide documentation, which can be accessed in PyMOL using the help command:

```
help com
```


Plugins
-------

The Plugin Manager was introduced in PyMOL 1.5. It is not available in MacPyMOL,
but is available on Macs by using the 
[X11 interface](http://www.pymolwiki.org/index.php/MAC_Install#X11_Hybrid).

Plugins and python scripts can be installed through the Plugin Manager's 'Install new plugin' tab. The options to install scripts directly from the pymol wiki or from github are particularly convenient. However, the downside of this method is that it will copy scripts into your PYMOL_HOME, so the plugins will have to be individually updated.

The following section describes a better method which automatically includes all scripts and plugins, and allows them to be easily updated with future changes.


Automatically loading all scripts
=================================

The following python code will automatically import all Plugin Manager plugins
(found in the 'modules' directory). The pymol_git variable should be modified
to point to the Pymol-script-repo directory.

```python
python

import sys, os

# Set as path to the Pymol-script-repo. Examples:
#pymol_git = "/usr/local/pymol-script-repo"
#pymol_git = os.path.abspath(os.path.join(os.path.expanduser('~'), 'Pymol-script-repo'))
pymol_git = os.path.abspath(os.path.join(os.environ['PYMOL_PATH'], 'Pymol-script-repo'))

# Add paths to sys.path so PyMOL can find modules and scripts
os.environ['PYMOL_GIT_MOD'] = os.path.join(pymol_git,'modules')
sys.path.append(pymol_git)
sys.path.append(os.environ['PYMOL_GIT_MOD'])

# Make setting changes to Plugin Manager
import pymol.plugins
pymol.plugins.preferences = {'instantsave': False, 'verbose': False}
pymol.plugins.autoload = {'apbs_tools': False}
pymol.plugins.set_startup_path([os.path.join(pymol_git, 'plugins'), os.path.join(sys.prefix, 'Lib', 'site-packages', 'pmg_tk', 'startup')])
pymol.plugins.preferences = {'instantsave': True, 'verbose': False}

python end
```

To install for a single user, the code can be placed in .pymolrc in your home
directory. Since .pymolrc uses PyMOL script syntax, surrounding
`python`...`python end` lines are necessary, as shown above.

To automatically enable plugins for all users, the code (without `python`
lines) can also be placed in a file called `run_on_startup.py` at the root
level of the pymol path. This will be executed for all users upon startup,
while still allowing users to manage plugins through the plugin manager.
If you are unsure where the pymol path is located, it can be determined from
running `print os.environ['PYMOL_PATH']` from within PyMOL.


