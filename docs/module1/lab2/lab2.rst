Lab 2 - Configure APM as OIDC Client and Resource Server
########################################################

Create all Oauth OIDC objects
*****************************

Create an Oauth Provider
========================

* Access > Federation > Oauth Client / RS > Provider
* Create a new provider, type ``MS Identity Platform 2.0``
* In the OpenID URI change ``_tenantid_`` by the Azure Entra ID tenant name or tenant ID -> 8807dced-9637-4205-a520-423077750c60

  * URL must be https://login.microsoftonline.com/8807dced-9637-4205-a520-423077750c60/v2.0/.well-known/openid-configuration

* Click Discover. This creates all the related objects and keys.
* Change Discovery Task Frequency to ``Hours``

.. image:: ../pictures/lab2/oauth-provider.png
   :align: center
   :scale: 70%

Create an Oauth Server
======================

* Access > Federation > Oauth Client / RS > Oauth Server
* Create a new Oauth Server

  * Mode : Client + Resource Server
  * Type : MS Identity Platform 2.0
  * Oauth Provider : Select the previous provider created
  * DNS resolver : root-dns (created earlier by me)

* In the Client section

  * Client ID : b55fd307-3270-4208-b059-8c3f292a7934
  * Client sec*** : g958Q~q8GwHen63sVMoPeIqUHTrSxCVtGvOfTcKA

* Save

.. image:: ../pictures/lab2/oauth-server.png
   :align: center
   :scale: 70%

Update the JWT token configuration
==================================

* Access > Federation > JSON Web Token > Token configuration
* Edit the existing object (created during the provider creation)
* In Audience, add the Oauth ClientID as Audience : b55fd307-3270-4208-b059-8c3f292a7934
* Save

.. image:: ../pictures/lab2/token-config.png
   :align: center
   :scale: 70%

Create a JWT provider list
==========================

* Access > Federation > JSON Web Token > Provider List
* Give a name such as ``jwt-provider-entraid``
* Provider : select your Oauth provider

.. image:: ../pictures/lab2/provider-list.png
   :align: center
   :scale: 70%



Create the policy and VPE
*************************



Create the Virtual Server and assign the APM policy
***************************************************


