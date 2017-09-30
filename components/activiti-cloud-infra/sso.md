# Single Sign On
Single Sign On is required to make sure that access to all our services is handled by a centralized component that create authorization tokens that can
confirm that the user is authorized to work against different services.

By Default we are using [Keycloak](http://keycloak.org) as our SSO provider. Keycloak supports OAuth2, Kerberos and SAML integrations. We are using the OAuth2 approach. We are also using Keycloak as our Identity Management abstraction layer.
Look at the Identity Management Section for more details about this.
By using Keycloak we are open the door for social login (Facebook, Google, etc.) to our entire platform, which might be useful for cloud deployments.

For IDM and SSO you can find the source code and Docker Image in the following repositories:
- [IDM & SSO -> Source Code](http://)
- [IDM & SSO -> Docker Image](http://)
