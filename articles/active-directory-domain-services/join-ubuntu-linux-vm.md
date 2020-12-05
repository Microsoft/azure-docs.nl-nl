---
title: Een Ubuntu-VM toevoegen aan Azure AD Domain Services | Microsoft Docs
description: Meer informatie over het configureren en toevoegen van een Ubuntu Linux virtuele machine aan een Azure AD Domain Services beheerd domein.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.author: justinha
ms.custom: fasttrack-edit
ms.openlocfilehash: 8b6d022b9a1f3be70f69943a53754c9dd909ca99
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619484"
---
# <a name="join-an-ubuntu-linux-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Een Ubuntu Linux virtuele machine toevoegen aan een Azure Active Directory Domain Services beheerd domein

Als u wilt dat gebruikers zich met één set referenties aanmelden bij virtuele machines (Vm's) in azure, kunt u Vm's toevoegen aan een door Azure Active Directory Domain Services (Azure AD DS) beheerd domein. Wanneer u een virtuele machine koppelt aan een door Azure AD DS beheerd domein, kunnen gebruikers accounts en referenties van het domein worden gebruikt voor het aanmelden en beheren van servers. Groepslid maatschappen van het beheerde domein worden ook toegepast om u de toegang tot bestanden of services op de virtuele machine te kunnen beheren.

In dit artikel wordt beschreven hoe u een Ubuntu Linux VM kunt toevoegen aan een beheerd domein.

## <a name="prerequisites"></a>Vereisten

Om deze zelfstudie te voltooien, hebt u de volgende resources en machtigingen nodig:

* Een actief Azure-abonnement.
    * Als u nog geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory-tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises map of een cloudmap.
    * [Maak zo nodig een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* Een door Azure Active Directory Domain Services beheerd domein dat in uw Azure AD-tenant is ingeschakeld en geconfigureerd.
    * Bekijk zo nodig de eerste zelfstudie voor [het maken en configureren van een door Azure Active Directory Domain Services beheerd domein][create-azure-ad-ds-instance].
* Een gebruikersaccount dat deel uitmaakt van het beheerde domein.

## <a name="create-and-connect-to-an-ubuntu-linux-vm"></a>Een Ubuntu Linux-VM maken en er verbinding mee tot stand brengen

Als u een bestaande Ubuntu Linux virtuele machine in azure hebt, kunt u er verbinding mee maken via SSH. Ga vervolgens verder met de volgende stap om [de virtuele machine te configureren](#configure-the-hosts-file).

Als u een Ubuntu Linux virtuele machine moet maken of een test-VM wilt maken voor gebruik met dit artikel, kunt u een van de volgende methoden gebruiken:

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
* *Ubuntu* is de hostnaam van de Ubuntu-VM die u aan het beheerde domein toevoegt.

Werk deze namen bij met uw eigen waarden:

```console
127.0.0.1 ubuntu.aaddscontoso.com ubuntu
```

Als u klaar bent, slaat u het *hosts* -bestand op en sluit u het af met de `:wq` opdracht van de editor.

## <a name="install-required-packages"></a>De vereiste pakketten installeren

De VM moet enkele extra pakketten hebben om de virtuele machine toe te voegen aan het beheerde domein. Als u deze pakketten wilt installeren en configureren, moet u de hulpprogram ma's voor domein deelname bijwerken en installeren met `apt-get`

Tijdens de installatie van Kerberos vraagt het *krb5-gebruikers* pakket voor de realm-naam in alle hoofd letters. Als de naam van uw beheerde domein bijvoorbeeld *aaddscontoso.com* is, voert u *AADDSCONTOSO.com* in als de realm. De installatie schrijft de `[realm]` `[domain_realm]` secties en in het configuratie bestand */etc/krb5.conf* . Zorg ervoor dat u de realm een hoofd letter opgeeft:

```console
sudo apt-get update
sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
```

## <a name="configure-network-time-protocol-ntp"></a>Network Time Protocol (NTP) configureren

Voor een juiste werking van domein communicatie moeten de datum en tijd van uw Ubuntu-VM worden gesynchroniseerd met het beheerde domein. Voeg de NTP-hostnaam van uw beheerde domein toe aan het */etc/ntp.conf* -bestand.

1. Open het bestand *NTP. conf* met een editor:

    ```console
    sudo vi /etc/ntp.conf
    ```

1. Maak in het bestand *NTP. conf* een regel voor het toevoegen van de DNS-naam van uw beheerde domein. In het volgende voor beeld wordt een vermelding voor *aaddscontoso.com* toegevoegd. Uw eigen DNS-naam gebruiken:

    ```console
    server aaddscontoso.com
    ```

    Als u klaar bent, slaat u het bestand *NTP. conf* op en sluit u het af met de `:wq` opdracht van de editor.

1. Om ervoor te zorgen dat de virtuele machine wordt gesynchroniseerd met het beheerde domein, zijn de volgende stappen nodig:

    * De NTP-server stoppen
    * De datum en tijd van het beheerde domein bijwerken
    * De NTP-service starten

    Voer de volgende opdrachten uit om deze stappen uit te voeren. Gebruik uw eigen DNS-naam met de `ntpdate` opdracht:

    ```console
    sudo systemctl stop ntp
    sudo ntpdate aaddscontoso.com
    sudo systemctl start ntp
    ```

## <a name="join-vm-to-the-managed-domain"></a>Virtuele machine toevoegen aan het beheerde domein

Nu de vereiste pakketten zijn geïnstalleerd op de VM en NTP is geconfigureerd, voegt u de virtuele machine toe aan het beheerde domein.

1. Gebruik de `realm discover` opdracht om het beheerde domein te detecteren. In het volgende voor beeld wordt de realm- *AADDSCONTOSO.com* gedetecteerd. Geef in alle hoofd letters uw eigen beheerde domein naam op:

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   Als de `realm discover` opdracht uw beheerde domein niet kan vinden, raadpleegt u de volgende stappen voor probleem oplossing:

    * Zorg ervoor dat het domein bereikbaar is vanaf de VM. Probeer `ping aaddscontoso.com` te zien of er een positief antwoord wordt geretourneerd.
    * Controleer of de virtuele machine is geïmplementeerd op hetzelfde of een peered virtueel netwerk waarin het beheerde domein beschikbaar is.
    * Controleer of de DNS-server instellingen voor het virtuele netwerk zijn bijgewerkt zodat ze verwijzen naar de domein controllers van het beheerde domein.

1. Initialiseer nu Kerberos met behulp van de `kinit` opdracht. Geef een gebruiker op die deel uitmaakt van het beheerde domein. Voeg, indien nodig, [een gebruikers account toe aan een groep in azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Opnieuw moet de naam van het beheerde domein worden ingevoerd in hoofd letters. In het volgende voor beeld wordt het account `contosoadmin@aaddscontoso.com` met de naam gebruikt voor het initialiseren van Kerberos. Voer uw eigen gebruikers account in die deel uitmaakt van het beheerde domein:

    ```console
    kinit -V contosoadmin@AADDSCONTOSO.COM
    ```

1. Ten slotte voegt u de virtuele machine toe aan het beheerde domein met behulp van de `realm join` opdracht. Gebruik hetzelfde gebruikers account dat deel uitmaakt van het beheerde domein dat u in de vorige opdracht hebt opgegeven `kinit` , zoals `contosoadmin@AADDSCONTOSO.COM` :

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM' --install=/
    ```

Het duurt enkele minuten om de virtuele machine toe te voegen aan het beheerde domein. In de volgende voorbeeld uitvoer ziet u dat de virtuele machine is toegevoegd aan het beheerde domein:

```output
Successfully enrolled machine in realm
```

Als uw virtuele machine het proces voor het samen voegen van het domein niet kan volt ooien, moet u ervoor zorgen dat uitgaand Kerberos-verkeer op TCP + UDP-poort 464 naar het subnet van het virtuele netwerk voor uw beheerde domein is toegestaan door de netwerk beveiligings groep van de VM.

Als u de fout niet-opgegeven GSS-fout hebt ontvangen *.  De secundaire code bevat mogelijk meer informatie (server niet gevonden in Kerberos-data base)*. Open het bestand */etc/krb5.conf* en voeg de volgende code toe aan de `[libdefaults]` sectie en probeer het opnieuw:

```console
rdns=false
```

## <a name="update-the-sssd-configuration"></a>De SSSD-configuratie bijwerken

Een van de pakketten die in een vorige stap zijn geïnstalleerd, was voor System Security Services daemon (SSSD). Wanneer een gebruiker zich probeert aan te melden bij een virtuele machine met behulp van domein referenties, stuurt SSSD de aanvraag door naar een verificatie provider. In dit scenario maakt SSSD gebruik van Azure AD DS om de aanvraag te verifiëren.

1. Open het bestand *SSSD. conf* met een editor:

    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

1. Voeg als volgt commentaar uit op de regel voor *use_fully_qualified_names* :

    ```console
    # use_fully_qualified_names = True
    ```

    Als u klaar bent, slaat u het bestand *SSSD. conf* op en sluit u het af met de `:wq` opdracht van de editor.

1. Start de SSSD-service opnieuw om de wijziging toe te passen:

    ```console
    sudo systemctl restart sssd
    ```

## <a name="configure-user-account-and-group-settings"></a>Gebruikers accounts en groeps instellingen configureren

Wanneer de virtuele machine is gekoppeld aan het beheerde domein en geconfigureerd voor verificatie, zijn er een aantal opties voor configuratie van de gebruiker. Deze configuratie wijzigingen zijn onder andere het toestaan van verificatie op basis van wacht woorden en het automatisch maken van basis mappen op de lokale virtuele machine wanneer domein gebruikers zich voor het eerst aanmelden.

### <a name="allow-password-authentication-for-ssh"></a>Wachtwoord verificatie voor SSH toestaan

Standaard kunnen gebruikers zich alleen aanmelden bij een VM met behulp van open bare SSH-sleutel verificatie. Verificatie op basis van wacht woorden mislukt. Wanneer u de virtuele machine lid maakt van een beheerd domein, moeten die domein accounts verificatie op basis van wacht woorden gebruiken. Werk de SSH-configuratie als volgt bij om verificatie op basis van wacht woorden toe te staan.

1. Open het *sshd_conf* -bestand met een editor:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. Werk de regel voor *PasswordAuthentication* bij naar *Ja*:

    ```console
    PasswordAuthentication yes
    ```

    Als u klaar bent, slaat u het *sshd_conf* bestand op en sluit u het af met de `:wq` opdracht van de editor.

1. Als u de wijzigingen wilt Toep assen en gebruikers wilt laten aanmelden met een wacht woord, start u de SSH-service opnieuw:

    ```console
    sudo systemctl restart ssh
    ```

### <a name="configure-automatic-home-directory-creation"></a>Automatisch maken van basis mappen configureren

Als u het automatisch maken van de basismap wilt inschakelen wanneer een gebruiker zich voor het eerst aanmeldt, voert u de volgende stappen uit:

1. Open het */etc/pam.d/common-Session* -bestand in een editor:

    ```console
    sudo vi /etc/pam.d/common-session
    ```

1. Voeg de volgende regel toe aan dit bestand onder de regel `session optional pam_sss.so` :

    ```console
    session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
    ```

    Als u klaar bent, slaat u het bestand *common-Session* op en sluit u het af met de `:wq` opdracht van de editor.

### <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>De groep ' AAD DC Administrators ' sudo privileges verlenen

Als u leden van de groep *Aad DC-Administrators* wilt toewijzen aan de Ubuntu-VM, voegt u een vermelding toe aan de */etc/sudoers*. Eenmaal toegevoegd, kunnen leden van de groep *Aad DC-Administrators* de `sudo` opdracht gebruiken op de Ubuntu-VM.

1. Open het *sudo* -bestand om het te bewerken:

    ```console
    sudo visudo
    ```

1. Voeg de volgende vermelding toe aan het einde van het */etc/sudoers* -bestand:

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

    Als u klaar bent, slaat u de editor op en sluit u deze met behulp van de `Ctrl-X` opdracht.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Aanmelden bij de virtuele machine met behulp van een domein account

Als u wilt controleren of de virtuele machine is toegevoegd aan het beheerde domein, start u een nieuwe SSH-verbinding met behulp van een domein gebruikers account. Bevestig dat er een basismap is gemaakt en dat groepslid maatschap van het domein wordt toegepast.

1. Maak een nieuwe SSH-verbinding vanuit uw-console. Gebruik een domein account dat deel uitmaakt van het beheerde domein met behulp van de `ssh -l` opdracht, zoals `contosoadmin@aaddscontoso.com` en voer vervolgens het adres van uw virtuele machine in, bijvoorbeeld *Ubuntu.aaddscontoso.com*. Als u de Azure Cloud Shell gebruikt, gebruikt u het open bare IP-adres van de virtuele machine in plaats van de interne DNS-naam.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com ubuntu.aaddscontoso.com
    ```

1. Wanneer u verbinding hebt gemaakt met de virtuele machine, controleert u of de basismap correct is geïnitialiseerd:

    ```console
    pwd
    ```

    U moet zich in de */Home* -map met uw eigen map bekomen die overeenkomt met het gebruikers account.

1. Controleer nu of de groepslid maatschappen correct worden omgezet:

    ```console
    id
    ```

    U moet de groepslid maatschappen van het beheerde domein zien.

1. Als u zich bij de virtuele machine hebt aangemeld als lid van de groep *Aad DC-Administrators* , controleert u of u de opdracht kunt gebruiken `sudo` :

    ```console
    sudo apt-get update
    ```

## <a name="next-steps"></a>Volgende stappen

Als u problemen ondervindt met het verbinden van de virtuele machine met het beheerde domein of als u zich aanmeldt met een domein account, raadpleegt u problemen [met domein deelname oplossen](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
