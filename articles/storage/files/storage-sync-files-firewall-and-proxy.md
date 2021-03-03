---
title: Azure File Sync on-premises firewall en proxy-instellingen | Microsoft Docs
description: Meer informatie over Azure File Sync on-premises proxy-en Firewall instellingen. Bekijk de configuratie gegevens voor poorten, netwerken en speciale verbindingen met Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/02/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f0dbe7f32f14eb4da3d591811d619eb2e9bea397
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729637"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Proxy- en firewallinstellingen van Azure File Sync
Azure File Sync maakt verbinding met uw on-premises servers met Azure Files, waarbij functies voor multi-site synchronisatie en Cloud lagen worden ingeschakeld. Als zodanig moet een on-premises server zijn verbonden met internet. Een IT-beheerder moet het beste pad bepalen dat de server kan bereiken in azure Cloud Services.

In dit artikel vindt u inzicht in specifieke vereisten en opties die beschikbaar zijn voor een geslaagde en veilige verbinding van uw server met Azure File Sync.

We raden u aan om [Aandachtspunten voor Azure File Sync-netwerken](storage-sync-files-networking-overview.md) te lezen voordat u dit artikel verder leest.

## <a name="overview"></a>Overzicht
Azure File Sync fungeert als een Orchestration-service tussen uw Windows-Server, uw Azure-bestands share en verschillende andere Azure-Services om gegevens te synchroniseren zoals beschreven in de synchronisatie groep. Azure File Sync om goed te laten werken, moet u uw servers configureren om te communiceren met de volgende Azure-Services:

- Azure Storage
- Azure File Sync
- Azure Resource Manager
- Verificatie services

> [!Note]  
> De Azure File Sync-agent op Windows server initieert alle aanvragen voor Cloud Services, waardoor het uitgaande verkeer van een firewall perspectief alleen hoeft te worden overwogen. <br /> Geen enkele Azure-service initieert een verbinding met de Azure File Sync-agent.

## <a name="ports"></a>Poorten
Azure File Sync verplaatst bestands gegevens en meta gegevens uitsluitend via HTTPS en vereist dat poort 443 open-uitgaand moet zijn.
Als gevolg hiervan wordt al het verkeer versleuteld.

## <a name="networks-and-special-connections-to-azure"></a>Netwerken en speciale verbindingen met Azure
De Azure File Sync-agent heeft geen vereisten met betrekking tot speciale kanalen zoals [ExpressRoute](../../expressroute/expressroute-introduction.md), etc. naar Azure.

