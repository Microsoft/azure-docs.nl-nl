---
title: De prestaties van de Azure-SSIS Integration Runtime configureren
description: Meer informatie over het configureren van de eigenschappen van de Azure-SSIS Integration Runtime voor hoge prestaties
ms.date: 01/10/2018
ms.topic: conceptual
ms.service: data-factory
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 5d275100124660b901504b7e7f71cf93518fd077
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364389"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>De Azure-SSIS Integration Runtime configureren voor hoge prestaties

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


In dit artikel wordt beschreven hoe u een Azure-SSIS Integration Runtime (IR) configureert voor hoge prestaties. Met de Azure-SSIS IR kunt u SQL Server Integration Services (SSIS)-pakketten implementeren en uitvoeren in Azure. Zie [Integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime) article (Engelstalig) voor meer informatie over Azure-SSIS IR. Voor informatie over het implementeren en uitvoeren van SSIS-pakketten in azure, Zie [lift-en Shift-SQL Server Integration Services workloads naar de Cloud](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

> [!IMPORTANT]
> Dit artikel bevat prestatie resultaten en opmerkingen van interne tests die worden uitgevoerd door leden van het SSIS-ontwikkel team. De resultaten kunnen variëren. Voer uw eigen tests uit voordat u de configuratie-instellingen voltooit, wat van invloed is op de kosten en prestaties.

## <a name="properties-to-configure"></a>Te configureren eigenschappen

In het volgende gedeelte van een configuratie script worden de eigenschappen weer gegeven die u kunt configureren wanneer u een Azure-SSIS Integration Runtime maakt. Zie [SQL Server Integration Services-pakketten implementeren in azure](tutorial-deploy-ssis-packages-azure-powershell.md)voor het volledige Power shell-script en de beschrijving.

```powershell
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your existing SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to max(2 x number of cores, 8) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/SQL Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your SQL Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your server name or managed instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for SQL Managed Instance]"
```

## <a name="azuressislocation"></a>AzureSSISLocation
**AzureSSISLocation** is de locatie voor het worker-knoop punt voor Integration runtime. Het worker-knoop punt onderhoudt een constante verbinding met de SSIS-catalogus database (SSISDB) in Azure SQL Database. Stel de **AzureSSISLocation** in op dezelfde locatie als de [logische SQL-Server](../azure-sql/database/logical-servers.md) die als host fungeert voor SSISDB, waardoor de Integration runtime zo efficiënt mogelijk kan werken.

## <a name="azuressisnodesize"></a>AzureSSISNodeSize
Data Factory, met inbegrip van de Azure-SSIS IR, ondersteunt de volgende opties:
-   Standard \_ a4 versie \_ 2
-   Standard \_ A8 versie \_ 2
-   Standard \_ D1 \_ v2
-   Standard \_ D2 \_ v2
-   Standard \_ D3 \_ v2
-   Standard \_ D4 versie \_ 2
-   Standard \_ D2 \_ v3
-   Standard \_ D4 \_ v3
-   Standard \_ D8 \_ v3
-   Standard \_ D16 \_ v3
-   Standard \_ D32 \_ v3
-   Standard \_ D64 \_ v3
-   Standaard \_ E2 \_ v3
-   Standard \_ E4 \_ v3
-   Standard \_ E8 \_ v3
-   Standard \_ e16 \_ v3
-   Standard \_ E32 \_ v3
-   Standard \_ E64 \_ v3

In de niet-officiële interne test door het SSIS engineering-team lijkt de D-serie beter te zijn voor de uitvoering van SSIS-pakketten dan die van de A-serie.

-   De verhouding van de prestaties/prijs van de D-serie is hoger dan de A-serie en de prestatie-en prijs verhouding van de V3-serie is hoger dan de v2-serie.
-   De door Voer voor de D-serie is hoger dan de reeks met dezelfde prijs en de door Voer voor de V3-serie is hoger dan de v2-reeks met dezelfde prijs.
-   De v2-serie knooppunten van Azure-SSIS IR zijn niet geschikt voor aangepaste installatie. gebruik in plaats daarvan de V3-reeks knooppunten. Als u de v2-reeks knooppunten al gebruikt, kunt u zo snel mogelijk overschakelen naar het gebruik van de V3-serie knooppunten.
-   De E-serie is geoptimaliseerd voor geheugen die een hogere geheugen-naar-CPU-verhouding biedt dan andere computers. Als uw pakket veel geheugen vereist, kunt u overwegen om de E-Series-VM te kiezen.

### <a name="configure-for-execution-speed"></a>Configureren voor uitvoerings snelheid
Als er niet veel pakketten kunnen worden uitgevoerd en u wilt dat pakketten snel worden uitgevoerd, gebruikt u de informatie in de volgende tabel om een type virtuele machine te kiezen dat geschikt is voor uw scenario.

Deze gegevens vertegenwoordigen één pakket uitvoering op één worker-knoop punt. Het pakket laadt 3.000.000 records met de voor-en achternaam kolommen van Azure Blob Storage, genereert een volledige naam kolom en schrijft de records met de volledige naam van meer dan 20 tekens naar Azure Blob Storage.

De y-as is het aantal pakketten dat in één uur wordt uitgevoerd. Houd er rekening mee dat dit slechts een test resultaat is van één pakket met geheugen verbruik. Als u de door Voer van uw pakket wilt weten, kunt u het beste de test zelf uitvoeren.

![Uitvoerings snelheid van SSIS Integration Runtime-pakket](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speedV2.png)

### <a name="configure-for-overall-throughput"></a>Configureren voor algemene door Voer

Als u veel pakketten wilt uitvoeren, en u de meeste algemene door Voer hebt, gebruikt u de informatie in de volgende tabel om een type virtuele machine te kiezen dat geschikt is voor uw scenario.

De y-as is het aantal pakketten dat in één uur wordt uitgevoerd. Houd er rekening mee dat dit slechts een test resultaat is van één pakket met geheugen verbruik. Als u de door Voer van uw pakket wilt weten, kunt u het beste de test zelf uitvoeren.

![SSIS Integration Runtime maximale totale door Voer](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughputV2.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** past de schaal baarheid van de Integration runtime aan. De door Voer van de Integration runtime is evenredig met de **AzureSSISNodeNumber**. Stel eerst de **AzureSSISNodeNumber** in op een kleine waarde, Controleer de door Voer van de Integration runtime en pas vervolgens de waarde voor uw scenario aan. Als u het aantal worker-knoop punten opnieuw wilt configureren, raadpleegt u [een Azure SSIS Integration runtime beheren](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

Wanneer u al een krachtig worker-knoop punt gebruikt voor het uitvoeren van pakketten, kan het toenemende aantal **AzureSSISMaxParallelExecutionsPerNode** de algemene door Voer van de Integration runtime verhogen. Als u de maximum waarde wilt verhogen, moet u Azure PowerShell gebruiken om **AzureSSISMaxParallelExecutionsPerNode** bij te werken. U kunt een schatting maken van de juiste waarde op basis van de kosten van uw pakket en de volgende configuraties voor de worker-knoop punten. Zie voor meer informatie de grootten van [virtuele machines voor algemene doel einden](../virtual-machines/sizes-general.md).

| Grootte             | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximale tijdelijke opslagdoorvoer: IOPS / MBps lezen / MBps schrijven | Maximumaantal gegevensschijven / doorvoer: IOPS | Maximum aantal NIC's/verwachte netwerkprestaties (Mbps) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standard \_ D1 \_ v2 | 1    | 3,5         | 50                     | 3000 / 46 / 23                                             | 2 / 2 x 500                         | 2 / 750                                        |
| Standard \_ D2 \_ v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4 / 4 x 500                         | 2 / 1500                                       |
| Standard \_ D3 \_ v2 | 4    | 14          | 200                    | 12.000 / 187 / 93                                           | 8 / 8 x 500                         | 4 / 3000                                       |
| Standard \_ D4 versie \_ 2 | 8    | 28          | 400                    | 24.000 / 375 / 187                                          | 16 / 16 x 500                       | 8 / 6000                                       |
| Standard \_ a4 versie \_ 2 | 4    | 8           | 40                     | 4000 / 80 / 40                                             | 8 / 8 x 500                         | 4 / 1000                                       |
| Standard \_ A8 versie \_ 2 | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16 / 16 x 500                       | 8 / 2000                                       |
| Standard \_ D2 \_ v3 | 2    | 8           | 50                     | 3000 / 46 / 23                                             | 4-6x500                         | 2 / 1000                                       |
| Standard \_ D4 \_ v3 | 4    | 16          | 100                    | 6000 / 93 / 46                                             | 8-12x500                        | 2 / 2000                                       |
| Standard \_ D8 \_ v3 | 8    | 32          | 200                    | 12.000 / 187 / 93                                           | 16-24x500                       | 4 / 4000                                       |
| Standard \_ D16 \_ v3| 16   | 64          | 400                    | 24.000 / 375 / 187                                          | 32-48x500                        | 8 / 8000                                       |
| Standard \_ D32 \_ v3| 32   | 128         | 800                    | 48.000 / 750 / 375                                          | 32/96x500                       | 8 / 16000                                      |
| Standard \_ D64 \_ v3| 64   | 256         | 1600                   | 96000/1000/500                                         | 32/192x500                      | 8 / 30000                                      |
| Standaard \_ E2 \_ v3 | 2    | 16          | 50                     | 3000 / 46 / 23                                             | 4-6x500                         | 2 / 1000                                       |
| Standard \_ E4 \_ v3 | 4    | 32          | 100                    | 6000 / 93 / 46                                             | 8-12x500                        | 2 / 2000                                       |
| Standard \_ E8 \_ v3 | 8    | 64          | 200                    | 12.000 / 187 / 93                                           | 16-24x500                       | 4 / 4000                                       |
| Standard \_ e16 \_ v3| 16   | 128         | 400                    | 24.000 / 375 / 187                                          | 32/48x500                       | 8 / 8000                                       |
| Standard \_ E32 \_ v3| 32   | 256         | 800                    | 48.000 / 750 / 375                                          | 32/96x500                       | 8 / 16000                                      |
| Standard \_ E64 \_ v3| 64   | 432         | 1600                   | 96000/1000/500                                         | 32/192x500                      | 8 / 30000                                      |

Hier volgen de richt lijnen voor het instellen van de juiste waarde voor de eigenschap **AzureSSISMaxParallelExecutionsPerNode** : 

1. Stel de waarde eerst in op een klein.
2. Verhoog het met een kleine hoeveelheid om te controleren of de algehele door Voer is verbeterd.
3. Stop de verhoging van de waarde wanneer de totale door Voer de maximum waarde bereikt.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** is de prijs categorie voor de SSIS-catalogus database (SSISDB) in Azure SQL database. Deze instelling is van invloed op het maximum aantal werk rollen in het IR-exemplaar, de snelheid waarmee een pakket kan worden uitgevoerd en de snelheid waarmee het uitvoerings logboek kan worden geladen.

-   Als u geen zorgen hebt over de snelheid waarmee de uitvoering van het wachtrij pakket wordt uitgevoerd en het uitvoerings logboek kan worden geladen, kunt u de laagste prijs categorie voor de data base kiezen. Azure SQL Database met Basic-prijzen ondersteunt 8 werk nemers in een Integration runtime-exemplaar.

-   Kies een krachtigere data base dan Basic als het aantal werk nemers meer is dan 8, of het aantal kernen groter is dan 50. Anders wordt de data base het knel punt van het Integration runtime-exemplaar en de algehele prestaties negatief beïnvloed.

-   Kies een krachtigere data base, zoals S3 als het logboek registratie niveau is ingesteld op uitgebreid. Volgens onze onofficieel testen kan de prijs categorie S3 de uitvoering van SSIS-pakketten ondersteunen met 2 knoop punten, 128 parallelle aantallen en het niveau van uitgebreide logboek registratie.

U kunt ook de prijs categorie van de data base aanpassen op basis van de gebruiks gegevens van de [Data Base Trans Action unit](../azure-sql/database/service-tiers-dtu.md) (DTU) die beschikbaar zijn op de Azure Portal.

## <a name="design-for-high-performance"></a>Ontwerp voor hoge prestaties
Het ontwerpen van een SSIS-pakket om uit te voeren op Azure wijkt af van het ontwerpen van een pakket voor on-premises uitvoering. In plaats van het combi neren van meerdere onafhankelijke taken in hetzelfde pakket, kunt u ze in meerdere pakketten scheiden voor een efficiëntere uitvoering in de Azure-SSIS IR. Maak een pakket uitvoering voor elk pakket, zodat ze niet hoeven te wachten tot elkaar zijn voltooid. Deze aanpak heeft voor delen ten opzichte van de schaal baarheid van de Azure-SSIS Integration runtime en verbetert de algehele door voer.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over de Azure-SSIS Integration Runtime. Zie [Azure-SSIS Integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).