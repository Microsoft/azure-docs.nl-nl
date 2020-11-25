---
title: Gegevens beveiliging in Azure Stream Analytics
description: In dit artikel wordt uitgelegd hoe u uw persoonlijke gegevens versleutelt die worden gebruikt door een Azure Stream Analytics-taak.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/23/2020
ms.openlocfilehash: 72566987068729efef4310ce145c30584c4895b0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011401"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Gegevens beveiliging in Azure Stream Analytics 

Azure Stream Analytics is een volledig beheerd platform-as-a-service waarmee u realtime analyse pijplijnen kunt maken. Alle zware hijsing, zoals het inrichten van clusters, het schalen van knoop punten voor uw gebruik en het beheer van interne controle punten, wordt beheerd achter de schermen.

## <a name="private-data-assets-that-are-stored"></a>Privé gegevens assets die zijn opgeslagen

Azure Stream Analytics blijven de volgende meta gegevens en gegevens behouden om uit te voeren: 

* Query definitie en de bijbehorende configuratie  

* Door de gebruiker gedefinieerde functies of aggregaties  

* Controle punten die nodig zijn voor de Stream Analytics runtime

* Moment opnamen van referentie gegevens 

* Verbindings Details van de resources die door uw Stream Analytics-taak worden gebruikt

U kunt meer te weten komen over de [nalevings aanbiedingen van micro soft](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)om u te helpen te voldoen aan uw nalevings verplichtingen in een gereguleerde branche of omgeving. 

## <a name="in-region-data-residency"></a>In-Region data locatie
Azure Stream Analytics klant gegevens en andere meta gegevens die hierboven worden beschreven, worden opgeslagen. Klant gegevens worden standaard opgeslagen door Azure Stream Analytics in één regio, zodat deze service automatisch voldoet aan de vereisten van de regio gegevens locatie, inclusief die in het [vertrouwens centrum](https://azuredatacentermap.azurewebsites.net/).
Daarnaast kunt u ervoor kiezen om alle gegevensassets (klant gegevens en andere meta gegevens) op te slaan die betrekking hebben op uw stream Analytics-taak in één regio door ze te versleutelen in een opslag account van uw keuze.

## <a name="encrypt-your-data"></a>Uw gegevens versleutelen

Stream Analytics maakt automatisch gebruik van de best preclass Encryption Standards in de infra structuur om uw gegevens te versleutelen en te beveiligen. U kunt Stream Analytics alleen vertrouwen om al uw gegevens veilig op te slaan, zodat u zich geen zorgen hoeft te maken over het beheer van de infra structuur.

Als u door de klant beheerde sleutels (CMK) wilt gebruiken om uw gegevens te versleutelen, kunt u uw eigen opslag account (algemeen gebruik v1 of v2) gebruiken voor het opslaan van privé gegevensassets die vereist zijn voor de Stream Analytics runtime. Uw opslag account kan naar behoefte worden versleuteld. Geen van uw privé gegevensassets wordt permanent opgeslagen door de Stream Analytics-infra structuur. 

Deze instelling moet worden geconfigureerd op het moment dat de taak wordt gemaakt Stream Analytics en kan niet worden gewijzigd tijdens de levens cyclus van de taak. Het wijzigen of verwijderen van opslag die door uw Stream Analytics wordt gebruikt, wordt niet aanbevolen. Als u uw opslag account verwijdert, verwijdert u alle persoonlijke gegevensassets definitief, waardoor uw taak niet kan worden uitgevoerd. 

Het is niet mogelijk om sleutels voor uw opslag account bij te werken of te roteren met behulp van de Stream Analytics Portal. U kunt de sleutels bijwerken met behulp van de REST-Api's.


### <a name="configure-storage-account-for-private-data"></a>Opslag account voor privé gegevens configureren 


Versleutel uw opslag account om al uw gegevens te beveiligen en de locatie van uw privé gegevens expliciet te kiezen. 

U kunt meer te weten komen over de [nalevings aanbiedingen van micro soft](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)om u te helpen te voldoen aan uw nalevings verplichtingen in een gereguleerde branche of omgeving. 



Gebruik de volgende stappen om uw opslag account te configureren voor privé gegevensassets. Deze configuratie wordt uitgevoerd vanuit uw Stream Analytics-taak, niet vanuit uw opslag account.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Selecteer in de linkerbovenhoek van Azure Portal **Een resource maken**. 

1. Selecteer **analyse**   >  **Stream Analytics taak**   in de lijst met resultaten. 

1. Vul de pagina Stream Analytics-taak in met de benodigde gegevens, zoals naam, regio en schaal. 

1. Schakel het selectie vakje in dat alle privé gegevensassets die *nodig zijn voor deze taak in mijn opslag account beveiligd*.

1. Selecteer een opslag account in uw abonnement. Houd er rekening mee dat deze instelling tijdens de levens cyclus van de taak niet kan worden gewijzigd. 

   ![Instellingen voor het opslag account voor privé gegevens](./media/data-protection/storage-account-create.png)

## <a name="private-data-assets-that-are-stored-by-stream-analytics"></a>Privé gegevensassets die zijn opgeslagen door Stream Analytics

Persoonlijke gegevens die door Stream Analytics moeten worden bewaard, worden opgeslagen in uw opslag account. Voor beelden van persoonlijke gegevens assets zijn: 

* Query's die u hebt gemaakt en de bijbehorende configuraties  

* Door de gebruiker gedefinieerde functies 

* Controle punten die nodig zijn voor de Stream Analytics runtime

* Moment opnamen van referentie gegevens 

De verbindings Details van uw resources, die worden gebruikt door uw Stream Analytics-taak, worden ook opgeslagen. Versleutel uw opslag account om al uw gegevens te beveiligen. 

U kunt meer te weten komen over de [nalevings aanbiedingen van micro soft](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)om u te helpen te voldoen aan uw nalevings verplichtingen in een gereguleerde branche of omgeving. 

## <a name="enables-data-residency"></a>Hiermee worden gegevens locatie 
U kunt deze functie gebruiken om eventuele vereisten voor gegevens locatie af te dwingen door een opslag account dienovereenkomstig op te geven.

## <a name="known-issues"></a>Bekende problemen
Er is een bekend probleem waarbij een taak die door de klant beheerde sleutel wordt gebruikt, wordt uitgevoerd bij het gebruik van beheerde identiteit om te verifiëren bij invoer of uitvoer. 

## <a name="next-steps"></a>Volgende stappen

* [Een Azure Storage-account maken](../storage/common/storage-account-create.md)
* [Wat is invoer van Azure Stream Analytics?](stream-analytics-add-inputs.md)
* [Controle punten en concepten voor opnieuw afspelen in Azure Stream Analytics taken](stream-analytics-concepts-checkpoint-replay.md)
* [Referentie gegevens gebruiken voor Zoek opdrachten in Stream Analytics](stream-analytics-use-reference-data.md)
