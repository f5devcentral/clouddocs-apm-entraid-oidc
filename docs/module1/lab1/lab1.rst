Lab 1 - READ ONLY - Create the Application in Azure Entra ID
############################################################

.. warning:: In this lab, you don't have to do anything. Everything is already created for you in Azure.

Create the Azure Entra ID Application
*************************************

In Azure Entra ID, go to ``App Registrations``

* Click on New Registration

.. image:: ../pictures/lab1/new-registration.png
   :align: center

* Give a name 
* For the redirect option, enter the FQDN of the app exposed on the APM, and append this suffix ``/oauth/client/redirect``

Example : https://federate.itc.demo/oauth/client/redirect

* Click Register

.. image:: ../pictures/lab1/register.png
   :align: center


Configure your Entra ID app
***************************

* Find your Entra ID Application and Edit it
* Copy and Save those ID - we will use them into APM

  * Application ID (this is the OIDC client ID) : b55fd307-3270-4208-b059-8c3f292a7934
  * Tenant ID or Tenant name : f5access.onmicrosoft.com

  .. image:: ../pictures/lab1/ids.png
     :align: center

* In Authentication menu, check the Web Redirect URIs. Must be the one defined previously. Don't change any other settings, we will use OIDC Autorization Grant Flow.

  .. image:: ../pictures/lab1/redirect.png
     :align: center
     
* In Certificate and Secrets, create a ``Client Secret`` and save the ``Value`` not the ``Secret ID``. This is the OIDC Secret ID we will use in APM.

  .. image:: ../pictures/lab1/secret-id.png
     :align: center

Terminology
===========

**HTTP Load Balancer**
F5XC reverse proxy and HTTP load balancer concepts allow for flow control of application and API traffic between services, to the internet, and from clients on the internet. HTTP Load Balancers allow for steering decisions based on URI or other HTTP based criteria.

**Origin Pool**
An origin pool is a mechanism to configure a set of application endpoints grouped together into a resource pool. These endpoints could be IP:port tuples within a give site or a service discovered by one of Volterra’s many service discovery methods. These objects will be used the next step.


.. note:: The goal is to publish this application through the F5XC Global Network so people all over the world can reach the closest F5XC RE.

 