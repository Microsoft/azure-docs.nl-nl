---
title: Externe on-premises resources beheren met behulp van Power shell-functies
description: Meer informatie over het configureren van Hybride verbindingen in Azure Relay om een Power shell-functie-app te verbinden met on-premises resources, die vervolgens kan worden gebruikt om de on-premises resource op afstand te beheren.
author: eamono
ms.topic: conceptual
ms.date: 04/26/2020
ms.author: eamono
ms.openlocfilehash: 5e01ffd8e17fda9113c7ec0fdb2c7f436b39c810
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97936903"
---
# <a name="managing-hybrid-environments-with-powershell-in-azure-functions-and-app-service-hybrid-connections"></a>Hybride omgevingen beheren met Power shell in Azure Functions en App Service Hybride verbindingen

Met de functie Azure App Service Hybride verbindingen kunt u toegang krijgen tot bronnen in andere netwerken. Meer informatie over deze mogelijkheid vindt u in de [hybride verbindingen](../app-service/app-service-hybrid-connections.md) -documentatie. In dit artikel wordt beschreven hoe u deze mogelijkheid kunt gebruiken om Power shell-functies uit te voeren die gericht zijn op een on-premises server. Deze server kan vervolgens worden gebruikt voor het beheren van alle resources in de on-premises omgeving vanuit een Azure PowerShell-functie.


## <a name="configure-an-on-premises-server-for-powershell-remoting"></a>Een on-premises server configureren voor externe communicatie met Power shell

Het volgende script schakelt externe communicatie van Power shell in en maakt een nieuwe firewall regel en een WinRM https-listener. Voor test doeleinden wordt een zelfondertekend certificaat gebruikt. In een productie omgeving wordt u aangeraden een ondertekend certificaat te gebruiken.

```powershell
# For configuration of WinRM, see
# https://docs.microsoft.com/windows/win32/winrm/installation-and-configuration-for-windows-remote-management.

# Enable PowerShell remoting.
Enable-PSRemoting -Force

# Create firewall rule for WinRM. The default HTTPS port is 5986.
New-NetFirewallRule -Name "WinRM HTTPS" `
                    -DisplayName "WinRM HTTPS" `
                    -Enabled True `
                    -Profile "Any" `
                    -Action "Allow" `
                    -Direction "Inbound" `
                    -LocalPort 5986 `
                    -Protocol "TCP"

# Create new self-signed-certificate to be used by WinRM.
$Thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME  -CertStoreLocation Cert:\LocalMachine\My).Thumbprint

