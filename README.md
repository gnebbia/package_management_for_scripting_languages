## Scripting Language Evironment Management Guide

Anytime on our system we deal with a programming language, especially scripting languages,
we have to correctly manage environments, there are some conventions used by programmers
useful to install on the system different versions of the same programming language interpreter,
for example what if we want to install ruby 2.2 and ruby 2.3 ? Is this possible ?
What if we want to install two environments of perl 5.22 but with different sets of library ?
What if we want to install a certain program but do not want to install all the dependencies
needed to run the program on the system, but only locally ?
This guide tries to answer all these questions.

### Python

To manage different versions of python we can use different things, i use pyenv,
in order to manage packages we should use "pip", well by default when we install
python we will have a much simpler package manager called "easy_install", so
we could use it to install pip and then use pip always. Notice that the 
repository containing all the python packages is called PyPi, and this is
equivalent to CPAN for Perl. Pip should always be used with user
privileges, and never with root/sudo. 
If we have problems installing packages with user privileges, we can try
forcing it by using the "pip install --user packagename" command, in practice
when we normally use pip it should have the "--user" option enabled by 
default, but if this is not true, we just specify it.

Let's see some basic pip commands:

```sh
pip search keyword 
```

this searches for modules which are related to the specified keyword

```sh
pip install moduleName
```

installs the specified module

To list currently installed modules

```sh
pip list
```

Or:

```sh
pip freeze --all
```

To uninstall a module we do:
```sh
pip uninstall moduleName 
```

#### Installing different python versions

I generally recommend to always use some kind of interpreter package manager,
for python a common choice is pyenv.

Pyenv allows us to not touch the operating system python interpreter and its
environment.

Whenever I am on a new machine I generally want to install pyenv in order to
work with python stuff.

Once pyenv is installed we can check the avaliable python versions to install
with:
```sh
pyenv install --list 
```

When we find the version we are interested in we can install it by ussuing a
simple:
```sh
pyenv install 3.6.5 # or whatever version you like
```

To list installed python versions we can issue:

```sh
pyenv versions
```

We can see that the python `system` version is also selectable (if we ever would
need it).
Now we can select the python version globally on the system with:

```sh
pyenv global 3.6.5
```

We can also temporarily set for the local shell environment a python version by
issuing a :
```sh
pyenv local 3.6.5
```

#### Virtual Environments

To manage isolated python environments we can use virtualenv.
There are many wrappers around virtualenv, but my suggestion is
to learn to use plain virtualenv, and only with practice maybe pass to
something more complex, actually virtualenv is not that difficult.
Virtualenv creates a folder which contains all the necessary executables to use the 
packages that a Python project would need.
Virtualenv ships by default with python3, but not with python2.

In order to install virtualenv for python2 we do:
```sh
pip install virtualenv 
```

Now we cd to the directory project where we want to create the virtualenv:

```sh
cd my_project_folder 
```


```sh
virtualenv env 
```
or if we are using python3 we can just do:

```sh
python -m venv env
```

this will create a python virtual environment called "env"
notice that we can give whatever name we like, a common convention
is to just call the directory containing all the needed things "env".

We can be more specific and tell virtualenv which python executable to use:
```sh
virtualenv -p /usr/bin/python2.7 env 
```


this will create a python virtual environment with a selected python version,
also remember in this case that if we are using pyenv to manage python versions,
we should include the full path to the local python version ~/.pyenv/###/###/###

In order to activate a virtual environment once we have the env directory
created, we do:

```sh
source env/bin/activate 
```
now we can install packages as usual and they will be isolated from the rest of
the python packages and python system packages.

For example we can install the famous `requests` package with a normal:
```sh
pip install requests
```

Remember to put the entire "env" directory in a git ignore file, if
we are using git.

When we are done working with the environment we can just do:
```sh
deactivate
```

To delete a virtual environment, just delete its folder. (In this case, it would be rm -rf env.)
In order to keep your environment consistent, it’s a good idea to “freeze” the current state of 
the environment packages. To do this, run:

```sh
pip freeze > requirements.txt
```


Later it will be easier for a different developer (or you, if you need to re-create the environment) 
to install the same packages using the same versions:

```sh
pip install -r requirements.txt
```

there are other tools which simplify all this process, such as virtualenvwrapper and autoenv, which 
automatically activates python environments anytime we cd into a virtualenv directory
if we want something more similar to perlbrew or rvm we should use pyenv.


### Perl
To manage different versions of perl we can use perlbrew or plenv, i use perlbrew
The only things to install on system perl is:

```sh
cpan -i App::perlbrew
```

this will install perlbrew, this is the only program needed to install,
with system perl, or it may be even avoided by installing it manually

```sh
perlbrew init
```

this command should be executed by each user which wants to use perlbrew
for zshells we should append to the .zshrc file the line:
source `~/perl5/perlbrew/etc/bashrc`
then it is adviced to stick with perlbrew to install everything one needs
another package used a lot once perlbrew is installed is cpanminus, to install it we do:

```sh
perlbrew install-cpanm 
```

it is adviced to install cpanm from perlbrew for the different versions

```sh
cpan App:cpanminus
```

for completeness, here we report the command a system wide cpnaminus
	

```sh
instmodsh
```

this will list currently installed modules on system perl


```sh
perlbrew available
```

list available perl versions that may be installed

```sh
perlbrew install perl-5.24.0
```

this install the specified perl version
perlbrew supports two notations to denote perl versions:
	* long notation: 	perl-5.22.2
	* short notation: 	5.22.2


