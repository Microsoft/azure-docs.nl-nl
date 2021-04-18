---
title: SAML-tokenclaims voor apps aanpassen
titleSuffix: Microsoft identity platform
description: Meer informatie over het aanpassen van de claims die zijn uitgegeven door het Microsoft Identity Platform in het SAML-token voor bedrijfstoepassingen.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: kenwith
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.openlocfilehash: 25e737afb524cb8c6f45ac8e99f46a8064ae7855
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598836"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Hoe: claims aanpassen die zijn uitgegeven in het SAML-token voor bedrijfstoepassingen

Het Microsoft Identity Platform ondersteunt momenteel eenmalige aanmelding (SSO) met de meeste bedrijfstoepassingen, waaronder zowel toepassingen die vooraf zijn geïntegreerd in de Azure AD-appgalerie als aangepaste toepassingen. Wanneer een gebruiker zich bij een toepassing verifieert via het Microsoft Identity Platform met behulp van het SAML 2.0-protocol, verzendt het Microsoft Identity Platform een token naar de toepassing (via een HTTP POST). Vervolgens valideert en gebruikt de toepassing het token om de gebruiker aan te melden in plaats van om een gebruikersnaam en wachtwoord te vragen. Deze SAML-tokens bevatten stukjes informatie over de gebruiker, ook wel *claims genoemd.*

