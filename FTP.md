`wso2/ftp` package provides an FTP client implementation and an FTP server listener implementation. 

## FTP Client

The FTP Client can be used to connect to an FTP server and perform I/O operations. It supports get, delete, out, append, mkdir, rmdir, rename, size, list operations.

An FTP Client endpoint can be defined like this.

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