# Create WinRM HTTPS listener.
$Cmd = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:COMPUTERNAME ""; CertificateThumbprint=""$Thumbprint""}"
cmd.exe /C $Cmd
```

## <a name="create-a-powershell-function-app-in-the-portal"></a>Een Power shell-functie-app maken in de portal

De functie App Service Hybride verbindingen is alleen beschikbaar in de abonnementen Basic, Standard en geïsoleerd. Wanneer u de functie-app met Power Shell maakt, maakt of selecteert u een van deze plannen.

1. Selecteer vanuit het menu van Azure Portal of op de **startpagina** de optie **Een resource maken**.

1. Selecteer op de pagina **Nieuw** **Reken** > **functie-app**.

1. Op de pagina **Basics** gebruikt u de instellingen voor de functie-app zoals in de volgende tabel wordt vermeld.

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe functie-app is gemaakt. |
    | **[Resourcegroep](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Naam voor de nieuwe resourcegroep waarin uw functie-app moet worden gemaakt. |
    | **Naam van de functie-app** | Wereldwijd unieke naam | Naam waarmee uw nieuwe functie-app wordt aangeduid. Geldige tekens zijn `a-z` (hoofdlettergevoelig), `0-9` en `-`.  |
    |**Publiceren**| Code | Optie voor het publiceren van codebestanden of een Docker-container. |
    | **Runtimestack** | Voorkeurstaal | Kies Power shell core. |
    |**Versie**| Versienummer | Kies de versie van uw geïnstalleerde runtime.  |
    |**Regio**| Voorkeursregio | Kies een [regio](https://azure.microsoft.com/regions/) in de buurt of in de buurt van andere services die door uw functie worden gebruikt. |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-basics.png" alt-text="Maak een functie-app-basis." border="true":::

1. Selecteer **Volgende : Hosting**. Voer op de pagina **Hosting** de volgende instellingen in.

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **[Opslagaccount](../storage/common/storage-account-create.md)** |  Wereldwijd unieke naam |  Maak een opslagaccount die wordt gebruikt door uw functie-app. Namen van opslagaccounts moeten tussen 3 en 24 tekens lang zijn en kunnen alleen cijfers en kleine letters bevatten. U kunt ook een bestaand account gebruiken dat voldoet aan de [vereisten voor een opslagaccount](../azure-functions/storage-considerations.md#storage-account-requirements). |
    |**Besturingssysteem**| Voorkeurbesturingssysteem | Er wordt vooraf een besturingssysteem geselecteerd voor u op basis van de selectie van de runtimestack, maar u kunt de instelling wijzigen, indien nodig. |
    | **[Plantype](../azure-functions/functions-scale.md)** | **App service-plan** | Kies **app service-plan**. Wanneer u in een App Service-plan uitvoert, moet u het [Schalen van uw functie-app](../azure-functions/functions-scale.md) beheren.  |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-hosting.png" alt-text="Maak een functie-app-hosting." border="true":::

1. Selecteer **Volgende : Bewaking**. Voer op de pagina **Bewaking** de volgende instellingen in.

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../azure-functions/functions-monitoring.md)** | Standaard | Hiermee maakt u een Application Insights-resource van dezelfde *app-naam* in de dichtstbijzijnde ondersteunde regio. Door deze instelling uit te vouwen of **Nieuwe maken** te selecteren, kunt u de naam van Application Insights wijzigen of een andere regio kiezen in een [Azure-geografie](https://azure.microsoft.com/global-infrastructure/geographies/) waar u uw gegevens wilt opslaan. |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-monitoring.png" alt-text="Maak een functie-app-bewaking." border="true":::

1. Selecteer **Beoordelen + maken** om de selecties van appconfiguratie te controleren.

1. Controleer uw instellingen op de pagina **Beoordelen en maken** en selecteer vervolgens **Maken** om de functie-app in te richten en te implementeren.

1. Selecteer het **Meldingspictogram** in de rechterbovenhoek van de portal en zoek het bericht **Implementatie voltooid**.

1. Selecteer **Naar de resource gaan** om uw nieuwe functie-app te bekijken. U kunt ook **Vastmaken aan dashboard** selecteren. Vastmaken maakt het gemakkelijker om terug te gaan naar deze functie-app-resource vanuit uw dashboard.

## <a name="create-a-hybrid-connection-for-the-function-app"></a>Een hybride verbinding maken voor de functie-app

Hybride verbindingen worden geconfigureerd via het gedeelte netwerken van de functie-app:

1. Selecteer **netwerken** onder **instellingen** in de functie-app die u zojuist hebt gemaakt. 
1. Selecteer **de eind punten voor uw hybride verbindingen configureren**.
   
    :::image type="content" source="./media/functions-hybrid-powershell/configure-hybrid-connection-endpoint.png" alt-text="De hybride verbindings eindpunten configureren." border="true":::

1. Selecteer **hybride verbinding toevoegen**.
   
    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-connection-overview.png" alt-text="Een hybride verbinding toevoegen." border="true":::

1. Voer informatie in over de hybride verbinding zoals deze wordt weer gegeven na de volgende scherm afbeelding. U hebt de mogelijkheid om de instelling **endpoint host** te laten overeenkomen met de hostnaam van de on-premises server, zodat u de server gemakkelijker later kunt onthouden wanneer u externe opdrachten uitvoert. De poort komt overeen met de standaard Windows Remote Management-service poort die eerder op de server is gedefinieerd.
  
      :::image type="content" source="./media/functions-hybrid-powershell/add-hybrid-connection.png" alt-text="Hybride verbinding toevoegen." border="true":::

    | Instelling      | Voorgestelde waarde  |
    | ------------ | ---------------- |
    | **Naam van hybride verbinding** | ContosoHybridOnPremisesServer |
    | **Endpoint-host** | finance1 |
    | **Eindpunt poort** | 5986 |
    | **Servicebus-naam ruimte** | Create New |
    | **Locatie** | Een beschik bare locatie kiezen |
    | **Naam** | contosopowershellhybrid | 

1. Selecteer **OK** om de hybride verbinding te maken.

## <a name="download-and-install-the-hybrid-connection"></a>De hybride verbinding downloaden en installeren

1. Selecteer **verbindings beheer downloaden** om het *MSI* -bestand lokaal op uw computer op te slaan.

    :::image type="content" source="./media/functions-hybrid-powershell/download-hybrid-connection-installer.png" alt-text="Down load het installatie programma." border="true":::

1. Kopieer het *MSI* -bestand van uw lokale computer naar de on-premises server.
1. Voer het Hybrid Connection Manager-installatie programma uit om de service op de on-premises server te installeren.

    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-installation.png" alt-text="Installeer de hybride verbinding." border="true":::

1. Open de hybride verbinding vanuit de portal en kopieer de gateway connection string naar het klem bord.

    :::image type="content" source="./media/functions-hybrid-powershell/copy-hybrid-connection.png" alt-text="Kopieer de hybride connection string." border="true":::

1. Open de Hybrid Connection Manager gebruikers interface op de on-premises server.

    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-connection-ui.png" alt-text="Open de gebruikers interface van de hybride verbinding." border="true":::

1. Selecteer **hand matig invoeren** en plak de Connection String van het klem bord.

    :::image type="content" source="./media/functions-hybrid-powershell/enter-manual-connection.png" alt-text="Plak de hybride verbinding." border="true":::

1. Start de Hybrid Connection Manager van Power shell als deze niet wordt weer gegeven als verbonden.
    ```powershell
    Restart-Service HybridConnectionManager
    ```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>Een app-instelling maken voor het wacht woord van een beheerders account

1. Selecteer **configuratie** onder **instellingen** voor de functie-app. 
1. Selecteer **+ nieuwe toepassings instelling**.

    :::image type="content" source="./media/functions-hybrid-powershell/select-configuration.png" alt-text="Configureer een wacht woord voor het beheerders account." border="true":::

1. Noem de instelling **ContosoUserPassword** en voer het wacht woord in. Selecteer **OK**.
1. Selecteer **Opslaan** om het wacht woord op te slaan in de functie toepassing.

    :::image type="content" source="./media/functions-hybrid-powershell/save-administrator-password.png" alt-text="Sla het wacht woord voor het beheerders account op." border="true":::

## <a name="create-a-function-http-trigger"></a>Een HTTP-trigger functie maken

1. Selecteer in de functie-app **functies** en selecteer vervolgens **+ toevoegen**.

    :::image type="content" source="./media/functions-hybrid-powershell/create-http-trigger-function.png" alt-text="Maak een nieuwe HTTP-trigger." border="true":::

1. Selecteer de sjabloon **http-trigger** .

    :::image type="content" source="./media/functions-hybrid-powershell/select-http-trigger-template.png" alt-text="Selecteer de sjabloon HTTP-trigger." border="true":::

1. Noem de nieuwe functie en selecteer **functie maken**.

    :::image type="content" source="./media/functions-hybrid-powershell/create-new-http-function.png" alt-text="Geef een naam op en maak de nieuwe HTTP-trigger functie." border="true":::

## <a name="test-the-function"></a>De functie testen

1. Selecteer in de nieuwe functie **code + test**. Vervang de Power shell-code uit de sjabloon door de volgende code:

    ```powershell
    # Input bindings are passed in via param block.
    param($Request, $TriggerMetadata)
    
    # Write to the Azure Functions log stream.
    Write-Output "PowerShell HTTP trigger function processed a request."
    
    # Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
    $UserName = "ContosoUser"
    $securedPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
    $Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)
    
    # This is the name of the hybrid connection Endpoint.
    $HybridEndpoint = "finance1"
    
    $Script = {
        Param(
            [Parameter(Mandatory=$True)]
            [String] $Service
        )
        Get-Service $Service
    }
    
    Write-Output "Scenario 1: Running command via Invoke-Command"
    Invoke-Command -ComputerName $HybridEndpoint `
                   -Credential $Credential `
                   -Port 5986 `
                   -UseSSL `
                   -ScriptBlock $Script `
                   -ArgumentList "*" `
                   -SessionOption (New-PSSessionOption -SkipCACheck)
    ```

1. Selecteer **Opslaan**.

    :::image type="content" source="./media/functions-hybrid-powershell/save-http-function.png" alt-text="Wijzig de Power shell-code en sla de HTTP-trigger functie op." border="true":::

 1. Selecteer **testen** en selecteer vervolgens **uitvoeren** om de functie te testen. Controleer de logboeken om te controleren of de test is geslaagd.

     :::image type="content" source="./media/functions-hybrid-powershell/test-function-hybrid.png" alt-text="Test de HTTP-activerings functie." border="true":::

## <a name="managing-other-systems-on-premises"></a>On-premises andere systemen beheren

U kunt de verbonden on-premises server gebruiken om verbinding te maken met andere servers en beheer systemen in de lokale omgeving. Hiermee kunt u uw Data Center-bewerkingen beheren vanuit Azure met behulp van uw Power shell-functies. Met het volgende script wordt een Power shell-configuratie sessie geregistreerd die wordt uitgevoerd onder de gegeven referenties. Deze referenties moeten voor een beheerder op de externe servers zijn. U kunt deze configuratie vervolgens gebruiken voor toegang tot andere eind punten op de lokale server of het Data Center.

```powershell
# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
$UserName = "ContosoUser"
$SecuredPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
$Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)

