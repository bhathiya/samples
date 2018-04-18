# Package overview

The `ballerina/config` package provides an API to read configurations from files in TOML format, environment variables and command line parameters and build a consolidated set of configurations. 

The precedence order for configuration lookup is as follows: 
1. CLI parameters (provided using the -e flag)
2. Environment variables 
3. Configuration files in TOML format

If a particular configuration is defined in both a configuration file and as an environment variable, the environment variable takes precedence. Similarly, if the same is set as a CLI parameter, it replaces the environment variable value. This configuration resolution happens at the start of the program execution. Configurations can be set programmatically as well. 

The config API also provides a mechanism for feeding sensitive values (e.g. passwords) to Ballerina programs in a secure way, by allowing them to be encrypted. 


# Samples

## Setting configurations

To explicitly specify a config file, `--config` or `-c` flag can be used. If this flag is not set, Ballerina looks for a `ballerina.conf` file in the source root. The path to the configuration file can either be an absolute path or a relative path. 

```sh
ballerina run my-program.bal --config /path/to/conf/file/custom-config-file-name.conf
```

A sample config file looks as follows and it should conform to the TOML format. The configuration file supports the following subset of features of TOML: value types (string, int, float and boolean), tables and nested tables. 

```
[b7a.http.tracelog]
console=true
path="./trace.log"

[b7a.http.accesslog]
console=true
path="./access.log"
```
A key specified inside a bracket forms a namespace. Any configurations specified between two such keys belong to the namespace of the first of these keys. To access a configuration in a namespace, the fully qualified key should be provided (e.g: `b7a.http.tracelog.path`).

Following types of values can be provided through a configuration file: `string`, `int`, `float` and `boolean`. If the configuration value is not an `int`, `float` or a `boolean`, it is considered a `string` and should always be quoted.

The same configs can be set using CLI parameters as follows.

```
ballerina run my-program.bal -e b7a.http.tracelog.console=true -e b7a.http.tracelog.path=./trace.log -e b7a.http.accesslog.console=true -e b7a.http.accesslog.path=./access.log
```

Configurations in a file can be overridden by environment variables. To override a particular configuration, an environment variable matching the configuration key must be set. Periods in a configuration key should be replaced by underscores as periods are not allowed in environment variables.

```
// In Linux and Mac
$ export b7a_http_tracelog_path=”./trace.log”
$ export b7a_http_accesslog_path=”./access.log”

// In Windows
$ set(x) b7a_http_tracelog_path=”./trace.log”
$ set(x) b7a_http_accesslog_path=”./access.log”
```

If configurations/properties need to be shared during runtime, those can be set using the `setConfig()` function. Such configs also are accessible to the entire BVM (Ballerina Virtual Machine). 

```ballerina
config:setConfig("john.country", "USA");
```
 
## Reading configurations

The API provides functions for reading configs in their original type.

```ballerina
// read a configuration as a string
string host = config:getAsString("host"); // this returns “” (i.e. empty string) if the configuration is not available

// read a configuration as an integer
int port = config:getAsInt("port"); // this returns 0 if the configuration is not available


// read a configuration as a float
float rate = config:getAsFloat("rate"); // this returns 0.0 if the configuration is not available

// read a configuration as a boolean
boolean enabled = config:getAsBoolean("service.enabled"); // this returns ‘false’ if the configuration is not available
```
When reading a configuration, a default value can be specified as well. If a default value is specified, this default value is returned if a configuration entry cannot be found for the specified key.

```ballerina
// read a configuration as a string, and if it does not exist, return “localhost”
string host  = config:getAsString("host", default = "localhost"); 
```

The `contains()` function can be used to check if a configuration entry exists for the specified key. 

```ballerina
// check if configuration is available
boolean configAvailable = config:contains("host"); 
```

A set of configurations belonging to a particular namespace can be retrieved as a `map` using the `getAsMap()` function. Here is an example.

```toml
[b7a.http.tracelog]
console=true
path="./trace.log"
host="@env:{TRACE_LOG_READER_HOST}"
port=5757

[b7a.http.accesslog]
console=true
path="./access.log"
```

The configurations for HTTP trace logs can be retrieved as a `map` as follows:

```ballerina
//read a configuration section as a map
map serverAlphaMap  = config:getAsMap("b7a.http.tracelog"); // here, the map’s key-value pairs represent config key-value pairs
```

In the above configuration file, the `host` is specified as `@env:{TRACE_LOG_READER_HOST}`. When resolving the configurations, Ballerina will look up in environment variables for a variable named `TRACE_LOG_READER_HOST` and map `b7a.http.tracelog.host` to its value. 

## Securing configuration values

Sensitives values can be encrypted through the `encrypt` command as follows:

```sh
$ ballerina encrypt
Enter value: 

Enter secret: 

Re-enter secret to verify: 

Add the following to the runtime config:
@encrypted:{JqlfWNWKM6gYiaGnS0Hse1J9F/v48gUR0Kxfa5gwjcM=}

Or add to the runtime command line:
-e<param>=@encrypted:{JqlfWNWKM6gYiaGnS0Hse1J9F/v48gUR0Kxfa5gwjcM=}
```

This encrypted value can then be placed in a configuration file or can be provided as a CLI param.

```
[admin]
password=”@encrypted:{JqlfWNWKM6gYiaGnS0Hse1J9F/v48gUR0Kxfa5gwjcM=}”
```
## Reading config files with encrypted values

When trying to run a Ballerina program with a configuration file which contains encrypted values, the user will be prompted to enter the secret which was used to encrypt the values in the first place. Values are decrypted only on demand, when an encrypted value is looked up using the `getAsString()` function.

```
$ ballerina run program.bal 
ballerina: enter secret for config value decryption:
```

**Note**: *The same config file cannot contain encrypted values encrypted using different secrets.* 
