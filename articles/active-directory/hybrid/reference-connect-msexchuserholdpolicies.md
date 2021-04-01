---
title: 'Azure AD Connect: msExchUserHoldPolicies en cloudMsExchUserHoldPolicies | Microsoft Docs'
description: In dit onderwerp wordt het kenmerk gedrag van de kenmerken msExchUserHoldPolicies en cloudMsExchUserHoldPolicies beschreven
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 09/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0fbda588d99de44c77118586519055a8fc474104
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861762"
---
# <a name="azure-ad-connect---msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Azure AD Connect-msExchUserHoldPolicies en cloudMsExchUserHoldPolicies
In het volgende referentie document worden deze kenmerken beschreven die worden gebruikt door Exchange en de juiste manier om de standaard synchronisatie regels te bewerken.

## <a name="what-are-msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Wat zijn msExchUserHoldPolicies en cloudMsExchUserHoldPolicies?
Er zijn twee soorten [bewaringen](/Exchange/policy-and-compliance/holds/holds) beschikbaar voor een Exchange-Server: Beschik baarheid en In-Place in de wacht stand. Als er een afwachting is ingeschakeld, worden alle items in de wachtrij geplaatst.  Een In-Place Hold wordt gebruikt om alleen die items te bewaren die voldoen aan de criteria van een zoek opdracht die u met het hulp programma In-Place eDiscovery hebt gedefinieerd.

Met de MsExchUserHoldPolcies-en cloudMsExchUserHoldPolicies-kenmerken kunnen lokale AD-en Azure AD-locaties bepalen welke gebruikers zich in een wacht ruimte bevinden, afhankelijk van of ze on-premises Exchange of Exchange Online gebruiken.

## <a name="msexchuserholdpolicies-synchronization-flow"></a>msExchUserHoldPolicies-synchronisatie stroom
Standaard worden MsExchUserHoldPolcies gesynchroniseerd door Azure AD Connect rechtstreeks naar het kenmerk msExchUserHoldPolicies in de tekst en vervolgens naar het kenmerk msExchUserHoldPolicies in azure AD

In de volgende tabellen wordt de stroom beschreven:

Inkomend van on-premises Active Directory:

|Active Directory kenmerk|Kenmerknaam|Stroom type|Omgekeerd kenmerk|Synchronisatie regel|
|-----|-----|-----|-----|-----|
|On-premises Active Directory|msExchUserHoldPolicies|Direct|msExchUserHoldPolicies|In van de uitwisseling van AD-gebruikers|

Uitgaand naar Azure AD:

|Omgekeerd kenmerk|Kenmerknaam|Stroom type|Azure AD-kenmerk|Synchronisatie regel|
|-----|-----|-----|-----|-----|
|Azure Active Directory|msExchUserHoldPolicies|Direct|msExchUserHoldPolicies|Naar AAD – UserExchangeOnline|

## <a name="cloudmsexchuserholdpolicies-synchronization-flow"></a>cloudMsExchUserHoldPolicies-synchronisatie stroom
Standaard worden cloudMsExchUserHoldPolicies gesynchroniseerd door Azure AD Connect rechtstreeks naar het kenmerk cloudMsExchUserHoldPolicies in de tekst. Als msExchUserHoldPolicies niet null is in de tekst, wordt het kenmerk gestroomd naar Active Directory.

In de volgende tabellen wordt de stroom beschreven:

Binnenkomend vanuit Azure AD:

|Active Directory kenmerk|Kenmerknaam|Stroom type|Omgekeerd kenmerk|Synchronisatie regel|
|-----|-----|-----|-----|-----|
|On-premises Active Directory|cloudMsExchUserHoldPolicies|Direct|cloudMsExchUserHoldPolicies|In van AAD-gebruikers uitwisseling|

Uitgaand naar on-premises Active Directory:

|Omgekeerd kenmerk|Kenmerknaam|Stroom type|Azure AD-kenmerk|Synchronisatie regel|
|-----|-----|-----|-----|-----|
|Azure Active Directory|cloudMsExchUserHoldPolicies|IF (NOT NULL)|msExchUserHoldPolicies|Naar AD – UserExchangeOnline|

## <a name="information-on-the-attribute-behavior"></a>Informatie over het kenmerk gedrag
De msExchangeUserHoldPolicies zijn één instantie kenmerk.  Een kenmerk met één instantie kan worden ingesteld voor een object (in dit geval gebruikers object) in de on-premises map of in de Cloud Directory.  Het begin van de Autoriteits regels bepaalt dat als het kenmerk vanuit on-premises is gesynchroniseerd, dit kenmerk niet door Azure AD kan worden bijgewerkt.

Het kenmerk cloudMSExchangeUserHoldPolicies wordt gebruikt om gebruikers toe te staan een blokkerings beleid in te stellen voor een gebruikers object in de Cloud. Dit kenmerk wordt gebruikt omdat Azure AD msExchangeUserHoldPolicies niet rechtstreeks kan instellen op basis van de hierboven beschreven regels.  Dit kenmerk wordt vervolgens opnieuw gesynchroniseerd naar de on-premises Directory als de msExchangeUserHoldPolicies niet null is en de huidige waarde van msExchangeUserHoldPolicies vervangen.

Onder bepaalde omstandigheden, bijvoorbeeld als beide on-premises en in azure op hetzelfde moment zijn gewijzigd, kan dit een aantal problemen veroorzaken.  

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
