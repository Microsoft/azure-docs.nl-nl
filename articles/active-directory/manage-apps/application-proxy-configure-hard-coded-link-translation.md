---
title: Koppelingen en Url's vertalen Azure AD-app proxy | Microsoft Docs
description: Meer informatie over het omleiden van in code vastgelegde koppelingen voor apps die zijn gepubliceerd met Azure AD-toepassings proxy.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/15/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb1935242790333a91b47ccecc19d934b8145085
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101688328"
---
# <a name="redirect-hard-coded-links-for-apps-published-with-azure-ad-application-proxy"></a>In code vastgelegde koppelingen omleiden voor apps die zijn gepubliceerd met Azure AD-toepassingsproxy

Azure AD-toepassingsproxy maakt uw on-premises apps beschikbaar voor gebruikers die zich op afstand of op hun eigen apparaten bevinden. Sommige apps zijn echter ontwikkeld met lokale koppelingen die zijn Inge sloten in de HTML. Deze koppelingen werken niet naar behoren wanneer de app op afstand wordt gebruikt. Wanneer u meerdere on-premises toepassingen naar elkaar wijst, verwachten uw gebruikers dat de koppelingen blijven werken wanneer ze niet op kantoor zijn. 

De beste manier om ervoor te zorgen dat koppelingen werken die zowel binnen als buiten uw bedrijfs netwerk zijn, is door de externe Url's van uw apps zo te configureren dat deze hetzelfde zijn als de interne Url's. Gebruik [aangepaste domeinen](application-proxy-configure-custom-domain.md) om uw externe url's te configureren voor de naam van uw bedrijfs domein in plaats van het standaard domein voor de toepassings proxy.


Als u geen aangepaste domeinen in uw Tenant kunt gebruiken, zijn er enkele andere opties voor het bieden van deze functionaliteit. Al deze zijn ook compatibel met aangepaste domeinen en elkaar, zodat u zo nodig aangepaste domeinen en andere oplossingen kunt configureren.

> [!NOTE]
> Koppelings vertalingen worden niet ondersteund voor in code vastgelegde interne Url's die via Java script worden gegenereerd.

**Optie 1: micro soft Edge gebruiken** : deze oplossing is alleen van toepassing als u van plan bent om aan te bevelen of vereist dat gebruikers toegang tot de toepassing krijgen via de micro soft Edge-browser. Alle gepubliceerde Url's worden verwerkt. 

**Optie 2: de MyApps-extensie gebruiken** : voor deze oplossing moeten gebruikers een browser extensie aan client zijde installeren, maar alle gepubliceerde url's worden verwerkt en werkt met de meeste populaire browsers. 

**Optie 3: gebruik de instelling voor het converteren van koppelingen** : dit is een instelling aan de beheerder zijde die onzichtbaar is voor gebruikers. Url's worden echter alleen verwerkt in HTML en CSS.   

Deze drie functies zorgen ervoor dat uw koppelingen werken, ongeacht waar uw gebruikers zich bevinden. Wanneer u apps hebt die rechtstreeks naar interne eind punten of poorten verwijzen, kunt u deze interne Url's toewijzen aan de Url's van de gepubliceerde externe toepassings proxy. 

 
> [!NOTE]
> De laatste optie is alleen voor tenants die om welke reden dan ook geen aangepaste domeinen kunnen gebruiken om dezelfde interne en externe Url's voor hun apps te hebben. Voordat u deze functie inschakelt, moet u controleren of [aangepaste domeinen in Azure AD-toepassingsproxy](application-proxy-configure-custom-domain.md) voor u kunnen werken. 
> 
> Als de toepassing die u wilt configureren met koppelings omzetting share point is, raadpleegt u [alternatieve toegangs toewijzingen voor share point 2013 configureren](/SharePoint/administration/configure-alternate-access-mappings) voor een andere benadering voor het toewijzen van koppelingen. 

 
### <a name="option-1-microsoft-edge-integration"></a>Optie 1: integratie met micro soft Edge 

