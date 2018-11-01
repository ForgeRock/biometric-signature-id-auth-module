![BioSig-ID Logo](/imgs/11.png)

# BioSig-ID™ Gesture Biometric Authentication Solution

Document Version 1.0 (Oct/2018)

BioSig-ID™ is a biometric identity authentication solution that requires no hardware or downloads. It authenticates users by capturing their unique movements (gestures) as they draw a password with their finger or mouse. Just four drawn characters are enough to biometrically identify legitimate users and stop imposters.

BioSig-ID plugs your single most vulnerable point of entry. It blends amazing biometric technology with the password format users are comfortable with. The simple change from typing to drawing takes the password from no-security to a technology that is one of the strongest in the world. Unlike other biometrics, it can be reset and revoked.

![BioSig-ID Flow](/imgs/12.png)

# Prerequisites

Establish a ForgeRock OpenAM instance

Acquire a service agreement license from BioSig-ID™

# Create a new Remote Identity Provider

Per instructions located here:  https://backstage.forgerock.com/docs/am/6/saml2-guide/#saml2-configure-remote-idp

[**Procedure 2.3. To Configure a Remote Identity Provider**](https://backstage.forgerock.com/docs/am/6/saml2-guide/#configure-remote-idp)

1. Obtain the identity provider metadata or the URL where you can obtain it.
2. Under Realms >; _Realm Name_ >; Dashboard >; Configure SAMLv2 Providers, click Configure Remote Identity Provider.
3. Provide the identity provider metadata or link to obtain metadata.  - example: &quot;https://verifyexpress.com/interface/saml/[YOUR-ENTITY]/metadata.aspx&quot;

The remote identity provider&#39;s metadata might contain more than one KeyDescriptor elements. If it does, the hosted AM service provider will validate assertions from the identity provider against certificates with key descriptors with an appropriateuse attribute. Incoming assertions that cannot be validated against any of the certificates will be rejected by the hosted service provider.

### Note

Ensure the MetaAlias is unique for each provider configured in a CoT and in the realm.

1. Either add the provider to the circle of trust you already created, or select Add to new and provide a New Circle of Trust name.
2. Click Configure to save your configuration.


![BioSig-ID Steps](/imgs/13.png)
 
1. If you want to use dynamic profile creation with auto-federation to federate identities, configure the required options:
  - To configure dynamic profile creation, navigate to Configure >; Authentication >; Core Attributes.
  - To configure auto-federation, navigate to Realms >; _Realm Name_ >; Applications >; Federation >; Entity Providers >;_Service Provider Name_ >; Assertion Processing >; Auto Federation.
2. Change the Assertion Consumer Service locations in the service provider configuration. The default locations support standalone mode. Therefore, you must change the locations when implementing integrated mode.

Change the locations as follows:

1.
  - In the AM console, navigate to Realms >; _Realm Name_ >; Applications >; Federation >; Entity Providers >; _Service Provider Name_ >; Services >; Assertion Consumer Service.
  - Change the location of the HTTP-Artifact consumer service to use AuthConsumer rather than Consumer. For example, if the location is http://www.sp.com:28080/openam/Consumer/metaAlias/sp, change it to http://www.sp.com:28080/openam/AuthConsumer/metaAlias/sp.
  - Similarly, change the location for the HTTP-POST consumer service to use AuthConsumer rather than Consumer.

Note that you do not need to change the location for the PAOS service because integrated mode does not support the PAOS binding.

1.
  - Click Save to save the changes to the endpoints.
2. If the AM server configured as the service provider runs as part of an AM site, enable SAML v2.0 failover. In the AM console, navigate to Configure >; Global Services, click SAML v2 Service Configuration, check the Enable SAMLv2 failover checkbox, and then save your changes.
3. Create a SAML2 authentication module:
  - In the AM console, navigate to Realms >; _Realm Name_ >; Authentication >; Modules.
  - Specify a name [example: mySAMLChain] for the module, and specify the module type as SAML2.
  - Click Create.
  - Configure the SAML2 authentication module options by adding the &quot;IdP Entity ID&quot; (provided by BioSig-ID™) and changing the request binding to HTTP-Post
  - Save your changes.
4. Create an authentication chain that includes the SAML2 authentication module that you created in the previous step.
5. If you specified a linking authentication chain in the SAML2 module configuration, create the linking chain. A linking chain is an authentication chain that authenticates the user on the service provider, enabling AM to persistently federate a user on the identity and service providers.
6. Test your configuration. First, clear your browser&#39;s cache and cookies. Then, attempt to log in to AM using a login URL that references the authentication chain that includes the SAML2 module. For example,http://www.sp.com:28080/openam/XUI/#login/&amp;service=mySAMLChain.

AM should redirect you to the identity provider for authentication. Authenticate to the identity provider.

If you configured a linking authentication chain, AM should prompt you to authenticate to that chain next. When authentication is complete, try logging out of the service provider, then navigate to the same login URL that you used earlier. Because you are still logged in at the identity provider, you should not be prompted to reauthenticate to the identity provider. And because your identity at the service provider is now federated with your identity at the identity provider, you should not be prompted to reauthenticate at the service provider either.

![BioSig-ID Steps](/imgs/14.png)
![BioSig-ID Steps](/imgs/15.png)
