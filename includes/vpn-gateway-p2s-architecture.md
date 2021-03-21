---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/30/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e3ad8fcb6cd5cf68b02ac522d0e5ef5a18ba8a3c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93144989"
---
Punt-naar-site systeem eigen Azure-certificaat authenticatie verbindingen gebruiken de volgende items, die u in deze oefening configureert:

* Een RouteBased VPN-gateway.
* De openbare sleutel (CER-bestand) voor een basiscertificaat dat is geüpload naar Azure. Nadat het certificaat is geüpload, wordt het beschouwd als een vertrouwd certificaat en wordt het gebruikt voor verificatie.
* Een clientcertificaat dat is gegenereerd op basis van het basiscertificaat. Het clientcertificaat dat is geïnstalleerd op elke clientcomputer die met het VNet verbinding zal maken. Dit certificaat wordt gebruikt voor clientverificatie.
* VPN-client configuratie. De VPN-client is geconfigureerd met configuratie bestanden voor VPN-clients. Deze bestanden bevatten de benodigde informatie die de client nodig heeft om verbinding te maken met het VNet. Met de bestanden wordt de bestaande VPN-client geconfigureerd die is ingebouwd in het besturingssysteem. Elke client die verbinding maakt, moet worden geconfigureerd met behulp van de instellingen in de configuratiebestanden.