U kunt micro soft Edge gebruiken om uw toepassing en inhoud verder te beveiligen. Als u deze oplossing wilt gebruiken, moet u gebruikers verplichten om toegang te krijgen tot de toepassing via micro soft Edge. Alle interne Url's die zijn gepubliceerd met toepassings proxy, worden herkend door Edge en omgeleid naar de bijbehorende externe URL. Dit zorgt ervoor dat alle hardcoded interne Url's werken, en als een gebruiker naar de browser gaat en rechtstreeks de interne URL typt, werkt deze zelfs als de gebruiker extern is.  

Zie voor meer informatie, inclusief hoe u deze optie configureert, de [webtoegang beheren door gebruik te maken van Edge voor IOS en Android met Microsoft intune](/mem/intune/apps/manage-microsoft-edge) documentatie.  

### <a name="option-2-myapps-browser-extension"></a>Optie 2: browser uitbreiding MyApps 

Met de browser uitbreiding MyApps worden alle interne Url's die zijn gepubliceerd met toepassings proxy, herkend door de extensie en omgeleid naar de bijbehorende externe URL. Dit zorgt ervoor dat alle hardcoded interne Url's werken en als een gebruiker naar de adres balk van de browser gaat en de interne URL rechtstreeks typt, werkt deze zelfs als de gebruiker extern is.  

Als u deze functie wilt gebruiken, moet de gebruiker de extensie downloaden en zijn aangemeld. Er is geen andere configuratie nodig voor beheerders of gebruikers. 

Raadpleeg de documentatie van de [MyApps-browser uitbreiding](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) voor meer informatie over het configureren van deze optie.

> [!NOTE]
> De MyApps browser uitbreiding biedt geen ondersteuning voor koppelings vertalingen voor Url's voor joker tekens.

### <a name="option-3-link-translation-setting"></a>Optie 3: instelling voor het omzetten van koppelingen 

Wanneer koppelings vertalingen is ingeschakeld, zoekt de Application proxy-service in HTML en CSS naar gepubliceerde interne koppelingen en worden ze vertaald zodat uw gebruikers een ononderbroken ervaring krijgen. Het gebruik van de MyApps-browser uitbreiding verdient de voor keur voor de instelling voor de conversie van koppelingen, omdat het een betere ervaring voor gebruikers biedt.

> [!NOTE]
> Als u optie 2 of 3 gebruikt, moet slechts één van deze tegelijk worden ingeschakeld.

## <a name="how-link-translation-works"></a>De werking van koppelings vertalingen

Na verificatie, wanneer de proxy server de toepassings gegevens door gegeven aan de gebruiker, scant de toepassings proxy de toepassing op in code vastgelegde koppelingen en vervangt deze door hun respectieve, gepubliceerde externe Url's.

Toepassings proxy gaat ervan uit dat toepassingen zijn gecodeerd in UTF-8. Als dat niet het geval is, geeft u het type code ring op in een HTTP-antwoord header, zoals `Content-Type:text/html;charset=utf-8` .

### <a name="which-links-are-affected"></a>Welke koppelingen worden getroffen?

De functie voor het omzetten van koppelingen zoekt alleen naar koppelingen in code tags in de hoofd tekst van een app. Toepassings proxy heeft een afzonderlijke functie voor het omzetten van cookies of Url's in kopteksten. 

Er zijn twee algemene typen interne koppelingen in on-premises toepassingen:

- **Relatieve interne koppelingen** die verwijzen naar een gedeelde bron in een lokale bestands structuur, zoals `/claims/claims.html` . Deze koppelingen werken automatisch in apps die zijn gepubliceerd via toepassings proxy en blijven werken met of zonder koppelings conversie. 
- **Hardcoded interne koppelingen** naar andere on-premises apps zoals `http://expenses` of gepubliceerde bestanden zoals `http://expenses/logo.jpg` . De functie voor het vertalen van koppelingen werkt op in code vastgelegde interne koppelingen en wijzigt ze zodat ze verwijzen naar de externe Url's die externe gebruikers nodig hebben om door te gaan.

