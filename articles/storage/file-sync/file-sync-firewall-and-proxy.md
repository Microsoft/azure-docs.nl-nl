---
title: Azure File Sync on-premises firewall en proxy-instellingen | Microsoft Docs
description: Meer Azure File Sync on-premises proxy- en firewallinstellingen. Bekijk configuratiedetails voor poorten, netwerken en speciale verbindingen met Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: b2c77c20bfb6fff60f2242d1ac2dad7b3fc9f6fe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796712"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Proxy- en firewallinstellingen van Azure File Sync
Azure File Sync uw on-premises servers met Azure Files, waardoor synchronisatie van meerdere locaties en functies voor cloudopslaglagen mogelijk zijn. Als zodanig moet een on-premises server zijn verbonden met internet. Een IT-beheerder moet bepalen wat het beste pad is voor de server om azure-cloudservices te bereiken.

Dit artikel biedt inzicht in specifieke vereisten en opties die beschikbaar zijn voor een succesvolle en veilige verbinding tussen uw server en Azure File Sync.

We raden u aan om [Aandachtspunten voor Azure File Sync-netwerken](file-sync-networking-overview.md) te lezen voordat u dit artikel verder leest.

## <a name="overview"></a>Overzicht
Azure File Sync fungeert als een orchestration-service tussen uw Windows Server, uw Azure-bestands share en verschillende andere Azure-services om gegevens te synchroniseren, zoals beschreven in uw synchronisatiegroep. Om Azure File Sync goed te laten werken, moet u uw servers configureren om te communiceren met de volgende Azure-services:

- Azure Storage
- Azure File Sync
- Azure Resource Manager
- Verificatieservices

> [!Note]  
> De Azure File Sync-agent op Windows Server initieert alle aanvragen voor cloudservices, waardoor alleen uitgaand verkeer vanuit het perspectief van de firewall hoeft te worden bekeken. <br /> Geen enkele Azure-service initieert een verbinding met de Azure File Sync agent.

## <a name="ports"></a>Poorten
Azure File Sync verplaatst bestandsgegevens en metagegevens uitsluitend via HTTPS en vereist dat poort 443 uitgaand is geopend.
Als gevolg hiervan wordt al het verkeer versleuteld.

## <a name="networks-and-special-connections-to-azure"></a>Netwerken en speciale verbindingen met Azure
De Azure File Sync-agent heeft geen vereisten met betrekking tot speciale kanalen, [zoals ExpressRoute,](../../expressroute/expressroute-introduction.md)enzovoort naar Azure.

