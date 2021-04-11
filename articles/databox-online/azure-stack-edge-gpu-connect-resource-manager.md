---
title: Verbinding maken met Azure Resource Manager op uw Azure Stack Edge Pro GPU-apparaat
description: Hierin wordt beschreven hoe u verbinding maakt met de Azure Resource Manager die wordt uitgevoerd op uw Azure Stack Edge Pro GPU met behulp van Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: e244c93fbe809c52eff1c42c284ce2e8389bbcc4
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553580"
---
# <a name="connect-to-azure-resource-manager-on-your-azure-stack-edge-pro-device"></a>Verbinding maken met Azure Resource Manager op uw Azure Stack Edge Pro-apparaat

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Resource Manager biedt een beheerlaag waarmee u resources in uw Azure-abonnement kunt maken, bijwerken en verwijderen. Het Azure Stack Edge Pro-apparaat ondersteunt dezelfde Azure Resource Manager Api's om Vm's te maken, bij te werken en te verwijderen in een lokaal abonnement. Met deze ondersteuning kunt u het apparaat beheren op een manier die overeenkomt met de Cloud. 

In deze zelf studie wordt beschreven hoe u verbinding maakt met de lokale Api's op uw Azure Stack Edge Pro-apparaat via Azure Resource Manager met behulp van Azure PowerShell.

## <a name="about-azure-resource-manager"></a>Over Azure Resource Manager

Azure Resource Manager biedt een consistente management-laag voor het aanroepen van de API voor het Azure Stack Edge Pro-apparaat en het uitvoeren van bewerkingen, zoals het maken, bijwerken en verwijderen van Vm's. De architectuur van de Azure Resource Manager wordt gedetailleerd beschreven in het volgende diagram.

![Diagram voor Azure Resource Manager](media/azure-stack-edge-gpu-connect-resource-manager/edge-device-flow.svg)


## <a name="endpoints-on-azure-stack-edge-pro-device"></a>Eind punten op Azure Stack Edge Pro-apparaat

De volgende tabel bevat een overzicht van de verschillende eind punten die beschikbaar zijn op uw apparaat, de ondersteunde protocollen en de poorten voor toegang tot deze eind punten. In het hele artikel vindt u verwijzingen naar deze eind punten.

| # | Eindpunt | Ondersteunde protocollen | Poort gebruikt | Gebruikt voor |
| --- | --- | --- | --- | --- |
| 1. | Azure Resource Manager | https | 443 | Verbinding maken met Azure Resource Manager voor automatisering |
| 2. | Beveiligings token service | https | 443 | Verificatie via toegangs-en vernieuwings tokens |
| 3. | Blob | https | 443 | Verbinding maken met de Blob-opslag via REST |


## <a name="connecting-to-azure-resource-manager-workflow"></a>Verbinding maken met Azure Resource Manager werk stroom

Het proces voor het maken van verbinding met lokale Api's van het apparaat met Azure Resource Manager vereist de volgende stappen:

