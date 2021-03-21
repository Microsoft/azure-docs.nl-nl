---
title: Integration runtime in Azure Data Factory bewaken
description: Meer informatie over het bewaken van verschillende typen Integration runtime in Azure Data Factory.
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/11/2020
author: dcstwh
ms.author: weetok
ms.openlocfilehash: 1cb4fcaa51e1a59ee9d09eb178faf9b250173709
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101740019"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Een IR bewaken in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]
  
**Integration runtime** is de reken infrastructuur die door Azure Data Factory (ADF) wordt gebruikt om verschillende mogelijkheden voor gegevens integratie in verschillende netwerk omgevingen te bieden. Er zijn drie typen Integration runtimes die worden aangeboden door Data Factory:

- Azure Integration Runtime
- Zelf-hostende Integration Runtime
- Azure-SQL Server Integration Services (SSIS) Integration runtime

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u de status van een exemplaar van Integration runtime (IR) wilt ophalen, voert u de volgende Power shell-opdracht uit: 

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

De cmdlet retourneert verschillende informatie voor verschillende typen Integration runtime. In dit artikel worden de eigenschappen en statussen van elk type Integration runtime beschreven.  

## <a name="azure-integration-runtime"></a>Azure Integration Runtime

De reken resource voor een Azure Integration runtime wordt volledig beheerd in Azure. De volgende tabel bevat beschrijvingen voor eigenschappen die worden geretourneerd door de opdracht **Get-AzDataFactoryV2IntegrationRuntime** :

### <a name="properties"></a>Eigenschappen

De volgende tabel bevat beschrijvingen van de eigenschappen die worden geretourneerd door de cmdlet voor een Azure Integration runtime:

| Eigenschap | Beschrijving |
-------- | ------------- | 
| Name | De naam van de Azure Integration runtime. |  
| Staat | Status van de Azure Integration runtime. | 
| Locatie | Locatie van de Azure Integration runtime. Zie [Inleiding tot Integration runtime](concepts-integration-runtime.md)voor meer informatie over de locatie van een Azure Integration runtime. |
| DataFactoryName | De naam van de data factory waarvan de Azure Integration runtime deel uitmaakt. | 
| ResourceGroupName | De naam van de resource groep waartoe de data factory behoort.  |
| Beschrijving | Beschrijving van de Integration runtime.  |

### <a name="status"></a>Status

De volgende tabel bevat mogelijke statussen van een Azure Integration runtime:

| Status | Opmerkingen/Scenario's | 
| ------ | ------------------ |
| Online | Azure Integration runtime is online en klaar om te worden gebruikt. | 
| Offline | De Azure Integration runtime is offline vanwege een interne fout. |

## <a name="self-hosted-integration-runtime"></a>Zelf-hostende Integration Runtime

Deze sectie bevat beschrijvingen voor eigenschappen die door de Get-AzDataFactoryV2IntegrationRuntime-cmdlet worden geretourneerd. 

> [!NOTE] 
> De geretourneerde eigenschappen en status bevatten informatie over de algehele zelf-hostende Integration runtime en elk knoop punt in de runtime.  

### <a name="properties"></a>Eigenschappen

De volgende tabel bevat beschrijvingen van de bewakings eigenschappen voor **elk knoop punt**:

