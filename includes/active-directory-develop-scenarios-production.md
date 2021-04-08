---
title: bestand opnemen
description: bestand opnemen
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 75650d7ff0ac647aeb6dace76c270680b1b89347
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954953"
---
## <a name="enable-logging"></a>Logboekregistratie inschakelen

Voor hulp bij het oplossen van problemen met fout opsporing en verificatie, biedt de micro soft-verificatie bibliotheek ingebouwde ondersteuning voor logboek registratie. Bij logboek registratie wordt elke bibliotheek behandeld in de volgende artikelen:

:::row:::
    :::column:::
        - [Logboekregistratie in MSAL.NET](../articles/active-directory/develop/msal-logging-dotnet.md)
        - [Logboekregistratie in MSAL voor Android](../articles/active-directory/develop/msal-logging-android.md)
        - [Logboekregistratie in MSAL.js](../articles/active-directory/develop/msal-logging-js.md)
    :::column-end:::
    :::column:::
        - [Logboekregistratie in MSAL voor iOS/macOS](../articles/active-directory/develop/msal-logging-ios.md)
        - [Logboekregistratie in MSAL voor Java](../articles/active-directory/develop/msal-logging-java.md)
        - [Logboekregistratie in MSAL voor Python](../articles/active-directory/develop/msal-logging-python.md)
    :::column-end:::
:::row-end:::

Hier volgen enkele suggesties voor het verzamelen van gegevens:

- Gebruikers kunnen om hulp vragen wanneer ze problemen ondervinden. Een best practice is Logboeken vastleggen en tijdelijk opslaan. Geef een locatie op waar gebruikers de logboeken kunnen uploaden. MSAL biedt logboek registratie-extensies voor het vastleggen van gedetailleerde informatie over verificatie.

- Als telemetrie beschikbaar is, kunt u dit inschakelen via MSAL om gegevens te verzamelen over de manier waarop gebruikers zich aanmelden bij uw app.


## <a name="validate-your-integration"></a>Uw integratie valideren

Test uw integratie door de [controle lijst voor de integratie van micro soft Identity platform](../articles/active-directory/develop/identity-platform-integration-checklist.md)te volgen.