Een *claim* is informatie die door een id-provider wordt verstrekt over een gebruiker in het token dat voor die gebruiker wordt verstrekt. In [het SAML-token](https://en.wikipedia.org/wiki/SAML_2.0)zijn deze gegevens doorgaans opgenomen in de SAML-kenmerkverklaring. De unieke id van de gebruiker wordt doorgaans weergegeven in het SAML-onderwerp dat ook wordt aangeroepen als naam-id.

Het Microsoft Identity Platform geeft standaard een SAML-token uit aan uw toepassing die een claim bevat met een waarde van de gebruikersnaam van de gebruiker (ook wel bekend als de user principal name) in Azure AD, waarmee de gebruiker uniek kan worden `NameIdentifier` identificeren. Het SAML-token bevat ook aanvullende claims met het e-mailadres, de voornaam en de achternaam van de gebruiker.

Als u de claims wilt weergeven of bewerken die zijn uitgegeven in het SAML-token voor de toepassing, opent u de toepassing in Azure Portal. Open vervolgens **de sectie Gebruikerskenmerken & Claims.**

![Open de sectie Gebruikerskenmerken & Claims in de Azure Portal](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Er zijn twee mogelijke redenen waarom u mogelijk de claims moet bewerken die zijn uitgegeven in het SAML-token:

* De toepassing vereist dat de claim of NameID iets anders is dan de gebruikersnaam `NameIdentifier` (of user principal name) die is opgeslagen in Azure AD.
* De toepassing is geschreven om een andere set claim-URI's of claimwaarden te vereisen.

## <a name="editing-nameid"></a>NameID bewerken

De NameID (naam-id-waarde) bewerken:

1. Open de **waardepagina Naam-id.**
1. Selecteer het kenmerk of de transformatie die u wilt toepassen op het kenmerk. U kunt desgewenst de indeling opgeven die u voor de NameID-claim wilt gebruiken.

   ![De waarde voor NameID (naam-id) bewerken](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>NameID-indeling

Als de SAML-aanvraag het element NameIDPolicy met een specifieke indeling bevat, respecteert het Microsoft-identiteitsplatform de indeling in de aanvraag.

Als de SAML-aanvraag geen element voor NameIDPolicy bevat, geeft het Microsoft Identity Platform de NameID uit met de indeling die u opgeeft. Als er geen indeling is opgegeven, gebruikt het Microsoft-identiteitsplatform de standaardbronindeling die is gekoppeld aan de geselecteerde claimbron.

In de **kies naam-id-indeling** vervolgkeuze kunt u een van de volgende opties selecteren.

| NameID-indeling | Description |
|---------------|-------------|
| **Standaard** | Microsoft Identity Platform gebruikt de standaardbronindeling. |
| **Permanent** | Het Microsoft Identity Platform gebruikt Permanent als de NameID-indeling. |
| **Emailaddress** | Het Microsoft Identity Platform gebruikt EmailAddress als de NameID-indeling. |
| **Niet opgegeven** | Het Microsoft Identity Platform gebruikt Niet-gespecificeerd als de NameID-indeling. |

Tijdelijke naam-id wordt ook ondersteund, maar is niet beschikbaar in de vervolgkeuzelijn en kan niet worden geconfigureerd aan de zijde van Azure. Zie Single Sign-On SAML protocol voor meer informatie over [het kenmerk NameIDPolicy.](single-sign-on-saml-protocol.md)

### <a name="attributes"></a>Kenmerken

Selecteer de gewenste bron voor de `NameIdentifier` claim (of NameID). U kunt kiezen uit de volgende opties.

| Naam | Beschrijving |
|------|-------------|
| E-mail | Het e-mailadres van de gebruiker |
| userprincipalName | User Principal Name (UPN) van de gebruiker |
| onpremisessamaccountname | SAM-accountnaam die is gesynchroniseerd vanuit on-premises Azure AD |
| objectid | Objectid van de gebruiker in Azure AD |
| employeeid | Werknemer-id van de gebruiker |
| Uitbreidingen van de directory | [Directory-extensies die zijn gesynchroniseerd vanuit on-premises Active Directory met Azure AD Connect Sync](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Extensiekenmerken 1-15 | On-premises extensiekenmerken die worden gebruikt om het Azure AD-schema uit te breiden |

Zie Tabel [3: geldige id-waarden per bron voor meer informatie.](reference-claims-mapping-policy-type.md#table-3-valid-id-values-per-source)

U kunt ook elke constante (statische) waarde toewijzen aan claims die u in Azure AD definieert. Volg de onderstaande stappen om een constante waarde toe te wijzen:

1. Klik in <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>in de sectie Gebruikerskenmerken **& Claims** op het  pictogram Bewerken om de claims te bewerken.
1. Klik op de vereiste claim die u wilt wijzigen.
1. Voer de constante waarde zonder aanhalingstekens in **het kenmerk Bron** in volgens uw organisatie en klik op **Opslaan.**

    ![De sectie Organisatiekenmerken & Claims in de Azure Portal](./media/active-directory-saml-claims-customization/organization-attribute.png)

1. De constante waarde wordt weergegeven zoals hieronder.

    ![Bewerk de sectie Kenmerken & Claims in de Azure Portal](./media/active-directory-saml-claims-customization/edit-attributes-claims.png)

### <a name="special-claims---transformations"></a>Speciale claims - transformaties

U kunt ook de functies voor claimtransformaties gebruiken.

| Functie | Beschrijving |
|----------|-------------|
| **ExtractMailPrefix()** | Hiermee verwijdert u het domeinachtervoegsel uit het e-mailadres of het user principal name. Hiermee wordt alleen het eerste deel van de gebruikersnaam geëxtraheert die wordt doorgegeven (bijvoorbeeld 'joe_smith' in plaats van joe_smith@contoso.com ). |
| **Join()** | Voegt een kenmerk met een geverifieerd domein toe. Als de geselecteerde gebruikers-id een domein heeft, wordt de gebruikersnaam geëxtraheerd om het geselecteerde geverifieerde domein toe te toevoegen. Als u bijvoorbeeld het e-mailadres ( ) als waarde voor de gebruikers-id selecteert en contoso.onmicrosoft.com als het geverifieerde domein, resulteert dit joe_smith@contoso.com in joe_smith@contoso.onmicrosoft.com . |
| **ToLower()** | Converteert de tekens van het geselecteerde kenmerk in kleine letters. |
| **ToUpper()** | Converteert de tekens van het geselecteerde kenmerk naar hoofdletters. |

## <a name="adding-application-specific-claims"></a>Toepassingsspecifieke claims toevoegen

Toepassingsspecifieke claims toevoegen:

1. Selecteer **in Gebruikerskenmerken & Claims** de optie Nieuwe claim toevoegen **om** de pagina **Gebruikersclaims beheren te** openen.
1. Voer de **naam** van de claims in. De waarde hoeft strikt genomen geen URI-patroon te volgen volgens de SAML-specificatie. Als u een URI-patroon nodig hebt, kunt u dat in het **veld Naamruimte** zetten.
1. Selecteer de **Bron** waar de claim de waarde van de claim gaat ophalen. U kunt een gebruikerskenmerk selecteren in de vervolgkeuzekeuze selecteren of een transformatie toepassen op het gebruikerskenmerk voordat u het als een claim kunt indienen.

### <a name="claim-transformations"></a>Claimtransformaties

Een transformatie toepassen op een gebruikerskenmerk:

1. In **Claim beheren selecteert** u *Transformatie als* claimbron om de pagina Transformatie **beheren te** openen.
2. Selecteer de functie in de vervolgkeuzekeuze selecteren. Afhankelijk van de geselecteerde functie moet u parameters en een constante waarde opgeven om te evalueren in de transformatie. Raadpleeg de onderstaande tabel voor meer informatie over de beschikbare functies.
3. Als u meerdere transformaties wilt toepassen, klikt u op **Transformatie toevoegen.** U kunt maximaal twee transformaties toepassen op een claim. U kunt bijvoorbeeld eerst het e-mail voorvoegsel van de `user.mail` extraheren. Maak vervolgens de hoofdletter van de tekenreeks.

   ![Transformatie van meerdere claims](./media/active-directory-saml-claims-customization/sso-saml-multiple-claims-transformation.png)

U kunt de volgende functies gebruiken om claims te transformeren.

| Functie | Beschrijving |
|----------|-------------|
| **ExtractMailPrefix()** | Hiermee verwijdert u het domeinachtervoegsel uit het e-mailadres of het user principal name. Hiermee wordt alleen het eerste deel van de gebruikersnaam geëxtraheert die wordt doorgegeven (bijvoorbeeld 'joe_smith' in plaats van joe_smith@contoso.com ). |
| **Join()** | Hiermee maakt u een nieuwe waarde door twee kenmerken samen te werken. U kunt eventueel een scheidingsteken tussen de twee kenmerken gebruiken. Voor de transformatie van de NameID-claim is de join beperkt tot een geverifieerd domein. Als de geselecteerde waarde voor de gebruikers-id een domein heeft, wordt de gebruikersnaam geëxtraheerd om het geselecteerde geverifieerde domein toe te toevoegen. Als u bijvoorbeeld het e-mailadres ( ) selecteert als de waarde voor de gebruikers-id en contoso.onmicrosoft.com als het geverifieerde domein, resulteert dit joe_smith@contoso.com in joe_smith@contoso.onmicrosoft.com . |
| **ToLowercase()** | Converteert de tekens van het geselecteerde kenmerk in kleine letters. |
| **ToUppercase()** | Converteert de tekens van het geselecteerde kenmerk naar hoofdletters. |
| **Contains()** | Hiermee wordt een kenmerk of constante uitgevoerd als de invoer overeenkomt met de opgegeven waarde. Anders kunt u een andere uitvoer opgeven als er geen overeenkomst is.<br/>Als u bijvoorbeeld een claim wilt verzenden waarbij de waarde het e-mailadres van de gebruiker is als deze het domein "" bevat, anders wilt u de uitvoer van de @contoso.com user principal name. Hiervoor configureert u de volgende waarden:<br/>*Parameter 1(input)*: user.email<br/>*Waarde:*" @contoso.com "<br/>Parameter 2 (uitvoer): user.email<br/>Parameter 3 (uitvoer als er geen overeenkomst is): user.userprincipalname |
| **EndWith()** | Hiermee wordt een kenmerk of constante uitgevoerd als de invoer eindigt met de opgegeven waarde. Anders kunt u een andere uitvoer opgeven als er geen overeenkomst is.<br/>Als u bijvoorbeeld een claim wilt indienen waarbij de waarde de werknemers-id van de gebruiker is als de werknemers-id eindigt op '000', wilt u anders een extensiekenmerk als uitvoer gebruiken. Hiervoor configureert u de volgende waarden:<br/>*Parameter 1(input)*: user.employeeid<br/>*Waarde:*"000"<br/>Parameter 2 (uitvoer): user.employeeid<br/>Parameter 3 (uitvoer als er geen overeenkomst is): user.extensionattribute1 |
| **StartWith()** | Hiermee wordt een kenmerk of constante uitgevoerd als de invoer begint met de opgegeven waarde. Anders kunt u een andere uitvoer opgeven als er geen overeenkomst is.<br/>Als u bijvoorbeeld een claim wilt indienen waarbij de waarde de werknemer-id van de gebruiker is als het land/de regio begint met 'US', anders wilt u een extensiekenmerk als uitvoer gebruiken. Hiervoor configureert u de volgende waarden:<br/>*Parameter 1(input)*: user.country<br/>*Waarde:*'US'<br/>Parameter 2 (uitvoer): user.employeeid<br/>Parameter 3 (uitvoer als er geen overeenkomst is): user.extensionattribute1 |
| **Extract() - Na het matchen** | Retourneert de subtekenreeks nadat deze overeenkomt met de opgegeven waarde.<br/>Als de waarde van de invoer bijvoorbeeld 'Finance_BSimon' is, is de overeenkomende waarde 'Finance_', dan is de uitvoer van de claim 'BSimon'. |
| **Extract() - Voordat er wordt gematcht** | Retourneert de subtekenreeks totdat deze overeenkomt met de opgegeven waarde.<br/>Als de waarde van de invoer bijvoorbeeld 'BSimon_US' is, is de overeenkomende waarde '_US', dan is de uitvoer van de claim 'BSimon'. |
| **Extract() - Tussen overeenkomende** | Retourneert de subtekenreeks totdat deze overeenkomt met de opgegeven waarde.<br/>Als de waarde van de invoer bijvoorbeeld 'Finance_BSimon_US' is, is de eerste overeenkomende waarde 'Finance', is de tweede overeenkomende waarde 'US', dan is de uitvoer van de \_ \_ claim 'BSimon'. |
| **ExtractAlpha() - Voorvoegsel** | Retourneert het alfabetische voorvoegselgedeelte van de tekenreeks.<br/>Als de waarde van de invoer bijvoorbeeld 'BSimon_123' is, wordt 'BSimon' als resultaat gegeven. |
| **ExtractAlpha() - Achtervoegsel** | Retourneert het achtervoegsel alfabetisch deel van de tekenreeks.<br/>Als de waarde van de invoer bijvoorbeeld '123_Simon' is, wordt 'Simon' als resultaat gegeven. |
| **ExtractNumeric() - Voorvoegsel** | Retourneert het numerieke voorvoegselgedeelte van de tekenreeks.<br/>Als de waarde van de invoer bijvoorbeeld '123_BSimon' is, wordt '123' als resultaat gegeven. |
| **ExtractNumeric() - Achtervoegsel** | Retourneert het numerieke achtervoegselgedeelte van de tekenreeks.<br/>Als de waarde van de invoer bijvoorbeeld 'BSimon_123' is, wordt '123' als resultaat gegeven. |
| **IfEmpty()** | Geeft een kenmerk of constante als de invoer null of leeg is.<br/>Als u bijvoorbeeld een kenmerk wilt gebruiken dat is opgeslagen in een extensionattribute als de werknemer-id voor een bepaalde gebruiker leeg is. Hiervoor configureert u de volgende waarden:<br/>Parameter 1(input): user.employeeid<br/>Parameter 2 (uitvoer): user.extensionattribute1<br/>Parameter 3 (uitvoer als er geen overeenkomst is): user.employeeid |
| **IfNotEmpty()** | Geeft een kenmerk of constante als de invoer niet null of leeg is.<br/>Als u bijvoorbeeld een kenmerk wilt uitvoer dat is opgeslagen in een extensionattribute als de werknemer-id voor een bepaalde gebruiker niet leeg is. Hiervoor configureert u de volgende waarden:<br/>Parameter 1(input): user.employeeid<br/>Parameter 2 (uitvoer): user.extensionattribute1 |

Als u aanvullende transformaties nodig hebt, kunt u uw idee indienen in [het feedbackforum in Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) onder de categorie *SaaS-toepassing.*

## <a name="emitting-claims-based-on-conditions"></a>Claims uitzenden op basis van voorwaarden

U kunt de bron van een claim opgeven op basis van het gebruikerstype en de groep waarvan de gebruiker deel uitmaken. 

Het gebruikerstype kan zijn:
- **Alle**: alle gebruikers hebben toegang tot de toepassing.
- **Leden:** native lid van de tenant
- **Alle gasten:** de gebruiker wordt overgenomen van een externe organisatie met of zonder Azure AD.
- **AAD-gasten:** gastgebruiker behoort tot een andere organisatie met behulp van Azure AD.
- **Externe gasten:** gastgebruiker behoort tot een externe organisatie die geen Azure AD heeft.


Een scenario waarin dit handig is, is wanneer de bron van een claim verschilt voor een gast en een werknemer die toegang heeft tot een toepassing. U kunt opgeven dat als de gebruiker een werknemer is, de NameID afkomstig is van user.email, maar als de gebruiker een gast is, wordt de NameID afkomstig uit user.extensionattribute1.

Een claimvoorwaarde toevoegen:

1. Vouw **in Claim beheren** de Claimvoorwaarden uit.
2. Selecteer het gebruikerstype.
3. Selecteer de groep(en) waarvan de gebruiker deel moet uitmaken. U kunt maximaal 50 unieke groepen voor alle claims voor een bepaalde toepassing selecteren. 
4. Selecteer de **Bron** waar de claim de waarde van de claim gaat ophalen. U kunt een gebruikerskenmerk selecteren in de vervolgkeuze voor bronkenmerken of een transformatie toepassen op het gebruikerskenmerk voordat u deze als een claim kunt indienen.

De volgorde waarin u de voorwaarden toevoegt, is belangrijk. Azure AD evalueert de voorwaarden van boven naar beneden om te bepalen welke waarde in de claim moet worden ingediend. De laatste waarde die overeenkomt met de expressie wordt in de claim uitgezonden.

Britta Simon is bijvoorbeeld een gastgebruiker in de Contoso-tenant. Ze behoort tot een andere organisatie die ook gebruikmaakt van Azure AD. Gezien de onderstaande configuratie voor de Fabrikam-toepassing, worden de voorwaarden als volgt geëvalueerd wanneer Britta zich probeert aan te melden bij Fabrikam.

Eerst controleert het Microsoft Identity Platform of het gebruikerstype van Britta `All guests` is. Aangezien dit waar is, wijst het Microsoft Identity Platform de bron voor de claim toe aan `user.extensionattribute1` . Ten tweede controleert het Microsoft-identiteitsplatform of het gebruikerstype van Britta is, omdat dit ook waar is. Vervolgens wijst het `AAD guests` Microsoft-identiteitsplatform de bron voor de claim toe aan `user.mail` . Ten slotte wordt de claim met de waarde `user.mail` voor Britta uitgezonden.

![Voorwaardelijke configuratie van claims](./media/active-directory-saml-claims-customization/sso-saml-user-conditional-claims.png)

## <a name="next-steps"></a>Volgende stappen

* [Toepassingsbeheer in Azure AD](../manage-apps/what-is-application-management.md)
* [Een aanmelding configureren voor toepassingen die zich niet in de Azure AD-toepassingsgalerie](../manage-apps/configure-saml-single-sign-on.md)
* [Problemen met eenmalige aanmelding op basis van SAML oplossen](../manage-apps/debug-saml-sso-issues.md)
