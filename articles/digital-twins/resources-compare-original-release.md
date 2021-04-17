---
title: Verschillen met de oorspronkelijke release
titleSuffix: Azure Digital Twins
description: Meer informatie over wat er is gewijzigd in de nieuwe versie van Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 1/28/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 2bfd36b79ad800c14a68948aa8488e842d3f4def
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481171"
---
# <a name="what-is-the-new-azure-digital-twins-how-is-it-different-from-the-original-version-2018"></a>Wat is het nieuwe Azure Digital Twins? Wat is het verschil met de oorspronkelijke versie (2018)?

De eerste openbare preview van Azure Digital Twins is uitgebracht in oktober 2018. Hoewel de belangrijkste concepten van die oorspronkelijke versie zijn doorvoering naar de huidige service, zijn veel van de interfaces en implementatiedetails gewijzigd om de service flexibeler en toegankelijker te maken. Deze wijzigingen zijn gebaseerd op de feedback van klanten.

> [!IMPORTANT]
> In het licht van de uitgebreide mogelijkheden van de nieuwe service, is de oorspronkelijke Azure Digital Twins service is gestopt. Vanaf januari 2021 zijn de API's en bijbehorende gegevens niet meer beschikbaar.

Als u de eerste versie van Azure Digital Twins hebt gebruikt tijdens de eerste openbare preview, gebruikt u de informatie en best practices in dit artikel om te leren werken met de huidige service en te profiteren van de functies ervan.

## <a name="differences-by-topic"></a>Verschillen per onderwerp

De onderstaande grafiek biedt een naast elkaar weergegeven weergave van concepten die zijn gewijzigd tussen de oorspronkelijke versie van de service en de huidige service.

| Onderwerp | In de oorspronkelijke versie | In de huidige versie |
| --- | --- | --- | --- |
| **Modelleren**<br>*Flexibeler* | De oorspronkelijke release is ontworpen rond slimme ruimten en heeft dus een ingebouwde woordenlijst voor gebouwen. | De huidige Azure Digital Twins is domein-agnostisch. U kunt uw eigen aangepaste woordenlijst en modellen voor uw oplossing definiëren om meer soorten omgevingen op flexibele manieren weer te geven.<br><br>Meer informatie in [*Concepten: Aangepaste modellen*](concepts-models.md). |
| **Topologie**<br>*Flexibeler*| De oorspronkelijke release ondersteunde een structuur van structuurgegevens, afgestemd op slimme ruimten. Digitale apparaatdubbels werden verbonden met hiërarchische relaties. | Met de huidige versie kunnen uw digitale tweelingen worden verbonden met willekeurige graafologieën, geordend zoals u wilt. Zo beschikt u over meer flexibiliteit voor het uitdrukken van de complexe relaties uit de echte wereld.<br><br>Meer informatie in [*Concepten: Digitale apparaatdubbels en de dubbele grafiek*](concepts-twins-graph.md). |
| **Compute**<br>*Rijker, flexibeler* | In de oorspronkelijke release is logica voor het verwerken van gebeurtenissen en telemetrie gedefinieerd in door de gebruiker gedefinieerde JavaScript-functies (UDF's). Foutopsporing met UDF's was beperkt. | De huidige release heeft een open rekenmodel: u biedt aangepaste logica door externe rekenresources te [koppelen, zoals Azure Functions](../azure-functions/functions-overview.md). Op die manier kunt u een programmeertaal naar keuze gebruiken, zonder beperkingen aangepaste codebibliotheken openen en profiteren van de ontwikkeling en foutopsporing van resources die de externe service mogelijk heeft.<br><br>Meer informatie in [*Instructies: Een Azure-functie instellen voor gegevensverwerking*](how-to-create-azure-function.md). |
| **Apparaatbeheer met IoT Hub**<br>*Toegankelijker* | De oorspronkelijke release beheerde apparaten met een exemplaar [van IoT Hub](../iot-hub/about-iot-hub.md) die intern was voor de Azure Digital Twins service. Deze geïntegreerde hub was niet volledig toegankelijk voor ontwikkelaars. | In de huidige release gaat u uw eigen IoT-hub gebruiken door een onafhankelijk gemaakte IoT Hub-instantie te koppelen (samen met alle apparaten die al worden beheert). Hiermee hebt u volledige toegang tot de mogelijkheden van IoT Hub en hebt u controle over het beheer van apparaten.<br><br>Meer informatie in [*Instructies: Telemetrie opnemen vanuit IoT Hub*](how-to-ingest-iot-hub-data.md). |
| **Beveiliging**<br>*Meer standaard* | De oorspronkelijke release had vooraf gedefinieerde rollen die u kunt gebruiken om de toegang tot uw exemplaar te beheren. | De huidige versie is geïntegreerd met dezelfde [Azure RBAC-back-endservice (op](../role-based-access-control/overview.md) rollen gebaseerd toegangsbeheer) die andere Azure-services gebruiken. Hierdoor is het gemakkelijker om te verifiëren tussen andere Azure-Services in uw oplossing, zoals IoT Hub, Azure Functions, Event Grid en meer.<br>Met RBAC kunt u nog steeds vooraf gedefinieerde rollen gebruiken, maar u kunt ook aangepaste rollen maken en configureren.<br><br>Meer informatie in [*Concepten: Beveiliging voor Azure Digital Twins-oplossingen*](concepts-security.md). |
| **Schaalbaarheid**<br>*Groter* | De oorspronkelijke release had schaalbeperkingen voor apparaten, berichten, grafieken en schaaleenheden. Er werd slechts één instantie van Azure Digital Twins ondersteund per abonnement.  | De huidige versie is afhankelijk van een nieuwe architectuur met verbeterde schaalbaarheid en heeft meer rekenkracht. Bovendien worden 10 instanties per regio, per abonnement ondersteund.<br><br>Zie [*Azure Digital Twins servicelimieten*](reference-service-limits.md) voor meer informatie over de limieten in de huidige release. |

## <a name="service-limits"></a>Servicelimieten

Zie Servicelimieten voor Azure Digital Twins een [*lijst Azure Digital Twins limieten.*](reference-service-limits.md)

## <a name="next-steps"></a>Volgende stappen

* Ga aan de slag met de huidige release in de [*quickstart: Quickstart: Een voorbeeldscenario verkennen.*](quickstart-azure-digital-twins-explorer.md)

* U kunt ook beginnen met het lezen van belangrijke concepten [*met Concepten: Aangepaste modellen.*](concepts-models.md)