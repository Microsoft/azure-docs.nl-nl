---
title: Toegestane CA voor het inschakelen van aangepaste HTTPS
titleSuffix: Azure Content Delivery Network
description: Als u uw eigen certificaat gebruikt om HTTPS in te scha kelen op een aangepast domein, moet u een toegestane certificerings instantie (CA) gebruiken om deze te maken.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: troubleshooting
ms.date: 02/04/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: f98e28c89fa70831108cfbbbaca6e2f316d1b039
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99573395"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https"></a>Toegestane certificerings instanties voor het inschakelen van aangepaste HTTPS

Specifieke certificaat vereisten zijn vereist wanneer u [de https-functie inschakelt met behulp van uw eigen certificaat](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) voor een Azure CDN (Content Delivery Network) aangepast domein. 

* Voor de **Azure CDN standaard van micro soft** -profiel is een certificaat van een van de goedgekeurde certificerings instanties (CA) in de volgende lijst vereist. Als een certificaat van een niet-goedgekeurde certificerings instantie of een zelfondertekend certificaat wordt gebruikt, wordt de aanvraag geweigerd. 

* **Azure CDN standaard van Verizon** en **Azure CDN Premium van Verizon** -profielen accepteren een geldig certificaat van een geldige certificerings instantie. Verizon-profielen bieden geen ondersteuning voor zelfondertekende certificaten.

> [!NOTE]
> De optie voor het gebruik van uw eigen certificaat om de HTTPS-functie van het aangepaste domein in te scha kelen is *niet* beschikbaar voor **Azure CDN standaard van Akamai** -profielen. 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

