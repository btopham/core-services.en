---
title: api methods - resetstate
description: resetstate method for the adobe experience cloud id service api
seo-title: adobe experience cloud id service api methods - resetstate
seo-description: resetstate method for the adobe experience cloud id service api
short-title: resetstate
doc-type: reference
audience: admin
index: true
translate: true
version: false
private-feature-pack: false
beta: false
redirect: false
product: core services id service
cloud: experience cloud
archetype: admin
user-guide: id service admin guide
git-edit: https://git.corp.adobe.com/AdobeDocs/core-services.en/tree/master/help/id-service/id-service-api/id-service-api-methods/id-service-api-methods-resetstate.md
git-issue: https://git.corp.adobe.com/AdobeDocs/core-services.en/issues/new
git-repo: https://git.corp.adobe.com/adobedocs/core-services.en
---
<!--Meta Data Values

**Required Meta for search optimization and page data**

title: free text string

description: free text string

seo-title: free text string

seo-description: free text string

**Optional Meta for extended capabilities**

audience:
all (default), admin, developer, end-user
 
index: true (default), false
 
translate:
true (default), false
 
doc-type:
reference (default), tutorials

version:
false (default), Classic, Standard, 6.5, 6.4, 6.3, 6.2
 
private-feature-pack:
false (default), true
 
beta:
false (default), true
 
redirect:
false (default), pathname
-->

# resetState

This function is designed mainly for A4T customers to help solve issues related to working with IDs on single page sites/screens or apps.
  
## Use Cases

As an A4T customer who uses the ID service, you may want to use the `visitor.resetState()` function when you need to:

+ To pass a Supplemental Data ID \(SDID\), or any other ID, from one page or screen to another through a redirect. Normally, the ID service won't pass this ID without this function.
+ Use code that only updates specific sections of a page or app via Ajax calls and you want to track those actions. For example, say you have a page where clicking on an object only loads or changes a special section. In this case, the ID service can't request a different ID unless the page is reloaded. However, with `visitor.resetState()`, you can request a new ID under these conditions.

See the code samples below.

## Syntax

`visitor.resetState(state);` 

## Code Samples

Your ID service implementation affects how you would use this function.


### Server-Side Implementation

A [server-side implementation](mcvid-setup-server-side.html) is for A4T customers with mixed server- and client-side implementations of Target, Analytics, and the ID service. 

If you've set up the ID service with this method all you need to do is add `visitor.resetState()` to the page. 
Calls to the ID service will return a new ID and server state automatically.

### Non-Standard Implementation \(with ID\)

If you've set up the ID service with a [non-standard implementation](mcvid-implementation-guides.html#section_2C4F2DB1F9704315A7CCCAB6D2E07113), you need to configure a variable object to hold the SDID \(or other IDs\) you want to pass with `visitor.resetState()`. 

As shown below, this would include your [organization ID](mcvid-requirements.html#section_A02F537129A64FFBB690D5738D360C26) and the ID you want to pass. 

Your code could look similar to the following example.

 ```javascript
//Instantiate server state variable
var serverState = {
     "Insert Experience Cloud organization ID here": {
          //Specify the SDID or other ID
          supplementalDataIDCurrent: "1234",
          supplementalDataIDCurrentConsumed: {
               "payload:top-center": false
          }
     }
};

//Instantiate ID service
var visitor = Visitor.getInstance ("Insert Experience Cloud organization ID here", {
     ...
});

//Reset server state to pass the SDID
visitor.resetState(serverState);
```

### Non-Standard Implementation \(without passing an ID\)

In this case, `visitor.resetState()` can be used to generate a new ID. This can be useful in a single-page app when a user navigates to a new screen without refreshing the page and you need a new ID.

 ```javascript
//Instantiate ID service
var visitor = Visitor.getInstance ("Insert Experience Cloud organization ID here", {
     ...
});

//Request a supplemental Data ID for consumer1 and consumer2:
var sdid1 = visitor.getSupplementalDataID("consumer1"); // sdid1: 1234
var sdid2 = visitor.getSupplementalDataID("consumer2"); // sdid2: 1234

//User navigates to a new screen in a single-page app, without refreshing the page.
//To reset the Supplemental Data ID internal, call resetState without passing any parameters.
//This way we will not be recycling the `1234` ID anymore. Instead Visitor will generate a new supplemental Data ID going forward.
visitor.resetState();

//Request a supplemental Data ID for consumer3 and consumer4:
var sdid1 = visitor.getSupplementalDataID("consumer3"); // sdid1: 5678

var sdid2 = visitor.getSupplementalDataID("consumer4"); // sdid2: 5678
```

### Dynamic Tag Manager \(DTM\)

Currently, there is no DTM configuration path for `visitor.resetState()`.