---
title: Geavanceerde opties voor het ondertekenen van SAML-tokencertificaten voor Azure AD-apps
description: Meer informatie over het gebruik van geavanceerde opties voor certificaat ondertekening in het SAML-token voor vooraf geïntegreerde apps in Azure Active Directory
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: iangithinji
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bb58f59b3796311f52d78ef87c8918f9888c152
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377867"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Geavanceerde opties voor certificaat ondertekening in het SAML-token voor galerie-apps in Azure Active Directory

Today Azure Active Directory (Azure AD) ondersteunt duizenden vooraf geïntegreerde toepassingen in de Azure Active Directory App Gallery. Meer dan 500 van de toepassingen ondersteunen [](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) een aanmelding via het Security Assertion Markup Language(SAML) 2.0-protocol, zoals de [NetSuite-toepassing.](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) Wanneer een klant zich bij een toepassing verifieert via Azure AD met behulp van SAML, verzendt Azure AD een token naar de toepassing (via een HTTP POST). De toepassing valideert en gebruikt het token vervolgens om de klant aan te melden in plaats van om een gebruikersnaam en wachtwoord te vragen. Deze SAML-tokens zijn ondertekend met het unieke certificaat dat wordt gegenereerd in Azure AD en door specifieke standaardalgoritmen.

Azure AD maakt gebruik van enkele van de standaardinstellingen voor de galerietoepassingen. De standaardwaarden worden ingesteld op basis van de vereisten van de toepassing.

In Azure AD kunt u opties voor certificaat-ondertekening en het algoritme voor certificaat-ondertekening instellen.

## <a name="certificate-signing-options"></a>Opties voor certificaatondertekening

Azure AD ondersteunt drie opties voor certificaat-ondertekening:

* **Onderteken de SAML-bewering**. Deze standaardoptie is ingesteld voor de meeste galerietoepassingen. Als u deze optie selecteert, ondertekent Azure AD als id-provider (IdP) de SAML-assertie en het certificaat met het [X.509-certificaat](https://wikipedia.org/wiki/X.509) van de toepassing.

* **Onderteken het SAML-antwoord**. Als u deze optie selecteert, ondertekent Azure AD als IdP het SAML-antwoord met het X.509-certificaat van de toepassing.

* **Onderteken het SAML-antwoord en de verklaring**. Als u deze optie selecteert, ondertekent Azure AD als IdP het volledige SAML-token met het X.509-certificaat van de toepassing.

## <a name="certificate-signing-algorithms"></a>Algoritmen voor certificaat-ondertekening

Azure AD ondersteunt twee ondertekeningsalgoritmen, of veilige hash-algoritmen (SHA's) om het SAML-antwoord te ondertekenen:

* **SHA-256**. Azure AD gebruikt dit standaardalgoritme om het SAML-antwoord te ondertekenen. Het is het nieuwste algoritme en is veiliger dan SHA-1. De meeste toepassingen ondersteunen het SHA-256-algoritme. Als een toepassing alleen SHA-1 als ondertekeningsalgoritme ondersteunt, kunt u dit wijzigen. Anders wordt u aangeraden het SHA-256-algoritme te gebruiken voor het ondertekenen van het SAML-antwoord.

* **SHA-1.** Dit algoritme is ouder en wordt beschouwd als minder veilig dan SHA-256. Als een toepassing alleen dit ondertekeningsalgoritme ondersteunt, kunt u deze optie selecteren in **de** vervolgkeuzelijst Algoritme voor ondertekening. Azure AD ondertekent vervolgens het SAML-antwoord met het SHA-1-algoritme.

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>Opties voor certificaat-ondertekening en ondertekeningsalgoritme wijzigen

Als u de opties voor SAML-certificaat-ondertekening en het algoritme voor certificaat ondertekening van een toepassing wilt wijzigen, selecteert u de toepassing in kwestie:

1. Meld u [Azure Active Directory de portal](https://aad.portal.azure.com/)aan bij uw account. De **Azure Active Directory beheercentrumpagina** wordt weergegeven.
1. Selecteer in het linkerdeelvenster **Enterprise-toepassingen**. Er wordt een lijst met bedrijfstoepassingen in uw account weergegeven.
1. Selecteer een toepassing. Er wordt een overzichtspagina voor de toepassing weergegeven.

   ![Voorbeeld: Overzichtspagina van de toepassing](./media/certificate-signing-options/application-overview-page.png)

Wijzig vervolgens de opties voor certificaat ondertekenen in het SAML-token voor die toepassing:

1. Selecteer in het linkerdeelvenster van de overzichtspagina van de toepassing **de optie Een aanmelding.**
1. Als de **pagina Single Sign-On met SAML - Preview** wordt weergegeven, gaat u naar stap 5.
1. Als de **pagina Select a single sign-on method** niet wordt weergegeven, selecteert u Change single **sign-on modes** om die pagina weer te geven.
1. Selecteer op **de pagina Select a single sign-on method** de optie **SAML,** indien beschikbaar. (Als **SAML** niet beschikbaar is, biedt de toepassing geen ondersteuning voor SAML en kunt u de rest van deze procedure en dit artikel negeren.)
1. Zoek op **de pagina Single Sign-On met SAML - Preview** de kop **SAML-handtekeningcertificaat** en selecteer **het** pictogram Bewerken (een potlood). De **pagina SAML-handtekeningcertificaat** wordt weergegeven.

   ![Voorbeeld: pagina SAML-handtekeningcertificaat](./media/certificate-signing-options/saml-signing-page.png)

1. Kies in **de vervolgkeuzelijst** Ondertekeningsoptie **saml-antwoord** ondertekenen, **SAML-asserty** ondertekenen of **SAML-antwoord en -bewering ondertekenen.** Beschrijvingen van deze opties worden eerder in dit artikel weergegeven in de [opties voor certificaat-ondertekening.](#certificate-signing-options)
1. Kies  **sha-1** of **SHA-256** in de vervolgkeuzelijst Algoritme voor ondertekening. Beschrijvingen van deze opties worden eerder in dit artikel weergegeven in de sectie [Algoritmen voor certificaat-ondertekening.](#certificate-signing-algorithms)
1. Als u tevreden bent met uw keuzes, selecteert u **Opslaan om** de nieuwe instellingen voor het SAML-handtekeningcertificaat toe te passen. Selecteer anders de **X om** de wijzigingen te verwijderen.

## <a name="next-steps"></a>Volgende stappen

* [Een aanmelding configureren voor toepassingen die zich niet in de Azure Active Directory App Gallery](./configure-saml-single-sign-on.md)
* [Problemen met eenmalige aanmelding op basis van SAML oplossen](./debug-saml-sso-issues.md)