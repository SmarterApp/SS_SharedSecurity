# SAML and OAuth

## Authentication Paths

There are three identified use cases that require slightly different authentication paths. These have all been integrated into the Smarter Balanced application layer using Spring Security.

### 1. SAML Assertion
Simple *Authentication*: Most of the web applications are protected by OpenAM. Protected resources include the UI assets (html, .js, images etc.) as well as all REST endpoints. This level of security detects whether an SSO session has been established with OpenAM; if so, it allows access. Otherwise it forces the user to establish a session by logging in. For an application (service provider) protected by OpenAM:

* The application which the user is attempting to access checks if an SSO session is already established. If so, the user is allowed in. If not, the user is presented with a login screen.
* The application sends the user's credentials via a [SAML Assertion](http://openam.forgerock.org/openam-documentation/openam-doc-source/doc/admin-guide/index/chap-auth-services.html) to the SSO provider (OpenAM). If the user exists in the system and the credentials match, an SSO session is established and the user is granted entry into the protected application.
* The SAML response contains a customized section with information about the user's SBAC Tenancy Chain, including the user's assigned roles.

### 2. SAML Bearer Assertion
Refer to [OAuth2 SAML Bearer](http://openam.forgerock.org/openam-documentation/openam-doc-source/doc/webhelp/admin-guide/oauth2-saml2-bearer.html).Used for *Application Access*: This is a high level permission check that matches up the user's roles (returned in the SAML response as part of the OpenAM authentication), with the permissions for the current application (obtained by querying the Permissions application). This step ensures that the user has at least one role that has permission to this app. Additionally, the users’ tenancy is calculated based on whether the entity (state, institution, etc.) associated with the user's role(s) is subscribed to this app and is in "good standing". All of this is configured in the Program Management application. This is how the tenant dropdown is populated and the application is skinned.

This is the case of a logged in user requiring a 'coordinated web service' which is a form of machine-to-machine (M2M) authentication, but on behalf of the logged in user. This essentially converts a user initiated SAML authentication into an OAuth token.

#### Example
ComponentX needs to make a REST call to componentY on behalf of a logged in user. In this case, the component needs to get an OAuth token utilizing a SAML bearer mechanism. This is facilitated using a two-step OAuth token exchange.

**Step 1. Get a token from OpenAM**
POST to https://your.openam.url/auth/oauth2/access_token?realm=/your-realm 
with `Content-Type = application/x-www-form-urlenocded` 
and the following params:
```
grant_type=password
username=<name of valid registered user with the appropriate roles>
password=<Registered user's password>
client_id=<oauth client id as defined in OpenAM>
client_secret=<oauth client's password>
```
That will return a JSON response similar to this:
```
{
    "sbacUUID": "53fb8da1e4b0cedc8f172b45",
    "mail": “<user’s email address>",
    "sn": “<role name>",
    "scope": [
        "sbacUUID",
        "mail",
        "sn",
        "cn",
        "sbacTenancyChain",
        "givenName"
    ],
    "grant_type": "password",
    "cn": “<common name of user>,
    "realm": "/sbac",
    "sbacTenancyChain": “<tenancy chain>",
    "token_type": "Bearer",
    "expires_in": 1605,
    "givenName": "wi",
    "access_token": "8744be5d-0e39-4995-b20e-05b45af3e0d8"
}
```

**Step 2. Call the Service Provider using your access_token**
Call the service provider endpoint with the token. To include the access_token you need to put it in the header of the call by using this format, for example:
`Authorization: Bearer 8744be5d-0e39-4995-b20e-05b45af3e0d8` 

### 3. OAuth client credentials grant
Refer to [Oauth2 Client Credentials](http://openam.forgerock.org/openam-documentation/openam-doc-source/doc/webhelp/admin-guide/oauth2-client-cred.html). This is a purely M2M (no user involved). Batch operations, bootstrap communications, etc. require this type of authentication. Since it's not performed on behalf of a user, it simply uses a pre-established clientId and client secret.

If the request from componentX to componentY has no logged in user, there is a different mechanism to get an OAuth token. This requires having a user provisioned in ART that is specifically used to grant access (via the appropriate role) to allow the client to talk to the service provider. In this case a similar process is followed to the SAML bearer assertion above, with `password` grant type of this specially provisioned user.
