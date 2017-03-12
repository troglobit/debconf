Debconf HowTo
=============

This is a simple walkthrough of Debconf, go through each and every step
carefully, soon you will be using it without any trouble.

Best of Luck!


What is Debconf?
----------------

Debconf is a backend database, with a frontend that talks to it and
presents an interface to the user.  There can be many different types of
frontends, from plain text to a web frontend.  The frontend also talks
to a special config script in the control section of a debian package,
and it can talk to postinst scripts and other scripts as well, all using
a special protocol.  These scripts tell the frontend what values they
need from the database, and the frontend asks the user questions to get
those values if they aren't set.

Confused yet?  Let's go through the three files: `config`, `templates`,
and `script`.


Files
-----

To use Debconf in your project, you need these two files:

    debian/config     <--- Set eXecutable bit!
    debian/templates

The `config` dictates the order in which to pose questions, which are
defined in `templates`.  This latter file can also be translated, see
the official documentation for this.

Those two files only handle the input to the debconf db.  Usually you
want to use that information in the `postinst` script.

    debian/postinst   <--- In our example we provide `script`

This file is called *after* your package has been installed to the file
system.  It is here you create or modify any `*.conf` file with info
from the debconf db.  If you need to do some system configuration or
preparation *before* the package has been installed, use `preinst`, but
that also requires some more digging in the official documentation.

You also need to tell `dpkg-buildpackage` to include the `config` and
your `templates`.  This requires modifying two files:

    debian/control    <--- Add 'debconf (>= 0.2.17)' to Depends:
    debian/rules      <--- Add 'dh_installdebconf' to binary-*: rules


Templates
---------

The `templates` file contains all the notes, different questions you
want to ask the user, be it boolean(true/false), multiple answer, string
inputs, displaying notes, everything you want to display or ask the
user.

> P.S. make sure you leave a space in the begining of the Long
> Description, check the file in case you have any doubt.


Config
------

Next, decide what order the questions should be asked and the messages
to the user should be displayed from the template file we talked about
earlier.

Config script does all this, it has no other job than to display notes
and questions from template file and take input from the user in the
form of answers to the question it asked.


Script
------

The job of this script is to use the input stored in debconf
database. It all depends how you want to use the inputs, I jave just
printed the inputs stored.


Where to find the Debconf database?
-----------------------------------

Do not modify these files directly!

    /var/cache/debconf/config.dat
    /var/cache/debconf/templates.dat

`config.dat` stores all the template questions and their replies, notes
if the user has 'seen' the questions already, to avoid asking again and
again.

`templates.dat` stores all templates.

Use the following [magic commands][debconf] to work remove templates or
unset the 'seen' flag:

    echo PURGE | sudo debconf-communicate <packagename>
    echo UNREGISTER package/setting | sudo debconf-communicate
    echo FSET package/setting seen false | sudo debconf-communicate

To see the value of settings:

    echo GET package/setting | sudo debconf-communicate


How to run the scripts?
-----------------------

1. Change the permssion of the scripts to executable
2. run the first script `sudo ./config` .You need to use sudo in order
   to make changes to the debconf database
3. run the second script `sudo ./script`

That's all, best of luck again and enjoy your experience!

[debconf]: https://www.debian.org/doc/packaging-manuals/debconf_specification.html#AEN106
