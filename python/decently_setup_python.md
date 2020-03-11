# Decently Setup Python Environments on Mac OS X

At the time of writing, Mac OS X comes with a stock Python2.
However, even though you are still working on Python2(deprecated!) for whatever reason, to play safe avoid touching the stock one.

To rescue, here are some decent approaches, with pros and cons.


## Official

For most releases of Python, there are official [installers](https://www.python.org/downloads/mac-osx/) available.
Simply by downloading and executing them, Python environments are installed with symbolic links created under `/usr/local/bin/`.

Not so straightforward as installation however, to set the default among all versions that are installed, you probably have to
```
ln -sf /Library/Frameworks/Python.framework/Versions/3.8 /Library/Frameworks/Python.framework/Versions/Current
```
and
```
ln -sf /usr/local/bin/Python3.8 /usr/local/bin/Python3
```
or
```
alias python3='/usr/local/bin/Python3.8'
```

Neither is there an officially managed way to uninstall.
Taking Python3.8 as an example again, you may need such manual work.
```
sudo rm -rf /Applications/Python\ 3.8/
sudo rm -rf /Library/Frameworks/Python.framework/Versions/3.8/
```
Apart from that, you may want to remove, as well, all symbolic links under `/usr/local/bin/` that are pointing to Python3.8.

Another potential drawback of this approach is that different releases under the same minor version(e.g. 3.8.1 and 3.8.2) would overwrite each other.
As a result, switching forward and backward between them can be awkward, though such use cases are rare.


## Homebrew

[Homebrew](https://brew.sh/) is an increasingly popular package manager.
At the time of writing, it offers only the latest releases of Python3.7 and Python3.8, which are available as `python` and `python@3.8` "formulae" respectively.

Apparently, the linkage is volatile between Homebrew formulae and Python versions.
We can expect formulae `python` being linked to Python3.8 in the future. And at that time, formulae `python@3.8` might no longer be available.


To install Python3.7,
```
brew install python
```
As a main formulae, `python` takes the privilege to create symbolic links under `/usr/local/bin/`.


Similarly, to install Python3.8,
```
brew install python@3.8
```
As an alternative formulae, `python@3.8` is installed "hidden" to avoid potential conflict.
To expose it, you are advised to
```
export PATH="/usr/local/opt/python@3.8/bin:$PATH"
```
This sets Python3.8 to default as well.


*Please be mindful that `brew upgrade` in the future would potentially replace them with later releases, or even upgrade Python3.7 into a higher minor version, which may or may not please you.*


Taking advantages of a package manager, uninstallation becomes as handy as
```
brew uninstall python
```
and
```
brew uninstall python@3.8
```


## pyenv

[`pyenv`](https://github.com/pyenv/pyenv#installation) is a dedicated Python version manager, which offers all Python releases.
It makes use of [`shim`](https://github.com/pyenv/pyenv#understanding-shims) to isolate each installed version and switch between them.
Thus, different releases under even the same minor version are able to coexist.

On Mac OS X, it highly relies on Homebrew to silently install additional formulae, such as `readline` and `openssl`, before installing Python.


To install Python3.8.2,
```
pyenv install 3.8.2
```
Initially, all versions are installed "hidden".
And it allows to expose multiple versions at the same time, with the first being the default.


To expose both 3.8.2 and 3.6.10, setting 3.8.2 to default,
```
pyenv global 3.8.2 3.6.10
```
As a result, Python3.8.2 is accessible by both `python3` and `python3.8`; while Python3.6.10 is only accessible by `python3.6`.


It allows you to list all installed versions,
```
pyenv versions
```


To uninstall 3.8.2,
```
pyenv uninstall 3.8.2
```


## MacPorts

[MacPorts](https://www.macports.org/install.php) is another popular package manager.
It tries hard to maintain an independent environment under `/opt/local/`, leaving the rest of the system untouched.

The official guide might be a bit misleading though.
The installation of MacPorts itself does not require `Xcode` actually. `Xcode command Line Tools` would suffice.

MacPorts offers only the latest release for each minor version of Python.

Being different from all the above, `pip` are separately offered.


To install Python3.8,
```
sudo port install python38 py38-pip
```
This creates symbolic links under `/opt/local/bin/`.


To set Python3.8 to default,
```
sudo port select python3 python38
sudo port select pip3 py38-pip
```
and/ or
```
sudo port select python python38
sudo port select pip py38-pip
```


And to uninstall Python3.8,
```
sudo port uninstall py38-pip
sudo port uninstall python38
```


## Summary

Here is a comparison of the above mentioned Python providers.

| | Official | HomeBrew | HomeBrew w/ pyenv | MacPorts |
|-|:-:|:-:|:-:|:-:|
| Version availability | Most releases | Latest releases for recent minors | All releases | Latest releases for all minors |
| Managed version switch |  :worried: | :worried: | :smile: | :smile: |
| Managed uninstallation | :worried: | :smile: | :smile: | :smile: |