# This is the name of the hybrid connection Endpoint.
$HybridEndpoint = "finance1"

# The remote server that will be connected to run remote PowerShell commands on
$RemoteServer = "finance2".

Write-Output "Use hybrid connection server as a jump box to connect to a remote machine"

# We are registering an endpoint that runs under credentials ($Credential) that has access to the remote server.
$SessionName = "HybridSession"
$ScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $SessionName)

    if (-not (Get-PSSessionConfiguration -Name $SessionName -ErrorAction SilentlyContinue))
    {
        Register-PSSessionConfiguration -Name $SessionName -RunAsCredential $Using:Credential
    }
}

Write-Output "Registering session on hybrid connection jumpbox"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $ScriptCommand `
               -ArgumentList $SessionName `
               -SessionOption (New-PSSessionOption -SkipCACheck)

# Script to run on the jump box to run against the second machine.
$RemoteScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $ComputerName)
        # Write out the hostname of the hybrid connection server.
        hostname
        # Write out the hostname of the remote server.
        Invoke-Command -ComputerName $ComputerName -Credential $Using:Credential -ScriptBlock {hostname} `
                        -UseSSL -Port 5986 -SessionOption (New-PSSessionOption -SkipCACheck)
}

Write-Output "Running command against remote machine via jumpbox by connecting to the PowerShell configuration session"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $RemoteScriptCommand `
               -ArgumentList $RemoteServer `
               -SessionOption (New-PSSessionOption -SkipCACheck) `
               -ConfigurationName $SessionName
```

Vervang de volgende variabelen in dit script door de toepasselijke waarden uit uw omgeving:
* $HybridEndpoint
* $RemoteServer

In de twee voor gaande scenario's kunt u verbinding maken met uw on-premises omgevingen en deze beheren met behulp van Power shell in Azure Functions en Hybride verbindingen. We raden u aan meer te weten te komen over [hybride verbindingen](../app-service/app-service-hybrid-connections.md) en [Power shell in functions](./functions-reference-powershell.md).

U kunt ook Azure [Virtual Networks](./functions-create-vnet.md) gebruiken om verbinding te maken met uw on-premises omgeving via Azure functions.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Meer informatie over het werken met Power shell-functies](functions-reference-powershell.md)
