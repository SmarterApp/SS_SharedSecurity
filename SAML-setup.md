# SAML Security Setup

SAML-based security is implemented via the Spring Security SAML Extension to connect your application (the "Service Provider" or SP) to the Single Sign On system (OpenAM - the Identity Provider).

## How-To Documentation 
(This is a highly recommended read and will explain the process in detail)

* **Spring SAML reference (general):** http://docs.spring.io/spring-security-saml/docs/1.0.0.RC3-SNAPSHOT/reference/html/
* **Source**: https://github.com/spring-projects/spring-security-saml/
* **Spring wiring, certificates**: http://docs.spring.io/spring-security-saml/docs/1.0.0.RC3-SNAPSHOT/reference/html/chapter-configuration.html

Components such as *Test Authoring* and *Test Spec Bank* are integrated with SSO using the *Shared Security* JAR, so a dependency on that component needs to be added to the POM of the project you’re working on (refer to those project's POMs for examples).

## Passing SAML Headers ##
Also, a JS include from the web app to the Test Authoring REST project’s [nothing.js](https://bitbucket.org/sbacoss/testauthoring/src/97a94c9138b96ca1a288a5782ae696e139292d0e/rest/src/main/webapp/resources/js/?at=default) needs to be done (that helps with passing the SAML headers to the REST component from the UI). AJAX calls by default weren’t passing the SAML headers - but a request for a web resource did. That establishes the session with the REST server & from there out it works.

For example: in Test Authoring’s web app’s jsp, they do this:

```
#!javascript

<script src="${testauthBaseUrl}resources/js/nothing.js"></script>

```

## Enabling SAML Security in Your Web App ##

* Create an SP file for your app (one per app per environment). It should contain at least this NameId format:
`urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`

* Go to Open AM UI
    * Federation -> Entity Providers -> Import Entity, choose the appropriate realm, then upload your SP file.
    * The IdP entry must be configured with the following NameId formats, in the order specified:
```
#!java

  urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress
  urn:oasis:names:tc:SAML:2.0:nameid-format:transient

```

* Then hit save/back and repeat for each new SP file.
* Add the new entity providers to circle of trust by clicking on the sbac realm and adding them from the left column to the right.
* For web app only:
    * add a logout link in jsp ( <li><a href="saml/logout">Logout</a></li>)
    * add logout.jsp
    * add tenant drop down & web service
    * add progman property for:
        * ${testreg.security.profile} 
        * ${testreg.security.idp}
        * ${component.name}
    * update UI to hook in logout button and associated actions