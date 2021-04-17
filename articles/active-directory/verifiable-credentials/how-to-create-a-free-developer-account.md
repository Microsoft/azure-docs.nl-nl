---
title: Een gratis ontwikkelaars-tenant Azure Active Directory maken
description: In dit artikel wordt beschreven hoe u een ontwikkelaarsaccount maakt
services: active-directory
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: how-to
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: a50c8b083c1cd453dbe3c51c63ec9cf53859c3bd
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587262"
---
# <a name="how-to-create-a-free-azure-active-directory-developer-tenant"></a>Een gratis ontwikkelaars-tenant Azure Active Directory maken

> [!IMPORTANT]
> Azure Active Directory Verifiable Credentials is momenteel beschikbaar als openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

> [!NOTE]
> In de preview-versie is een P2-licentie vereist. 

Er zijn twee eenvoudige manieren om een gratis Azure Active Directory te maken met een P2-proeflicentie, zodat u de verifieerbare referentieverlener-service kunt installeren en het maken en valideren van verifieerbare referenties kunt testen:

- [Doe](https://aka.ms/o365devprogram) mee aan Microsoft 365 Developer Program en krijg een gratis sandbox, hulpprogramma's en andere resources, zoals een Azure Active Directory met P2-licenties. Geconfigureerde gebruikers, groepen, postvakken, enzovoort.
- Maak een nieuwe [tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) en activeer een [gratis proefversie](https://azure.microsoft.com/trial/get-started-active-directory/) van Azure AD Premium P1 of P2 in uw nieuwe tenant.

Als u zich wilt registreren voor het gratis Microsoft 365 ontwikkelaarsprogramma, moet u een paar eenvoudige stappen volgen:

1. Klik op de **knop Nu toevoegen** op het scherm.

2. Meld u aan met een nieuw Microsoft-account of gebruik een bestaand (werk)account dat u al hebt.

3. Op de aanmeldingspagina selecteert u uw regio, voert u een bedrijfsnaam in en accepteert u de voorwaarden van het programma voordat u op **Volgende klikt.**

4. Klik op **Abonnement instellen.** Geef de regio op waar u uw nieuwe tenant wilt maken, maak een gebruikersnaam en domein en voer een wachtwoord in. Hiermee maakt u een nieuwe tenant en de eerste beheerder van de tenant.

5. Voer de beveiligingsgegevens in die nodig zijn om het beheerdersaccount van uw nieuwe tenant te beveiligen. Hiermee wordt MFA-verificatie voor het account ingesteld.


U hebt nu een tenant gemaakt met 25 E5-gebruikerslicenties. De E5-licenties bevatten Azure AD P2-licenties. U kunt eventueel voorbeeldgegevenspakketten toevoegen met gebruikers, groepen, e-mail en SharePoint om u te helpen testen in uw ontwikkelomgeving. Voor de service Verifiable Credential Issuing zijn ze niet vereist.

Voor uw gemak kunt u uw eigen werkaccount als gast toevoegen [aan](/azure/active-directory/external-identities/b2b-quickstart-add-guest-users-portal) de zojuist gemaakte tenant en dat account gebruiken om de tenant te beheren. Als u wilt dat het gastaccount de verifieerbare referentieservice kan beheren, moet u de rol 'Globale beheerder' toewijzen aan die gebruiker.

## <a name="next-steps"></a>Volgende stappen

Nu u een ontwikkelaarsaccount hebt, kunt u onze eerste [zelfstudie proberen](get-started-verifiable-credentials.md) voor meer informatie over verifieerbare referenties.
