---
title: De configuratie server voor fysieke servers in Azure Site Recovery beheren
description: In dit artikel wordt beschreven hoe u de Azure Site Recovery configuratie server voor herstel na nood gevallen van een fysieke server naar Azure beheert.
services: site-recovery
author: mayurigupta13
ms.service: site-recovery
ms.topic: article
ms.date: 02/28/2019
ms.author: mayg
ms.openlocfilehash: ff612b7c052ead5658ea4bbfafd7aace51ba3c02
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017437"
---
# <a name="manage-the-configuration-server-for-physical-server-disaster-recovery"></a>De configuratie server voor herstel na nood gevallen voor fysieke servers beheren

U stelt een on-premises configuratie server in wanneer u de [Azure site Recovery](site-recovery-overview.md) -service gebruikt voor herstel na nood gevallen van fysieke servers naar Azure. De configuratie server coördineert de communicatie tussen on-premises computers en Azure, en beheert de gegevens replicatie. In dit artikel vindt u een overzicht van algemene taken voor het beheren van de configuratie server nadat deze is geïmplementeerd.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

De tabel bevat een overzicht van de vereisten voor het implementeren van de on-premises configuratie server machine.

| **Onderdeel** | **Vereiste** |
| --- |---|
| CPU-kernen| 8 |
| RAM | 16 GB|
| Aantal schijven | 3, waaronder de besturingssysteemschijf, de cacheschijf van de processerver en de bewaarschijf voor failback |
| Vrije schijfruimte (cache van de processerver) | 600 GB
| Vrije schijfruimte (bewaarschijf) | 600 GB|
| Besturingssysteem  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Landinstelling van het besturingssysteem | Engels (VS)|
| VMware vSphere PowerCLI-versie | Niet vereist|
| Windows Server-functies | Deze rollen niet inschakelen: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V |
| Groepsbeleidsregels| Deze groepsbeleidsregels niet inschakelen: <br> -Toegang tot de opdracht prompt voor komen <br> -Toegang tot register bewerkings Programma's voor komen <br> -Logica vertrouwen voor bestands bijlagen <br> -Uitvoering van script inschakelen <br> [Meer informatie](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))|
| IIS | - Geen vooraf bestaande standaardwebsite <br> - [Anonieme verificatie](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) inschakelen <br> - Instelling [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) inschakelen  <br> - Geen vooraf bestaande website/toepassing die luistert op poort 443<br>|
| Type NIC | VMXNET3 (wanneer geïmplementeerd als een VMware-VM) |
| Type IP-adres | Statisch |
| Toegang tot het internet | De server moet toegang hebben tot deze Url's: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - `https://management.azure.com` <br> -*. services.visualstudio.com <br> - https://dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi (niet vereist voor scale-out proces servers) <br> - time.nist.gov <br> - time.windows.com |
| Poorten | 443 (Orchestration-besturingselement)<br>9443 (Gegevenstransport)|

## <a name="download-the-latest-installation-file"></a>Down load het meest recente installatie bestand

