`wso2/ftp` package provides an FTP client implementation and an FTP server listener implementation. 

## FTP Client

The `ftp:Client` can be used to connect to an FTP server and perform file operations. It supports `get`, `delete`, `put`, `append`, `mkdir`, `rmdir`, `rename`, `size`, and `list` operations.

An `ftp:Client` endpoint is defined like this.

```ballerina
endpoint ftp:Client client {
    protocol: "sftp",
    host:"ftp.ballerina.com",
    username : "john",
    passPhrase : "password"
};
```

 It supports ftp and sftp protocols.  In addition to above parameters, optional `port` parameter is also supported. Examples for each operation is below. All operations return `FTPClientError
` in case of any error.

```ballerina
// Make a directory in remote FTP location
ftp:FTPClientError? dirCreErr client -> mkdir("/personal/files");  

// Put a file to FTP location
io:ByteChannel bchannel = io:openFile("/home/john/files/MyFile.xml", "r");
ftp:FTPClientError? filePutErr = client -> put("/personal/files/MyFile.xml", bchannel);

// List files of FTP location
var listOrError = client -> list("/personal/files");

// Rename or move a file in FTP location
ftp:FTPClientError? renameErr = client -> rename("/personal/files/MyFile.xml", "/personal/New.xml");

// Read the size of a file in FTP location
var sizeOrError = client -> size("/personal/New.xml");

// Download a file from FTP location
var byteChannelOrError = client -> get("/personal/New.xml");

// Delete a file in FTP location
ftp:FTPClientError? fileDelErr = client -> delete("/personal/New.xml");

// Delete a directory in FTP location
ftp:FTPClientError? dirDelErr = client -> rmdir("/personal/files");    
```

See [Complete FTP Client Example](//BBE link goes here)

## FTP Listener

The `ftp:Listener` can be used to listen to a remote FTP location. It keeps listening to the specified directory, and an event is fired when a new file is added to the directory. The fired event is the type of `FileEvent` and it consists of following information.

`uri` - Absolute URI of the newly added file<br/>
`baseName` - Name of the newly added file<br/>
`path` - The new file’s path from root directory<br/>
`size` - Size of the newly added file in bytes<br/>
`lastModifiedTimeStamp` - The timestamp of when the file was created (Number of milliseconds since January 1, 1970, 00:00:00 GMT)


An `ftp:Listener` endpoint is defined like this. 

```ballerina
endpoint ftp:Listener remoteFolder {
    protocol:"sftp",
    host:"ftp.ballerina.com",
    username : "john",
    passPhrase : “password"
    path:"/personal",
    pollingInterval:"2000",
    sftpIdentities:"/home/john/.ssh/id_rsa",
    sftpIdentityPassPhrase:"",
    sftpUserDirIsRoot:"true"
};
```

Following configurations are available for `ftp:Listener`.

`protocol` - Supports both ftp and sftp<br/>
`host` - SFTP hostname<br/>
`username` and `passPhrase` - Credentials for password based authentication<br/>
`path` - The absolute local path from root <br/>
`pollingInterval` -  How often the listener should be checking for the changes in the file system (in milliseconds) [Default: 1000]  <br/>
`sftpIdentities` - Private key for key-based authentication<br/>
`sftpIdentityPassPhrase` - Passphrase for key-based authentication<br/>
`sftpUserDirIsRoot` - True if the user directory should be treated as the root directory. [Default: True]


A service can be defined binding to above `ftp:Listener` endpoint.  

```ballerina
service myRemoteFiles bind remoteFolder {
    fileResource (ftp:FileEvent m) {
	log:printInfo(m.uri);
	log:printInfo(m.baseName);
        log:printInfo(m.path);
    }
}
```

Whenever a new file is added to the remote location, `fileResource` function is invoked.
 
