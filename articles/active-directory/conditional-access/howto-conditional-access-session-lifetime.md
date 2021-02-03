---
title: Beheer van verificatie sessies configureren-Azure Active Directory
description: De configuratie van de Azure AD-verificatie sessie aanpassen, waaronder de aanmeldings frequentie van de gebruiker en de persistentie van de browser sessie.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/23/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 205136437fa1a2a33b3b337f5cc9f2dde283faef
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491937"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>Verificatie sessie beheer met voorwaardelijke toegang configureren

In complexe implementaties moeten organisaties wellicht verificatie sessies beperken. Enkele scenario's zijn onder andere:

* Toegang tot bronnen vanaf een niet-beheerd of gedeeld apparaat
* Toegang tot gevoelige informatie van een extern netwerk
* Grote invloed op gebruikers
* Essentiële zakelijke toepassingen

Met besturings elementen voor voorwaardelijke toegang kunt u beleids regels maken die gericht zijn op specifieke use cases binnen uw organisatie zonder dat dit van invloed is op alle gebruikers.

Voordat u meer informatie over het configureren van het beleid krijgt, gaan we de standaard configuratie bekijken.

## <a name="user-sign-in-frequency"></a>Aanmeldings frequentie van gebruikers

Met de aanmeldings frequentie wordt de tijds periode gedefinieerd voordat een gebruiker wordt gevraagd om zich opnieuw aan te melden wanneer wordt geprobeerd om toegang te krijgen tot een bron.

De standaard configuratie van de Azure Active Directory (Azure AD) voor de aanmeldings frequentie van gebruikers is een rollend venster van 90 dagen. Het is vaak een goed idee om gebruikers te vragen om referenties, maar dit kan Backfire: gebruikers die zijn getraind om hun referenties op te geven zonder dat ze dit doen, kunnen ze per ongeluk door geven aan een kwaad aardige referentie prompt.

Het kan erop klinken dat een waarschuwing wordt weer gegeven om te voor komen dat een gebruiker zich opnieuw aanmeldt, op voor waarde dat de sessie wordt ingetrokken door een schending van de IT-beleids regels. Enkele voor beelden zijn (maar niet beperkt tot) het wijzigen van een wacht woord, een niet-compatibel apparaat of het uitschakelen van een account. U kunt [gebruikers sessies ook expliciet intrekken met behulp van Power shell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0&preserve-view=true). De standaard configuratie van Azure AD wordt niet meer door gebruikers gevraagd om hun referenties op te geven als de beveiligings postuur van hun sessies niet is gewijzigd.

De instelling voor de aanmeldings frequentie werkt met apps waarop OAUTH2 of OIDC-protocollen zijn geïmplementeerd volgens de standaarden. De meeste micro soft-apps voor Windows, Mac en Mobile met inbegrip van de volgende webtoepassingen voldoen aan de instelling.

- Word, Excel, Power Point online
- OneNote online
- Office.com
- Beheer Portal Microsoft 365
- Exchange Online
- Share point en OneDrive
- Web-client voor teams
- Dynamics CRM Online
- Azure Portal

De instelling voor de aanmeldings frequentie werkt ook met SAML-toepassingen, zolang ze hun eigen cookies niet hebben verwijderd en teruggestuurd worden naar Azure AD voor verificatie op regel matige basis.

### <a name="user-sign-in-frequency-and-multi-factor-authentication"></a>Aanmeldings frequentie van gebruikers en multi-factor Authentication

De aanmeldings frequentie is eerder alleen toegepast op de eerste factor Authentication op apparaten die zijn toegevoegd aan Azure AD, hybride Azure AD en geregistreerde Azure AD. Er is geen eenvoudige manier voor onze klanten om multi-factor Authentication (MFA) op deze apparaten opnieuw af te dwingen. Op basis van feedback van klanten is de aanmeldings frequentie ook van toepassing op MFA.

