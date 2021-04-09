---
title: De configuratie server voor herstel na nood gevallen met Azure Site Recovery beheren
description: Meer informatie over de algemene taken voor het beheren van een on-premises configuratie server voor herstel na nood gevallen van virtuele VMware-machines en fysieke servers naar Azure met Azure Site Recovery.
author: Rajeswari-Mamilla
ms.author: ramamill
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 67f25c97e0b4433ed09cb8c6337afe671424ba0b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048541"
---
# <a name="manage-the-configuration-server-for-vmware-vmphysical-server-disaster-recovery"></a>De configuratieserver beheren voor herstel na noodgeval van VMware-VM/fysieke server

U stelt een on-premises configuratie server in wanneer u [Azure site Recovery](site-recovery-overview.md) gebruikt voor herstel na nood gevallen van virtuele VMware-machines en fysieke servers naar Azure. De configuratie server coördineert de communicatie tussen on-premises VMware en Azure en beheert de gegevens replicatie. In dit artikel vindt u een overzicht van algemene taken voor het beheren van de configuratie server nadat deze is geïmplementeerd.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-windows-license"></a>Windows-licentie bijwerken

De licentie die is opgenomen in de OVF-sjabloon is een evaluatie licentie die gedurende 180 dagen geldig is. Voor niet-onderbroken gebruik moet u Windows activeren met een aangeschafte licentie. Licentie-update kan worden uitgevoerd via een zelfstandige sleutel of met de standaard KMS-sleutel. Richt lijnen is beschikbaar op de [DISM Windows-opdracht regel voor het uitvoeren van besturings systemen](/windows-hardware/manufacture/desktop/dism-windows-edition-servicing-command-line-options). Als u sleutels wilt verkrijgen, raadpleegt u de [KMS-client instellen](/windows-server/get-started/kmsclientkeys).

## <a name="access-configuration-server"></a>Toegangs configuratie server

U kunt als volgt toegang krijgen tot de configuratie server:

* Meld u aan bij de virtuele machine waarop het wordt geïmplementeerd en start **Azure Site Recovery Configuration Manager** vanaf de snelkoppeling op het bureau blad.
* U kunt de configuratie server ook op afstand openen via https://*ConfigurationServerName*/: 44315/. Meld u aan met beheerders referenties.

## <a name="modify-vmware-server-settings"></a>Instellingen voor VMware-Server wijzigen

