

# henchman [![Circle CI](https://circleci.com/gh/apigee/henchman/tree/master.svg?style=svg)](https://circleci.com/gh/apigee/henchman/tree/master)
## What is Henchman
Henchman is a non-agent based orchestration and automation tool created in Go, and inspired by Ansible.

## Why Go?
Although, python and ruby are awesome as systems languages, runtime dependencies are generally an issue where users have to install and maintain gems/pip modules. System tools should never enforce the headache of having to manage multiple runtime dependencies and as the number of machines managed increases, it becomes an additional overhead to take care of. With Golang there is no such overhead and a single static binary file can be shipped for use.

## Why are modules written in Python then?
Henchman allows the modules to be written in any programming language as long as they have access to STDOUT and STDIN.  In the future, we may look into using just bash scripts for the modules if Python becomes the bottleneck. For now, writing python modules gives us a nice balance between speed of development and not having to enforce dependencies. The modules themselves use only system libraries.

## Building Henchman
* Install go [here](https://golang.org/doc/install)
* Clone this repo to `$GOPATH/src/github.com/apigee`
* If you have not done so, set `export $PATH=$PATH:$GOPATH/bin`
* `go get github.com/tools/godep`
* `godep restore`
* `go build -o bin/henchman`

## How Henchman Works
Henchman executes a plan (a collection of tasks) on a given set of machines.  Each plan will run the list of tasks by SSHing into a machine, and generally copy over the module specified by the task and execute it.  

## Plan
For details of a plan, go [here](https://github.com/apigee/henchman/wiki/Plans)
## Inventories
Inventory takes 2 keys at the top: 'groups' and 'hostvars'.
Under 'groups', you can specify various group names and they in turn can have 'hosts' and 'vars' applicable to the group of hosts
'hostvars' allow overrides at individual host level.

'groups', and 'hosts' are both required keys.  In addition, a group name cannot be named 'hosts'.
```yaml
groups:
  all:
    hosts:
      - 192.168.33.10
    vars:
      test: 10
 
  zookeeper:
    hosts:
      - z1
      - z2
      - z3
hostvars:
   "192.168.33.10":
      test: 20
```  
In addition,henchman transport variables can also be overridden
Example:
```yaml
groups:
  g1:
    hosts:
      - 10.224.192.11
    vars:
      henchman_username: "vagrant"
      henchman_keyfile: "~/.ssh/all_private_key"

  g2:
    hosts:
      - 10.224.192.12
    vars:
      sysadmin_email: "foobar2@apigee.com"
      henchman_username: "vagrant"
 
hostvars:
   "10.224.192.12":
      henchman_keyfile: "~/.ssh/g2_private_key"
```

## How to Use
CLI commands insert here.

If you are using Vagrant to spin up vms
`bin/henchman exec plan.yml --inventory inv.yaml --user vagrant --keyfile <  >`

## Modules
For more details on how to build modules, go [here](https://github.com/apigee/henchman/wiki/Modules).  

All modules will return a struct with the fields: Msg, Output, State   
('ok' , 'changed', 'skipped', 'failure', 'error', 'unreachable').  
  
Here are a list of core modules available:

#### copy
Copy the specified file to the destination.  
Params:
* src - location of file to be copied
* dest - location of file to be stored

#### template
Renders and copies over a file.  
Params:
* src - location of file to be copied
* dest - location of file to be stored

#### shell
Runs a shell command.  
Params:
* chdir - cd into the given directory to execute the shell command
* shell - The shell to use, by default it's /bin/sh
* cmd - The command to run. Wrapped by quotes if there are spaces. (required)
* loglevel - if debug, it shows you the entire output as part of 'output' key

#### rpm
Install a package using rpm.  
Params:
* url - url that provides the rpm that should be installed
* loglevel - if debug, it shows you the entire output as part of 'output' key

#### yum
Install a package using yum.  
Params:
* package - the name of the package to be installed
* loglevel - if debug, it shows you the entire output as part of 'output' key

## Contributing

Just clone or fork from [https://github.com/apigee/henchman](https://github.com/apigee/henchman) and off you go! Fixing issues marked `easy` in the issue tracker is a great way to get started. Or you can help by creating more modules.  Look at the modules section for more details.
