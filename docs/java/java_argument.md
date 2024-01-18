# Snippets for JAVA JVM arguments

```java
-Xmx4G -Duser.timezone=UTC -Duser.timeControl=true 
-javaagent:"${env_var:HOMEPATH}/.m2/repository/org/springframework/spring-instrument/3.2.18.RELEASE/spring-instrument-3.2.18.RELEASE.jar" -Duser.timezone=UTC -Duser.timeControl="true" -Dfilejipdatadir="file://${env_var:CIQ_USER_HOME}" -Dmaven.multiModuleProjectDirectory=%M3_HOME% -DsitePublishDir="file://${env_var:CIQ_USER_HOME}/sitePublish" -javaagent:"${env_var:HOMEPATH}/.m2/repository/org/jmockit/jmockit/1.17/jmockit-1.17.jar" 
```