Azure File Sync beschik bare manieren om in azure te kunnen bereiken, automatisch aan verschillende netwerk kenmerken, zoals band breedte, latentie en het bieden van beheer beheer voor het afstemmen. Niet alle functies zijn op dit moment beschikbaar. Als u specifiek gedrag wilt configureren, laat u ons weten via [Azure files UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Proxy
Azure File Sync biedt ondersteuning voor app-specifieke proxy-instellingen voor de hele machine.

Met **app-specifieke proxy-instellingen** kunt u een proxy configureren die specifiek is voor Azure file sync verkeer. App-specifieke proxy-instellingen worden ondersteund op Agent versie 4.0.1.0 of hoger en kunnen worden geconfigureerd tijdens de installatie van de agent of met behulp van de Set-StorageSyncProxyConfiguration Power shell-cmdlet.

PowerShell-opdrachten voor het configureren van app-specifieke proxyinstellingen:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
Als uw proxy server bijvoorbeeld verificatie vereist met een gebruikers naam en wacht woord, voert u de volgende Power shell-opdrachten uit:

```powershell
# IP address or name of the proxy server.
$Address="127.0.0.1"  

# The port to use for the connection to the proxy.
$Port=8080

# The user name for a proxy.
$UserName="user_name" 

# Please type or paste a string with a password for the proxy.
$SecurePassword = Read-Host -AsSecureString

$Creds = New-Object System.Management.Automation.PSCredential ($UserName, $SecurePassword)

# Please verify that you have entered the password correctly.
Write-Host $Creds.GetNetworkCredential().Password

Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"

Set-StorageSyncProxyConfiguration -Address $Address -Port $Port -ProxyCredential $Creds
```
**Proxy-instellingen** voor alle computers zijn transparant voor de Azure file sync-agent, omdat het hele verkeer van de server via de proxy wordt doorgestuurd.

Volg de onderstaande stappen voor het configureren van proxyinstellingen voor een gehele machine: 

1. Proxy-instellingen voor .NET-toepassingen configureren 

   - Bewerk deze twee bestanden:  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Voeg de sectie <system.net> toe aan de machine.config-bestanden (onder het gedeelte <System. service model>).  Wijzig 127.0.01:8888 in het IP-adres en de poort voor de proxy server. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. De WinHTTP-proxy-instellingen instellen 

   - Voer de volgende opdracht uit vanaf een opdracht prompt met verhoogde bevoegdheid of Power shell om de bestaande proxy-instelling te bekijken:   

     Netsh WinHTTP show proxy

   - Voer de volgende opdracht uit vanaf een opdracht prompt met verhoogde bevoegdheid of Power shell om de proxy instelling in te stellen (127.0.01:8888 wijzigen in het IP-adres en de poort voor de proxy server):  

     Netsh WinHTTP set proxy 127.0.0.1:8888

3. Start de opslag synchronisatie Agent-service opnieuw door de volgende opdracht uit te voeren vanaf een opdracht prompt met verhoogde bevoegdheid of Power shell: 

      net stop filesyncsvc

      Opmerking: de opslag synchronisatie agent (filesyncsvc) wordt automatisch gestart zodra de service is gestopt.

## <a name="firewall"></a>Firewall
Zoals vermeld in een vorige sectie, moet poort 443 open-uitgaand zijn. Afhankelijk van het beleid in uw Data Center, vertakking of regio kunt u het verkeer dat via deze poort wordt beperkt tot specifieke domeinen, mogelijk gewenst of vereist.

In de volgende tabel worden de vereiste domeinen voor communicatie beschreven:

| Service | Open bare-Cloud eindpunt | Azure Government-eind punt | Gebruik |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | `https://management.azure.com` | https://management.usgovcloudapi.net | Elke gebruikers aanroep (zoals Power shell) gaat naar/via deze URL, met inbegrip van de eerste registratie aanroep van de server. |
| **Azure Active Directory** | https://login.windows.net<br>`https://login.microsoftonline.com` | https://login.microsoftonline.us | Azure Resource Manager-aanroepen moeten worden uitgevoerd door een geverifieerde gebruiker. Deze URL wordt gebruikt voor gebruikers verificatie. |
| **Azure Active Directory** | https://graph.microsoft.com/ | https://graph.microsoft.com/ | Als onderdeel van de implementatie van Azure File Sync wordt een Service-Principal in de Azure Active Directory van het abonnement gemaakt. Deze URL wordt gebruikt voor die. Deze principal wordt gebruikt voor het delegeren van een minimale set rechten voor de Azure File Sync-Service. De gebruiker die de eerste installatie van Azure File Sync uitvoert, moet een geverifieerde gebruiker zijn met de bevoegdheid eigenaar van het abonnement. |
| **Azure Active Directory** | https://secure.aadcdn.microsoftonline-p.com | Gebruik de URL van het open bare eind punt. | Deze URL wordt geopend door de Active Directory-verificatie bibliotheek die de gebruikers interface voor de Azure File Sync-Server registratie gebruikt om zich aan te melden bij de beheerder. |
| **Azure Storage** | &ast;. core.windows.net | &ast;. core.usgovcloudapi.net | Wanneer een bestand door de server wordt gedownload, voert de server de gegevens efficiënter uit wanneer deze rechtstreeks met de Azure-bestands share in het opslag account communiceren. De server heeft een SAS-sleutel die alleen toegang tot de doel bestands shares toestaat. |
| **Azure File Sync** | &ast;. one.microsoft.com<br>&ast;. afs.azure.net | &ast;. afs.azure.us | Na de eerste registratie van de server ontvangt de server een regionale URL voor het Azure File Sync service-exemplaar in die regio. De-server kan de URL gebruiken om rechtstreeks en efficiënt te communiceren met het exemplaar dat de synchronisatie afhandelt. |
| **Micro soft PKI** |  https://www.microsoft.com/pki/mscorp/cps<br>http://crl.microsoft.com/pki/mscorp/crl/<br>http://mscrl.microsoft.com/pki/mscorp/crl/<br>http://ocsp.msocsp.com<br>http://ocsp.digicert.com/<br>http://crl3.digicert.com/ | https://www.microsoft.com/pki/mscorp/cps<br>http://crl.microsoft.com/pki/mscorp/crl/<br>http://mscrl.microsoft.com/pki/mscorp/crl/<br>http://ocsp.msocsp.com<br>http://ocsp.digicert.com/<br>http://crl3.digicert.com/ | Zodra de Azure File Sync-agent is geïnstalleerd, wordt de PKI-URL gebruikt voor het downloaden van tussenliggende certificaten die nodig zijn om te communiceren met de Azure File Sync-Service en de Azure-bestands share. De OCSP-URL wordt gebruikt om de status van een certificaat te controleren. |
| **Microsoft Update** | &ast;.update.microsoft.com<br>&ast;.download.windowsupdate.com<br>&ast;. ctldl.windowsupdate.com<br>&ast;.dl.delivery.mp.microsoft.com<br>&ast;.emdl.ws.microsoft.com | &ast;.update.microsoft.com<br>&ast;.download.windowsupdate.com<br>&ast;. ctldl.windowsupdate.com<br>&ast;.dl.delivery.mp.microsoft.com<br>&ast;.emdl.ws.microsoft.com | Zodra de Azure File Sync-agent is geïnstalleerd, worden de Microsoft Update Url's gebruikt om Azure File Sync agent-updates te downloaden. |

> [!Important]
> Bij het toestaan van verkeer naar &ast; . AFS.Azure.net is verkeer alleen mogelijk voor de synchronisatie service. Er zijn geen andere micro soft-services die gebruikmaken van dit domein.
> Bij het toestaan van verkeer naar &ast; One.Microsoft.com, is verkeer naar meer dan alleen de synchronisatie service mogelijk van de server. Er zijn veel meer micro soft-services beschikbaar onder subdomeinen.

Als &ast; . AFS.Azure.net of &ast; . One.Microsoft.com te breed is, kunt u de communicatie van de server beperken door communicatie toe te staan alleen expliciete regionale instanties van de Azure files-synchronisatie service. Welk exemplaar (en) u kiest, is afhankelijk van de regio van de opslag synchronisatie service die u hebt geïmplementeerd en de server hebt geregistreerd bij. Deze regio wordt ' primaire eind punt-URL ' genoemd in de volgende tabel.

Voor bedrijfs continuïteit en herstel na nood gevallen (BCDR) kunt u uw Azure-bestands shares opgeven in een globaal redundante (GRS)-opslag account. Als dat het geval is, zal de Azure-bestands shares een failover uitvoeren naar de gekoppelde regio in het geval van een blijvende regionale storing. Azure File Sync gebruikt dezelfde regionale koppeling als opslag. Als u dus GRS-opslag accounts gebruikt, moet u extra Url's inschakelen zodat uw server kan communiceren met de gekoppelde regio voor Azure File Sync. In de volgende tabel wordt deze ' gekoppelde regio ' aangeroepen. Daarnaast is er een Traffic Manager-profiel-URL die ook moet worden ingeschakeld. Zo zorgt u ervoor dat netwerk verkeer naadloos opnieuw kan worden gerouteerd naar de gekoppelde regio in het geval van een failover en wordt "detectie-URL" genoemd in de onderstaande tabel.

| Cloud  | Region | URL van het primaire eind punt | Gekoppelde regio | Detectie-URL |
|--------|--------|----------------------|---------------|---------------|
| Openbaar |Australië - oost | https: \/ /australiaeast01.AFS.Azure.net<br>https: \/ /kailani-Aue.One.Microsoft.com | Australië - zuidoost | https: \/ /TM-australiaeast01.AFS.Azure.net<br>https: \/ /TM-kailani-Aue.One.Microsoft.com |
| Openbaar |Australië - zuidoost | https: \/ /australiasoutheast01.AFS.Azure.net<br>https: \/ /kailani-aus.One.Microsoft.com | Australië - oost | https: \/ /TM-australiasoutheast01.AFS.Azure.net<br>https: \/ /TM-kailani-aus.One.Microsoft.com |
| Openbaar | Brazilië - zuid | https: \/ /brazilsouth01.AFS.Azure.net | VS - zuid-centraal | https: \/ /TM-brazilsouth01.AFS.Azure.net |
| Openbaar | Canada - midden | https: \/ /canadacentral01.AFS.Azure.net<br>https: \/ /kailani-CAC.One.Microsoft.com | Canada - oost | https: \/ /TM-canadacentral01.AFS.Azure.net<br>https: \/ /TM-kailani-CAC.One.Microsoft.com |
| Openbaar | Canada - oost | https: \/ /canadaeast01.AFS.Azure.net<br>https: \/ /kailani-CAE.One.Microsoft.com | Canada - midden | https: \/ /TM-canadaeast01.AFS.Azure.net<br>https: \/ /TM-kailani.CAE.One.Microsoft.com |
| Openbaar | India - centraal | https: \/ /centralindia01.AFS.Azure.net<br>https: \/ /kailani-cin.One.Microsoft.com | India - zuid | https: \/ /TM-centralindia01.AFS.Azure.net<br>https: \/ /TM-kailani-cin.One.Microsoft.com |
| Openbaar | VS - centraal | https: \/ /centralus01.AFS.Azure.net<br>https: \/ /kailani-cus.One.Microsoft.com | VS - oost 2 | https: \/ /TM-centralus01.AFS.Azure.net<br>https: \/ /TM-kailani-cus.One.Microsoft.com |
| Openbaar | Azië - oost | https: \/ /eastasia01.AFS.Azure.net<br>https: \/ /kailani11.One.Microsoft.com | Azië - zuidoost | https: \/ /TM-eastasia01.AFS.Azure.net<br>https: \/ /TM-kailani11.One.Microsoft.com |
| Openbaar | VS - oost | https: \/ /eastus01.AFS.Azure.net<br>https: \/ /kailani1.One.Microsoft.com | VS - west | https: \/ /TM-eastus01.AFS.Azure.net<br>https: \/ /TM-kailani1.One.Microsoft.com |
| Openbaar | VS - oost 2 | https: \/ /eastus201.AFS.Azure.net<br>https: \/ /kailani-ESS.One.Microsoft.com | VS - centraal | https: \/ /TM-eastus201.AFS.Azure.net<br>https: \/ /TM-kailani-ESS.One.Microsoft.com |
| Openbaar | Duitsland - noord | https: \/ /germanynorth01.AFS.Azure.net | Duitsland - west-centraal | https: \/ /TM-germanywestcentral01.AFS.Azure.net |
| Openbaar | Duitsland - west-centraal | https: \/ /germanywestcentral01.AFS.Azure.net | Duitsland - noord | https: \/ /TM-germanynorth01.AFS.Azure.net |
| Openbaar | Japan - oost | https: \/ /japaneast01.AFS.Azure.net | Japan - west | https: \/ /TM-japaneast01.AFS.Azure.net |
| Openbaar | Japan - west | https: \/ /japanwest01.AFS.Azure.net | Japan - oost | https: \/ /TM-japanwest01.AFS.Azure.net |
| Openbaar | Korea - centraal | https: \/ /koreacentral01.AFS.Azure.net/ | Korea - zuid | https: \/ /TM-koreacentral01.AFS.Azure.net/ |
| Openbaar | Korea - zuid | https: \/ /koreasouth01.AFS.Azure.net/ | Korea - centraal | https: \/ /TM-koreasouth01.AFS.Azure.net/ |
| Openbaar | VS - noord-centraal | https: \/ /northcentralus01.AFS.Azure.net | VS - zuid-centraal | https: \/ /TM-northcentralus01.AFS.Azure.net |
| Openbaar | Europa - noord | https: \/ /northeurope01.AFS.Azure.net<br>https: \/ /kailani7.One.Microsoft.com | Europa -west | https: \/ /TM-northeurope01.AFS.Azure.net<br>https: \/ /TM-kailani7.One.Microsoft.com |
| Openbaar | VS - zuid-centraal | https: \/ /southcentralus01.AFS.Azure.net | VS - noord-centraal | https: \/ /TM-southcentralus01.AFS.Azure.net |
| Openbaar | India - zuid | https: \/ /southindia01.AFS.Azure.net<br>https: \/ /kailani-Sin.One.Microsoft.com | India - centraal | https: \/ /TM-southindia01.AFS.Azure.net<br>https: \/ /TM-kailani-Sin.One.Microsoft.com |
| Openbaar | Azië - zuidoost | https: \/ /southeastasia01.AFS.Azure.net<br>https: \/ /kailani10.One.Microsoft.com | Azië - oost | https: \/ /TM-southeastasia01.AFS.Azure.net<br>https: \/ /TM-kailani10.One.Microsoft.com |
| Openbaar | Zwitserland - noord | https: \/ /switzerlandnorth01.AFS.Azure.net<br>https: \/ /TM-switzerlandnorth01.AFS.Azure.net | Zwitserland - west | https: \/ /switzerlandwest01.AFS.Azure.net<br>https: \/ /TM-switzerlandwest01.AFS.Azure.net |
| Openbaar | Zwitserland - west | https: \/ /switzerlandwest01.AFS.Azure.net<br>https: \/ /TM-switzerlandwest01.AFS.Azure.net | Zwitserland - noord | https: \/ /switzerlandnorth01.AFS.Azure.net<br>https: \/ /TM-switzerlandnorth01.AFS.Azure.net |
| Openbaar | Verenigd Koninkrijk Zuid | https: \/ /uksouth01.AFS.Azure.net<br>https: \/ /kailani-UKs.One.Microsoft.com | Verenigd Koninkrijk West | https: \/ /TM-uksouth01.AFS.Azure.net<br>https: \/ /TM-kailani-UKs.One.Microsoft.com |
| Openbaar | Verenigd Koninkrijk West | https: \/ /ukwest01.AFS.Azure.net<br>https: \/ /kailani-UKW.One.Microsoft.com | Verenigd Koninkrijk Zuid | https: \/ /TM-ukwest01.AFS.Azure.net<br>https: \/ /TM-kailani-UKW.One.Microsoft.com |
| Openbaar | VS - west-centraal | https: \/ /westcentralus01.AFS.Azure.net | VS - west 2 | https: \/ /TM-westcentralus01.AFS.Azure.net |
| Openbaar | Europa -west | https: \/ /westeurope01.AFS.Azure.net<br>https: \/ /kailani6.One.Microsoft.com | Europa - noord | https: \/ /TM-westeurope01.AFS.Azure.net<br>https: \/ /TM-kailani6.One.Microsoft.com |
| Openbaar | VS - west | https: \/ /westus01.AFS.Azure.net<br>https: \/ /kailani.One.Microsoft.com | VS - oost | https: \/ /TM-westus01.AFS.Azure.net<br>https: \/ /TM-kailani.One.Microsoft.com |
| Openbaar | VS - west 2 | https: \/ /westus201.AFS.Azure.net | VS - west-centraal | https: \/ /TM-westus201.AFS.Azure.net |
| Overheid | VS (overheid) - Arizona | https: \/ /usgovarizona01.AFS.Azure.us | VS (overheid) - Texas | https: \/ /TM-usgovarizona01.AFS.Azure.us |
| Overheid | VS (overheid) - Texas | https: \/ /usgovtexas01.AFS.Azure.us | VS (overheid) - Arizona | https: \/ /TM-usgovtexas01.AFS.Azure.us |

- Als u lokaal redundante (LRS) of zone redundante (ZRS) opslag accounts gebruikt, hoeft u alleen de URL in te scha kelen die wordt weer gegeven onder URL van het primaire eind punt.

- Als u wereld wijd redundante opslag accounts (GRS) gebruikt, schakelt u drie Url's in.

**Voor beeld:** U implementeert een opslag synchronisatie service in `"West US"` en registreert deze bij de server. De Url's waarmee de server kan communiceren voor deze aanvraag:

> - https: \/ /westus01.AFS.Azure.net (primair eind punt: VS-West)
> - https: \/ /eastus01.AFS.Azure.net (gekoppelde failover-over regio: VS-Oost)
> - https: \/ /TM-westus01.AFS.Azure.net (detectie-URL van de primaire regio)

### <a name="allow-list-for-azure-file-sync-ip-addresses"></a>Acceptatie lijst voor Azure File Sync IP-adressen
Azure File Sync ondersteunt het gebruik van [service Tags](../../virtual-network/service-tags-overview.md)die een groep IP-adres voorvoegsels voor een bepaalde Azure-service vertegenwoordigen. U kunt service tags gebruiken om firewall regels te maken waarmee communicatie met de Azure File Sync-Service mogelijk wordt gemaakt. Het servicetag voor Azure File Sync is `StorageSyncService` .

Als u Azure File Sync binnen Azure gebruikt, kunt u de naam van het service label rechtstreeks in uw netwerk beveiligings groep gebruiken om verkeer toe te staan. Zie [Netwerkbeveiligingsgroepen](../../virtual-network/network-security-groups-overview.md) voor meer informatie over hoe u dit doet.

Als u Azure File Sync on-premises gebruikt, kunt u de servicetag-API gebruiken om specifieke IP-adresbereiken op te halen voor de lijst met toegestane IP-adressen van uw firewall. Er zijn twee manieren om deze informatie op te halen:

- De huidige lijst met IP-adresbereiken voor alle Azure-services die servicetags ondersteunen, wordt wekelijks in het Microsoft Downloadcentrum gepubliceerd in de vorm van een JSON-document. Elke Azure-cloud heeft zijn eigen JSON-document met de IP-adresbereiken die relevant zijn voor die cloud:
    - [Azure openbaar](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure van de Amerikaanse overheid](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure China](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure Duitsland](https://www.microsoft.com/download/details.aspx?id=57064)
- Met de API voor servicetagdetectie (preview-versie) kunt u de huidige lijst met servicetags programmatisch ophalen. In de preview-versie kan de API voor servicetagdetectie informatie retourneren die minder actueel is dan informatie die wordt geretourneerd uit de JSON-documenten die in het Microsoft Downloadcentrum worden gepubliceerd. U kunt het API-gebied gebruiken op basis van uw automatiseringsvoorkeur:
    - [REST API](/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Azure-CLI](/cli/azure/network#az-network-list-service-tags)

Omdat de service Tags detectie-API niet zo vaak wordt bijgewerkt als de JSON-documenten die zijn gepubliceerd naar het micro soft Download centrum, raden we u aan het JSON-document te gebruiken om de acceptatie lijst van uw on-premises Firewall bij te werken. Dit kan als volgt worden gedaan:

```PowerShell
# The specific region to get the IP address ranges for. Replace westus2 with the desired region code 
# from Get-AzLocation.
$region = "westus2"

# The service tag for Azure File Sync. Do not change unless you're adapting this
# script for another service.
$serviceTag = "StorageSyncService"

# Download date is the string matching the JSON document on the Download Center. 
$possibleDownloadDates = 0..7 | `
    ForEach-Object { [System.DateTime]::Now.AddDays($_ * -1).ToString("yyyyMMdd") }

# Verify the provided region
$validRegions = Get-AzLocation | `
    Where-Object { $_.Providers -contains "Microsoft.StorageSync" } | `
    Select-Object -ExpandProperty Location

if ($validRegions -notcontains $region) {
    Write-Error `
            -Message "The specified region $region is not available. Either Azure File Sync is not deployed there or the region does not exist." `
            -ErrorAction Stop
}

# Get the Azure cloud. This should automatically based on the context of 
# your Az PowerShell login, however if you manually need to populate, you can find
# the correct values using Get-AzEnvironment.
$azureCloud = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty Name

# Build the download URI
$downloadUris = @()
switch($azureCloud) {
    "AzureCloud" { 
        $downloadUris = $possibleDownloadDates | ForEach-Object {  
            "https://download.microsoft.com/download/7/1/D/71D86715-5596-4529-9B13-DA13A5DE5B63/ServiceTags_Public_$_.json"
        }
    }

    "AzureUSGovernment" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/6/4/D/64DB03BF-895B-4173-A8B1-BA4AD5D4DF22/ServiceTags_AzureGovernment_$_.json"
        }
    }

    "AzureChinaCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/9/D/0/9D03B7E2-4B80-4BF3-9B91-DA8C7D3EE9F9/ServiceTags_China_$_.json"
        }
    }

    "AzureGermanCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/0/7/6/076274AB-4B0B-4246-A422-4BAF1E03F974/ServiceTags_AzureGermany_$_.json"
        }
    }

    default {
        Write-Error -Message "Unrecognized Azure Cloud: $_" -ErrorAction Stop
    }
}