| Eigenschap | Beschrijving | 
| -------- | ----------- | 
| Name | De naam van de zelf-hostende Integration runtime en knoop punten die eraan zijn gekoppeld. Knoop punt is een on-premises Windows-computer waarop de zelf-hostende Integration runtime is geïnstalleerd. |  
| Status | De status van de algemene zelf-hostende Integration runtime en elk knoop punt. Voor beeld: online/offline/beperkt/etc. Zie de volgende sectie voor meer informatie over deze statussen. | 
| Versie | De versie van de zelf-hostende Integration runtime en elk knoop punt. De versie van de zelf-hostende Integration runtime wordt bepaald op basis van de versie van de knoop punten in de groep. Als er knoop punten met verschillende versies in de zelf-hostende Integration runtime-installatie zijn, worden alleen de knoop punten met hetzelfde versie nummer als de logische zelf-hostende Integration runtime-functie correct uitgevoerd. Andere bevinden zich in de beperkte modus en moeten hand matig worden bijgewerkt (alleen als de automatische update mislukt). | 
| Beschikbaar geheugen | Beschikbaar geheugen op een zelf-hostend Integration runtime-knoop punt. Deze waarde is een bijna realtime moment opname. | 
| CPU-gebruik | CPU-gebruik van een zelf-hostend Integration runtime-knoop punt. Deze waarde is een bijna realtime moment opname. |
| Netwerken (in/uit) | Netwerk gebruik van een zelf-hostend Integration runtime-knoop punt. Deze waarde is een bijna realtime moment opname. | 
| Gelijktijdige taken (uitvoeren/beperken) | **Wordt uitgevoerd**. Aantal taken of taken dat op elk knoop punt wordt uitgevoerd. Deze waarde is een bijna realtime moment opname. <br/><br/>**Limiet**. De limiet is het maximale aantal gelijktijdige taken voor elk knoop punt. Deze waarde wordt gedefinieerd op basis van de grootte van de machine. U kunt de limiet verhogen voor het opschalen van gelijktijdige taak uitvoering in geavanceerde scenario's, wanneer activiteiten een time-out hebben, zelfs wanneer de CPU, het geheugen of het netwerk wordt gebruikt. Deze mogelijkheid is ook beschikbaar met een zelf-hostende Integration runtime met één knoop punt. |
| Rol | Er zijn twee soorten rollen in een zelf-hostende Integration runtime met meerdere knoop punten: dispatcher en worker. Alle knoop punten zijn werk nemers, wat betekent dat ze allemaal kunnen worden gebruikt om taken uit te voeren. Er is slechts één dispatcher-knoop punt, dat wordt gebruikt om taken/taken uit te geven vanuit Cloud Services en deze te verzenden naar verschillende worker-knoop punten. Het dispatcher-knoop punt is ook een worker-knoop punt. |

Sommige instellingen van de eigenschappen maken meer indruk wanneer er twee of meer knoop punten aanwezig zijn in de zelf-hostende Integration runtime (dat wil zeggen, in een scenario voor schalen).

#### <a name="concurrent-jobs-limit"></a>Limiet voor gelijktijdige taken

De standaard waarde van de limiet voor gelijktijdige taken wordt ingesteld op basis van de grootte van de machine. De factoren die worden gebruikt om deze waarde te berekenen, zijn afhankelijk van de hoeveelheid RAM-geheugen en het aantal CPU-kernen van de machine. Hoe meer kernen en meer geheugen, des te hoger de standaard limiet van gelijktijdige taken.

U kunt uitschalen door het aantal knoop punten te verhogen. Wanneer u het aantal knoop punten verhoogt, is de limiet voor gelijktijdige taken de som van de gelijktijdige taak limiet waarden van alle beschik bare knoop punten.  Als u bijvoorbeeld met één knoop punt Maxi maal twaalf gelijktijdige taken uitvoert, kunt u met drie meer soort gelijke knoop punten Maxi maal 48 gelijktijdige taken uitvoeren (dat wil zeggen 4 x 12). Het is raadzaam om de limiet voor gelijktijdige taken alleen te verhogen wanneer u weinig resource gebruik ziet met de standaard waarden voor elk knoop punt.

U kunt de berekende standaard waarde in het Azure Portal overschrijven. Selecteer Auteur > verbindingen > Integration Runtimes > > knooppunten te bewerken > gelijktijdige taak waarde per knoop punt te wijzigen. U kunt ook de opdracht Power shell [Update-Azdatafactoryv2integrationruntimenode](/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples) gebruiken.
  
### <a name="status-per-node"></a>Status (per knoop punt)

De volgende tabel bevat mogelijke statussen van een zelf-hostende Integration runtime-knoop punt:

| Status | Beschrijving |
| ------ | ------------------ | 
| Online | Het knoop punt is verbonden met de Data Factory-service. |
| Offline | Het knoop punt is offline. |
| Upgrade uitvoeren | Het knoop punt wordt automatisch bijgewerkt. |
| Beperkt | Vanwege een probleem met de verbinding. Wordt mogelijk veroorzaakt door een probleem met de HTTP-poort 8060, een probleem met de Service Bus-verbinding of een probleem met de synchronisatie van referenties. |
| Niet-actief | Het knoop punt bevindt zich in een configuratie die verschilt van de configuratie van andere hoofd knooppunten. |

