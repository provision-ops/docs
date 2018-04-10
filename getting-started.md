# Getting Started

## Getting Started

The prerequisites for Provision are:

* PHP-CLI.
* Composer.
* A web & database server _or_ Docker and `docker-compose` \(Docker v17.05 or higher\).

Since we are still early in development, we recommend installing from source repository at [https://github.com/provision4/provision](https://github.com/provision4/provision).

```
$ git clone git@github.com:provision4/provision.git ~/Projects/provision4
$ cd ~/Projects/provision
$ composer install
$ sudo ln -s $PWD/bin/provision /usr/local/bin/provision
$ sudo ln -s $PWD/bin/provision /usr/local/bin/pro
```

### Provision Configuration

It will help to read some background on how Provision manages configuration:

1. The `provision` CLI has configuration we call the "console config".  This is configuration that just affects the behavior of the command line tool itself. It can be overwritten by creating a file in your $HOME folder, for example `/home/me/.provision.yml` or `/Users/me/.provision.yml` on a Mac.
2. The Provision console configuration is mostly automatically generated from your system. There are two important settings that you might want to override by creating a `.provision.yml` file: `config`_`path and contexts`_`path`.
3. The `config_path` setting is the parent folder for all others.
   1. If you have a `$HOME/.config` folder \(most desktop OS's do, Mac & Linux included\), the default will be `$HOME/.config/provision`. 
   2. If you don't have a `$HOME/.config` folder, the default will be `$HOME/config`.
4. The `contexts_path` is where the YML files representing your sites and servers are saved. The default contexts path is contexts inside the \`config\_path folder.
5. Each server context gets a folder created for it inside the `config_path`. All configuration for the server goes in this folder, such as Apache configuration.

{% hint style="success" %}
In Provision, sites and servers are called "contexts". You can think of them like Drupal nodes: they are just stored metadata about your server and sites, one file per site and server.
{% endhint %}

You can let Provision use the defaults, or you can create a `.provision.yml` file in your home folder to override these settings like so:

```text
# The default is in .config. This puts it in ~/.provision
config_path: /Users/pat/.provision
contexts_path: /Users/pat/.provision/contexts  
```

When you first run provision, it will check if these folders exist, and if they don't it will automatically run the `setup` command to walk you through creating them.

{% hint style="danger" %}
There is a bug currently where the first time run will throw an error about missing `config_path` folder. We are working on making it run the setup command automatically instead.

Create the `contexts_path` at the default locations to continue:

```bash
$ mkdir $HOME/.config/provision/contexts -p
```
{% endhint %}

Once those links are in place, you can run `provision` or `pro` to view the list of commands.

```
    ____                  _      _                __ __
   / __ \_________ _   __(_)____(_)___  ____     / // /
  / /_/ / ___/ __ \ | / / / ___/ / __ \/ __ \   / // /_
 / ____/ /  / /_/ / |/ / (__  ) / /_/ / / / /  /__  __/
/_/   /_/   \____/|___/_/____/_/\____/_/ /_/     /_/   
                                
Provision 4.x-dev

Usage:
  command [options] [arguments]

Options:
  -h, --help               Display this help message
  -q, --quiet              Do not output any message
  -V, --version            Display this application version
      --ansi               Force ANSI output
      --no-ansi            Disable ANSI output
  -n, --no-interaction     Do not ask any interactive question
  -c, --context[=CONTEXT]  The target context to act on.
  -v|vv|vvv, --verbose     Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug

Available commands:
  help      Displays help for a command
  list      Lists commands
  save      [add] Create or update a site, platform, or server.
  services  Manage the services attached to servers.
  setup     Configure provision for your system.
  shell     commands.shell.description
  status    Display system status.
  verify    Verify a Provision Context.

```

### Provision Setup command.

Run the provision setup command to get a guided tour and run a setup wizard:

```bash
$ pro setup
                                                                               
  Welcome to the Provision Setup Wizard!                                       
                                                                               

Console Configuration
=====================

 In this section, we will make sure your Provision CLI configuration and       
 folders are created.                                                          

 ! Provision CLI configuration file was not found at /Users/jon/.provision.yml

 Helpful Tips:                                                                 

 ➤ The ~/.provision.yml file determines the config_path and contexts_path settings.
 ➤ The config_path is where Provision stores server configuration. Each server gets a folder inside this path to store their configuration files, such as Apache virtualhosts. The default config path is /Users/jon/.config/provision
 ➤ The contexts_path is where Provision stores the metadata about your servers and sites, called Contexts. Contexts are saved as YML files in this folder. The default contexts path is /Users/jon/.config/provision/contexts
 ➤ When Provision asks you a question, it may provide a [default value]. If you just hit enter, that default value will be used.

 Where would you like Provision to store its configuration? [/Users/jon/.config/provision]:
 > 

 Where would you like Provision to store its contexts? [/Users/jon/.config/provision/contexts]:
 > 

 Would you like to create the file /Users/jon/.provision.yml ? (yes/no) [yes]:
```



### Provision Save

The provision save command will add sites and servers to the system. It is fully interactive, so you can just type `provision save` to get started. 

You must start with a server, so Provision will help make sure that's the first thing you create. 

Traditionally, the first server we add to Provision is called server\_master, but you can call it whatever you'd like.

{% hint style="info" %}
When Provision asks you a question, it often offers you a \[Default Value\], which will be inside the brackets \[like this\]. If you see this and are happy with that value \(or aren't sure what it means\), just hit _Enter_ and keep moving!
{% endhint %}

So, to create your first server, run the `provision save` command:

{% code-tabs %}
{% code-tabs-item title="\`\`\`" %}
```text
$ pro save

 Context name [server_master]:
 > 

 Using default option remote_host=localhost
 Using default option script_user=jon
 Using default option aegir_root=/Users/jon
 Using default option server_config_path=
 -------------------- -------------------------------------------- 
  Saving Context:      server_master                               
 -------------------- -------------------------------------------- 
  remote_host          localhost                                   
  script_user          jon                                         
  aegir_root           /Users/jon                                  
  server_config_path   /Users/jon/.config/provision/server_master  
  type                 server                                      
  name                 server_master                               
 -------------------- -------------------------------------------- 

 Save server context server_master to /Users/jon/.config/provision/contexts/server.server_master.yml? (yes/no) [yes]:
 > 

                                                                               
 [OK] Configuration saved to                                                   
      /Users/jon/.config/provision/contexts/server.server_master.yml           
                                                                               

 Add a service? (yes/no) [yes]:
 > 

```
{% endcode-tabs-item %}
{% endcode-tabs %}

### Add Server & Services

A "server" represents the system providing services to a site, such as "web" and "database".

{% hint style="warning" %}
**NOTE on User Experience: **Currently, you must add services individually to your server context. In the near future, the `setup` command will assess your system and offer pre-configured servers.

Thank you for your patience as we work to make things as streamlined as possible!
{% endhint %}

After the `provision save` command, Pro4 asks if you would like to add services, remember, just hit _Enter_ to answer Yes:

{% hint style="danger" %}
There is currently a bug that requires that the `http` server be added first. Please select "http" as your first service.
{% endhint %}

```text
Add a service? (yes/no) [yes]:
 > 

 Add Services

 Which service?:
  [db  ] Database Server
  [http] Web Server
 > http

 Which service type?:
  [nginx       ] NGINX
  [apacheDocker] Apache on Docker
  [php         ] PHP Server
  [apache      ] Apache
 > apacheDocker

 http_port (The port which the web service is running on.) [80]:
 > 9999

 web_group (Web server group.) [www]:
 > www-data

 Adding http service apacheDocker...

                                                                               
 [OK] Service saved to Context!                                                
                                   
```

### Native vs Docker

Provision works by writing Apache or NGINX configuration and dynamically creating databases.

Provision works on _native_ systems where multiple services are running in a single operating system, like your laptop or a traditional server.

Provision can also work using _docker_ systems where services are run inside Docker containers launched using _Docker Compose. _

The same configuration files are used for _native_ and _docker _systems.

{% hint style="info" %}
To use _Docker_ services, select `httpDocker` and `mysqlDocker` when adding services to your server. Otherwise, Provision assumes your services are running natively.
{% endhint %}

#### Native Services Setup

_Native _services work by linking Provision web server config into your systems Apache or NGINX configuration.

Once you have created a _Server_ context, and added a HTTP service, you will have some a folder and some files created in the `config_path` \(Defaults to `~/.config/provision/$SERVERNAME/apache.conf` or `~/.config/provision/$SERVERNAME/apache.conf`.

Link this file into your host's apache or nginx configuration folders. Every system varies slightly. Edit this command and run it for your system:

```text
# On MacOS:
sudo ln -s /Users/$USER/.config/provision/$SERVERNAME/apache.conf /private/etc/apache2/other/provision.conf
# On Linux (varies by OS)
sudo ln -s /home/$USER/.config/provision/$SERVERNAME/apache.conf /etc/apache2/conf-enabled/provision.conf
$ On Linux with NGINX
sudo ln -s /home/$USER/.config/provision/$SERVERNAME/nginx.conf /etc/nginx/conf.d/provision.conf
```

### Command: provision verify

Sites and servers get setup after you add the context using the `verify` command.

Run `provision verify` and select your server to check that it has access to everything it needs to launch Drupal:

```bash
$ provision verify

 Choose a context:
  [server_master] server server_master
 > server_master

Verify server: server_master
============================


Verify service: Web Server
--------------------------
 ✔ Writing web server configuration... DONE in 0.00s
 ✔ Checking web server configuration... DONE in 0.35s
 ✔ Restarting web server... DONE in 0.15s
```

{% hint style="info" %}
**TIP: Context Aliases**

When you run any command, if you do not specify a context, Provision will ask which context you would like to run on.

If you already know the context, you can run `provision @CONTEXTNAME verify` to run the command on that context.
{% endhint %}

### Adding Database Service

In order to rapidly launch a Drupal site, your Provision server context must have a `db` service attached. This service stores the root password of your database server so that Provision can create and destroy databases and assign permissions on the fly.

To add the `db` service to your Server, you can use the `provision services add` command. If you wish to use Docker, make sure to select `mysqlDocker` as your service type.

{% hint style="info" %}
When Provision asks you for the `master_db` setting, it needs to be in the MySQL DSN format, which looks like a URL. 

If using `mysqlDocker` service, the username and password will be extracted and used to create the containers. You must use `db` as the host.

If using native `mysql` service, the username and password must already be set in the MySQL Server.
{% endhint %}

{% hint style="warning" %}
The `db_grant_all_hosts` setting should be set to 1 if using Docker services.
{% endhint %}

{% hint style="warning" %}
The `db_port` option should only be set if you are using Docker and want to expose the database container to be accessible outside the docker-compose cluster. Otherwise, the port in the `master_db`  setting will be used.
{% endhint %}

```text
$ provision services add
pro services add

 Choose a context:
  [server_master] server server_master
 > server_master

 Add Services

 Which service?:
  [db  ] Database Server
  [http] Web Server
 > db

 Which service type?:
  [mysqlDocker] MySQL on Docker
  [mysql      ] MySQL
 > mysqlDocker

 master_db (server with db: Master database connection info, {type}://{user}:{password}@{host}):
 > mysql://root:RANDOMPASSWORD@db

 db_grant_all_hosts (Grant access to site database users from any web host. If set to TRUE, any host will be allowed to connect to MySQL site databases on this server using the generated username and password. If set to FALSE, web hosts will be granted access by their detected IP address.):
 > 1

 db_port (The port to run the database server on.):
 > 3307

 Adding db service mysqlDocker...

                                                                          
 [OK] Service saved to Context! 
```

### Add Site

Now that you have a working Server context providing `db` and `http` services, you can add a site.

Add new sites to your system using the same `provision save` command use for adding servers. When you call `provision save` all by itself, Provision will ask you if you want to update an existing context or create a new one.  

Run `provision save` or `provision add` \(an alias\) to add a new site to your system:

```bash
pro save

 Choose a context:
  [server_master] server server_master
  [new          ] Create a new context.
 > new

 Context Type?:
  [server  ] Server
  [platform] Platform
  [site    ] Site
 > site

 Context name:
 > mskcc.org

 Checking service requirements for context type site...                   

 ✔ Service http: Available
 ✔ Service db: Available

 uri (site: example.com URI, no http:// or trailing /):
 > local.mskcc.org

 platform (site: The platform this site is run on. (Optional)):
 > 

 root (Path to source code. Enter an absolute path or relative to /Users/jon/.config/provision. If path does not exist, it will be created.) [/Users/jon/.config/provision]:
 > /Users/jon/Projects/mskcc.org/mskcc_composer

 ✔ Using root /Users/jon/Projects/mskcc.org/mskcc_composer

 git_url (platform: Git repository remote URL.):
 > git@bitbucket.org:mskwebteam/mskcc_build2
 Checking git remote...

 ✔ Connected to git remote.

 makefile (platform: Drush makefile to use for building the platform. May be a path or URL.):
 > 

 make_working_copy (platform: Specifiy TRUE to build the platform with the Drush make --working-copy option.):
 > 

 document_root (platform: Relative path to the "document root" in your source code. Leave blank if docroot is the root.):
 > web
 
 composer_install_command (The command to run immediately after cloning or pulling new code. If this is a production site, you would should add "--no-dev".) [composer install --no-interaction]:
 > 

```

{% hint style="info" %}
It is common practice to set the `contextname` property to the URI of the site, but you can call it whatever you want. 

Don't get confused! Only the URI is used for setting configuration
{% endhint %}

Some more information on the options:

* `platform` is a reference to a Platform context, which is not required. More documentation on platform usage coming soon.
* `root` is the full path to the root of your codebase \(the root of the git repository, not the document root\).
* `git_url` is the full git repository URL for your site. If you enter a git url and the root path does not exist, the site's codebase will be cloned on the first run of `provision verify`.
* `makefile` is the path to a Drush makefile, if you have one. A URL, absolute path, or relative path inside the `root` is acceptable. Optional.
* `make_working_copy` should be set to 1 if you want to clone individual projects specified in the makefile. Optional.
* `document_root` is the relative path to the exposed web root from your git repository root. Most commonly `docroot` or `web`.
* `composer_install_command` is the command to run from the `root` before verifying the codebase.

### Service Subscriptions

Provision treats Services as the bridge between Servers and Sites. 

Just as you can use the `provision services add` command to tell Provision about a Server's services, you can use the `provision services add` command to assign a site to web and database servers:

#### Database Service Subscription

Services store properties, just like contexts. When you add a DB service to a site, you need to specify options such as `db_name`_, `db_user`_, and `db_password`:

```text
 $ pro services add

 Choose a context:
  [server_master] server server_master
  [mysite.org    ] site mysite.org
 > mysite.org

 Add Services

 Which service?:
  [db  ] Database Server
  [http] Web Server
 > db

 Which server?:
  [server_master] server_master: mysqlDocker
 > server_master

 db_name (The name of the database. Default: Automatically generated.):
 > dbb

 db_user (The username used to access the database. Default: Automatically generated.):
 > dbb

 db_password (The password used to access the database. Default: Automatically generated.):
 > dbb

 Using db service from server server_master...

                                                                          
 [OK] Service saved to Context!                     
```

{% hint style="warning" %}
**WARNING: **Currently Provision does _not_ automatically generate default database credentials, yet. It will soon. 

Please enter anything into db\_name_, _db\_user, and db_\__password, and Provision will use these credentials to create the database and write the settings.php file.
{% endhint %}

Adding a http server to a site is simpler, there are no options:

```bash
$ pro services add

 Choose a context:
  [server_master] server server_master
  [mysite.org    ] site mysite.org
 > mysite.org

 Add Services

 Which service?:
  [db  ] Database Server
  [http] Web Server
 > http

 Which server?:
  [server_master] server_master: apacheDocker
 > server_master

 Using http service from server server_master...

                                                                          
 [OK] Service saved to Context!              
```

### Finally... Site Verify!

If your server context verifies, and you have added your site and assigned services, you can now run `provision verify` on the site context and Provision will:

1. Clone your site codebase to the set root.
2. Run `composer install` \(Not yet functional\)
3. Create a database, create a user and set permissions.
4. Write credentials to your settings.php.
5. Write a web server virtualhost configuration file.
6. Change permissions of your sites/default/files folder and more to appropriate defaults.

As long as the URI you have chosen resolves to the IP address of the computer you are using, you should be able to load the website now!





