---
title: MFA op telefoon beveiligen in Azure AD B2C
titleSuffix: Azure AD B2C
description: Tips voor het beveiligen van op telefoon gebaseerde multi-factor Authentication (MFA) in uw Azure AD B2C-Tenant met behulp van Azure Monitor Log Analytics-rapporten en-waarschuwingen. Gebruik onze werkmap om frauduleuze telefonische authenticaties te identificeren en frauduleuze aanmeld pogingen te verhelpen. =
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 02/01/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cc9e0be90c138ba33e1b4dfe11ea6f9c8b7da297
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033551"
---
# <a name="securing-phone-based-multi-factor-authentication-mfa"></a>Multi-factor Authentication (MFA) op basis van de telefoon beveiligen

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Met Azure Active Directory (Azure AD) Multi-Factor Authentication (MFA) kunnen gebruikers ervoor kiezen om een automatische spraak oproep te ontvangen op een telefoon nummer dat ze voor verificatie registreren. Kwaadwillende gebruikers kunnen profiteren van deze methode door meerdere accounts te maken en telefoon gesprekken te voeren zonder het MFA-registratie proces te volt ooien. Met deze talloze mislukte aanmeldingen kunnen de toegestane aanmeldings pogingen worden uitgeput, waardoor andere gebruikers zich niet kunnen aanmelden voor nieuwe accounts in uw Azure AD B2C-Tenant. Om u te helpen bij het beveiligen van deze aanvallen, kunt u Azure Monitor gebruiken om problemen met de telefoon verificatie te controleren en frauduleuze aanmeld pogingen te verhelpen.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u een [log Analytics-werk ruimte](azure-monitor.md)maken.

## <a name="create-a-phone-based-mfa-events-workbook"></a>Een op een telefoon gebaseerde MFA-gebeurtenissen werkmap maken

