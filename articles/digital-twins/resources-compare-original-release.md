---
title: Verschillen van de oorspronkelijke release
titleSuffix: Azure Digital Twins
description: Meer informatie over wat er is gewijzigd in de nieuwe versie van Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 1/28/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 8010581667354f2e8484bc7341227ec41713d10f
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072633"
---
# <a name="what-is-the-new-azure-digital-twins-how-is-it-different-from-the-original-version-2018"></a>Wat is het nieuwe Azure Digital Twins? Wat is het verschil met de oorspronkelijke versie (2018)?

De eerste openbare preview van Azure Digital Twins is uitgebracht in oktober 2018. Hoewel de basis concepten van die oorspronkelijke versie naar de huidige service zijn overgedragen, zijn veel van de interfaces en implementatie gegevens gewijzigd om de service flexibeler en toegankelijker te maken. Deze wijzigingen zijn gebaseerd op de feedback van klanten.

> [!IMPORTANT]
> In het licht van de uitgebreide mogelijkheden van de nieuwe service is de oorspronkelijke Azure Digital Apparaatdubbels-service buiten gebruik gesteld. Vanaf januari 2021 zijn de Api's en de bijbehorende gegevens niet meer beschikbaar.

Als u de eerste versie van Azure Digital Apparaatdubbels hebt gebruikt tijdens de eerste open bare preview, gebruikt u de informatie en best practices in dit artikel om te leren hoe u met de huidige service kunt werken en kunt u profiteren van de functies.

## <a name="differences-by-topic"></a>Verschillen per onderwerp

In de onderstaande grafiek ziet u een gelijktijdige weer gave van concepten die zijn gewijzigd tussen de oorspronkelijke versie van de service en de huidige service.

| Onderwerp | In oorspronkelijke versie | In huidige versie |
| --- | --- | --- | --- |
| **Modelleren**<br>*Flexibeler* | De oorspronkelijke release was ontworpen rond slimme Spaces, zodat het een ingebouwde vocabulaire voor gebouwen is. | De huidige Azure Digital Apparaatdubbels is domein-neutraal. U kunt uw eigen aangepaste woordenlijst en modellen voor uw oplossing definiëren om meer soorten omgevingen op flexibele manieren weer te geven.<br><br>Meer informatie in [*Concepten: Aangepaste modellen*](concepts-models.md). |
| **Topologie**<br>*Flexibeler*| De oorspronkelijke release ondersteunt een structuur gegevens structuur die is afgestemd op slimme ruimten. Digitale apparaatdubbels werden verbonden met hiërarchische relaties. | Met de huidige versie kan uw digitale apparaatdubbels worden aangesloten op wille keurige grafiek topologieën, geordend, zoals u wilt. Zo beschikt u over meer flexibiliteit voor het uitdrukken van de complexe relaties uit de echte wereld.<br><br>Meer informatie in [*Concepten: Digitale apparaatdubbels en de dubbele grafiek*](concepts-twins-graph.md). |
| **Compute**<br>*Rijker, flexibeler* | In de oorspronkelijke release is logica voor het verwerken van gebeurtenissen en telemetrie gedefinieerd in door de gebruiker gedefinieerde Java script-functies (Udf's). Foutopsporing met UDF's was beperkt. | De huidige release heeft een open Compute-model: u kunt aangepaste logica opgeven door externe reken resources te koppelen, zoals [Azure functions](../azure-functions/functions-overview.md). Op die manier kunt u een programmeertaal naar keuze gebruiken, zonder beperkingen aangepaste codebibliotheken openen en profiteren van de ontwikkeling en foutopsporing van resources die de externe service mogelijk heeft.<br><br>Meer informatie in [*Instructies: Een Azure-functie instellen voor gegevensverwerking*](how-to-create-azure-function.md). |
| **Apparaatbeheer met IoT Hub**<br>*Toegankelijker* | De oorspronkelijke release beheerde apparaten met een exemplaar van [IOT hub](../iot-hub/about-iot-hub.md) dat intern was voor de Azure Digital apparaatdubbels-service. Deze geïntegreerde hub was niet volledig toegankelijk voor ontwikkelaars. | In de huidige versie kunt u uw eigen IoT-hub meenemen door een afzonderlijk gemaakte IoT Hub-exemplaar (samen met alle apparaten die al worden beheerd) te koppelen. Hiermee hebt u volledige toegang tot de mogelijkheden van IoT Hub en hebt u controle over het beheer van apparaten.<br><br>Meer informatie in [*Instructies: Telemetrie opnemen vanuit IoT Hub*](how-to-ingest-iot-hub-data.md). |
| **Beveiliging**<br>*Meer standaard* | De oorspronkelijke release bevat vooraf gedefinieerde rollen die u kunt gebruiken om de toegang tot uw exemplaar te beheren. | De huidige release kan worden geïntegreerd met de back-end-service [op basis van op rollen gebaseerd toegangs beheer (Azure RBAC)](../role-based-access-control/overview.md) die door andere Azure-Services wordt gebruikt. Hierdoor is het gemakkelijker om te verifiëren tussen andere Azure-Services in uw oplossing, zoals IoT Hub, Azure Functions, Event Grid en meer.<br>Met RBAC kunt u nog steeds vooraf gedefinieerde rollen gebruiken, maar u kunt ook aangepaste rollen maken en configureren.<br><br>Meer informatie in [*Concepten: Beveiliging voor Azure Digital Twins-oplossingen*](concepts-security.md). |
| **Schaalbaarheid**<br>*Groter* | De oorspronkelijke release had schaal beperkingen voor apparaten, berichten, grafieken en schaal eenheden. Er werd slechts één instantie van Azure Digital Twins ondersteund per abonnement.  | De huidige versie is afhankelijk van een nieuwe architectuur met verbeterde schaal baarheid en heeft meer reken kracht. Bovendien worden 10 instanties per regio, per abonnement ondersteund.<br><br>Zie de [*Azure Digital apparaatdubbels-service limieten*](reference-service-limits.md) voor meer informatie over de limieten in de huidige release. |

## <a name="service-limits"></a>Servicelimieten

Zie [*Azure Digital apparaatdubbels-service limieten*](reference-service-limits.md)voor een lijst met Azure Digital apparaatdubbels-limieten.

## <a name="next-steps"></a>Volgende stappen

* Kijk eens naar het werken met de huidige release in Quick Start: [*Quick Start: een voorbeeld scenario verkennen*](quickstart-adt-explorer.md).

* Of begin met het lezen van belang rijke concepten met [*concepten: aangepaste modellen*](concepts-models.md).