[![Aanmeldings frequentie en MFA](media/howto-conditional-access-session-lifetime/conditional-access-flow-chart-small.png)](media/howto-conditional-access-session-lifetime/conditional-access-flow-chart.png#lightbox)

### <a name="user-sign-in-frequency-and-device-identities"></a>Aanmeldings frequentie van gebruikers en apparaat-id's

Als u Azure AD hebt toegevoegd, hybride Azure AD is toegevoegd of geregistreerde Azure AD-apparaten, wanneer een gebruiker het apparaat ontgrendelt of zich interactief aanmeldt, voldoet deze gebeurtenis ook aan het beleid voor aanmeldings frequentie. In de volgende twee voor beelden is de aanmeldings frequentie voor gebruikers ingesteld op 1 uur:

Voorbeeld 1:

- Bij 00:00 meldt een gebruiker zich aan bij hun Windows 10 Azure AD-apparaat dat is toegevoegd aan een document dat is opgeslagen in share point online.
- De gebruiker blijft gedurende een uur aan hetzelfde document op het apparaat werken.
- Bij 01:00 wordt de gebruiker gevraagd zich opnieuw aan te melden op basis van de vereiste aanmeldings frequentie in het beleid voor voorwaardelijke toegang dat door de beheerder is geconfigureerd.

Voorbeeld 2:

- Bij 00:00 meldt een gebruiker zich aan bij hun Windows 10 Azure AD-apparaat dat is toegevoegd aan een document dat is opgeslagen in share point online.
- Bij 00:30 wordt de gebruiker opgehaald en wordt een verbreekt de vergren deling van het apparaat.
- Bij 00:45 wordt de gebruiker teruggegeven van het verbreken en wordt het apparaat ontgrendeld.
- Bij 01:45 wordt de gebruiker gevraagd zich opnieuw aan te melden op basis van de vereiste aanmeldings frequentie in het beleid voor voorwaardelijke toegang dat door de beheerder is geconfigureerd sinds de laatste aanmelding is opgetreden op 00:45.

## <a name="persistence-of-browsing-sessions"></a>Persistentie van Browse sessies

Met een permanente browser sessie kunnen gebruikers aangemeld blijven nadat ze het browser venster hebben gesloten en opnieuw hebben geopend.

Met de Azure AD-standaard voor browser sessie persistent kunnen gebruikers op persoonlijke apparaten kiezen of ze de sessie willen blijven door een ' aangemeld blijven? ' weer te geven. vragen na geslaagde verificatie. Als de browser persistentie is geconfigureerd in AD FS met behulp van de richt lijnen in het artikel [AD FS enkele Sign-On instellingen](/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
), voldoen wij aan dat beleid en blijven ook de Azure AD-sessie behouden. U kunt ook configureren of gebruikers in uw Tenant de ' aangemeld blijven? ' moeten zien. prompt door de juiste instelling te wijzigen in het deel venster huis stijl in Azure Portal met behulp van de richt lijnen in het artikel [uw Azure AD-aanmeldings pagina aanpassen](../fundamentals/customize-branding.md).

## <a name="configuring-authentication-session-controls"></a>Verificatie sessie besturings elementen configureren

Voorwaardelijke toegang is een Azure AD Premium mogelijkheid en vereist een Premium-licentie. Zie [Wat is voorwaardelijke toegang in azure Active Directory?](overview.md#license-requirements) voor meer informatie over voorwaardelijke toegang.

> [!WARNING]
> Als u de configuratie van de [Configureer bare levens duur van tokens](../develop/active-directory-configurable-token-lifetimes.md) gebruikt die momenteel beschikbaar is in de preview-versie, is het niet mogelijk om twee verschillende beleids regels te maken voor dezelfde combi natie van gebruiker of app: een met deze functie en een andere met een Configureer bare levens duur van tokens. Micro soft heeft de Configureer bare levens duur van het token voor vernieuwing-en sessie token levensduur op 30 januari 2021 buiten gebruik gesteld en vervangen door de beheer functie voor de verificatie van voorwaardelijke toegang.  
>
> Voordat u de aanmeldings frequentie inschakelt, moet u ervoor zorgen dat andere instellingen voor opnieuw verifiëren zijn uitgeschakeld in uw Tenant. Als ' MFA op vertrouwde apparaten onthouden ' is ingeschakeld, moet u deze uitschakelen voordat u de aanmeldings frequentie gebruikt. Als u deze twee instellingen samen gebruikt, kan dit ertoe leiden dat gebruikers onverwachts worden gevraagd. Voor meer informatie over het opnieuw verifiëren van de verificatie en de levens duur van de sessie raadpleegt u het artikel, [Optimaliseer de prompts voor herverificatie en de levens duur van sessies voor Azure AD multi-factor Authentication](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

### <a name="policy-1-sign-in-frequency-control"></a>Beleid 1: regel voor aanmeldings frequentie

1. Nieuw beleid maken
1. Kies alle vereiste voor waarden voor de omgeving van de klant, inclusief de doel-Cloud-apps.

   > [!NOTE]
   > Het is raadzaam om een gelijke frequentie voor verificatie prompts in te stellen voor Key Microsoft Office-apps, zoals Exchange Online en share point online voor een optimale gebruikers ervaring.

1. Ga naar de **Access controls**-  >  **sessie** en klik op **aanmeldings frequentie**
1. Voer de vereiste waarde van dagen en uren in het eerste tekstvak in
1. Een waarde van **uren** of **dagen** selecteren in de vervolg keuzelijst
1. Uw beleid opslaan

![Beleid voor voorwaardelijke toegang geconfigureerd voor aanmeldings frequentie](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

Op Azure AD geregistreerde Windows-apparaten aanmelden bij het apparaat wordt beschouwd als een prompt. Als u bijvoorbeeld de aanmeldings frequentie hebt ingesteld op 24 uur voor Office-apps, voldoen gebruikers op Azure AD geregistreerde Windows-apparaten met het aanmeldings frequentie beleid door zich aan te melden bij het apparaat en wordt er niet opnieuw gevraagd om de Office-apps te openen.

### <a name="policy-2-persistent-browser-session"></a>Beleid 2: permanente browser sessie

1. Nieuw beleid maken
1. Kies alle vereiste voor waarden.

   > [!NOTE]
   > Houd er rekening mee dat dit besturings element vereist dat alle Cloud-apps als voor waarde worden gekozen. De browser sessie persistentie wordt bepaald door het verificatie sessie token. Alle tabbladen in een browser sessie delen één sessie token en daarom moeten alle gebruikers de persistentie status delen.

1. Ga naar de **Access controls**-  >  **sessie** en klik op **permanente browser sessie**
1. Selecteer een waarde in de vervolg keuzelijst
1. Beleid opslaan

![Beleid voor voorwaardelijke toegang geconfigureerd voor permanente browser](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Met de permanente configuratie van de browser sessie in azure AD voorwaardelijke toegang wordt de ' aangemeld blijven? ' overschreven. in het deel venster huis stijl van het bedrijf in de Azure Portal voor dezelfde gebruiker als u beide beleids regels hebt geconfigureerd.

## <a name="validation"></a>Validatie

Met het What-If-hulp programma kunt u een aanmelding van de gebruiker naar de doel toepassing en andere voor waarden simuleren op basis van de manier waarop u het beleid hebt geconfigureerd. De besturings elementen voor verificatie sessie beheer worden weer gegeven in het resultaat van het hulp programma.

![Resultaten van What If tool voor voorwaardelijke toegang](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Implementatie van beleid

Om ervoor te zorgen dat uw beleid werkt zoals verwacht, is het aanbevolen best practice het te testen voordat u het in productie neemt. In het ideale geval gebruikt u een test Tenant om te controleren of uw nieuwe beleid werkt zoals bedoeld. Zie het artikel [een implementatie van voorwaardelijke toegang plannen](plan-conditional-access.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Als u klaar bent voor het configureren van beleid voor voorwaardelijke toegang voor uw omgeving, raadpleegt u het artikel [een implementatie van voorwaardelijke toegang plannen](plan-conditional-access.md).