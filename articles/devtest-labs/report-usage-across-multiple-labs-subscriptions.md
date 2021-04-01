---
title: Azure DevTest Labs gebruik in meerdere Labs en abonnementen
description: Meer informatie over het rapporteren van Azure DevTest Labs gebruik over meerdere Labs en abonnementen.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1e4d1f0abb5596c7fd9d22740bf052827c2ca666
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102452642"
---
# <a name="report-azure-devtest-labs-usage-across-multiple-labs-and-subscriptions"></a>Rapport Azure DevTest Labs gebruik in meerdere Labs en abonnementen

De meeste grote organisaties willen het resource gebruik volgen om effectiever te zijn met deze resources door trends en uitschieters in het gebruik te visualiseren. Op basis van resource gebruik kunnen de Lab-eigen aren of-managers de Labs aanpassen om het [resource gebruik en de kosten te verbeteren](../cost-management-billing/cost-management-billing-overview.md). In Azure DevTest Labs kunt u het resource gebruik per Lab downloaden zodat een diep gaande historisch inzicht in de gebruiks patronen kan worden weer geven. Deze gebruiks patronen kunnen helpen bij het lokaliseren van wijzigingen om de efficiëntie te verbeteren. De meeste ondernemingen willen zowel het individuele Lab-gebruik als het algehele gebruik over [meerdere Labs en abonnementen](/azure/architecture/cloud-adoption/decision-guides/subscriptions/). 

In dit artikel wordt beschreven hoe u gegevens over resource gebruik kunt afhandelen in meerdere Labs en abonnementen.

![Gebruik rapporteren](./media/report-usage-across-multiple-labs-subscriptions/report-usage.png)

## <a name="individual-lab-usage"></a>Individueel Lab-gebruik

In deze sectie wordt beschreven hoe u resource gebruik voor één Lab exporteert.

Voordat u het resource gebruik van DevTest Labs kunt exporteren, moet u een Azure Storage-account instellen om de verschillende bestanden met de gebruiks gegevens te kunnen opslaan. Er zijn twee algemene manieren om het exporteren van gegevens uit te voeren:

* [DevTest Labs REST API](/rest/api/dtl/labs/exportresourceusage) 
* De Power shell AZ. resource module [invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) met de actie `exportResourceUsage` , de test resource-id en de benodigde para meters. 

    Het artikel [persoonlijke gegevens exporteren of verwijderen](personal-data-delete-export.md) bevat een Power shell-voorbeeld script met gedetailleerde informatie over de gegevens die worden geëxporteerd. 

    > [!NOTE]
    > De datum parameter bevat geen tijds tempel zodat de gegevens uit middernacht bestaan op basis van de tijd zone waarin het lab zich bevindt.

Zodra het exporteren is voltooid, worden er meerdere CSV-bestanden in de Blob-opslag met de verschillende bron gegevens.
  
Er zijn momenteel twee CSV-bestanden:

* *virtualmachines.csv* : bevat informatie over de virtuele machines in het lab
* *disks.csv* : bevat informatie over de verschillende schijven in het lab 

Deze bestanden worden opgeslagen in de *labresourceusage* -BLOB-container onder de naam van het lab, de unieke Lab-id, de uitgevoerde datum en de volledige of de begin datum die is gebaseerd op de export aanvraag. Een voor beeld van een BLOB-structuur is:

* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD26-MM6-2019YYYY/full/virtualmachines.csv`
* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD-MM-YYYY/26-6-2019/20-6-2019<Start>DD-MM-YYYY/virtualmachines.csv`

## <a name="exporting-usage-for-all-labs"></a>Gebruik voor alle Labs exporteren

Als u de gebruiks gegevens voor meerdere Labs wilt exporteren, moet u 

* [Azure functions](../azure-functions/index.yml), beschikbaar in veel talen, waaronder Power shell, of 
* [Azure Automation runbook](../automation/index.yml), Power shell, python of een aangepaste grafische Designer gebruiken om de export code te schrijven.

Met deze technologieën kunt u de afzonderlijke Lab-exports uitvoeren op alle Labs op een specifieke datum en tijd. 

Uw Azure-functie moet de gegevens pushen naar de langere termijn opslag. Bij het exporteren van gegevens voor meerdere Labs kan het exporteren enige tijd duren. We raden u aan elk lab parallel uit te voeren om de prestaties te verbeteren en de kans op gegevens duplicatie te verminderen. Voer Azure Functions asynchroon uit om parallellisme uit te voeren. Profiteer ook van de timer trigger die Azure Functions aanbieding.

## <a name="using-a-long-term-storage"></a>Lange termijn opslag gebruiken

Een lange termijn opslag consolideert de export gegevens van verschillende Labs naar één gegevens bron. Een ander voor deel van het gebruik van de lange termijn opslag is dat de bestanden van het opslag account kunnen worden verwijderd om dubbele en kosten te verminderen. 

De lange termijn opslag kan worden gebruikt voor het bewerken van tekst, bijvoorbeeld: 

* beschrijvende namen toevoegen
* complexe groeperingen maken
* de gegevens worden samengevoegd.

Enkele veelvoorkomende opslag oplossingen zijn: [SQL Server](https://azure.microsoft.com/services/sql-database/), [Azure data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/)en [Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). U kunt kiezen welke lange termijn opslag oplossing u kiest, afhankelijk van de voor keur. U kunt overwegen om het hulp programma te kiezen, afhankelijk van wat het biedt voor de beschik baarheid van de interactie bij het visualiseren van de gegevens.

## <a name="visualizing-data-and-gathering-insights"></a>Gegevens visualiseren en inzichten verzamelen

Gebruik een hulp programma voor gegevens visualisatie van uw keuze om verbinding te maken met uw lange termijn opslag om de gebruiks gegevens weer te geven en inzichten te verzamelen om de efficiëntie van het gebruik te controleren. [Power bi](/power-bi/power-bi-overview) kan bijvoorbeeld worden gebruikt om de gebruiks gegevens te organiseren en weer te geven. 

U kunt [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) gebruiken om uw resources te maken, te koppelen en te beheren binnen één locatie-interface. Als meer controle nodig is, kan de afzonderlijke resource binnen één resource groep worden gemaakt en onafhankelijk van de Data Factory service worden beheerd.  

## <a name="next-steps"></a>Volgende stappen

Zodra het systeem is ingesteld en gegevens naar de lange termijn opslag worden verplaatst, moet u de volgende stap uitvoeren om te vragen dat de gegevens moeten worden beantwoord. Bijvoorbeeld: 

-   Wat is het gebruik van de VM-grootte?

    Selecteren gebruikers hoge prestaties (meer dure) VM-grootten?
-   Welke Marketplace-installatie kopieën worden gebruikt?

    Aangepaste installatie kopieën zijn de meest voorkomende VM-basis, moet een gemeen schappelijke installatie kopie archief worden gemaakt, zoals de [Galerie met gedeelde afbeeldingen](../virtual-machines/shared-image-galleries.md) of [installatie kopie-Factory](image-factory-create.md).
-   Welke aangepaste installatie kopieën worden er gebruikt of worden niet gebruikt?