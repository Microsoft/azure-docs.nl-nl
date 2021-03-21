---
title: bestand opnemen
description: bestand opnemen
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6f910dbe91ed8e1cb65eefa6dfc48c72a689bf25
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "73491606"
---
1. Zoek naar de virtuele WAN die u hebt gemaakt. Op de pagina Virtuele WAN, onder het gedeelte **Connectiviteit**, selecteert u **Hubs**.
2. Op de pagina Hubs selecteert u **+Nieuwe hub** om de pagina **Virtuele hub maken** te openen.
3. Op de pagina **Basisinstellingen** van de pagina **Virtuele hub maken** vult u de volgende velden in:

   ![Basisinstellingen](./media/virtual-wan-tutorial-er-hub-include/hub1.png "Basisbeginselen")

    **Projectgegevens**

   * Regio (voorheen Locatie genoemd)
   * Naam
   * Privé-adresruimte van hub. De minimale adresruimte is /24 voor het maken van een hub, wat impliceert dat alles tussen /25 en /32 een fout produceert tijdens het maken.
4. Selecteer het **tabblad ExpressRoute**.

5. Vul op het tabblad **ExpressRoute** de volgende velden in:

   ![ExpressRoute](./media/virtual-wan-tutorial-er-hub-include/hub2.png "ExpressRoute")

   * Selecteer **Ja** om een **ExpressRoute**-gateway te maken.
   * Selecteer de **Gateway-schaaleenheden** in de vervolgkeuzelijst.
6. Selecteer **Beoordelen en maken** om de validatie uit te voeren.
7. Selecteer **Maken** om de hub te maken. Na 30 minuten selecteert u **Vernieuwen** om de hub op de pagina **Hubs** te bekijken. Selecteer **Naar resource gaan** om naar de resource te gaan.