---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 393b29245141b2970e7c1a227d6e8b1b131c445c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93061602"
---
Als u een P2S-verbinding wilt maken vanaf een andere clientcomputer dan de computer die u gebruikt om de clientcertificaten te genereren, moet u een clientcertificaat installeren. Wanneer u een clientcertificaat installeert, hebt u het wachtwoord nodig dat is gemaakt tijdens het exporteren van het clientcertificaat.

1. Zoek het *PFX*-bestand en kopieer het naar de clientcomputer. Dubbelklik op de clientcomputer op het *PFX*-bestand om het te installeren. Wijzig de **opslag locatie** als **huidige gebruiker** en selecteer **volgende**.
1. Laat de pagina **Te exporteren bestand** ongewijzigd. Selecteer **Next**.
1. Voer op de pagina **beveiliging met persoonlijke sleutel** het wacht woord in voor het certificaat of Controleer of de beveiligingsprincipal juist is en selecteer **volgende**.
1. Accepteer op de pagina **certificaat archief** de standaard locatie en selecteer **volgende**.
1. Selecteer **Finish**. Selecteer op de **beveiligings waarschuwing** voor de installatie van het certificaat de optie **Ja**. U kunt de optie Ja selecteren voor deze beveiligings waarschuwing omdat u het certificaat hebt gegenereerd.
1. Het certificaat wordt nu geïmporteerd.
