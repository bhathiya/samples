# Package overview

`ballerina/config` package provides an API to read configurations from files, environment variables and command line parameters. 

The precedence order for config lookup is as follows: 
1. CLI parameters 
2. Environment variables 
3. Config files 

If a particular config is defined in both a configuration file and as an environment variable, the environment variable takes precedence. Similarly, if the same is set as a CLI parameter, it replaces the environment variable value. Configurations can be set programmatically as well. 


# Samples

## Setting configurations

To explicitly specify a config file, `--config` or `-c` flag can be used. If this flag is not set, Ballerina looks for a `ballerina.conf` file in the directory from which the user executes the program. The path to the config file can either be an absolute or a relative path.

```
ballerina run my-program.bal --config /path/to/conf/file/custom-config-file-name.conf
```

A sample config file looks as follows. Content should be in the TOML format. Note the structure where multiple configs can be grouped under one config, which is inside square brackets. 

```
 username.instances=john,peter
 [john]
 access.rights=RW
 organization=wso2.org
 [peter]
 access.rights=R
 organization=ballerina.org
```

 The same configs can be set using CLI parameters as follows.

```
ballerina run my-program.bal -Busername.instances=john,peter -Bjohn.access.rights=RW -Bjohn.organization=wso2.org -Bpeter.access.rights=R -Bpeter.organization=ballerina.org
```

Configurations can be set as environment variables as well. 

```
export server.hostname=server.abc.com
export server.ports.http=80
export server.ports.https=443

```

Configurations can be set programmatically as follows.

```ballerina
config:setConfig("john.country", "USA");
```
 
## Reading configurations

There are 3 different ways of reading a configuration, and samples for each are given below.

```ballerina
//check if configuration is available
boolean configAvailable = config:contains("host");

//read a configuration
string host1 = config:getAsString("host"); // this returns “” (i.e. empty string) if the configuration is not available

//read a configuration while setting a default value
string host2  = config:getAsString("host", default = "localhost"); // this returns “localhost” if the configuration is not available

//read a configuration table as a map
map userMap  = config:getTable("username.instances"); // here, the map’s key-value pairs represent config key-value pairs
```
