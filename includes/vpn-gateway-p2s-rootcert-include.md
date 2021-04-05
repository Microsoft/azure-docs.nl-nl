---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/28/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 59d18c89a8f66f781f01d1dace9d8968df17a626
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93041535"
---
Het .cer-bestand voor het basiscertificaat verkrijgen. U kunt een basiscertificaat gebruiken dat is gegenereerd met een commerciële oplossing (aanbevolen) of een zelfondertekend certificaat genereren. Nadat u het basiscertificaat hebt gemaakt, exporteert u de gegevens van het openbare certificaat (niet de persoonlijke sleutel) als een X.509 CER-bestand dat is gecodeerd met Base64. U uploadt dit bestand later naar Azure.

* **Commercieel certificaat** Als u een bedrijfsoplossing gebruikt, kunt u de bestaande certificaatketen gebruiken. Haal het CER-bestand op dat u wilt gebruiken voor het basiscertificaat.
* **Zelfondertekend basiscertificaat**: Als u geen commerciële certificeringsoplossing gebruikt, maakt u een zelfondertekend basiscertificaat. Anders zijn de certificaten die u maakt niet compatibel met uw P2S-verbindingen en treedt er voor clients een verbindingsfout op tijdens het verbinden. U kunt Azure PowerShell, MakeCert of OpenSSL gebruiken. In de stappen in de volgende artikelen wordt beschreven hoe u een compatibel, zelfondertekend basiscertificaat genereert:

  * [Instructies voor Windows 10 PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md): bij deze instructies zijn Windows 10 en PowerShell vereist voor het genereren van certificaten. Clientcertificaten die worden gegenereerd op basis van het basiscertificaat kunnen op alle ondersteunde P2S-clients worden ondersteund.
  * [MakeCert-instructies](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): gebruik MakeCert als u geen toegang hebt tot een Windows 10-computer om certificaten te genereren. MakeCert is inmiddels afgeschaft, maar u kunt het nog wel gebruiken om certificaten te genereren. Clientcertificaten die u genereert op basis van het basiscertificaat kunnen worden geïnstalleerd op alle ondersteunde P2S-clients.
  * [Instructies voor Linux](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md).