<h1>Scripting Language Evironment Management Guide</h1>

Anytime on our system we deal with a programming language, especially scripting languages,
we have to correctly manage environments, there are some conventions used by programmers
useful to install on the system different versions of the same programming language interpreter,
for example what if we want to install ruby 2.2 and ruby 2.3 ? Is this possible ?
What if we want to install two environments of perl 5.22 but with different sets of library ?
What if we want to install a certain program but do not want to install all the dependencies
needed to run the program on the system, but only locally ?
This guide tries to answer all these questions.

<h2>PERL</h2>
To manage different versions of perl we can use perlbrew or plenv, i use perlbrew
The only things to install on system perl is:

```$cpan App::perlbrew```

this will install perlbrew, this is the only program needed to install,
with system perl, or it may be even avoided by installing it manually

```$perlbrew init```

this command should be executed by each user which wants to use perlbrew
for zshells we should append to the .zshrc file the line:
source ~/perl5/perlbrew/etc/bashrc
then it is adviced to stick with perlbrew to install everything one needs
another package used a lot once perlbrew is installed is cpanminus, to install it we do:

```$perlbrew install-cpanm ```

it is adviced to install cpanm from perlbrew for the different versions

```$cpan App:cpanminus ```

for completeness, here we report the command a system wide cpnaminus
	

```$instmodsh ```

this will list currently installed modules on system perl


```$perlbrew available ```

list available perl versions that may be installed

```$perlbrew install perl-5.24.0 ```

this install the specified perl version
perlbrew supports two notations to denote perl versions:
	- long notation: 	perl-5.22.2
	- short notation: 	5.22.2


```$perlbrew list ```

list currently installed perl versions

```$perlbrew switch perl-5.22.2 ```

switch to the specified perl version, this must be available in "perlbrew list", the switch
							
changes the default perl version

```$perlbrew use 5.22.2 ```

the use command changes the perl version only for the current terminal session

```$perlbrew list-modules ```

this will list currently installed modules on user perl
now the modules we view are the one installed on the perl versions listed with "perlbrew list"
notice that these are the equivalent of rvm "global" gemsets, so whichever module we install
we are installing it globally even for different projects, so here we advice only to install
fundamental modules, such as Modern::Perl or other modules which we commonly use
in order to create different instances we can create the so called "lib"s so we do:

```$perlbrew lib create default ```

in this case we created with the current perl version selected a lib called "default"
we can switch to this lib with

```$perlbrew switch 5.22.2@default ```

this switches to the default environment o perl 5.22.2
now we can install modules and not be contamined with the other perl versions

```$cpanm Module::Name ```

this will install the module

```$cpanm -U Module::Name ```

this will remove the module
to remove a lib environment we can do:

```$perlbrew lib delete 5.22.2@default```



to search for packages, we can do the following things:

```$cpan # once inside we do "reload index"```

then we have to zgrep our 02package file, so we do:

```$locate 02package ```

once found we do:

```$zgrep 'WWW' path/to/02package.gz```

if we use this function a lot we could script a function or create an alias to search.

We can store dependencies of a project by creating a file with the various dependencies,for example:

```$echo 'requires "Mojolicious";' >> cpanfile```

```$echo 'requires "Net::Frame::Device";' >> cpanfile```

now if a user wants to install the dependencies, just needs to do:

```$cpanm --installdeps .```

  
<h2>PYTHON</h2>
To manage different versions of python we can use different things, i use pyenv

```$pip search keyword ```

this searches for modules which are related to the specified keyword

```$pip install moduleName ```

installs the specified module

```$pip list ```

lists currently installed modules

```$pip freeze --all```

analogous to "pip list"

```$pip uninstall moduleName ```

uninstalls the specified module
to manage different perl environments we can use virtualenv is a tool to create isolated Python environments. 
virtualenv creates a folder which contains all the necessary executables to use the 
packages that a Python project would need, let's see the basics:

```$pip install virtualenv ```

we first install virtualenv

```$cd my_project_folder ```

we cd to the directory project

```$virtualenv my_proj ```

this will create a python virtual environment called "my_proj"

```$virtualenv -p /usr/bin/python2.7 my_proj ```

this will create a python virtual environment with a selected python version

