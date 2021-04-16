---
title: Federatiecertificaten beheren in Azure AD | Microsoft Docs
description: Meer informatie over het aanpassen van de vervaldatum voor uw federatiecertificaten en het vernieuwen van certificaten die binnenkort verlopen.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: iangithinji
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 594af54221dd242bd481fe3d2fc823f628c1f955
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379448"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Certificaten beheren voor federatief aanmelden met Azure Active Directory

In dit artikel behandelen we veelvoorkomende vragen en informatie met betrekking tot certificaten die door Azure Active Directory (Azure AD) worden gemaakt om federatief eenmalige aanmelding (SSO) tot stand te brengen bij uw SaaS-toepassingen (Software as a Service). Toepassingen toevoegen vanuit de Azure AD-app-galerie of met behulp van een toepassingssjabloon buiten de galerie. Configureer de toepassing met behulp van de optie federatief eenmalige aanmelding.

Dit artikel is alleen relevant voor apps die zijn geconfigureerd voor het gebruik van Eenmalige aanmelding van Azure AD [via Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML)-federatie.

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Automatisch gegenereerd certificaat voor galerie- en niet-galerietoepassingen

Wanneer u een nieuwe toepassing uit de galerie toevoegt en een op SAML gebaseerde aanmelding configureert (door SAML voor een een aanmelding te selecteren op de overzichtspagina van de toepassing), genereert Azure AD een certificaat voor de toepassing dat drie jaar geldig  >   is. Als u het actieve certificaat wilt downloaden als een **.cer-bestand**(beveiligingscertificaat), gaat u terug naar die pagina (aanmelding op basis van **SAML)** en selecteert u een downloadkoppeling in de kop **SAML-handtekeningcertificaat.** U kunt kiezen tussen het onbewerkte (binaire) certificaat of het Base64-certificaat (base 64-gecodeerde tekst). Voor galerietoepassingen kan in deze sectie ook een koppeling worden  weergegeven om het certificaat te downloaden als XML-bestand met federatiemetagegevens (een XML-bestand), afhankelijk van de vereiste van de toepassing.

