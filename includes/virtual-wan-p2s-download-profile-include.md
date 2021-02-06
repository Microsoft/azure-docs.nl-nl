---
title: bestand opnemen
description: bestand opnemen
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/05/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: eea8cb61ce98b2394abff6995e5cc89f00a7cf46
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628950"
---
1. Selecteer op de pagina voor uw virtuele WAN op **Configuraties gebruikers-VPN**.
1. Selecteer op de pagina **configuratie van gebruikers-VPN** een configuratie en selecteer vervolgens **virtueel WAN-gebruikers profiel voor VPN downloaden**. Wanneer u de configuratie op WAN-niveau downloadt, krijgt u een ingebouwd VPN-gebruikers profiel op basis van Traffic Manager. Zie [Hubprofielen](../articles/virtual-wan/global-hub-profile.md) voor meer informatie over globale of op hub gebaseerde profielen. Failover-scenario's worden vereenvoudigd met wereldwijde profielen.

   
   Als om enige reden een hub niet beschikbaar is, zal het ingebouwde verkeerbeheer dat wordt geboden door de service de verbinding naar Azure-resources voor punt-naar-site-gebruikers garanderen via een andere hub. U kunt altijd een VPN-configuratie specifiek voor een hub downloaden door naar de hub te gaan. Download onder **Gebruikers-VPN (punt-naar-site)** het **Gebruikers-VPN**-profiel van de virtuele hub.
1. Selecteer op de pagina **virtueel WAN-gebruikers profiel voor VPN-verbinding downloaden** het **verificatie type** en selecteer vervolgens **profiel genereren en downloaden**. Het profiel pakket wordt gegenereerd en er wordt een zip-bestand met de configuratie-instellingen gedownload.
