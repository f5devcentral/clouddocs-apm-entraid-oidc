Lab 2 - Configure APM as OIDC Client and Resource Server
########################################################

* Connect to BIG-IP TMUI as admin / admin.

Create all Oauth OIDC objects
*****************************

Create an Oauth Provider
========================

* Access > Federation > Oauth Client / RS > Provider
* Create a new provider names `EntraID``, type ``MS Identity Platform 2.0``
* In the OpenID URI change ``_tenantid_`` by the Azure Entra ID tenant name or tenant ID -> 8807dced-9637-4205-a520-423077750c60

  * URL must be https://login.microsoftonline.com/8807dced-9637-4205-a520-423077750c60/v2.0/.well-known/openid-configuration

* Click ``Discover``. This creates all the related objects and keys.
* Change Discovery Task Frequency to ``Hours`` and ``save``

.. image:: ../pictures/lab2/oauth-provider.png
   :align: center
   :scale: 70%

Create an Oauth Server
======================

* Access > Federation > Oauth Client / RS > Oauth Server
* Create a new Oauth Server

  * Name : oauth-server-entraid
  * Mode : Client + Resource Server
  * Type : MS Identity Platform 2.0
  * Oauth Provider : Select the previous provider created (EntraID)
  * DNS resolver : f5-aws-dns

* In the Client section

  * Client ID : b55fd307-3270-4208-b059-8c3f292a7934
  * Client sec*** : g958Q~q8GwHen63sVMoPeIqUHTrSxCVtGvOfTcKA
  * ServerSSL profile : apm-default-serverssl

* In the Resource Server section, keep it empty except the ServerSSL profile (apm-default-serverssl)

* Save

.. image:: ../pictures/lab2/oauth-server.png
   :align: center
   :scale: 70%

Update the JWT token configuration
==================================

* Access > Federation > JSON Web Token > Token configuration
* Edit the existing object (created during the provider creation)
* In Audience, add the Oauth ClientID as Audience : b55fd307-3270-4208-b059-8c3f292a7934

.. warning:: Don't forget to click on add button so that the value is added into the list

* Save

.. image:: ../pictures/lab2/token-config.png
   :align: center
   :scale: 70%

Create a JWT provider list
==========================

* Access > Federation > JSON Web Token > Provider List
* Give a name such as ``jwt-provider-entraid``
* Provider : select your Oauth provider (EntraID)
* Click on ``Add``
* Save

.. image:: ../pictures/lab2/provider-list.png
   :align: center
   :scale: 70%



Create the policy and VPE
*************************

* Access > Profiles / Policies
* Create a new policy

  * Name : federate-webtop
  * Profile Type : All
  * Profile Scope : Profile
  * Customization : Modern
  * Languages : English

* Edit the VPE

.. image:: ../pictures/lab2/edit-vpe.png
   :align: center
   :scale: 70%

* Create such VPE

 .. image:: ../pictures/lab2/vpe.png
   :align: center
   :scale: 70%

* Configure Oauth client agent as below

  * Scope must be the scope created in Azure Entra ID application
  * Scope : api://b55fd307-3270-4208-b059-8c3f292a7934/federate
 
  .. image:: ../pictures/lab2/vpe-client.png
     :align: center
     :scale: 70%

.. note:: The Oauth Client agent is the agent asking for the Oauth token. In our use case, APM is the Oauth Agent. APM asks for a token, but also validate the token (next step with Scope)

* Configure Oauth Scope as below

  * Select the JWT provider list created below
 
  .. image:: ../pictures/lab2/vpe-scope.png
     :align: center
     :scale: 70%

.. note:: We select Internal in this use case as the token type is a JWT (compared to Opaque token). JWT is a readable token, meaning APM does not have to do a side-band call to Azure to validate it. APM will use the public key receive during the OAuth provider discovery process, to validate the JWT signature. If the signature and the timestamp are good, token is validated.

* Don't forget to change the end DENY to end ALLOW
* Save and Apply the policy (top left corner)

Create the Virtual Server and assign the APM policy
***************************************************

* Create a virtual server with those settings

  * Name : vs-web-oidc
  * Destination address : 10.1.10.100  
  * Port : 443
  * HTTP profile : http
  * Client SSL : client-itc-demo
  * Source Address Translation : Auto Map
  * Access Profile : federate-webtop
  * Default pool : iis

* Save