| Wizardstap # | U voert deze stap uit... | .. op deze locatie. |
| --- | --- | --- |
| 1. | [Uw Azure Stack Edge Pro-apparaat configureren](#step-1-configure-azure-stack-edge-pro-device) | Lokale web-UI |
| 2. | [Certificaten maken en installeren](#step-2-create-and-install-certificates) | Windows-client/lokale web-UI |
| 3. | [De vereisten controleren en configureren](#step-3-install-powershell-on-the-client) | Windows-client |
| 4. | [Azure PowerShell instellen op de client](#step-4-set-up-azure-powershell-on-the-client) | Windows-client |
| 5. | [Het hostbestand voor de naam omzetting van het eind punt wijzigen](#step-5-modify-host-file-for-endpoint-name-resolution) | Windows-client of DNS-server |
| 6. | [Controleer of de naam van het eind punt is opgelost](#step-6-verify-endpoint-name-resolution-on-the-client) | Windows-client |
| 7. | [Azure PowerShell-cmdlets gebruiken om te controleren of de verbinding met Azure Resource Manager](#step-7-set-azure-resource-manager-environment) | Windows-client |

De volgende secties beschrijven elk van de bovenstaande stappen bij het maken van verbinding met Azure Resource Manager.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat de client die wordt gebruikt om verbinding te maken met het apparaat via Azure Resource Manager TLS 1,2 gebruikt. Ga voor meer informatie naar [Configure TLS 1,2 on Windows client access Azure stack Edge Pro Device](azure-stack-edge-gpu-configure-tls-settings.md).

## <a name="step-1-configure-azure-stack-edge-pro-device"></a>Stap 1: Azure Stack Edge Pro-apparaat configureren 

Voer de volgende stappen uit in de lokale web-UI van uw Azure Stack Edge Pro-apparaat.

1. Voltooi de netwerk instellingen voor uw Azure Stack Edge Pro-apparaat. 

    ![Pagina 'Netwerkinstellingen' van lokale webgebruikersinterface](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png)


    Noteer het IP-adres van het apparaat. U gebruikt dit IP-adres later.

2. Configureer de naam van het apparaat en het DNS-domein vanaf de pagina **apparaat** . Noteer de naam van het apparaat en het DNS-domein, zoals u deze later gebruikt.

    ![Pagina Apparaat van de lokale webinterface](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-2.png)

    > [!IMPORTANT]
    > De apparaatnaam, het DNS-domein wordt gebruikt om de eind punten te vormen die worden weer gegeven.
    > Gebruik de Azure Resource Manager-en BLOB-eind punten van de pagina **apparaat** in de lokale webgebruikersinterface.


## <a name="step-2-create-and-install-certificates"></a>Stap 2: certificaten maken en installeren

Certificaten zorgen ervoor dat uw communicatie vertrouwd is. Op uw Azure Stack Edge Pro-apparaat worden automatisch ondertekende apparaten, blobs en Azure Resource Manager certificaten gegenereerd. U kunt eventueel ook uw eigen ondertekende Blob en Azure Resource Manager certificaten meenemen.

Wanneer u een ondertekend certificaat van uw eigen certificaten inbrengt, moet u ook de bijbehorende ondertekening keten van het certificaat hebben. Voor de handtekening keten, Azure Resource Manager en de BLOB-certificaten op het apparaat hebt u de bijbehorende certificaten op de client computer ook nodig om te verifiëren en te communiceren met het apparaat.

Als u verbinding wilt maken met Azure Resource Manager, moet u een handtekening keten en eindpunt certificaten aanmaken of ophalen, deze certificaten importeren op uw Windows-client en deze certificaten ten slotte uploaden op het apparaat.

### <a name="create-certificates-optional"></a>Certificaten maken (optioneel)

Voor het gebruik van alleen testen en ontwikkelen kunt u Windows Power shell gebruiken om certificaten te maken op uw lokale systeem. Volg de volgende richt lijnen bij het maken van de certificaten voor de client:

1. U moet eerst een basis certificaat maken voor de handtekening keten. Zie stappen voor het [maken van handtekening keten certificaten](azure-stack-edge-gpu-manage-certificates.md#create-signing-chain-certificate)voor meer informatie.

2. U kunt vervolgens de eindpunt certificaten voor de BLOB en Azure Resource Manager maken. U kunt deze eind punten ophalen van de pagina **apparaat** in de lokale webgebruikersinterface. Zie de stappen voor het [maken van eindpunt certificaten](azure-stack-edge-gpu-manage-certificates.md#create-signed-endpoint-certificates).

3. Voor al deze certificaten moet u ervoor zorgen dat de naam van de onderwerpnaam en de alternatieve houder voldoen aan de volgende richt lijnen:

    |Type |Onderwerpnaam (SN)  |Alternatieve naam voor onderwerp (SAN)  |Voor beeld van onderwerpnaam |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob Storage|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Multi-SAN één certificaat voor beide eind punten|`<Device name>.<dnsdomain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |

Ga voor meer informatie over certificaten naar het beheren van [certificaten](azure-stack-edge-gpu-manage-certificates.md).

### <a name="upload-certificates-on-the-device"></a>Certificaten uploaden op het apparaat

De certificaten die u in de vorige stap hebt gemaakt, bestaan in het persoonlijke archief van uw client. Deze certificaten moeten op uw client worden geëxporteerd naar de juiste indelings bestanden die vervolgens naar uw apparaat kunnen worden geüpload.

1. Het basis certificaat moet worden geëxporteerd als een DER-Format-bestand met de extensie *. CER* . Zie [certificaten exporteren als. CER-indelings bestand](azure-stack-edge-gpu-manage-certificates.md#export-certificates-as-der-format)voor gedetailleerde stappen.

2. De eindpunt certificaten moeten worden geëxporteerd als *PFX* -bestanden met persoonlijke sleutels. Zie [certificaten exporteren als. pfx-bestand met persoonlijke sleutels](azure-stack-edge-gpu-manage-certificates.md#export-certificates-as-pfx-format-with-private-key)voor gedetailleerde stappen.

3. De basis-en eindpunt certificaten worden vervolgens op het apparaat geüpload met behulp van de optie **+ certificaat toevoegen** op de pagina **certificaten** in de lokale webgebruikersinterface. Als u de certificaten wilt uploaden, volgt u de stappen in [certificaten uploaden](azure-stack-edge-gpu-manage-certificates.md#upload-certificates).


### <a name="import-certificates-on-the-client-running-azure-powershell"></a>Certificaten importeren op de client met Azure PowerShell

De Windows-client waar u de Azure Resource Manager-Api's aanroept, moet een vertrouwens relatie met het apparaat tot stand brengen. Hiertoe moeten de certificaten die u hebt gemaakt in de vorige stap, worden geïmporteerd op de Windows-client in het juiste certificaat archief.

1. Het basis certificaat dat u hebt geëxporteerd als de bestands indeling met de extensie *. CER* moet nu worden geïmporteerd in de vertrouwde basis certificerings instanties op het client systeem. Zie [certificaten importeren in het archief met vertrouwde basis certificerings instanties](azure-stack-edge-gpu-manage-certificates.md#import-certificates-as-der-format) voor gedetailleerde stappen.

2. De eindpunt certificaten die u als *PFX* hebt geëxporteerd, moeten worden geëxporteerd als *. CER*. Deze *CER* wordt vervolgens geïmporteerd in het **persoonlijke** certificaat archief op uw systeem. Zie [certificaten importeren in persoonlijk archief](azure-stack-edge-gpu-manage-certificates.md#import-certificates-as-der-format)voor gedetailleerde stappen.

## <a name="step-3-install-powershell-on-the-client"></a>Stap 3: Power shell op de client installeren 

Uw Windows-client moet aan de volgende vereisten voldoen:

1. Voer Power shell-versie 5,0. U moet Power shell-versie 5,0 hebben. Power shell core wordt niet ondersteund. Als u de versie van Power shell op uw systeem wilt controleren, voert u de volgende cmdlet uit:

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Vergelijk de **primaire** versie en zorg ervoor dat deze 5,0 of hoger is.

    Zie [Een upgrade uitvoeren van een bestaande Windows PowerShell-versie](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true#upgrading-existing-windows-powershell) als u met een verouderde versie werkt.

    Als u \' geen Power shell 5,0 hebt, volgt u de [installatie van Windows Power shell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true).

    Hieronder ziet u een voorbeeld van de uitvoer.

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved. 
    Try the new cross-platform PowerShell https://aka.ms/pscore6
    PS C:\windows\system32> $PSVersionTable.PSVersion
    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      18362  145
    ```
    
2. U kunt toegang krijgen tot de PowerShell Gallery.

    Voer Power shell uit als Administrator. Controleer of de `PSGallery` is geregistreerd als opslag plaats.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop
    Get-PSRepository -Name "PSGallery"
    ```
    
    Hieronder ziet u een voorbeeld van de uitvoer.
    
    ```powershell
    PS C:\windows\system32> Import-Module -Name PowerShellGet -ErrorAction Stop
    PS C:\windows\system32> Import-Module -Name PackageManagement -ErrorAction Stop
    PS C:\windows\system32> Get-PSRepository -Name "PSGallery"
    Name                      InstallationPolicy   SourceLocation
    ----                      ------------------   --------------
    PSGallery                 Trusted              https://www.powershellgallery.com/api/v2
    ```
    
Als uw opslag plaats niet wordt vertrouwd of als u meer informatie nodig hebt, raadpleegt u [de PowerShell Gallery-toegankelijkheid valideren](/azure-stack/operator/azure-stack-powershell-install?view=azs-1908&preserve-view=true&preserve-view=true#2-validate-the-powershell-gallery-accessibility).

## <a name="step-4-set-up-azure-powershell-on-the-client"></a>Stap 4: Azure PowerShell instellen op de client 

<!--1. Verify the API profile of the client and identify which version of the Azure PowerShell modules and libraries to include on your client. In this example, the client system will be running Azure Stack 1904 or later. For more information, see [Azure Resource Manager API profiles](/azure-stack/user/azure-stack-version-profiles?view=azs-1908#azure-resource-manager-api-profiles).-->

1. U installeert Azure PowerShell modules op uw client die met uw apparaat werken.

    a. Voer PowerShell uit als beheerder. U moet toegang hebben tot Power shell Gallery. 


    b. Voer de volgende opdracht uit om de vereiste Azure PowerShell-modules te installeren via de PowerShell Gallery:

    ```powershell
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet.
    
    Install-Module -Name AzureRM.BootStrapper
    
   # Install and import the API Version Profile into the current PowerShell session.
    
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    
    # Confirm the installation of PowerShell
    Get-Module -Name "Azure*" -ListAvailable
    ```
    
    Zorg ervoor dat u de Azure-RM-module versie 2.5.0 uitvoert aan het einde van de installatie. 
    Als u een bestaande versie van de Azure-RM-module hebt die niet overeenkomt met de vereiste versie, verwijdert u met de volgende opdracht:

    `Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose`

    Nu moet u de vereiste versie opnieuw installeren.
   

Hieronder ziet u een voor beeld van een uitvoer die aangeeft dat de 2.5.0-modules van de AzureRM-versie zijn geïnstalleerd.

```powershell
PS C:\windows\system32> Install-Module -Name AzureRM.BootStrapper
PS C:\windows\system32> Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Loading Profile 2019-03-01-hybrid
PS C:\windows\system32> Get-Module -Name "Azure*" -ListAvailable
 
    Directory: C:\Program Files\WindowsPowerShell\Modules
 
ModuleType Version    Name                          ExportedCommands
---------- -------    ----                          ----------------
Script     4.5.0      Azure.Storage                       {Get-AzureStorageTable, New-AzureStorageTableSASToken, New...
Script     2.5.0      AzureRM
Script     0.5.0      AzureRM.BootStrapper                {Update-AzureRmProfile, Uninstall-AzureRmProfile, Install-...
Script     4.6.1      AzureRM.Compute                     {Remove-AzureRmAvailabilitySet, Get-AzureRmAvailabilitySet...
Script     3.5.1      AzureRM.Dns                         {Get-AzureRmDnsRecordSet, New-AzureRmDnsRecordConfig, Remo...
Script     5.1.5      AzureRM.Insights                    {Get-AzureRmMetricDefinition, Get-AzureRmMetric, Remove-Az...
Script     4.2.0      AzureRM.KeyVault                    {Add-AzureKeyVaultCertificate, Set-AzureKeyVaultCertificat...
Script     5.0.1      AzureRM.Network                     {Add-AzureRmApplicationGatewayAuthenticationCertificate, G...
Script     5.8.3      AzureRM.profile                     {Disable-AzureRmDataCollection, Disable-AzureRmContextAuto...
Script     6.4.3      AzureRM.Resources                   {Get-AzureRmProviderOperation, Remove-AzureRmRoleAssignmen...
Script     5.0.4      AzureRM.Storage                     {Get-AzureRmStorageAccount, Get-AzureRmStorageAccountKey, ...
Script     4.0.2      AzureRM.Tags                        {Remove-AzureRmTag, Get-AzureRmTag, New-AzureRmTag}
Script     4.0.3      AzureRM.UsageAggregates             Get-UsageAggregates
Script     5.0.1      AzureRM.Websites                    {Get-AzureRmAppServicePlan, Set-AzureRmAppServicePlan, New...

 
    Directory: C:\Program Files (x86)\Microsoft Azure Information Protection\Powershell
 
ModuleType Version    Name                            ExportedCommands
---------- -------    ----                           ----------------
Binary     1.48.204.0 AzureInformationProtection          {Clear-RMSAuthentication, Get-RMSFileStatus, Get-RMSServer...
```


## <a name="step-5-modify-host-file-for-endpoint-name-resolution"></a>Stap 5: het hostbestand wijzigen voor de naam omzetting van het eind punt 

U voegt nu het Azure consistente VIP toe dat u hebt gedefinieerd in de lokale web-UI van het apparaat naar:

- Het hostbestand op de client, OF
- De DNS-serverconfiguratie

> [!IMPORTANT]
> U wordt aangeraden de configuratie van de DNS-server voor de naam omzetting van het eind punt te wijzigen.

Voer de volgende stappen uit op de Windows-client die u gebruikt om verbinding te maken met het apparaat:

1. Start **Klad blok** als beheerder en open vervolgens het **hosts** -bestand dat zich bevindt in C:\Windows\System32\Drivers\etc.

    ![Hosts-bestand in Windows Verkenner](media/azure-stack-edge-gpu-connect-resource-manager/hosts-file.png)

2. Voeg de volgende vermeldingen toe aan uw **hosts**-bestand, waarbij u de juiste waarden voor uw apparaat invoert: 

    ```
    <Device IP> login.<appliance name>.<DNS domain>
    <Device IP> management.<appliance name>.<DNS domain>
    <Device IP> <storage name>.blob.<appliance name>.<DNS domain>
    ```

    > [!IMPORTANT]
    > De vermelding in het hosts-bestand moet exact overeenkomen met de waarde die is opgegeven om in een latere stap verbinding te maken met Azure Resource Manager. Zorg ervoor dat de DNS-domein vermelding hier is in de kleine letters.

    U hebt het IP-adres van het apparaat in een eerdere stap van de lokale webgebruikersinterface opgeslagen.

    De aanmelding. \<appliance name\> .\<DNS domain\> de vermelding is het eind punt voor de Security Token Service (STS). STS is verantwoordelijk voor het maken, valideren, vernieuwen en annuleren van beveiligings tokens. De beveiligings token service wordt gebruikt voor het maken van het toegangs token en het vernieuwings token dat wordt gebruikt voor continue communicatie tussen het apparaat en de client.

3. Gebruik de volgende afbeelding ter referentie. Sla het bestand met **hosts** op.

    ![Hosts-bestand in Kladblok](media/azure-stack-edge-gpu-connect-resource-manager/hosts-file-notepad.png)

## <a name="step-6-verify-endpoint-name-resolution-on-the-client"></a>Stap 6: de naam omzetting van eind punten op de client controleren

Controleer of de naam van het eind punt is omgezet op de client die u gebruikt om verbinding te maken met de Azure consistent VIP.

1. U kunt het opdracht regel hulpprogramma ping.exe gebruiken om te controleren of de naam van het eind punt is opgelost. Als er een IP-adres wordt opgegeven, retourneert de ping-opdracht de TCP/IP-hostnaam van de computer die u \' opnieuw wilt traceren.

    Voeg de `-a` Schakel optie toe aan de opdracht regel, zoals wordt weer gegeven in het onderstaande voor beeld. Als de hostnaam kan worden geretourneerd, wordt deze mogelijk waardevolle informatie in het antwoord geretourneerd.

    ![Ping in opdracht prompt](media/azure-stack-edge-gpu-connect-resource-manager/ping-command-prompt.png)



## <a name="step-7-set-azure-resource-manager-environment"></a>Stap 7: Azure Resource Manager omgeving instellen

Stel de Azure Resource Manager omgeving in en controleer of uw apparaat naar client communicatie via Azure Resource Manager prima werkt. Voer de volgende stappen uit om deze verificatie uit te voeren:


1. Gebruik de cmdlet `Add-AzureRmEnvironment` om ervoor te zorgen dat de communicatie via Azure Resource Manager goed werkt en de API-aanroepen worden uitgevoerd via de poort die is toegewezen voor Azure Resource Manager - 443.

    Met de cmdlet `Add-AzureRmEnvironment` voegt u eindpunten en metagegevens toe om Azure Resource Manager-cmdlets te gebruiken om verbinding te maken met een nieuw exemplaar van Azure Resource Manager. 


    > [!IMPORTANT]
    > De URL van het Azure Resource Manager-eind punt dat u in de volgende cmdlet opgeeft, is hoofdletter gevoelig. Zorg ervoor dat de eind punt-URL in kleine letters is en overeenkomt met wat u in het hosts-bestand hebt gegeven. Als de aanvraag niet overeenkomt, wordt er een fout melding weer geven.

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>/"
    ```

    Hieronder ziet u een voorbeeld van de uitvoer:
    
    ```powershell
    PS C:\windows\system32> Add-AzureRmEnvironment -Name AzDBE -ARMEndpoint https://management.dbe-n6hugc2ra.microsoftdatabox.com/
    
    Name  Resource Manager Url                    ActiveDirectory Authority
    ----  --------------------                   -------------------------
    AzDBE https://management.dbe-n6hugc2ra.microsoftdatabox.com https://login.dbe-n6hugc2ra.microsoftdatabox.com/adfs/
    ```

2. Stel de omgeving in als Azure Stack Edge Pro en de poort die moet worden gebruikt voor Azure Resource Manager-aanroepen als 443. U definieert de omgeving op twee manieren:

    - Stel de omgeving in. Typ de volgende opdracht:

    ```powershell
    Set-AzureRMEnvironment -Name <Environment Name>
    ```
    
    Ga naar [set-AzureRMEnvironment](/powershell/module/azurerm.profile/set-azurermenvironment?view=azurermps-6.13.0&preserve-view=true)voor meer informatie.

    - Definieer de omgeving inline voor elke cmdlet die u uitvoert. Dit zorgt ervoor dat alle API-aanroepen via de juiste omgeving worden uitgevoerd. Standaard worden de aanroepen via Azure openbaar, maar u wilt dat deze door de omgeving die u instelt voor Azure Stack Edge Pro-apparaat.

    - Zie voor meer informatie over [het scha kelen tussen AzureRM-omgevingen](#switch-environments).

2. Roep de API's van het lokale apparaat aan om de verbindingen met Azure Resource Manager te verifiëren. 

    1. Deze referenties gelden voor een lokale machine account en worden alleen gebruikt voor API-toegang.

    2. U kunt verbinding maken via `login-AzureRMAccount` of via de `Connect-AzureRMAccount` opdracht. 

        1. Typ de volgende opdracht om u aan te melden. De Tenant-ID in dit exemplaar is vastgelegd in vaste code-c0257de7-538f-415c-993a-1b87a031879d. Gebruik de volgende gebruikers naam en dit wacht woord.

            - **Gebruikers naam**  -  *EdgeArmUser*

            - **Wacht woord**  -  [Stel het wacht woord voor Azure Resource Manager](azure-stack-edge-gpu-set-azure-resource-manager-password.md) in en gebruik dit wacht woord om u aan te melden. 

            ```powershell
            PS C:\windows\system32> $pass = ConvertTo-SecureString "<Your password>" -AsPlainText -Force;
            PS C:\windows\system32> $cred = New-Object System.Management.Automation.PSCredential("EdgeArmUser", $pass)
            PS C:\windows\system32> Connect-AzureRmAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d -credential $cred
            
            Account       SubscriptionName   TenantId            Environment
            -------       ----------------   --------            -----------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            
            PS C:\windows\system32>
            ```
                   
        
            Een andere manier om u aan te melden, is door de cmdlet te gebruiken `login-AzureRmAccount` . 
            
            `login-AzureRMAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d` 

            Hier volgt een voor beeld van de uitvoer van de opdracht. 
         
            ```powershell
            PS C:\Users\Administrator> login-AzureRMAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d
            
            Account         SubscriptionName  TenantId              Environment
            -------         ----------------  --------              -------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            PS C:\Users\Administrator>
            ```



> [!IMPORTANT]
> De verbinding met Azure Resource Manager verloopt elke 1,5 uur of als uw Azure Stack Edge Pro-apparaat opnieuw wordt opgestart. Als dit gebeurt, worden met de cmdlets die u uitvoert, fout berichten geretourneerd naar het effect dat u niet meer met Azure hebt verbonden. U moet zich opnieuw aanmelden.

## <a name="switch-environments"></a>Scha kelen tussen omgevingen

Voer `Disconnect-AzureRmAccount` de opdracht uit om over te scha kelen naar een andere `AzureRmEnvironment` . 

Als u `Set-AzureRmEnvironment` en `Login-AzureRmAccount` zonder gebruikt `Disconnect-AzureRmAccount` , wordt de omgeving niet daad werkelijk geswitcheerd.  

In de volgende voor beelden ziet u hoe u kunt scha kelen tussen twee omgevingen `AzDBE1` en `AzDBE2` .

Eerst een lijst van alle bestaande omgevingen op uw client.


```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmEnvironment
Name    Resource Manager Url     ActiveDirectory Authority
----    --------------------      -------------------------
AzureChinaCloud   https://management.chinacloudapi.cn/                 https://login.chinacloudapi.cn/
AzureCloud        https://management.azure.com/                        https://login.microsoftonline.com/
AzureGermanCloud  https://management.microsoftazure.de/                https://login.microsoftonline.de/
AzDBE1            https://management.HVTG1T2-Test.microsoftdatabox.com https://login.hvtg1t2-test.microsoftdatabox.com/adfs/
AzureUSGovernment https://management.usgovcloudapi.net/                https://login.microsoftonline.us/
AzDBE2            https://management.CVV4PX2-Test.microsoftdatabox.com https://login.cvv4px2-test.microsoftdatabox.com/adfs/
```

Vervolgens krijgt u de omgeving waarmee u momenteel verbinding hebt via uw Azure Resource Manager.

```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (A4257FDE-B946-4E01-ADE7-674760B8D1A3) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE2
Subscription       : a4257fde-b946-4e01-ade7-674760b8d1a3
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```

U moet nu de verbinding met de huidige omgeving verbreken voordat u overschakelt naar de andere omgeving.


```azurepowershell
PS C:\WINDOWS\system32> Disconnect-AzureRmAccount

Id                    : EdgeArmUser@localhost
Type                  : User
Tenants               : {c0257de7-538f-415c-993a-1b87a031879d}
AccessToken           :
Credential            :
TenantMap             : {}
CertificateThumbprint :
ExtendedProperties    : {[Subscriptions, A4257FDE-B946-4E01-ADE7-674760B8D1A3], [Tenants, c0257de7-538f-415c-993a-1b87a031879d]}
```

Meld u aan bij de andere omgeving. Hieronder ziet u een voorbeeld van de uitvoer.

```azurepowershell
PS C:\WINDOWS\system32> Login-AzureRmAccount -Environment "AzDBE1" -TenantId $ArmTenantId

Account     SubscriptionName   TenantId        Environment
-------     ----------------   --------        -----------
EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE1
```

Voer deze cmdlet uit om te bevestigen met welke omgeving u verbinding hebt.

```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (A4257FDE-B946-4E01-ADE7-674760B8D1A3) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE1
Subscription       : a4257fde-b946-4e01-ade7-674760b8d1a3
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```
U bent nu overgeschakeld naar de gewenste omgeving.

## <a name="next-steps"></a>Volgende stappen

[Implementeer VM's op uw Azure Stack Edge-apparaat](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).
