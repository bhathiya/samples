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

Configurations can be set as environment variables as well. Here, dots should be replaced by underscores as dots are not allowed in environment variables.

```
export server_hostname=server.abc.com
export server_ports_http=80
export server_ports_https=443
```

Configurations can be set programmatically as follows.

```ballerina
config:setConfig("john.country", "USA");
```
 
## Reading configurations

Configurations can be read as different data types.

```ballerina
//read a configuration as string
string host1 = config:getAsString("host"); // this returns “” (i.e. empty string) if the configuration is not available

//read a configuration as integer
int port = config:getAsInt("port"); // this returns 0 if the configuration is not available


//read a configuration as float
float rate = config:getAsFloat("rate"); // this returns 0.0 if the configuration is not available

//read a configuration as boolean
boolean enabled = config:getAsBoolean("service.enabled"); // this returns ‘false’ if the configuration is not available
```
Configurations can be read while providing a default value as well. When a default value is provides, in case of configuration being not available, the default value is returned. 

```ballerina
//read a configuration as string while setting a default value
string host2  = config:getAsString("host", default = "localhost"); // this returns “localhost” if the configuration is not available
```

If a developer wants to explicitly check if a configuration is available regardless of the default value, `contains()` function can be used.

```ballerina
//check if configuration is available
boolean configAvailable = config:contains("host"); 
```

A set of configurations can be read at once as a map. Assume the configuration file is like this.

```toml
[servers]

    [servers.alpha]
    ip = "10.0.0.1"
    dc = "eqdc10"

    [servers.beta]
    ip = "10.0.0.2"
    dc = "eqdc10"
```
`[servers.alpha]` section can be read as a map at once like this. 

//read a configuration section as a map
map serverAlphaMap  = config:getAsMap("servers.alpha"); // here, the map’s key-value pairs represent config key-value pairs
```