```sh
perlbrew list
```

list currently installed perl versions

```sh
perlbrew switch perl-5.22.2
```

switch to the specified perl version, this must be available in "perlbrew list", the switch
							
changes the default perl version

```
perlbrew use 5.22.2
```

the use command changes the perl version only for the current terminal session

```
perlbrew list-modules
```

this will list currently installed modules on user perl now the modules we view
are the one installed on the perl versions listed with "perlbrew list"
notice that these are the equivalent of rvm "global" gemsets, so whichever
module we install we are installing it globally even for different projects, 
so here we advice only to install fundamental modules, such as Modern::Perl 
or other modules which we commonly use in order to create different 
instances we can create the so called "lib"s so we do:

```sh
perlbrew lib create default
```

in this case we created with the current perl version selected a lib called "default"
we can switch to this lib with

```sh
perlbrew switch 5.22.2@default
```

this switches to the default environment o perl 5.22.2
now we can install modules and not be contamined with the other perl versions

```sh
cpanm Module::Name
```

this will install the module

```
cpanm -U Module::Name
```

this will remove the module
to remove a lib environment we can do:

```sh
perlbrew lib delete 5.22.2@default
```

to search for packages, we can do the following things:

```sh
cpan 
# once inside we do 
reload index
```

then we have to zgrep our 02package file, so we do:

```sh
locate 02package
```

once found the file we can do:

```sh
zgrep 'WWW' path/to/02package.gz
```

if we use this function a lot we could script a function or create an 
alias to search. We can store dependencies of a project by creating 
a file with the various dependencies,for example:

```sh 
echo 'requires "Mojolicious";' >> cpanfile
```

```sh 
echo 'requires "Net::Frame::Device";' >> cpanfile
```

now if a user wants to install the dependencies, just needs to do:

```
cpanm --installdeps .
```


### Ruby

To manage different versions of ruby we can use rvm or rbenv, let's see something with rvm:

In order to list the available versions of ruby that can be downloaded:

```sh
rvm list known
```


Now from the list let's say we are interested in the version 2.3 of ruby, then we can install it with: 

```sh
rvm install 2.3
```


Now we can view the locally installed versions of ruby we can do:

```sh
rvm list
```


Let's install another ruby version:

```sh
rvm install 2.2.4
```


In order to switch to a specific version of ruby for the current session we do:

```sh
rvm use 2.2.4 
```


obviously the version must be one of the locally installed versions.


In order to list the available and currently selected gemsets for the current version of ruby we do:

```sh
rvm gemset list 
```


we can think about a gemset as a set of modules.
For example we could have for ruby 2.2 different gemsets, by default there are two gemsets: 
	-default
	-global


In order to list all the current versions of ruby with various gemsets we do:

```sh
rvm list gemsets 
```

 
this can be very useful to get a big picture of the situation of various ruby environments
on the system, and we can even inspect for gemsets we don't use anymore.


In order to set the default version of ruby we do:

```sh
rvm use --default 1.9.3 
```


To show the currently selected (and working) version of ruby in use we do:

```sh
rvm current 
```


In order to switch to the default ruby version we do:

```sh
rvm default 
```



In order to uninstall and remove the sources of a specific version of ruby we do:

```sh
rvm remove 2.3 
```


notice that this will not remove or touch in any way any gemsets.


```sh
gem help commands
```

show the available commands

#### Install, Remove and Search for Packages

To install a gem (i.e. ruby name for module) we do:

```sh
gem install moduleName
```


In order to list the locally installed gems for the current environment we do:

```sh
gem list --local
```


To search for a package we do:

```sh
gem search keyword
```


To search for a package using keywords we do:

```sh
gem search --details keyword
```


we add details to each package, note that this operation takes longer.

To uninstall a module we do:

```sh
gem uninstall moduleName
```


To search for specific modules we can even use regex:

```sh
gem search '^rails$'
```


this will find only one match, against plain 'rails' so search accepts regexes.

#### Installing a gemset only for a specific project

The first step involves choosing a version of ruby:

```sh
rvm use 2.3 
```


then we create a new gemset for the selected ruby version:

```sh
rvm gemset create my_project_name
```


now to check the current gemsets we do:

```sh
rvm gemset list
```


now we should see our gemset in the list, we can select our gemset with:

```sh
rvm gemset use my_proj
```


now we can install whatever gem we want, in case we want to delete the gemset we do:

```sh
rvm gemset delete my_proj
```


this will delete the entire gemset called my_proj,
remember that the global gemset is the set of packages which are installed in all others gemset
so when we create a new gemset all the gem installed in the global gemset will be transferred
to the new created gemset.


#### Create Project Based Ruby Version and Gemset

once we have created a project dir with:
mkdir my_own_proj; cd my_own_proj
we can now create two files, which will contain
correspectively ruby version and ruby gemset name, so we do:


```sh
echo "2.3" > .ruby-version
```


```sh
echo "my_own_proj" > .ruby-gemset
```


now anytime we will cd to the directory we will get the
an automatic change of ruby version and gemset, this is
very adviceable for mantaining projects etc...

once we had deleted the project we can execute:

```sh
rvm gemset delete ruby-2.3@my-own-proj
```


this will delete the entire gemset.


#### Uninstall RVM 

In order to uninstall rvm we just execute:

```sh
rvm implode
```

many other options are available, for example we can migrate gemsets and many other things,
refer to the official doc for additional infos.