1. Als u een andere VMware-Server aan de configuratie server wilt koppelen, selecteert u **vCenter Server/VSphere ESXi-server toevoegen** nadat u [zich hebt aangemeld](#access-configuration-server).
2. Voer de details in en selecteer **OK**.

## <a name="modify-credentials-for-automatic-discovery"></a>Referenties voor automatische detectie wijzigen

1. Als u de referenties die worden gebruikt om verbinding te maken met de VMware-Server voor automatische detectie van virtuele VMware-machines, wilt bijwerken, kiest u na [aanmelding](#access-configuration-server)het account en klikt u op **bewerken**.
2. Voer de nieuwe referenties in en selecteer **OK**.

    ![VMware wijzigen](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)

U kunt de referenties ook wijzigen via CSPSConfigtool.exe.

1. Meld u aan bij de configuratie server en start CSPSConfigtool.exe
2. Kies het account dat u wilt wijzigen en klik op **bewerken**.
3. Voer de gewijzigde referenties in en klik op **OK**

## <a name="modify-credentials-for-mobility-service-installation"></a>Referenties voor de installatie van de Mobility-service wijzigen

Wijzig de referenties die worden gebruikt om Mobility service automatisch te installeren op de virtuele VMware-machines die u voor replicatie inschakelt.

1. Nadat u [zich hebt aangemeld](#access-configuration-server), selecteert u referenties voor **virtuele machines beheren**
2. Kies het account dat u wilt wijzigen en klik op **bewerken**
3. Voer de nieuwe referenties in en selecteer **OK**.

    ![De referenties van de Mobility-service wijzigen](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

U kunt de referenties ook wijzigen via CSPSConfigtool.exe.

1. Meld u aan bij de configuratie server en start CSPSConfigtool.exe
2. Kies het account dat u wilt wijzigen en klik op **bewerken**
3. Voer de nieuwe referenties in en klik op **OK**.

## <a name="add-credentials-for-mobility-service-installation"></a>Referenties voor de installatie van de Mobility-service toevoegen

Als u het toevoegen van referenties tijdens de OVF-implementatie van de configuratie server hebt gemist,

1. Nadat u [zich hebt aangemeld](#access-configuration-server), selecteert u referenties voor **virtuele machines beheren**.
2. Klik op **referenties voor virtuele machines toevoegen**.
    ![Scherm afbeelding toont het deel venster referenties voor virtuele machine beheren met de koppeling referenties voor de virtuele machine toevoegen.](media/vmware-azure-manage-configuration-server/add-mobility-credentials.png)
3. Voer de nieuwe referenties in en klik op **toevoegen**.

U kunt ook referenties toevoegen via CSPSConfigtool.exe.

1. Meld u aan bij de configuratie server en start CSPSConfigtool.exe
2. Klik op **toevoegen**, voer de nieuwe referenties in en klik op **OK**.

## <a name="modify-proxy-settings"></a>Proxy-instellingen wijzigen

Wijzig de proxy-instellingen die door de configuratie server machine worden gebruikt voor Internet toegang tot Azure. Als u een proces server machine hebt, naast de standaard proces server die wordt uitgevoerd op de computer van de configuratie server, wijzigt u de instellingen op beide computers.

1. Nadat u [zich hebt aangemeld](#access-configuration-server) bij de configuratie server, selecteert u **Connectiviteit beheren**.
2. Werk de proxy waarden bij. Selecteer vervolgens **Opslaan** om de instellingen bij te werken.

## <a name="add-a-network-adapter"></a>Een netwerk adapter toevoegen

Met de sjabloon Open Virtualization Format (OVF) wordt de VM van de configuratie server geïmplementeerd met één netwerk adapter.

- U kunt [een extra adapter toevoegen aan de virtuele machine](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter), maar u moet deze toevoegen voordat u de configuratie server in de kluis registreert.
- Als u een adapter wilt toevoegen nadat u de configuratie server in de kluis hebt geregistreerd, voegt u de adapter toe aan de eigenschappen van de virtuele machine. Vervolgens moet u de server [opnieuw registreren](#reregister-a-configuration-server-in-the-same-vault) in de kluis.

## <a name="how-to-renew-ssl-certificates"></a>SSL-certificaten vernieuwen

De configuratie server heeft een ingebouwde webserver, waarmee de activiteiten van de Mobility-agents op alle beveiligde computers, ingebouwde, scale-out proces servers en Master doel servers die ermee zijn verbonden, worden beheerd. De webserver gebruikt een SSL-certificaat om clients te verifiëren. Het certificaat verloopt na drie jaar en kan op elk gewenst moment worden vernieuwd.

### <a name="check-expiry"></a>Verval datum controleren

De verval datum wordt weer gegeven onder status van de **Configuratie server**. Voor implementaties van de configuratie server vóór 2016 mei is certificaat verloop ingesteld op één jaar. Als u een certificaat wilt laten verlopen, gebeurt het volgende:

- Wanneer de verval datum twee maanden of minder is, start de service de verzen ding van meldingen in de portal en per e-mail (als u zich hebt geabonneerd op meldingen van Site Recovery).
- Er wordt een meldings banner weer gegeven op de pagina kluis resource. Selecteer de banner voor meer informatie.
- Als de knop **Nu bijwerken** wordt weer gegeven, betekent dit dat sommige onderdelen in uw omgeving niet zijn bijgewerkt naar 9,4. xxxx. x of hoger. Voer een upgrade uit voor de onderdelen voordat u het certificaat vernieuwt. U kunt niet vernieuwen op oudere versies.

### <a name="if-certificates-are-yet-to-expire"></a>Als certificaten nog verlopen zijn

1. Als u wilt vernieuwen, opent u **site Recovery-infrastructuur**  >  **Configuratie server** in de kluis. Selecteer de vereiste configuratie server.
2. Zorg ervoor dat alle onderdelen scale-out proces servers, Master doel servers en mobiliteits agenten op alle beveiligde computers op de nieuwste versies zijn en de status verbonden hebben.
3. Selecteer nu **certificaten vernieuwen**.
4. Volg zorgvuldig de instructies op deze pagina en klik op OK om de certificaten op de geselecteerde configuratie server en de bijbehorende onderdelen te vernieuwen.

### <a name="if-certificates-have-already-expired"></a>Als certificaten al zijn verlopen

1. Het bericht is verlopen, certificaten **kunnen niet worden vernieuwd vanuit Azure Portal**. Voordat u doorgaat, moet u ervoor zorgen dat alle componenten scale-out proces servers, Master doel servers en mobiliteits agenten op alle beveiligde computers de nieuwste versies hebben en de status verbonden hebben.
2. **Voer deze procedure alleen uit als de certificaten al zijn verlopen.** Meld u aan bij de configuratie server, navigeer naar C drive > Program Data > Site Recovery > start > svsystems > bin en voer het hulp programma RenewCerts uit als Administrator.
3. Er wordt een Power shell-uitvoerings venster geactiveerd en de certificaten worden verlengd. Dit kan tot 15 minuten duren. Sluit het venster pas nadat de verlenging is voltooid.

:::image type="content" source="media/vmware-azure-manage-configuration-server/renew-certificates.png" alt-text="RenewCertificates":::

## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Een configuratie server opnieuw registreren in dezelfde kluis

Als dat nodig is, kunt u de configuratie server opnieuw registreren in dezelfde kluis. Als u beschikt over een extra proces Server computer, moet u naast de standaard proces server die wordt uitgevoerd op de computer van de configuratie server, beide computers opnieuw registreren.


1. Open   >  **site Recovery-infrastructuur**  >  **configuratie servers** beheren in de kluis.
2. In **servers** selecteert u **registratie sleutel downloaden** om het bestand met kluis referenties te downloaden.
3. Meld u aan bij de computer met de configuratie server.
4. Open **cspsconfigtool.exe** in **%ProgramData%\ASR\home\svsystems\bin**.
5. Klik op het tabblad **kluis registratie** op **Bladeren** en zoek het kluis referentie bestand dat u hebt gedownload.
6. Geef zo nodig proxyserver gegevens op. Selecteer vervolgens **Registreren**.
7. Open een Power shell-opdracht venster voor beheerders en voer de volgende opdracht uit:
   ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
   ```

    >[!NOTE]
    >Als u de **meest recente certificaten** van de configuratie server wilt ophalen voor de scale-out proces server voert u de opdracht uit *\<Installation Drive\Microsoft Azure Site Recovery\agent\cdpcli.exe> --registermt uit*

8. Start ten slotte de obengine opnieuw door de volgende opdracht uit te voeren.
   ```
        net stop obengine
        net start obengine
   ```


## <a name="register-a-configuration-server-with-a-different-vault"></a>Een configuratie server registreren bij een andere kluis

> [!WARNING]
> Met de volgende stap wordt de configuratie server ontkoppeld van de huidige kluis en wordt de replicatie van alle beveiligde virtuele machines onder de configuratie server gestopt.

1. Meld u aan bij de configuratie server.
2. Open een Power shell-opdracht venster voor beheerders en voer de volgende opdracht uit:

    ```
    reg delete "HKLM\Software\Microsoft\Azure Site Recovery\Registration"
    net stop dra
    ```
3. Start de configuratie server toestel browser Portal met behulp van de snelkoppeling op het bureau blad.
4. Voer de registratie stappen uit die vergelijkbaar zijn met de [registratie](vmware-azure-tutorial.md#register-the-configuration-server)van een nieuwe configuratie server.

## <a name="upgrade-the-configuration-server"></a>De configuratie Server upgraden

U voert update pakketten uit om de configuratie server bij te werken. Updates kunnen Maxi maal N-4 versies worden toegepast. Bijvoorbeeld:

- Als u 9,7, 9,8, 9,9 of 9,10 uitvoert, kunt u rechtstreeks upgraden naar 9,11.
- Als u 9,6 of eerder uitvoert en u een upgrade naar 9,11 wilt uitvoeren, moet u eerst een upgrade uitvoeren naar versie 9,7. vóór 9,11.

Zie [hier](./service-updates-how-to.md#support-statement-for-azure-site-recovery)voor gedetailleerde richt lijnen voor de ondersteunings verklaring van Azure site Recovery-onderdelen.
Koppelingen naar update pakketten voor het uitvoeren van een upgrade naar alle versies van de configuratie server zijn [hier](./service-updates-how-to.md#links-to-currently-supported-update-rollups)beschikbaar.

> [!IMPORTANT]
> Bij elke nieuwe versie ' N ' van een Azure Site Recovery onderdeel dat is uitgebracht, worden alle versies onder N-4 als niet meer ondersteund. Het is altijd verstandig om te upgraden naar de meest recente beschik bare versie.</br>
> Zie [hier](./service-updates-how-to.md#support-statement-for-azure-site-recovery)voor gedetailleerde richt lijnen voor de ondersteunings verklaring van Azure site Recovery-onderdelen.

Voer als volgt een upgrade uit voor de server:

1. Ga in de kluis naar   >  configuratie servers voor **site Recovery-infra structuur** beheren  >  .
2. Als er een update beschikbaar is, wordt er een koppeling weer gegeven in de kolom **Agent versie** >.
    ![Update](./media/vmware-azure-manage-configuration-server/update2.png)
3. Down load het installatie bestand voor de update naar de configuratie server.

    ![Scherm afbeelding waarin wordt weer gegeven waar u moet klikken om het installatie bestand van de update te downloaden.](./media/vmware-azure-manage-configuration-server/update1.png)

4. Dubbel klik om het installatie programma uit te voeren.
5. Het installatie programma detecteert de huidige versie die op de computer wordt uitgevoerd. Klik op **Ja** om de upgrade te starten.
6. Wanneer de upgrade is voltooid, wordt de server configuratie gevalideerd.

    ![Scherm opname waarin de voltooide Server validatie configuratie wordt weer gegeven.](./media/vmware-azure-manage-configuration-server/update3.png)

7. Klik op **volt ooien** om het installatie programma te sluiten.
8. Raadpleeg onze [upgrade-richt lijnen](./service-updates-how-to.md#vmware-vmphysical-server-disaster-recovery-to-azure)om de rest van de site Recovery onderdelen bij te werken.

## <a name="upgrade-configuration-serverprocess-server-from-the-command-line"></a>Upgrade van de configuratie server of proces server vanaf de opdracht regel

Voer het installatie bestand als volgt uit:

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Voorbeeldgebruik
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="parameters"></a>Parameters

|Parameternaam| Type | Description| Waarden|
|-|-|-|-|
| /ServerMode|Vereist|Hiermee wordt aangegeven of zowel de configuratieserver als de processerver moet worden geïnstalleerd, of alleen de processerver|CS<br>PS|
|/InstallLocation|Vereist|De map waarin de onderdelen worden geïnstalleerd| Een map op de computer|
|/MySQLCredsFilePath|Vereist|Het bestandspad waarin de referenties voor de MySQL-server worden opgeslagen|Het bestand moet de indeling hebben die hieronder wordt aangegeven|
|/VaultCredsFilePath|Vereist|Het pad naar het bestand met kluisreferenties|Geldig bestandspad|
|/EnvType|Vereist|Type omgeving die u wilt beveiligen |VMware<br>NonVMware|
|/PSIP|Vereist|IP-adres van de NIC dat wordt gebruikt voor de overdracht van replicatiegegevens| Een geldig IP-adres|
|/CSIP|Vereist|Het IP-adres van de NIC waarop de configuratieserver luistert| Een geldig IP-adres|
|/PassphraseFilePath|Vereist|Het volledige pad naar het bestand met de wachtwoordzin|Geldig bestandspad|
|/BypassProxy|Optioneel|Hiermee wordt aangegeven dat de configuratieserver zonder proxy verbinding moet maken met Azure|Deze waarde moet van Venu worden opgehaald|
|/ProxySettingsFilePath|Optioneel|Proxy-instellingen (de standaardproxy vereist verificatie of een aangepaste proxy)|Het bestand moet de indeling hebben die hieronder wordt aangegeven|
|DataTransferSecurePort|Optioneel|Poortnummer van de PSIP dat wordt gebruikt voor replicatiegegevens| Geldig poortnummer (standaardwaarde is 9433)|
|/SkipSpaceCheck|Optioneel|Hiermee wordt aangegeven dat de controle op vrije ruimte in de cacheschijf moet worden overgeslagen| |
|/AcceptThirdpartyEULA|Vereist|Een vlag impliceert acceptatie van de gebruiksrechtovereenkomst van derden| |
|/ShowThirdpartyEULA|Optioneel|Hiermee wordt de gebruiksrechtovereenkomst van derden weergegeven. Indien opgegeven als invoer, worden alle andere parameters genegeerd| |



### <a name="create-file-input-for-mysqlcredsfilepath"></a>Bestands invoer maken voor MYSQLCredsFilePath

De para meter MySQLCredsFilePath neemt een bestand als invoer. Maak het bestand met de volgende indeling en geef het de para meter invoer MySQLCredsFilePath.
```ini
[MySQLCredentials]
MySQLRootPassword = "Password>"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>Bestands invoer maken voor ProxySettingsFilePath
ProxySettingsFilePath para meter gebruikt een bestand als invoer. Maak het bestand met de volgende indeling en geef het de para meter invoer ProxySettingsFilePath.

```ini
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```

## <a name="delete-or-unregister-a-configuration-server"></a>Een configuratie server verwijderen of de registratie ervan opheffen

1. [Schakel de beveiliging](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) voor alle virtuele machines onder de configuratie server uit.
2. [Koppel](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) en [Verwijder](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) alle replicatie beleidsregels van de configuratie server.
3. [Verwijder](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) alle vCenter-servers/vSphere-hosts die zijn gekoppeld aan de configuratie server.
4. Open **site Recovery-infrastructuur**  >  **configuratie servers** in de kluis.
5. Selecteer de configuratie server die u wilt verwijderen. Selecteer vervolgens op de pagina **Details** de optie **verwijderen**.

    ![Configuratie server verwijderen](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)


### <a name="delete-with-powershell"></a>Verwijderen met Power shell

U kunt eventueel de configuratie server verwijderen met behulp van Power shell.

1. [Installeer](/powershell/azure/install-Az-ps) de Azure PowerShell-module.
2. Meld u aan bij uw Azure-account met behulp van deze opdracht:

    `Connect-AzAccount`
3. Selecteer het kluis abonnement.

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  Stel de kluis context in.

    ```
    $vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. De configuratie server ophalen.

    `$fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. De configuratie server verwijderen.

    `Remove-AzSiteRecoveryFabric -Fabric $fabric [-Force]`

> [!NOTE]
> U kunt de optie **-Force** gebruiken in Remove-AzSiteRecoveryFabric voor geforceerde verwijdering van de configuratie server.

## <a name="generate-configuration-server-passphrase"></a>Wachtwoordzin voor configuratie server genereren

1. Meld u aan bij uw configuratie server en open vervolgens een opdracht prompt venster als beheerder.
2. Als u de map wilt wijzigen in de bin-map, voert u de opdracht **cd%ProgramData%\ASR\home\svsystems\bin** uit.
3. Als u het wachtwoordzinbestand wilt genereren, voert u **genpassphrase.exe-v > MobSvc. wachtwoordzin**.
4. Uw wachtwoordzin wordt opgeslagen in het bestand dat zich bevindt op **%ProgramData%\ASR\home\svsystems\bin\MobSvc.passphrase**.

## <a name="refresh-configuration-server"></a>Configuratie server vernieuwen

1. Ga in het Azure Portal naar **Recovery Services kluis**  >    >  **site Recovery infra structuur** beheren  >  **voor VMware &**  >  **configuratie servers** voor fysieke machines
2. Klik op de configuratie server die u wilt vernieuwen.
3. Klik op de Blade met details van de gekozen configuratie server op **meer**  >  **server vernieuwen**.
4. Controleer de voortgang van de taak onder **Recovery Services kluis**  >  **bewaking**  >  **site Recovery taken**.

## <a name="failback-requirements"></a>Vereisten voor failback

Tijdens het opnieuw beveiligen en failback moet de on-premises configuratie server worden uitgevoerd en de status verbonden hebben. Voor een geslaagde failback moet de virtuele machine die wordt teruggezet, bestaan in de data base van de configuratie server.

Zorg ervoor dat u regel matig geplande back-ups van uw configuratie server onderneemt. Als er zich een nood situatie voordoet en de configuratie server is verbroken, moet u eerst de configuratie server herstellen vanaf een back-upkopie en ervoor zorgen dat de herstelde configuratie server hetzelfde IP-adres heeft als waarop het is geregistreerd bij de kluis. Failback werkt niet als er een ander IP-adres wordt gebruikt voor de herstelde configuratie server.

## <a name="next-steps"></a>Volgende stappen

Bekijk de zelf studies voor het instellen van herstel na nood gevallen van [virtuele VMware-machines](vmware-azure-tutorial.md) in Azure.
