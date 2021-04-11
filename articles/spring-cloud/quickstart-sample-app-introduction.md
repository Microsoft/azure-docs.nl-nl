---
title: 'Quickstart: Inleiding tot de voorbeeld-app - Azure Spring Cloud'
description: Beschrijft de voorbeeld-app die wordt gebruikt in deze reeks quickstarts voor implementatie in Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: dd36bb18e84ea299195b77286887a3b279f81469
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104879284"
---
# <a name="introduction-to-the-sample-app"></a>Inleiding tot de voorbeeld-app

::: zone pivot="programming-language-csharp"
In deze reeks quickstarts wordt een voorbeeld-app gebruikt die bestaat uit twee microservices om te laten zien hoe u een .NET Core Steeltoe-app kunt implementeren in de Azure Spring Cloud-service. U gebruikt Azure Spring Cloud-functies zoals servicedetectie, configuratieserver, logboeken, metrische gegevens en gedistribueerde tracering.

## <a name="functional-services"></a>Functionele services

De voorbeeld-app bestaat uit twee microservices:

* De service `planet-weather-provider` retourneert een weerbericht in reactie op een HTTP-aanvraag waarin de naam van de planeet is opgegeven. Het kan bijvoorbeeld 'zeer warm' retourneren voor de planeet Mercurius. Hiermee worden de weergegevens van de configuratieserver opgehaald. De configuratieserver haalt de weergegevens op uit een YAML-bestand in een Git-opslagplaats, bijvoorbeeld:

  ```yaml
  MercuryWeather: very warm
  VenusWeather: quite unpleasant
  MarsWeather: very cool
  SaturnWeather: a little bit sandy
  ```

* De service `solar-system-weather` retourneert gegevens voor vier planeten in reactie op een HTTP-aanvraag. De gegevens worden opgehaald door vier HTTP-aanvragen te verzenden naar `planet-weather-provider`. Er wordt gebruikgemaakt van de Eureka-serverdetectieservice om `planet-weather-provider` aan te roepen. Deze retourneert JSON, bijvoorbeeld:

  ```json
  [{
      "Key": "Mercury",
      "Value": "very warm"
  }, {
      "Key": "Venus",
      "Value": "quite unpleasant"
  }, {
      "Key": "Mars",
      "Value": "very cool"
  }, {
      "Key": "Saturn",
      "Value": "a little bit sandy"
  }]
  ```

In het volgende diagram ziet u de architectuur van de voorbeeld-app:

:::image type="content" source="media/spring-cloud-quickstart-sample-app-introduction/sample-app-diagram.png" alt-text="Diagram van de voorbeeld-app":::

## <a name="code-repository"></a>Codeopslagplaats

De voorbeeld-app bevindt zich in de map [steeltoe-sample](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample) van de opslagplaats Azure-Samples/Azure-Spring-Cloud-Samples op GitHub.

De instructies in de volgende quickstarts verwijzen naar de broncode waar nodig.

::: zone-end

::: zone pivot="programming-language-java"
In deze quickstart gebruiken we een voorbeeld van persoonlijk financiën, genaamd PiggyMetrics, om te laten zien hoe u een app kunt implementeren in de Azure Spring Cloud-service. PiggyMetrics laat het architectuurpatroon van de microservice zien en markeert de uitsplitsing van de services. U krijgt te zien hoe dit wordt geïmplementeerd in Azure met krachtige Azure Spring Cloud-mogelijkheden, waaronder servicedetectie, configuratieserver, logboeken, metrische gegevens, en gedistribueerde tracering.

Als u de voorbeelden van Azure Spring Cloud-implementatie wilt volgen, hebt u alleen de locatie van de broncode nodig. Indien nodig wordt deze locatie opgegeven.

## <a name="functional-services"></a>Functionele services

PiggyMetrics bestaat uit drie kernmicroservices. Dit zijn allemaal toepassingen die onafhankelijk kunnen worden geïmplementeerd, georganiseerd op bedrijfsdomein.

* **Rekeningservice (te implementeren)** : Bevat algemene logica en validatie voor gebruikersinvoer: inkomsten/uitgaven, spaargeld, en instellingen voor rekeningen.
* **Statistiekservice (niet gebruikt in deze quickstart)** : Hiermee worden berekeningen uitgevoerd op belangrijke statistische parameters en tijdreeksen vastgelegd voor elke rekening. Gegevenspunt bevat waarden, genormaliseerd naar basisvaluta en tijdsperiode. Deze gegevens worden gebruikt om de geldstroomdynamiek voor de levensduur van een rekening bij te houden.
* **Meldingsservice (niet gebruikt in deze quickstart)** : Hiermee worden contactgegevens en meldingsinstellingen voor gebruikers opgeslagen, zoals de frequentie van herinneringen en back-ups. Geplande werknemer verzamelt de vereiste gegevens van andere services en verzendt e-mailberichten naar geabonneerde klanten.

## <a name="infrastructure-services"></a>Infrastructuurservices

Er zijn verschillende algemene patronen in gedistribueerde systemen die helpen bij de werking van kernservices. Azure Spring Cloud biedt krachtige hulpmiddelen die het gedrag van Spring Boot-toepassingen verbeteren om deze patronen te implementeren: 

* **Configuratieservice (gehost met Azure Spring Cloud)** : Azure Spring Cloud-configuratie is een gecentraliseerde configuratieservice voor gedistribueerde systemen die horizontaal kan worden geschaald. De service maakt gebruik van een pluggable opslagplaats die momenteel ondersteuning biedt voor lokale opslag, Git en Subversion.
* **Servicedetectie (gehost met Azure Spring Cloud)** : Staat automatische detectie van netwerklocaties toe voor service-exemplaren die dynamisch toegewezen adressen kunnen hebben vanwege automatisch schalen, fouten en upgrades.
* **Verificatieservice (te implementeren)** Autorisatieverantwoordelijkheden worden volledig uitgepakt op een afzonderlijke server, die OAuth2-tokens verleent voor de back-endresourceservices. Verificatieserver zorgt voor gebruikersautorisatie en beveiligde communicatie tussen machines binnen een perimeter.
* **API-gateway (te implementeren)** : De drie kernservices maken een externe API beschikbaar voor de client. In de praktijk kan het aantal functies in een systeem zeer snel toenemen naarmate een systeem complexer wordt. Er kunnen honderden services betrokken zijn bij het weergeven van één complexe webpagina. De API-gateway is een enkel ingangspunt in het systeem, dat wordt gebruikt om aanvragen af te handelen en te routeren naar de juiste back-endservice, of om meerdere back-endservices aan te roepen, waarbij de resultaten worden samengevoegd. 

## <a name="sample-usage-of-piggymetrics"></a>Voorbeeld van het gebruik van PiggyMetrics

Zie [PiggyMetrics](https://github.com/Azure-Samples/piggymetrics) voor de volledige implementatiegegevens. De voorbeelden verwijzen naar de broncode, indien nodig.
::: zone-end

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Azure Spring Cloud-exemplaar inrichten](spring-cloud-quickstart-provision-service-instance.md)
