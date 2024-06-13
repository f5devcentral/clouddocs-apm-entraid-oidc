Lab 2 - Create the APM configuration and policy
###############################################

It time to creeate a new APM policy with Network Access resource

Create the Network Access resources
***********************************

Create a new Lease Pool
=======================

* Access > Connectivity / VPN > Network Access (VPN) > IPv4 Lease Pool
* Create a new Lease Pool
* Start IP address : 10.1.20.200
* End IP address : 10.1.20.210
* Click Add

.. image:: ../pictures/lab2/lease-pool.png
   :align: center
   :scale: 70%

Create a new Address Space
==========================

In order to keep RDP session up in UDF, we will use split-tunneling. Else we will lose control of the RDP session if we use full-tunnel

* Access > Connectivity / VPN > Network Access (VPN) > Address Spaces
* Create a new Address Space
* In IPv4 Address Space, add : 10.1.20.0/24

.. note:: 10.1.20.0/24 is the back end network. The RDP session is on 10.1.1.0/24 network. Meaning we will keep control of the RDP session when tunnel will be up.

.. image:: ../pictures/lab2/address-space.png
   :align: center
   :scale: 70%

Create the Network Access profile
=================================

* Access > Connectivity / VPN > Network Access (VPN) > Network Access Lists
* Create a new Network Access profile, name it as you want (na-oidc)
* Save
* In Network Settings tab

  * IPv4 Lease Pool : select your lease pool created
  * Traffic options : Use split tunneling
  * Dynamic LAN Address Spaces : Remove ``default-all`` and add your Address Space

* Save/Update

.. image:: ../pictures/lab2/na.png
   :align: center


Create the APM policy and VPE
*****************************

* Access > Profiles / Policies
* Create a new policy

  * Nalme : vpn-oidc
  * Profile Type : All
  * Profile Scope : Profile
  * Customization : Modern
  * Languages : English

* Edit the VPE
* Create such VPE

.. image:: ../pictures/lab2/vpe.png
   :align: center

* In Oauth Scope, same settings as previous use case.

.. image:: ../pictures/lab2/vpe-scope.png
   :align: center
   :scale: 70%

* In Advanced Resource Assign, 
* Click ``Add new entry`` and ``Add/Delete`` and assign the Webtop (already created) and the Network Access

.. image:: ../pictures/lab2/resource.png
   :align: center
   :scale: 70%

* Don't forget to change the end DENY to end ALLOW
* Save and Apply the policy

Create the Connectivity profile
*******************************

This is where the OIDC Client mode is set. We will create a custom Connectivity profile so that Edge Client uses OIDC as authentication. Else, Edge client will use the embedded browser (webview)

* Access > Connectivity / VPN > Connectivity > Profiles
* Create a new Connectivity profile
* Give a name : edge-oidc
* Parent profile : /Common/connectivity
* In Win/Mac Edge Client section, under Oauth settings, set the right OIDC Client values (same as previous use case)

  * Provider : EntraID (your provider created in previous lab)
  * Client ID : b55fd307-3270-4208-b059-8c3f292a7934
  * Client Sec*** : g958Q~q8GwHen63sVMoPeIqUHTrSxCVtGvOfTcKA
  * Scopes : api://b55fd307-3270-4208-b059-8c3f292a7934/federate

* In Win/Mac Edge Client section, under Server List, add en entry

  * Alias : vpn
  * Host Name : vpn.itc.demo

.. image:: ../pictures/lab2/vpn-list.png
   :align: center
   :scale: 70%

.. note:: As you can notice, we use the same client settings as previous lab, but instead of using the Client agent in the VPE for APM, we use the Client Agent of the Edge client.

* Save

Create the Virtual Server
*************************

* Create a new VS
* Name : vs-vpn
* Destination Address : 10.1.10.101
* Port : 443
* HTTP Profile : http
* Client SSL profile : client-itc-demo
* Source Address Translation : Auto Map
* Access Profile : vpn-oidc
* Connectivity profile : edge-oidc
* Save

