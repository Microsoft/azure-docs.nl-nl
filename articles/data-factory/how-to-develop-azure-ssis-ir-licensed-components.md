---
title: Gelicentieerde onderdelen voor Azure-SSIS Integration runtime installeren
description: Meer informatie over hoe een ISV betaalde of gelicentieerde aangepaste onderdelen kan ontwikkelen en installeren voor Azure SSIS Integration runtime
ms.service: data-factory
author: swinarko
ms.author: sawinark
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/09/2020
ms.openlocfilehash: fd62822e111346ee9a81a5d1bcce55191b19da02
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386285"
---
# <a name="install-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Betaalde of gelicentieerde aangepaste onderdelen installeren voor Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt beschreven hoe een ISV, betaalde of gelicentieerde aangepaste onderdelen voor SQL Server Integration Services (SSIS)-pakketten die worden uitgevoerd in azure, kan ontwikkelen en installeren in de Azure SSIS Integration runtime.

## <a name="the-problem"></a>Het probleem

De aard van de Azure SSIS Integration runtime biedt verschillende uitdagingen, waardoor de gebruikelijke licentie methoden worden gebruikt voor de on-premises installatie van aangepaste onderdelen die niet toereikend zijn. Als gevolg hiervan heeft de Azure-SSIS IR een andere aanpak nodig.

-   De knoop punten van de Azure-SSIS IR zijn vluchtig en kunnen op elk gewenst moment worden toegewezen of vrijgegeven. U kunt knoop punten bijvoorbeeld starten of stoppen om de kosten te beheren, of omhoog en omlaag schalen door verschillende groottes van knoop punten. Als gevolg hiervan is het binden van een licentie van een derde partij aan een bepaald knoop punt met behulp van computerspecifieke info, zoals MAC-adres of CPU-ID niet langer beschikbaar.

-   U kunt ook de Azure-SSIS IR in-of uitschalen, zodat het aantal knoop punten op elk gewenst moment kan worden verkleind of uitgevouwen.

## <a name="the-solution"></a>De oplossing

Als gevolg van de beperkingen van traditionele licentie methoden die in de vorige sectie zijn beschreven, biedt de Azure-SSIS IR een nieuwe oplossing. Deze oplossing maakt gebruik van Windows-omgevings variabelen en SSIS-systeem variabelen voor de licentie binding en validatie van onderdelen van derden. Isv's kunnen deze variabelen gebruiken om unieke en permanente informatie te verkrijgen voor een Azure-SSIS IR, zoals de cluster-ID en het aantal cluster knooppunten. Met deze informatie kunnen Isv's de licentie voor hun onderdeel binden aan een Azure-SSIS IR *als een cluster*. Deze binding maakt gebruik van een ID die niet verandert wanneer klanten worden gestart of gestopt, omhoog of omlaag schalen, in-of uitschalen of de Azure-SSIS IR op een wille keurige manier opnieuw configureren.

In het volgende diagram ziet u de typische installatie, activering en licentie bindingen en validatie stromen voor onderdelen van derden die gebruikmaken van deze nieuwe variabelen:

![Installatie van gelicentieerde onderdelen](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Instructies
1. Isv's kunnen hun gelicentieerde onderdelen aanbieden in verschillende Sku's of lagen (bijvoorbeeld één knoop punt, Maxi maal vijf knoop punten, Maxi maal 10 knoop punten, enzovoort). De ISV levert de bijbehorende product code wanneer klanten een product kopen. De ISV kan ook een Azure Storage BLOB-container bieden die een ISV-installatie script en bijbehorende bestanden bevat. Klanten kunnen deze bestanden in hun eigen opslag container kopiëren en deze wijzigen met hun eigen product code (bijvoorbeeld door uit te voeren `IsvSetup.exe -pid xxxx-xxxx-xxxx` ). Klanten kunnen vervolgens de Azure-SSIS IR inrichten of opnieuw configureren met de SAS-URI van hun container als para meter. Zie [Aangepaste instelling voor de Azure-SSIS-integratieruntime](how-to-configure-azure-ssis-ir-custom-setup.md) voor meer informatie.

2. Wanneer de Azure-SSIS IR is ingericht of opnieuw geconfigureerd, wordt ISV Setup uitgevoerd op elk knoop punt om een query uit te voeren op de Windows-omgevings variabelen `SSIS_CLUSTERID` `SSIS_CLUSTERNODECOUNT` . Vervolgens verzendt de Azure-SSIS IR de cluster-ID en de product code voor het gelicentieerde product naar de ISV-activerings server om een activerings sleutel te genereren.

3. Na ontvangst van de activerings sleutel kan ISV Setup de sleutel lokaal opslaan op elk knoop punt (bijvoorbeeld in het REGI ster).

4. Wanneer klanten een pakket uitvoeren dat gebruikmaakt van het gelicentieerde onderdeel van de ISV op een knoop punt van de Azure-SSIS IR, leest het pakket de lokaal opgeslagen activerings sleutel en valideert deze met de cluster-ID van het knoop punt. Het pakket kan ook optioneel het aantal cluster knooppunten rapporteren aan de ISV-activerings server.

    Hier volgt een voor beeld van code die de activerings sleutel valideert en het aantal cluster knooppunten rapporteert:

    ```csharp
    public override DTSExecResult Validate(Connections, VariableDispenser, IDTSComponentEvents componentEvents, IDTSLogging log) 
                                                                                                                               
    {                                                                                                                             
                                                                                                                               
    Variables vars = null;                                                                                                        
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterID");                                                                           
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterNodeCount");                                                                    
                                                                                                                               
    variableDispenser.GetVariables(ref vars);                                                                                     
                                                                                                                               
    // Validate Activation Key with ClusterID                                                                                     
                                                                                                                               
    // Report on ClusterNodeCount                                                                                                 
                                                                                                                               
    vars.Unlock();                                                                                                                
                                                                                                                               
    return base.Validate(connections, variableDispenser, componentEvents, log);                                                   
                                                                                                                               
    }
    ```

## <a name="isv-partners"></a>ISV-partners

U vindt een lijst met ISV-partners die hun onderdelen en uitbrei Azure-SSIS IR dingen hebben aangepast aan het einde van deze blog post- [Enter prise Edition, aangepaste Setup en uitbreid baarheid van derden voor SSIS in ADF](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Enterprise-Edition-Custom-Setup-and-3rd-Party-Extensibility-for/ba-p/388360).

## <a name="next-steps"></a>Volgende stappen

-   [Aangepaste installatie voor Azure-SSIS Integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Enter prise-editie van de Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
