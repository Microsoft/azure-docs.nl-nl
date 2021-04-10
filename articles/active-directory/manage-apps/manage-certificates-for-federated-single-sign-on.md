---
title: Federatie certificaten beheren in azure AD | Microsoft Docs
description: Meer informatie over het aanpassen van de verval datum voor uw Federatie certificaten en het vernieuwen van certificaten die binnenkort verlopen.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: kenwith
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63054b18589cdcc2d950d70452b4f982c324a6c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104603995"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Certificaten beheren voor federatieve eenmalige aanmelding in Azure Active Directory

In dit artikel Best rijken we algemene vragen en informatie met betrekking tot certificaten die Azure Active Directory (Azure AD) maakt om federatieve eenmalige aanmelding (SSO) tot stand te brengen met uw SaaS-toepassingen (Software as a Service). Toepassingen toevoegen vanuit de Azure AD-App-galerie of door gebruik te maken van een niet-galerie toepassings sjabloon. Configureer de toepassing met behulp van de optie Federated SSO.

Dit artikel is alleen relevant voor apps die zijn geconfigureerd voor het gebruik van Azure AD SSO via [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) Federatie.

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Automatisch gegenereerd certificaat voor galerie-en niet-galerie toepassingen

Wanneer u een nieuwe toepassing uit de galerie toevoegt en een op SAML gebaseerde aanmelding configureert (door de SAML van **eenmalige aanmelding** te selecteren op  >   de overzichts pagina van de toepassing), genereert Azure AD een certificaat voor de toepassing dat drie jaar geldig is. Als u het actieve certificaat wilt downloaden als een beveiligings certificaat bestand (**. CER**), gaat u terug naar die pagina (op **SAML gebaseerde aanmelding**) en selecteert u een download koppeling in de kop **SAML-handtekening certificaat** . U kunt kiezen tussen het onbewerkte (binaire) certificaat of het Base64-certificaat (basis 64-gecodeerd tekst). Voor galerie toepassingen kan deze sectie ook een koppeling weer geven om het certificaat te downloaden als XML voor federatieve meta gegevens (een **. XML** -bestand), afhankelijk van de vereiste van de toepassing.

