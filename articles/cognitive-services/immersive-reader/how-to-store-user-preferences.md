---
title: Gebruikersvoorkeuren opslaan
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u de voor keuren van de gebruiker opslaat.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: 16ecd2166604d29fbc2242229f625b30ffd684e5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102617689"
---
# <a name="how-to-store-user-preferences"></a>Gebruikers voorkeuren opslaan

In dit artikel wordt beschreven hoe u de gebruikers interface-instellingen van de gebruiker, formeel bekend als **gebruikers voorkeuren**, kunt opslaan via de opties [voor voor keuren](./reference.md#options) en [-ONPREFERENCESCHANGED](./reference.md#options) insluitende lezer SDK.

Wanneer de [CookiePolicy](./reference.md#cookiepolicy-options) SDK-optie is ingesteld op *ingeschakeld*, slaat de toepassing voor insluitende lezer de **gebruikers voorkeuren** (tekst grootte, thema kleur, letter type enzovoort) op in cookies, die lokaal zijn voor een specifieke browser en een specifiek apparaat. Telkens wanneer de gebruiker de insluitende lezer op dezelfde browser en hetzelfde apparaat start, wordt deze geopend met de voor keuren van de gebruiker van de laatste sessie op het apparaat. Als de gebruiker echter de insluitende lezer op een andere browser of een ander apparaat opent, worden de instellingen in eerste instantie geconfigureerd met de standaard instellingen van de insluitende lezer. de gebruiker moet de voor keuren opnieuw instellen, enzovoort voor elk apparaat dat ze gebruiken. De- `-preferences` en `-onPreferencesChanged` insluitende Reader SDK-opties bieden een manier om toepassingen de voor keuren van een gebruiker te laten zwerven over verschillende browsers en apparaten, zodat de gebruiker een consistente ervaring heeft wanneer ze de toepassing gebruiken.

Ten eerste, door de `-onPreferencesChanged` call back SDK-optie op te geven bij het starten van de toepassing voor insluitende lezer, stuurt de insluitende lezer een `-preferences` teken reeks terug naar de hosttoepassing telkens wanneer de gebruiker de voor keuren wijzigt tijdens de insluitende lezer-sessie. De hosttoepassing is dan verantwoordelijk voor het opslaan van de gebruikers voorkeuren in hun eigen systeem. Wanneer dezelfde gebruiker vervolgens de insluitende lezer weer start, kan de hosttoepassing de voor keuren van die gebruiker ophalen uit de opslag, en deze opgeven als de `-preferences` teken reeks-SDK-optie bij het starten van de toepassing voor insluitende lezer, zodat de voor keuren van de gebruiker worden hersteld.

Deze functionaliteit kan worden gebruikt als een alternatieve manier om **gebruikers voorkeuren** op te slaan in het geval dat het gebruik van cookies niet gewenst of haalbaar is.

> [!CAUTION]
> **Belang rijk** Probeer niet programmatisch de waarden te wijzigen van de `-preferences` teken reeks die wordt verzonden naar en van de toepassing voor insluitende lezer. Dit kan leiden tot onverwacht gedrag als gevolg van een gedegradeerde gebruikers ervaring voor uw klanten. Hosttoepassingen mogen nooit een aangepaste waarde toewijzen aan of de `-preferences` teken reeks bewerken. `-preferences`Gebruik de optie teken reeks om alleen de exacte waarde te gebruiken die is geretourneerd met de `-onPreferencesChanged` optie terugbellen.

## <a name="how-to-enable-storing-user-preferences"></a>Het opslaan van gebruikers voorkeuren inschakelen

de [launchAsync](./reference.md#launchasync) -para meter voor de insluitende lezer-SDK `options` bevat de `-onPreferencesChanged` retour aanroep. Deze functie wordt aangeroepen wanneer de gebruiker de voor keuren wijzigt. De `value` para meter bevat een teken reeks die de huidige voor keuren van de gebruiker vertegenwoordigt. Deze teken reeks wordt vervolgens door de hosttoepassing opgeslagen voor die gebruiker.

```typescript
const options = {
    onPreferencesChanged: (value: string) => {
        // Store user preferences here
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="how-to-load-user-preferences-into-the-immersive-reader"></a>Gebruikers voorkeuren laden in de insluitende lezer

Geef de voor keuren van de gebruiker door aan de insluitende lezer met behulp van de `-preferences` optie. Een trivial voor beeld om de voor keuren van de gebruiker op te slaan en te laden, is als volgt:

```typescript
const storedUserPreferences = localStorage.getItem("USER_PREFERENCES");
let userPreferences = storedUserPreferences === null ? null : storedUserPreferences;
const options = {
    preferences: userPreferences,
    onPreferencesChanged: (value: string) => {
        userPreferences = value;
        localStorage.setItem("USER_PREFERENCES", userPreferences);
    }
};
```

## <a name="next-steps"></a>Volgende stappen

* Lees de [naslagdocumentatie voor de Immersive Reader-SDK](./reference.md).