# maven-scm-provider-dimensionscm
Maven SCM Provider for Micro Focus Dimensions CM ([https://www.microfocus.com/products/dimensions-cm](https://www.microfocus.com/products/dimensions-cm))

## Overview

The Maven SCM Provider for Dimensions CM implements common SCM operations such as updating a work area, delivering changes, creating a new tag/baseline etc from a Dimensions CM Stream or Project using Maven.

### Pre-Requisites

To use the Maven SCM Provider for Dimensions CM you will need to have the Dimensions CM client software installed and the `dmcli` executable on your system path.

### Plugin Configuration

The Maven SCM Provider for Dimensions CM has been deployed to Maven Central so there is no need to build it from sources or download it yourself manually.  

To use the provider you must first declare it as a build plugin in your pom.xml, e.g:

    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-scm-plugin</artifactId>
          <version>1.11.2</version>
          <dependencies>
            <dependency>
              <groupId>com.microfocus.adm.dimensionscm</groupId>
              <artifactId>maven-scm-provider-dimensionscm</artifactId>
              <version>1.11.2.1</version>
            </dependency>
          </dependencies>
        </plugin>
      </plugins>
    </build>

Depending on your exact use case you may need to declare the Maven SCM Provider for Dimensions CM as a dependency of your maven-release-plugin or other plugins.

## SCM Configuration

You must also add a `<scm>` section to your pom.xml to define a URL which describes the Dimensions CM connection and Product/Stream/Project to use.  For example:

    <scm>
      <connection>scm:dimensionscm://myusername:mypassword@cmserver:671/cm_typical@dim14/qlarius:java_brancha_str</connection>
    </scm>
 

## URL Format


`scm:dimensionscm://username:password@hostname:[port number]/databasename@connection/product:stream/[directory]`

where:

- username:password - Specifies the credentials to login to Dimensions CM.
- hostname - Specifies the hostname of the Dimensions CM server.
- [port number] - Specifies an optional port number on the server.
- databasename - Specifies the base database to use.
- connection - Specifies the database connection to use.
- product - Specifies the name of the product to use.
- stream - Specifies the name of the stream to use.
- [directory] - Specifies an optional path to a directory within the stream.

### Example URL:

scm:dimensionscm://dinesh:mypassword@cmserver:671/cm\_typical@dim14/qlarius:java\_brancha\_str

This will authenticate to Dimensions CM as the user dinesh, to the server called cmserver (on port 671).  It will connect to the base database cm\_typical using the dim14 connection.  SCM operations will be performed in the QLARIUS product in the stream JAVA\_BRANCHA\_STR.

## SCM Commands

### scm:add 
Deliver newly created files to the stream.

Attributes:   

- dimensionscm.requests - comma separated list of requests.  
	For example: `-Ddimensionscm.requests="QLARIUS\_TASK\_1,QLARIUS\_TASK\_2"`
- includes - comma separated list of includes file patterns.  
    For example: `-Dincludes="**/*.txt,**/*.xml"`
- excludes- comma separated list of excludes file patterns.  
    For example: `-Dexcludes="**/base/*,**/*.xml"`
- message – comment/message to apply to the delivery.

### scm:checkin 
Deliver updated/deleted/created files to the stream. Delivery of newly created files is switched off by default (you can enable it by specifying the attribute `add`).  

Attributes:

- dimensionscm.requests - comma separated list of requests.
- includes - comma separated list of includes file patterns.
- excludes - comma separated list of excludes file patterns.
- message - comment/message to apply to the delivery.
- dimensionscm.add - property (boolean value) for enabling or disabling additions on check-in.  
    For example: `-Ddimensionscm.add=true`
- dimensionscm.all - property (boolean value) for enabling or disabling whether all foreign content is allowed to be delivered to a stream.  
    For example: `-Ddimensionscm.all=true`
- dimensionscm.relativeLocation - property for passing a project/stream relative location.  
    For example: `-Ddimensionscm.relativeLocation="src/main/java"`

### scm:checkout
Get a fresh copy of the latest source from the configured stream. Overwrites any local changes.

Attributes:

- dimensionscm.requests - comma separated list of requests.
- includes - comma separated list of includes file patterns.
- excludes - comma separated list of excludes file patterns.
- dimensionscm.baseline - property for passing a baseline name, used to update the work area from the specified baseline rather than a stream.  
    For example: `-Ddimensionscm.baseline="bsl1"`
- dimensionscm.relativeLocation - property for passing a project/stream relative location.

### scm:update
Update the local working copy with the latest source from the configured stream.
Does not overwrite local changes.

Attributes:

- dimensionscm.requests - comma separated list of requests.
- includes - comma separated list of includes file patterns.
- excludes - comma separated list of excludes file patterns.
- dimensionscm.baseline - property for passing a baseline name, used to update the work area from the specified baseline rather than a stream.
- dimensionscm.relativeLocation - property for passing a project/stream relative location.

### scm:changelog
Display the changelog contents on the console.

Attributes:

- startDate - Earliest date/time to display changelog contents for.  
    For example: `-DstartDate="2019-12-21 09:00" -DuserDateFormat="yyyy-MM-dd HH:mm"`
- endDate  - Latest date/time to display changelog contents for.  
    For example: `- DendDate="2019-12-21 09:00" -DuserDateFormat="yyyy-MM-dd HH:mm"`
- dimensionscm.filename – parameter which allows filtering the output of the LOG command by filename/file pattern.  
    For example: `-Ddimensionscm.filename="**/*.java"`

### scm:status
Displays the modification status of the files in the configured stream.

Attributes:

- includes - comma separated list of includes file patterns.
- excludes - comma separated list of excludes file patterns.
- dimensionscm.add - property (boolean value) for enabling or disabling display of additions.
- dimensionscm.all - property (boolean value) for enabling or disabling whether foreign content is displayed.

### scm:tag
Create a release baseline from the current stream version.

Attributes:

- includes - comma separated list of includes file patterns.
- excludes - comma separated list of excludes file patterns.
- tag - name of the baseline to be created.
- dimensionscm.requests - comma separated list of requests to relate to the new baseline.
- dimensionscm.type - specifies the type name for the baseline that will be created.  
    For example: `-Ddimensionscm.type=label`
- dimensionscm.attributes - property for passing user defined attributes for the new baseline (in json format).  
    For example: `-Ddimensionscm.attributes="{db:oracle,years:[2000,2009,2011,2018]}"`