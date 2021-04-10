---
title: Pakket opname gebruiken voor proactieve netwerk bewaking met waarschuwingen-Azure Functions
titleSuffix: Azure Network Watcher
description: In dit artikel wordt beschreven hoe u een door een waarschuwing gegenereerde pakket opname maakt met Azure Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 8e2af73be0fc887b132f523133159472ce1d1f98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570975"
---
# <a name="use-packet-capture-for-proactive-network-monitoring-with-alerts-and-azure-functions"></a>Pakket opname gebruiken voor proactieve netwerk bewaking met waarschuwingen en Azure Functions

Network Watcher pakket vastleggen maakt opname sessies voor het bijhouden van verkeer in en van virtuele machines. Het opname bestand kan een filter hebben dat is gedefinieerd voor het bijhouden van alleen het verkeer dat u wilt bewaken. Deze gegevens worden vervolgens opgeslagen in een opslag-BLOB of lokaal op de gast machine.

Deze mogelijkheid kan op afstand worden gestart vanuit andere automatiserings scenario's, zoals Azure Functions. Met pakket opname beschikt u over de mogelijkheid om proactieve opnames uit te voeren op basis van gedefinieerde netwerk afwijkingen. Andere gebruiken zijn onder andere het verzamelen van netwerk statistieken, het ophalen van informatie over inbreuken op het netwerk, het opsporen van fouten in client-server communicatie en meer.

Resources die zijn geïmplementeerd in azure run 24/7. U en uw mede werkers kunnen de status van alle resources 24/7 niet actief controleren. Wat gebeurt er bijvoorbeeld als er een probleem optreedt bij 2 uur?

Door Network Watcher, waarschuwingen en functies vanuit het Azure-ecosysteem te gebruiken, kunt u proactief reageren op de gegevens en hulpprogram ma's voor het oplossen van problemen in uw netwerk.

![In het diagram wordt Network Watcher-extensie weer gegeven op een virtuele machine die naar een T C P-segment stuurt dat > 100-fout wordt verzonden en die naar Azure Functions stromen, die stromen naar de Network Watcher die terugvallen op Network Watcher uitbrei ding.][scenario]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

* De nieuwste versie van [Azure PowerShell](/powershell/azure/install-Az-ps).
* Een bestaand exemplaar van Network Watcher. Als u er nog geen hebt, [maakt u een instantie van Network Watcher](network-watcher-create.md).
* Een bestaande virtuele machine in dezelfde regio als Network Watcher met de extensie van de [Windows-extensie](../virtual-machines/extensions/network-watcher-windows.md) of [Linux-virtuele machine](../virtual-machines/extensions/network-watcher-linux.md).

## <a name="scenario"></a>Scenario

In dit voor beeld verzendt uw VM meer TCP-segmenten dan normaal en wilt u waarschuwingen ontvangen. TCP-segmenten worden hier als voor beeld gebruikt, maar u kunt elke wille keurige waarschuwings voorwaarde gebruiken.

Wanneer u een waarschuwing ontvangt, wilt u gegevens op pakket niveau ontvangen om te begrijpen waarom de communicatie is toegenomen. Vervolgens kunt u stappen ondernemen om de virtuele machine te retour neren naar normale communicatie.

In dit scenario wordt ervan uitgegaan dat u een bestaand exemplaar van Network Watcher en een resource groep hebt met een geldige virtuele machine.

In de volgende lijst vindt u een overzicht van de werk stroom die wordt uitgevoerd:

1. Er wordt een waarschuwing geactiveerd op uw VM.
1. De waarschuwing roept uw Azure-functie aan via een webhook.
1. Uw Azure-functie verwerkt de waarschuwing en start een sessie voor het vastleggen van Network Watcher-pakketten.
1. De pakket opname wordt uitgevoerd op de virtuele machine en verzamelt verkeer.
1. Het pakket opname bestand wordt geüpload naar een opslag account voor controle en diagnose.

Om dit proces te automatiseren, maken en koppelen we een waarschuwing op onze VM om te activeren wanneer het incident optreedt. We maken ook een functie om in Network Watcher aan te roepen.

Dit scenario doet het volgende:

