# boxlang-couchbase-starter

## Seamless Integration: Using Couchbase with BoxLang
This application explores how to leverage the power of [Couchbase](https://www.couchbase.com/), a NoSQL database, within your [BoxLang](https://boxlang.io) applications. We'll demonstrate the ease of utilizing existing Java libraries through BoxLang's [interoperability](https://boxlang.ortusbooks.com/boxlang-language/syntax/java-integration) features.
## Introduction:
BoxLang, a fresh, dynamic JVM language, offers exciting possibilities for developers. Its strength lies in its multi-runtime capabilities, allowing code to run on various platforms, giving you tons of Object-Oriented (OO), Functional Programming (FP) Constructs, and dynamic Metadata Programming (MP).  You can try it at https://try.boxlang.io/.  This post dives into how BoxLang seamlessly integrates with the popular [Couchbase Java SDK](https://docs.couchbase.com/java-sdk/current/hello-world/start-using-sdk.html), enabling you to interact with your Couchbase servers directly from your BoxLang application.
### Prerequisites:
- Basic understanding of BoxLang syntax
- Basic understanding of CommandBox
- Familiarity with Couchbase concepts (optional)
## Setting Up BoxLang Environment:
 - CommandBox Module Installation:
Install the module BoxLang for [CommandBox](https://www.ortussolutions.com/products/commandbox), the powerful toolset for BoxLang and CFML development. Use the following command in your terminal:
`box install commandbox-boxlang`

- BoxLang Server Startup:
Start a BoxLang server using CommandBox:
`box server start cfengine=boxlang javaVersion=openjdk21_jdk`

## Interacting with Couchbase using BoxLang:
### Retrieving Couchbase Jars:

Since BoxLang integrates Java libraries, we'll need the Couchbase Java client JARs. Download them from a repository like https://jar-download.com/ and extract them into a lib directory within your BoxLang project.  I
got them from https://jar-download.com/artifacts/com.couchbase.client/java-client  (one caveat is to remove the slf4j-api-1.7.36.jar jar file because its included with the BoxLang thus not needed or it will cause conflicts)


### Enabling Java Classpath:

BoxLang's Application.bx file manages [application settings](https://boxlang.ortusbooks.com/boxlang-framework/applicationbx). Application.bx is a special BoxLang file used to control events (such as onApplicationStart, onRequestStart, etc) and apply configuration to your web application.  Modify yours to include the lib directory containing the Couchbase JARs:

```
class {

    this.javaSettings = {
        loadPaths = [ "./lib" ],
        reloadOnChange = false
    }

}
```

### Creating a BoxLang Class:

Create a couchbase.bx class file containing the BoxLang code to interact with Couchbase. You can learn more about how to sturcture your BoxLang program at https://boxlang.ortusbooks.com/boxlang-language/program-structure.
Here's a what the couchbase.bx class looks like based off the Java sample provided by Couchbase

```
import java:com.couchbase.client.java.Cluster;
import java.java.time.Duration;

class Couchbase {

    // Update connection details
    connectionString = "couchbase://your-server-address";
    username = "your-username";
    password = "your-password";
    bucketName = "your-bucket-name";

    function main() {

        cl = Cluster.connect(connectionString, username, password);

        // Get bucket reference
        b = cl.bucket(bucketName);
        b.waitUntilReady(Duration.ofSeconds(10));
        collection = b.defaultCollection();

        // Upsert document
        upsertResult = collection.upsert("my-document", {"name": "Curt Gratz"});

        // Get document
        getResult = collection.get("my-document");
        name = getResult.contentAsObject().getString("name");
        return name;  // Should return "Curt Gratz"
    }
}
```


**Explanation**
- Imports: Import necessary Java classes for Couchbase operations.
- Connection Details: Update connection string, username, password, and bucket name with your actual values.
main Function: This function establishes a connection, retrieves a bucket reference, performs an upsert operation, fetches the document, and returns the retrieved name.
- Connect to the cluster, then the bucket you interact with
- Uses the default collection on the bucket
- Upserts (inserts if not existing or else updates) a document to your bucket
- Retrieves the document

### Creating a BoxLang Template:

Develop a BoxLang template (couchbase.bxm) to display the results on a webpage:


```
<bx:set cbs = createObject('component','couchbase')>  <h1>
    Hello Couchbase
</h1>
<p>
    Thanks for upserting and then retrieving the name:
    <b><bx:output>#cbs.main()#</bx:output></b>
</p>
```


**Explanation:**

- bx:set: Create a variable cbs to hold an instance of the couchbase class.
- Template Output: Display a message and utilize bx:output to call the main function of the couchbase class and display the retrieved name.
## Conclusion:
This example showcases the seamless integration of Couchbase with BoxLang. BoxLang's Java interoperability allows developers to leverage existing Java libraries, empowering them to build powerful and dynamic applications.