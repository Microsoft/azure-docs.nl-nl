---
title: Implementatie fase van de team data Science proces levenscyclus
description: De doelen, taken en producten voor de implementatie fase van uw data-Science-projecten
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c3bf8e5f81ae7bf35ff34039fa1e81c9fd4a406b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93324544"
---
# <a name="deployment-stage-of-the-team-data-science-process-lifecycle"></a>Implementatie fase van de team data Science proces levenscyclus

In dit artikel vindt u een overzicht van de doelen, taken en producten die zijn gekoppeld aan de implementatie van het team data Science process (TDSP). Dit proces biedt een aanbevolen levens cyclus die u kunt gebruiken om uw projecten met een gegevens wetenschap te structureren. De levenscyclus bevat de belangrijkste fasen die projecten meestal doorlopen, vaak iteratief:

   1. **Zakelijke inzichten**
   2. **Gegevens verkrijgen en meer informatie**
   3. **Modelleren**
   4. **Implementatie**
   5. **Aanvaarding van de klant**

Hier volgt een visuele representatie van de levens cyclus van TDSP: 

![TDSP levenscyclus](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Doel
Implementeer modellen met een gegevens pijplijn naar een productie-of productie omgeving voor eind gebruikers acceptatie. 

## <a name="how-to-do-it"></a>Hoe u dit doet
De belangrijkste taak die in deze fase wordt behandeld:

**Operationeel maken het model**: Implementeer het model en de pijp lijn op een productie-of productie omgeving voor het verbruik van toepassingen.

### <a name="operationalize-a-model"></a>Een model operationeel maken
Nadat u een set modellen hebt die goed pres teren, kunt u deze operationeel maken voor andere toepassingen die u wilt gebruiken. Afhankelijk van de bedrijfs vereisten worden de voor spellingen in realtime of op basis van een batch gemaakt. Als u modellen wilt implementeren, kunt u ze beschikbaar maken met een open API-interface. Met de-interface kan het model eenvoudig worden gebruikt vanuit verschillende toepassingen, zoals:

   * Online websites
   * Spreadsheets 
   * Dashboards
   * Line-of-business-toepassingen 
   * Back-end-toepassingen 

Zie [Deploy a Azure machine learning web service](../classic/deploy-a-machine-learning-web-service.md)(Engelstalig) voor voor beelden van model uitoefening met een Azure machine learning-webservice. Het is een best practice om telemetrie te bouwen en te controleren in het productie model en de gegevens pijplijn die u implementeert. Deze werk wijze helpt bij het volgen van de systeem status rapportage en probleem oplossing.  

## <a name="artifacts"></a>Artifacts

* Een status dashboard waarin de metrische gegevens van de systeem status en de sleutel worden weer gegeven
* Een laatste model rapport met implementatie Details
* Een laatste document van de oplossings architectuur


## <a name="next-steps"></a>Volgende stappen

Hier vindt u koppelingen naar elke stap in de levens cyclus van de TDSP:

   1. [Inzicht in het bedrijf](lifecycle-business-understanding.md)
   2. [Gegevens verkrijgen en meer informatie](lifecycle-data.md)
   3. [Modelleren](lifecycle-modeling.md)
   4. [Implementatie](lifecycle-deployment.md)
   5. [Aanvaarding van de klant](lifecycle-acceptance.md)

We bieden volledige instructies voor het demonstreren van alle stappen in het proces voor specifieke scenario's. Het artikel [voorbeeld](walkthroughs.md) scenario's bevat een lijst met de scenario's met koppelingen en miniaturen. In de scenario's wordt uitgelegd hoe u Cloud, on-premises hulpprogram ma's en services in een werk stroom of pijp lijn kunt combi neren om een intelligente toepassing te maken. 

Zie [de TDSP gebruiken met Azure machine learning](./index.yml)voor voor beelden van het uitvoeren van stappen in TDSPs die gebruikmaken van Azure machine learning Studio.