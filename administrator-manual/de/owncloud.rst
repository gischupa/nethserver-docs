========
ownCloud
========

`ownCloud <http://owncloud.org/>`_ ermöglicht den Zugriff auf private Dateien über das Internet 
oder das lokale Netzwerk. Dies kann vom PC erfolgen, oder via App vom Tablet oder Smartphone.
Darüber hinaus stellt Owncloud eine Plattformübergreifende Möglichkeit für das einfache Abgleichen 
von Kontakten, Kalendern und Lesezeichen dar. Es ist außerdem möglich, einfache Dokumente direkt 
online zu bearbeiten.


**Schlüsselfunktionen:**

* Vorkonfiguration von :index:`ownCloud` mit mysql und default Zugangsberechtigungen
* Vorkonfiguration von httpd 
* Einbindung der Systembenutzer und Gruppen von |product|
* Dokumentation
* Backup der OwnCloud-Daten mit dem *nethserver-backup-data tool*


Installation
============

Die Installation erfolgt über die Web-Oberfläche von  |product|.
Nach der Installation:

* Öffnen der Adresse https://your_nethserver_ip/owncloud
* Anmelden mit den Daten **admin/Nethesis,1234**
* Änderung des voreingestellten Kennworts

Standardmäßig ist die LDAP-Anmeldung aktiviert, so dass jeder Benutzer mit seinen üblichen
Anmeldedaten auf OwnCloud zugreifen kann.
Nach der Installation befindet sich auf der Web-Oberfläche von |product| ein neues Anwendungssymbol.

LDAP Konfiguration
==================

.. note:: Neue Installationen erfordern dies nicht, da die Konfiguration automatisch erfolgt.

#. Kopieren des LDAP-Kennworts mit folgendem Befehl: ::

    cat /var/lib/nethserver/secrets/owncloud

#. Anmeldung als Administrator in OwnCloud 
#. Suchen des Backends für LDAP und Gruppen: *Anwendungen -> LDAP Benutzer und Gruppen*
#. Aktivieren von "LDAP Benutzer und Gruppen"
#. Einstellen der Server-Parameter: *Admin -> Admin -> Server*
#. Eintragen "Server" folgenden Daten: ::

    Host: localhost:389
    Port: 389
    DN user: cn=owncloud,dc=directory,dc=nh
    Password: "kopiertes Kennwort von oben"
    DN base: dc=directory,dc=nh

#. Eintragen "Benutzerfilter": ::

    Änderung des Filters auf: (&(objectClass=person)(givenName=*))

#. Eintragen "Zugriff" ::

    Änderung des Filters auf: uid=%uid

#. Eintrag "Gruppenfilter": ::

    Änderung des Filters auf (&(objectClass=posixGroup)(memberUid=*))

#. Einstellungen "Erweitert": ::

    Directory settings
        Display username: cn
        User structure base: dc=directory,dc=nh
        Display group name: cn
        Group structure base: dc=directory,dc=nh
        Group-member association -> memberUid

    Special Attributes
        Email field: email

#. Einstellungen "Expert": ::

    Internal username Attribute: uid
    Click on "Clear Username-LDAP user mapping" 

#. "Save" Knopf anklicken

LDAP Note
=========


User list
---------

After ownCloud LDAP configuration, the user list can show some usernames containing random numbers.
This is because ownCloud ensures that there are no duplicate internal usernames as reported in section `Internal Username. <http://doc.owncloud.org/server/6.0/admin_manual/configuration/auth_ldap.html#expert-settings>`_

If two administrator users are present, they are of ownCloud and LDAP. So you can remove that of ownCloud after have assigned the LDAP one to the administrator group. So, as a result, you can use only the LDAP administrator user. You can do this by the following steps:

#. login to ownCloud as administrator
#. open the user list: *admin -> Users*
#. change the group of "admin_xxx" user, checking "admin"
#. change the password of ownCloud admin user
#. logout and login with LDAP admin user
#. delete ownCloud admin user (named "admin")


Trusted Domains
===============

`Trusted domains <https://doc.owncloud.org/server/7.0/admin_manual/configuration/config_sample_php_parameters.html>`_ are a list of domains that users can log into. Default trusted domains are:

* domain name
* ip address

To add a new one use: ::

    config setprop owncloud TrustedDomains server.domain.com
    signal-event nethserver-owncloud-update

To add more than one, concatenate the names with a comma.
