# API2LaTeX User Guide

I recently started working on my (long long overdue) master's thesis, and naturally I came to LaTeX for typesetting. As a LaTeX newbie*, I was pleased to find out that compiling LaTeX is just like compiling any program: there's a bunch of source code files (.tex) and resources (.bib, .sty, images) which you feed to preprocessors and compilers to yield a dvi/ps/pdf document. TeX is actually a turing-complete language, and LaTeX is a macro collection and a preprocessor for TeX.

There's even an online package repository!

And since writing LaTeX has so many similarities with writing code, even if you're just writing text and using existing LaTeX macros and not developing your own, some of the development practices can be applied here as well, like a build system, refactoring to keep the document manageable, version control, and continuous integration.

Continuous integration is nice not only because it asserts that the document is valid (yes, you can screw up TeX documents), but also you automatically get the resulting PDF as a build artifact. You could even hook a diff step in the process, to make reviews easier.

But finding a public continuous integration server is hard, not to mention one with a LaTeX distribution installed. Luckily, LaTeX compilation can be delegated to a remote server, thanks to CLSI (Common LaTeX Service Interface). So all the integration server has to do is forward a compilation request to the CLSI server when it detects changes in the VCS.

# ORIGINAL HEADING 1

Here is some text that was checked in with the initial creation of this file. The following lines can be used to track differences and merges of branches.

  - January
  - February
  - March
  - April
  - May
  - June
  - July
  - August
  - September
  - October
  - November
  
<code>This sentence originally occured on line 29.</code>

# Working with Resource Collections

In the Redfish protocol a URI can represent a collection of similar resources. A Resource Collection can represent a group of Systems, Chassis, Managers, or a group of other kinds of resources. For example:

 - /redfish/v1/Systems
 - /redfish/v1/Chassis
 - /redfish/v1/Managers

The Members of a Resource Collection are returned as a JSON array, where each element of the array is a JSON object. The name of the property representing the members of the collection is `Members`.


## Operations Related to Resource Collections

Some of the common operations associated with collections are as follows:

### A GET request for a Resource Collection

To read the contents of a Resource Collection, a client application sends an HTTP GET request to the URI of the collection. A client application typically discovers the URI of the collection by parsing the resource identifier from a previous request.  For example, the `Links` property of a previously returned resource can contain a URI that points to a collection. A client application could parse the information in the `Links` property to obtain the URI of the collection.

The response includes properties of the Resource Collection including an array of its Members. If the Resource Collection is empty, the returned JSON object is an empty array (not null).

To request a subset of Members of the Resource Collection, use the paging query options:

- `$top`
- `$skip`

These paging query options apply specifically to the `Members` array property within a Resource Collection.

### The response to a GET request for a Resource Collection

A Redfish service returns a Resource Collection as a JSON object in an HTTP response. The JSON object can include the following properties:

| Property  | Description   |
| -- | -- |
| @odata.context  | Describes the source of the payload. |
| @odata.count    | Displays the total number of Members in the Resource Collection |
| @odata.members  | The array of the members in the collection    |
| @odata.nextLink | Indicates the "nextLink" when the payload contains partial results |

When a response represents only a part of a Resource Collection, the response includes a next link property named `Members@odata.nextLink`. The value of the `@odata.nextlink` property is a URL to a resource with the same @odata.type that  contains the next set of partial members. The `@odata.nextlink` property is only present if the number of Members in the Resource Collection is greater than the number of members returned.

### Iterating through the members of a collection

A Resource Collection includes a count of the total number of entries in its "Members" array.

The total number of resources (Members) available in a Resource Collection is represented in the count property. The count property is named `Members@odata.count`. The value of odata.count represents the total number of members available in the Resource Collection. This count is not affected by the `$top` or `$skip` query parameters.


### Additional notations

A JSON object representing a Resource Collection may include additional annotations represented as properties whose name is of the form:

@Namespace.TermName

where

  - Namespace = the name of the namespace where the annotation term is defined. This namespace is referenced by the metadata document specified in the context URL of the request.
  - TermName = the name of the annotation term being applied to the Resource Collection.

The client can get the definition of the annotation from the service metadata, or may ignore the annotation entirely, but should not fail reading the response due to unrecognized annotations, including new annotations defined within the Redfish namespace.

