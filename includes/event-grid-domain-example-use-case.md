---
title: bestand opnemen
description: bestand opnemen
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 03/04/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2459cf6c5055dcde83dccf37addc160aeeaa64ad
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198648"
---
Gebeurtenis domeinen worden het eenvoudigst uitgelegd met een voor beeld. Stel dat u contoso-bouw machines uitvoert, waarbij u tractoren, Blijf spitten-apparatuur en andere zware machines produceert. Als onderdeel van het uitvoeren van het bedrijf pusht u real-time informatie naar klanten over het onderhoud van apparaten, systeem status en contract updates. Al deze informatie gaat naar verschillende eind punten, met inbegrip van uw app, klant eindpunten en andere infra structuur die klanten hebben ingesteld.

Met gebeurtenis domeinen kunt u contoso-bouw machines als één gebeurtenis entiteit model leren. Elk van uw klanten wordt weer gegeven als een onderwerp binnen het domein. Verificatie en autorisatie worden afgehandeld met behulp van Azure Active Directory. Elk van uw klanten kan zich abonneren op hun onderwerp en hun gebeurtenissen ontvangen. Beheerde toegang via het gebeurtenis domein zorgt ervoor dat ze alleen toegang hebben tot hun onderwerp.

Het biedt u ook een enkel eind punt, waarmee u alle evenementen van uw klant kunt publiceren naar. Event Grid zorgt ervoor dat elk onderwerp alleen op de hoogte is van de gebeurtenissen die zijn afgestemd op de Tenant.

:::image type="content" source="./media/event-grid-domain-example-use-case/contoso-construction-example.png" alt-text="Voor beeld van Contoso-constructie" lightbox="./media/event-grid-domain-example-use-case/contoso-construction-example.png":::