# maestro
Maestro is a [Fabric](http://fabfile.org) based toolkit for managing systems.  There is also integration with cloud providers via [Apache Libcloud](http://libcloud.apache.org).

# Installation

* `pip install maestro`

# Usage
Either download the [default](https://github.com/ehazlett/maestro/blob/master/fabfile.py) fabfile or create your own:

```python
from maestro.core import *
from maestro import system as sys
```

You can then run `fab -l` to see the available tasks.

For example, to run a memory report for a host named `dev.example.com`:

`fab -H dev.example.com sys.memory`

```
[dev.example.com] Executing task 'sys.memory'
[dev.example.com]              total       used       free     shared    buffers     cached
[dev.example.com] Mem:          7948       4076       3871          0       1942        295
```

# Nodes
Maestro has the ability to use a cloud provider (currently EC2 and Rackspace) for a node list.

You can optionally specify a specific region to only show those nodes.  By default, all regions will be used.

To see a list of available regions, run:

`fab list_available_providers`

## Amazon EC2
To use Amazon EC2, you must set the following environment variables:

```
EC2_ACCESS_ID=<your_aws_access_id>
EC2_SECRET_KEY=<your_aws_secret_key>
```
To run an uptime report for all nodes in EC2:

`fab nodes:ec2 sys.uptime`

```
[1.2.3.4] 07:26:16 up 49 days, 12:34,  1 user,  load average: 0.32, 0.05, 0.01
[4.5.6.7] 07:26:17 up 34 days, 13:15,  1 user,  load average: 0.22, 0.10, 0.01
[4.3.2.1] 07:26:18 up 23 days, 15:25,  1 user,  load average: 0.17, 0.06, 0.01
```

To run an uptime report for all nodes in EC2 us-east-1 region:

`fab nodes:ec2,us-east-1 sys.uptime`

```
[1.2.3.4] 07:26:16 up 49 days, 12:34,  1 user,  load average: 0.32, 0.05, 0.01
```

## Rackspace Cloud
To use Rackspace, you must set the following environment variables:

```
RACKSPACE_ID=<username>
RACKSPACE_KEY=<api_key>
```

To run an uptime report for all nodes:

`fab nodes:rackspace sys.uptime`

```
[50.1.2.3] 00:16:37 up 2 min,  1 user,  load average: 0.00, 0.00, 0.00
```

## Filtering
You can also filter nodes based upon the machine name (the `Name` tag in EC2) using a regular expression:

`fab nodes:ec2,filter=^puppet sys.uptime`

```
[puppet.example.com] 23:17:19 up 50 days,  4:25,  1 user,  load average: 0.00, 0.00, 0.00
```

## ~/.maestrorc
You can put the environment variables in `key=value` form in a file named `.maestrorc` in your home directory.  Maestro will automatically load it and use the values.

For example, create `~/.maestrorc` with the following:

```
EC2_ACCESS_ID=123456789
EC2_SECRET_KEY=abcdefghijlkmnop
RACKSPACE_ID=rackspaceuser
RACKSPACE_KEY=800012345abcdefg12345
```

# Using Maestro from a custom fabfile
To use Maestro from a custom `fabfile` simply add the following to your fabfile:

```python
from maestro.core import *
from maestro import system as sys
# optional -- ~/.maestrorc loader
from maestro.utils import load_maestro_rc
load_maestro_rc()
```

You should then be able to run `fab -l` and see the Maestro tasks.

# Applications

There is also experimental support for creating and deploying Python applications (more languages and frameworks will be included in the future).

** Note: this assumes you have the following packages installed (these are for debian/ubuntu):

 * build-essential
 * python-dev
 * python-setuptools
 * libxml2-dev
 * libxslt-dev
 * supervisor
 * nginx

** You will also need to install uWSGI:

 * `sudo easy_install pip`
 * `sudo pip install uwsgi`
 * `sudo pip install virtualenv`

## Create an application

`fab -H 10.1.2.3 py.create_app:myapp,"myapp.mydomain.com"`

This will create the application directory, setup supervisor to run uWSGI, and create an nginx config.

## Deploy an application

To deploy an application, make sure there is a `wsgi.py` module in the root of the application.  Here is an example for a Django app:

```
import os
import django.core.handlers.wsgi
os.environ.setdefault("DJANGO_SETTINGS_MODULE", "myapp.settings")

application = django.core.handlers.wsgi.WSGIHandler()
```

### Deploy

`fab -H 10.1.2.3 py.deploy:myapp,/path/to/myapp/source`

You can also deploy using git:

`fab -H 10.1.2.3 py.deploy:myapp,https://github.com/username/myapp`

When deploying, Maestro will upload or pull the latest source, install packages for the virtualenv (from a `requirements.txt` located in the root directory of your app), and restart the application.

## Delete an application

`fab -H 10.1.2.3 py.delete_app:myapp`

# Contributing
Forking encouraged :)  For bugs, features, etc. please use Github issues.

# Credits

* Many thanks to the [Fabric](http://fabfile.org) and [Apache Libcloud](http://libcloud.apache.org) projects for making my job easier :)