![Downloadopties voor actief SAML-handtekeningcertificaat](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

U kunt ook een actief of inactief certificaat downloaden door  het pictogram Bewerken (een potlood) van het **SAML-handtekeningcertificaat** te selecteren. Hiermee wordt de **pagina SAML-handtekeningcertificaat** weergegeven. Selecteer het beletselteken (**...**) naast het certificaat dat u wilt downloaden en kies vervolgens welke certificaatindeling u wilt. U hebt de extra optie om het certificaat in PEM-indeling (Privacy Enhanced Mail) te downloaden. Deze indeling is identiek aan Base64, maar met de bestandsnaamextensie **.pem,** die in Windows niet wordt herkend als een certificaatindeling.

![Downloadopties voor SAML-handtekeningcertificaat (actief en inactief)](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Pas de vervaldatum voor uw federatiecertificaat aan en rol deze over naar een nieuw certificaat

Standaard configureert Azure een certificaat dat na drie jaar verloopt wanneer het automatisch wordt gemaakt tijdens de configuratie van een saml-aanmelding. Omdat u de datum van een certificaat niet kunt wijzigen nadat u het hebt op slaan, moet u het volgende doen:

1. Maak een nieuw certificaat met de gewenste datum.
1. Sla het nieuwe certificaat op.
1. Download het nieuwe certificaat in de juiste indeling.
1. Upload het nieuwe certificaat naar de toepassing.
1. Maak het nieuwe certificaat actief in de Azure Active Directory portal.

De volgende twee secties helpen u bij het uitvoeren van deze stappen.

### <a name="create-a-new-certificate"></a>Een nieuw certificaat maken

Maak eerst een nieuw certificaat met een andere vervaldatum en sla het op:

1. Meld u aan bij de [Azure Active Directory Portal](https://aad.portal.azure.com/). De **Azure Active Directory beheercentrumpagina** wordt weergegeven.
1. Selecteer in het linkerdeelvenster **Enterprise-toepassingen**. Er wordt een lijst met bedrijfstoepassingen in uw account weergegeven.
1. Selecteer de betreffende toepassing. Er wordt een overzichtspagina voor de toepassing weergegeven.
1. Selecteer In het linkerdeelvenster van de overzichtspagina van de toepassing **de optie Een aanmelding.**
1. Als de **pagina Select a single sign-on method** wordt weergegeven, selecteert u **SAML**.
1. Zoek op **de pagina Single Sign-On met SAML - Preview** de kop **SAML-handtekeningcertificaat** en selecteer **het** pictogram Bewerken (een potlood). De **pagina SAML-handtekeningcertificaat** wordt weergegeven, met de status (**Actief** of **Inactief),** vervaldatum en vingerafdruk (een hash-tekenreeks) van elk certificaat.
1. Selecteer **Nieuw certificaat.** Er wordt een nieuwe rij weergegeven onder de certificaatlijst, waarbij de vervaldatum exact drie jaar na de huidige datum is. (Uw wijzigingen zijn nog niet opgeslagen, dus u kunt de vervaldatum nog steeds wijzigen.)
1. Beweeg in de nieuwe certificaatrij de muisaanwijzer over de kolom vervaldatum en selecteer het **pictogram Datum selecteren** (een kalender). Er wordt een kalenderbesturingselement weergegeven met de dagen van een maand van de huidige vervaldatum van de nieuwe rij.
1. Gebruik het kalenderbesturingselement om een nieuwe datum in te stellen. U kunt een datum instellen tussen de huidige datum en drie jaar na de huidige datum.
1. Selecteer **Opslaan**. Het nieuwe certificaat wordt nu weergegeven met de status **Inactief,** de door u gekozen vervaldatum en een vingerafdruk.
1. Selecteer de **X om** terug te keren naar de pagina Single Sign-On instellen **met SAML - Preview.**

### <a name="upload-and-activate-a-certificate"></a>Een certificaat uploaden en activeren

Download vervolgens het nieuwe certificaat in de juiste indeling, upload het naar de toepassing en maak het actief in Azure Active Directory:

1. Bekijk de aanvullende configuratie-instructies voor SAML-aanmelding door:

   - De koppeling **naar de configuratiehandleiding** selecteren om weer te geven in een afzonderlijk browservenster of tabblad, of
   - Ga naar de **kop instellen en** selecteer **Stapsgewijs** weergeven instructies om weer te geven in een zijbalk.

1. Noteer in de instructies de coderingsindeling die is vereist voor het uploaden van het certificaat.
1. Volg de instructies in [de sectie Automatisch gegenereerd certificaat voor galerie- en niet-galerietoepassingen](#auto-generated-certificate-for-gallery-and-non-gallery-applications) eerder. Met deze stap wordt het certificaat gedownload in de coderingsindeling die is vereist voor het uploaden door de toepassing.
1. Wanneer u wilt overrollen naar het nieuwe certificaat, gaat u terug naar de pagina **SAML-handtekeningcertificaat** en selecteert u in de zojuist opgeslagen certificaatrij het beletselteken (**...**) en selecteert u **Certificaat actief maken.** De status van het nieuwe certificaat wordt gewijzigd in **Actief** en het eerder actieve certificaat wordt gewijzigd in de status **Inactief.**
1. Ga door met het volgen van de configuratie-instructies voor SAML-aanmelding die u eerder hebt weergegeven, zodat u het SAML-handtekeningcertificaat in de juiste coderingsindeling kunt uploaden.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>E-mailmeldingsadressen toevoegen voor verlopen certificaten

Azure AD verzendt 60, 30 en 7 dagen voordat het SAML-certificaat verloopt een e-mailmelding. U kunt meer dan één e-mailadres toevoegen om meldingen te ontvangen. Als u de e-mailadressen wilt opgeven waar u de meldingen naar wilt verzenden:

1. Ga op **de pagina SAML-handtekeningcertificaat** naar de kop **e-mailadressen van meldingen.** Deze kop gebruikt standaard alleen het e-mailadres van de beheerder die de toepassing heeft toegevoegd.
1. Typ onder het uiteindelijke e-mailadres het e-mailadres dat de vervaldatum van het certificaat moet ontvangen en druk op Enter.
1. Herhaal de vorige stap voor elk e-mailadres dat u wilt toevoegen.
1. Selecteer voor elk e-mailadres dat u wilt verwijderen het pictogram **Verwijderen** (een prullenbak) naast het e-mailadres.
1. Selecteer **Opslaan**.

U kunt maximaal 5 e-mailadressen toevoegen aan de lijst met meldingen (inclusief het e-mailadres van de beheerder die de toepassing heeft toegevoegd). Als u wilt dat meer personen op de hoogte worden gesteld, gebruikt u de e-mailberichten van de distributielijst.

U ontvangt de e-mailmelding van aadnotification@microsoft.com . Als u wilt voorkomen dat het e-mailbericht naar uw spamlocatie wordt verzonden, voegt u dit e-mailbericht toe aan uw contactpersonen.

## <a name="renew-a-certificate-that-will-soon-expire"></a>Een certificaat vernieuwen dat binnenkort verloopt

Als een certificaat op het punt staat te verlopen, kunt u het vernieuwen met behulp van een procedure die geen aanzienlijke downtime voor uw gebruikers tot gevolg heeft. Een verlopend certificaat vernieuwen:

1. Volg de instructies in de [sectie Een nieuw certificaat](#create-a-new-certificate) maken eerder, met behulp van een datum die overlapt met het bestaande certificaat. Deze datum beperkt de hoeveelheid downtime die wordt veroorzaakt door het verlopen van het certificaat.
1. Als de toepassing automatisch een certificaat kan omdraaien, stelt u het nieuwe certificaat in op actief door de volgende stappen uit te voeren:
   1. Terug naar de **pagina SAML-handtekeningcertificaat.**
   1. Selecteer in de rij met het zojuist opgeslagen certificaat het beletselteken (**...**) en selecteer **vervolgens Certificaat actief maken.**
   1. Sla de volgende twee stappen over.

1. Als de app slechts één certificaat tegelijk kan verwerken, kiest u een downtime-interval om de volgende stap uit te voeren. (Als de toepassing het nieuwe certificaat niet automatisch oppikt, maar meer dan één handtekeningcertificaat kan verwerken, kunt u de volgende stap op elk gewenst moment uitvoeren.)
1. Voordat het oude certificaat verloopt, volgt u de instructies in de sectie [Een certificaat](#upload-and-activate-a-certificate) uploaden en activeren eerder.
1. Meld u aan bij de toepassing om ervoor te zorgen dat het certificaat correct werkt.

## <a name="related-articles"></a>Verwante artikelen:

- [Tutorials for integrating SaaS applications with Azure Active Directory](../saas-apps/tutorial-list.md) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)
- [Toepassingsbeheer met Azure Active Directory](what-is-application-management.md)
- [Eenmalige aanmelding bij toepassingen in Azure Active Directory](what-is-single-sign-on.md)
- [Foutopsporing uitvoeren in op SAML gebaseerde eenmalige aanmelding bij toepassingen in Azure Active Directory](./debug-saml-sso-issues.md)
