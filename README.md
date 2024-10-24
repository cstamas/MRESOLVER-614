# MRESOLVER-614

Facts:
* root POM defines depMgt for level5 1.0.2
* level2 POM defines depMgt for level3=1.0.1, level4=1.0.1 and level5=1.0.1
* without transitive manager (Maven3) all we see is root POM depMgt is applied (level5 = 1.0.2)
* with transitive manager (Maven4) we should see level3 unchanged 1.0.0 (as level2 should not apply own depMgt onto its own dependencies), level4 managed to 1.0.1, and level5 managed by root to 1.0.2 (and not 1.0.1)

Command to run:

```
mvn dependency:tree -Dmaven.repo.local.tail=local-repo -Dmaven.repo.local.tail.ignoreAvailability
```

Example output with 3.9.9: Maven 3 is not transitive regarding dependency management, and it shows 1.0.0 all way down
except for level5 that has applies depMgt from root.
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
[INFO]             \- org.apache.maven.it.mresolver614:level5:jar:1.0.2:compile
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.386 s
[INFO] Finished at: 2024-10-24T18:57:44+02:00
[INFO] ------------------------------------------------------------------------
$
```

Example output with 4.0.0-beta-5: this version is transitive but broken, it applies level2 depMgt onto it's own
dependencies.
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
[INFO]             \- org.apache.maven.it.mresolver614:level5:jar:1.0.2:compile
[INFO] --------------------------------------------------------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] --------------------------------------------------------------------------------------------------------------------------
[INFO] Total time:  0.451 s
[INFO] Finished at: 2024-10-24T18:56:36+02:00
[INFO] --------------------------------------------------------------------------------------------------------------------------
$
```
