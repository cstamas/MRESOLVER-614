# MRESOLVER-614

Command to run:

```
mvn dependency:tree -Dmaven.repo.local.tail=local-repo -Dmaven.repo.local.tail.ignoreAvailability
```

Example output with 3.9.9:
```
$ mvn -V dependency:tree -Dmaven.repo.local.tail=local-repo -Dmaven.repo.local.tail.ignoreAvailability
Apache Maven 3.9.9 (8e8579a9e76f7d015ee5ec7bfcdc97d260186937)
Maven home: /home/cstamas/.sdkman/candidates/maven/3.9.9
Java version: 21.0.4, vendor: Eclipse Adoptium, runtime: /home/cstamas/.sdkman/candidates/java/21.0.4-tem
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "6.11.4-201.fc40.x86_64", arch: "amd64", family: "unix"
[INFO] Scanning for projects...
[INFO] 
[INFO] ---------------< org.apache.maven.it.mresolver614:root >----------------
[INFO] Building root 1.0.0
[INFO]   from pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- dependency:3.7.0:tree (default-cli) @ root ---
[INFO] org.apache.maven.it.mresolver614:root:jar:1.0.0
[INFO] \- org.apache.maven.it.mresolver614:level1:jar:1.0.0:compile
[INFO]    \- org.apache.maven.it.mresolver614:level2:jar:1.0.0:compile
[INFO]       \- org.apache.maven.it.mresolver614:level3:jar:1.0.0:compile
[INFO]          \- org.apache.maven.it.mresolver614:level4:jar:1.0.0:compile
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.368 s
[INFO] Finished at: 2024-10-24T18:44:14+02:00
[INFO] ------------------------------------------------------------------------
$
```
Maven 3 is not transitive re dependency management, and it shows 1.0.0 all way down.

Example output with 4.0.0-beta-5:
```
$ mvn -V dependency:tree -Dmaven.repo.local.tail=local-repo -Dmaven.repo.local.tail.ignoreAvailability
Apache Maven 4.0.0-beta-5 (6e78fcf6f5e76422c0eb358cd11f0c231ecafbad)
Maven home: /home/cstamas/.sdkman/candidates/maven/4.0.0-beta-5
Java version: 21.0.4, vendor: Eclipse Adoptium, runtime: /home/cstamas/.sdkman/candidates/java/21.0.4-tem
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "6.11.4-201.fc40.x86_64", arch: "amd64", family: "unix"
[WARNING] Unable to find the root directory. Create a .mvn directory in the root directory or add the root="true" attribute on the root project's model to identify it.
[WARNING] Legacy/insecurely encrypted password detected for server my-legacy-server
[WARNING] Legacy/insecurely encrypted password detected for server my-legacy-broken-server
[INFO] Scanning for projects...
[INFO] 
[INFO] ----------------------------------------< org.apache.maven.it.mresolver614:root >-----------------------------------------
[INFO] Building root 1.0.0
[INFO]   from pom.xml
[INFO] ---------------------------------------------------------[ jar ]----------------------------------------------------------
[INFO] 
[INFO] --- dependency:3.8.0:tree (default-cli) @ root ---
[INFO] org.apache.maven.it.mresolver614:root:jar:1.0.0
[INFO] \- org.apache.maven.it.mresolver614:level1:jar:1.0.0:compile
[INFO]    \- org.apache.maven.it.mresolver614:level2:jar:1.0.0:compile
[INFO]       \- org.apache.maven.it.mresolver614:level3:jar:1.0.1:compile
[INFO]          \- org.apache.maven.it.mresolver614:level4:jar:1.0.1:compile
[INFO] --------------------------------------------------------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] --------------------------------------------------------------------------------------------------------------------------
[INFO] Total time:  0.451 s
[INFO] Finished at: 2024-10-24T18:44:34+02:00
[INFO] --------------------------------------------------------------------------------------------------------------------------
$
```
This version is broken, as is transitive, but it applies node dependency management onto itself. Level3 should be 
1.0.0.
