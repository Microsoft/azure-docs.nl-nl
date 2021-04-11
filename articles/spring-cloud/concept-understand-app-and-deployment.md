---
title: App en implementatie in azure lente Cloud
description: In dit onderwerp wordt uitgelegd wat het verschil is tussen toepassing en implementatie in azure lente Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 07/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 5473daedc8a7ad5a3b6ddffc65234160d4b3019d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878148"
---
# <a name="app-and-deployment-in-azure-spring-cloud"></a>App en implementatie in azure lente Cloud

**Dit artikel is van toepassing op:** ✔️ Java ✔️ C#

**App** en **implementatie** zijn de twee belang rijke concepten in het resource model van Azure lente Cloud. In azure lente Cloud is een *app* een abstractie van één zakelijke app of een micro service.  Een versie van code of binair wordt geïmplementeerd als de *app* wordt uitgevoerd in een *implementatie*.  Apps worden uitgevoerd in een *Azure lente-Cloud service-exemplaar* of gewoon *service-exemplaar*, zoals hieronder wordt weer gegeven.

 ![Apps en implementaties](./media/spring-cloud-app-and-deployment/app-deployment-rev.png)

U kunt meerdere service-exemplaren binnen één Azure-abonnement hebben, maar de Azure lente-Cloud service is het gemakkelijkst te gebruiken wanneer alle apps die een zakelijke app of micro service vormen, zich in één service-exemplaar bevinden.

In de Cloud Standard-laag van Azure lente kan één app één productie-implementatie en één faserings implementatie hebben, zodat u deze eenvoudig kunt uitvoeren op een blauw/groen-implementatie.

## <a name="app"></a>App
De volgende functies/eigenschappen worden gedefinieerd op app-niveau.

| Functies | Description |
|:--|:----------------|
| Openbaar</br>Eindpunt | De URL voor toegang tot de app |
| Aangepast</br>Domain | CNAME-record dat het aangepaste domein beveiligt |
| Service</br>Binding | Out-of-Box-verbinding met andere Azure-Services |
| Beheerd</br>Identiteit | Met beheerde identiteit door Azure Active Directory kan uw app eenvoudig toegang krijgen tot andere met Azure AD beveiligde resources, zoals Azure Key Vault |
| Permanent</br>Storage | Instelling die ervoor zorgt dat gegevens behouden blijven na het opnieuw opstarten van de app |

## <a name="deployment"></a>Implementatie

De volgende functies/eigenschappen worden gedefinieerd op implementatie niveau en worden uitgewisseld bij het wisselen van productie-en faserings implementatie.

| Functies | Beschrijving |
|:--|:----------------|
| CPU | Aantal vcores per app-exemplaar |
| Geheugen | GB aan geheugen per app-exemplaar|
| Exemplaar</br>Count | Het aantal app-exemplaren, hand matig of automatisch instellen |
| Automatisch schalen | Aantal exemplaren automatisch schalen op basis van vooraf gedefinieerde regels en schema's |
| JVM</br>Opties | Opties voor JVM instellen  |
| Omgeving</br>Variabelen | Omgevings variabelen instellen |
| Runtime</br>Versie | Java 8/Java 11|

## <a name="restrictions"></a>Beperkingen

* **Een app moet één productie-implementatie hebben**: het verwijderen van een productie-implementatie wordt geblokkeerd door de API. Deze moet worden omgewisseld naar staging voordat ze worden verwijderd.
* **Een app kan Maxi maal twee implementaties hebben**: het maken van meer dan twee implementaties wordt geblokkeerd door de API. Implementeer uw nieuwe binaire bestand naar de bestaande productie-of faserings implementatie.
* **Implementatie beheer is niet beschikbaar in de Basic-laag**: gebruik de laag standaard voor Blue-Green implementatie mogelijkheden.

## <a name="see-also"></a>Zie ook
* [Een faserings omgeving instellen in azure lente-Cloud](spring-cloud-howto-staging-environment.md)
