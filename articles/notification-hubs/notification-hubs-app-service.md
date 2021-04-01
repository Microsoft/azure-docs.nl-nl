---
title: Integratie van Notification Hubs met App Service Mobile Apps
description: Meer informatie over hoe Azure Notification Hubs werken met Azure App Service Mobile Apps.
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 08/06/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: e7042be7e407e8e0827e142ba6878dfff812e1f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88004055"
---
# <a name="integration-with-app-service-mobile-apps"></a>Integratie met App Service Mobile Apps

Voor een naadloze werking van alle Azure-services biedt [App Service Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) ingebouwde ondersteuning voor meldingen via Azure Notification Hubs. Met [App Service Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) kunnen ontwikkelaars van ondernemingen en systeemintegrators gebruikmaken van een zeer schaalbaar, algemeen beschikbaar ontwikkelplatform voor mobiele toepassingen, dat uitgebreide mogelijkheden biedt voor ontwikkelaars van mobiele apps.

Ontwikkelaars van mobiele apps kunnen gebruikmaken van Notification Hubs in de volgende werkstroom:

1. Haal de PNS-ingang van het apparaat op.
2. Registreer het apparaat bij Notification Hubs met behulp van registratie-API’s van de client-SDK voor mobiele apps.

    > [!NOTE]
    > Om beveiligingsreden worden alle tags op registraties door mobiele apps verwijderd. Gebruik Notification Hubs om vanuit de back-end rechtstreeks tags aan apparaten koppelen.

3. Verzend meldingen vanuit de back-end van uw app met Notification Hubs.

Een aantal voordelen van deze integratie zijn:

- **Client SDK's voor mobiele apps**: Deze SDK's voor meerdere platforms bieden API's voor de registratie en communicatie met de Notification Hub die aan de mobiele app is gekoppeld. U hebt geen aanmeldgegevens voor Notification Hubs nodig en hoeft niet met een extra service te werken.
  - *Pushen naar gebruiker*: De SDK's coderen het gespecificeerde apparaat automatisch met een geverifieerde gebruikers-id van de mobiele app om het verzenden van pushberichten mogelijk te maken.
  - *Pushen naar apparaat*: De SDK's gebruiken de installatie-id van een mobiele app automatisch als GUID om te registreren bij Notification Hubs, waardoor het niet nodig is meerdere service-GUID's bij te houden.
- **Installatiemodel**: Mobiele apps werken met het meest recente pushmodel van Notification Hubs. Hierdoor zijn alle pusheigenschappen beschikbaar die aan een apparaat zijn gekoppeld in een JSON-installatie, die aansluit op de Push Notification Services en eenvoudig te gebruiken is.
- **Flexibiliteit**: Ontwikkelaars kunnen altijd beslissen om rechtstreeks met Notification Hubs te werken, zelfs na integratie.
- **Geïntegreerde ervaring in de [Azure-portal](https://portal.azure.com)** : De pushfunctie in mobiele apps is zeer visueel en ontwikkelaars kunnen eenvoudig via mobiele apps met de bijbehorende Notification Hub werken.