Azure File Sync werkt via alle beschikbare middelen waarmee Azure kan worden bereikt, automatisch wordt aangepast aan verschillende netwerkkenmerken, zoals bandbreedte, latentie en beheer voor afstemming. Op dit moment zijn niet alle functies beschikbaar. Als u specifiek gedrag wilt configureren, laat het ons dan weten [via Azure Files UserVoice.](https://feedback.azure.com/forums/217298-storage?category_id=180670)

## <a name="proxy"></a>Proxy
Azure File Sync biedt ondersteuning voor app-specifieke en machinebrede proxy-instellingen.

**Met app-specifieke proxyinstellingen** kunt u een proxy configureren voor Azure File Sync verkeer. App-specifieke proxy-instellingen worden ondersteund op agentversie 4.0.1.0 of hoger en kunnen worden geconfigureerd tijdens de installatie van de agent of met behulp van de Set-StorageSyncProxyConfiguration PowerShell-cmdlet.

PowerShell-opdrachten voor het configureren van app-specifieke proxyinstellingen:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
Als uw proxyserver bijvoorbeeld verificatie met een gebruikersnaam en wachtwoord vereist, voert u de volgende PowerShell-opdrachten uit:

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
**Proxy-instellingen voor de hele machine** zijn transparant voor Azure File Sync agent, omdat het hele verkeer van de server via de proxy wordt gerouteerd.

Volg de onderstaande stappen voor het configureren van proxyinstellingen voor een gehele machine: 

1. Proxyinstellingen configureren voor .NET-toepassingen 

   - Bewerk deze twee bestanden:  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Voeg de sectie <system.net> toe aan de machine.config bestanden (onder de sectie <system.serviceModel>).  Wijzig 127.0.01:8888 in het IP-adres en de poort voor de proxyserver. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. De WinHTTP-proxyinstellingen instellen 

   > [!Note]  
   > Er zijn verschillende methoden (WPAD, PAC-bestand, netsh, enzovoort) om een Windows-server te configureren voor het gebruik van een proxyserver. In de onderstaande stappen wordt beschreven hoe u de proxyinstellingen configureert met behulp van netsh, maar elke methode die wordt vermeld in de documentatie [Proxyserverinstellingen configureren in Windows](https://docs.microsoft.com/troubleshoot/windows-server/networking/configure-proxy-server-settings) wordt ondersteund.


   - Voer de volgende opdracht uit vanaf een opdrachtprompt met verhoogde bevoegdheid of PowerShell om de bestaande proxy-instelling te zien:   

     netsh winhttp show proxy

   - Voer de volgende opdracht uit vanaf een opdrachtprompt met verhoogde bevoegdheid of PowerShell om de proxy-instelling in te stellen (wijzig 127.0.01:8888 in het IP-adres en de poort voor de proxyserver):  

     netsh winhttp set proxy 127.0.0.1:8888

3. Start de Storage Sync Agent-service opnieuw door de volgende opdracht uit te voeren vanaf een opdrachtprompt met verhoogde bevoegdheid of PowerShell: 

      net stop filesyncsvc

      Opmerking: de opslagsynchronisatieagentservice (filesyncsvc) wordt automatisch aan de slag als deze is gestopt.

## <a name="firewall"></a>Firewall
Zoals vermeld in een vorige sectie, moet poort 443 uitgaand zijn geopend. Op basis van beleidsregels in uw datacenter, vertakking of regio kan verdere beperking van verkeer via deze poort tot specifieke domeinen gewenst of vereist zijn.

In de volgende tabel worden de vereiste domeinen voor communicatie beschreven:

| Service | Eindpunt van openbare cloud | Azure Government eindpunt | Gebruik |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | `https://management.azure.com` | https://management.usgovcloudapi.net | Elke aanroep van een gebruiker (zoals PowerShell) gaat naar/via deze URL, inclusief de eerste aanroep voor serverregistratie. |
| **Azure Active Directory** | https://login.windows.net<br>`https://login.microsoftonline.com` | https://login.microsoftonline.us | Azure Resource Manager-aanroepen moeten worden gedaan door een geverifieerde gebruiker. Als u wilt slagen, wordt deze URL gebruikt voor gebruikersverificatie. |
| **Azure Active Directory** | https://graph.microsoft.com/ | https://graph.microsoft.com/ | Als onderdeel van het implementeren Azure File Sync, wordt er een service-principal in de Azure Active Directory van het abonnement gemaakt. Deze URL wordt hiervoor gebruikt. Deze principal wordt gebruikt voor het delegeren van een minimale set rechten aan de Azure File Sync service. De gebruiker die de initiële installatie van de Azure File Sync moet een geverifieerde gebruiker met eigenaarsbevoegdheden voor het abonnement zijn. |
| **Azure Active Directory** | https://secure.aadcdn.microsoftonline-p.com | Gebruik de URL van het openbare eindpunt. | Deze URL wordt gebruikt door de Active Directory-verificatiebibliotheek die de Azure File Sync voor serverregistratie gebruikt om de beheerder aan te melden. |
| **Azure Storage** | &ast;.core.windows.net | &ast;.core.usgovcloudapi.net | Wanneer de server een bestand downloadt, voert de server die gegevensverkeer efficiënter uit wanneer rechtstreeks met de Azure-bestands share in het opslagaccount wordt gesproken. De server heeft een SAS-sleutel die alleen toegang tot de doelbestands share toestaat. |
| **Azure File Sync** | &ast;.one.microsoft.com<br>&ast;.afs.azure.net | &ast;.afs.azure.us | Na de eerste serverregistratie ontvangt de server een regionale URL voor het Azure File Sync service-exemplaar in die regio. De server kan de URL gebruiken om rechtstreeks en efficiënt te communiceren met het exemplaar dat de synchronisatie afhandelt. |
| **Microsoft PKI** |  https://www.microsoft.com/pki/mscorp/cps<br>http://crl.microsoft.com/pki/mscorp/crl/<br>http://mscrl.microsoft.com/pki/mscorp/crl/<br>http://ocsp.msocsp.com<br>http://ocsp.digicert.com/<br>http://crl3.digicert.com/ | https://www.microsoft.com/pki/mscorp/cps<br>http://crl.microsoft.com/pki/mscorp/crl/<br>http://mscrl.microsoft.com/pki/mscorp/crl/<br>http://ocsp.msocsp.com<br>http://ocsp.digicert.com/<br>http://crl3.digicert.com/ | Zodra de Azure File Sync agent is geïnstalleerd, wordt de PKI-URL gebruikt om tussenliggende certificaten te downloaden die nodig zijn om te communiceren met de Azure File Sync-service en De Azure-bestands share. De OCSP-URL wordt gebruikt om de status van een certificaat te controleren. |
| **Microsoft Update** | &ast;.update.microsoft.com<br>&ast;.download.windowsupdate.com<br>&ast;.ctldl.windowsupdate.com<br>&ast;.dl.delivery.mp.microsoft.com<br>&ast;.emdl.ws.microsoft.com | &ast;.update.microsoft.com<br>&ast;.download.windowsupdate.com<br>&ast;.ctldl.windowsupdate.com<br>&ast;.dl.delivery.mp.microsoft.com<br>&ast;.emdl.ws.microsoft.com | Zodra de Azure File Sync agent is geïnstalleerd, worden de Microsoft Update-URL's gebruikt om de agentupdates Azure File Sync downloaden. |

> [!Important]
> Wanneer u verkeer naar &ast; .afs.azure.net toestaat, is verkeer naar de synchronisatieservice alleen mogelijk. Er zijn geen andere Microsoft-services dit domein te gebruiken.
> Wanneer verkeer naar .one.microsoft.com, is verkeer naar meer dan alleen de &ast; synchronisatieservice mogelijk vanaf de server. Er zijn veel meer Microsoft-services beschikbaar onder subdomeinen.

Als .afs.azure.net of .one.microsoft.com te breed is, kunt u de communicatie van de server beperken door communicatie toe te staan tot alleen expliciete regionale exemplaren van de &ast; &ast; Azure Files Sync-service. Welke instantie(s) u moet kiezen, is afhankelijk van de regio van de opslagsynchronisatieservice waarvoor u de server hebt geïmplementeerd en geregistreerd. Deze regio wordt 'URL van primair eindpunt' genoemd in de onderstaande tabel.

Om redenen van bedrijfscontinuïteit en herstel na noodherstel (BCDR) hebt u uw Azure-bestands shares mogelijk opgegeven in een wereldwijd redundant (GRS)-opslagaccount. Als dat het geval is, wordt voor uw Azure-bestands shares een fail overgeslagen naar de gekoppelde regio in het geval van een langdurige regionale storing. Azure File Sync maakt gebruik van dezelfde regionale koppeling als opslag. Dus als u GRS-opslagaccounts gebruikt, moet u extra URL's inschakelen zodat uw server kan praten met de gekoppelde regio voor Azure File Sync. In de onderstaande tabel wordt deze 'gekoppelde regio' genoemd. Daarnaast is er een Traffic Manager-profiel-URL die ook moet worden ingeschakeld. Dit zorgt ervoor dat netwerkverkeer naadloos kan worden gerouteerd naar de gekoppelde regio in het geval van een failover en in de onderstaande tabel detectie-URL wordt genoemd.

| Cloud  | Region | URL van primair eindpunt | Gekoppelde regio | Detectie-URL |
|--------|--------|----------------------|---------------|---------------|
| Openbaar |Australië - oost | https: \/ /australiaeast01.afs.azure.net<br>https: \/ /kailani-aue.one.microsoft.com | Australië - zuidoost | https: \/ /tm-australiaeast01.afs.azure.net<br>https: \/ /tm-kailani-aue.one.microsoft.com |
| Openbaar |Australië - zuidoost | https: \/ /australiasoutheast01.afs.azure.net<br>https: \/ /kailani-aus.one.microsoft.com | Australië - oost | https: \/ /tm-australiasoutheast01.afs.azure.net<br>https: \/ /tm-kailani-aus.one.microsoft.com |
| Openbaar | Brazilië - zuid | https: \/ /brazilsouth01.afs.azure.net | VS - zuid-centraal | https: \/ /tm-brazilsouth01.afs.azure.net |
| Openbaar | Canada - midden | https: \/ /canadacentral01.afs.azure.net<br>https: \/ /kailani-cac.one.microsoft.com | Canada - oost | https: \/ /tm-canadacentral01.afs.azure.net<br>https: \/ /tm-kailani-cac.one.microsoft.com |
| Openbaar | Canada - oost | https: \/ /canadaeast01.afs.azure.net<br>https: \/ /kailani-cae.one.microsoft.com | Canada - midden | https: \/ /tm-canadaeast01.afs.azure.net<br>https: \/ /tm-kailani.cae.one.microsoft.com |
| Openbaar | India - centraal | https: \/ /centralindia01.afs.azure.net<br>https: \/ /kailani-cin.one.microsoft.com | India - zuid | https: \/ /tm-centralindia01.afs.azure.net<br>https: \/ /tm-kailani-cin.one.microsoft.com |
| Openbaar | Central US | https: \/ /centralus01.afs.azure.net<br>https: \/ /kailani-cus.one.microsoft.com | VS - oost 2 | https: \/ /tm-centralus01.afs.azure.net<br>https: \/ /tm-kailani-cus.one.microsoft.com |
| Openbaar | Azië - oost | https: \/ /eastasia01.afs.azure.net<br>https: \/ /kailani11.one.microsoft.com | Azië - zuidoost | https: \/ /tm-eastasia01.afs.azure.net<br>https: \/ /tm-kailani11.one.microsoft.com |
| Openbaar | VS - oost | https: \/ /eastus01.afs.azure.net<br>https: \/ /kailani1.one.microsoft.com | VS - west | https: \/ /tm-eastus01.afs.azure.net<br>https: \/ /tm-kailani1.one.microsoft.com |
| Openbaar | VS - oost 2 | https: \/ /eastus201.afs.azure.net<br>https: \/ /kailani-ess.one.microsoft.com | Central US | https: \/ /tm-eastus201.afs.azure.net<br>https: \/ /tm-kailani-ess.one.microsoft.com |
| Openbaar | Duitsland - noord | https: \/ /germanynorth01.afs.azure.net | Duitsland - west-centraal | https: \/ /tm-germanywestcentral01.afs.azure.net |
| Openbaar | Duitsland - west-centraal | https: \/ /germanywestcentral01.afs.azure.net | Duitsland - noord | https: \/ /tm-germanynorth01.afs.azure.net |
| Openbaar | Japan - oost | https: \/ /japaneast01.afs.azure.net | Japan - west | https: \/ /tm-japaneast01.afs.azure.net |
| Openbaar | Japan - west | https: \/ /japanwest01.afs.azure.net | Japan - oost | https: \/ /tm-japanwest01.afs.azure.net |
| Openbaar | Korea - centraal | https: \/ /koreacentral01.afs.azure.net/ | Korea - zuid | https: \/ /tm-koreacentral01.afs.azure.net/ |
| Openbaar | Korea - zuid | https: \/ /koreasouth01.afs.azure.net/ | Korea - centraal | https: \/ /tm-koreasouth01.afs.azure.net/ |
| Openbaar | VS - noord-centraal | https: \/ /northcentralus01.afs.azure.net | VS - zuid-centraal | https: \/ /tm-northcentralus01.afs.azure.net |
| Openbaar | Europa - noord | https: \/ /northeurope01.afs.azure.net<br>https: \/ /kailani7.one.microsoft.com | Europa -west | https: \/ /tm-northeurope01.afs.azure.net<br>https: \/ /tm-kailani7.one.microsoft.com |
| Openbaar | VS - zuid-centraal | https: \/ /southcentralus01.afs.azure.net | VS - noord-centraal | https: \/ /tm-southcentralus01.afs.azure.net |
| Openbaar | India - zuid | https: \/ /southindia01.afs.azure.net<br>https: \/ /kailani-sin.one.microsoft.com | India - centraal | https: \/ /tm-southindia01.afs.azure.net<br>https: \/ /tm-kailani-sin.one.microsoft.com |
| Openbaar | Azië - zuidoost | https: \/ /southeastasia01.afs.azure.net<br>https: \/ /kailani10.one.microsoft.com | Azië - oost | https: \/ /tm-southeastasia01.afs.azure.net<br>https: \/ /tm-kailani10.one.microsoft.com |
| Openbaar | Zwitserland - noord | https: \/ /switzerlandnorth01.afs.azure.net<br>https: \/ /tm-switzerlandnorth01.afs.azure.net | Zwitserland - west | https: \/ /switzerlandwest01.afs.azure.net<br>https: \/ /tm-switzerlandwest01.afs.azure.net |
| Openbaar | Zwitserland - west | https: \/ /switzerlandwest01.afs.azure.net<br>https: \/ /tm-switzerlandwest01.afs.azure.net | Zwitserland - noord | https: \/ /switzerlandnorth01.afs.azure.net<br>https: \/ /tm-switzerlandnorth01.afs.azure.net |
| Openbaar | Verenigd Koninkrijk Zuid | https: \/ /uksouth01.afs.azure.net<br>https: \/ /kailani-uks.one.microsoft.com | Verenigd Koninkrijk West | https: \/ /tm-uksouth01.afs.azure.net<br>https: \/ /tm-kailani-uks.one.microsoft.com |
| Openbaar | Verenigd Koninkrijk West | https: \/ /ukwest01.afs.azure.net<br>https: \/ /kailani-ukw.one.microsoft.com | Verenigd Koninkrijk Zuid | https: \/ /tm-ukwest01.afs.azure.net<br>https: \/ /tm-kailani-ukw.one.microsoft.com |
| Openbaar | VS - west-centraal | https: \/ /westcentralus01.afs.azure.net | VS - west 2 | https: \/ /tm-westcentralus01.afs.azure.net |
| Openbaar | Europa -west | https: \/ /westeurope01.afs.azure.net<br>https: \/ /kailani6.one.microsoft.com | Europa - noord | https: \/ /tm-westeurope01.afs.azure.net<br>https: \/ /tm-kailani6.one.microsoft.com |
| Openbaar | VS - west | https: \/ /westus01.afs.azure.net<br>https: \/ /kailani.one.microsoft.com | VS - oost | https: \/ /tm-westus01.afs.azure.net<br>https: \/ /tm-kailani.one.microsoft.com |
| Openbaar | VS - west 2 | https: \/ /westus201.afs.azure.net | VS - west-centraal | https: \/ /tm-westus201.afs.azure.net |
| Overheid | VS (overheid) - Arizona | https: \/ /usgovarizona01.afs.azure.us | VS (overheid) - Texas | https: \/ /tm-usgovarizona01.afs.azure.us |
| Overheid | VS (overheid) - Texas | https: \/ /usgovtexas01.afs.azure.us | VS (overheid) - Arizona | https: \/ /tm-usgovtexas01.afs.azure.us |

- Als u lokaal redundante (LRS) of zone-redundante opslagaccounts (ZRS) gebruikt, hoeft u alleen de URL in teschakelen die wordt vermeld onder 'Primaire eindpunt-URL'.

- Als u opslagaccounts met globaal redundante opslag (GRS) gebruikt, moet u drie URL's inschakelen.

**Voorbeeld:** U implementeert een opslagsynchronisatieservice in `"West US"` en registreert uw server bij deze service. De URL's waarmee de server in dit geval kan communiceren, zijn:

> - https: \/ /westus01.afs.azure.net (primair eindpunt: VS - west)
> - https: \/ /eastus01.afs.azure.net (gekoppelde fail-overregio: VS - oost)
> - https: \/ /tm-westus01.afs.azure.net (detectie-URL van de primaire regio)

### <a name="allow-list-for-azure-file-sync-ip-addresses"></a>Lijst met toegestane Azure File Sync IP-adressen
Azure File Sync ondersteunt het gebruik van [servicetags](../../virtual-network/service-tags-overview.md), die een groep IP-adres voorvoegsels voor een bepaalde Azure-service vertegenwoordigen. U kunt servicetags gebruiken om firewallregels te maken die communicatie met de Azure File Sync maken. De servicetag voor Azure File Sync is `StorageSyncService` .

Als u een Azure File Sync azure gebruikt, kunt u de naam van de servicetag rechtstreeks in uw netwerkbeveiligingsgroep gebruiken om verkeer toe te staan. Zie [Netwerkbeveiligingsgroepen](../../virtual-network/network-security-groups-overview.md) voor meer informatie over hoe u dit doet.

Als u Azure File Sync on-premises gebruikt, kunt u de servicetag-API gebruiken om specifieke IP-adresbereiken op te halen voor de lijst met toegestane IP-adressen van uw firewall. Er zijn twee manieren om deze informatie op te halen:

- De huidige lijst met IP-adresbereiken voor alle Azure-services die servicetags ondersteunen, wordt wekelijks in het Microsoft Downloadcentrum gepubliceerd in de vorm van een JSON-document. Elke Azure-cloud heeft zijn eigen JSON-document met de IP-adresbereiken die relevant zijn voor die cloud:
    - [Azure openbaar](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure van de Amerikaanse overheid](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure China](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure Duitsland](https://www.microsoft.com/download/details.aspx?id=57064)
- Met de API voor servicetagdetectie (preview-versie) kunt u de huidige lijst met servicetags programmatisch ophalen. In de preview-versie kan de API voor servicetagdetectie informatie retourneren die minder actueel is dan informatie die wordt geretourneerd uit de JSON-documenten die in het Microsoft Downloadcentrum worden gepubliceerd. U kunt het API-gebied gebruiken op basis van uw automatiseringsvoorkeur:
    - [REST API](/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Azure-CLI](/cli/azure/network#az_network_list_service_tags)

Omdat de detectie-API voor servicetags niet zo vaak wordt bijgewerkt als de JSON-documenten die naar het Microsoft Downloadcentrum zijn gepubliceerd, wordt u aangeraden het JSON-document te gebruiken om de lijst met toegestane bestanden van uw on-premises firewall bij te werken. U kunt dit als volgt doen:

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

Vervolgens kunt u de IP-adresbereiken in gebruiken `$ipAddressRanges` om uw firewall bij te werken. Raadpleeg de website van uw firewall/netwerkapparaat voor informatie over het bijwerken van uw firewall.

## <a name="test-network-connectivity-to-service-endpoints"></a>Netwerkconnectiviteit met service-eindpunten testen
Zodra een server is geregistreerd bij de Azure File Sync-service, kunnen de Test-StorageSyncNetworkConnectivity-cmdlet en ServerRegistration.exe worden gebruikt om communicatie te testen met alle eindpunten (URL's) die specifiek zijn voor deze server. Deze cmdlet kan helpen bij het oplossen van problemen wanneer onvolledige communicatie voorkomt dat de server volledig werkt met Azure File Sync en kan worden gebruikt om proxy- en firewallconfiguraties af te stemmen.

Als u de netwerkverbindingstest wilt uitvoeren, installeert Azure File Sync agent versie 9.1 of hoger en voert u de volgende PowerShell-opdrachten uit:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Test-StorageSyncNetworkConnectivity
```

## <a name="summary-and-risk-limitation"></a>Samenvatting en risicobeperking
De lijsten eerder in dit document bevatten de URL's Azure File Sync momenteel met communiceert. Firewalls moeten uitgaand verkeer naar deze domeinen kunnen toestaan. Microsoft streeft ernaar deze lijst bijgewerkt te houden.

Het instellen van firewallregels die domeinbeperkend zijn, kan een maatstaf zijn om de beveiliging te verbeteren. Als deze firewallconfiguraties worden gebruikt, moet u er rekening mee houden dat URL's worden toegevoegd en zelfs na een periode kunnen worden gewijzigd. Raadpleeg dit artikel regelmatig.

## <a name="next-steps"></a>Volgende stappen
- [Planning voor een Azure Files Sync-implementatie](file-sync-planning.md)
- [Azure Files SYNC implementeren](file-sync-deployment-guide.md)
- [Azure File Sync bewaken](file-sync-monitoring.md)
