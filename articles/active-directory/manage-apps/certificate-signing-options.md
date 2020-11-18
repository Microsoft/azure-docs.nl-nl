---
title: Geavanceerde opties voor het ondertekenen van SAML-token certificaten voor Azure AD-apps
description: Meer informatie over het gebruik van geavanceerde opties voor certificaat ondertekening in het SAML-token voor vooraf geïntegreerde apps in Azure Active Directory
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: kenwith
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79bc43bb2fa99f95a462dcc4c68d27840199b45c
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658533"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Geavanceerde opties voor certificaat ondertekening in het SAML-token voor galerij-apps in Azure Active Directory

Vandaag Azure Active Directory (Azure AD) ondersteunt duizenden vooraf geïntegreerde toepassingen in de app-galerie van Azure Active Directory. Meer dan 500 van de toepassingen bieden ondersteuning voor eenmalige aanmelding met behulp van het [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) 2,0-protocol, zoals de toepassing [NetSuite](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) . Wanneer een klant zich via Azure AD bij een toepassing verifieert met behulp van SAML, verzendt Azure AD een token naar de toepassing (via een HTTP POST). De toepassing valideert vervolgens het token om zich aan te melden bij de klant in plaats van een gebruikers naam en wacht woord op te vragen. Deze SAML-tokens zijn ondertekend met het unieke certificaat dat wordt gegenereerd in azure AD en aan specifieke standaard algoritmen.

Azure AD gebruikt enkele van de standaard instellingen voor de galerie toepassingen. De standaard waarden worden ingesteld op basis van de vereisten van de toepassing.

In azure AD kunt u opties voor het ondertekenen van certificaten en het algoritme voor het ondertekenen van certificaten instellen.

## <a name="certificate-signing-options"></a>Opties voor certificaatondertekening

Azure AD ondersteunt drie opties voor certificaat ondertekening:

* **SAML-bevestiging ondertekenen**. Deze standaard optie is ingesteld voor de meeste galerie toepassingen. Als u deze optie selecteert, ondertekent Azure AD als een id-provider (IdP) de SAML-bevestiging en het certificaat met het [X. 509](https://wikipedia.org/wiki/X.509) -certificaat van de toepassing.

* **SAML-respons ondertekenen**. Als u deze optie selecteert, ondertekent Azure AD als een IdP het SAML-antwoord met het X. 509-certificaat van de toepassing.

* **SAML-respons en-bevestiging ondertekenen**. Als u deze optie selecteert, ondertekent Azure AD als een IdP het volledige SAML-token met het X. 509-certificaat van de toepassing.

## <a name="certificate-signing-algorithms"></a>Algoritmen voor certificaat ondertekening

Azure AD ondersteunt twee Ondertekeningsalgoritme, of algoritmen voor beveiligde hash-algoritmen, voor het ondertekenen van het SAML-antwoord:

* **SHA-256**. Azure AD gebruikt deze standaard algoritme voor het ondertekenen van het SAML-antwoord. Het is het nieuwste algoritme en veiliger dan SHA-1. De meeste toepassingen ondersteunen het SHA-256-algoritme. Als een toepassing alleen SHA-1 als het handtekening algoritme ondersteunt, kunt u deze wijzigen. Anders raden wij aan dat u de SHA-256-algoritme gebruikt voor het ondertekenen van het SAML-antwoord.

* **SHA-1**. Dit algoritme is ouder en wordt behandeld als minder veilig dan SHA-256. Als een toepassing alleen dit Ondertekeningsalgoritme ondersteunt, kunt u deze optie selecteren in de vervolg keuzelijst **handtekening algoritme** . Azure AD ondertekent vervolgens de SAML-respons met het algoritme SHA-1.

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>Opties voor certificaat ondertekening en handtekening algoritme wijzigen

Als u de opties voor het ondertekenen van SAML-certificaten en het algoritme voor het ondertekenen van certificaten wilt wijzigen, selecteert u de betreffende toepassing:

1. Meld u aan bij uw account in de [Azure Active Directory Portal](https://aad.portal.azure.com/). De pagina **Azure Active Directory beheer centrum** wordt weer gegeven.
1. Selecteer in het linkerdeelvenster **Enterprise-toepassingen**. Er wordt een lijst weer gegeven met de bedrijfs toepassingen in uw account.
1. Selecteer een toepassing. Er wordt een overzichts pagina voor de toepassing weer gegeven.

   ![Voor beeld: overzichts pagina van de toepassing](./media/certificate-signing-options/application-overview-page.png)

Wijzig vervolgens de opties voor certificaat ondertekening in het SAML-token voor die toepassing:

1. Selecteer **eenmalige aanmelding** in het linkerdeel venster van de overzichts pagina van de toepassing.
1. Als de pagina **eén Sign-On met SAML-preview instellen** wordt weer gegeven, gaat u naar stap 5.
1. Als de pagina **Eén aanmeldings methode selecteren** niet wordt weer gegeven, selecteert u **modus voor eenmalige aanmelding wijzigen** om die pagina weer te geven.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML** indien beschikbaar. (Als **SAML** niet beschikbaar is, ondersteunt de toepassing geen SAML en kunt u de rest van deze procedure en dit artikel negeren.)
1. Zoek in de pagina **eén Sign-On met SAML-preview instellen** op de kop **SAML-handtekening certificaat** en selecteer het **bewerkings** pictogram (een potlood). De pagina **SAML-handtekening certificaat** wordt weer gegeven.

   ![Voor beeld: pagina SAML-handtekening certificaat](./media/certificate-signing-options/saml-signing-page.png)

1. Kies in de vervolg keuzelijst **handtekening opties** de optie **SAML-respons ondertekenen**, **SAML-bevestiging ondertekenen** of **SAML-respons en-bevestiging ondertekenen**. Beschrijvingen van deze opties worden eerder in dit artikel weer gegeven in de [Opties voor certificaat ondertekening](#certificate-signing-options).
1. Kies in de vervolg keuzelijst **handtekening algoritme** **SHA-1** of **SHA-256**. Beschrijvingen van deze opties worden eerder in dit artikel weer gegeven in de sectie [algoritmen voor certificaat ondertekening](#certificate-signing-algorithms) .
1. Als u tevreden bent met uw keuzes, selecteert u **Opslaan** om de nieuwe instellingen voor het SAML-handtekening certificaat toe te passen. Anders selecteert u de **X** om de wijzigingen te negeren.

## <a name="next-steps"></a>Volgende stappen

* [Eenmalige aanmelding configureren voor toepassingen die zich niet in de Azure Active Directory app-galerie bevinden](./configure-saml-single-sign-on.md)
* [Problemen met eenmalige aanmelding op basis van SAML oplossen](./debug-saml-sso-issues.md)