* Hiermee maakt u een Azure-functie waarmee een pakket opname wordt gestart.
* Hiermee maakt u een waarschuwings regel op een virtuele machine en configureert u de waarschuwings regel om de Azure-functie aan te roepen.

## <a name="create-an-azure-function"></a>Een Azure-functie maken

De eerste stap is het maken van een Azure-functie voor het verwerken van de waarschuwing en het maken van een pakket opname.

1. Selecteer in de [Azure Portal](https://portal.azure.com) **een bron**  >  **berekenings**  >  **functie-app** maken.

    ![Een functie-app maken][1-1]

2. Voer op de Blade **functie-app** de volgende waarden in en selecteer **OK** om de app te maken:

    |**Instelling** | **Waarde** | **Details** |
    |---|---|---|
    |**Naam van app**|PacketCaptureExample|De naam van de functie-app.|
    |**Abonnement**|[Uw abonnement] Het abonnement waarvoor u de functie-app wilt maken.||
    |**Resourcegroep**|PacketCaptureRG|De resource groep die de functie-app bevat.|
    |**Hostingabonnement**|Verbruiksabonnement| Het type van het plan dat door uw functie-app wordt gebruikt. Opties zijn verbruik of Azure App Service plan. |
    |**Locatie**|VS - centraal| De regio waarin de functie-app moet worden gemaakt.|
    |**Opslagaccount**|automatisch gegenereerde| Het opslag account dat Azure Functions vereist voor opslag voor algemeen gebruik.|

3. Selecteer op de Blade **PacketCaptureExample functie-apps** de optie **functies**  >  **aangepaste functie**  > **+** .

4. Selecteer **http trigger-Power shell** en voer vervolgens de resterende informatie in. Ten slotte selecteert u **maken** om de functie te maken.

    |**Instelling** | **Waarde** | **Details** |
    |---|---|---|
    |**Scenario**|Experimenteel|Type scenario|
    |**Een naam voor de functie opgeven**|AlertPacketCapturePowerShell|De naam van de functie|
    |**Verificatieniveau**|Functie|Autorisatie niveau voor de functie|

![Functions-voor beeld][functions1]

> [!NOTE]
> De Power shell-sjabloon is experimentele en biedt geen volledige ondersteuning.

Aanpassingen zijn vereist voor dit voor beeld en worden beschreven in de volgende stappen.

### <a name="add-modules"></a>Modules toevoegen

Als u Network Watcher Power shell-cmdlets wilt gebruiken, moet u de meest recente Power shell-module uploaden naar de functie-app.

1. Voer de volgende Power shell-opdracht uit op de lokale computer waarop de meest recente Azure PowerShell modules zijn geïnstalleerd:

    ```powershell
    (Get-Module Az.Network).Path
    ```

    Dit voor beeld geeft u het lokale pad van uw Azure PowerShell-modules. Deze mappen worden in een latere stap gebruikt. De modules die worden gebruikt in dit scenario zijn:

   * Az.Network

   * Az.Accounts

   * Az.Resources

     ![Power shell-mappen][functions5]

1. Selecteer de **functie-app-instellingen**  >  **Ga naar app service-editor**.

    ![Instellingen voor functie-app][functions2]

1. Klik met de rechter muisknop op de map **AlertPacketCapturePowershell** en maak een map met de naam **azuremodules**. 

4. Maak een submap voor elke module die u nodig hebt.

    ![Map en submappen][functions3]

    * Az.Network

    * Az.Accounts

    * Az.Resources

1. Klik met de rechter muisknop op de submap **AZ. Network** en selecteer vervolgens **bestanden uploaden**. 

6. Ga naar uw Azure-modules. Selecteer in de lokale map **AZ. Network** alle bestanden in de map. Selecteer vervolgens **OK**. 

7. Herhaal deze stappen voor **AZ. accounts** en **AZ. resources**.

    ![Bestanden uploaden][functions6]

1. Wanneer u klaar bent, moet elke map de Power shell-module bestanden van uw lokale computer hebben.

    ![Power Shell-bestanden][functions7]

### <a name="authentication"></a>Verificatie

Als u de Power shell-cmdlets wilt gebruiken, moet u zich verifiëren. U kunt verificatie configureren in de functie-app. Als u verificatie wilt configureren, moet u omgevings variabelen configureren en een versleuteld sleutel bestand uploaden naar de functie-app.

> [!NOTE]
> Dit scenario bevat slechts één voor beeld van het implementeren van verificatie met Azure Functions. Er zijn nog andere manieren om dit te doen.

#### <a name="encrypted-credentials"></a>Versleutelde referenties

Met het volgende Power shell-script maakt u een sleutel bestand met de naam **PassEncryptKey. key**. Het bevat ook een versleutelde versie van het wacht woord dat wordt verstrekt. Dit wacht woord is hetzelfde wacht woord dat is gedefinieerd voor de Azure Active Directory toepassing die wordt gebruikt voor verificatie.

```powershell
#Variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#Keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#Get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

Maak een map met de naam **sleutels** onder **AlertPacketCapturePowerShell** in de app service-editor van de functie-app. Upload vervolgens het bestand **PassEncryptKey. key** dat u hebt gemaakt in het vorige Power shell-voor beeld.

![Functie sleutel][functions8]

### <a name="retrieve-values-for-environment-variables"></a>Waarden voor omgevings variabelen ophalen

De laatste vereiste is om de omgevings variabelen in te stellen die nodig zijn voor toegang tot de waarden voor verificatie. In de volgende lijst ziet u de omgevings variabelen die zijn gemaakt:

* AzureClientID

* AzureTenant

* AzureCredPassword


#### <a name="azureclientid"></a>AzureClientID

De client-ID is de toepassings-ID van een toepassing in Azure Active Directory.

1. Als u nog geen toepassing hebt om te gebruiken, voert u het volgende voor beeld uit om een toepassing te maken.

    ```powershell
    $app = New-AzADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > Het wacht woord dat u gebruikt bij het maken van de toepassing moet hetzelfde wacht woord zijn dat u eerder hebt gemaakt bij het opslaan van het sleutel bestand.

1. Selecteer in de Azure Portal **abonnementen**. Selecteer het abonnement dat u wilt gebruiken en selecteer vervolgens **toegangs beheer (IAM)**.

    ![Functie IAM][functions9]

1. Kies het account dat u wilt gebruiken en selecteer vervolgens **Eigenschappen**. Kopieer de toepassings-ID.

    ![Functions-toepassings-ID][functions10]

#### <a name="azuretenant"></a>AzureTenant

Haal de Tenant-ID op door het volgende Power shell-voor beeld uit te voeren:

```powershell
(Get-AzSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

De waarde van de omgevings variabele AzureCredPassword is de waarde die u krijgt om het volgende Power shell-voor beeld uit te voeren. Dit voor beeld is hetzelfde als die wordt weer gegeven in de sectie voor gaande **versleutelde referenties** . De waarde die nodig is, is de uitvoer van de `$Encryptedpassword` variabele.  Dit is het Service-Principal-wacht woord dat u hebt versleuteld met behulp van het Power shell-script.

```powershell
#Variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#Keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#Get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

### <a name="store-the-environment-variables"></a>De omgevings variabelen opslaan

1. Ga naar functie app. Selecteer vervolgens **functie-app-instellingen**  >  **app-instellingen configureren**.

    ![App-instellingen configureren][functions11]

1. Voeg de omgevings variabelen en hun waarden toe aan de app-instellingen en selecteer vervolgens **Opslaan**.

    ![App-instellingen][functions12]

### <a name="add-powershell-to-the-function"></a>Power shell toevoegen aan de functie

Het is nu tijd om vanuit de Azure-functie aanroepen naar Network Watcher te maken. Afhankelijk van de vereisten kan de implementatie van deze functie verschillen. De algemene stroom van de code is echter als volgt:

1. Invoer parameters verwerken.
2. Query's uitvoeren op bestaande pakket opnames om limieten te controleren en naam conflicten op te lossen.
3. Maak een pakket opname met de juiste para meters.
4. Navraag pakket vastleggen regel matig tot het is voltooid.
5. Informeer de gebruiker dat de pakket opname sessie is voltooid.

Het volgende voor beeld is Power shell-code die in de functie kan worden gebruikt. Er zijn waarden die moeten worden vervangen voor **subscriptionId**, **resourceGroupName** en **storageAccountName**.

```powershell
            #Import Azure PowerShell modules required to make calls to Network Watcher
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Accounts\Az.Accounts.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Network\Az.Network.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Resources\Az.Resources.psd1" -Global

            #Process alert request body
            $requestBody = Get-Content $req -Raw | ConvertFrom-Json

            #Storage account ID to save captures in
            $storageaccountid = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}"

            #Packet capture vars
            $packetcapturename = "PSAzureFunction"
            $packetCaptureLimit = 10
            $packetCaptureDuration = 10

            #Credentials
            $tenant = $env:AzureTenant
            $pw = $env:AzureCredPassword
            $clientid = $env:AzureClientId
            $keypath = "D:\home\site\wwwroot\AlertPacketCapturePowerShell\keys\PassEncryptKey.key"

            #Authentication
            $secpassword = $pw | ConvertTo-SecureString -Key (Get-Content $keypath)
            $credential = New-Object System.Management.Automation.PSCredential ($clientid, $secpassword)
            Connect-AzAccount -ServicePrincipal -Tenant $tenant -Credential $credential #-WarningAction SilentlyContinue | out-null


            #Get the VM that fired the alert
            if($requestBody.context.resourceType -eq "Microsoft.Compute/virtualMachines")
            {
                Write-Output ("Subscription ID: {0}" -f $requestBody.context.subscriptionId)
                Write-Output ("Resource Group:  {0}" -f $requestBody.context.resourceGroupName)
                Write-Output ("Resource Name:  {0}" -f $requestBody.context.resourceName)
                Write-Output ("Resource Type:  {0}" -f $requestBody.context.resourceType)

                #Get the Network Watcher in the VM's region
                $networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $requestBody.context.resourceRegion}

                #Get existing packetCaptures
                $packetCaptures = Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher

                #Remove existing packet capture created by the function (if it exists)
                $packetCaptures | %{if($_.Name -eq $packetCaptureName)
                { 
                    Remove-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName $packetCaptureName
                }}

                #Initiate packet capture on the VM that fired the alert
                if ((Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher).Count -lt $packetCaptureLimit){
                    echo "Initiating Packet Capture"
                    New-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $requestBody.context.resourceId -PacketCaptureName $packetCaptureName -StorageAccountId $storageaccountid -TimeLimitInSeconds $packetCaptureDuration
                    Out-File -Encoding Ascii -FilePath $res -inputObject "Packet Capture created on ${requestBody.context.resourceID}"
                }
            } 
 ``` 
#### <a name="retrieve-the-function-url"></a>De functie-URL ophalen 
1. Nadat u de functie hebt gemaakt, configureert u de waarschuwing om de URL aan te roepen die aan de functie is gekoppeld. Als u deze waarde wilt ophalen, kopieert u de functie-URL uit de functie-app.

    ![De functie-URL zoeken][functions13]

2. Kopieer de functie-URL voor uw functie-app.

    ![De functie-URL kopiëren][2]

Als u aangepaste eigenschappen nodig hebt in de payload van de POST-aanvraag van de webhook, raadpleegt u [een webhook configureren voor een Azure metric-waarschuwing](../azure-monitor/alerts/alerts-webhooks.md).

## <a name="configure-an-alert-on-a-vm"></a>Een waarschuwing configureren voor een virtuele machine

Waarschuwingen kunnen worden geconfigureerd om personen te waarschuwen wanneer een specifieke metriek een drempel overschrijdt die hieraan is toegewezen. In dit voor beeld bevindt de waarschuwing zich op de TCP-segmenten die worden verzonden, maar de waarschuwing kan worden geactiveerd voor veel andere metrische gegevens. In dit voor beeld wordt een waarschuwing geconfigureerd om een webhook aan te roepen om de functie aan te roepen.

### <a name="create-the-alert-rule"></a>De waarschuwings regel maken

Ga naar een bestaande virtuele machine en voeg vervolgens een waarschuwings regel toe. Meer gedetailleerde documentatie over het configureren van waarschuwingen vindt u [in azure monitor voor Azure-Services-Azure Portal](../azure-monitor/alerts/alerts-classic-portal.md). Voer de volgende waarden in op de Blade **waarschuwings regel** en selecteer **OK**.

  |**Instelling** | **Waarde** | **Details** |
  |---|---|---|
  |**Naam**|TCP_Segments_Sent_Exceeded|De naam van de waarschuwings regel.|
  |**Beschrijving**|Drempel waarde verzonden TCP-segmenten overschreden|De beschrijving voor de waarschuwings regel.|
  |**Meting**|Verzonden TCP-segmenten| De metriek die moet worden gebruikt om de waarschuwing te activeren. |
  |**Condition**|Groter dan| De voor waarde die moet worden gebruikt bij het evalueren van de metriek.|
  |**Spreek**|100| De waarde van de metriek waarmee de waarschuwing wordt geactiveerd. Deze waarde moet worden ingesteld op een geldige waarde voor uw omgeving.|
  |**Periode**|In de afgelopen vijf minuten| Bepaalt de periode waarin de drempel waarde voor de metriek moet worden gezocht.|
  |**Webhook**|[webhook-URL uit functie-app]| De webhook-URL uit de functie-app die in de vorige stappen is gemaakt.|

> [!NOTE]
> De metrische gegevens voor TCP-segmenten zijn standaard niet ingeschakeld. Meer informatie over het inschakelen van aanvullende metrische gegevens vindt u [controle en diagnostische gegevens inschakelen](../azure-monitor/overview.md).

## <a name="review-the-results"></a>De resultaten bekijken

Nadat de criteria voor de waarschuwing zijn geactiveerd, wordt een pakket opname gemaakt. Ga naar Network Watcher en selecteer **pakket vastleggen**. Op deze pagina kunt u de koppeling naar het pakket opname bestand selecteren om de pakket opname te downloaden.

![Pakket opname weer geven][functions14]

Als het opname bestand lokaal is opgeslagen, kunt u het ophalen door u aan te melden bij de virtuele machine.

Zie [aan de slag met Azure Blob Storage met .net](../storage/blobs/storage-quickstart-blobs-dotnet.md)voor instructies over het downloaden van bestanden van Azure-opslag accounts. Een ander hulp programma dat u kunt gebruiken, is [Storage Explorer](https://storageexplorer.com/).

Nadat uw opname is gedownload, kunt u deze weer geven met behulp van elk hulp programma dat een **. Cap** -bestand kan lezen. Hieronder vindt u koppelingen naar twee van deze hulpprogram ma's:

- [Micro soft Message Analyzer](/message-analyzer/microsoft-message-analyzer-operating-guide)
- [WireShark](https://www.wireshark.org/)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het weer geven van uw pakket opnames kunt u vinden met behulp van [pakket Capture-analyse met wireshark](network-watcher-deep-packet-inspection.md).


[1]: ./media/network-watcher-alert-triggered-packet-capture/figure1.png
[1-1]: ./media/network-watcher-alert-triggered-packet-capture/figure1-1.png
[2]: ./media/network-watcher-alert-triggered-packet-capture/figure2.png
[3]: ./media/network-watcher-alert-triggered-packet-capture/figure3.png
[functions1]:./media/network-watcher-alert-triggered-packet-capture/functions1.png
[functions2]:./media/network-watcher-alert-triggered-packet-capture/functions2.png
[functions3]:./media/network-watcher-alert-triggered-packet-capture/functions3.png
[functions4]:./media/network-watcher-alert-triggered-packet-capture/functions4.png
[functions5]:./media/network-watcher-alert-triggered-packet-capture/functions5.png
[functions6]:./media/network-watcher-alert-triggered-packet-capture/functions6.png
[functions7]:./media/network-watcher-alert-triggered-packet-capture/functions7.png
[functions8]:./media/network-watcher-alert-triggered-packet-capture/functions8.png
[functions9]:./media/network-watcher-alert-triggered-packet-capture/functions9.png
[functions10]:./media/network-watcher-alert-triggered-packet-capture/functions10.png
[functions11]:./media/network-watcher-alert-triggered-packet-capture/functions11.png
[functions12]:./media/network-watcher-alert-triggered-packet-capture/functions12.png
[functions13]:./media/network-watcher-alert-triggered-packet-capture/functions13.png
[functions14]:./media/network-watcher-alert-triggered-packet-capture/functions14.png
[scenario]:./media/network-watcher-alert-triggered-packet-capture/scenario.png