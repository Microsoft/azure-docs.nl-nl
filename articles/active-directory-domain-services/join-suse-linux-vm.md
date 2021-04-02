---
title: Een SLE-VM toevoegen aan Azure AD Domain Services | Microsoft Docs
description: Meer informatie over het configureren en koppelen van een SUSE Linux Enter prise-virtuele machine aan een Azure AD Domain Services beheerd domein.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 08/12/2020
ms.author: justinha
ms.openlocfilehash: f2f421d95dfc376aed373c718198db33a870d9dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619603"
---
# <a name="join-a-suse-linux-enterprise-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Een SUSE Linux Enter prise-virtuele machine toevoegen aan een Azure Active Directory Domain Services beheerd domein

Als u wilt dat gebruikers zich met één set referenties aanmelden bij virtuele machines (Vm's) in azure, kunt u Vm's toevoegen aan een door Azure Active Directory Domain Services (Azure AD DS) beheerd domein. Wanneer u een virtuele machine koppelt aan een door Azure AD DS beheerd domein, kunnen gebruikers accounts en referenties van het domein worden gebruikt voor het aanmelden en beheren van servers. Groepslid maatschappen van het beheerde domein worden ook toegepast om u de toegang tot bestanden of services op de virtuele machine te kunnen beheren.

In dit artikel wordt beschreven hoe u een SUSE Linux Enter prise (SLE)-VM kunt koppelen aan een beheerd domein.

## <a name="prerequisites"></a>Vereisten

Om deze zelfstudie te voltooien, hebt u de volgende resources en machtigingen nodig:

* Een actief Azure-abonnement.
    * Als u nog geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory-tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises map of een cloudmap.
    * [Maak zo nodig een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* Een door Azure Active Directory Domain Services beheerd domein dat in uw Azure AD-tenant is ingeschakeld en geconfigureerd.
    * Bekijk zo nodig de eerste zelfstudie voor [het maken en configureren van een door Azure Active Directory Domain Services beheerd domein][create-azure-ad-ds-instance].
* Een gebruikersaccount dat deel uitmaakt van het beheerde domein.

## <a name="create-and-connect-to-a-sle-linux-vm"></a>Maken en verbinding maken met een SLE Linux-VM

Als u een bestaande virtuele machine met SLE Linux in azure hebt, kunt u er verbinding mee maken via SSH. Ga vervolgens verder met de volgende stap om te beginnen met het [configureren van de virtuele machine](#configure-the-hosts-file).

Als u een virtuele machine met SLE Linux wilt maken of een test-VM wilt maken voor gebruik met dit artikel, kunt u een van de volgende methoden gebruiken:

* [Azure-portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure-CLI](../virtual-machines/linux/quick-create-cli.md)
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
* *Linux-q2gr* is de hostnaam van uw SLE-VM die u aan het beheerde domein toevoegt.

Werk deze namen bij met uw eigen waarden:

```console
127.0.0.1 linux-q2gr linux-q2gr.aaddscontoso.com
```

Als u klaar bent, slaat u het *hosts* -bestand op en sluit u het af met de `:wq` opdracht van de editor.

## <a name="join-vm-to-the-managed-domain-using-sssd"></a>Virtuele machine toevoegen aan het beheerde domein met behulp van SSSD

Voer de volgende stappen uit om lid te worden van het beheerde domein met behulp van **SSSD** en de beheer module voor *gebruikers aanmelding* van YaST:

1. Installeer de YaST-module voor *aanmeldings beheer voor gebruikers* :

    ```bash
    sudo zypper install yast2-auth-client
    ```

1. Open YaST.

1. Als u de automatische detectie van DNS wilt gebruiken, moet u de IP-adressen (de *Active Directory server*) van het beheerde domein configureren als de naam server voor uw client.

    Selecteer in YaST **systeem > netwerk instellingen**.

1. Selecteer het tabblad *hostnaam/DNS* en geef vervolgens de IP-adres (sen) van het beheerde domein op in het tekstvak *naam server 1*. Deze IP-adressen worden weer gegeven in het venster *Eigenschappen* in de Azure portal voor uw beheerde domein, zoals *10.0.2.4* en *10.0.2.5*.

    Voeg uw eigen beheerde domein-IP-adressen toe en selecteer **OK**.

1. Kies in het hoofd venster van YaST *Network Services*  >  *aanmeldings beheer voor gebruikers*.

    De module wordt geopend met een overzicht van de verschillende netwerk eigenschappen van uw computer en de verificatie methode die momenteel in gebruik is, zoals wordt weer gegeven in de volgende voorbeeld scherm afbeelding:

    ![Voor beeld van een scherm opname van het venster gebruikers aanmelding beheer in YaST](./media/join-suse-linux-vm/overview-window.png)

    Als u wilt bewerken, selecteert u **instellingen wijzigen**.

Voer de volgende stappen uit om de virtuele machine toe te voegen aan het beheerde domein:

1. Selecteer in het dialoog venster **domein toevoegen**.

1. Geef de juiste *domein naam* op, bijvoorbeeld *aaddscontoso.com*, en geef vervolgens de services op die moeten worden gebruikt voor identiteits gegevens en-authenticatie. Selecteer *micro soft-Active Directory* voor beide.

    Zorg ervoor dat de optie voor *het inschakelen van het domein* is geselecteerd.

1. Selecteer **OK** wanneer u klaar bent.

1. Accepteer de standaard instellingen in het volgende dialoog venster en selecteer vervolgens **OK**.

1. De VM installeert extra software waar nodig, waarna wordt gecontroleerd of het beheerde domein beschikbaar is.

    Als alles correct is, wordt het volgende voor beeld weer gegeven om aan te geven dat de virtuele machine het beheerde domein heeft gedetecteerd, maar dat u *nog niet bent Inge schreven*.

    ![Voor beeld van een scherm opname van het Active Directory-inschrijvings venster in YaST](./media/join-suse-linux-vm/enroll-window.png)

1. Geef in het dialoog venster de *gebruikers naam* en het *wacht woord* op van een gebruiker die deel uitmaakt van het beheerde domein. Voeg, indien nodig, [een gebruikers account toe aan een groep in azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Om ervoor te zorgen dat het huidige domein is ingeschakeld voor samba, activeert *u Samba-configuratie overschrijven zodat deze AD kan worden gebruikt*.

1. Selecteer **OK** om te registreren.

1. Er wordt een bericht weer gegeven om te bevestigen dat u bent Inge schreven. Selecteer **OK** om te volt ooien.

Nadat de virtuele machine is inge schreven in het beheerde domein, configureert u de client met behulp van *domein aanmelding beheren*, zoals wordt weer gegeven in de volgende voorbeeld scherm afbeelding:

![Voor beeld van een scherm opname van het venster gebruikers aanmelding domein beheren in YaST](./media/join-suse-linux-vm/manage-domain-user-logon-window.png)

1. Als u aanmeldingen wilt toestaan met behulp van gegevens die zijn verschaft door het beheerde domein, schakelt u het selectie vakje *Aanmelden bij domein gebruikers toestaan* in.

1. Schakel eventueel onder *domein gegevens bron inschakelen* de extra gegevens bronnen in die nodig zijn voor uw omgeving. Deze opties omvatten welke gebruikers **sudo** mogen gebruiken of welke netwerk stations beschikbaar zijn.

1. Als u wilt toestaan dat gebruikers in het beheerde domein hun basis mappen op de VM kunnen hebben, schakelt u het selectie vakje *basis mappen maken* in.

1. Selecteer op de balk aan de zijkant **Service opties › naam Switch** en vervolgens *uitgebreide opties*. Selecteer *fallback_homedir* of *override_homedir* in het venster en selecteer vervolgens **toevoegen**.

1. Geef een waarde op voor de locatie van de basismap. Als u wilt dat basis mappen de indeling */home/user_name*, gebruikt u */Home/%u*. Zie voor meer informatie over mogelijke variabelen de pagina SSSD. conf man ( `man 5 sssd.conf` ), sectie *override_homedir*.

1. Selecteer **OK**.

1. Selecteer **OK** om de wijzigingen op te slaan. Controleer vervolgens of de waarden die nu worden weer gegeven juist zijn. Selecteer **Annuleren** om het dialoog venster te sluiten.

1. Als u van plan bent om SSSD en winbind tegelijk uit te voeren (bijvoorbeeld wanneer u verbinding maakt via SSSD, maar vervolgens een samba-Bestands server uitvoert), moet u de samba-optie *Kerberos-methode* instellen op *geheimen en keytab* in SMB. conf. De SSSD-optie *ad_update_samba_machine_account_password* moet ook worden ingesteld op *True* in SSSD. conf. Met deze opties wordt voor komen dat de systeem keytab synchroon gaat.

## <a name="join-vm-to-the-managed-domain-using-winbind"></a>Virtuele machine toevoegen aan het beheerde domein met behulp van winbind

Voer de volgende stappen uit om lid te worden van het beheerde domein met behulp van **winbind** en de module *Windows-domein lidmaatschap* van YaST:

1. Selecteer in YaST **Network Services > Windows-domein lidmaatschap**.

1. Voer het domein in om lid te worden van *domein of werk groep* in het scherm *lidmaatschap van Windows-domein* . Voer de naam van het beheerde domein in, zoals *aaddscontoso.com*.

    ![Voor beeld van een scherm opname van het Windows-domein lidmaatschaps venster in YaST](./media/join-suse-linux-vm/samba-client-window.png)

1. Als u de SMB-bron voor Linux-verificatie wilt gebruiken, controleert u de optie voor het *gebruik van SMB-informatie voor Linux-verificatie*.

1. Als u automatisch een lokale basis directory wilt maken voor beheerde domein gebruikers op de virtuele machine, schakelt u de optie voor het maken van de *basismap op aanmelden*.

1. Controleer de optie voor *offline verificatie* zodat uw domein gebruikers zich kunnen aanmelden, zelfs als het beheerde domein tijdelijk niet beschikbaar is.

1. Als u de UID-en GID-bereiken voor de Samba-gebruikers en-groepen wilt wijzigen, selecteert u *Geavanceerde instellingen*.

1. Configureer tijd synchronisatie van Network Time Protocol (NTP) voor uw beheerde domein door de *configuratie van NTP* te selecteren. Voer de IP-adressen in van het beheerde domein. Deze IP-adressen worden weer gegeven in het venster *Eigenschappen* in de Azure portal voor uw beheerde domein, zoals *10.0.2.4* en *10.0.2.5*.

1. Selecteer **OK** en bevestig de domein deelname wanneer u hierom wordt gevraagd.

1. Geef het wacht woord voor een beheerder op in het beheerde domein en selecteer **OK**.

    ![Voor beeld van een scherm opname van het verificatie dialoogvenster wanneer u een SLE-VM toevoegt aan het beheerde domein](./media/join-suse-linux-vm/domain-join-authentication-prompt.png)

Nadat u lid bent geworden van het beheerde domein, kunt u zich vanaf uw werk station aanmelden met behulp van de weer gave Manager van uw bureau blad of de-console.

## <a name="join-vm-to-the-managed-domain-using-winbind-from-the-yast-command-line-interface"></a>Virtuele machine toevoegen aan het beheerde domein met behulp van winbind van de YaST-opdracht regel interface

Om lid te worden van het beheerde domein met behulp van **winbind** en de *YaST-opdracht regel interface*:

* Lid worden van het domein:

  ```console
  sudo yast samba-client joindomain domain=aaddscontoso.com user=<admin> password=<admin password> machine=<(optional) machine account>
  ```

## <a name="join-vm-to-the-managed-domain-using-winbind-from-the-terminal"></a>Virtuele machine toevoegen aan het beheerde domein met behulp van winbind van de Terminal

Om lid te worden van het beheerde domein met behulp van **winbind** en de *`samba net` opdracht*:

1. Installeer de Kerberos-client en Samba-winbind:

   ```console
   sudo zypper in krb5-client samba-winbind
   ```

2. Bewerk de configuratie bestanden:

   * /etc/samba/smb.conf
   
     ```ini
     [global]
         workgroup = AADDSCONTOSO
         usershare allow guests = NO #disallow guests from sharing
         idmap config * : backend = tdb
         idmap config * : range = 1000000-1999999
         idmap config AADDSCONTOSO : backend = rid
         idmap config AADDSCONTOSO : range = 5000000-5999999
         kerberos method = secrets and keytab
         realm = AADDSCONTOSO.COM
         security = ADS
         template homedir = /home/%D/%U
         template shell = /bin/bash
         winbind offline logon = yes
         winbind refresh tickets = yes
     ```

   * /etc/krb5.conf
   
     ```ini
     [libdefaults]
         default_realm = AADDSCONTOSO.COM
         clockskew = 300
     [realms]
         AADDSCONTOSO.COM = {
             kdc = PDC.AADDSCONTOSO.COM
             default_domain = AADDSCONTOSO.COM
             admin_server = PDC.AADDSCONTOSO.COM
         }
     [domain_realm]
         .aaddscontoso.com = AADDSCONTOSO.COM
     [appdefaults]
         pam = {
             ticket_lifetime = 1d
             renew_lifetime = 1d
             forwardable = true
             proxiable = false
             minimum_uid = 1
         }
     ```

   * /etc/security/pam_winbind. conf
   
     ```ini
     [global]
         cached_login = yes
         krb5_auth = yes
         krb5_ccache_type = FILE
         warn_pwd_expire = 14
     ```

   * /etc/nsswitch.conf
   
     ```ini
     passwd: compat winbind
     group: compat winbind
     ```

3. Controleer of de datum en tijd in azure AD en Linux synchroon zijn. U kunt dit doen door de Azure AD-server toe te voegen aan de NTP-service:
   
   1. Voeg de volgende regel toe aan/etc/ntp.conf:
     
      ```console
      server aaddscontoso.com
      ```

   1. Start de NTP-service opnieuw:
     
      ```console
      sudo systemctl restart ntpd
      ```

4. Lid worden van het domein:

   ```console
   sudo net ads join -U Administrator%Mypassword
   ```

5. Schakel winbind in als een aanmeldings bron in de PAM-module voor pluggable verificatie modules:

   ```console
   pam-config --add --winbind
   ```

6. Automatisch maken van basis mappen inschakelen zodat gebruikers zich kunnen aanmelden:

   ```console
   pam-config -a --mkhomedir
   ```

7. Start de winbind-service en schakel deze in:

   ```console
   sudo systemctl enable winbind
   sudo systemctl start winbind
   ```

## <a name="allow-password-authentication-for-ssh"></a>Wachtwoord verificatie voor SSH toestaan

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
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>De groep ' AAD DC Administrators ' sudo privileges verlenen

Als u leden van de groep *Aad DC-Administrators* beheer bevoegdheden wilt verlenen op de SLE-VM, voegt u een vermelding toe aan de */etc/sudoers*. Eenmaal toegevoegd, kunnen leden van de groep *Aad DC-Administrators* de `sudo` opdracht gebruiken op de SLE-VM.

1. Open het *sudo* -bestand om het te bewerken:

    ```console
    sudo visudo
    ```

1. Voeg de volgende vermelding toe aan het einde van het */etc/sudoers* -bestand. De groep *Aad DC-Administrators* bevat witruimte in de naam, dus neem het escape-teken van de back slash op in de groeps naam. Voeg uw eigen domein naam toe, zoals *aaddscontoso.com*:

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    Als u klaar bent, slaat u de editor op en sluit u deze met de `:wq` opdracht van de editor.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Aanmelden bij de virtuele machine met behulp van een domein account

Als u wilt controleren of de virtuele machine is toegevoegd aan het beheerde domein, start u een nieuwe SSH-verbinding met behulp van een domein gebruikers account. Bevestig dat er een basismap is gemaakt en dat groepslid maatschap van het domein wordt toegepast.

1. Maak een nieuwe SSH-verbinding vanuit uw-console. Gebruik een domein account dat deel uitmaakt van het beheerde domein met behulp van de `ssh -l` opdracht, zoals `contosoadmin@aaddscontoso.com` en voer vervolgens het adres van uw virtuele machine in, bijvoorbeeld *Linux-q2gr.aaddscontoso.com*. Als u de Azure Cloud Shell gebruikt, gebruikt u het open bare IP-adres van de virtuele machine in plaats van de interne DNS-naam.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com linux-q2gr.aaddscontoso.com
    ```

2. Wanneer u verbinding hebt gemaakt met de virtuele machine, controleert u of de basismap correct is geïnitialiseerd:

    ```console
    pwd
    ```

    U moet zich in de */Home* -map met uw eigen map bekomen die overeenkomt met het gebruikers account.

3. Controleer nu of de groepslid maatschappen correct worden omgezet:

    ```console
    id
    ```

    U moet de groepslid maatschappen van het beheerde domein zien.

4. Als u zich bij de virtuele machine hebt aangemeld als lid van de groep *Aad DC-Administrators* , controleert u of u de opdracht kunt gebruiken `sudo` :

    ```console
    sudo zypper update
    ```

## <a name="next-steps"></a>Volgende stappen

Als u problemen ondervindt met het verbinden van de virtuele machine met het beheerde domein of als u zich aanmeldt met een domein account, raadpleegt u problemen [met domein deelname oplossen](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
