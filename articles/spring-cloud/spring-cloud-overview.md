---
title: Inleiding tot Azure Spring Cloud
description: Ontdek de functies en voordelen van Azure Spring Cloud voor het implementeren en beheren van Java Spring-toepassingen in Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 12/02/2020
ms.author: brendm
ms.custom: devx-track-java, contperf-fy21q2
ms.openlocfilehash: b7f5d4206140bf2101c10b1cd4ac46d80bdd3342
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98881700"
---
# <a name="what-is-azure-spring-cloud"></a>Wat is Azure Spring Cloud?

Met Azure Spring Cloud kunt u eenvoudig microservicetoepassingen van Spring Boot implementeren in Azure zonder codewijzigingen.  De service beheert de infrastructuur van Spring Cloud-toepassingen, zodat ontwikkelaars zich kunnen richten op hun code.  Azure Spring Cloud biedt levenscyclusbeheer met uitgebreide bewaking en diagnose, configuratiebeheer, servicedetectie, CI/CD-integratie, blauw-groen-implementaties, en meer.

## <a name="why-use-azure-spring-cloud"></a>Waarom zou u Azure Spring Cloud gebruiken?

De implementatie van toepassingen op Azure Spring Cloud heeft veel voordelen.  U kunt het volgende doen:
* Bestaande Spring-apps efficiënt migreren en de schaalbaarheid en kosten voor de cloud beheren.
* Apps moderniseren met Spring Cloud-patronen om de flexibiliteit en snelheid van de levering te verbeteren.
* Java uitvoeren op cloudschaalbaarheid en hoger gebruik aansturen zonder ingewikkelde infrastructuur.
* Snel ontwikkelen en implementeren zonder containerisatie-afhankelijkheden.
* Productiewerkbelastingen efficiënt en moeiteloos bewaken.

