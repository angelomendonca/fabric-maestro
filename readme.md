# maestro
Maestro is a [Fabric](http://fabfile.org) based toolkit for managing systems.  There is also integration with Cloud providers via [Apache Libcloud](http://libcloud.apache.org).

# Installation

* `git clone https://github.com/ehazlett/maestro`
* `mkvirtualenv maestro` or `virtualenv maestro`
* `pip install -r requirements.txt` or `source /path/to/virtualenv/bin/activate && pip install -r requirements.txt`

# Usage
To use, simply run `fab -l` to see a list of available commands. 

For example, to run a memory report for a host named `dev.example.com`:

`fab -H dev.example.com sys.memory`

```
[dev.example.com] Executing task 'sys.memory'
[dev.example.com] out:              total       used       free     shared    buffers     cached
[dev.example.com] out: Mem:          7948       4076       3871          0       1942        295
```

# Nodes
Maestro has the ability to use a cloud provider (currently only EC2) for a node list. 

## Amazon EC2
To use Amazon EC2, you must set the following environment variables:

`EC2_ACCESS_ID=<your_aws_access_id>`
`EC2_SECRET_KEY=<your_aws_secret_key>`

To run an uptime report for all nodes in EC2 us-east region:

`fab nodes:ec2_us_east sys.uptime`

```
[1.2.3.4] out:  07:26:16 up 49 days, 12:34,  1 user,  load average: 0.32, 0.05, 0.01
[4.5.6.7] out:  07:26:17 up 34 days, 13:15,  1 user,  load average: 0.22, 0.10, 0.01
[4.3.2.1] out:  07:26:18 up 23 days, 15:25,  1 user,  load average: 0.17, 0.06, 0.01
```

## ~/.maestrorc
You can put the environment variables in `key=value` form in a file named `.maestrorc` in your home directory.  Maestro will automatically load it and use the values.

For example, create `~/.maestrorc` with the following:

```
EC2_ACCESS_ID=123456789
EC2_SECRET_KEY=abcdefghijlkmnop
```

# Credits

* Many thanks to the [Fabric](http://fabfile.org) and [Apache Libcloud](http://libcloud.apache.org) projects for making my job easier :)