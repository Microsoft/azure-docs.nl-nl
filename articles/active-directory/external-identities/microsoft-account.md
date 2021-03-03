---
title: Micro soft-account (MSA) ID-provider in azure AD
description: Gebruik Azure AD om een externe gebruiker (gast) in te scha kelen om u aan te melden bij uw Azure AD-apps met hun Microsoft-account (MSA).
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25d3380277d0e653fd5cb03069b7d91cb363dd95
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693060"
---
# <a name="microsoft-account-msa-identity-provider-for-external-identities-preview"></a>Micro soft-account (MSA)-ID-provider voor externe identiteiten (preview-versie)

> [!NOTE]
> De micro soft-account-ID-provider is een open bare preview-functie van Azure Active Directory. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Uw B2B-gast gebruikers kunnen hun eigen persoonlijke micro soft-accounts gebruiken voor B2B-samen werking zonder verdere configuratie. Gast gebruikers kunnen de uitnodigingen van uw B2B-samen werking inwisselen of uw gebruikers stromen voor registratie volt ooien met hun persoonlijke Microsoft-account.

Micro soft-accounts worden ingesteld door een gebruiker om toegang te krijgen tot op consumenten gebaseerde micro soft-producten en-Cloud Services, zoals Outlook, OneDrive, Xbox LIVE of Microsoft 365. Het account wordt gemaakt en opgeslagen in het micro soft-account systeem voor consumenten identiteiten dat wordt uitgevoerd door micro soft.

## <a name="guest-sign-in-using-microsoft-accounts"></a>Aanmelden met een gast met micro soft-accounts

Micro soft-account is standaard beschikbaar in de lijst met id-providers voor externe identiteiten. Er is geen verdere configuratie nodig om gast gebruikers toe te staan zich aan te melden met hun Microsoft-account met behulp van de uitnodigings stroom of een self-service voor het aanmelden van een gebruiker.

![Micro soft-account in de lijst met id-providers](media/microsoft-account/microsoft-account-identity-provider.png)

### <a name="microsoft-account-in-the-invitation-flow"></a>Micro soft-account in de uitnodigings stroom

Wanneer u [een gast gebruiker uitnodigt](add-users-administrator.md) voor B2B-samen werking, kunt u hun Microsoft-account opgeven als het e-mail adres waarmee u zich aanmeldt.

![Uitnodigen met behulp van een Microsoft-account](media/microsoft-account/microsoft-account-invite.png)

### <a name="microsoft-account-in-self-service-sign-up-user-flows"></a>Micro soft-account in Self-Service gebruikers stromen voor aanmeldingen

Micro soft-account is een id-provider optie voor uw Self-service registratie gebruikers stromen. Gebruikers kunnen zich aanmelden voor uw toepassingen met hun eigen micro soft-accounts. Eerst moet u de [selfservice registratie](self-service-sign-up-user-flow.md) voor uw Tenant inschakelen. Vervolgens kunt u een gebruikers stroom voor de toepassing instellen en micro soft-account selecteren als een van de aanmeldings opties.

![Microsoft-account in een gebruikers stroom voor selfservice registratie](media/microsoft-account/microsoft-account-user-flow.png)

## <a name="next-steps"></a>Volgende stappen

- [Azure Active Directory B2B-samenwerkings gebruikers toevoegen](add-users-administrator.md)
- [Een self-service aanmelding toevoegen aan een app](self-service-sign-up-user-flow.md)