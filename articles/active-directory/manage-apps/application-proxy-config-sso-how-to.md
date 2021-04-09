---
title: Informatie over eenmalige aanmelding met een on-premises app met toepassings proxy
description: Informatie over eenmalige aanmelding met een on-premises app met toepassings proxy.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: kenwith
ms.reviewer: japere, asteen
ms.openlocfilehash: 10b722edbe8d70c92e617c78db3d2fb1d46da3a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99254101"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Eenmalige aanmelding configureren voor een toepassing Toepassingsproxy

Met eenmalige aanmelding (SSO) kunnen uw gebruikers toegang krijgen tot een toepassing zonder dat er meerdere keren worden geverifieerd. Hiermee kan de ene verificatie worden uitgevoerd in de Cloud, op basis van Azure Active Directory, en kan de service of connector de gebruiker imiteren om aanvullende verificatie uitdagingen van de toepassing te volt ooien.

## <a name="how-to-configure-single-sign-on"></a>Eenmalige aanmelding configureren
Als u SSO wilt configureren, moet u eerst controleren of uw toepassing is geconfigureerd voor verificatie vooraf via Azure Active Directory. Als u deze configuratie wilt uitvoeren, gaat u naar **Azure Active Directory**  - &gt; **Enter prise-toepassingen**  - &gt; **alle toepassingen** van  - &gt; de toepassings **- &gt; proxy** van uw toepassing. Op deze pagina ziet u het veld pre-verificatie en zorgt u ervoor dat is ingesteld op Azure Active Directory. 

Zie stap 4 van het [app-publicatie document](application-proxy-add-on-premises-application.md)voor meer informatie over de pre-verificatie methoden.

   ![Methode voor verificatie vooraf in Azure Portal](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Modus voor eenmalige aanmelding configureren voor toepassings proxy toepassingen
Configureer het specifieke type eenmalige aanmelding. De aanmeldings methoden worden geclassificeerd op basis van het type verificatie dat door de back-end-toepassing wordt gebruikt. App-proxy toepassingen ondersteunen drie typen aanmelding:

-   **Aanmelden op basis van wacht woorden**: aanmelden op basis van wacht woorden kan worden gebruikt voor elke toepassing die gebruikmaakt van gebruikers naam en wacht woord om zich aan te melden. Configuratie stappen zijn [een eenmalige aanmelding met een wacht woord configureren voor een toepassing in de Azure AD-galerie](configure-password-single-sign-on-non-gallery-applications.md).

-   **Geïntegreerde Windows-verificatie**: voor toepassingen die gebruikmaken van geïntegreerde Windows-authenticatie (IWA), wordt eenmalige aanmelding ingeschakeld via beperkte Kerberos-delegering (KCD). Deze methode biedt toepassings proxy connectors machtiging in Active Directory om gebruikers te imiteren en om namens hen tokens te verzenden en ontvangen. Meer informatie over het configureren van KCD vindt u in de [enkele Sign-On met KCD-documentatie](application-proxy-configure-single-sign-on-with-kcd.md).

-   **Aanmelden op basis** van een header: aanmelden op basis van een koptekst wordt gebruikt om mogelijkheden voor eenmalige aanmelding te bieden met behulp van http-headers. Zie [enkelvoudige aanmelding op basis](application-proxy-configure-single-sign-on-with-headers.md)van een koptekst voor meer informatie.

-   **Eenmalige aanmelding via SAML**: met eenmalige aanmelding via SAML verifieert Azure AD bij de toepassing met behulp van het Azure ad-account van de gebruiker. Azure AD geeft de aanmeldingsgegevens door aan de toepassing via een verbindingsprotocol. Met eenmalige aanmelding op basis van SAML kunt u gebruikers toewijzen aan specifieke toepassingsrollen op basis van de regels die u in uw SAML-claims definieert. Zie voor meer informatie over het instellen van eenmalige SAML-aanmelding [SAML voor eenmalige aanmelding met toepassings proxy](application-proxy-configure-single-sign-on-on-premises-apps.md).

U kunt elk van deze opties vinden door naar uw toepassing te gaan in ' bedrijfs toepassingen ' en de pagina voor **eenmalige aanmelding** te openen in het menu links. Als uw toepassing is gemaakt in de oude Portal, ziet u mogelijk niet al deze opties.

Op deze pagina ziet u ook een extra Sign-On optie: gekoppelde aanmelding. Deze optie wordt ook ondersteund door de toepassings proxy. Met deze optie wordt echter niet eenmalige aanmelding toegevoegd aan de toepassing. Die zei dat de toepassing mogelijk al een eenmalige aanmelding heeft geïmplementeerd met een andere service, zoals Active Directory Federation Services. 

Met deze optie kan een beheerder een koppeling maken naar een toepassing waar gebruikers zich voor het eerst bevinden bij het openen van de toepassing. Als er bijvoorbeeld een toepassing is die is geconfigureerd om gebruikers te verifiëren met behulp van Active Directory Federation Services 2,0, kan een beheerder de optie ' gekoppelde aanmelding ' gebruiken om een koppeling naar de app te maken in mijn apps.

## <a name="next-steps"></a>Volgende stappen
- [Wachtwoord kluis voor eenmalige aanmelding met toepassings proxy](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Beperkte Kerberos-overdracht voor eenmalige aanmelding met toepassings proxy](application-proxy-configure-single-sign-on-with-kcd.md)
- [Verificatie op basis van een header voor eenmalige aanmelding met toepassings proxy](application-proxy-configure-single-sign-on-with-headers.md) 
- [SAML voor eenmalige aanmelding met toepassings proxy](application-proxy-configure-single-sign-on-on-premises-apps.md).
