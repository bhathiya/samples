## Package overview

`ballerina/user` package provides an API to retrieve information of the user who runs the application. This API provides information such as user account name, user home directory path, user's country, user's language and system’s locale. 

## Samples

```ballerina
// Get user account name
string username = user:getName();  //Eg. “john”

// Get user home path
string userHome = user:getName();  //Eg. “/home/john”

// Get two-letter language code of the default locale. This is system dependent.
string userLanguage = user:getLanguage();  //Eg. “en”

// Get country. This is system dependent.
string userLanguage = user:getCountry();  //Eg. “US”

// Get the system’s locale. This is system dependent.
util:Locale locale = user:getLocale(); 
// The locale has 2 components, the language and the country
string language = locale.language;  //Eg. “en”
string country = locale.countryCode;   //Eg. “US”
```