```$source my_proj/bin/activate ```

this will activate the environment
now we can install packages as usual

```$pip install requests```

if we are done working with this environment we do:

```$deactivate```

To delete a virtual environment, just delete its folder. (In this case, it would be rm -rf venv.)
In order to keep your environment consistent, it’s a good idea to “freeze” the current state of 
the environment packages. To do this, run:

```$pip freeze > requirements.txt```

Later it will be easier for a different developer (or you, if you need to re-create the environment) 
to install the same packages using the same versions:

```$pip install -r requirements.txt```

there are other tools which simplify all this process, such as virtualenvwrapper and autoenv, which 
automatically activates python environments anytime we cd into a virtualenv directory
if we want something more similar to perlbrew or rvm we should look into pyenv


<h2>RUBY</h2>
To manage different versions of ruby we can use rvm or rbenv, let's see something with rvm:

In order to list the available versions of ruby that can be downloaded:

```$rvm list known```

Now from the list let's say we are interested in the version 2.3 of ruby, then we can install it with: 

```$rvm install 2.3```

Now we can view the locally installed versions of ruby we can do:

```$rvm list```

Let's install another ruby version:

```$rvm install 2.2.4```

In order to switch to a specific version of ruby for the current session we do:

```$rvm use 2.2.4 ```

obviously the version must be one of the locally installed versions.


In order to list the available and currently selected gemsets for the current version of ruby we do:

```$rvm gemset list ```

we can think about a gemset as a set of modules.
For example we could have for ruby 2.2 different gemsets, by default there are two gemsets: 
	-default
	-global


In order to list all the current versions of ruby with various gemsets we do:

```$rvm list gemsets ```
 
this can be very useful to get a big picture of the situation of various ruby environments
on the system, and we can even inspect for gemsets we don't use anymore.


In order to set the default version of ruby we do:

```$rvm use --default 1.9.3 ```

To show the currently selected (and working) version of ruby in use we do:

```$rvm current ```

In order to switch to the default ruby version we do:

```$rvm default ```


In order to uninstall and remove the sources of a specific version of ruby we do:

```$rvm remove 2.3 ```

notice that this will not remove or touch in any way any gemsets.


```$gem help commands```
show the available commands

<h4>Install, Remove and Search for Packages</h4>

To install a gem (i.e. ruby name for module) we do:

```$gem install moduleName```

In order to list the locally installed gems for the current environment we do:

```$gem list --local```

To search for a package we do:

```$gem search keyword```

To search for a package using keywords we do:

```$gem search --details keyword```

we add details to each package, note that this operation takes longer.

To uninstall a module we do:

```$gem uninstall moduleName```

To search for specific modules we can even use regex:

```$gem search '^rails$'```

this will find only one match, against plain 'rails' so search accepts regexes.

<h4>Installing a gemset only for a specific project</h4>

The first step involves choosing a version of ruby:

```$rvm use 2.3 ```

then we create a new gemset for the selected ruby version:

```$rvm gemset create my_project_name```

now to check the current gemsets we do:

```$rvm gemset list```

now we should see our gemset in the list, we can select our gemset with:

```$rvm gemset use my_proj```

now we can install whatever gem we want, in case we want to delete the gemset we do:

```$rvm gemset delete my_proj```

this will delete the entire gemset called my_proj,
remember that the global gemset is the set of packages which are installed in all others gemset
so when we create a new gemset all the gem installed in the global gemset will be transferred
to the new created gemset.


<h4>Create Project Based Ruby Version and Gemset</h4>
once we have created a project dir with:
mkdir my_own_proj; cd my_own_proj
we can now create two files, which will contain
correspectively ruby version and ruby gemset name, so we do:


```$echo "2.3" > .ruby-version```

```$echo "my_own_proj" > .ruby-gemset```

now anytime we will cd to the directory we will get the
an automatic change of ruby version and gemset, this is
very adviceable for mantaining projects etc...

once we had deleted the project we can execute:

```$rvm gemset delete ruby-2.3@my-own-proj```

this will delete the entire gemset.


<h4>Uninstall RVM</h4>

In order to uninstall rvm we just execute:

```$rvm implode```

many other options are available, for example we can migrate gemsets and many other things,
refer to the official doc for additional infos.

