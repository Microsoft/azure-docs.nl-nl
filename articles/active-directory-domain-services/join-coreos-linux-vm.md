---
title: Een CoreOS-VM toevoegen aan Azure AD Domain Services | Microsoft Docs
description: Meer informatie over het configureren en toevoegen van een CoreOS-virtuele machine aan een Azure AD Domain Services beheerd domein.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.author: justinha
ms.openlocfilehash: 1e98f32bd6fe7d5373d5ab6621ffdce5e79abc08
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619586"
---
# <a name="join-a-coreos-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Een virtuele CoreOS-machine toevoegen aan een Azure Active Directory Domain Services beheerd domein

Als u wilt dat gebruikers zich met één set referenties aanmelden bij virtuele machines (Vm's) in azure, kunt u Vm's toevoegen aan een door Azure Active Directory Domain Services (Azure AD DS) beheerd domein. Wanneer u een virtuele machine koppelt aan een door Azure AD DS beheerd domein, kunnen gebruikers accounts en referenties van het domein worden gebruikt voor het aanmelden en beheren van servers. Groepslid maatschappen van het beheerde domein worden ook toegepast om u de toegang tot bestanden of services op de virtuele machine te kunnen beheren.

In dit artikel wordt beschreven hoe u een CoreOS-VM kunt toevoegen aan een beheerd domein.

## <a name="prerequisites"></a>Vereisten

Om deze zelfstudie te voltooien, hebt u de volgende resources en machtigingen nodig:

* Een actief Azure-abonnement.
    * Als u nog geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory-tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises map of een cloudmap.
    * [Maak zo nodig een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* Een door Azure Active Directory Domain Services beheerd domein dat in uw Azure AD-tenant is ingeschakeld en geconfigureerd.
    * Bekijk zo nodig de eerste zelfstudie voor [het maken en configureren van een door Azure Active Directory Domain Services beheerd domein][create-azure-ad-ds-instance].
* Een gebruikersaccount dat deel uitmaakt van het beheerde domein.

## <a name="create-and-connect-to-a-coreos-linux-vm"></a>Maken en verbinding maken met een CoreOS Linux-VM

Als u een bestaande virtuele machine met CoreOS Linux in azure hebt, kunt u er verbinding mee maken via SSH. Ga vervolgens verder met de volgende stap om te beginnen met het [configureren van de virtuele machine](#configure-the-hosts-file).

Als u een virtuele machine met CoreOS Linux wilt maken of een test-VM wilt maken voor gebruik met dit artikel, kunt u een van de volgende methoden gebruiken:

* [Azure-portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Wanneer u de virtuele machine maakt, moet u aandacht best Eden aan de instellingen voor virtueel netwerk om ervoor te zorgen dat de virtuele machine kan communiceren met het beheerde domein:

* Implementeer de virtuele machine in dezelfde of een peered virtueel netwerk waarin u Azure AD Domain Services hebt ingeschakeld.
* Implementeer de virtuele machine in een ander subnet dan uw Azure AD Domain Services beheerde domein.

Wanneer de VM is geïmplementeerd, volgt u de stappen om via SSH verbinding te maken met de VM.

## <a name="configure-the-hosts-file"></a>Het hosts-bestand configureren

Om ervoor te zorgen dat de hostnaam van de virtuele machine correct is geconfigureerd voor het beheerde domein, bewerkt u het bestand *bestand/etc/hosts* en stelt u de hostnaam in:

```console
sudo vi /etc/hosts
```

Werk in het bestand *hosts* het *localhost* -adres bij. In het volgende voorbeeld:

* *aaddscontoso.com* is de DNS-domein naam van uw beheerde domein.
* *coreos* is de hostnaam van de COREOS-VM die u aan het beheerde domein toevoegt.

Werk deze namen bij met uw eigen waarden:

```console
127.0.0.1 coreos coreos.aaddscontoso.com
```

Als u klaar bent, slaat u het *hosts* -bestand op en sluit u het af met de `:wq` opdracht van de editor.

## <a name="configure-the-sssd-service"></a>De SSSD-service configureren

Werk de configuratie van de */etc/SSSD/SSSD.conf* SSSD bij.

```console
sudo vi /etc/sssd/sssd.conf
```

Geef uw eigen beheerde domein naam op voor de volgende para meters:

* *domeinen* in alle hoofd letters
* *[domain/AADDSCONTOSO]* waarbij AADDSCONTOSO zich in alle hoofd letters bevindt
* *ldap_uri*
* *ldap_search_base*
* *krb5_server*
* *krb5_realm* in alle hoofd letters

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = AADDSCONTOSO.COM

[domain/AADDSCONTOSO]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://aaddscontoso.com
ldap_search_base = dc=aaddscontoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = aaddscontoso.com
krb5_realm = AADDSCONTOSO.COM
```

## <a name="join-the-vm-to-the-managed-domain"></a>De VM aan het beheerde domein toevoegen

Als het SSSD-configuratie bestand is bijgewerkt, voegt u de virtuele machine nu toe aan het beheerde domein.

1. Gebruik eerst de `adcli info` opdracht om te controleren of u informatie kunt bekijken over het beheerde domein. In het volgende voor beeld wordt informatie opgehaald voor het domein *AADDSCONTOSO.com*. Geef in alle hoofd letters uw eigen beheerde domein naam op:

    ```console
    sudo adcli info AADDSCONTOSO.COM
    ```

   Als de `adcli info` opdracht uw beheerde domein niet kan vinden, raadpleegt u de volgende stappen voor probleem oplossing:

    * Zorg ervoor dat het domein bereikbaar is vanaf de VM. Probeer `ping aaddscontoso.com` te zien of er een positief antwoord wordt geretourneerd.
    * Controleer of de virtuele machine is geïmplementeerd op hetzelfde of een peered virtueel netwerk waarin het beheerde domein beschikbaar is.
    * Controleer of de DNS-server instellingen voor het virtuele netwerk zijn bijgewerkt zodat ze verwijzen naar de domein controllers van het beheerde domein.

1. Voeg nu de virtuele machine toe aan het beheerde domein met behulp van de `adcli join` opdracht. Geef een gebruiker op die deel uitmaakt van het beheerde domein. Voeg, indien nodig, [een gebruikers account toe aan een groep in azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Opnieuw moet de naam van het beheerde domein worden ingevoerd in hoofd letters. In het volgende voor beeld wordt het account `contosoadmin@aaddscontoso.com` met de naam gebruikt voor het initialiseren van Kerberos. Voer uw eigen gebruikers account in die deel uitmaakt van het beheerde domein.

    ```console
    sudo adcli join -D AADDSCONTOSO.COM -U contosoadmin@AADDSCONTOSO.COM -K /etc/krb5.keytab -H coreos.aaddscontoso.com -N coreos
    ```

    De `adcli join` opdracht retourneert geen informatie wanneer de virtuele machine is toegevoegd aan het beheerde domein.

1. Start de SSSD-service om de configuratie van het domein aan te passen:
  
    ```console
    sudo systemctl start sssd.service
    ```

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Aanmelden bij de virtuele machine met behulp van een domein account

Als u wilt controleren of de virtuele machine is toegevoegd aan het beheerde domein, start u een nieuwe SSH-verbinding met behulp van een domein gebruikers account. Bevestig dat er een basismap is gemaakt en dat groepslid maatschap van het domein wordt toegepast.

1. Maak een nieuwe SSH-verbinding vanuit uw-console. Gebruik een domein account dat deel uitmaakt van het beheerde domein met behulp van de `ssh -l` opdracht, zoals `contosoadmin@aaddscontoso.com` en voer vervolgens het adres van uw virtuele machine in, bijvoorbeeld *coreos.aaddscontoso.com*. Als u de Azure Cloud Shell gebruikt, gebruikt u het open bare IP-adres van de virtuele machine in plaats van de interne DNS-naam.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com coreos.aaddscontoso.com
    ```

1. Controleer nu of de groepslid maatschappen correct worden omgezet:

    ```console
    id
    ```

    U moet de groepslid maatschappen van het beheerde domein zien.

## <a name="next-steps"></a>Volgende stappen

Als u problemen ondervindt met het verbinden van de virtuele machine met het beheerde domein of als u zich aanmeldt met een domein account, raadpleegt u problemen [met domein deelname oplossen](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
