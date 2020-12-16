---
title: Verschillen van de eerste release
titleSuffix: Azure Digital Twins
description: Meer informatie over wat er is gewijzigd in de nieuwe versie van Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: fb0d3e3c57a26f7ca14b74edc42cb657ba6074c3
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97604993"
---
# <a name="what-is-the-new-azure-digital-twins-how-is-it-different-from-the-previous-version-2018"></a>Wat is het nieuwe Azure Digital Twins? Wat is het verschil met de vorige versie (2018)?

De eerste openbare preview van Azure Digital Twins is uitgebracht in oktober 2018. Hoewel de basis concepten van die eerste versie naar de nieuwe service zijn uitgevoerd, zijn veel van de interfaces en implementatie gegevens gewijzigd om de service flexibeler en toegankelijker te maken. Deze wijzigingen zijn gebaseerd op de feedback van klanten.

> [!IMPORTANT]
> In het licht van de uitgebreide mogelijkheden van de nieuwe service wordt de vorige Azure Digital Apparaatdubbels-service buiten gebruik gesteld, met de Api's en de bijbehorende gegevens die niet meer beschikbaar zijn vanaf januari 18, 2021.

Als u de eerste versie van Azure Digital Apparaatdubbels hebt gebruikt tijdens de eerste open bare preview, gebruikt u de informatie en best practices in dit artikel om te leren hoe u met de nieuwe service kunt werken en kunt u profiteren van de functies.

## <a name="differences-by-topic"></a>Verschillen per onderwerp

In de onderstaande grafiek ziet u een weergave van concepten naast elkaar die zijn gewijzigd tussen de vorige versie van de service en de nieuwe (huidige) service.

| Onderwerp | In vorige versie | In nieuwe versie |
| --- | --- | --- | --- |
| **Modelleren**<br>*Flexibeler* | De vorige versie is ontworpen rond slimme ruimten en heeft een ingebouwde woordenlijst voor gebouwen. | De nieuwe Azure Digital Twins heeft geen domein nodig. U kunt uw eigen aangepaste woordenlijst en modellen voor uw oplossing definiëren om meer soorten omgevingen op flexibele manieren weer te geven.<br><br>Meer informatie in [*Concepten: Aangepaste modellen*](concepts-models.md). |
| **Topologie**<br>*Flexibeler*| In de vorige versie werd een gegevensstructuur ondersteund die is afgestemd op slimme ruimten. Digitale apparaatdubbels werden verbonden met hiërarchische relaties. | In de nieuwe versie kunnen uw digitale apparaatdubbels worden verbonden met willekeurige grafiektopologieën die u naar wens kunt inrichten. Zo beschikt u over meer flexibiliteit voor het uitdrukken van de complexe relaties uit de echte wereld.<br><br>Meer informatie in [*Concepten: Digitale apparaatdubbels en de dubbele grafiek*](concepts-twins-graph.md). |
| **Compute**<br>*Rijker, flexibeler* | In de vorige versie werd logica voor het verwerken van gebeurtenissen en telemetrie gedefinieerd in JavaScript-UDF's (door de gebruiker gedefinieerde functies). Foutopsporing met UDF's was beperkt. | De nieuwe release heeft een open rekenmodel: u kunt aangepaste logica opgeven door externe rekenresources te koppelen, zoals [Azure Functions](../azure-functions/functions-overview.md). Op die manier kunt u een programmeertaal naar keuze gebruiken, zonder beperkingen aangepaste codebibliotheken openen en profiteren van de ontwikkeling en foutopsporing van resources die de externe service mogelijk heeft.<br><br>Meer informatie in [*Instructies: Een Azure-functie instellen voor gegevensverwerking*](how-to-create-azure-function.md). |
| **Apparaatbeheer met IoT Hub**<br>*Toegankelijker* | De vorige versie beheerde apparaten met een instantie van [IoT Hub](../iot-hub/about-iot-hub.md) die intern was voor de Azure Digital Twins-service. Deze geïntegreerde hub was niet volledig toegankelijk voor ontwikkelaars. | In de nieuwe versie moet u uw eigen IoT-hub meenemen door een afzonderlijk gemaakte IoT Hub-instantie (samen met de apparaten die al worden beheerd) te koppelen. Hiermee hebt u volledige toegang tot de mogelijkheden van IoT Hub en hebt u controle over het beheer van apparaten.<br><br>Meer informatie in [*Instructies: Telemetrie opnemen vanuit IoT Hub*](how-to-ingest-iot-hub-data.md). |
| **Beveiliging**<br>*Meer standaard* | De vorige versie bevatte vooraf gedefinieerde rollen die u kon gebruiken om de toegang tot uw instantie te beheren. | De nieuwe versie integreert met dezelfde [Azure RBAC (op rollen gebaseerd toegangsbeheer)](../role-based-access-control/overview.md) back-endservice die door andere Azure-services wordt gebruikt. Hierdoor is het gemakkelijker om te verifiëren tussen andere Azure-Services in uw oplossing, zoals IoT Hub, Azure Functions, Event Grid en meer.<br>Met RBAC kunt u nog steeds vooraf gedefinieerde rollen gebruiken, maar u kunt ook aangepaste rollen maken en configureren.<br><br>Meer informatie in [*Concepten: Beveiliging voor Azure Digital Twins-oplossingen*](concepts-security.md). |
| **Schaalbaarheid**<br>*Groter* | De vorige versie had schaalbeperkingen voor apparaten, berichten, grafieken en schaaleenheden. Er werd slechts één instantie van Azure Digital Twins ondersteund per abonnement.  | De nieuwe versie is gebaseerd op een nieuwe architectuur met verbeterde schaalbaarheid en heeft meer rekenkracht. Bovendien worden 10 instanties per regio, per abonnement ondersteund.<br><br>Zie [*Naslag informatie: Service limieten*](reference-service-limits.md) voor meer informatie over de limieten in de huidige release. |

## <a name="service-limits"></a>Servicelimieten

Voor een lijst met beperkingen van Azure Digital Twins, raadpleegt u [*Referentie: servicelimieten*](reference-service-limits.md).

## <a name="next-steps"></a>Volgende stappen

Ga nu direct aan de slag met Azure Digital Twins door de eerste zelfstudie te volgen:

[*Zelfstudie: Een client-app coderen*](tutorial-code.md)