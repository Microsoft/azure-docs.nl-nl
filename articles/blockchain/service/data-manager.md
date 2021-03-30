---
title: Wat is Block Chain Data Manager voor de Azure Block Chain-Service
description: Block Chain Data Manager om Block Chain-gegevens vast te leggen, te transformeren en te leveren aan Event Grid onderwerpen.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: chroyal
ms.openlocfilehash: 78c93880007259267b26cf53e93de722be1c7323
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "77209440"
---
# <a name="what-is-blockchain-data-manager-for-azure-blockchain-service"></a>Wat is Block Chain Data Manager voor Azure Blockchain Service?

Block Chain Data Manager legt, transformeert en levert Azure Block Chain Service-transactie gegevens op Azure Event Grid onderwerpen met betrouw bare en schaal bare Block Chain-integratie met Azure-Services.

In de meeste Block Chain-scenario's voor ondernemingen is een Block Chain-boek een onderdeel van een oplossing. Als u bijvoorbeeld een Asset van de ene naar de andere entiteit wilt overzetten, hebt u een mechanisme nodig om de trans actie te verzenden. U hebt vervolgens een mechanisme nodig voor het lezen van grootboek gegevens om te controleren of de trans actie is uitgevoerd, is geaccepteerd en de resulterende status wijzigingen worden vervolgens geïntegreerd met uw end-to-end-oplossing. Als u in dit voor beeld een slim contract schrijft voor het overdragen van assets, kunt u Block Chain-Data Manager gebruiken om toepassingen en gegevens archieven uit de keten te integreren. Voor het voor beeld van de Asset Transfer worden wijzigingen in de status van gebeurtenissen en eigenschappen door Block Chain Data Manager via Event Grid door gegeven wanneer een activum wordt overgebracht naar de Block chain. U kunt vervolgens meerdere gebeurtenis-handlers voor Event Grid gebruiken om Block Chain gegevens uit de keten op te slaan of te reageren op status wijzigingen in realtime.

Block Chain Data Manager voert drie hoofd functies uit: vastleggen, transformeren en leveren.

![Data Manager functies Block Chain](./media/data-manager/functions.png)

## <a name="capture"></a>Vastleggen

Elk block Chain-Data Manager exemplaar maakt verbinding met één micro Block Chain trans actie-knoop punt in de service. Alleen gebruikers met toegang tot het transactie knooppunt kunnen een verbinding maken die ervoor zorgt dat de klant gegevens goed toegankelijk zijn. Een Block Chain Data Manager instantie beveiligt op een betrouw bare manier alle onbewerkte blok-en ruwe transactie gegevens van het trans actie knooppunt en kan worden geschaald om de werk belasting van de onderneming

## <a name="transform"></a>Transformeren

U kunt Block Chain Data Manager gebruiken om de status van gebeurtenissen en eigenschappen te decoderen door slimme contract toepassingen te configureren in Block Chain Data Manager. Als u een slim contract wilt toevoegen, geeft u de ABI en byte code van het contract op. Block Chain Data Manager gebruikt de slimme contract artefacten om contract adressen te decoderen en te detecteren. Nadat de Block Chain-toepassing aan het exemplaar is toegevoegd, detecteert Block Chain Data Manager dynamisch het adres van het slimme contract wanneer het slimme contract wordt geïmplementeerd in het consortium en wordt de gedecodeerde gebeurtenis en eigenschaps status naar geconfigureerde bestemmingen verzonden.

## <a name="deliver"></a>Aanbieden

Block Chain Data Manager ondersteunt meerdere uitgaande verbindingen van Event Grid onderwerp voor een gegeven block Chain Data Manager exemplaar. U kunt Block Chain-gegevens naar één bestemming verzenden of block Chain-gegevens verzenden naar meerdere bestemmingen. Met block Chain Data Manager kunt u een schaal bare oplossing voor gegevens publicatie op basis van gebeurtenissen maken voor elke Block Chain-implementatie.

## <a name="configuration-options"></a>Configuratie-opties

U kunt Block Chain Data Manager configureren om te voldoen aan de behoeften van uw oplossing. U kunt bijvoorbeeld het volgende inrichten:

* Eén Block Chain Data Manager-exemplaar voor een lid van de Azure Block Chain-service.
* Een Block Chain Data Manager instantie per Azure Block Chain Service-transactie knooppunt. Persoonlijke transactie knooppunten kunnen bijvoorbeeld hun eigen Block Chain Data Manager instantie hebben om de vertrouwelijkheid te behouden.
* Een Block Chain Data Manager-exemplaar kan meerdere uitvoer verbindingen ondersteunen. Een Block Chain Data Manager-exemplaar kan worden gebruikt voor het beheren van alle integratie punten voor gegevens publicatie voor een lid van de Azure Block Chain-service.

## <a name="next-steps"></a>Volgende stappen

Maak [een Block Chain-Data Manager-exemplaar](data-manager-portal.md) voor een lid van de Azure Block Chain-service.
