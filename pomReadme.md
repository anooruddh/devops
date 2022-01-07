# settings.xml
A complete settings.xml file looks like: (typically, this will be /user.home/.m2/settings.xml).

    <?xml version="1.0" encoding="UTF-8"?>
    <settings xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd" xmlns="http://maven.apache.org/SETTINGS/1.0.0"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
      <mirrors>
        <mirror>
          <mirrorOf>*</mirrorOf>
          <name>repo</name>
          <url>http://localhost:8081/artifactory/repo</url>
          <id>repo</id>
        </mirror>
      </mirrors>
      <servers>
        <server>
        <id>central</id>
        <username>admin</username>
        <password>password</password>
        </server>
      </servers>
      <profiles>
        <profile>
          <repositories>
            <repository>
              <snapshots>
                <enabled>false</enabled>
              </snapshots>
              <id>central</id>
              <name>libs-release</name>
              <url>http://localhost:8081/artifactory/libs-release</url>
            </repository>
            <repository>
              <snapshots />
              <id>snapshots</id>
              <name>libs-snapshot</name>
              <url>http://localhost:8081/artifactory/libs-snapshot</url>
            </repository>
          </repositories>
          <pluginRepositories>
            <pluginRepository>
              <snapshots>
                <enabled>false</enabled>
              </snapshots>
              <id>central</id>
              <name>plugins-release</name>
              <url>http://localhost:8081/artifactory/plugins-release</url>
            </pluginRepository>
            <pluginRepository>
              <snapshots />
              <id>snapshots</id>
              <name>plugins-snapshot</name>
              <url>http://localhost:8081/artifactory/plugins-snapshot</url>
            </pluginRepository>
          </pluginRepositories>
          <id>artifactory</id>
        </profile>
      </profiles>
      <activeProfiles>
        <activeProfile>artifactory</activeProfile>
      </activeProfiles>
    </settings>


From maven project pom file

    <distributionManagement>
    <repository>
        <id>central</id>
        <name>ip-172-31-3-145.ap-south-1.compute.internal-releases</name>
        <url>http://35.154.91.240:8081/artifactory/libs-release-local</url>
    </repository>

    <snapshotRepository>
        <id>snapshots</id>
        <name>ip-172-31-3-145.ap-south-1.compute.internal-snapshots</name>
        <url>http://35.154.91.240:8081/artifactory/libs-snapshot-local</url>
    </snapshotRepository>
    </distributionManagement>

# SCM ( in pom file)

        <project>
          ...
          <packaging>jar</packaging>
          <version>1.0-SNAPSHOT</version>
          <name>SCM Sample Project</name>
          <url>http://somecompany.com</url>
          <scm>
            <connection>scm:svn:http://somerepository.com/svn_repo/trunk</connection>
            <developerConnection>scm:svn:https://somerepository.com/svn_repo/trunk</developerConnection>
            <url>http://somerepository.com/view.cvs</url>
          </scm>
          ...
        </project>
        
# Project Details

        <project>
          ...
          <build>
            [...]
            <plugins>
              <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-scm-plugin</artifactId>
                <version>1.12.2</version>
                <configuration>
                  <connectionType>connection</connectionType>
                </configuration>
              </plugin>
              ...
            </plugins
            ...
          </build>
          ...
        </project>


# What is the difference between a Release version and SNAPSHOT version in Maven?

A SNAPSHOT version in Maven is the one that has not been released.

Before every release version there is a SNAPSHOT version. Before 1.0 release there will be 1.0-SNAPSHOT.

If we download 1.0-SNAPSHOT today then we may get different set of files than the one we get on downloading it yesterday. SNAPSHOT version can keep getting changes in it since it is under development.

But release version always gives exactly same set files with each download.

### snapshots are mutable, releases are immutable.

#### A "release" is the final build for a version which does not change. A "snapshot" is a build which can be replaced by another build which has the same name. It implies that the build could change at any time and is still under active development. You have different artifacts for different builds based on the same code.


Just add this to your ~/.m2/settings.xml:


    <profiles>
      <profile>
         <id>allow-snapshots</id>
            <activation><activeByDefault>true</activeByDefault></activation>
         <repositories>
           <repository>
             <id>snapshots-repo</id>
             <url>https://oss.sonatype.org/content/repositories/snapshots</url>
             <releases><enabled>false</enabled></releases>
             <snapshots><enabled>true</enabled></snapshots>
           </repository>
         </repositories>
       </profile>
    </profiles>

And add these following lines in your project's pom.xml file -

    <distributionManagement>
        <snapshotRepository>
            <id>my-snapshots</id>
            <name>My internal repository</name>
            <url>http://localhost:8081/nexus/content/repositories/snapshots</url>
        </snapshotRepository>

        <repository>
            <id>my-releases</id>
            <name>My internal repository</name>
            <url>http://localhost:8081/nexus/content/repositories/releases</url>
        </repository>
    </distributionManagement>
