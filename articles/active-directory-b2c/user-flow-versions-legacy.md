---
title: Oudere versies van de gebruikers stroom in Azure Active Directory B2C | Microsoft Docs
description: Meer informatie over oudere versies van gebruikers stromen die beschikbaar zijn in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1fbe93c93b5ede2c6b031dab53a1450da473f802
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102517800"
---
# <a name="legacy-user-flow-versions-in-azure-active-directory-b2c"></a>Oudere versies van de gebruikers stroom in Azure Active Directory B2C

> [!IMPORTANT]
> In dit artikel wordt de verouderde versie methode beschreven voor gebruikers stromen, die V1 (voor productie kant-en-klare) versies en V 1.1-en v2-versies van gebruikers stromen biedt. In andere omgevingen dan de open bare Azure-Cloud blijft deze verouderde versie methode gebruiken. In de open bare Azure-Cloud wordt deze methode vervangen door [nieuwe **Aanbevolen** en **Preview** -versies](user-flow-versions.md).
> 
Gebruikers stromen in Azure Active Directory B2C (Azure AD B2C) helpen u bij het instellen van een gemeen schappelijke [beleids](user-flow-overview.md) regel voor het beschrijven van de ervaring van de klant identiteit. Deze ervaring omvat het aanmelden, aanmelden, het opnieuw instellen van wacht woorden of het bewerken van profielen.

In de onderstaande tabel, tenzij een gebruikers stroom wordt aangeduid als **Aanbevolen**, wordt het beschouwd als een *Preview-versie*. Gebruik alleen aanbevolen gebruikers stromen voor uw productie toepassingen.

## <a name="v1"></a>V1

| Gebruikersstroom | Aanbevolen | Description |
| --------- | ----------- | ----------- |
| Wachtwoord opnieuw instellen | Yes | Hiermee kan een gebruiker een nieuw wacht woord kiezen na het verifiëren van het e-mail adres. Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Meervoudige verificatie](multi-factor-authentication.md)</li><li>Compatibiliteits instellingen voor tokens</li><li>[Vereisten voor wachtwoord complexiteit](password-complexity.md)</li></ul> |
| Profiel bewerken | Yes | Hiermee kan een gebruiker hun gebruikers kenmerken configureren. Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Levens duur van token](tokens-overview.md)</li><li>Compatibiliteits instellingen voor tokens</li><li>Gedrag van sessie</li></ul> |
| Aanmelden met ROPC | No | Hiermee kan een gebruiker met een lokaal account zich rechtstreeks aanmelden bij systeem eigen toepassingen (er is geen browser vereist). Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Levens duur van token](tokens-overview.md)</li><li>Compatibiliteits instellingen voor tokens</li></ul> |
| Aanmelden | No | Hiermee kan een gebruiker zich aanmelden bij hun account. Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Meervoudige verificatie](multi-factor-authentication.md)</li><li>[Levens duur van token](tokens-overview.md)</li><li>Compatibiliteits instellingen voor tokens</li><li>Gedrag van sessie</li><li>Aanmelden blok keren</li><li>Wacht woord opnieuw instellen afdwingen</li><li>Aangemeld blijven (KMSI)</ul><br>U kunt de gebruikers interface niet aanpassen met deze gebruikers stroom. |
| Aanmelden | No | Hiermee kan een gebruiker een account maken. Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Meervoudige verificatie](multi-factor-authentication.md)</li><li>[Levens duur van token](tokens-overview.md)</li><li>Compatibiliteits instellingen voor tokens</li><li>Gedrag van sessie</li><li>[Vereisten voor wachtwoord complexiteit](password-complexity.md)</li></ul> |
| Registreren en aanmelden | Yes | Hiermee kan een gebruiker een account maken of zich aanmelden bij hun account. Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Meervoudige verificatie](multi-factor-authentication.md)</li><li>[Levens duur van token](tokens-overview.md)</li><li>Compatibiliteits instellingen voor tokens</li><li>Gedrag van sessie</li><li>[Vereisten voor wachtwoord complexiteit](password-complexity.md)</li></ul>|

## <a name="v11"></a>V 1.1

| Gebruikersstroom | Aanbevolen | Description |
| --------- | ----------- | ----------- |
| Wacht woord opnieuw instellen v 1.1 | No | Hiermee kan een gebruiker een nieuw wacht woord kiezen na het verifiëren van het e-mail adres (nieuwe pagina-indeling beschikbaar). Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Meervoudige verificatie](multi-factor-authentication.md)</li><li>Compatibiliteits instellingen voor tokens</li><li>[Vereisten voor wachtwoord complexiteit](password-complexity.md)</li></ul> |

## <a name="v2"></a>V2

| Gebruikersstroom | Aanbevolen | Description |
| --------- | ----------- | ----------- |
| Wacht woord opnieuw instellen v2 | No | Hiermee kan een gebruiker een nieuw wacht woord kiezen na het verifiëren van het e-mail adres. Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Meervoudige verificatie](multi-factor-authentication.md)</li><li>Compatibiliteits instellingen voor tokens</li><li>[Leeftijdsbeperking](age-gating.md)</li><li>[vereisten voor wachtwoord complexiteit](password-complexity.md)</li></ul> |
| Profiel bewerken v2 | Yes | Hiermee kan een gebruiker hun gebruikers kenmerken configureren. Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Levens duur van token](tokens-overview.md)</li><li>Compatibiliteits instellingen voor tokens</li><li>Gedrag van sessie</li></ul> |
| Aanmelden v2 | No | Hiermee kan een gebruiker zich aanmelden bij hun account. Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Meervoudige verificatie](multi-factor-authentication.md)</li><li>[Levens duur van token](tokens-overview.md)</li><li>Compatibiliteits instellingen voor tokens</li><li>Gedrag van sessie</li><li>[Leeftijdsbeperking](age-gating.md)</li><li>Aanpassing van de aanmeldings pagina</li></ul> |
| Registreren v2 | No | Hiermee kan een gebruiker een account maken. Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Meervoudige verificatie](multi-factor-authentication.md)</li><li>[Levens duur van token](tokens-overview.md)</li><li>Compatibiliteits instellingen voor tokens</li><li>Gedrag van sessie</li><li>[Leeftijdsbeperking](age-gating.md)</li><li>[Vereisten voor wachtwoord complexiteit](password-complexity.md)</li></ul> |
| Registreren en aanmelden v2 | No | Hiermee kan een gebruiker een account maken of zich aanmelden bij hun account. Met deze gebruikers stroom kunt u het volgende configureren: <ul><li>[Meervoudige verificatie](multi-factor-authentication.md)</li><li>[Leeftijdsbeperking](age-gating.md)</li><li>[Vereisten voor wachtwoord complexiteit](password-complexity.md)</li></ul> |