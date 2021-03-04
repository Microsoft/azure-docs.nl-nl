---
title: Modus voor gedeeld apparaat voor Android-apparaten
titleSuffix: Microsoft identity platform | Azure
description: Meer informatie over het inschakelen van de modus gedeeld apparaat zodat Firstline werk rollen een Android-apparaat kunnen delen
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: marsma
ms.reviewer: hahamil
ms.custom: aaddev, identitypla | Azuretformtop40
ms.openlocfilehash: ea902b8b609d226f6bb9e61ec0bfd60f75a3825d
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102038574"
---
# <a name="shared-device-mode-for-android-devices"></a>Modus voor gedeeld apparaat voor Android-apparaten

>[!IMPORTANT]
> Deze functie [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

Firstline-werk rollen, zoals Retail Associates, leden van de vliegtuig bemanning en mede werkers van de Field service gebruiken vaak een gedeeld mobiel apparaat om hun werk uit te voeren. Dit wordt problematisch wanneer ze beginnen met het delen van wacht woorden of pincodes om toegang te krijgen tot klant-en bedrijfs gegevens op het gedeelde apparaat.

In de modus gedeeld apparaat kunt u een Android-apparaat configureren zodat het eenvoudig kan worden gedeeld door meerdere werk nemers. Werk nemers kunnen zich snel aanmelden en klant gegevens benaderen. Wanneer de taken zijn voltooid met hun verschuiving of taak, kunnen ze zich afmelden bij het apparaat en zal het direct klaar zijn voor de volgende werk nemer.

De modus gedeeld apparaat biedt ook micro soft-identiteits beheer van het apparaat.

Als u een app voor gedeelde apparaten wilt maken, werken ontwikkel aars en beheerders van Cloud apparaten samen:

- Ontwikkel aars schrijven een app met één account (apps met meerdere accounts worden niet ondersteund in de modus gedeeld apparaat), toevoegen `"shared_device_mode_supported": true` aan de configuratie van de app en code schrijven voor het afhandelen van dingen zoals het afmelden van gedeelde apparaten.
- Apparaat beheerders maken het apparaat klaar om te worden gedeeld door de verificator-app te installeren en het apparaat in te stellen op de gedeelde modus met de verificator-app. Alleen gebruikers met de rol [beheerder van Cloud apparaat](../roles/permissions-reference.md#cloud-device-administrator) kunnen een apparaat in de gedeelde modus plaatsen met behulp van de [verificator-app](../user-help/user-help-auth-app-overview.md). U kunt het lidmaatschap van uw organisatie rollen in de Azure Portal configureren via: **Azure Active Directory**-  >  **rollen en-Administrators** van de  >  **Cloud apparaat-beheerder**.

 In dit artikel wordt voornamelijk aandacht besteed aan ontwikkel aars.

## <a name="single-vs-multiple-account-applications"></a>Enkele versus toepassingen met meerdere accounts

Toepassingen die zijn geschreven met behulp van de micro soft Authentication Library SDK (MSAL) kunnen één of meerdere accounts beheren. Zie de modus voor [één account of meerdere accounts](single-multi-account.md)voor meer informatie. De micro soft Identity platform-functies die beschikbaar zijn voor uw app variëren, afhankelijk van het feit of de toepassing wordt uitgevoerd in de modus voor één account of in de modus voor meerdere accounts.

**Modus voor gedeelde apparaten-apps werken alleen in de modus voor één account**.

> [!IMPORTANT]
> Toepassingen die alleen de modus meerdere accounts ondersteunen, kunnen niet worden uitgevoerd op een gedeeld apparaat. Als een werk nemer een app laadt die geen ondersteuning biedt voor de modus met één account, wordt deze niet uitgevoerd op het gedeelde apparaat.
>
> Apps die zijn geschreven voordat de MSAL SDK werd uitgebracht, worden uitgevoerd in de modus voor meerdere accounts en moeten worden bijgewerkt om de modus voor één account te ondersteunen voordat ze kunnen worden uitgevoerd op een apparaat in de gedeelde modus.

**Ondersteuning voor zowel één account als meerdere accounts**

Uw app kan worden gebouwd om te ondersteunen die wordt uitgevoerd op zowel persoonlijke apparaten als gedeelde apparaten. Als uw app momenteel meerdere accounts ondersteunt en u de modus gedeeld apparaat wilt ondersteunen, voegt u ondersteuning toe voor de modus voor één account.

Mogelijk wilt u ook dat uw app het gedrag ervan wijzigt, afhankelijk van het type apparaat waarop het wordt uitgevoerd. Gebruiken `ISingleAccountPublicClientApplication.isSharedDevice()` om te bepalen wanneer moet worden uitgevoerd in de modus voor één account.

Er zijn twee verschillende interfaces die het type apparaat vertegenwoordigen waarop uw toepassing zich bevindt. Wanneer u een instantie van een toepassing aanvraagt vanuit de Application Factory van MSAL, wordt automatisch het juiste toepassings object gegeven.

Het volgende object model illustreert het type object dat u kunt ontvangen en wat het betekent in de context van een gedeeld apparaat:

![overname model voor open bare client toepassing](media/v2-shared-device-mode/ipublic-client-app-inheritance.png)

Als u uw object ontvangt, moet u een type controle uitvoeren en naar de juiste interface casten `PublicClientApplication` . Met de volgende code wordt gecontroleerd op de modus voor meerdere accounts of één account en wordt het toepassings object op de juiste wijze geconverteerd:

```java
private IPublicClientApplication mApplication;

        // Running in personal-device mode?
        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        // Running in shared-device mode?
        } else if (mApplication instanceOf ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

De volgende verschillen zijn van toepassing, afhankelijk van het feit of uw app wordt uitgevoerd op een gedeeld of persoonlijk apparaat:

|  | Apparaat voor gedeelde modus  | Persoonlijk apparaat |
|---------|---------|---------|
| **Accounts**     | Eén account | Meerdere accounts |
| **Aanmelden** | Globaal | Globaal |
| **Afmelden** | Globaal | Elke toepassing kan bepalen of de afmelding lokaal is voor de app of voor de reeks toepassingen. |
| **Ondersteunde accounttypen** | Alleen werk accounts | Persoonlijke en werk accounts worden ondersteund  |

## <a name="why-you-may-want-to-only-support-single-account-mode"></a>Waarom u alleen één-account modus wilt ondersteunen

Als u een app schrijft die alleen wordt gebruikt voor firstline-werk nemers met behulp van een gedeeld apparaat, wordt u aangeraden uw toepassing te schrijven zodat deze alleen ondersteuning biedt voor de modus met één account. Dit omvat de meeste toepassingen die zijn gericht op taken, zoals apps voor medische dossiers of voor facturen, en de meeste Line-Of-Business-apps. De ondersteuning voor de modus voor één account vereenvoudigt de ontwikkeling omdat u de aanvullende functies die deel uitmaken van apps met meerdere accounts niet hoeft te implementeren.

## <a name="what-happens-when-the-device-mode-changes"></a>Wat er gebeurt wanneer de modus van het apparaat wordt gewijzigd

Als uw toepassing wordt uitgevoerd in de modus voor meerdere accounts en een beheerder het apparaat in de modus gedeeld apparaat plaatst, worden alle accounts op het apparaat uit de toepassing gewist en wordt de toepassing overgezet naar de modus met één account.

## <a name="shared-device-sign-out-and-the-overall-app-lifecycle"></a>Afmelding van gedeeld apparaat en de levens cyclus van de hele app

Wanneer een gebruiker zich afmeldt, moet u actie ondernemen om de privacy en de gegevens van de gebruiker te beveiligen. Als u bijvoorbeeld een medische record toepassing bouwt, wilt u er zeker van zijn dat wanneer de gebruiker eerder weer gegeven patiënten-records wist, wordt gewist. Uw toepassing moet hiervoor worden voor bereid en elke keer dat deze wordt geactiveerd, wordt gecontroleerd.

Als uw app MSAL gebruikt voor het afmelden van de gebruiker in een app die wordt uitgevoerd op een apparaat in de gedeelde modus, worden het aangemelde account en de in de cache opgeslagen tokens verwijderd uit de app en het apparaat.

In het volgende diagram ziet u de algemene levens cyclus van de app en algemene gebeurtenissen die kunnen optreden tijdens de uitvoering van uw app. Het diagram gaat uit van het tijdstip waarop een activiteit wordt gestart, het aanmelden en afmelden van een account en hoe gebeurtenissen zoals het onderbreken, hervatten en stoppen van de activiteit in passen.

![Levens cyclus van gedeelde apparaat-app](media/v2-shared-device-mode/lifecycle.png)

## <a name="next-steps"></a>Volgende stappen

Probeer de [modus gedeeld apparaat gebruiken in de zelf studie voor Android-toepassingen](tutorial-v2-shared-device-mode.md) om te zien hoe u een firstline worker-app uitvoert op een Android-apparaat in de gedeelde modus.
