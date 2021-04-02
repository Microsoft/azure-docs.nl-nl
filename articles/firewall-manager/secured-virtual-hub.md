---
title: Wat is een beveiligde virtuele hub?
description: Meer informatie over beveiligde virtuele hubs
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: victorh
ms.openlocfilehash: c840bf9e82b8dcdb1fbf9b380ea847b3d1b08dd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91948065"
---
# <a name="what-is-a-secured-virtual-hub"></a>Wat is een beveiligde virtuele hub?

Een virtuele hub is een virtueel netwerk dat door micro soft wordt beheerd en waarmee u verbinding kunt maken vanuit andere bronnen. Wanneer een virtuele hub wordt gemaakt op basis van een virtueel WAN in de Azure Portal, worden er een virtuele-hub VNet en gateways (optioneel) gemaakt als de bijbehorende onderdelen.

Een *beveiligde* virtuele hub is een [virtuele WAN-hub van Azure](../virtual-wan/virtual-wan-about.md#resources) met bijbehorende beveiligings-en routerings beleidsregels die door Azure firewall Manager zijn geconfigureerd. Gebruik beveiligde virtuele hubs om eenvoudig hub-en-spoke en transitieve architecturen te maken met systeem eigen beveiligings Services voor verkeers beheer en-beveiliging. 

U kunt een beveiligde virtuele hub gebruiken om verkeer te filteren tussen virtuele netwerken (V2V), virtuele netwerken en filialen (B2V) en verkeer naar Internet (B2I/V2I). Een beveiligde virtuele hub biedt geautomatiseerde route ring. U hoeft uw eigen Udr's (door de gebruiker gedefinieerde routes) niet te configureren om verkeer via uw firewall te routeren.

U kunt de vereiste beveiligings providers kiezen om uw netwerk verkeer te beveiligen en te beheren, met inbegrip van Azure Firewall, SECaaS-providers (Security as a Service) van derden, of beide. Op dit moment biedt een beveiligde hub geen ondersteuning voor het filteren en filteren van Branch to Branch (B2B) op meerdere hubs. Zie [Wat is Azure firewall Manager?](overview.md#known-issues)voor meer informatie. 

## <a name="create-a-secured-virtual-hub"></a>Een beveiligde virtuele hub maken

Met firewall manager in de Azure Portal kunt u een nieuwe beveiligde virtuele hub maken of een bestaande virtuele hub die u eerder hebt gemaakt met behulp van Azure Virtual WAN converteren.

## <a name="next-steps"></a>Volgende stappen

Zie [zelf studie: uw Cloud netwerk beveiligen met Azure firewall Manager met behulp van de Azure Portal](secure-cloud-network.md)om een beveiligde virtuele hub te maken en deze te gebruiken om een hub-en spoke-netwerk te beveiligen en te beheren.