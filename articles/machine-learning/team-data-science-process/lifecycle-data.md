---
title: Gegevens verwerving en inzichten van het proces voor gegevens Wetenschappen van teams
description: De doel stellingen, taken en producten voor het verwerving van gegevens en de fase van uw data-Science-projecten
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: fffb52e333bea1b2be11b127a9eab6656dc1d1f5
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94740325"
---
# <a name="data-acquisition-and-understanding-stage-of-the-team-data-science-process"></a>Gegevens verwerving en uitleg van het team data Science process

In dit artikel vindt u een overzicht van de doelen, taken en producten die zijn gekoppeld aan de gegevens verwerving en de fase van het team data Science process (TDSP). Dit proces biedt een aanbevolen levens cyclus die u kunt gebruiken om uw projecten met een gegevens wetenschap te structureren. De levenscyclus bevat de belangrijkste fasen die projecten meestal doorlopen, vaak iteratief:

   1. **Zakelijke inzichten**
   2. **Gegevens verkrijgen en meer informatie**
   3. **Modelleren**
   4. **Implementatie**
   5. **Aanvaarding van de klant**

Hier volgt een visuele representatie van de levens cyclus van TDSP: 

![TDSP levenscyclus](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Doelstellingen
* Maak een schone en hoogwaardige gegevensset waarvan de relatie met de doel variabelen wordt begrepen. Zoek de gegevensset in de juiste analyse omgeving zodat u gereed bent om te model leren.
* Ontwikkel een oplossings architectuur van de gegevens pijplijn waarmee de gegevens regel matig worden vernieuwd en gescoord.

## <a name="how-to-do-it"></a>Hoe u dit doet
Er zijn drie hoofd taken die in deze fase worden behandeld:

   * **De gegevens** opnemen in de doel analyse omgeving.
   * **Verken de gegevens** om te bepalen of de gegevens kwaliteit voldoende is om de vraag te beantwoorden. 
   * **Stel een gegevens pijplijn** in om nieuwe of regel matig vernieuwde gegevens te beoordelen.

### <a name="ingest-the-data"></a>De gegevens opnemen
Stel het proces in om de gegevens van de bron locaties te verplaatsen naar de doel locaties waar u Analytics-bewerkingen uitvoert, zoals training en voor spellingen. Zie [gegevens laden in opslag omgevingen voor analyse](ingest-data.md)voor technische details en opties voor het verplaatsen van gegevens met verschillende Azure-gegevens Services. 

### <a name="explore-the-data"></a>De gegevens verkennen
Voordat u uw modellen traint, moet u een goed idee van de gegevens ontwikkelen. Real-World-gegevens sets zijn vaak ruis, ontbreken waarden of hebben een host van andere verschillen. U kunt gegevens samenvatting en visualisatie gebruiken om de kwaliteit van uw gegevens te controleren en de informatie die u nodig hebt voor het verwerken van de gegevens te verstrekken voordat deze klaar is voor model lering. Dit proces wordt vaak herhaald. Zie [taken om gegevens voor te bereiden voor verbeterde machine learning](prepare-data.md)voor meer informatie over het opschonen van de gegevens.  

Nadat u tevreden bent met de kwaliteit van de gereinigde gegevens, is de volgende stap het beter inzicht te krijgen in de patronen die inherent zijn aan de gegevens. Deze gegevens analyse helpt u bij het kiezen en ontwikkelen van een geschikt voorspellend model voor uw doel. Zoek naar bewijs voor het goed verbonden met de gegevens met het doel. Bepaal vervolgens of er voldoende gegevens zijn om door te gaan met de volgende model stappen. Dit proces wordt vaak herhaald. Mogelijk moet u nieuwe gegevens bronnen met nauw keurigere of meer relevante gegevens vinden om de gegevensset die in de vorige fase is geïdentificeerd, te verbeteren. 

### <a name="set-up-a-data-pipeline"></a>Een gegevens pijplijn instellen
Naast de eerste opname en reiniging van de gegevens moet u doorgaans een proces instellen om nieuwe gegevens te scoren of de gegevens regel matig te vernieuwen als onderdeel van een doorlopend leer proces. Score kan worden voltooid met een gegevens pijplijn of werk stroom. Het [verplaatsen van gegevens van een SQL Server-exemplaar naar Azure SQL database met Azure Data Factory](move-sql-azure-adf.md) artikel geeft een voor beeld van het instellen van een pijp lijn met [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

In deze fase ontwikkelt u een oplossings architectuur van de gegevens pijplijn. U ontwikkelt de pijp lijn parallel met de volgende fase van het data Science-project. Afhankelijk van de behoeften van uw bedrijf en de beperkingen van uw bestaande systemen waarin deze oplossing wordt geïntegreerd, kan de pijp lijn een van de volgende opties zijn: 

   * Op batch gebaseerd
   * Streaming of realtime 
   * Een hybride 

## <a name="artifacts"></a>Artifacts
Hieronder vindt u de producten in deze fase:

   * [Rapport gegevens kwaliteit](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/DataSummaryReport.md): dit rapport bevat gegevens overzichten, de relaties tussen de verschillende kenmerken en doelen, de classificatie van variabelen en meer. 
   * **Oplossings architectuur**: de oplossings architectuur kan een diagram of beschrijving van uw gegevens pijplijn zijn die u gebruikt om scores of voor spellingen uit te voeren op nieuwe gegevens nadat u een model hebt gemaakt. Het bevat ook de pijp lijn voor het opnieuw trainen van uw model op basis van nieuwe gegevens. Sla het document op in de [projectmap](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) wanneer u de TDSP-sjabloon voor de directory structuur gebruikt.
   * **Beslissing van het controle punt**: voordat u begint met het bouwen van het ontwerp van de functie en het model, kunt u het project opnieuw evalueren om te bepalen of de verwachte waarde voldoende is om door te gaan. U kunt bijvoorbeeld klaar zijn om door te gaan, meer gegevens te verzamelen of het project te verlaten omdat de gegevens niet bestaan om de vraag te beantwoorden.

## <a name="next-steps"></a>Volgende stappen

Hier vindt u koppelingen naar elke stap in de levens cyclus van de TDSP:

   1. [Zakelijke inzichten](lifecycle-business-understanding.md)
   2. [Gegevens verkrijgen en meer informatie](lifecycle-data.md)
   3. [Modelleren](lifecycle-modeling.md)
   4. [Implementatie](lifecycle-deployment.md)
   5. [Aanvaarding van de klant](lifecycle-acceptance.md)

We bieden volledige instructies voor het demonstreren van alle stappen in het proces voor specifieke scenario's. Het artikel [voorbeeld](walkthroughs.md) scenario's bevat een lijst met de scenario's met koppelingen en miniaturen. In de scenario's wordt uitgelegd hoe u Cloud, on-premises hulpprogram ma's en services in een werk stroom of pijp lijn kunt combi neren om een intelligente toepassing te maken. 

Zie [de TDSP gebruiken met Azure machine learning]()voor voor beelden van het uitvoeren van stappen in TDSPs die gebruikmaken van Azure machine learning Studio.
