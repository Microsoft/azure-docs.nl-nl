---
title: bestand opnemen
description: Include-bestand
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/22/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6cb30b96f9c2094e6d690b565edf487d6508d520
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92487057"
---
1. Typ n [Azure Portal](https://portal.azure.com) in **Resources, services en documenten zoeken(G+/)** **virtuele netwerkgateway**. Zoek **Gateway van virtueel netwerk** in de zoekresultaten en selecteer de vermelding.

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-empty/search.png" alt-text="Zoekveld" lightbox="./media/vpn-gateway-add-gw-rm-portal-empty/search-expand.png":::

1. Selecteer op de pagina **Gateway van virtueel netwerk** de optie **+ Toevoegen**. Hiermee opent u de pagina **Gateway van het virtuele netwerk maken**.

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-empty/add.png" alt-text="Pagina virtuele netwerkgateways":::
1. Vul op het tabblad **Basisinformatie** de waarden in voor de gateway van het virtuele netwerk.

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-empty/gateway.png" alt-text="Gateway-velden":::

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-empty/gateway-vnet.png" alt-text="Extra gateway-velden":::

   **Projectgegevens**

   * **Abonnement**: Selecteer in de vervolgkeuzelijst het abonnement dat u wilt gebruiken.
   * **Resourcegroep:** Deze instelling wordt automatisch ingevuld wanneer u het virtuele netwerk op deze pagina selecteert.

   **Exemplaardetails**

   * **Naam**: naam van uw gateway. Een naam opgeven voor een gateway is niet hetzelfde als een naam opgeven voor een gatewaysubnet. Het is de naam van het gateway-object dat u maakt.
   * **Regio**: Selecteer de regio waarin u deze resource wilt maken. De regio voor de gateway moet hetzelfde zijn als die voor het virtuele netwerk.
   * **Gatewaytype**: selecteer **VPN**. VPN-gateways maken gebruik van een gateway van het virtuele netwerk van het type **VPN**.
   * **VPN-type** selecteer het VPN-type dat wordt opgegeven voor uw configuratie. De meeste configuraties vereisen een op route gebaseerd VPN-type.
   * **SKU**: selecteer de gateway-SKU in de vervolgkeuzelijst. Welke SKU's worden weergegeven in de vervolgkeuzelijst, is afhankelijk van het VPN-type dat u selecteert. Zie [Gateway-SKU's](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku) voor informatie over gateway-SKU's.
   * **Generatie**: Zie [Gateway-SKU's](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) voor meer informatie over de generatie van VPN Gateway.
   * **Virtueel netwerk**: Kies in de vervolgkeuzelijst het virtuele netwerk waaraan u deze gateway wilt toevoegen.
   * **Adresbereik gatewaysubnet**: Dit veld wordt alleen weergegeven als uw VNet geen gatewaysubnet heeft. Maak, indien mogelijk, het bereik /27 of groter (/26, /25 enzovoort). Het maken van een bereik dat kleiner is dan /28 wordt afgeraden. Als u al een gatewaysubnet hebt, kunt u de gegevens van het gatewaysubnet weergeven door naar uw virtuele netwerk te navigeren. Klik op **Subnetten** om het bereik weer te geven. Als u het bereik wilt wijzigen, kunt u het gatewaysubnet verwijderen en opnieuw maken.

   **Openbaar IP-adres**

   Deze instelling geeft het object van het openbare IP-adres aan dat wordt gekoppeld aan de VPN-gateway. Het openbare IP-adres wordt dynamisch toegewezen aan dit object wanneer de VPN-gateway wordt gemaakt. Het openbare IP-adres verandert alleen wanneer de gateway wordt verwijderd en opnieuw wordt gemaakt. Het verandert niet wanneer de grootte van uw VPN Gateway verandert, wanneer deze gateway opnieuw wordt ingesteld of wanneer andere interne onderhoudswerkzaamheden of upgrades worden uitgevoerd.

     * **Openbaar IP-adres**: Laat **Nieuwe maken** geselecteerd.
     * **Openbare IP-adresnaam**: Typ in het tekstvak een naam voor het exemplaar van uw openbare IP-adres.
     * **Toewijzing**: VPN-gateway ondersteunt alleen Dynamisch.
     * **De modus actief-actief inschakelen**: Selecteer **Modus actief-actief inschakelen** alleen als u een gatewayconfiguratie van het type actief-actief maakt. Anders laat u deze instelling **Uitgeschakeld**.
     * Laat **BGP configureren** ingesteld **Uitgeschakeld**, tenzij dit voor uw configuratie-instelling nodig is. Als u deze instelling wel nodig hebt, is de ASN standaard 65515. U kunt deze waarde wijzigen.
1. Selecteer **Beoordelen en maken** om de validatie uit te voeren.
1. Wanneer de validatie is geslaagd, selecteert u **Maken** om de VPN-gateway te implementeren. Het kan tot 45 minuten duren voordat een gateway volledig is gemaakt en geïmplementeerd. U kunt de implementatiestatus bekijken op de overzichtspagina van uw gateway.

Nadat de gateway is aangemaakt, kunt u het IP-adres dat eraan is toegewezen bekijken door naar het virtuele netwerk in de portal te kijken. De gateway wordt weergegeven als verbonden apparaat.
