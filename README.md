# Vagrant environment for Symfony2

This project provides a virtual environment for Symfony2 development using
[Vagrant](https://vagrantup.com).

This is a fork from: https://github.com/kleiram/vagrant-symfony

## What's in the box?

When you start Vagrant, this environment will provide the following tools
that can be useful when developing for Symfony2:

- Git
- cURL
- MySQL
  * Username: `root`
  * Password: empty
- nginx
- PHP
- PHP-FPM
- APC
- PEAR
- XDebug
- nodejs
- bower

inactive: 
- SQLite
- RabbitMQ


Additionally, it will create a MySQL database called `symfony` that a Symfony2
application can connect to without any configuration.

## Usage and requirements

### Ansible

[Ansible](http://ansible.com) is used to provision the virtual machine, so you
must have that installed. Follow the
[installation instructions](http://docs.ansible.com/intro_installation.html#installation).

### Usage

Installation is as easy as cloning a GitHub project:

```
$ cd your-symfony-project
$ git clone https://github.com/Wichteldesign/vagrant-symfony.git vagrant
```

Or, if you're using Git already in your project, you can use it as a submodule:

```
$ cd your-symfony-project
$ git submodule add https://github.com/Wichteldesign/vagrant-symfony.git vagrant
```

After the project is added, you can start the environment like this:

```
$ cd vagrant
$ vagrant up
```

Starting the VM might take some time, since it will download the entire box
and additional applications/library. When the VM is done setting up, point
your browser towards [http://192.168.33.10](http://192.168.33.10) and there you
have it: Symfony2.

#### Note

If you're using Windows, you have to modify the `Vagrantfile` a little bit to
make it all work (since Windows doesn't support NFS). Replace the following
lines in the Vagrantfile:

```ruby
config.vm.synced_folder ".",  "/vagrant", id: "vagrant-root", :nfs => true
config.vm.synced_folder "..", "/var/www", id: "application",  :nfs => true
```

with:

```ruby
config.vm.synced_folder ".",  "/vagrant", id: "vagrant-root"
config.vm.synced_folder "..", "/var/www", id: "application"
```

## Troubleshooting

### I'm not allowed to access my site?

If you visit your site and you get the following error message:

```
You are not allowed to access this file. Check app_dev.php for more information.
```

You have to remove the following files from `web/app_dev.php`:

```php
if (isset($_SERVER['HTTP_CLIENT_IP'])
    || isset($_SERVER['HTTP_X_FORWARDED_FOR'])
    || !in_array(@$_SERVER['REMOTE_ADDR'], array('127.0.0.1', 'fe80::1', '::1'))
) {
    header('HTTP/1.0 403 Forbidden');
    exit('You are not allowed to access this file. Check '.basename(__FILE__).' for more information.');
}
```

### Why is my site so slow?

If your site is noticeably slow (and I'm talking hundreds of milliseconds), it's
because the caching and logging is taking it's time (NFS synchronization). One
way to fix this is by changing the cache folder in the `app/AppKernel.php` file.
At the end of the AppKernel class, add the following methods:

```php
public function getCacheDir()
{
    return '/tmp/symfony/cache/'. $this->environment;
}

public function getLogDir()
{
    return '/tmp/symfony/log/'. $this->environment;
}
```

This will change the location of the `cache` and `log` directories you normally
find in the `app` directory of Symfony to the `/tmp/symfony` directory. This
will speed up your site _a lot_. The downside is that you won't be able to check
the log from your host computer (the computer that's running Vagrant).

## To-do

Feel free to add these components and create a pull request!

## License

```
Copyright (c) 2014, Ramon Kleiss <ramon@cubilon.nl>
All rights reserved.

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are met:

1. Redistributions of source code must retain the above copyright notice, this
   list of conditions and the following disclaimer.
2. Redistributions in binary form must reproduce the above copyright notice,
   this list of conditions and the following disclaimer in the documentation
   and/or other materials provided with the distribution.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND
ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED
WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT OWNER OR CONTRIBUTORS BE LIABLE FOR
ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES
(INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES;
LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND
ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
(INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS
SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

The views and conclusions contained in the software and documentation are those
of the authors and should not be interpreted as representing official policies,
either expressed or implied, of the FreeBSD Project.
```