### Order of Members

Collections are arrays of OData objects. The OData objects contain IDs of resources.

The order in which Members exist in a collection is deterministic, but the members are not sorted. In other words, assuming that the members have not changed since the last request, the order in which memebrs are returned will be unchanged. The order of the members will not be sorted by any specific criteria.


### Examples of commonly used collections

#### Collection of Systems

A System represents the logical view of a computer system as seen from the operating system (OS) level.

Any subsystem accessible from the host CPU is represented in a System resource. Each instance of a System includes CPUs, memory, and other components. Each computer System can be contained as a member of a Systems collection.

~~~json
{
    "@odata.type": "#ComputerSystemCollection.ComputerSystemCollection",
    "Name": "Computer System Collection",
    "Members@odata.count": 1,
    "Members": [
        {
            "@odata.id": "/redfish/v1/Systems/437XR1138R2"
        }
    ],
    "@odata.context": "/redfish/v1/$metadata#ComputerSystemCollection.ComputerSystemCollection",
    "@odata.id": "/redfish/v1/Systems"
}
~~~


#### Collection of Chassis

The Chassis collection contains resources that represent the physical aspects of the infrastructure. Think of this collection as the properties needed to locate a physical unit, or to identify a physical unit, or to install or service a physical computer.

A Chassis is roughly defined as a physical view of a computer system as seen by a human. A single Chassis resource can house sensors, fans, and other components. Racks, enclosures, and blades are examples of Chassis resources included in the Chassis collection.

The Redfish protocol allows the representation of a Chassis contained within another Chassis.

~~~json
{
    "@odata.type": "#ChassisCollection.ChassisCollection",
    "Name": "Chassis Collection",
    "Members@odata.count": 5,
    "Members": [
        {
            "@odata.id": "/redfish/v1/Chassis/MultiBladeEnclosure"
        },
        {
            "@odata.id": "/redfish/v1/Chassis/Blade1"
        },
        {
            "@odata.id": "/redfish/v1/Chassis/Blade2"
        },
        {
            "@odata.id": "/redfish/v1/Chassis/Blade3"
        },
        {
            "@odata.id": "/redfish/v1/Chassis/Blade4"
        }
    ],
    "@odata.context": "/redfish/v1/$metadata#ChassisCollection.ChassisCollection",
    "@odata.id": "/redfish/v1/Chassis"
}
~~~

#### Collection of Managers

A Managers collection contains BMCs, Enclosure Managers or any other component managing the infrastructure. Managers handle various management services and can also have their own components (such as NICs).

~~~json
{
    "@odata.type": "#ManagerCollection.ManagerCollection",
    "Name": "Manager Collection",
    "Members@odata.count": 3,
    "Members": [
        {
            "@odata.id": "/redfish/v1/Managers/EnclosureManager"
        },
        {
            "@odata.id": "/redfish/v1/Managers/Blade1BMC"
        },
        {
            "@odata.id": "/redfish/v1/Managers/Blade2BMC"
        }
    ],
    "@odata.context": "/redfish/v1/$metadata#ManagerCollection.ManagerCollection",
    "@odata.id": "/redfish/v1/Managers"
}
~~~

# Error messages

A Redfish service typically returns two types of error messages:  

- HTTP response codes
- Error responses

## HTTP response codes

The HTTP reponse codes are the standard codes returned by all HTTP servers.

These include familiar HTTP codes such as HTTP response code `200 OK`, which means that the HTTP request succeeded.

For more information about the meaning of these codes when returned from a Redfish service, see the latest Redfish specification at:

  - http://www.dmtf.org/standards/redfish

## Redfish error responses

HTTP response status codes alone often do not provide enough information to determine the nature of an error. For example, if a client application sends a PATCH request and some of the properties do not match while others are not supported, simply returning an HTTP status code of 400 does not clearly indicate which values were in error.

Redfish error responses provide more meaningful and deterministic error information.

A Redfish service can provide multiple error responses in an HTTP response in order to provide as much information about the error situation as possible. Additionally, a Redfish service can provide Redfish-standardized errors, OEM-defined errors, or both, depending on what is available from a perticular service.

Error responses are defined by an extended error resource, represented as a single JSON object.  The JSON object is part of a property named "error".
