# Identity Management / Single Sign On

Single Sign On is required to make sure that access to all our services is handled by a centralized component that create authorization tokens that can confirm that the user is authorized to work against different services.

By Default we are using [Keycloak](http://keycloak.org) as our SSO provider. Keycloak supports OAuth2, Kerberos and SAML integrations. We are using the OAuth2 approach. We are also using Keycloak as our Identity Management abstraction layer. Look at the Identity Management Section for more details about this. By using Keycloak we are open the door for social login \(Facebook, Google, etc.\) to our entire platform, which might be useful for cloud deployments.

As most BPM solutions, we require an integration layer between Tasks & Process and the user, groups and roles defined at the organization where you are implementing your solution. For this reason we provide an out of the box integration with Keycloak as our SSO and Identity Management integration layer. Keycloak opens the door to integrate different systems such as custom database, LDAP, Active Directory and Social Logins.

For IDM and SSO you can find the source code and Docker Image in the following repositories:

* [IDM & SSO -&gt; Source Code](http://)
* [IDM & SSO -&gt; Docker Image](http://)