Azure Spring Cloud ondersteunt [Spring Boot](https://spring.io/projects/spring-boot)-apps van Java en [Steeltoe](https://steeltoe.io/)-apps van ASP.NET Core. Ondersteuning van Steeltoe wordt momenteel aangeboden als openbare preview. Met openbare preview-aanbiedingen kunt u voorafgaand aan de officiële release met nieuwe functies experimenteren. Openbare preview-functies en -services zijn niet bedoeld voor gebruik in productie. Zie de [Veelgestelde vragen](https://azure.microsoft.com/support/faq/) of een dien een [ondersteuningsaanvraag](../azure-portal/supportability/how-to-create-azure-support-request.md) in voor meer informatie.

## <a name="service-overview"></a>Overzicht van services

Als onderdeel van het Azure-ecosysteem kan Azure Spring Cloud eenvoudig worden verbonden met Azure-services, waaronder opslag, databases, bewaking en meer.  

  ![Overzicht van Azure Spring Cloud](media/spring-cloud-principles/azure-spring-cloud-overview.png)

* Azure Spring Cloud is een volledig beheerde service voor Spring Boot-apps waarmee u zich kunt richten op het bouwen en uitvoeren van apps zonder dat u de infrastructuur hoeft te beheren.

* U hoeft alleen maar uw JAR’s of code te implementeren en Azure Spring Cloud zorgt er automatisch voor dat uw apps worden uitgerust met Spring-serviceruntime en ingebouwde levenscyclus van de app.

* Bewaken is eenvoudig. Na de implementatie kunt u de prestaties van de app bewaken, fouten corrigeren en toepassingen snel verbeteren. 

* Volledige integratie met de ecosystemen en services van Azure.

* Azure Spring Cloud is geschikt voor gebruik door bedrijven met een volledige beheerde infrastructuur, ingebouwd levenscyclusbeheer en eenvoudige bewaking.

## <a name="documentation-overview"></a>Overzicht van documentatie
Deze documentatie bevat secties waarin wordt uitgelegd hoe u aan de slag kunt gaan en Azure Spring Cloud-services kunt gebruiken.

* Aan de slag
    * [Uw eerste app lanceren](spring-cloud-quickstart.md)
    * [Een Azure Spring Cloud-service inrichten](spring-cloud-quickstart-provision-service-instance.md)
    * [De configuratieserver instellen]()
    * [Apps bouwen en implementeren](spring-cloud-quickstart-deploy-apps.md)
    * [Logboeken, metrische gegevens en tracering gebruiken](spring-cloud-quickstart-logs-metrics-tracing.md)
* Uitleg
    * [Ontwikkelen](spring-cloud-tutorial-prepare-app-deployment.md): Een bestaande Java Spring-toepassing voorbereiden voor implementatie in Azure Spring Cloud. Wanneer Azure Spring Cloud op de juiste wijze is geconfigureerd, beschikt u over krachtige services voor het bewaken, schalen en bijwerken van Java Spring Cloud-toepassingen.
    * [Implementeren](spring-cloud-howto-staging-environment.md): Een faseringsimplementatie instellen met behulp van het blauw-groen-implementatiepatroon in Azure Spring Cloud. Blauw/groen-implementatie is een Azure DevOps-patroon voor continue levering dat erop vertrouwt een bestaande (blauwe) versie live te houden terwijl een nieuwe (groene) wordt geïmplementeerd.
    * [Apps configureren](spring-cloud-howto-start-stop-delete.md):  Uw Azure Spring Cloud-toepassingen starten, stoppen en verwijderen. De status van een toepassing in Azure Spring Cloud wijzigen met behulp van de Azure Portal of de Azure CLI.
    * [Schalen](spring-cloud-tutorial-scale-manual.md): Elke microservicetoepassing schalen met behulp van het Azure Spring Cloud-dashboard in de Azure Portal of de instellingen voor automatische schaalaanpassing gebruiken. Openbare IP's zijn beschikbaar om te communiceren met externe resources, zoals databases, opslag en sleutelkluizen.
    * [Apps bewaken](spring-cloud-tutorial-distributed-tracing.md): Hulpprogramma's voor gedistribueerde tracering om ingewikkelde problemen eenvoudig op te sporen en te bewaken. Azure Spring Cloud integreert Spring Cloud Sleuth met Application Insights van Azure. Deze integratie biedt krachtige functies voor gedistribueerde tracering via de Azure-portal.
    * [Beveiligde apps](spring-cloud-howto-enable-system-assigned-managed-identity.md): Azure-resources bieden een automatisch beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij alle services die Microsoft Azure AD-verificatie ondersteunen, zonder dat u aanmeldingsgegevens in uw code hoeft te hebben.
    * [Integratie met andere Azure-services](spring-cloud-tutorial-bind-cosmos.md): In plaats van uw Spring Boot-toepassingen handmatig te configureren, kunt u bepaalde Azure-services automatisch met uw toepassingen verbinden; u kunt uw toepassing bijvoorbeeld verbinden aan een Azure Cosmos DB-database.
    * [Automatiseren](spring-cloud-howto-cicd.md): Met de hulpprogramma's voor continue integratie en continue levering kunt u snel updates implementeren voor bestaande toepassingen met minimale inspanning en risico. Azure DevOps helpt bij het organiseren en beheren van deze belangrijke taken. 
    * [Problemen oplossen](spring-cloud-howto-self-diagnose-solve.md): Azure Spring Cloud-diagnose biedt een interactieve ervaring bij het oplossen van problemen met apps. Er is geen configuratie vereist. Wanneer u problemen ondervindt, identificeert de Azure Spring Cloud-diagnose problemen en begeleidt u bij het oplossen van problemen.
    * [Migreren](/azure/developer/java/migration/migrate-spring-boot-to-azure-spring-cloud): Een bestaande Spring Cloud-toepassing of Spring Boot-toepassing migreren om uit te voeren in Azure Spring Cloud.

## <a name="next-steps"></a>Volgende stappen

Voltooi de [Spring Cloud-quickstart](spring-cloud-quickstart.md) om aan de slag te gaan

Voorbeelden zijn beschikbaar op GitHub: [Azure Spring Cloud-voorbeelden](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/).