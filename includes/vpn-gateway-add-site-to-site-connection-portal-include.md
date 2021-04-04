---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/22/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 48f33514510618abadf329a11a9ab71a020be0bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92479557"
---
1. Open de pagina voor uw virtuele netwerkgateway. U kunt naar de gateway gaan via **Naam van uw VNet -> Overzicht -> Verbonden apparaten -> Naam van uw gateway**, hoewel er ook nog andere manieren zijn om te navigeren.
1. Op de pagina voor de gateway selecteert u **Verbindingen**. Selecteer bovenaan de pagina Verbindingen op **+ Toevoegen** om de pagina **Verbinding toevoegen** te openen.

   :::image type="content" source="./media/vpn-gateway-add-site-to-site-connection-portal-include/connection.png" alt-text="Site-naar-site-verbinding":::
1. Op de pagina **Verbinding toevoegen** configureert u de waarden die nodig zijn om verbinding te maken.

   * **Naam:** de naam van de verbinding.
   * **Verbindingstype:** Selecteer **Site-naar-site (IPsec)** .
   * **Virtuele netwerkgateway:** hiervoor geldt een vaste waarde, omdat u verbinding maakt vanaf deze gateway.
   * **Lokale netwerkgateway:** Selecteer **Een lokale netwerkgateway kiezen** en selecteer de lokale netwerkgateway die u wilt gebruiken.
   * **Gedeelde sleutel:** de waarde hier moet overeenkomen met de waarde die u voor uw lokale on-premises VPN-apparaat gebruikt. In het voorbeeld wordt 'abc123' gebruikt, maar u kunt een ingewikkeldere waarde gebruiken (aanbevolen). Het is van belang dat de waarde die u hier opgeeft, dezelfde waarde is die u hebt opgegeven bij het configureren van het VPN-apparaat.
   * Laat **Privé-IP-adres van Azure gebruiken** uitgeschakeld.
   * Laat **BGP inschakelen** uitgeschakeld.
   * Selecteer **IKEv2**.
   * De resterende waarden voor **Abonnement**, **Resourcegroep**, en **Locatie** zijn vast.

1. Selecteer **OK** om uw verbinding te maken. U ziet *Verbinding maken* op het scherm knipperen.
1. U kunt de verbinding bekijken op de pagina **Verbindingen** van de virtuele netwerkgateway. De status verandert van *Onbekend* in *Verbinding maken* en vervolgens in *Voltooid*.
