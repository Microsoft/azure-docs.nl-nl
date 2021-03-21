---
title: 'Azure AD Connect: naadloze single Sign-On-hoe het werkt | Microsoft Docs'
description: In dit artikel wordt beschreven hoe de Azure Active Directory naadloze single Sign-On-functie werkt.
services: active-directory
keywords: Wat is Azure AD Connect, installeer Active Directory, vereiste onderdelen voor Azure AD, SSO, eenmalige aanmelding
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bde937adba8d2469390a6cf404f6cce8c5008e87
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100369642"
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Azure Active Directory naadloze eenmalige aanmelding: technische grondige kennis

In dit artikel vindt u technische details over de manier waarop de Azure Active Directory naadloze SSO-functie (single Sign-On) werkt.

## <a name="how-does-seamless-sso-work"></a>Hoe werkt naadloze SSO?

Deze sectie heeft drie delen:

1. De installatie van de functie naadloze SSO.
2. Hoe een eenmalige gebruiker zich aanmeldt in een webbrowser, werkt met naadloze SSO.
3. Hoe een eenmalige gebruiker zich aanmeldt op een systeem eigen client, werkt met naadloze SSO.

### <a name="how-does-set-up-work"></a>Hoe werkt het instellen?

Naadloze SSO is ingeschakeld met Azure AD Connect, zoals [hier](how-to-connect-sso-quick-start.md)wordt weer gegeven. De volgende stappen worden uitgevoerd tijdens het inschakelen van de functie:

- Er wordt een computer account ( `AZUREADSSOACC` ) gemaakt in uw on-premises Active Directory (AD) in elk AD-forest dat u synchroniseert met Azure AD (met behulp van Azure AD Connect).
- Daarnaast wordt er een aantal Spn's (Service Principal Names) voor Kerberos gemaakt dat wordt gebruikt tijdens het aanmelden bij Azure AD.
- De Kerberos-ontsleutelings sleutel van de computer account wordt beveiligd gedeeld met Azure AD. Als er meerdere AD-forests zijn, heeft elk computer account een eigen unieke Kerberos-ontsleutelings sleutel.

>[!IMPORTANT]
> Het `AZUREADSSOACC` computer account moet om veiligheids redenen sterk worden beveiligd. Alleen domein Administrators moeten het computer account kunnen beheren. Zorg ervoor dat Kerberos-delegering op het computer account is uitgeschakeld en dat geen ander account in Active Directory overdrachts machtigingen heeft voor het `AZUREADSSOACC` computer account. Sla het computer account op in een organisatie-eenheid (OE) waar het veilig is tegen onbedoeld verwijderen en waar alleen domein beheerders toegang hebben. De Kerberos-ontsleutelings sleutel op het computer account moet ook als gevoelig worden beschouwd. We raden u ten zeerste aan [de Kerberos-ontsleutelings sleutel](how-to-connect-sso-faq.md) van het `AZUREADSSOACC` computer account ten minste elke 30 dagen in te voeren.

>[!IMPORTANT]
> Naadloze SSO ondersteunt de AES256_HMAC_SHA1, AES128_HMAC_SHA1 en RC4_HMAC_MD5 versleutelings typen voor Kerberos. Het is raadzaam om het versleutelings type voor de AzureADSSOAcc $-account in te stellen op AES256_HMAC_SHA1, of een van de AES-typen versus RC4 voor extra beveiliging. Het versleutelings type wordt opgeslagen in het kenmerk msDS-SupportedEncryptionTypes van het account in uw Active Directory.  Als het versleutelings type AzureADSSOAcc $-account is ingesteld op RC4_HMAC_MD5 en u het wilt wijzigen in een van de AES-versleutelings typen, moet u ervoor zorgen dat u de eerste keer de Kerberos-ontsleutelingssleutel van het AzureADSSOAcc $-account uitschakelt, zoals wordt uitgelegd in het [document met veelgestelde vragen](how-to-connect-sso-faq.md) onder de relevante vraag, anders wordt naadloze SSO niet uitgevoerd.

Zodra de installatie is voltooid, werkt naadloze SSO op dezelfde manier als andere aanmelding waarbij gebruik wordt gemaakt van geïntegreerde Windows-authenticatie (IWA).

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>Hoe werkt de aanmelding in een webbrowser met naadloze SSO?

De aanmeldings stroom van een webbrowser is als volgt:

