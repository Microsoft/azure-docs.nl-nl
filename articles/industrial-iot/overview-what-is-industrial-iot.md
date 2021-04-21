---
title: Overzicht van Azure Industrial IoT
description: In dit artikel vindt u een overzicht van Industriële IoT. Hier worden de connectiviteits- en beveiligingsonderdelen van de winkel in IIoT uitgelegd.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: overview
ms.date: 3/22/2021
ms.openlocfilehash: ce582f810f483f2e5d3fdda2c3379ecad3842d51
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813268"
---
# <a name="what-is-industrial-iot-iiot"></a>Wat is Industrial IoT (IIoT)?

![Industriële IoT](media/overview-what-is-Industrial-IoT/icon-255-px.png)

Microsoft Azure Industrial Internet of Things (IIoT) is een suite met Azure-modules en -services die de kracht van de cloud integreren in de industrie- en productiebar. Azure IIoT maakt gebruik van open interfaces die voldoen aan de industriestandaard, zoals de [Open Platform Communications Unified Architecture (OPC UA)](https://opcfoundation.org/about/opc-technologies/opc-ua/)en biedt u de mogelijkheid om gegevens van assets en sensoren, inclusief die die al in uw fabriek werken, te integreren in de Azure-cloud. Door uw gegevens in de cloud te hebben, kunnen deze sneller en flexibeler worden gebruikt als feedback voor het ontwikkelen van transformerende zakelijke en industriële processen.

## <a name="discover-register-and-manage-your-industrial-assets-with-azure"></a>Uw industriële activa ontdekken, registreren en beheren met Azure

Met Azure Industrial IoT kunnen fabrieksoperators OPC UA-servers in een fabrieksnetwerk ontdekken en registreren in Azure IoT Hub. Operationele medewerkers kunnen zich abonneren op en reageren op gebeurtenissen op de fabriekswerkzaamheden, overal ter wereld, waarschuwingen en waarschuwingen ontvangen en er in realtime op reageren.

IIoT biedt een set microservices die OPC UA-functionaliteit implementeren. De REST API's van Microservices spiegelen de OPC UA-services aan de rand. Ze worden beveiligd met behulp van OAUTH-verificatie en autorisatie die worden Azure Active Directory (AAD). Hierdoor kunnen uw cloudtoepassingen bladeren door serveradresruimten of lees-/schrijfvariabelen en methoden uitvoeren met https en eenvoudige OPC UA JSON-nettoladingen. De edge-services worden geïmplementeerd als Azure IoT Edge modules en worden on-premises uitgevoerd. De cloudmicroservices worden geïmplementeerd als ASP.NET microservices met een REST-interface en worden uitgevoerd in beheerde Azure Kubernetes Services of als een Azure App Service. Voor zowel edge- als cloudservices biedt IIoT vooraf gebouwde Docker-containers in de Microsoft Container Registry (MCR), waardoor deze stap voor de klant wordt verwijderd. De rand- en cloudservices maken gebruik van elkaar en moeten samen worden gebruikt. IIoT biedt ook eenvoudig te gebruiken implementatiescripts waarmee het hele platform met één opdracht kan worden geïmplementeerd.

Daarnaast kunnen de REST API's worden gebruikt met elke programmeertaal via de beschikbaar gemaakt Open API-specificatie (Swagger). Dit betekent dat bij het integreren van OPC UA in cloudbeheeroplossingen ontwikkelaars vrij zijn om technologie te kiezen die overeenkomt met hun vaardigheden, interesses en architectuurkeuzes. Een webontwikkelaar met volledige stack die een toepassing voor een alarm- en gebeurtenisdashboard ontwikkelt, kan bijvoorbeeld logica schrijven om te reageren op gebeurtenissen in JavaScript of TypeScript zonder een OPC UA SDK, C, C++, Java of C# te gebruiken.

## <a name="manage-certificates-and-trust-groups"></a>Certificaten en vertrouwensgroepen beheren

Azure Industrial IoT beheert OPC UA-toepassingscertificaten en vertrouwenslijsten van machines op de fabrieksbouw en controlesystemen om de communicatie tussen OPC UA-client en server veilig te houden. Hiermee wordt beperkt welke client met welke server mag praten. De opslag van persoonlijke sleutels en het ondertekenen van certificaten wordt ondersteund door Azure Key Vault, die ondersteuning biedt voor hardwarebeveiliging (HSM).

## <a name="industrial-iot-components"></a>Industriële IoT-onderdelen

Azure IIoT-oplossingen zijn gebouwd op basis van specifieke onderdelen. Dit zijn onder andere de volgende.

- **Ten minste één Azure IoT Hub.**
- **IoT Edge apparaten.**
- **Industriële Edge-modules.**

### <a name="iot-hub"></a>IoT Hub
[Azure IoT Hub]( fungeert als een centrale berichtenhub voor veilige, bi-directionele communicatie tussen een IoT-toepassing en de apparaten die https://azure.microsoft.com/services/iot-hub/ verantwoordelijk zijn voor het beheer ervan. Het is een open en flexibele cloud-platform as a service (PaaS) die ondersteuning biedt voor opensource-SDK's en meerdere protocollen. 

Door uw industriële en zakelijke gegevens te verzamelen op een IoT Hub kunt u uw gegevens veilig opslaan, er bedrijfs- en efficiëntieanalyses op uitvoeren en er rapporten van genereren. U kunt ook Microsoft Azure services en hulpprogramma's, zoals [Power BI,](https://powerbi.microsoft.com)toepassen op uw geconsolideerde gegevens.

### <a name="iot-edge-devices"></a>IoT Edge apparaten
De [edge-services](https://azure.microsoft.com/services/iot-edge/) worden geïmplementeerd als Azure IoT Edge modules en worden on-premises uitgevoerd. De cloudmicroservices worden geïmplementeerd als ASP.NET microservices met een REST-interface en worden uitgevoerd in beheerde Azure Kubernetes Services of als een Azure App Service. Voor zowel edge- als cloudservices hebben we vooraf gebouwde Docker-containers in de Microsoft Container Registry (MCR) geleverd, waardoor deze stap voor de klant is verwijderd. De rand- en cloudservices maken gebruik van elkaar en moeten samen worden gebruikt. We hebben ook eenvoudig te gebruiken implementatiescripts geboden waarmee het hele platform met één opdracht kan worden geïmplementeerd.

Een IoT Edge apparaat bestaat uit Edge Runtime- en Edge-modules.
- **Edge-modules** zijn Docker-containers, de kleinste rekeneenheid, zoals OPC Publisher en OPC Twin. 
- **Het Edge-apparaat** wordt gebruikt voor het implementeren van dergelijke modules, die fungeren als een verbinding tussen de OPC UA-server en IoT Hub in de cloud.

### <a name="industrial-edge-modules"></a>Industrial Edge-modules
- **OPC Publisher:** de OPC Publisher wordt uitgevoerd binnen IoT Edge. Het maakt verbinding met OPC UA-servers en publiceert met JSON gecodeerde telemetriegegevens van deze servers in OPC UA Pub/Sub-indeling naar Azure IoT Hub. Alle transportprotocollen die worden ondersteund door Azure IoT Hub client-SDK kunnen worden gebruikt, dat wil zeggen HTTPS, AMQP en MQTT.
- **OPC Twin:** De OPC Twin bestaat uit microservices die gebruikmaken van Azure IoT Edge en IoT Hub verbinding te maken met de cloud en het fabrieksnetwerk. OPC Twin biedt detectie, registratie en extern beheer van industriële apparaten via REST-API's. VOOR OPC Twin is geen OPC Unified Architecture (OPC UA) SDK vereist. De programmeertaal is agnostisch en kan worden opgenomen in een serverloze werkstroom.
- **Detectie:** de detectiemodule, vertegenwoordigd door de detectie-identiteit, biedt detectieservices aan de rand, waaronder OPC UA-serverdetectie. Als detectie is geconfigureerd en ingeschakeld, verzendt de module de resultaten van een scantest via het IoT Edge- en IoT Hub-telemetriepad naar de onboardingservice. De service verwerkt de resultaten en werkt alle gerelateerde identiteiten in het register bij.

## <a name="next-steps"></a>Volgende stappen
Nu u hebt geleerd wat Industriële IoT is, kunt u meer lezen over het Industrial IoT Platform en de OPC Publisher:

> [!div class="nextstepaction"]
> [Wat is de OPC Publisher?](overview-what-is-opc-publisher.md)