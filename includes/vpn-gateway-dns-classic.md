---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/08/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0c5e3a095b312d36cb05a78e84a13bc2a73d95d5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91875543"
---
DNS-instellingen zijn geen vereist onderdeel van deze configuratie, maar DNS is nodig als u de naam omzetting tussen uw Vm's wilt. Het opgeven van een waarde betekent niet dat er een nieuwe DNS-server wordt gemaakt. Het IP-adres van de DNS-server dat u opgeeft, moet het adres zijn van een DNS-server zijn die de namen kan omzetten voor de resources waarmee u verbinding maakt.

Nadat u uw virtuele netwerk hebt gemaakt, kunt u het IP-adres van een DNS-server toevoegen om de naamomzetting te verwerken. Open de instellingen voor het virtuele netwerk, selecteer DNS-servers en voeg het IP-adres toe van de DNS-server die u wilt gebruiken voor naam omzetting.

1. Zoek het virtuele netwerk in de portal.
2. Selecteer op de pagina voor uw virtuele netwerk onder de sectie **instellingen** de optie **DNS-servers**.
3. Voeg een DNS-server toe.
4. Selecteer boven aan de pagina **Opslaan** om uw instellingen op te slaan.