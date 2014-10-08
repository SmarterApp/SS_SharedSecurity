# Welcome to the Shared Security Component #
The Shared Security component contains reusable components for authentication and authorization. Please review the SAML-setup.md document for additional info on SAML integration.

## License ##
This project is licensed under the [AIR Open Source License v1.0](http://www.smarterapp.org/documents/American_Institutes_for_Research_Open_Source_Software_License.pdf).

## Getting Involved ##
We would be happy to receive feedback on its capabilities, problems, or future enhancements:

* For general questions or discussions, please use the [Forum](http://forum.opentestsystem.org/viewforum.php?f=15).
* Use the **Issues** link to file bugs or enhancement requests.
* Feel free to **Fork** this project and develop your changes!

## Dependencies
SharedSecurity has a number of direct dependencies that are necessary for it to function.  These dependencies are already built into the Maven POM files.

### Compile Time Dependencies
* shared-build-parent
* prog-mgmnt-client
* prog-mgmnt-client-null-impl
* logback-core
* logback-classic
* janino
* slf4j-api
* jcl-over-slf4j
* log4j-over-slf4j
* spring-core
* spring-beans
* spring-context
* spring-webmvc
* spring-web
* spring-expression
* spring-tx
* spring-security-web
* spring-security-config
* spring-security-core
* spring-context
* spring-context-support
* bcprov-jdk15on
* spring-security-saml2-core
* javax.inject
* httpclient
* commons-lang
* joda-time
* jackson-datatype-joda
* jackson-annotations
* jackson-core
* jackson-databind
* guava
* ehcache
* spring-security-oauth2


### Test Dependencies
* spring-test
* hamcrest-core
* commons-io
* junit
* podam


### Runtime Dependencies
* servlet-api
* jsp-api
* jstl-api