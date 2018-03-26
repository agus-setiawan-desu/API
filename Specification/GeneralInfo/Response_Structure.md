
### Structure of the response object:

The return objects are encoded in JSON. The response consists of:
+ A "metadata" key containing pagination, status, and file information;
+ A "result" key that can contain:
  + Arbitrary properties; and/or
  + A "data" key containing an array of objects of the same type.


#### The Metadata Key

The metadata key is structured as followed:


````
  "metadata" : {
      "pagination" : {
            "totalCount" : 1234,
            // total number of elements available in the super set (unpaged)
            "pageSize" : 200,
            // number of elements in the current returned page
            "totalPages" : 7,
            // total number of pages available (total count / requested page size)
            "currentPage" : 2
            // index number of the current returned page
       },
       "status" : [
           {
               "code" : "200",
               "message" : "Success"
           }
       ],
       "asynchstatus": {
           "status": "PENDING",
           "startTime": "2017-06-16T14:47:23-0600",
           "endTime": null,
           "percentComplete": 0
       },
       "datafiles" : ["/mnt/local/matrix_01.csv",
                      "/mnt/local/matrix_02.csv"]
   }

````

+  **pagination**: The pagination object is applicable only when the payload contains a "data" key. It describes the pagination of the data contained in the "data" array, as a way to identify which subset of data is being returned. Pages are zero indexed, so the first page will be page 0 (zero).

+ **datafiles**: The datafiles key contains a list of file paths, which can be relative or complete URLs. These files contain additional information related to the returned object and can be retrieved by a subsequent call. The empty list should be returned if no additional data files are present.

+ **status**: The status object contains a list of objects with the keys "code" and "message". If no status is reported, an empty list should be returned. 

**NOTE:** The Status object should be used _in addition_ to the standard [HTTP status codes](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes). The purpose is to provide additional, BrAPI specific information back to the client.   

The following are officially accepted status codes, though others maybe used for specific implementation needs.

Code|Message|Description
--|--|--
200|"Success"|Optional status for representing explicitly that the request was accepted and returned without any issue
2001|"Upload Successful"|New data was submitted to and accepted by the server
2002|"Async call in progress"|An Async call has been successfully started, See the section on Asynchronous Calls for more details.
400|"Failure"|Optional status for representing explicitly that the request was bad in some way
4001|"Could not update values for <object type>"| Error to be returned when the server is unable to store some data submitted
4002|"Missing required parameter <parameter name>"| Error to be returned when a required parameter is missing from request
4003|"Permission Denied"| Error to be returned when the user does not have permission to access the requested resource
4004|"No objects found for given parameters"| Error to be returned when there are no objects in the database which match the requested search parameters


+ **asynchstatus**: (Optional) The asynchstatus object is used to provide additional information around certain calls being performed asynchronously. See the section on Asynchronous Calls for more details.



#### Payload

The BRAPI response payload, which is contained in the "result" key, allows for three different types of responses:
+ **master**: In this type of response, the "result" key consists of arbitrary properties without a "data" key (in this case, pagination does not apply). 
````
{
  "metadata" : {
    "pagination" : {
      "totalCount" : 0,
      "pageSize" : 0,
      "totalPages" : 0,
      "currentPage" : 0
    },
    "status" : [ ],
    "datafiles" : [ ]
  },
  "result" : {
    "key0": "master",
    "key1": 20,
    "key2": [ "foo", "bar", "baz" ]
  }
}
```` 
+ **detail**: In this type of response, the "result" element only contains the "data" key, which is an arbitrarily long array of objects of the same type. 
````
{
  "metadata" : {
    "pagination" : {
      "totalCount" : 20,
      "pageSize" : 3,
      "totalPages" : 7,
      "currentPage" : 0
    },
    "status" : [ ],
    "datafiles" : [ ]
  },
  "result" : {
    "data" : [ 
      {
        "detailKey0" : "detail0",
        "detailKey1" : [ "foo", "bar" ]
      }, 
      {
        "detailKey0" : "detail1",
        "detailKey1" : [ "bar", "baz" ]
      }, 
      {
        "detailKey0" : "detail2",
        "detailKey1" : [ "baz", "foo" ]
      },
    ]
  }
}
````

+ **master/detail**: In this type of response, the "result" key contains both arbtirary properties and the "data" key. 
````
{
  "metadata" : {
    "pagination" : {
      "totalCount" : 20,
      "pageSize" : 3,
      "totalPages" : 7,
      "currentPage" : 0
    },
    "status" : [ ],
    "datafiles" : [ ]
  },
"result" : {
    "key0": "master",
    "key1": 20,
    "key2": [ "foo", "bar", "baz" ],
    "data" : [ 
      {
        "detailKey0" : "detail0",
        "detailKey1" : [ "foo", "bar" ]
      }, 
      {
        "detailKey0" : "detail1",
        "detailKey1" : [ "bar", "baz" ]
      }, 
      {
        "detailKey0" : "detail2",
        "detailKey1" : [ "baz", "foo" ]
      },
    ]
  }
}
````

Additional documentation is in the [GitHub wiki](https://github.com/plantbreeding/documentation/wiki). 
See especially the [Best Practices and Conventions]
(https://github.com/plantbreeding/documentation/wiki/Best-Practices-and-Conventions).