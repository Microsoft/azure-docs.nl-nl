---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/28/2020
ms.author: cherylmc
ms.openlocfilehash: 34986ac80a309bcfd495e5782496ba560f84c5f7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93041580"
---
Op elke clientcomputer die u verbindt met een VNet via een punt-naar-site-verbinding, moet een clientcertificaat zijn geïnstalleerd. U genereert het clientcertificaat op basis van het basiscertificaat en installeert het clientcertificaat op elke clientcomputer. Als u geen geldig clientcertificaat hebt geïnstalleerd en de client probeert verbinding te maken met het VNet, mislukt de verificatie.

U kunt een uniek certificaat genereren voor elke client of hetzelfde certificaat gebruiken voor meerdere clients. Het voordeel van het genereren van unieke clientcertificaten is dat het mogelijk is om één certificaat in te trekken. Anders moet u, als meerdere clients hetzelfde certificaat voor verificatie gebruiken en u het certificaat intrekt, nieuwe certificaten genereren en installeren voor elke client die dat certificaat voor verificatie gebruikt.

U kunt clientcertificaten genereren op de volgende manieren:

* **Commercieel certificaat**

  * Als u een commerciële certificeringsoplossing gebruikt, genereert u een clientcertificaat met de algemene indeling voor de naamwaarde *naam\@uwdomein.com*. Gebruik deze indeling in plaats van de indeling *domeinnaam\gebruikersnaam*.

  * Zorg ervoor dat het clientcertificaat dat u verleent, is gebaseerd op de certificaatsjabloon voor *Gebruiker met Clientverificatie* als het eerste item in de lijst. U kunt het certificaat controleren door op het clientcertificaat te dubbelklikken en **Enhanced Key Usage** (Uitgebreid sleutelgebruik) weer te geven op het tabblad **Details**.

* **Zelfondertekend basiscertificaat**: Volg de stappen in een van de volgende artikelen over P2S-certificaten zodat de clientcertificaten die u maakt compatibel zijn met P2S-verbindingen.

  Als u een clientcertificaat genereert op basis van een zelfondertekend basiscertificaat, wordt het certificaat automatisch geïnstalleerd op de computer die u hebt gebruikt om het certificaat te genereren. Als u een clientcertificaat op een andere clientcomputer wilt installeren, moet u het certificaat samen met de gehele certificaatketen exporteren als PFX-bestand. Hiermee maakt u een PFX-bestand met alle gegevens van het basiscertificaat die nodig zijn voor de verificatie van de client.

  De stappen in deze artikelen zijn bedoeld om een compatibel clientcertificaat te maken dat u kunt exporteren en distribueren.

  * [Instructies voor Windows 10 PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert): bij deze instructies zijn Windows 10 en PowerShell vereist voor het genereren van certificaten. De gegenereerde certificaten kunnen worden geïnstalleerd op alle ondersteunde P2S-clients.

  * [Instructies voor MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): Gebruik MakeCert als u geen toegang hebt tot een Windows 10-computer voor het genereren van certificaten. MakeCert is inmiddels afgeschaft, maar u kunt het nog wel gebruiken om certificaten te genereren. U kunt de gegenereerde certificaten installeren op alle ondersteunde P2S-clients.

  * [Instructies voor Linux](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md).