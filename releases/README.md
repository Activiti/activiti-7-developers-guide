# Releases

{% hint style="info" %}
Since 8.2.0 we no longer generate release pages.

Releases lists are available on Github release pages:

* ​[Activiti](https://github.com/Activiti/Activiti/releases)​
* [Activiti Cloud​](https://github.com/Activiti/activiti-cloud/releases)
* ​[Activiti Cloud Common Chart​](https://github.com/Activiti/activiti-cloud-common-chart/releases)
* ​[Activiti Cloud Full Chart​](https://github.com/Activiti/activiti-cloud-full-chart/releases)
{% endhint %}

The Maven artifacts can be consumed from Alfresco Nexus:

```markup
<repositories>
  <repository>
    <id>activiti-releases</id>
    <url>https://artifacts.alfresco.com/nexus/content/repositories/activiti-releases</url>
  </repository>
</repositories>
```

_Activiti Cloud:_

```markup
<dependencyManagement>
   <dependencies>
     <dependency>
       <groupId>org.activiti.cloud</groupId>
       <artifactId>activiti-cloud-dependencies</artifactId>
       <version>VERSION</version>
       <scope>import</scope>
       <type>pom</type>
     </dependency>
   </dependencies>
 </dependencyManagement>
```

_Activiti Core_

```markup
  <dependencyManagement>
    <dependencies>
      <dependency>
        <groupId>org.activiti</groupId>
        <artifactId>activiti-dependencies</artifactId>
        <version>VERSION</version>
        <scope>import</scope>
        <type>pom</type>
      </dependency>
    </dependencies>
  </dependencyManagement>
```
