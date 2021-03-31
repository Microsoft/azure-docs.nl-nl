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
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 2325509f68ced7c66d9f733b07247ae01301b565
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "82181529"
---
## <a name="test-your-code"></a>Uw code testen

Druk op **F5** in Visual Studio om uw project uit te voeren. De **MainWindow** van uw toepassing wordt weergegeven, zoals hier wordt getoond:

![Uw toepassing testen](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

De eerste keer dat u de toepassing uitvoert en de knop **Microsoft Graph API aanroepen** selecteert, wordt u gevraagd om u aan te melden. Gebruik een Azure Active Directory-account (werk- of schoolaccount) of een Microsoft-account (live.com, outlook.com) om de toepassing te testen.

![Aanmelden bij Azure Portal](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>Toestemming voor toegang tot de toepassing geven

De eerste keer dat u zich bij uw toepassing aanmeldt, wordt u ook gevraagd om de toepassing toestemming te geven om uw profiel te openen en u aan te melden, zoals hier wordt weergegeven:

![De toepassing toestemming geven voor toegang](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>Resultaten van de toepassing weergeven

Nadat u zich hebt aangemeld, zouden de gebruikersprofielgegevens die worden geretourneerd door de aanroep aan de Microsoft Graph-API moeten worden weergegeven. De resultaten worden weergegeven in het vak met de **resultaten van de API-aanroep**. Basisgegevens van het token dat is verkregen via de aanroep aan `AcquireTokenInteractive` of `AcquireTokenSilent`, moeten zichtbaar zijn in het vak **Tokengegevens**. De resultaten bevatten de volgende eigenschappen:

|Eigenschap  |Indeling  |Beschrijving |
|---------|---------|---------|
|**Gebruikersnaam** |<span>user@domain.com</span> |De gebruikersnaam die is gebruikt om de gebruiker te identificeren.|
|**Vervaldatum van token** |DateTime |Het moment waarop het token verloopt. MSAL verlengt de vervaldatum door het token wanneer nodig te vernieuwen.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Meer informatie over bereiken en gedelegeerde machtigingen

De Microsoft Graph API vereist het bereik *user.read* om het profiel van een gebruiker te lezen. Dit bereik wordt standaard automatisch toegevoegd in elke toepassing die is geregistreerd in de toepassingsregistratieportal. Overige API's voor Microsoft Graph, evenals aangepaste API's voor uw back-endserver, vereisen mogelijk aanvullende bereiken. De Microsoft Graph API vereist het bereik *Calendars.Read* om de agenda's van de gebruiker weer te geven.

Als u toegang wilt krijgen tot de agenda van de gebruiker in de context van een toepassing, voegt u de gedelegeerde toestemming *Calendars.Read* toe aan de toepassingsregistratiegegevens. Voeg vervolgens het bereik *Calendars.Read* toe aan de oproep `acquireTokenSilent`.

>[!NOTE]
>De gebruiker wordt mogelijk gevraagd om aanvullende machtigingen te geven naarmate u het aantal bereiken verhoogt.

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
