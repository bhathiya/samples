`wso2/ftp` package provides an FTP client implementation and an FTP server listener implementation. 

## FTP Client

The `ftp:Client` can be used to connect to an FTP server and perform I/O operations. It supports get, delete, out, append, mkdir, rmdir, rename, size, list operations.

An FTP Client endpoint is defined like this.

endpoint ftp:Client client {
    protocol: "sftp",
    host:"ftp.ballerina.com",
    username : "john",
    passPhrase : “password"
};

 It supports ftp and sftp protocols.  In addition to above parameters, optional `port` parameter is also supported. Examples for each operation is below. All operations return `FTPClientError
` in case of any error.

```sh
// Make a directory in remote FTP location
var error = client -> mkdir("/personal/files");  

// Put a file to FTP location
io:ByteChannel bchannel = io:openFile("/home/john/files/MyFile.xml", "r");
error = client -> put("/personal/files/MyFile.xml", bchannel);

// List files of FTP location
var listOrError = client -> list("/personal/files");

// Rename or move a file in FTP location
error = client -> rename("/personal/files/MyFile.xml", "/personal/New.xml");

// Read the size of a file in FTP location
var sizeOrError = client -> size("/personal/New.xml");

// Download a file from FTP location
var byteChannelOrError = client -> get("/personal/New.xml");

// Delete a file in FTP location
error = client -> delete("/personal/New.xml");

// Delete a directory in FTP location
error = client -> rmdir("/personal/files");    
```

See [Complete FTP Client Example](//BBE link goes here)

## FTP Listener

The `ftp:Listener` can be used to listen to a remote FTP location. It keeps listening to the specified directory and an event is fired when a new file is added to the directory. The fired event is the type of `FileEvent` and it consists of following information.

`uri` - Absolute URI of the newly added file
`baseName` - Name of the newly added file
`path` - The new file’s path from root directory
`size` - Size of the newly added file in bytes
`lastModifiedTimeStamp` - The timestamp the file was created (Number of milliseconds since January 1, 1970, 00:00:00 GMT)

An `ftp:Listener` endpoint is defined like this. 

```java
endpoint ftp:Listener remoteFolder {
    protocol:"sftp",
    host:"ftp.ballerina.com",
    username : "john",
    passPhrase : “password"
    path:"/personal",
    pollingInterval:"2000",
    sftpIdentities:"/home/gihan/.ssh/id_rsa",
    sftpIdentityPassPhrase:"",
    sftpUserDirIsRoot:"true"
};
```

Following configurations are available for `ftp:Listener`.

`protocol` - Supports both ftp and sftp 
 `host` - SFTP hostname
 `username` and `passPhrase` - Credentials for password based authentication
`path` - The absolute local path from root 
`pollingInterval` -  How often the listener should be checking for the changes in the file system (in milliseconds)  
`sftpIdentities` - Private key for key-based authentication
`sftpIdentityPassPhrase` - Passphrase for key-based authentication
`sftpUserDirIsRoot` - True if the user directory should be treated as the root directory.

A service can be defined binding to above created `ftp:Listener` endpoint. Whenever a new file is added to the remote location, `fileResource` function is invoked. 

```java
service myRemoteFiles bind remoteFolder {
    fileResource (ftp:FileEvent m) {
	log:printInfo(m.uri);
	log:printInfo(m.baseName);
        	log:printInfo(m.path);
    }
}
```
 
