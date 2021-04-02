---
title: bestand opnemen
description: bestand opnemen
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/09/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5915830e4521399ad322dd4a6f3926428d811455
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94942896"
---
Open een browser, ga naar [Azure Portal](https://portal.azure.com) en meld u aan met uw Azure-account.

1. Selecteer in de portal **+ Een resource maken**. Typ **Virtual WAN** in het zoekvak en selecteer **Enter**.
1. Selecteer **Virtual WAN** uit de resultaten. Selecteer op de pagina Virtual WAN **Maken** om de pagina WAN maken te openen.
1. Vul op de pagina **Wan maken**, op het tabblad **Basisprincipes**, de volgende velden in:

   :::image type="content" source="./media/virtual-wan-create-vwan-include/basics.png" alt-text="Schermopname toont het deelvenster 'WAN maken' waarbij het tabblad Basisprincipes geselecteerd is.":::

   * **Abonnement** - selecteer het abonnement dat u wilt gebruiken.
   * **Resourcegroep** - maak een nieuwe resourcegroep of gebruik een bestaande.
   * **Locatie van de resourcegroep** - kies een resourcelocatie uit de vervolgkeuzelijst. Een WAN een globale resource en bevindt zich niet in een bepaalde regio. U moet echter een regio selecteren om de WAN-resource die u maakt te kunnen beheren en vinden.
   * **Naam** - typ de naam die u voor uw WAN hebt gekozen.
   * **Type**: Basic of Standard. selecteer **Standaard**. Als u Basic-VWAN selecteert, moet u onthouden dat Basic-VWAN's alleen Basic-hubs kunnen bevatten. Dit beperkt uw verbindingstype tot site-naar-site.
1. Nadat u klaar bent met het invullen van de velden, selecteert u **+Maken**.
1. Wanneer de validatie is geslaagd, selecteert u **Maken** om het virtuele WAN te maken.
