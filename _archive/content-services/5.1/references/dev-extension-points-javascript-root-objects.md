---
author: Alfresco Documentation
---

# JavaScript Root Objects

A number of JavaScript root objects are available when you are implementing a controller for a Web Script, such as `companyhome` and `people`. Sometimes you might have custom Java code that you want to call from JavaScript controllers, this is possible by adding custom JavaScript root objects.

|Information|JavaScript Root Objects|
|-----------|-----------------------|
|Support Status|[Full Support](http://docs.alfresco.com/support/concepts/su-product-lifecycle.html)|
|Architecture Information|[Platform Architecture](../concepts/dev-platform-arch.md)|
|Description|It is possible to create and add custom script APIs implemented in Java and accessible as root objects in JavaScript. This provides an integration point for Alfresco extensions to provide custom JavaScript APIs where appropriate.

 In order to implement a custom JavaScript API it is recommended that you develop a POJO \(Plain Old Java Object\) that extends the base class `org.alfresco.repo.processor.BaseProcessorExtension`. The `public` methods of your class will be those that will be accessible from JavaScript.

 Let's implement a custom root object that can print text to the log \(i.e. tomcat/logs/catalina.out\):

 ```
package org.alfresco.training.jscript;
import org.alfresco.repo.processor.BaseProcessorExtension;

public class CustomProcessorExtension extends BaseProcessorExtension {
 public void log2StdOut(String text) {
  System.out.println(text);
 }
}   
```

 This class needs to be defined as a Spring Bean with parent set to `baseJavaScriptExtension`:

 ```
<bean id="org.alfresco.training.customRootObject" 
         class="org.alfresco.training.jscript.CustomProcessorExtension" 
         parent="baseJavaScriptExtension">
    <property name="extensionName" value="custom" />
</bean>
```

 The `extensionName` property is used to configure what root object name you want to use in the JavaScript controllers.

 The new `custom` root object can now be used in the JavaScript controller as follows:

 ```
custom.log2StdOut("Hello World!");
```

|
|Deployment - App Server|JavaScript root object implementations does not lend themselves very well to be manually installed in an application server. See Deployment SDK Project instead.|
|[Deployment - SDK Project](../tasks/alfresco-sdk-tutorials-amp-archetype.md)|-   repo-amp/src/main/java - put the implementation class for the root object somewhere under this directory
-   repo-amp/src/main/amp/config/alfresco/module/repo-amp/context/service-context.xml - put the root object Spring bean here.


|
|More Information|-   [Repository side JavaScript root objects reference](API-JS-rootscoped.md) \(Have a look at what root objects are available for Repo Web Scripts etc\)
-   [Surf root object reference](APISurf-rootscoped.md) \(Have a look at what root objects are available for Surf Web Scripts\)

|
|Sample Code|None|
|Tutorials|[SDK Deployment tutorials](../concepts/alfresco-sdk-tutorials-archetypes.md)|
|Alfresco Developer Blogs|None|

**Parent topic:**[Platform extension points](../concepts/dev-platform-extension-points.md)