1. De gebruiker probeert toegang te krijgen tot een webtoepassing (bijvoorbeeld de Outlook Web app- https://outlook.office365.com/owa/) van een bedrijfs apparaat dat lid is van een domein in uw bedrijfs netwerk.
2. Als de gebruiker nog niet is aangemeld, wordt de gebruiker omgeleid naar de aanmeldings pagina van Azure AD.
3. De gebruiker typt de gebruikers naam in de aanmeldings pagina van Azure AD.

   >[!NOTE]
   >Voor [bepaalde toepassingen](./how-to-connect-sso-faq.md)worden stap 2 & 3 overgeslagen.

4. Met behulp van Java script kunt u met Azure AD via een 401 niet-geautoriseerde reactie een Kerberos-ticket aanbieden aan de browser.
5. De browser vraagt op zijn beurt een ticket aan bij Active Directory voor het `AZUREADSSOACC` computer account (dat staat voor Azure AD).
6. Active Directory het computer account zoekt en een Kerberos-ticket retourneert naar de browser die is versleuteld met het geheim van het computer account.
7. De browser stuurt het Kerberos-ticket dat is verkregen van Active Directory naar Azure AD.
8. Azure AD ontsleutelt het Kerberos-ticket, dat de identiteit bevat van de gebruiker die is aangemeld bij het bedrijfs apparaat, met behulp van de eerder gedeelde sleutel.
9. Na de evaluatie retourneert Azure AD een token terug naar de toepassing of vraagt de gebruiker om extra proeven uit te voeren, zoals Multi-Factor Authentication.
10. Als de gebruiker zich aanmeldt, kan de gebruiker toegang krijgen tot de toepassing.

In het volgende diagram ziet u alle onderdelen en de betrokken stappen.

![Naadloze stroom voor eenmalige aanmelding op Web-apps](./media/how-to-connect-sso-how-it-works/sso2.png)

Naadloze SSO is opportunistisch, wat betekent dat als dit mislukt, de aanmeldings ervaring weer normaal is. de gebruiker moet het wacht woord invoeren om zich aan te melden.

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>Hoe werkt aanmelden op een systeem eigen client met naadloze SSO?

De aanmeldings stroom op een systeem eigen client is als volgt:

1. De gebruiker probeert toegang te krijgen tot een systeem eigen toepassing (bijvoorbeeld de Outlook-client) vanuit een bedrijfs apparaat dat lid is van een domein in uw bedrijfs netwerk.
2. Als de gebruiker nog niet is aangemeld, haalt de systeem eigen toepassing de gebruikers naam van de gebruiker op uit de Windows-sessie van het apparaat.
3. De app verzendt de gebruikers naam naar Azure AD en haalt het WS-Trust MEX-eind punt van uw Tenant op. Dit WS-Trust-eind punt wordt uitsluitend gebruikt door de functie naadloze SSO en is geen algemene implementatie van het WS-Trust-protocol op Azure AD.
4. De app stuurt vervolgens een query naar het WS-Trust MEX-eind punt om te zien of er een geïntegreerd verificatie-eind punt beschikbaar is. Het geïntegreerde verificatie-eind punt wordt uitsluitend gebruikt door de functie naadloze SSO.
5. Als stap 4 slaagt, wordt er een Kerberos-vraag gegeven.
6. Als de app het Kerberos-ticket kan ophalen, wordt deze doorgestuurd naar het geïntegreerde verificatie-eind punt van Azure AD.
7. Het Kerberos-ticket wordt door Azure AD ontsleuteld en gevalideerd.
8. Azure AD ondertekent de gebruiker in en geeft een SAML-token door aan de app.
9. De app verzendt vervolgens het SAML-token naar het OAuth2-token eindpunt van Azure AD.
10. Azure AD valideert het SAML-token en verleent aan de app een toegangs token en een vernieuwings token voor de opgegeven resource en een id-token.
11. De gebruiker krijgt toegang tot de resource van de app.

In het volgende diagram ziet u alle onderdelen en de betrokken stappen.

![Naadloze stroom voor eenmalige aanmelding op de eigen app](./media/how-to-connect-sso-how-it-works/sso14.png)

## <a name="next-steps"></a>Volgende stappen

- [**Snel starten**](how-to-connect-sso-quick-start.md) : krijg Azure AD naadloze SSO en voer deze uit.
- [**Veelgestelde vragen**](how-to-connect-sso-faq.md) : antwoorden op veelgestelde vragen.
- [**Problemen oplossen**](tshoot-connect-sso.md) : informatie over het oplossen van veelvoorkomende problemen met de functie.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) -voor het indienen van nieuwe functie aanvragen.
