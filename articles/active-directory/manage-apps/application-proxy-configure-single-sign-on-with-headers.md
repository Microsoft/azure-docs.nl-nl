---
title: Eenmalige aanmelding op basis van een header voor on-premises apps met Azure AD-app proxy
description: Meer informatie over het bieden van eenmalige aanmelding voor on-premises toepassingen die zijn beveiligd met verificatie op basis van een header.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 512316b78a0d6422daf5e268ef30db72ccbcfaeb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101688311"
---
# <a name="header-based-single-sign-on-for-on-premises-apps-with-azure-ad-app-proxy"></a>Eenmalige aanmelding op basis van een header voor on-premises apps met Azure AD-app proxy

Azure Active Directory-toepassings proxy (Azure AD) biedt systeem eigen ondersteuning voor eenmalige aanmelding voor toepassingen die gebruikmaken van headers voor authenticatie. U kunt in azure AD vereiste header-waarden configureren voor uw toepassing. De header waarden worden via toepassings proxy verzonden naar de toepassing. Voor delen van het gebruik van systeem eigen ondersteuning voor verificatie op basis van een header met toepassings proxy zijn onder andere:  

* **Vereenvoudig externe toegang tot uw on-premises apps** : met app proxy kunt u uw bestaande architectuur voor externe toegang vereenvoudigen. U kunt VPN-toegang tot deze apps vervangen. U kunt ook afhankelijkheden van on-premises identiteits oplossingen voor verificatie verwijderen. Uw gebruikers zien niets anders wanneer ze zich aanmelden om uw bedrijfs toepassingen te gebruiken. Ze kunnen nog steeds vanaf elke locatie op elk apparaat werken.  

* **Geen extra software of wijzigingen in uw apps** : u kunt uw bestaande Application proxy-connectors gebruiken. u hoeft geen extra software te installeren.  

