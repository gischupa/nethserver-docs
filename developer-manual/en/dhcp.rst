====
DHCP
====

.. warning:: This page is outdated since ``nethserver-dnmasq-1.3.0`` release

The system can act as DHCP server for the local network.
Machines which are configured by DHCP have their names automatically included in the DNS server.

The DHCP can be enabled only on *green* and *blue* interfaces (see :ref:`section-roles-and-zones`).
Configuration is saved inside the ``dhcp`` database. 

Each record of ``range`` type is associated to an ethernet interface and can have following properties:

* ``status``: can be ``enabled`` or ``disabled``
* ``DhcpRangeStart``: first IP address of DHCP range
* ``DhcpRangeEnd``: last IP address of DHCP range
* ``DhcpLeaseTime``: seconds of lease time. Default is 86400
* ``DhcpGatewayIp``: (optional) set a custom gateway ip. If not set, the gateway is the ip address of associated interface (record key)

The key of the record is the name of the associated interface. Example: ::

  eth0=range
    DhcpGatewayIp=
    DhcpLeaseTime=86400
    DhcpRangeEnd=192.168.1.100
    DhcpRangeStart=192.168.5.200
    status=enabled


Hosts inside the blue network can always access the local DNS server.

.. note:: If a record is a related to an interface without a role, the record is automatically deleted.


The gateway for clients will be:

* if set, the value of property ``DhcpGatewayIp``
* otherwise if the server has a red interface, the gateway is the IP address of the interface where the DHCP is enabled 
  (eg. IP of the blue interface for clients in the guest's network)
* otherwise if the server has only a green interface, the gateway of the green interface will be used


.. _ip_reservation-section:

IP reservation
==============

It's possible to reserve IPs for specific devices associating the MAC address of the device with the reserved IP.
The reservation is saved inside the ``hosts`` database.

Example: ::

  host1=local
      Description=Internal network host #1
      IpAddress=192.168.1.23
      MacAddress=08:00:27:48:BF:F3

