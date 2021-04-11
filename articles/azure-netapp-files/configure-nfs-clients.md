---
title: Een NFS-client configureren voor Azure NetApp Files | Microsoft Docs
description: Hierin wordt beschreven hoe u NFS-clients configureert voor gebruik met Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/09/2020
ms.author: b-juche
ms.openlocfilehash: e0b86a7014af42f2ffb067c2de797f270a5b1855
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967469"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>Een NFS-client voor Azure NetApp Files configureren

De configuratie van de NFS-client die in dit artikel wordt beschreven, maakt deel uit van de installatie wanneer u [nfsv 4.1 Kerberos Encryption configureert](configure-kerberos-encryption.md) of [een Dual-protocol volume maakt](create-volumes-dual-protocol.md). Er zijn een groot aantal Linux-distributies beschikbaar voor gebruik met Azure NetApp Files. In dit artikel worden configuraties beschreven voor twee van de vaker gebruikte omgevingen: RHEL 8 en Ubuntu 18,04. 

## <a name="requirements-and-considerations"></a>Vereisten en overwegingen  

Ongeacht de Linux-smaak die u gebruikt, zijn de volgende configuraties vereist:

* Configureer een NTP-client om problemen met tijd verschil te voor komen.
* Configureer DNS-vermeldingen van de Linux-client voor naam omzetting.  
    Deze configuratie moet het volgende bevatten: een (voorwaartse) record en een PTR-record (reverse). 
* Maak voor domein deelname een computer account voor de Linux-client in de doel Active Directory (die wordt gemaakt tijdens de realm-koppelings opdracht). 
    > [!NOTE] 
    > De `$SERVICEACCOUNT` variabele die in de onderstaande opdrachten wordt gebruikt, moet een gebruikers account met machtigingen of delegering zijn om een computer account te maken in de doel organisatie-eenheid.

## <a name="rhel-8-configuration"></a>RHEL 8-configuratie 

In deze sectie worden RHEL-configuraties beschreven die vereist zijn voor NFSv 4.1 Kerberos-versleuteling en het dubbele protocol.  

In de voor beelden in deze sectie worden de volgende domein naam en IP-adres gebruikt:  

* Domein naam: `contoso.com`
* Privé-IP: `10.6.1.4`

### <a name="rhel-8-configuration-if-you-are-using-nfsv41-kerberos-encryption"></a><a name="rhel8_nfsv41_kerberos"></a>RHEL 8-configuratie als u NFSv 4.1 Kerberos-versleuteling gebruikt

1. Configureer `/etc/resolv.conf` met de juiste DNS-server.  

    Bijvoorbeeld:  

    `[root@reddoc cbs]# cat /etc/resolv.conf`   
    `search contoso.com`   
    `nameserver 10.6.1.4(private IP)`   

2. Voeg de NFS-client record toe aan de DNS-server voor de DNS-zone voor forward en reverse lookup.

3. Gebruik de volgende opdrachten van de NFS-client om DNS te controleren:   

    `# nslookup [hostname/FQDN of NFS client(s)]`   
    `# nslookup [IP address of NFS client(s)]`

4. Pakketten installeren:   

    `yum update`   
    `sudo yum -y install realmd sssd adcli samba-common krb5-workstation chrony nfs-utils`

5.  Configureer de NTP-client.  

    RHEL 8 maakt standaard gebruik van chrony. Volg de richt lijnen in de configuratie van [het gebruik van de `Chrony` Suite voor het configureren van NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/using-chrony-to-configure-ntp).

