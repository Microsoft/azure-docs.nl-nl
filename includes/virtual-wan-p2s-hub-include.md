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
ms.openlocfilehash: 134f1dc7cb6e53c181b2f518055e5cb758fccf31
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91812677"
---
1. Selecteer onder uw virtuele WAN de optie Hubs en selecteer **+Nieuwe hub**.

   :::image type="content" source="media/virtual-wan-p2s-hub/new-hub.jpg" alt-text="Nieuwe hub":::

1. Vul de volgende velden in op de pagina virtuele hub maken.

   * **Regio**, selecteer de regio waarin u de virtuele hub wilt implementeren.
   * **Naam**, typ de naam die u voor uw virtuele hub hebt gekozen.
   * **Privé-adresruimte hub**, het adresbereik van de hub in CIDR-notatie.

   :::image type="content" source="media/virtual-wan-p2s-hub/create-hub.jpg" alt-text="Virtuele hub maken":::

1. Vul op het tabblad Punt-naar-site de volgende velden in:

   * **Schaaleenheden gateway**, deze vertegenwoordigen de totale capaciteit van de VPN-gateway van de gebruiker.
   * **Punt-naar-site-configuratie**, die u in de vorige stap hebt gemaakt.
   * **Adresgroep client**, voor de externe gebruikers.
   * **Aangepast IP-adres DNS-server**.

   :::image type="content" source="media/virtual-wan-p2s-hub/hub-with-p2s.png" alt-text="Hub met point-to-site":::

1. Selecteer **Controleren + maken**.
1. Selecteer op de pagina **Validatie geslaagd**, **Maken**.