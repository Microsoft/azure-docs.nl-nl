---
title: App-status in azure lente-Cloud
description: Meer informatie over de app-status Categorieën in azure lente Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 93ceb1f006b39ebaae95bb77fd3fcb474e006eb9
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878202"
---
# <a name="app-status-in-azure-spring-cloud"></a>App-status in azure lente-Cloud

**Dit artikel is van toepassing op:** ✔️ Java ✔️ C#

De Azure veer Cloud-gebruikers interface levert informatie over de status van actieve toepassingen.  Er is een optie voor **apps** voor elke resource groep in een abonnement dat algemene status van toepassings typen weergeeft.  Voor elk toepassings type worden **toepassings exemplaren** weer gegeven.

## <a name="apps-status"></a>Status van apps
Als u de algemene status van een toepassings type wilt weer geven, selecteert u **apps** in het navigatie deel venster links van een resource groep. Met het resultaat wordt de status van de geïmplementeerde app weer gegeven:

* Bij de **inrichtings status** wordt de inrichtings status van de implementatie weer gegeven
* **Met het actieve exemplaar** wordt weer gegeven hoeveel app-exemplaren worden uitgevoerd/hoeveel app-exemplaren gewenst zijn. Als de app moet worden gestopt, wordt de kolom *gestopt* weer gegeven.
* In het **geregistreerde exemplaar** wordt weer gegeven hoeveel app-exemplaren zijn geregistreerd bij Eureka/hoeveel app-exemplaar gewenst zijn. Als de app moet worden gestopt, wordt de kolom *gestopt* weer gegeven.


 ![Status van apps](media/spring-cloud-concept-app-status/apps-ui-status.png)

**De implementatie status wordt gerapporteerd als een van de volgende waarden:**

| Enum | Definitie |
|:--:|:----------------:|
| Wordt uitgevoerd | De implementatie moet worden uitgevoerd. |
| Gestopt | De implementatie moet worden gestopt. |

**De inrichtings status is alleen toegankelijk vanuit de CLI.  De waarde wordt gerapporteerd als een van de volgende waarden:**

| Enum | Definitie |
|:--:|:----------------:|
| Maken | De resource wordt gemaakt. |
| Bijwerken | De resource wordt bijgewerkt. |
| Geslaagd | Er zijn resources opgegeven en het binaire bestand wordt geïmplementeerd. |
| Mislukt | Het doel van het *succes* is niet gerealiseerd. |
| Verwijderen | De resource wordt verwijderd. Dit voor komt dat de bewerking wordt voor komen en de resource is niet beschikbaar in deze status. |

## <a name="app-instances-status"></a>Status app-exemplaren

Als u de status van een specifiek exemplaar van een geïmplementeerde app wilt weer geven, klikt u op de **naam** van de app in de gebruikers interface van **apps** . De resultaten worden weer gegeven:
* **Status**: of de instantie wordt uitgevoerd of de status ervan
* **DiscoveryStatus**: de geregistreerde status van het app-exemplaar in de Eureka-server

 ![Status app-exemplaren](media/spring-cloud-concept-app-status/apps-ui-instance-status.png)

**De status van het exemplaar wordt gerapporteerd als een van de volgende waarden:**

| Enum | Definitie |
|:--:|:----------------:|
| Starten | Het binaire bestand is geïmplementeerd voor het opgegeven exemplaar. Het starten van het exemplaar van het jar-bestand kan mislukken omdat jar niet goed kan worden uitgevoerd. |
| Wordt uitgevoerd | Het exemplaar werkt. |
| Mislukt | Het app-exemplaar kan het binaire bestand van de gebruiker niet starten na verschillende pogingen. |
| Afsluit | Het app-exemplaar wordt afgesloten. |

**De detectie status van het exemplaar wordt gerapporteerd als een van de volgende waarden:**

| Enum | Definitie |
|:--:|:----------------:|
| GEVOUWEN | Het app-exemplaar is geregistreerd voor Eureka en klaar om verkeer te ontvangen |
| OUT_OF_SERVICE | Het app-exemplaar is geregistreerd voor Eureka en kan verkeer ontvangen. maar wordt voor het bewuste verkeer afgesloten. |
| OPGESOMD | Het app-exemplaar is niet geregistreerd bij Eureka of is geregistreerd, maar kan geen verkeer ontvangen. |


## <a name="see-also"></a>Zie ook
* [Een lente-of Steeltoe-toepassing voorbereiden voor implementatie in azure lente-Cloud](how-to-prepare-app-deployment.md)