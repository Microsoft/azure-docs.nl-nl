---
title: Use case-klant profilering
description: Meer informatie over hoe Azure Data Factory wordt gebruikt voor het maken van een gegevensgestuurde werk stroom (pijp lijn) om klanten van games te profileren.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: bb7f0fb6184a813dd9fd3c193bc5e3bda5628f2d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376714"
---
# <a name="use-case---customer-profiling"></a>Use case-klant profilering
Azure Data Factory is een van de vele services die worden gebruikt voor het implementeren van de Cortana Intelligence Suite oplossings versnellers.  Ga voor meer informatie over Cortana Intelligence naar [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics). In dit document beschrijven we een eenvoudige use-case om u te helpen aan de slag te gaan met informatie over hoe Azure Data Factory veelvoorkomende analyse problemen kunt oplossen.

## <a name="scenario"></a>Scenario
Contoso is een gaming bedrijf dat games maakt voor meerdere platformen: spel consoles, hand apparaten en personal computers (Pc's). Als spelers deze games spelen, worden er grote hoeveel heden logboek gegevens geproduceerd waarmee de gebruiks patronen, de spel stijl en de voor keuren van de gebruiker worden bijgehouden.  In combi natie met demografische, regionale en product gegevens kan contoso Analytics uitvoeren om ze te begeleiden bij het verbeteren van de ervaring van spelers en deze te richten op upgrades en in-game aankopen. 

Het doel van Contoso is het identificeren van verkoop-en cross-sell-mogelijkheden op basis van de game geschiedenis van de spelers en het toevoegen van fascinerende functies om de groei van het bedrijf te stimuleren en klanten een betere ervaring te bieden. Voor deze use-case gebruiken we een gaming bedrijf als voor beeld van een bedrijf. Het bedrijf wil de games optimaliseren op basis van het gedrag van spelers. Deze principes zijn van toepassing op elk bedrijf dat haar klanten wil betrekken over zijn goederen en services en de ervaring van hun klanten kan verbeteren.

In deze oplossing wil Contoso de effectiviteit evalueren van een marketing campagne die onlangs is gestart. We beginnen met de onbewerkte gaming logboeken, verwerken en verrijken deze met geolocatiegegevens, nemen samen met de advertentie referentie gegevens en kopiëren ze in een Azure SQL Database om de impact van de campagne te analyseren.

## <a name="deploy-solution"></a>Oplossing implementeren
Alles wat u nodig hebt om toegang te krijgen tot deze eenvoudige use-case is een [Azure-abonnement](https://azure.microsoft.com/pricing/free-trial/), een [Azure Blob-opslag account](../../storage/common/storage-account-create.md)en een [Azure SQL database](../../azure-sql/database/single-database-create-quickstart.md). U implementeert de klant profilerings pijplijn vanuit de tegel **voorbeeld pijplijnen** op de start pagina van uw Data Factory.

1. Maak een data factory of open een bestaande data factory. Zie [gegevens kopiëren van Blob Storage naar SQL database met behulp van Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor stappen om een Data Factory te maken.
2. Klik in de Blade **Data Factory** voor de Data Factory op de tegel **voorbeeld pijplijnen** .

    ![Tegel voor voorbeeld pijplijnen](./media/data-factory-samples/SamplePipelinesTile.png)
3. Klik op de Blade **voorbeeld pijplijnen** op de **klant profilering** die u wilt implementeren.

    ![Blade voorbeeld pijplijnen](./media/data-factory-samples/SampleTile.png)
4. Geef de configuratie-instellingen voor het voor beeld op. Bijvoorbeeld uw Azure Storage-account naam en-sleutel, naam van logische SQL-Server, Data Base, gebruikers-ID en wacht woord.

    ![Voorbeeld Blade](./media/data-factory-samples/SampleBlade.png)
5. Nadat u klaar bent met het opgeven van de configuratie-instellingen, klikt u op **maken** om de voorbeeld pijplijnen en gekoppelde services/tabellen die door de pijp lijnen worden gebruikt, te maken/te implementeren.
6. U ziet de status van de implementatie op de voorbeeld tegel waarop u eerder hebt geklikt op de Blade **voorbeeld pijplijnen** .

    ![Implementatiestatus](./media/data-factory-samples/DeploymentStatus.png)
7. Wanneer het bericht **implementatie voltooid** wordt weer gegeven op de tegel voor het voor beeld, sluit u de Blade **voorbeeld pijplijnen** .  
8. Op de Blade **Data Factory** ziet u dat gekoppelde services, gegevens sets en pijp lijnen aan uw Data Factory worden toegevoegd.  

    ![Blade Gegevensfactory](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="solution-overview"></a>Overzicht van de oplossing
Deze eenvoudige use-case kan worden gebruikt als voor beeld van hoe u Azure Data Factory kunt gebruiken om gegevens op te nemen, voor te bereiden, te transformeren, te analyseren en te publiceren.

![End-to-end werkstroom](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

In deze afbeelding ziet u hoe de gegevens pijplijnen worden weer gegeven in de Azure Portal nadat ze zijn geïmplementeerd.

1. De **PartitionGameLogsPipeline** leest de onbewerkte Game gebeurtenissen uit Blob Storage en maakt partities op basis van jaar, maand en dag.
2. De **EnrichGameLogsPipeline** neemt deel aan gepartitioneerde Game gebeurtenissen met geo-code referentie gegevens en verrijkt de gegevens door IP-adressen toe te wijzen aan de overeenkomstige geografische locaties.
3. De **AnalyzeMarketingCampaignPipeline** -pijp lijn maakt gebruik van de verrijkte gegevens en verwerkt deze met de advertentie gegevens om de uiteindelijke uitvoer te maken die de effectiviteit van de marketing campagne bevat.

In dit voor beeld wordt Data Factory gebruikt voor het indelen van activiteiten die invoer gegevens kopiëren, transformeren en verwerken van de gegevens, en het uitvoeren van de laatste gegevens naar een Azure SQL Database.  U kunt ook het netwerk van gegevens pijplijnen visualiseren, beheren en hun status controleren vanuit de gebruikers interface.

## <a name="benefits"></a>Voordelen
Door hun gebruikers profiel analyse te optimaliseren en deze uit te lijnen met zakelijke doel stellingen, kan gaming bedrijf snel gebruiks patronen verzamelen en de effectiviteit van de marketing campagnes analyseren.