De [Azure AD B2C-rapporten &](https://github.com/azure-ad-b2c/siem#phone-authentication-failures) de opslag plaats voor waarschuwingen in github bevatten artefacten die u kunt gebruiken voor het maken en publiceren van rapporten, waarschuwingen en dash boards op basis van Azure AD B2C Logboeken. In de concept werkmap die hieronder wordt weer gegeven, worden telefoon fouten gemarkeerd.

### <a name="overview-tab"></a>Tabblad Overzicht

De volgende informatie wordt weer gegeven op het tabblad **overzicht** :

- Fout oorzaken (het totale aantal mislukte telefoon authenticaties voor elke opgegeven reden)
- Geblokkeerd vanwege een onjuiste reputatie
- IP-adres met mislukte telefoon authenticaties (het totale aantal mislukte telefoon authenticaties voor elk opgegeven IP-adres)
- Telefoon nummers met IP-adres-mislukte telefoon authenticaties
- Browser (mislukte telefoon verificaties per client browser)
- Besturings systeem (mislukte telefoon verificaties per client besturingssysteem)

![Tabblad Overzicht](media/phone-based-mfa/overview-tab.png)

### <a name="details-tab"></a>Het tabblad Details

De volgende informatie wordt op het tabblad **Details** gerapporteerd:

- Azure AD B2C beleid-mislukte telefoon authenticaties
- Problemen met telefoon verificatie per telefoon nummer-tijd diagram (aanpas bare tijd lijn)
- Problemen met de telefoon verificatie per Azure AD B2C beleid-tijd diagram (aanpas bare tijd lijn)
- Problemen met de telefoon verificatie per IP-adres-tijd diagram (aanpas bare tijd lijn)
- Telefoon nummer selecteren om fout details weer te geven (Selecteer een telefoon nummer voor een gedetailleerde lijst met fouten)

![Tabblad Details 1 van 3](media/phone-based-mfa/details-tab-1.png)

![Tabblad Details 2 van 3](media/phone-based-mfa/details-tab-2.png)

![Tabblad Details 3 van 3](media/phone-based-mfa/details-tab-3.png)

## <a name="use-the-workbook-to-identify-fraudulent-sign-ups"></a>De werkmap gebruiken om frauduleuze aanmeld pogingen te identificeren

U kunt de werkmap gebruiken om MFA-gebeurtenissen op basis van telefoons te begrijpen en mogelijk kwaad aardig gebruik van de Telephony-service te identificeren.

1. Krijg inzicht in wat er normaal is voor uw Tenant door deze vragen te beantwoorden:

   - Waar bevinden de regio's waar u op de telefoon gebaseerde MFA wordt verwacht?
   - Bekijk de redenen die worden weer gegeven voor mislukte telefonische MFA-pogingen. worden ze normaal gezien of verwacht?

2. De kenmerken van frauduleuze registratie herkennen:

   - **Op locatie gebaseerd**: controleren op problemen met de **telefoon verificatie op IP-adres** voor accounts die zijn gekoppeld aan locaties waarvan u niet verwacht dat gebruikers zich kunnen registreren.

   > [!NOTE]
   > Het gegeven IP-adres is een geschatte regio.

   - **Op basis van snelheid**: kijken naar **mislukte telefoon-authenticaties (per dag)**, waarmee telefoon nummers worden aangegeven die een abnormaal aantal mislukte aanvragen voor telefonische authenticatie per dag door lopen, gesorteerd van hoogst (links) naar laag (rechts).

3. Beperk frauduleuze aanmeldings pogingen door de stappen in de volgende sectie te volgen.
 

## <a name="mitigate-fraudulent-sign-ups"></a>Frauduleuze aanmeldings pogingen beperken

Voer de volgende acties uit om frauduleuze aanmeld pogingen te helpen voor komen.

- Gebruik de **Aanbevolen** versies van gebruikers stromen om het volgende te doen:
     
   - [De eenmalige e-mail wachtwoord functie (OTP)](phone-authentication-user-flows.md) voor MFA inschakelen (is van toepassing op zowel registratie-als aanmeldings stromen).
   - [Een beleid voor voorwaardelijke toegang configureren](conditional-access-user-flow.md) om aanmeldingen te blok keren op basis van locatie (alleen van toepassing op aanmeldingen, geen stromen voor aanmelden).
   - API-connectors gebruiken om te [integreren met een anti-bot-oplossing zoals reCAPTCHA](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-captcha) (van toepassing op registratie stromen).

- Verwijder land codes die niet relevant zijn voor uw organisatie in de vervolg keuzelijst waar de gebruiker hun telefoon nummer verifieert (deze wijziging is van toepassing op toekomstige aanmeldingen):
    
   1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com).

   2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat door in het bovenste menu te klikken op het filter **Map en abonnement** en de map te kiezen waarin de tenant zich bevindt.

   3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.

   4. Selecteer de gebruikers stroom en selecteer vervolgens **talen**. Selecteer de taal voor de geografische locatie van uw organisatie om het paneel taal details te openen. (In dit voor beeld selecteren we **Engels en** voor de Verenigde Staten). Selecteer **multi-factor Authentication pagina** en selecteer vervolgens **standaard waarden downloaden (en)**.
 
      ![Nieuwe onderdrukkingen uploaden om standaard waarden te downloaden](media/phone-based-mfa/download-defaults.png)

   5. Open het JSON-bestand dat in de vorige stap is gedownload. Zoek in het bestand naar `DEFAULT` de regel en vervang deze door `"Value": "{\"DEFAULT\":\"Country/Region\",\"US\":\"United States\"}"` . Zorg ervoor dat u instelt `Overrides` op `true` .

   > [!NOTE]
   > U kunt de lijst met toegestane land codes in het `countryList` element aanpassen (Zie het [pagina-voor beeld van de telefoon Factor Authentication](localization-string-ids.md#phone-factor-authentication-page-example)).

   7. Sla het JSON-bestand op. Selecteer in het paneel taal Details onder **nieuwe onderdrukkingen uploaden** het gewijzigde JSON-bestand om dit te uploaden.

   8. Sluit het deel venster en selecteer **gebruikers stroom uitvoeren**. Voor dit voor beeld bevestigt u dat **Verenigde Staten** de enige beschik bare land code in de vervolg keuzelijst is:
 
      ![Vervolg keuzelijst land code](media/phone-based-mfa/country-code-drop-down.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [identiteits beveiliging en voorwaardelijke toegang voor Azure AD B2C](conditional-access-identity-protection-overview.md) 

- [Voorwaardelijke toegang Toep assen op gebruikers stromen in azure Active Directory B2C](conditional-access-user-flow.md)