De meest recente versie van het installatie bestand van de configuratie server is beschikbaar in de Site Recovery Portal. Daarnaast kan het rechtstreeks vanuit het [micro soft Download centrum](https://aka.ms/unifiedsetup)worden gedownload.

1. Meld u aan bij de Azure Portal en blader naar uw Recovery Services kluis.
2. Blader naar **site Recovery infrastructuur**  >  **configuratie servers** (onder voor VMware & fysieke machines).
3. Klik op de knop **+ servers** .
4. Klik op de pagina **server toevoegen** op de knop downloaden om de registratie sleutel te downloaden. U hebt deze sleutel nodig tijdens de installatie van de configuratie server om deze te registreren bij Azure Site Recovery service.
5. Klik op de koppeling **down load Microsoft Azure site Recovery Unified Setup** om de nieuwste versie van de configuratie server te downloaden.

   ![Download pagina](./media/physical-manage-configuration-server/downloadcs.png)


## <a name="install-and-register-the-server"></a>De server installeren en registreren

1. Voer het installatiebestand voor de geïntegreerde Setup uit.
2. Selecteer bij **Voordat u begint** de optie **De configuratieserver en processerver installeren**.

    ![Voordat u begint](./media/physical-manage-configuration-server/combined-wiz1.png)

3. Klik bij **Licentievoorwaarden voor software van derden** op **Ik ga akkoord** om MySQL te downloaden en te installeren.
4. Geef bij **Internetinstellingen** op hoe de provider die op de configuratieserver wordt uitgevoerd, via internet verbinding moet maken met Azure Site Recovery. Zorg ervoor dat u de vereiste URL's hebt.

    - Als u verbinding wilt maken met de proxy die momenteel op de computer is ingesteld, selecteert u **Verbinding maken met Azure Site Recovery via een proxyserver**.
    - Als u wilt dat de provider rechtstreeks verbinding maakt, selecteert u **Rechtstreeks verbinding maken met Azure Site Recovery zonder proxyserver**.
    - Als voor de bestaande proxy verificatie is vereist of als u voor de verbinding met de provider een aangepaste proxy wilt gebruiken, selecteert u **Verbinding maken met aangepaste proxyinstellingen** en geeft u het adres, de poort en referenties op.
     ![Firewall](./media/physical-manage-configuration-server/combined-wiz4.png)
6. Tijdens Setup wordt in **Controle op vereisten** gecontroleerd of de installatie kan worden uitgevoerd. Als er een waarschuwing wordt weergegeven over **Synchronisatiecontrole voor algemene tijd**, moet u controleren of de tijd op de systeemklok (instellingen voor **datum en tijd**) overeenkomt met de tijdzone.

    ![Vereisten](./media/physical-manage-configuration-server/combined-wiz5.png)
7. Maak bij **MySQL-configuratie** referenties voor aanmelden bij de MySQL-serverinstantie die is geïnstalleerd.

    ![MySQL](./media/physical-manage-configuration-server/combined-wiz6.png)
8. Selecteer bij **Details van de omgeving** of u virtuele VMware-machines wilt repliceren. Als dat het geval is, controleert Setup of PowerCLI 6,0 is geïnstalleerd.
9. Selecteer bij **Installatielocatie** waar u de binaire bestanden wilt installeren en de cache wilt opslaan. Het station dat u selecteert, moet ten minste 5 GB vrije schijfruimte bevatten, maar wij raden u aan een cachestation te gebruiken met minstens 600 GB vrije ruimte.

    ![Installatielocatie](./media/physical-manage-configuration-server/combined-wiz8.png)
10. Selecteer in **Netwerkselectie** eerst de NIC die de ingebouwde processerver gebruikt voor herstel na noodgeval en push installatie van de mobiliteitsservice op de bronmachines. Selecteer vervolgens de NIC die de configuratieserver gebruikt voor de connectiviteit met Azure. Poort 9443 is de standaardpoort voor het verzenden en ontvangen van replicatieverkeer, maar u kunt dit poortnummer aanpassen aan de vereisten van de omgeving. Naast poort 9443 wordt ook poort 443 geopend. Deze wordt door een webserver gebruikt om replicatiebewerkingen in te delen. Gebruik poort 443 niet voor het verzenden of ontvangen van replicatieverkeer.

    ![Netwerk selecteren](./media/physical-manage-configuration-server/combined-wiz9.png)


11. Lees de informatie bij **Samenvatting** en klik op **Installeren**. Wanneer de installatie is voltooid, wordt er een wachtwoordzin gegenereerd. U hebt deze nodig bij het inschakelen van de replicatie. Kopieer de wachtwoordzin daarom en bewaar deze op een veilige locatie.


Na voltooiing van de registratie wordt de server weergegeven op de blade **Instellingen** > **Servers** in de kluis.


## <a name="install-from-the-command-line"></a>Installeren vanaf de opdracht regel

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
MySQLRootPassword = "Password"
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
## <a name="modify-proxy-settings"></a>Proxy-instellingen wijzigen

U kunt de proxy-instellingen voor de configuratie Server computer als volgt wijzigen:

1. Meld u aan bij de configuratie server.
2. Start de cspsconfigtool.exe met behulp van de snelkoppeling op het bureau blad.
3. Klik op het tabblad **kluis registratie** .
4. Down load een nieuw kluis registratie bestand vanuit de portal en geef het op als invoer voor het hulp programma.

   ![REGI ster-configuratie-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Geef de nieuwe proxy Details op en klik op de knop **registreren** .
6. Open een Power shell-opdracht venster met beheerders rechten.
7. Voer de volgende opdracht uit:

   ```powershell
   $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
   net stop obengine
   net start obengine
   ```

   > [!WARNING]
   > Als er extra proces servers aan de configuratie server zijn gekoppeld, moet u [de proxy-instellingen op alle scale-out proces servers](vmware-azure-manage-process-server.md#modify-proxy-settings-for-an-on-premises-process-server) in uw implementatie herstellen.

## <a name="reregister-a-configuration-server-with-the-same-vault"></a>Een configuratie server opnieuw registreren met dezelfde kluis
1. Meld u aan bij uw configuratie server.
2. Start de cspsconfigtool.exe met behulp van de snelkoppeling op het bureau blad.
3. Klik op het tabblad **kluis registratie** .
4. Down load een nieuw registratie bestand vanuit de portal en geef dit op als invoer voor het hulp programma.
      ![REGI ster-configuratie-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Geef de proxyserver gegevens op en klik op de knop **registreren** .  
6. Open een Power shell-opdracht venster met beheerders rechten.
7. Voer de volgende opdracht uit

    ```powershell
    $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
    net stop obengine
    net start obengine
    ```

   > [!WARNING]
   > Als u meerdere proces servers hebt, moet u [ze opnieuw registreren](vmware-azure-manage-process-server.md#reregister-a-process-server).

## <a name="register-a-configuration-server-with-a-different-vault"></a>Een configuratie server registreren bij een andere kluis

> [!WARNING]
> Met de volgende stap wordt de configuratie server ontkoppeld van de huidige kluis en wordt de replicatie van alle beveiligde virtuele machines onder de configuratie server gestopt.

1. Meld u aan bij de configuratie server
2. Voer de opdracht uit vanaf een opdracht prompt van de beheerder:

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Start de cspsconfigtool.exe met behulp van de snelkoppeling op het bureau blad.
4. Klik op het tabblad **kluis registratie** .
5. Down load een nieuw registratie bestand vanuit de portal en geef dit op als invoer voor het hulp programma.
6. Geef de proxyserver gegevens op en klik op de knop **registreren** .  
7. Open een Power shell-opdracht venster met beheerders rechten.
8. Voer de volgende opdracht uit
    ```powershell
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrade-a-configuration-server"></a>Een configuratie Server upgraden

U voert update pakketten uit om de configuratie server bij te werken. Updates kunnen Maxi maal N-4 versies worden toegepast. Bijvoorbeeld:

- Als u 9,7, 9,8, 9,9 of 9,10 uitvoert, kunt u rechtstreeks een upgrade uitvoeren naar 9,11.
- Als u 9,6 of eerder gebruikt en u een upgrade naar 9,11 wilt uitvoeren, moet u eerst een upgrade naar versie 9,7 uitvoeren. vóór 9,11.

Koppelingen naar update pakketten voor het uitvoeren van een upgrade naar alle versies van de configuratie server zijn beschikbaar op de [pagina wiki-updates](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Voer als volgt een upgrade uit voor de server:

1. Down load het installatie bestand voor de update naar de configuratie server.
2. Dubbel klik om het installatie programma uit te voeren.
3. Het installatie programma detecteert de huidige versie die op de computer wordt uitgevoerd.
4. Klik op **OK** om te bevestigen en voer de upgrade uit. 


## <a name="delete-or-unregister-a-configuration-server"></a>Een configuratie server verwijderen of de registratie ervan opheffen

> [!WARNING]
> Controleer het volgende voordat u de configuratie server uit bedrijf neemt.
> 1. [Schakel de beveiliging uit](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) voor alle virtuele machines onder deze configuratie server.
> 2. [Koppel](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) en [Verwijder](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) alle replicatie beleidsregels van de configuratie server.
> 3. [Verwijder](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) alle vCenter-servers/vSphere-hosts die zijn gekoppeld aan de configuratie server.


### <a name="delete-the-configuration-server-from-azure-portal"></a>De configuratie server verwijderen uit Azure Portal
1. Ga in azure Portal naar **site Recovery infrastructuur**  >  **configuratie servers** in het menu kluis.
2. Klik op de configuratie server die u buiten gebruik wilt stellen.
3. Op de pagina Details van de configuratie server klikt u op de knop **verwijderen** .
4. Klik op **Ja** om het verwijderen van de server te bevestigen.

### <a name="uninstall-the-configuration-server-and-its-dependencies"></a>De configuratie server en de bijbehorende afhankelijkheden verwijderen
> [!TIP]
>   Als u van plan bent om de configuratie server opnieuw met Azure Site Recovery opnieuw te gebruiken, kunt u door gaan naar stap 4 rechtstreeks

1. Meld u als beheerder aan bij de configuratie server.
2. Open configuratie scherm > programma > Program Ma's verwijderen
3. Verwijder de Program ma's in de volgende volg orde:
   * Microsoft Azure Recovery Services-agent
   * Microsoft Azure Site Recovery Mobility service/Master doel server
   * Site Recovery provider Microsoft Azure
   * Configuratie server of proces Server Microsoft Azure Site Recovery
   * Afhankelijkheden van Site Recovery configuratie Server Microsoft Azure
   * MySQL-Server 5,5
4. Voer de volgende opdracht uit en beheer opdracht prompt.
   ```
   reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
   ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>Een configuratie server verwijderen of de registratie ervan opheffen (Power shell)

1. [Installeren](/powershell/azure/install-Az-ps) Module Azure PowerShell
2. Meld u aan bij uw Azure-account met behulp van de opdracht
    
    `Connect-AzAccount`
3. Selecteer het abonnement waaronder de kluis aanwezig is

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  Stel nu uw kluis context in
    
    ```powershell
    $Vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $Vault
    ```
4. De configuratie server selecteren

    `$Fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. De configuratie server verwijderen

    `Remove-AzSiteRecoveryFabric -Fabric $Fabric [-Force]`

> [!NOTE]
> De optie **-Force** in het Remove-AzSiteRecoveryFabric kan worden gebruikt om het verwijderen of verwijderen van de configuratie server af te dwingen.

## <a name="renew-tlsssl-certificates"></a>TLS/SSL-certificaten vernieuwen
De configuratie server heeft een ingebouwde webserver, die de activiteiten van de Mobility-service, de proces servers en de Master doel servers die ermee zijn verbonden, vastmaakt. De webserver gebruikt een TLS/SSL-certificaat om clients te verifiëren. Het certificaat verloopt na drie jaar en kan op elk gewenst moment worden vernieuwd.

### <a name="check-expiry"></a>Verval datum controleren

Voor implementaties van de configuratie server vóór 2016 mei is certificaat verloop ingesteld op één jaar. Als u een certificaat verloopt, gebeurt het volgende:

- Wanneer de verval datum twee maanden of minder is, start de service de verzen ding van meldingen in de portal en per e-mail (als u zich hebt geabonneerd op meldingen van Azure Site Recovery).
- Er wordt een meldings banner weer gegeven op de pagina kluis resource. Klik op de banner voor meer informatie.
- Als de knop **Nu bijwerken** wordt weer gegeven, geeft dit aan dat er sommige onderdelen in uw omgeving zijn die niet zijn bijgewerkt naar 9,4. xxxx. x of hoger. Upgrade onderdelen voordat u het certificaat vernieuwt. U kunt niet vernieuwen op oudere versies.

### <a name="renew-the-certificate"></a>Het certificaat vernieuwen

1. Open **site Recovery-infrastructuur**  >  **Configuratie server** in de kluis en klik op de vereiste configuratie server.
2. De verval datum wordt weer gegeven onder status van de **Configuratie server**
3. Klik op **certificaten vernieuwen**. 




## <a name="common-issues"></a>Algemene problemen
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>Volgende stappen

Bekijk de zelf studies voor het instellen van herstel na nood gevallen van [fysieke servers](./physical-azure-disaster-recovery.md) naar Azure.