De volledige lijst met kenmerken in HTML-code tags die toepassings proxy ondersteunt koppelings vertalingen voor zijn onder andere:
* a (HREF)
* Audio (SRC)
* basis (HREF)
* knop (formaction)
* div (Data-background, Style, data-src)
* insluiten (SRC)
* formulier (actie)
* frame (SRC)
* Head (profiel)
* HTML (manifest)
* iframe (longdesc, src)
* img (longdesc, src)
* invoer (formaction, src, value)
* koppeling (HREF)
* menu item (pictogram)
* Meta (inhoud)
* object (archief, gegevens, code basis)
* script (SRC)
* Bron (SRC)
* bijhouden (SRC)
* Video (src, poster)

Daarnaast wordt het URL-kenmerk ook omgezet in CSS.

### <a name="how-do-apps-link-to-each-other"></a>Hoe maken apps verbinding met elkaar?

Koppelings vertalingen zijn ingeschakeld voor elke toepassing, zodat u de gebruikers ervaring op het niveau per app kunt controleren. Schakel koppelings vertalingen voor een app in als u wilt dat de koppelingen *van* die app worden vertaald, en niet *naar die app* . 

Stel bijvoorbeeld dat u drie toepassingen hebt gepubliceerd via een toepassings proxy die helemaal aan elkaar is gekoppeld: voor delen, kosten en reizen. Er is een vierde app, feedback die niet is gepubliceerd via de toepassings proxy.

Wanneer u koppelings vertalingen inschakelt voor de app voor delen, worden de koppelingen naar onkosten en reizen omgeleid naar de externe Url's voor die apps, maar de koppeling naar de feedback wordt niet omgeleid omdat er geen externe URL is. Koppelingen van onkosten en reizen terug naar voor delen werken niet, omdat koppelings omzetting niet is ingeschakeld voor deze twee apps.

![Koppelingen van voor delen naar andere apps wanneer koppelings vertalingen is ingeschakeld](./media/application-proxy-configure-hard-coded-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>Welke koppelingen worden niet vertaald?

Sommige koppelingen worden niet vertaald om de prestaties en beveiliging te verbeteren:

- Koppelingen niet in code tags. 
- Koppelingen niet in HTML of CSS. 
- Koppelingen in URL-gecodeerde indeling.
- Interne koppelingen die vanuit andere Program ma's worden geopend. Koppelingen die worden verzonden via e-mail of chat bericht, of die zijn opgenomen in andere documenten, worden niet vertaald. De gebruikers moeten weten dat ze naar de externe URL moeten gaan.

Als u een van deze twee scenario's wilt ondersteunen, moet u dezelfde interne en externe Url's gebruiken in plaats van koppelings vertalingen.  

## <a name="enable-link-translation"></a>Koppelings vertalingen inschakelen

Aan de slag met koppelings vertalingen is net zo eenvoudig als het klikken op een knop:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als beheerder.
2. Ga naar **Azure Active Directory**  >  **Enter prise-toepassingen**  >  **alle toepassingen** > Selecteer de app die u wilt beheren > **toepassings proxy**.
3. Zet **url's in de hoofd tekst van de toepassing om in** **Ja**.

   ![Selecteer Ja om de Url's in de hoofd tekst van de toepassing te vertalen](./media/application-proxy-configure-hard-coded-link-translation/select_yes.png)
4. Klik op **Opslaan** om uw wijzigingen toe te passen.

Wanneer uw gebruikers nu toegang tot deze toepassing hebben, scant de proxy automatisch op interne Url's die zijn gepubliceerd via de toepassings proxy van uw Tenant.

## <a name="send-feedback"></a>Feedback verzenden

We willen dat deze functie geschikt is voor al uw apps. We zoeken meer dan 30 Tags in HTML en CSS. Als u een voor beeld hebt van gegenereerde koppelingen die niet worden vertaald, verzendt u een code fragment naar de feedback van de [toepassings proxy](mailto:aadapfeedback@microsoft.com). 

## <a name="next-steps"></a>Volgende stappen
[Aangepaste domeinen met Azure AD-toepassingsproxy gebruiken](application-proxy-configure-custom-domain.md) om dezelfde interne en externe URL te hebben

[Alternatieve toegangs toewijzingen configureren voor share point 2013](/SharePoint/administration/configure-alternate-access-mappings)
