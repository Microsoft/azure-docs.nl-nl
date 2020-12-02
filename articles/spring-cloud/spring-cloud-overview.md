---
title: Inleiding tot Azure Spring Cloud
description: Ontdek de functies en voordelen van Azure Spring Cloud voor het implementeren en beheren van Java Spring-toepassingen in Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 74ebdafb835aff75f282b9d6ac02d8ccf672a2be
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501087"
---
# <a name="what-is-azure-spring-cloud"></a>Wat is Azure Spring Cloud?

Met Azure Spring Cloud kunt u eenvoudig microservicetoepassingen op basis van Spring Boot implementeren in Azure zonder codewijzigingen.  Azure Spring Cloud beheert de infrastructuur van Spring Cloud-toepassingen, zodat ontwikkelaars zich kunnen richten op hun code.  Spring Cloud biedt levenscyclusbeheer met uitgebreide bewaking en diagnose, configuratiebeheer, servicedetectie, CI/CD-integratie, blue-green implementaties, en meer.

Azure Spring Cloud ondersteunt [Spring Boot](https://spring.io/projects/spring-boot)-apps van Java en [Steeltoe](https://steeltoe.io/)-apps van ASP.NET Core. Ondersteuning van Steeltoe wordt momenteel aangeboden als openbare preview. Met openbare preview-aanbiedingen kunt u voorafgaand aan de officiële release met nieuwe functies experimenteren.  Openbare preview-functies en -services zijn niet bedoeld voor gebruik in productie.  Zie de [Veelgestelde vragen](https://azure.microsoft.com/support/faq/) of een dien een [ondersteuningsaanvraag](../azure-portal/supportability/how-to-create-azure-support-request.md) in voor meer informatie.

Als onderdeel van het Azure-ecosysteem kan Azure Spring Cloud eenvoudig worden verbonden met Azure-services, waaronder opslag, databases, bewaking en meer.

In deze inleiding worden de volgende mogelijkheden van Azure Spring Cloud beschreven:

* Configuratieserver
* Blauwe of groene implementaties
* Schalen van toepassingen
* Integratie met Azure DevOps
* Toepassingsbewaking

## <a name="spring-cloud-config-server"></a>Spring Cloud-configuratieserver

Azure Spring Cloud-configuratieserver biedt ondersteuning voor een geëxternaliseerde configuratie in een gedistribueerd systeem aan server- en clientzijde.  De Azure Spring Cloud-configuratieserver is een centrale locatie voor het beheren van toepassingseigenschappen in alle omgevingen. Raadpleeg de [Spring Cloud Config Server-referentie](https://spring.io/projects/spring-cloud-config) voor meer informatie. 

## <a name="bluegreen-deployments"></a>Blauwe of groene implementaties

Azure Spring Cloud ondersteunt blue-green implementaties voor het vrijgeven en bijwerken van code naar productieomgevingen.  Door dit patroon voor wijzigingsbeheer kunnen ontwikkelaars functie- en codewijzigingen implementeren met de zekerheid van een directe fallback wanneer dat nodig is.  Ontwikkelaars kunnen zich concentreren op het schrijven van code met meerdere productieomgevingen om codewijzigingen bij te werken of terug te draaien zonder de toepassing te onderbreken.  Zie dit [artikel met instructies](spring-cloud-howto-staging-environment.md) voor meer informatie over faseringsomgevingen en blue-green implementaties.

## <a name="cicd-pipeline-automation"></a>Automatisering van CI/CD-pijplijnen

Azure Spring Cloud biedt integratie met Azure DevOps met behulp van de Azure CLI.  Met Azure DevOps kunt u code-integratie en -implementatie automatiseren in uw Spring-toepassing.  Lees [dit artikel](spring-cloud-howto-cicd.md) voor meer informatie.

## <a name="application-scaling"></a>Schalen van toepassingen

Met Azure Spring Cloud kunt u eenvoudig de microservices in uw Azure Spring Cloud-dashboard schalen.  Het aantal Vcpu's en de hoeveelheid geheugen die beschikbaar is voor uw microservices kunnen omhoog of omlaag worden geschaald op basis van uw vereisten.  Schalen gaat binnen enkele seconden van kracht en vereist geen codewijzigingen of herimplementatie.  Volg deze [zelfstudie](spring-cloud-tutorial-scale-manual.md) voor meer informatie.

## <a name="application-monitoring"></a>Toepassingsbewaking

Met de gedistribueerde traceringshulpprogramma's in Spring Cloud kunnen ontwikkelaars de complexe verbindingen tussen microservices in een toepassing bewaken en er fouten in opsporen.  Dankzij de integratie van [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) met [Application Insights](../azure-monitor/monitor-reference.md) van Azure biedt Azure krachtige functies voor gedistribueerde tracering rechtstreeks vanuit de Azure-portal.  Volg deze [zelfstudie](spring-cloud-tutorial-distributed-tracing.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Voltooi de [Spring Cloud-quickstart](spring-cloud-quickstart.md) om aan de slag te gaan

Voorbeelden zijn beschikbaar op GitHub: [Azure Spring Cloud-voorbeelden](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/).