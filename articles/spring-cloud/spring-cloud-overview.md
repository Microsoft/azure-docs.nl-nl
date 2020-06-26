---
title: Inleiding tot Azure Spring-Cloud
description: Meer informatie over de functies en voor delen van Azure Spring-Cloud voor het implementeren en beheren van Java Spring-toepassingen in Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 11/4/2019
ms.author: brendm
ms.openlocfilehash: 4426044b3608be0ded378f4f56cbec6bc1948d75
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78273262"
---
# <a name="what-is-azure-spring-cloud"></a>Wat is Azure Spring Cloud?

Met Azure Spring-Cloud kunt u eenvoudig microservicetoepassingen op basis van een Spring boot implementeren op Azure zonder wijzigingen in de code.  Azure Spring-Cloud beheert de levenscyclus van Spring-Cloud toepassingen, zodat ontwikkelaars zich kunnen richten op hun code. Spring-Cloud biedt levenscyclusbeheer met uitgebreide bewaking en diagnose, configuratiebeheer, service detectie, CI/CD-integratie, Blue-Green implementaties en meer.

Als onderdeel van het Azure-ecosysteem kan Azure Spring-Cloud eenvoudig worden gebonden aan andere Azure-Services, waaronder opslag, databases, bewaking en meer.

Azure Spring Cloud wordt momenteel aangeboden als een openbare preview. Met openbare preview-aanbiedingen kunnen klanten experimenteren met nieuwe functies vóór hun officiële release.  Openbare preview-functies en-services zijn niet bedoeld voor gebruik in productie omgevingen.  Raadpleeg voor meer informatie over ondersteuning tijdens previews onze [Veelgestelde vragen](https://azure.microsoft.com/support/faq/) of bestand [ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) voor meer informatie.

Om aan de slag te gaan, voltooit u de Spring-Cloud Snelstartgids met behulp van de [Azure cli](spring-cloud-quickstart-launch-app-cli.md), het [Azure Portal](spring-cloud-quickstart-launch-app-portal.md) of [maven](spring-cloud-quickstart-launch-app-maven.md).

Meer voorbeelden zijn beschikbaar op GitHub: [Azure Spring-Cloud voorbeelden](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).

## <a name="application-configuration"></a>Toepassingsconfiguratie

### <a name="spring-cloud-config-server"></a>Spring Cloud configuratie server

Azure Spring Cloud config server biedt externe configuratie in een gedistribueerd systeem met server-en client-side-ondersteuning.  De configuratie server biedt een centrale locatie voor het beheren van toepassingseigenschappen in alle omgevingen.  Ga voor meer informatie naar de [bron van de Spring-Cloud configuratie server](https://spring.io/projects/spring-cloud-config.md) en voltooi de zelfstudie.

### <a name="enable-bluegreen-deployments"></a>Blue/groen-implementaties inschakelen

Azure Spring-Cloud ondersteunt Blue/Green-implementaties voor het vrijgeven en bijwerken van code naar productieomgevingen.  Door gebruik te maken van dit patroon voor veranderingenbeheer kunnen ontwikkelaars functies en code wijzigingen implementeren met de zekerheid dat een directe terugval mogelijk is wanneer nodig. Met Azure kunnen ontwikkelaars zich concentreren op het schrijven van code doordat Azure het beheer van meerdere productieomgevingen en het eenvoudig bijwerken of terugdraaien van codewijzigingen zonder de toepassing te onderbreken mogelijk maakt.  Ga naar dit [artikel voor instructies](spring-cloud-howto-staging-environment.md)voor meer informatie over faseringsomgevingen en Blue/Green-implementaties.

### <a name="automate-cicd-pipelines"></a>CI/CD-pijp lijnen automatiseren

Azure Spring Cloud biedt integratie met Azure DevOps met behulp van de Azure CLI.  Met Azure DevOps kunt u code integratie en implementatie automatiseren in uw Spring-toepassing.  Ga naar dit [artikel](spring-cloud-howto-cicd.md)voor meer informatie.

### <a name="scale-your-application"></a>Uw toepassing schalen

Met Azure Spring Cloud kunt u eenvoudig de microservices in uw Azure Spring Cloud-dashboard schalen.  Het aantal vCPU's en de hoeveelheid geheugen die beschikbaar is voor uw microservices kunnen omhoog of omlaag worden geschaald op basis van uw vereisten.  Schalen gebeurt in een paar seconden en vereist geen code wijzigingen of herimplementatie.  Voltooi deze [zelf studie](spring-cloud-tutorial-scale-manual.md)voor meer informatie.

## <a name="application-monitoring"></a>Toepassingsbewaking

### <a name="monitor-your-application-using-distributed-tracing-and-azure-app-insights"></a>Uw toepassing bewaken met gedistribueerde tracering en Azure-app inzichten

Met de gedistribueerde traceer hulpprogramma's van de Spring-Cloud kunnen ontwikkelaars de complexe verbindingen tussen microservices in een toepassing opsporen en bewaken. Door [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) te integreren met de [Application Insights](../azure-monitor/insights/insights-overview.md) van Azure, biedt Azure krachtige functies voor gedistribueerde tracering rechtstreeks vanuit de Azure Portal. Voltooi deze [zelf studie](spring-cloud-tutorial-distributed-tracing.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Start uw Spring Cloud-app vanuit de CLI](spring-cloud-quickstart-launch-app-cli.md)
