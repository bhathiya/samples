## Package overview

`ballerina/reflect` package provides a way to inspect and manipulate objects in Ballerina runtime. One usage of this package is checking if 2 objects (including arrays) are exactly equal using the [reflect:equals](reflect.html#equals) function. 

## Samples

### reflect:equals function

```ballerina
// Compare 2 strings
string s1 = "a";
string s2 = "a";
boolean equal = reflect:equals(s1,s2); // Returns `true`

// Compare 2 string arrays
string[] s1 = ["a", "b"];
string[] s2 = ["a", "b"];
boolean equalArrays = reflect:equals(s1,s2);  // Returns `true`
```

Similarly, `reflect:equals` can be used with other primitive types as well. 

Below is a sample for complex types.

```ballerina
// Compare 2 JSON object
json jObj1 = {   name:"Target",
                 location:{
                              address1:"19, sample road",
                              postalCode: 6789
                          },
                 products:[{price: 40.50, isNew: true, name:"apple"},
                           {name:"orange", price: 30.50}],
                 manager: ()
             };
json jObj2 = {   name:"Target",
                 location:{
                              address1:"19, sample road",
                              postalCode: 6789
                          },
                 products:[{price: 40.50, isNew: true, name:"apple"},
                           {name:"orange", price: 30.50}],
                 manager: ()
             };
boolean equalJsons = reflect:equals(jObj1,jObj2);
```

## Package contents