Een knoop punt kan inactief zijn wanneer er geen verbinding kan worden gemaakt met andere knoop punten.

### <a name="status-overall-self-hosted-integration-runtime"></a>Status (algemene zelf-hostende Integration runtime)

De volgende tabel bevat mogelijke statussen van een zelf-hostende Integration runtime. Deze status is afhankelijk van de status van alle knoop punten die deel uitmaken van de runtime. 

| Status | Beschrijving |
| ------ | ----------- | 
| Registratie vereist | Er is nog geen knoop punt geregistreerd bij deze zelf-hostende Integration runtime. |
| Online | Alle knoop punten zijn online. |
| Offline | Er is geen knoop punt online. |
| Beperkt | Niet alle knoop punten in deze zelf-hostende Integration runtime hebben de status in orde. Deze status is een waarschuwing dat bepaalde knoop punten mogelijk niet actief zijn. Deze status kan worden veroorzaakt door een probleem met de synchronisatie van referenties op de verzender/het worker-knoop punt. |

Gebruik de cmdlet **Get-AzDataFactoryV2IntegrationRuntimeMetric** om de JSON-nettolading op te halen met de gedetailleerde runtime-eigenschappen voor zelf-hostende Integration en de bijbehorende momentopname waarden tijdens de uitvoering van de cmdlet.

```powershell
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName | ConvertTo-Json 
```

Voorbeeld uitvoer (er wordt van uitgegaan dat er twee knoop punten zijn gekoppeld aan deze zelf-hostende Integration runtime):

```json
{
    "IntegrationRuntimeName":  "<Name of your integration runtime>",
    "ResourceGroupName":  "<Resource Group Name>",
    "DataFactoryName":  "<Data Factory Name>",
    "Nodes":  [
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        },
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        }

    ]
} 
```

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS-integratie-runtime