* **Breed overzicht van de beschik bare kenmerken en trans formaties** : alle beschik bare header waarden zijn gebaseerd op standaard claims die worden uitgegeven door Azure AD. Alle kenmerken en trans formaties die beschikbaar zijn voor het [configureren van claims voor SAML-of OIDC-toepassingen](../develop/active-directory-saml-claims-customization.md#attributes) , kunnen ook worden gebruikt als header waarden. 

## <a name="pre-requisites"></a>Vereisten
Voordat u aan de slag gaat met eenmalige aanmelding voor op headers gebaseerde verificatie-apps, moet u ervoor zorgen dat uw omgeving klaar is met de volgende instellingen en configuraties:
- U dient toepassings proxy in te scha kelen en een connector te installeren die een regel van de site voor uw toepassingen bevat. Raadpleeg de zelf studie [een lokale toepassing toevoegen voor externe toegang via toepassings proxy](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) om te leren hoe u uw on-premises omgeving voorbereidt, een connector installeert en registreert en de connector test. 

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

De volgende tabel bevat de algemene mogelijkheden die zijn vereist voor toepassingen die zijn gebaseerd op header verificatie die worden ondersteund met toepassings proxy. 

|Vereiste   |Beschrijving|
|----------|-----------|
|Federatieve SSO |In de vooraf geauthenticeerde modus worden alle toepassingen beveiligd met Azure AD-verificatie en kunnen gebruikers eenmalige aanmelding gebruiken. |
|Externe toegang |Met toepassings proxy kan externe toegang tot de app worden ingeschakeld. Gebruikers kunnen via internet toegang krijgen tot de toepassing via een browser met behulp van de externe URL. Toepassings proxy is niet bedoeld voor gebruik met bedrijfs toegang. |
|Op Koptekst gebaseerde integratie |Toepassings proxy voert de SSO-integratie met Azure AD uit en geeft identiteits-of andere toepassings gegevens als HTTP-headers door aan de toepassing. |
|Toepassings autorisatie |Gemeen schappelijke beleids regels kunnen worden opgegeven op basis van de toegang tot de toepassing, het groepslid maatschap en andere beleids regels van de gebruiker. In azure AD worden beleids regels geïmplementeerd met behulp van [voorwaardelijke toegang](../conditional-access/overview.md). Autorisatie beleid voor toepassingen is alleen van toepassing op de eerste verificatie aanvraag. |
|Stapsgewijze verificatie |Beleids regels kunnen worden gedefinieerd om toegevoegde verificatie te forceren, bijvoorbeeld om toegang te krijgen tot gevoelige bronnen. |
|Verfijnde autorisatie |Biedt toegangs beheer op URL-niveau. Toegevoegde beleids regels kunnen worden afgedwongen op basis van de URL die wordt geopend. De interne URL die voor de app is geconfigureerd, definieert het bereik van de app waarop het beleid wordt toegepast. Het beleid dat is geconfigureerd voor het meest gedetailleerde pad wordt afgedwongen.  |

> [!NOTE] 
> In dit artikel wordt gebruikgemaakt van het verbinden van op headers gebaseerde verificatie toepassingen naar Azure AD met behulp van toepassings proxy en is het aanbevolen patroon. Als alternatief is er ook een integratie patroon dat gebruikmaakt van PingAccess met Azure AD om verificatie op basis van een header mogelijk te maken. Zie voor meer informatie [verificatie op basis van headers voor eenmalige aanmelding met toepassings proxy en PingAccess](application-proxy-ping-access-publishing-guide.md).

## <a name="how-it-works"></a>Uitleg

:::image type="content" source="./media/application-proxy-configure-single-sign-on-with-headers/how-it-works.png" alt-text="Hoe eenmalige aanmelding op basis van een header werkt met toepassings proxy." lightbox="./media/application-proxy-configure-single-sign-on-with-headers/how-it-works.png":::

1. De beheerder past de kenmerk toewijzingen aan die vereist zijn voor de toepassing in de Azure AD-Portal. 
2. Wanneer een gebruiker toegang heeft tot de app, zorgt toepassings proxy ervoor dat de gebruiker wordt geverifieerd door Azure AD 
3. De Cloud service van de toepassings proxy is op de hoogte van de vereiste kenmerken. De service haalt dus de overeenkomende claims op uit het ID-token dat tijdens de verificatie is ontvangen. De service vertaalt vervolgens de waarden in de vereiste HTTP-headers als onderdeel van de aanvraag voor de connector. 
4. De aanvraag wordt vervolgens door gegeven aan de connector, die vervolgens wordt door gegeven aan de back-end-toepassing. 
5. De toepassing ontvangt de kopteksten en kan deze headers gebruiken als dat nodig is. 

## <a name="publish-the-application-with-application-proxy"></a>De toepassing publiceren met toepassings proxy

1. Publiceer uw toepassing volgens de instructies in [toepassingen publiceren met toepassings proxy](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).  
    - De interne URL-waarde bepaalt het bereik van de toepassing. Als u een interne URL-waarde op het hoofdpad van de toepassing configureert, krijgen alle subpaden onder de hoofdmap dezelfde header configuratie en andere toepassings configuratie. 
    - Maak een nieuwe toepassing om een andere header configuratie of gebruikers toewijzing in te stellen voor een gedetailleerdere pad dan de toepassing die u hebt geconfigureerd. Configureer in de nieuwe toepassing de interne URL met het specifieke pad dat u nodig hebt en configureer vervolgens de specifieke headers die nodig zijn voor deze URL. Toepassings proxy komt altijd overeen met uw configuratie-instellingen voor de meest gedetailleerde set paden voor een toepassing. 

2. Selecteer **Azure Active Directory**   als **verificatie methode vooraf**. 
3. Wijs een test gebruiker toe door te navigeren naar **gebruikers en groepen** en de juiste gebruikers en groepen toe te wijzen. 
4. Open een browser en navigeer naar de **externe URL**   in de proxy-instellingen van de toepassing. 
5. Controleer of u verbinding kunt maken met de toepassing. Hoewel u verbinding kunt maken, hebt u nog geen toegang tot de app, omdat de headers niet zijn geconfigureerd. 

## <a name="configure-single-sign-on"></a>Eenmalige aanmelding configureren 
Voordat u aan de slag gaat met eenmalige aanmelding voor toepassingen die op Koptekst zijn gebaseerd, moet u al een toepassings proxy connector hebben geïnstalleerd en de connector heeft toegang tot de doel toepassingen. Als dat niet het geval is, volgt u de stappen in de [zelf studie: Azure AD-toepassingsproxy](application-proxy-add-on-premises-application.md)   u hier terug. 

1. Als uw toepassing wordt weer gegeven in de lijst met bedrijfs toepassingen, selecteert u deze en selecteert u **eenmalige aanmelding**. 
2. De modus voor eenmalige aanmelding instellen op **op basis van een koptekst**. 
3. In de basis configuratie, **Azure Active Directory**, standaard wordt geselecteerd. 
4. Selecteer het potlood bewerken in kopteksten om kopteksten te configureren voor verzen ding naar de toepassing. 
5. Selecteer **nieuwe koptekst toevoegen**. Geef een **naam** op voor de header en selecteer **kenmerk** of **trans formatie** en selecteer in de vervolg keuzelijst welke header uw toepassing nodig heeft.  
    - Zie [claims aanpassen-kenmerken](../develop/active-directory-saml-claims-customization.md#attributes)voor meer informatie over de lijst met beschik bare kenmerken. 
    - Zie claims aanpassen voor meer informatie over de lijst met beschik bare trans formatie [-claim transformaties](../develop/active-directory-saml-claims-customization.md#claim-transformations). 
    - U kunt ook een **groepskop tekst** toevoegen om alle groepen te verzenden waarvan een gebruiker deel uitmaakt, of de groepen die zijn toegewezen aan de toepassing als een koptekst. Zie voor meer informatie over het configureren van groepen als waarde: [groeps claims voor toepassingen configureren](../hybrid/how-to-connect-fed-group-claims.md#add-group-claims-to-tokens-for-saml-applications-using-sso-configuration). 
6. Selecteer Opslaan. 

## <a name="test-your-app"></a>Uw app testen 

Wanneer u al deze stappen hebt voltooid, moet uw app actief zijn en beschikbaar zijn. De app testen: 
1. Open een nieuw browser venster of een nieuwe persoonlijke browser om ervoor te zorgen dat eerder in de cache geplaatste headers worden gewist. Ga vervolgens naar de **externe URL**   van de proxy-instellingen van de toepassing.
2. Meld u aan met het test account dat u aan de app hebt toegewezen. Als u de toepassing kunt laden en aanmelden met behulp van SSO, bent u goed. 

## <a name="considerations"></a>Overwegingen

- Toepassings proxy wordt gebruikt voor externe toegang tot apps on-premises of op een privécloud. Toepassings proxy wordt niet aanbevolen voor het afhandelen van verkeer dat intern afkomstig is van het bedrijfs netwerk.
- Toegang tot op header gebaseerde verificatie toepassingen moet worden beperkt tot alleen verkeer van de connector of een andere toegelaten verificatie oplossing op basis van een header. Dit gebeurt meestal door het beperken van de netwerk toegang tot de toepassing met behulp van een firewall of IP-beperking op de toepassings server.

## <a name="next-steps"></a>Volgende stappen

- [Wat is eenmalige aanmelding?](what-is-single-sign-on.md)
- [Wat is toepassings proxy?](what-is-application-proxy.md)
- [Quickstartreeks over toepassingsbeheer](view-applications-portal.md)
