---
title: ISV-partner galerie voor Azure AD B2C
titleSuffix: Azure AD B2C
description: Leer hoe u kunt integreren met onze ISV-partners om uw eindgebruikers ervaring aan uw behoeften aan te passen. Ons partner netwerk breidt onze oplossings mogelijkheden uit; MFA inschakelen, beveiligde klant verificatie, op rollen gebaseerd toegangs beheer; bestrij ding van fraude via verificatie op basis van identiteiten.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/11/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a1ee632e3aaae7b858ab43b45f6e72aff8d1fb77
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100361758"
---
# <a name="azure-active-directory-b2c-isv-partners"></a>Azure Active Directory B2C ISV-partners

Het ISV-partner netwerk breidt onze oplossings mogelijkheden uit om u te helpen bij het bouwen van een naadloze ervaring voor de eind gebruiker. Met Azure AD B2C kunt u integreren met ISV-partners om multi-factor Authentication-methoden (MFA) in te scha kelen, Toegangs beheer op basis van rollen mogelijk te maken, identiteits verificatie in te scha kelen en te controleren, de beveiliging te verbeteren met bot-detectie en fraude bescherming, en te voldoen aan de vereisten voor de veilige klant verificatie (PSD2). Gebruik onze gedetailleerde voorbeeld scenario's voor meer informatie over het integreren van apps met de ISV-partners.

