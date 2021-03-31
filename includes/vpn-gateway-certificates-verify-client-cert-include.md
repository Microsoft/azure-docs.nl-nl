---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c0ce4e882f270f5e0c789a608aaada5c6c9cba92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "67175894"
---
Als u problemen hebt met de verbinding, controleer dan het volgende:

- Als u een clientcertificaat hebt geëxporteerd met de **Wizard Certificaat exporteren**, moet u ervoor zorgen dat u dit als een .pfx-bestand exporteert en dat u **Indien mogelijk alle certificaten in het certificeringspad opnemen** hebt geselecteerd. Als u het certificaat met behulp van deze waarde exporteert, worden de gegevens van het basiscertificaat ook geëxporteerd. Nadat u het certificaat op de clientcomputer hebt geïnstalleerd, wordt het basiscertificaat in het .pbx-bestand ook geïnstalleerd. Open **Gebruikerscertificaten beheren** en selecteer **Vertrouwde basiscertificeringsinstanties\Certificaten** om te controleren of het basiscertificaat is geïnstalleerd. Controleer of het basiscertificaat wordt vermeld, anders werkt de verificatie niet.

- Als u een certificaat hebt gebruikt dat is uitgegeven door een CA-oplossingen voor ondernemingen en u niet kunt verifiëren, controleert u de verificatievolgorde op het clientcertificaat. Controleer de verificatievolgorde door dubbel te klikken in het clientcertificaat, het tabblad **Details** te selecteren en vervolgens **Enhanced Key Usage**. Zorg ervoor dat *Clientverificatie* het eerste item in de lijst is. Als dat niet het geval is, moet u een clientcertificaat op basis van de gebruikerssjabloon verlenen waarin *Clientverificatie* het eerste item is in de lijst.

- Zie [Troubleshoot P2S connections](../articles/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) (Problemen met P2S-verbindingen oplossen) voor extra informatie over het oplossen van problemen met P2S,.