# Find most recent file
$found = $false 
foreach($downloadUri in $downloadUris) {
    try { $response = Invoke-WebRequest -Uri $downloadUri -UseBasicParsing } catch { }
    if ($response.StatusCode -eq 200) {
        $found = $true
        break
    }
}

if ($found) {
    # Get the raw JSON 
    $content = [System.Text.Encoding]::UTF8.GetString($response.Content)

    # Parse the JSON
    $serviceTags = ConvertFrom-Json -InputObject $content -Depth 100

    # Get the specific $ipAddressRanges
    $ipAddressRanges = $serviceTags | `
        Select-Object -ExpandProperty values | `
        Where-Object { $_.id -eq "$serviceTag.$region" } | `
        Select-Object -ExpandProperty properties | `
        Select-Object -ExpandProperty addressPrefixes
} else {
    # If the file cannot be found, that means there hasn't been an update in
    # more than a week. Please verify the download URIs are still accurate
    # by checking https://docs.microsoft.com/azure/virtual-network/service-tags-overview
    Write-Verbose -Message "JSON service tag file not found."
    return
}
```

Vervolgens kunt u de IP-adresbereiken gebruiken in `$ipAddressRanges` om uw firewall bij te werken. Raadpleeg de website van uw firewall/netwerk apparaat voor informatie over het bijwerken van uw firewall.

## <a name="test-network-connectivity-to-service-endpoints"></a>De netwerk verbinding met Service-eind punten testen
Zodra een server is geregistreerd bij de Azure File Sync-Service, kunnen de Test-StorageSyncNetworkConnectivity-cmdlet en ServerRegistration.exe worden gebruikt om de communicatie met alle eind punten (Url's) die voor deze server gelden, te testen. Met deze cmdlet kunt u problemen oplossen wanneer de server niet volledig met Azure File Sync werkt en kan worden gebruikt voor het afstemmen van proxy-en firewall configuraties.

Als u de test voor de netwerk verbinding wilt uitvoeren, installeert u Azure File Sync agent versie 9,1 of hoger en voert u de volgende Power shell-opdrachten uit:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Test-StorageSyncNetworkConnectivity
```

## <a name="summary-and-risk-limitation"></a>Samen vatting en risico beperking
De lijsten eerder in dit document bevatten de Url's Azure File Sync momenteel communiceert met. Firewalls moeten uitgaand verkeer naar deze domeinen kunnen toestaan. Micro soft streeft ernaar om deze lijst bijgewerkt te laten worden.

Het instellen van domein beperking van firewall regels kan een maat eenheid zijn voor het verbeteren van de beveiliging. Als deze firewall configuraties worden gebruikt, moet er een of meer Url's worden toegevoegd. Dit kan zelfs na verloop van tijd worden gewijzigd. Controleer dit artikel regel matig.

## <a name="next-steps"></a>Volgende stappen
- [Planning voor een Azure Files Sync-implementatie](storage-sync-files-planning.md)
- [Azure Files SYNC implementeren](storage-sync-files-deployment-guide.md)
- [Azure File Sync bewaken](storage-sync-files-monitoring.md)