>[!NOTE]
>De [Azure Active Directory B2C community-site op github](https://azure-ad-b2c.github.io/azureadb2ccommunity.io/) biedt ook voor beelden van aangepaste beleids regels van de community.

## <a name="identity-verification-and-proofing"></a>Identiteits verificatie en controle

Met Azure AD B2C-partners kunnen klanten identiteits verificatie en controle van hun eind gebruikers inschakelen voordat ze account registratie of toegang toestaan. Identiteits verificatie en controle kunnen document, op kennis gebaseerde informatie en de Live-ervaring controleren.

In een architectuur diagram op hoog niveau wordt de stroom uitgelegd.

![Diagram toont de identiteits controle stroom](./media/partner-gallery/third-party-identity-proofing.png)

Micro soft-partners met de volgende Isv's voor identiteits verificatie en controle.

| ISV-partner | Beschrijving en integratie scenario's |
|:-------------------------|:--------------|
|![Scherm opname van een Experian-logo.](./media/partner-gallery/experian-logo.png) | [Experian](./partner-experian.md) is een identiteits controle-provider waarmee risico beoordelingen worden uitgevoerd op basis van gebruikers kenmerken om fraude te voor komen. |
|![Scherm opname van een IDology-logo.](./media/partner-gallery/idology-logo.png) | [IDology](./partner-idology.md) is een identiteits verificatie en controle provider met id-verificatie oplossingen, fraude preventie oplossingen, compatibiliteits oplossingen en anderen.|
|![Scherm opname van een Jumio-logo.](./media/partner-gallery/jumio-logo.png) | [Jumio](./partner-jumio.md) is een id-verificatie service waarmee realtime-verificatie via een geautomatiseerde id wordt ingeschakeld, waarbij klant gegevens worden beschermd. |
| ![Scherm opname van een LexisNexis-logo.](./media/partner-gallery/lexisnexis-logo.png) | [LexisNexis](./partner-lexisnexis.md) is een profilerings-en identiteits validatie provider die gebruikers identificatie verifieert en uitgebreide risico analyse biedt op basis van het apparaat van de gebruiker. |
| ![Scherm afbeelding van een Onfido-logo](./media/partner-gallery/onfido-logo.png) | [Onfido](./partner-onfido.md) is een verificatie oplossing met een document-id en gelaats biometrie waarmee bedrijven voldoen aan de vereisten van *uw klant* en identiteit in real-time.  |

## <a name="mfa-and-passwordless-authentication"></a>MFA en verificatie met een wacht woord

Micro soft-partners met de volgende Isv's voor MFA en verificatie zonder wacht woord.

| ISV-partner | Beschrijving en integratie scenario's |
|:-------------------------|:--------------|
| ![Scherm afbeelding van een hypr-logo](./media/partner-gallery/hypr-logo.png) | [Hypr](./partner-hypr.md) is een verificatie provider zonder wacht woord, waarmee wacht woorden met een open bare sleutel versleuteling worden vervangen door fraude, phishing en hergebruik van referenties. |
| ![Scherm afbeelding van een Itsme-logo](./media/partner-gallery/itsme-logo.png) | [Itsme](./partner-itsme.md) is een digitale id-oplossing voor identificatie, authenticatie en Trust Services (eiDAS) waarmee gebruikers veilig kunnen worden aangemeld zonder kaart lezers, wacht woorden, twee ledige verificatie en meerdere pincodes. |
|![Scherm afbeelding van een logo met een toets zonder toets.](./media/partner-gallery/keyless-logo.png) | [Minder](./partner-keyless.md) dan een wacht woordloze verificatie provider die verificatie biedt in de vorm van een biometrische scan van het gezichts signaal en elimineert fraude, phishing en hergebruik van referenties.
| ![Scherm afbeelding van een Nevis-logo](./media/partner-gallery/nevis-logo.png) | [Nevis](./partner-nevis.md) maakt verificatie zonder wacht woord mogelijk en biedt een volledige, volledig brandende eindgebruikers ervaring met Nevis Access-app voor sterke klant verificatie en om te voldoen aan de PSD2-transactie vereisten. |
| ![Scherm afbeelding van een trusona-logo](./media/partner-gallery/trusona-logo.png) | [Trusona](./partner-trusona.md) -integratie helpt u veilig te registreren en maakt verificatie zonder wacht woord, MFA en digitale licentie controle mogelijk. |
| ![Scherm opname van een twilio-logo.](./media/partner-gallery/twilio-logo.png) | [Twilio verify app](./partner-twilio.md) biedt meerdere oplossingen om MFA in te scha kelen via EENMALIGe SMS-wacht woord (OTP), op tijd gebaseerd wacht woord (mobiele totp) en push meldingen, en om te voldoen aan de vereisten voor de SCA voor PSD2. |
| ![Scherm afbeelding van een typingDNA-logo](./media/partner-gallery/typingdna-logo.png) | [TypingDNA](./partner-typingdna.md) maakt sterke klanten verificatie mogelijk door het type patroon van een gebruiker te analyseren. Het helpt bedrijven een stille MFA in te scha kelen en te voldoen aan de SCA-vereisten voor PSD2. |
| ![Scherm afbeelding van een whoiam-logo](./media/partner-gallery/whoiam-logo.png) | [WhoIAM](./partner-whoiam.md) is een BRIMS-toepassing (merk bare identiteits beheersysteem) waarmee organisaties hun gebruikers basis kunnen verifiëren door middel van spraak, SMS en e-mail. |

## <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer 
 
Micro soft-partners met de volgende Isv's voor op rollen gebaseerd toegangs beheer.

| ISV-partner | Beschrijving en integratie scenario's |
|:-------------------------|:--------------|
| ![Scherm afbeelding van een n8identity-logo](./media/partner-gallery/n8identity-logo.png) | [N8Identity](./partner-n8identity.md) is een platform voor identiteits-as-a-service dat oplossing biedt voor het oplossen van de migratie van klant accounts en klantenservice aanvragen (CSR) die worden uitgevoerd op Microsoft Azure. |
| ![Scherm afbeelding van een Saviynt-logo](./media/partner-gallery/saviynt-logo.png) | [Saviynt](./partner-Saviynt.md) Cloud-systeem eigen platform bevordert betere beveiliging, naleving en beheer door intelligente analyses en integratie van meerdere toepassingen voor het stroom lijnen van IT-modernisatie. |

## <a name="security"></a>Beveiliging

Micro soft-partners met de volgende Isv's voor beveiliging.

| ISV-partner | Beschrijving en integratie scenario's |
|:-------------------------|:--------------|
| ![Scherm afbeelding van een arkose Lab-logo](./media/partner-gallery/arkose-logo.png) | [Arkose Labs](./partner-arkose-labs.md) is een oplossings provider voor het voor komen van fraude die organisaties helpt bij het beveiligen tegen bot-aanvallen, inbreuk op account overname en frauduleuze account openingen. |
| ![Scherm afbeelding van een micro soft Dynamics 365-logo](./media/partner-gallery/microsoft-dynamics365-logo.png) | [Micro soft Dynamics 365 fraude bescherming](./partner-dynamics-365-fraud-protection.md) is een oplossing die organisaties helpt te beschermen tegen frauduleuze openingen van accounts door middel van vinger afdrukken van apparaten. |
| ![Scherm afbeelding van een ping-logo](./media/partner-gallery/ping-logo.png) | Met [ping-identiteit](./partner-ping-identity.md) wordt beveiligde hybride toegang tot on-premises oudere toepassingen in meerdere clouds mogelijk. |
| ![Scherm afbeelding van een Strata-logo](./media/partner-gallery/strata-logo.png) | [Strata](./partner-strata.md) biedt beveiligde hybride toegang tot on-premises toepassingen door het afdwingen van consistente toegangs beleid, het behoud van identiteiten en het eenvoudig maken van toepassingen van oudere identiteits systemen naar op standaarden gebaseerde verificatie en toegangs beheer van Azure AD B2C. |
| ![Scherm afbeelding van een zscaler-logo](./media/partner-gallery/zscaler-logo.png) | [Zscaler](./partner-zscaler.md) levert op beleid gebaseerde, beveiligde toegang tot persoonlijke toepassingen en assets zonder de kosten, gedoe of beveiligings Risico's van een VPN. |

## <a name="additional-information"></a>Aanvullende informatie

- [Aangepast beleid in Azure AD B2C](./custom-policy-overview.md)

- [Aan de slag met aangepast beleid in Azure AD B2C](./custom-policy-get-started.md?tabs=applications)

## <a name="next-steps"></a>Volgende stappen

Selecteer een partner in de tabellen die worden vermeld voor meer informatie over het integreren van de oplossing met Azure AD B2C.