![Download opties voor het SAML-actieve handtekening certificaat](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

U kunt ook een actief of inactief certificaat downloaden door het **bewerkings** pictogram van de kop van het **SAML-handtekening certificaat** te selecteren (een potlood), waarin de pagina **SAML-handtekening certificaat** wordt weer gegeven. Selecteer het beletsel teken (**...**) naast het certificaat dat u wilt downloaden en kies vervolgens de gewenste certificaat indeling. U hebt de extra optie voor het downloaden van het certificaat in de PEM-indeling (Privacy Enhanced mail). Deze indeling is identiek aan base64, maar met de bestandsnaam extensie **. pem** , die niet wordt herkend in Windows als een certificaat indeling.

![Download opties voor het SAML-handtekening certificaat (actief en inactief)](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>De verval datum voor uw Federatie certificaat aanpassen en naar een nieuw certificaat overschakelen

Standaard configureert Azure een certificaat dat na drie jaar verloopt wanneer het automatisch wordt gemaakt tijdens de configuratie van eenmalige SAML-aanmelding. Omdat u de datum van een certificaat niet kunt wijzigen nadat u het hebt opgeslagen, moet u het volgende doen:

1. Maak een nieuw certificaat met de gewenste datum.
1. Sla het nieuwe certificaat op.
1. Down load het nieuwe certificaat in de juiste indeling.
1. Upload het nieuwe certificaat naar de toepassing.
1. Maak het nieuwe certificaat actief in de Azure Active Directory Portal.

De volgende twee secties helpen u bij het uitvoeren van deze stappen.

### <a name="create-a-new-certificate"></a>Een nieuw certificaat maken

Maak eerst een nieuw certificaat met een andere verval datum en sla dit op:

1. Meld u aan bij de [Azure Active Directory Portal](https://aad.portal.azure.com/). De pagina **Azure Active Directory beheer centrum** wordt weer gegeven.
1. Selecteer in het linkerdeelvenster **Enterprise-toepassingen**. Er wordt een lijst weer gegeven met de bedrijfs toepassingen in uw account.
1. Selecteer de betreffende toepassing. Er wordt een overzichts pagina voor de toepassing weer gegeven.
1. Selecteer **eenmalige aanmelding** in het linkerdeel venster van de overzichts pagina van de toepassing.
1. Als de pagina **Eén aanmeldings methode selecteren** wordt weer gegeven, selecteert u **SAML**.
1. Zoek in de pagina **eén Sign-On met SAML-preview instellen** op de kop **SAML-handtekening certificaat** en selecteer het **bewerkings** pictogram (een potlood). De pagina **SAML-handtekening certificaat** wordt weer gegeven, waarin de status (**actief** of **inactief**), de verval datum en de vinger afdruk (een hash-teken reeks) van elk certificaat worden weer gegeven.
1. Selecteer **Nieuw certificaat**. Er wordt een nieuwe rij onder de lijst met certificaten weer gegeven, waarbij de verval datum standaard precies drie jaar na de huidige datum is. (Uw wijzigingen zijn nog niet opgeslagen, zodat u de verval datum nog steeds kunt wijzigen.)
1. Beweeg de muis aanwijzer over de kolom verval datum in de rij nieuw certificaat en selecteer vervolgens het pictogram **datum selecteren** (een kalender). Er wordt een kalender besturings element weer gegeven waarin de dagen van de maand van de huidige verval datum van de nieuwe rij worden weer gegeven.
1. Gebruik het besturings element kalender om een nieuwe datum in te stellen. U kunt elke datum tussen de huidige datum en drie jaar na de huidige datum instellen.
1. Selecteer **Opslaan**. Het nieuwe certificaat wordt nu weer gegeven met de status **inactief**, de verval datum die u hebt gekozen en een vinger afdruk.
1. Selecteer de **X** om terug te keren naar de pagina **Eén Sign-On met SAML-preview instellen** .

### <a name="upload-and-activate-a-certificate"></a>Een certificaat uploaden en activeren

Down load vervolgens het nieuwe certificaat in de juiste indeling, upload het naar de toepassing en maak het actief in Azure Active Directory:

1. Bekijk de aanvullende configuratie-instructies voor SAML-aanmeldingen met behulp van een van de volgende opties:

   - De koppeling van de **configuratie handleiding** selecteren die u wilt weer geven in een afzonderlijk browser venster of tabblad of
   - Ga naar de kop **instellen** en selecteer **Stapsgewijze instructies** om weer te geven in een zijbalk.

1. In de instructies ziet u de coderings indeling die is vereist voor het uploaden van het certificaat.
1. Volg de instructies in de sectie [automatisch gegenereerd certificaat voor galerie en niet-galerie toepassingen](#auto-generated-certificate-for-gallery-and-non-gallery-applications) eerder. Met deze stap wordt het certificaat gedownload in de coderings indeling die vereist is voor het uploaden door de toepassing.
1. Als u wilt overschakelen naar het nieuwe certificaat, gaat u terug naar de pagina **SAML-handtekening certificaat** en selecteert u in de rij nieuw opgeslagen certificaat het weglatings teken (**...**) en selecteert **u certificaat actief maken**. De status van het nieuwe certificaat verandert in **actief** en het vorige actieve certificaat verandert in de status **inactief**.
1. Ga door met het volgen van de configuratie-instructies voor SAML-aanmeldingen van de toepassing die u eerder hebt weer gegeven, zodat u het SAML-handtekening certificaat kunt uploaden naar de juiste indeling voor versleuteling.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>E-mail meldings adressen toevoegen voor het verlopen van certificaten

Azure AD verzendt een e-mail melding 60, 30 en 7 dagen voordat het SAML-certificaat is verlopen. U kunt meer dan één e-mail adres toevoegen om meldingen te ontvangen. De e-mail adressen opgeven waarnaar de meldingen moeten worden verzonden:

1. Ga op de pagina **SAML-handtekening certificaat** naar de koptekst voor het **e-mail adres** van de melding. Standaard gebruikt deze kop alleen het e-mail adres van de beheerder die de toepassing heeft toegevoegd.
1. Typ onder het laatste e-mail adres het e-mail adres dat het verloop bericht van het certificaat moet ontvangen en druk vervolgens op ENTER.
1. Herhaal de vorige stap voor elk e-mail adres dat u wilt toevoegen.
1. Voor elk e-mail adres dat u wilt verwijderen, selecteert u het **Verwijder** pictogram (een schoner) naast het e-mail adres.
1. Selecteer **Opslaan**.

U kunt Maxi maal 5 e-mail adressen toevoegen aan de lijst met meldingen (met inbegrip van het e-mail adres van de beheerder die de toepassing heeft toegevoegd). Als u meer mensen wilt laten weten dat ze een melding moeten ontvangen, gebruikt u de e-mail berichten distributie lijst.

U ontvangt de e-mail melding van aadnotification@microsoft.com . Als u wilt voor komen dat het e-mail bericht naar uw spam locatie gaat, voegt u deze e-mail toe aan uw contact personen.

## <a name="renew-a-certificate-that-will-soon-expire"></a>Een certificaat vernieuwen dat binnenkort verloopt

Als een certificaat bijna verloopt, kunt u het vernieuwen met behulp van een procedure die leidt tot een aanzienlijke downtime van uw gebruikers. Een verlooptd certificaat vernieuwen:

1. Volg de instructies in de sectie [een nieuw certificaat maken](#create-a-new-certificate) met een datum die overlapt met het bestaande certificaat. Met deze datum wordt de hoeveelheid downtime beperkt die wordt veroorzaakt door het verlopen van het certificaat.
1. Als de toepassing automatisch kan worden gekanteld via een certificaat, stelt u het nieuwe certificaat in op actief door de volgende stappen uit te voeren:
   1. Ga terug naar de pagina **SAML-handtekening certificaat** .
   1. Selecteer in de rij nieuw opgeslagen certificaat het weglatings teken (**...**) en selecteer vervolgens **certificaat maken actief**.
   1. De volgende twee stappen overs Laan.

1. Als de app slechts één certificaat tegelijk kan verwerken, kiest u een interval voor uitval tijd om de volgende stap uit te voeren. (Als de toepassing het nieuwe certificaat niet automatisch ophaalt, maar wel meer dan één handtekening certificaat kan verwerken, kunt u de volgende stap op elk gewenst moment uitvoeren.)
1. Voordat het oude certificaat verloopt, volgt u de instructies in de sectie [Upload en een certificaat activeren](#upload-and-activate-a-certificate) eerder.
1. Meld u aan bij de toepassing om er zeker van te zijn dat het certificaat correct werkt.

## <a name="related-articles"></a>Verwante artikelen:

- [Tutorials for integrating SaaS applications with Azure Active Directory](../saas-apps/tutorial-list.md) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)
- [Toepassingsbeheer met Azure Active Directory](what-is-application-management.md)
- [Eenmalige aanmelding bij toepassingen in Azure Active Directory](what-is-single-sign-on.md)
- [Foutopsporing uitvoeren in op SAML gebaseerde eenmalige aanmelding bij toepassingen in Azure Active Directory](./debug-saml-sso-issues.md)
