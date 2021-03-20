---
title: Problemen met Internet Explorer (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Gebruik de micro soft-verificatie bibliotheek voor Java script (MSAL.js) met de browser Internet Explorer.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 73dd7877b399d34a009c9fe479550eb1bf6dc0f1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99583973"
---
# <a name="known-issues-on-internet-explorer-browsers-msaljs"></a>Bekende problemen met browsers in Internet Explorer (MSAL.js)

De micro soft-verificatie bibliotheek voor Java script (MSAL.js) wordt gegenereerd voor [Java script-ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) zodat deze kan worden uitgevoerd in Internet Explorer. Er zijn echter enkele dingen die u moet weten.

## <a name="run-an-app-in-internet-explorer"></a>Een app uitvoeren in Internet Explorer
Als u van plan bent om MSAL.js te gebruiken in toepassingen die kunnen worden uitgevoerd in Internet Explorer, moet u een verwijzing toevoegen aan de polyvulling van een Promise voordat u naar het MSAL.js script gaat.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

Dit komt omdat Internet Explorer geen systeem eigen ondersteuning biedt voor Java script-beloftes.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Fout opsporing in een toepassing die wordt uitgevoerd in Internet Explorer

### <a name="running-in-production"></a>Uitvoeren in productie
Het implementeren van uw toepassing naar productie (bijvoorbeeld in azure web apps) werkt normaal, vooropgesteld dat de eind gebruiker pop-ups heeft geaccepteerd. We hebben het getest met Internet Explorer 11.

### <a name="running-locally"></a>Lokaal uitvoeren
Als u lokaal wilt uitvoeren en fouten wilt opsporen in uw toepassing die wordt uitgevoerd in Internet Explorer, moet u rekening houden met de volgende overwegingen (Stel dat u uw toepassing wilt uitvoeren als *http://localhost:1234* ):

- Internet Explorer heeft een beveiligings mechanisme met de naam ' beveiligde modus ', waarmee wordt voor komen dat MSAL.js goed werkt. Nadat u zich hebt aangemeld, kunt u onder andere de pagina omleiden naar http://localhost:1234/null .

- Als u uw toepassing lokaal wilt uitvoeren en fouten wilt opsporen, moet u deze beveiligde modus uitschakelen. Voor dit:

    1. Klik op **Hulpprogram ma's** voor Internet Explorer (het tandwiel pictogram).
    1. Selecteer **Internet opties** en klik vervolgens op het tabblad **beveiliging** .
    1. Klik op de zone **Internet** en Schakel **beveiligde modus inschakelen uit (hiervoor moet Internet Explorer opnieuw worden gestart)**. Internet Explorer waarschuwt dat uw computer niet meer is beveiligd. Klik op **OK**.
    1. Start Internet Explorer opnieuw.
    1. Uw toepassing uitvoeren en fouten opsporen.

Wanneer u klaar bent, herstelt u de beveiligings instellingen van Internet Explorer.  **Instellingen** selecteren  ->  **Internet opties**  ->  **beveiliging**  ->  **alle zones opnieuw instellen op standaard niveau**.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [bekende problemen bij het gebruik van MSAL.js in Internet Explorer](msal-js-use-ie-browser.md).