Azure-SSIS IR is een volledig beheerd cluster met virtuele Azure-machines (Vm's of knoop punten) die zijn toegewezen om uw SSIS-pakketten uit te voeren. U kunt SSIS-pakket uitvoeringen aanroepen op Azure-SSIS IR met behulp van verschillende methoden, bijvoorbeeld via Azure-SQL Server Data Tools (SSDT), AzureDTExec opdracht regel hulpprogramma, T-SQL op SQL Server Management Studio (SSMS)/SQL Server Agent en SSIS-pakket activiteiten uitvoeren in ADF-pijp lijnen. Azure-SSIS IR voert geen andere ADF-activiteiten uit. Wanneer u eenmaal hebt ingericht, kunt u de algemene/knooppunt specifieke eigenschappen en statussen controleren via Azure PowerShell, Azure Portal en Azure Monitor.

### <a name="monitor-the-azure-ssis-integration-runtime-with-azure-powershell"></a>De Azure SSIS Integration runtime bewaken met Azure PowerShell

Gebruik de volgende Azure PowerShell-cmdlet om de eigenschappen en statussen van Azure-SSIS IR voor het hele knoop punt te controleren.

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

#### <a name="properties"></a>Eigenschappen

De volgende tabel bevat beschrijvingen van eigenschappen die door de bovenstaande cmdlet voor een Azure-SSIS IR worden geretourneerd.

| Eigenschap/status              | Beschrijving                  |
| ---------------------------- | ---------------------------- |
| CreateTime                   | De UTC-tijd waarop uw Azure-SSIS IR is gemaakt. |
| Knooppunten                        | De toegewezen/beschik bare knoop punten van uw Azure-SSIS IR met knooppunt-specifieke statussen (starten/beschikbaar/recyclen/niet beschikbaar) en fouten die kunnen optreden. |
| OtherErrors                  | De niet-knooppunt specifieke actie fouten op uw Azure-SSIS IR. |
| LastOperation                | Het resultaat van de laatste start-en stop bewerking op uw Azure-SSIS IR met een actie bare fout (en) als deze is mislukt. |
| Staat                        | De algehele status van uw Azure-SSIS IR (begin/start/gestart/gestopt). |
| Locatie                     | De locatie van uw Azure-SSIS IR. |
| NodeSize                     | De grootte van elk knoop punt in uw Azure-SSIS IR. |
| NodeCount                    | Het aantal knoop punten in uw Azure-SSIS IR. |
| MaxParallelExecutionsPerNode | Het maximum aantal parallelle uitvoeringen per knoop punt in uw Azure-SSIS IR. |
| CatalogServerEndpoint        | Het eind punt van uw bestaande Azure SQL Database Server of Managed instance to host SSIS Catalog (SSISDB). |
| CatalogAdminUserName         | De gebruikers naam van de beheerder voor uw bestaande Azure SQL Database Server of een beheerd exemplaar. ADF gebruikt deze informatie om namens u SSISDB voor te bereiden en te beheren. |
| CatalogAdminPassword         | Het beheerders wachtwoord voor uw bestaande Azure SQL Database Server of beheerde instantie. |
| CatalogPricingTier           | De prijs categorie voor SSISDB die wordt gehost door Azure SQL Database Server.  Niet van toepassing op Azure SQL Managed instance hosting SSISDB. |
| VNetId                       | De resource-ID van het virtuele netwerk waarmee de Azure-SSIS IR moet worden toegevoegd. |
| Subnet                       | De naam van het subnet voor de Azure-SSIS IR die u wilt toevoegen. |
| Id                           | De resource-ID van uw Azure-SSIS IR. |
| Type                         | Het IR-type (beheerd/zelf-Hostend) van uw Azure-SSIS IR. |
| ResourceGroupName            | De naam van uw Azure-resource groep waarin uw ADF en Azure-SSIS IR zijn gemaakt. |
| DataFactoryName              | De naam van de ADF. |
| Name                         | De naam van uw Azure-SSIS IR. |
| Beschrijving                  | De beschrijving van uw Azure-SSIS IR. |
  
#### <a name="status-per-azure-ssis-ir-node"></a>Status (per Azure-SSIS IR knoop punt)

De volgende tabel bevat mogelijke statussen van een Azure-SSIS IR knoop punt:

| Knooppunt-specifieke status | Beschrijving |
| -------------------- | ----------- | 
| Starten             | Dit knoop punt wordt voor bereid. |
| Beschikbaar            | Met dit knoop punt kunt u SSIS-pakketten implementeren en uitvoeren. |
| Recycl            | Dit knoop punt wordt hersteld/opnieuw gestart. |
| Niet beschikbaar          | Dit knoop punt is niet gereed voor het implementeren/uitvoeren van SSIS-pakketten en heeft actie fouten/problemen die kunnen worden opgelost. |

#### <a name="status-overall-azure-ssis-ir"></a>Status (totaal Azure-SSIS IR)

De volgende tabel bevat mogelijke algemene statussen van een Azure-SSIS IR. De algemene status is afhankelijk van de gecombineerde status van alle knoop punten die deel uitmaken van de Azure-SSIS IR. 

| Algemene status | Beschrijving | 
| -------------- | ----------- | 
| Eerste        | De knoop punten van uw Azure-SSIS IR zijn niet toegewezen/voor bereid. | 
| Starten       | De knoop punten van uw Azure-SSIS IR worden toegewezen/voor bereid en de facturering is gestart. |
| Gestart        | De knoop punten van uw Azure-SSIS IR zijn toegewezen/voor bereid en ze zijn klaar om SSIS-pakketten te implementeren en uit te voeren. |
| Stoppen       | De knoop punten van uw Azure-SSIS IR worden vrijgegeven. |
| Gestopt        | De knoop punten van uw Azure-SSIS IR zijn vrijgegeven en de facturering is gestopt. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-azure-portal"></a>De Azure SSIS Integration runtime in Azure Portal bewaken

Als u uw Azure-SSIS IR in Azure Portal wilt bewaken, gaat u naar de pagina **Integration Runtimes** van **monitor** hub in de ADF-gebruikers interface, waar u al uw Integration Runtimes kunt zien.

![Alle Integration Runtimes bewaken](media/monitor-integration-runtime/monitor-integration-runtimes.png)

Selecteer vervolgens de naam van uw Azure-SSIS IR om de pagina controle te openen, waar u de eigenschappen en statussen van de algemene/specifieke knoop punten kunt zien. Afhankelijk van de manier waarop u de instellingen algemeen, implementatie en Geavanceerd van uw Azure-SSIS IR configureert, vindt u in deze pagina verschillende informatief en functionele tegels.

In de informatie tegels **type** en **regio** worden respectievelijk het type en de regio van uw Azure-SSIS IR weer gegeven.

De tegel informatie over **knooppunt grootte** bevat de SKU (SSIS edition_VM tier_VM Series), het aantal CPU-kernen en de grootte van het RAM-geheugen per knoop punt voor uw Azure-SSIS IR. 

De tegel belichte **/AANGEvraagde knoop** punten vergelijkt het aantal knoop punten dat momenteel wordt uitgevoerd op het totale aantal knoop punten dat eerder is aangevraagd voor uw Azure-SSIS IR.

De tegel **koppeling voor dubbele stand-by-functie/rol** bevat de naam van uw dubbele stand-by Azure-SSIS IR-paar dat wordt gesynchroniseerd met een Azure SQL database/Managed instance failover groep voor bedrijfs continuïteit en herstel na nood gevallen (BCDR) en de huidige primaire/secundaire rol van uw Azure-SSIS IR. Wanneer er een SSISDB-failover optreedt, worden de rollen door uw primaire en secundaire Azure-SSIS-IRs gewisseld (Zie [uw Azure-SSIS IR configureren voor BCDR](./configure-bcdr-azure-ssis-integration-runtime.md)).

De functionele tegels worden in meer details hieronder beschreven.

![Uw Azure-SSIS IR bewaken](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime.png)

#### <a name="status-tile"></a>STATUS tegel

Op de **status** tegel van de pagina Azure-SSIS IR bewaking ziet u de algehele status, bijvoorbeeld **actief** of **gestopt**. Als u de status actief selecteert, **wordt** er een venster weer gegeven met de knop Live **stoppen** om de Azure-SSIS IR te stoppen. Als u de status **stopped** selecteert, wordt er een venster weer gegeven met de knop Live **Start** om uw Azure-SSIS IR te starten. Het pop-upvenster bevat ook de knop **Execute SSIS package** om automatisch een ADF-pijp lijn te genereren met activiteit voor het uitvoeren van SSIS-pakketten die op uw Azure-SSIS IR wordt uitgevoerd (Zie [SSIS-packs uitvoeren als uitvoering van SSIS-pakket activiteiten in ADF-pijp lijnen](./how-to-invoke-ssis-package-ssis-activity.md)) en het tekstvak **resource-id** , van waaruit u uw Azure-SSIS IR resource-id () kunt kopiëren `/subscriptions/YourAzureSubscripton/resourcegroups/YourResourceGroup/providers/Microsoft.DataFactory/factories/YourADF/integrationruntimes/YourAzureSSISIR` . Het achtervoegsel van de Azure-SSIS IR Resource-ID die uw ADF-en Azure-SSIS IR-namen bevat vormt een cluster-ID die kan worden gebruikt voor het aanschaffen van extra Premium/gelicentieerde SSIS-onderdelen van onafhankelijke software leveranciers (Isv's) en deze aan uw Azure-SSIS IR te koppelen (Zie [Premium/gelicentieerde onderdelen op uw Azure-SSIS IR installeren](./how-to-develop-azure-ssis-ir-licensed-components.md)).

![De tegel Azure-SSIS IR STATUS bewaken](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-status.png)

#### <a name="ssisdb-server-endpoint-tile"></a>Tegel SSISDB-SERVER EINDPUNT

Als u een project implementatie model gebruikt waarbij pakketten worden opgeslagen in SSISDB die worden gehost door uw Azure SQL Database Server of beheerde instantie, ziet u de tegel **SSISDB server-eind punt** op de pagina Azure-SSIS IR bewaking (Zie [uw implementatie-instellingen voor Azure-SSIS IR configureren](./tutorial-deploy-ssis-packages-azure.md#deployment-settings-page)). Op deze tegel kunt u een koppeling selecteren met uw Azure SQL Database Server of een beheerd exemplaar om een venster te openen, waar u het server eindpunt vanuit een tekstvak kunt kopiëren en gebruiken wanneer u verbinding maakt vanuit SSMS om uw pakketten te implementeren, te configureren, uit te voeren en te beheren. In het pop-upvenster kunt u ook de koppeling **uw Azure SQL database of beheerde exemplaar instellingen weer geven** selecteren om uw SSISDB opnieuw te configureren/verg Roten of verkleinen in azure Portal.

![De tegel Azure-SSIS IR-SSISDB bewaken](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-ssisdb.png)

#### <a name="proxy--staging-tile"></a>Tegel voor PROXY/fase ring

Als u Self-Hosted IR (SHIR) downloadt, installeert en configureert als een proxy voor uw Azure-SSIS IR om toegang te krijgen tot gegevens on-premises, ziet u de tegel **proxy/STAGING** op uw Azure-SSIS IR bewakings pagina (Zie [SHIR configureren als proxy voor uw Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md)). Op deze tegel kunt u een koppeling selecteren die uw SHIR aanwijst om de controle pagina ervan te openen. U kunt ook een andere koppeling selecteren om uw Azure-Blob Storage aan te wijzen voor fase ring om de gekoppelde service opnieuw te configureren.

#### <a name="validate-vnet--subnet-tile"></a>De tegel VNET/SUBNET valideren

Als u uw Azure-SSIS IR aan een VNet koppelt, ziet u de tegel **VNet/SUBNET valideren** op uw Azure-SSIS IR bewakings pagina (zie [uw Azure-SSIS IR toevoegen aan een vnet](./join-azure-ssis-integration-runtime-virtual-network.md)). Op deze tegel kunt u een koppeling selecteren voor uw VNet en subnet om een venster te openen, waar u uw VNet-Resource-ID ( `/subscriptions/YourAzureSubscripton/resourceGroups/YourResourceGroup/providers/Microsoft.Network/virtualNetworks/YourARMVNet` ) en de naam van het subnet kopieert vanuit tekst vakken, en uw vnet-en subnet-configuraties kunt valideren om ervoor te zorgen dat de vereiste inkomende/uitgaande netwerk verkeer en het beheer van uw Azure-SSIS IR niet worden belemmerd.

![De tegel Azure-SSIS IR-validatie bewaken](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-validate.png)

#### <a name="diagnose-connectivity-tile"></a>CONNECTIVITEITs tegel DIAGNOSTICeren

Op de tegel connectiviteit voor het controleren van de **verbinding** van de pagina bewaking van Azure-SSIS IR kunt u de **verbindings koppeling testen** selecteren om een venster te openen. hier kunt u de verbindingen tussen uw Azure-SSIS IR en relevante pakket-en configuratie-en gegevens archieven controleren, evenals beheer Services via hun Fully Qualified Domain Name (FQDN)/IP-adres en de aangewezen poort (Zie [verbindingen testen van uw Azure-SSIS IR](./ssis-integration-runtime-diagnose-connectivity-faq.md)).

![De tegel Azure-SSIS IR-diagnose controleren](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-diagnose.png)

#### <a name="static-public-ip-addresses-tile"></a>Tegel voor statische open bare IP-adressen

Als u uw eigen statische open bare IP-adressen voor Azure-SSIS IR meebrengt, ziet u de tegel **statische open bare IP-adressen** op de pagina voor Azure-SSIS IR bewaking (Zie [uw eigen statische open bare IP-adressen voor Azure-SSIS IR](./join-azure-ssis-integration-runtime-virtual-network.md#publicIP)plaatsen). Op deze tegel kunt u koppelingen selecteren met uw eerste/tweede statische open bare IP-adressen voor Azure-SSIS IR om een venster te openen waarin u de resource-ID () kunt kopiëren `/subscriptions/YourAzureSubscripton/resourceGroups/YourResourceGroup/providers/Microsoft.Network/publicIPAddresses/YourPublicIPAddress` van een tekstvak. In het pop-upvenster kunt u ook de koppeling **uw eerste/tweede statische open bare IP-adres weer geven** selecteren om uw eerste/tweede statische open bare IP-adres te beheren in azure Portal.

![Uw Azure-SSIS IR-statische tegel bewaken](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-static.png)

#### <a name="package-stores-tile"></a>Tegel pakket winkels

Als u pakket implementatie model gebruikt waarbij pakketten worden opgeslagen in het bestands systeem/Azure Files/SQL Server Data Base (MSDB) gehost door uw door Azure SQL beheerd exemplaar en worden beheerd via Azure-SSIS IR pakket winkels, ziet u de tegel **pakket archieven** op de pagina voor Azure-SSIS IR controle (zie [uw Azure-SSIS IR implementatie-instellingen configureren](./tutorial-deploy-ssis-packages-azure.md#deployment-settings-page)). Op deze tegel kunt u een koppeling selecteren met het aantal pakket archieven dat is gekoppeld aan uw Azure-SSIS IR om een venster te openen. hier kunt u de relevante gekoppelde services voor uw Azure-SSIS IR-pakket archieven opnieuw configureren op het bestands systeem/Azure Files/MSDB gehost door uw door Azure SQL beheerd exemplaar.

![De tegel Azure-SSIS IR-pakket bewaken](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-package.png)

#### <a name="errors-tile"></a>Tegel fout (en)

Als er problemen zijn met het starten/stoppen/onderhoud/bijwerken van uw Azure-SSIS IR, ziet u een tegel met een extra **fout (en)** op de pagina voor Azure-SSIS IR bewaking. Op deze tegel kunt u een koppeling selecteren met het aantal fouten dat door uw Azure-SSIS IR is gegenereerd om een venster weer te geven, waar u deze fouten in meer details kunt zien en kopiëren om de aanbevolen oplossingen te vinden in onze probleemoplossings handleiding (Zie [problemen met uw Azure-SSIS IR oplossen](./ssis-integration-runtime-management-troubleshoot.md)).

![Uw Azure-SSIS IR-fout tegel bewaken](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-error.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-azure-monitor"></a>De Azure SSIS Integration runtime bewaken met Azure Monitor

Zie [SSIS-bewerkingen controleren met Azure monitor](./monitor-using-azure-monitor.md#monitor-ssis-operations-with-azure-monitor)om uw Azure-SSIS IR te controleren met Azure monitor.

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>Meer informatie over de Azure-SSIS Integration runtime

Raadpleeg de volgende artikelen voor meer informatie over Azure-SSIS Integration runtime:

- [Azure-SSIS Integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Dit artikel bevat conceptuele informatie over Integration Runtimes in het algemeen, met inbegrip van de Azure-SSIS IR. 
- [Zelfstudie: SSIS-pakketten implementeren in Azure](./tutorial-deploy-ssis-packages-azure.md). In dit artikel vindt u stapsgewijze instructies voor het maken van uw Azure-SSIS IR en het gebruik van Azure SQL Database voor het hosten van de SSIS-catalogus (SSISDB). 
- [Procedure: Een Azure SSIS Integration Runtime maken](create-azure-ssis-integration-runtime.md). In dit artikel wordt de zelf studie uitgebreid en vindt u instructies voor het gebruik van Azure SQL Managed instance voor het hosten van SSISDB. 
- [Een Azure-SSIS IR beheren](manage-azure-ssis-integration-runtime.md). Dit artikel laat u zien hoe u uw Azure-SSIS IR kunt starten, stoppen of verwijderen. U ziet ook hoe u deze kunt schalen door meer knoop punten toe te voegen. 
- [Een Azure-SSIS-integratieruntime toevoegen aan een virtueel netwerk](join-azure-ssis-integration-runtime-virtual-network.md). Dit artikel bevat instructies voor het toevoegen van uw Azure-SSIS IR aan een virtueel netwerk.

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor het bewaken van pijp lijnen op verschillende manieren: 

- [Snelstartgids: een Data Factory maken](quickstart-create-data-factory-dot-net.md).
- [Azure Monitor gebruiken om Data Factory pijp lijnen te bewaken](monitor-using-azure-monitor.md)