6.  Word lid van het domein Active Directory:  

    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou="OU=$YOUROU"`

    Bijvoorbeeld: 

    `sudo realm join CONTOSO.COM -U ad_admin --computer-ou="CN=Computers"`
    
    Zorg ervoor dat `default_realm` is ingesteld op de opgegeven realm in `/etc/krb5.conf` .  Als dat niet het geval is, voegt u deze toe onder de `[libdefaults]` sectie in het bestand, zoals wordt weer gegeven in het volgende voor beeld:
    
    `default_realm = CONTOSO.COM`

7. Alle NFS-services opnieuw starten:  
 
    `systemctl start nfs-*`   
    `systemctl restart rpc-gssd.service`

    Als de computer opnieuw wordt opgestart, wordt de fout voor komen `“mount.nfs: an incorrect mount option was specified”` tijdens een Kerberos-koppeling.

8. Voer de `kinit` opdracht uit met het gebruikers account om tickets te verkrijgen: 
 
    `sudo kinit $SERVICEACCOUNT@DOMAIN`  

    Bijvoorbeeld:   

    `sudo kinit ad_admin@CONTOSO.COM`


### <a name="rhel-8-configuration-if-you-are-using-dual-protocol"></a>RHEL 8-configuratie als u gebruikmaakt van Dual Protocol

De volgende stappen zijn optioneel. U moet de stappen alleen uitvoeren als u gebruikers toewijzing gebruikt op de NFS-client: 

1. Voer alle stappen uit die worden beschreven in de [RHEL 8-configuratie als u de sectie nfsv 4.1 Kerberos-versleuteling gebruikt](#rhel8_nfsv41_kerberos) .   

2. Voeg een statische DNS-record in uw bestand/etc/hosts-bestand toe om Fully Qualified Domain Name (FQDN) voor uw AD te gebruiken, in plaats van het IP-adres in SSSD-configuratie bestand te gebruiken:  

    `cat /etc/hosts`   
    `10.6.1.4 winad2016.contoso.com`

3. Een extra sectie toevoegen voor domeinen om id's van AD LDAP-server op te lossen:    

    `[root@reddoc cbs]# cat /etc/sssd/sssd.conf`   
    `[sssd]`   
    `domains = contoso.com, contoso-ldap (new entry added for LDAP as id_provider)`   
    `config_file_version = 2`   
    `services = nss, pam, ssh, sudo (ensure nss is present in this list)`   
 
    `[domain/contoso-ldap] (Copy the following lines. Modify as per your domain name.)`   
    `auth_provider = krb5`   
    `chpass_provider = krb5`   
    `id_provider = ldap`   
    `ldap_search_base = dc=contoso,dc=com(your domain)`   
    `ldap_schema = rfc2307bis`   
    `ldap_sasl_mech = GSSAPI`   
    `ldap_user_object_class = user`   
    `ldap_group_object_class = group`   
    `ldap_user_home_directory = unixHomeDirectory`   
    `ldap_user_principal = userPrincipalName`   
    `ldap_account_expire_policy = ad`   
    `ldap_force_upper_case_realm = true`   
    `ldap_user_search_base = cn=Users,dc=contoso,dc=com (based on your domain)`   
    `ldap_group_search_base = cn=Users,dc=contoso,dc=com (based on your domain)`   
    `ldap_sasl_authid = REDDOC$ (ensure $ at the end you can get this from “klist -kte” command)`   
    `krb5_server = winad2016.contoso.com (same as AD address which is added in /etc/hosts)`   
    `krb5_realm = CONTOSO.COM (domain name in caps)`   
    `krb5_kpasswd = winad2016.contoso.com (same as AD address which is added in /etc/hosts)`   
    `use_fully_qualified_names = false`   
 
    `[domain/contoso.com]  (Do not edit or remove any of the following information. This information is automatically generated during the realm join process.)`   
    `ad_domain = contoso.com`   
    `krb5_realm = CONTOSO.COM`   
    `realmd_tags = manages-system joined-with-adcli`   
    `cache_credentials = True`   
    `id_provider = ad`   
    `krb5_store_password_if_offline = True`   
    `default_shell = /bin/bash`   
    `ldap_id_mapping = True`   
    `use_fully_qualified_names = True`   
    `fallback_homedir = /home/%u@%d`   
    `access_provider = ad`   

4. Zorg ervoor dat u `/etc/nsswitch.conf` de `sss` vermelding hebt:   

    `cat /etc/nsswitch.conf`   
    `passwd: sss files systemd`   
    `group: sss files systemd`   
    `netgroup: sss files`   

