---
title: UI Framework-teams insluiten
titleSuffix: An Azure Communication Services quickstart
description: In dit document leert u hoe de functionaliteit van de Azure Communication Services UI Framework-invoeg toepassing kan worden gebruikt om kant-en-klare aanroepen te maken.
author: tophpalmer
ms.author: chpalm
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 8301f81b21db50814df1bc764cc1fae38b4f14de
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307636"
---
# <a name="teams-embed"></a>Teams insluiten

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]


Teams insluiten is een Azure Communication Services-functie die gericht is op algemene bedrijfs-to-consumer-en Business-to-Business-oproep interacties. De kern van het insluitings systeem van de teams is het [aanroepen van video en spraak](../voice-video-calling/calling-sdk-features.md), maar de teams insluiten systeem builds op basis van de aanroepende primitieven van Azure om een volledige gebruikers ervaring te bieden die is gebaseerd op vergaderingen van micro soft teams.

Teams insluiten Sdk's zijn gesloten-bron en maken deze mogelijkheden beschikbaar voor u in een kant-en-klare, samengestelde vorm. U kunt teams in het canvas van uw app neerzetten en de SDK genereert een complete gebruikers ervaring. Omdat deze gebruikers ervaring lijkt op vergaderingen van micro soft teams, kunt u profiteren van:

- Minder ontwikkelings tijd en technische complexiteit
- Eindgebruikers ervaring met teams
- De mogelijkheid om inhoud van de [training voor eind gebruikers van teams](https://support.microsoft.com/office/meetings-in-teams-e0b0ae21-53ee-4462-a50d-ca9b9e217b67) opnieuw te gebruiken

De functie voor het insluiten van teams biedt de meeste functies die worden ondersteund in teams vergaderingen, waaronder:

- Voorbereidende ervaring waarbij een gebruiker hun audio-en video apparaten configureert
- In-Meeting-ervaring voor het configureren van audio-en video apparaten
- [Video-achtergronden](https://support.microsoft.com/office/change-your-background-for-a-teams-meeting-f77a2381-443a-499d-825e-509a140f4780): toestaan dat deel nemers hun achtergronden kunnen vervagen of vervangen
- [Meerdere opties voor de](https://support.microsoft.com/office/using-video-in-microsoft-teams-3647fc29-7b92-4c26-8c2d-8a596904cdae) uitgebreide galerie met video galerie, samen modus, focus, vastmaken en Spotlight
- [Delen van inhoud](https://support.microsoft.com/en-us/office/share-content-in-a-meeting-in-teams-fcc2bf59-aecd-4481-8f99-ce55dd836ce8): deel nemers toestaan hun scherm te delen

Voor meer informatie over deze gebruikers interface in vergelijking met andere Sdk's van Azure Communication raadpleegt u de [Inleiding over UI SDK-concepten](ui-sdk-overview.md). 
