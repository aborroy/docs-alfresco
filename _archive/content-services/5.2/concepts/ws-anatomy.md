---
author: Alfresco Documentation
---

# URI anatomy

Web scripts are invoked through their defined URIs. Every web script URI follows the same form.

For example:

`http[s]://<host>:<port>/[<contextPath>/]/<servicePath>[/<scriptPath>] [?<scriptArgs>]`

The `host`, `port`, and `contextPath` are all predefined by where the Alfresco Content Services server is installed. By default, the `contextPath` is `alfresco`.

The Web Script Framework is mapped to `servicePath`. All Alfresco Content Services server URL requests that start with `/<contextPath>/<servicePath>` trigger the Web Script Framework into action by assuming that a web script is to be invoked. By default, there are two variations of `servicePath` that are acceptable: `/service` and an abbreviated version `/s`.

Both of the following URIs will invoke a web script, in this case an admin call:

-   `curl -uadmin:admin "http://localhost:8080/alfresco/service/api/admin/usage"`
-   `curl -uadmin:admin "http://localhost:8080/alfresco/s/api/admin/usage"`

The `scriptPath` identifies the web script to invoke and is defined by the web script itself. It must be unique within an Alfresco Content Services server. Duplicate URIs result in a web script registration failure and one of the URIs will have to be adjusted before successful registration. A `scriptPath` can be as simple or as complex as required and can comprise many path segments. For example, the CMIS web script URI to retrieve children of a folder residing in the repository contains the folder path. The following command line retrieves the children of the Data Dictionary folder as an Atom feed:

`curl -uadmin:admin "http://localhost:8080/alfresco/s/cmis/p/Data%20Dictionary/children"`

Finally, a web script URI can support query parameters as defined by the web script to control its behavior. For example, the CMIS web script to retrieve folder children can be restricted to return only documents, filtering out folders:

`curl -uadmin:admin "http://localhost:8080/alfresco/s/cmis/p/Data%20Dictionary/children?types=documents"`

There are some query parameters that apply to all web script invocations such as `alf_ticket` and `format`, which can be mixed with web script specific parameters:

`curl -uadmin:admin "http://localhost:8080/alfresco/s/cmis/p/Data%20Dictionary/children?types=documents&format=atomfeed"`

When in doubt over how to construct a URI for a given web script, consult its web script descriptor file, which you can find by using the web script index. The web script index can be displayed by directing your browser to the following URL:

`http://localhost:8080/alfresco/service/index`

**Parent topic:**[Web Script Framework](../concepts/ws-framework.md)

