---
title: bestand opnemen
description: bestand opnemen
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/04/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f469664c716ecef6b82de2befa40b33f253e229f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99627694"
---
1. Zoek het virtuele WAN dat u hebt gemaakt. Selecteer **hubs** op de virtuele WAN-pagina onder het gedeelte **connectiviteit** .
2. Selecteer op de pagina **hubs** **+ nieuwe hub** om de pagina **virtuele hub maken** te openen.

    ![Schermopname van het deelvenster Virtuele hub maken waarbij het tabblad Basisprincipes geselecteerd is.](./media/virtual-wan-tutorial-hub-include/basics.png "Basisbeginselen")
3. Op de pagina **Basisinstellingen** van de pagina **Virtuele hub maken** vult u de volgende velden in:

   * **Regio** (voorheen locatie genoemd)
   * **Naam**
   * **Privé-adres ruimte van hub** -de minimale adres ruimte is/24 om een hub te maken. Als u iets gebruikt in het bereik van/25 tot/32, wordt er tijdens het maken een fout gegenereerd. U hoeft de adres ruimte van het subnet niet expliciet te plannen voor de services in de virtuele hub. Omdat Azure Virtual WAN een beheerde service is, maakt het de juiste subnetten in de virtuele hub voor de verschillende gateways/Services (bijvoorbeeld VPN-gateways, ExpressRoute gateways, gebruikers VPN punt-naar-site gateways, firewall, route ring enzovoort).
4. Selecteer **Volgende: Site-naar-site**

    ![Schermopname van het deelvenster Virtuele hub maken waarin Site-naar-site is geselecteerd.](./media/virtual-wan-tutorial-hub-include/site-to-site.png "Site-naar-site")

5. Vul op het tabblad **Site-naar-site** de volgende velden in:

   * Selecteer **Ja** om een Site-naar-site VPN-verbinding te maken.
   * Het veld als getal kan niet worden bewerkt.
   * Selecteer de **Gateway-schaaleenheden** in de vervolgkeuzelijst. Met de schaaleenheid kunt u de geaggregeerde doorvoer van de VPN-gateway kiezen die wordt gemaakt in de virtuele hub waarmee sites worden verbonden. Als u 1 schaaleenheid = 500 Mbps kiest, betekent dit dat er twee exemplaren voor redundantie worden gemaakt, elk met een maximale doorvoer van 500 Mbps. Als u bijvoorbeeld vijf vertakkingen hebt, elk met een doorvoer van 10 Mbps, hebt u aan het eind van de vertakkingen een totaal van 50 Mbps nodig. Het plannen van de totale capaciteit van de Azure VPN-gateway moet worden uitgevoerd na het beoordelen van de capaciteit die nodig is om het aantal branches naar de hub te ondersteunen.
6. Selecteer **Beoordelen en maken** om de validatie uit te voeren.
7. Selecteer **Maken** om de hub te maken. Na 30 minuten selecteert u **Vernieuwen** om de hub op de pagina **Hubs** te bekijken. Selecteer **Naar resource gaan** om naar de resource te gaan.