5. Start de `sssd` service opnieuw en wis de cache:   

    `service sssd stop`   
    `rm -f /var/lib/sss/db/*`   
    `service sssd start`   
 
6. Test om te controleren of uw client is geïntegreerd met de LDAP-server:   

    `[root@red81 cbs]# id ldapuser1`   
    `uid=1234(ldapuser1) gid=1111(ldapgroup1) groups=1111(ldapgroup1)`   

## <a name="ubuntu-configuration"></a>Ubuntu-configuratie   

In deze sectie worden Ubuntu-configuraties beschreven die vereist zijn voor NFSv 4.1 Kerberos-versleuteling en het dubbele protocol. 

In de voor beelden in deze sectie worden de volgende domein naam en IP-adres gebruikt:  

* Domein naam: `contoso.com`
* Privé-IP: `10.6.1.4`

1. Configureren `/etc/resolv.conf` met de juiste DNS-server:

    `root@ubuntu-rak:/home/cbs# cat /etc/resolv.conf`   
    `search contoso.com`   
    `nameserver <private IP address of DNS server>`   

2. Voeg NFS-client record toe aan de DNS-server voor de DNS-zone voor forward en reverse lookup.
 
    Gebruik de volgende opdrachten van de NFS-client om DNS te controleren:

    `# nslookup [hostname/FQDN of NFS client(s)]`   
    `# nslookup [IP address of NFS client(s)]`   

3. Pakketten installeren:
 
    `apt-get update`   
    `apt-get install -y realmd packagekit sssd adcli samba-common chrony krb5-user nfs-common`
    
    Als u hierom wordt gevraagd, wordt invoer `$DOMAIN.NAME` (met hoofd letters `CONTOSO.COM` ) als de standaard Kerberos-realm.

4. Start de service opnieuw `rpc-gssd.service` : 

    `sudo systemctl start rpc-gssd.service`

5. Ubuntu 18,04 maakt standaard gebruik van chrony. Volg de richt lijnen voor configuratie in [Ubuntu Bionic: using chrony om NTP te configureren](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp).

6. Word lid van het domein Active Directory:   
 
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou="OU=$YOUROU"`
 
    Bijvoorbeeld:    
    `sudo realm join CONTOSO.COM -U ad_admin --computer-ou="CN=Computers"`

7. Voer uit `kinit` met de gebruiker om tickets te verkrijgen: 
 
    `sudo kinit $SERVICEACCOUNT`   
 
    Bijvoorbeeld:    
    `sudo kinit ad_admin`  

### <a name="ubuntu-configuration-if-you-are-using-dual-protocol"></a>Ubuntu-configuratie als u gebruikmaakt van Dual Protocol  

De volgende stappen zijn optioneel.  U moet de stappen alleen uitvoeren als u de gebruikers toewijzing wilt gebruiken op de NFS-client:  

1. Voer de volgende opdracht uit om de geïnstalleerde pakketten bij te werken:   
    `sudo apt update && sudo apt install libnss-ldap libpam-ldap ldap-utils nscd`

    In het volgende voor beeld wordt gebruikgemaakt van voorbeeld waarden. Wanneer u met de opdracht wordt gevraagd om in te voeren, moet u de invoer opgeven op basis van uw omgeving. 

    `base dc=contoso,dc=com uri ldap://10.20.0.4:389/ ldap_version 3 rootbinddn cn=admin,cn=Users,dc=contoso,dc=com pam_password ad`   

2. Voer de volgende opdracht uit om de service opnieuw te starten en in te scha kelen:

    `sudo systemctl restart nscd && sudo systemctl enable nscd`   

In het volgende voor beeld wordt een query uitgevoerd voor de AD LDAP-server van de Ubuntu LDAP-client voor een LDAP-gebruiker `‘hari1’` : 

`root@cbs-k8s-varun4-04:/home/cbs# getent passwd hari1`   
`hari1:*:1237:1237:hari1:/home/hari1:/bin/bash`   


## <a name="next-steps"></a>Volgende stappen  

* [Een NFS-volume maken voor Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Een volume met dubbele protocollen maken voor Azure NetApp Files](create-volumes-dual-protocol.md)

