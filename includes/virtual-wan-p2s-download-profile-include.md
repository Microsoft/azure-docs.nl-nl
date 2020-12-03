---
title: bestand opnemen
description: bestand opnemen
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 317d75dae83e8529bda25897b77824d8cd36e72e
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95561390"
---
1. Selecteer op de pagina voor uw virtuele WAN op **Configuraties gebruikers-VPN**.
1. Selecteer bovenaan de pagina **Configuratie gebruikers-VPN downloaden**. Door een WAN-configuratie te downloaden, wordt er een ingebouwd VPN-gebruikersprofiel op basis van Traffic Manager gemaakt. Zie [Hubprofielen](../articles/virtual-wan/global-hub-profile.md) voor meer informatie over globale of op hub gebaseerde profielen. Failover-scenario's worden vereenvoudigd met wereldwijde profielen.

   Als om enige reden een hub niet beschikbaar is, zal het ingebouwde verkeerbeheer dat wordt geboden door de service de verbinding naar Azure-resources voor punt-naar-site-gebruikers garanderen via een andere hub. U kunt altijd een VPN-configuratie specifiek voor een hub downloaden door naar de hub te gaan. Download onder **Gebruikers-VPN (punt-naar-site)** het **Gebruikers-VPN**-profiel van de virtuele hub.
1. Wanneer het bestand gereed is, selecteert u de koppeling om het te downloaden.
1. Gebruik het profielbestand om de VPN-clients te configureren.