---
title: Toepassings typen in v 1.0 | Azure
description: Beschrijft de typen apps en scenario's die worden ondersteund door het Azure Active Directory v 2.0-eind punt.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 5ff2858dd8b91ba036c517cbff07be96a729ef8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88116441"
---
# <a name="application-types-in-v10"></a>Toepassings typen in v 1.0

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory (Azure AD) ondersteunt verificatie voor verschillende moderne app-architecturen, die allemaal zijn gebaseerd op gestandaardiseerde protocollen OAuth 2,0 of OpenID Connect Connect.

In het volgende diagram ziet u de scenario's en toepassings typen en hoe u verschillende onderdelen kunt toevoegen:

![App-soorten en scenario’s](./media/authentication-scenarios/application-types-scenarios.png)

Dit zijn de vijf scenario's voor de primaire toepassing die worden ondersteund door Azure AD:

- **[Toepassing met één pagina (Spa)](single-page-application.md)**: een gebruiker moet zich aanmelden bij een toepassing met één pagina die is beveiligd met Azure AD.
- **[Webbrowser voor webtoepassing](web-app.md)**: een gebruiker moet zich aanmelden bij een webtoepassing die wordt beveiligd door Azure AD.
- **[Systeem eigen toepassing op Web-API](native-app.md)**: een systeem eigen toepassing die wordt uitgevoerd op een telefoon, tablet of PC moet een gebruiker verifiëren om bronnen te verkrijgen van een web-API die wordt beveiligd door Azure AD.
- Web **[Application to Web API](web-api.md)**: een webtoepassing moet resources ophalen van een web-API die wordt beveiligd door Azure AD.
- **[Daemon of server toepassing op Web-API](service-to-service.md)**: voor een daemon-toepassing of een server toepassing zonder webinterface moeten bronnen worden opgehaald van een web-API die wordt beveiligd door Azure AD.

Volg de koppelingen om meer te weten te komen over elk type app en inzicht te krijgen in de scenario's op hoog niveau voordat u met de code aan de slag gaat. U kunt ook meer te weten komen over de verschillen die u moet weten wanneer u een bepaalde app schrijft die werkt met het eind punt van de v 1.0 of van het v 2.0-eind punt.

> [!NOTE]
> Het v 2.0-eind punt biedt geen ondersteuning voor alle Azure AD-scenario's en-functies. Als u wilt bepalen of u het v 2.0-eind punt moet gebruiken, leest u over [v 2.0-beperkingen](./azure-ad-endpoint-comparison.md?bc=%2fazure%2factive-directory%2fazuread-dev%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fazuread-dev%2ftoc.json).

U kunt elk van de hier beschreven apps en scenario's ontwikkelen met behulp van verschillende talen en platforms. Ze worden allemaal ondersteund door de volledige code voorbeelden die beschikbaar zijn in de code samples Guide: [v 1.0 code samples by scenario](sample-v1-code.md) en [v 2.0 code samples by scenario](../develop/sample-v2-code.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json). U kunt de code voorbeelden ook rechtstreeks vanuit de bijbehorende [github-voorbeeld opslagplaatsen](https://github.com/Azure-Samples?q=active-directory)downloaden.

Als uw toepassing bovendien een specifiek onderdeel of segment van een end-to-end-scenario nodig heeft, kan de functionaliteit in de meeste gevallen onafhankelijk worden toegevoegd. Als u bijvoorbeeld een systeem eigen toepassing hebt die een web-API aanroept, kunt u eenvoudig een webtoepassing toevoegen die ook de Web-API aanroept.

## <a name="app-registration"></a>App-registratie

### <a name="registering-an-app-that-uses-the-azure-ad-v10-endpoint"></a>Een app registreren die gebruikmaakt van het Azure AD v 1.0-eind punt

Elke toepassing die de authenticatie van de verificatie voor Azure AD uitbrengt, moet in een directory worden geregistreerd. Deze stap omvat het vertellen van Azure AD over uw toepassing, met inbegrip van de URL waar deze zich bevindt, de URL voor het verzenden van antwoorden na verificatie, de URI voor het identificeren van uw toepassing en meer. Deze informatie is vereist om een paar belang rijke redenen:

* Azure AD moet met de toepassing communiceren bij het verwerken van aanmeldings-of uitwisselings tokens. De informatie die wordt door gegeven tussen Azure AD en de toepassing bestaat uit het volgende:
  
  * **URI van de toepassings-id** : de id voor een toepassing. Deze waarde wordt tijdens de verificatie naar Azure AD verzonden om aan te geven voor welke toepassing de aanroeper een token wil. Daarnaast is deze waarde opgenomen in het token, zodat de toepassing weet dat het beoogde doel was.
  * **Antwoord-URL** en **omleidings-URI** : voor een web-API of webtoepassing is de antwoord-URL de locatie waar Azure AD de verificatie respons verzendt, inclusief een token als de verificatie is geslaagd. Voor een systeem eigen toepassing is de omleidings-URI een unieke id waarnaar de gebruikers agent in een OAuth 2,0-aanvraag wordt doorgestuurd door Azure AD.
  * **Toepassings-id** : de id voor een toepassing, die door Azure AD wordt gegenereerd wanneer de toepassing wordt geregistreerd. Bij het aanvragen van een autorisatie code of token worden de toepassings-ID en-sleutel verzonden naar Azure AD tijdens de verificatie.
  * **Sleutel** : de sleutel die wordt verzonden samen met een toepassings-id bij de verificatie bij Azure AD om een web-API aan te roepen.
* Azure AD moet ervoor zorgen dat de toepassing beschikt over de vereiste machtigingen voor toegang tot uw Directory gegevens, andere toepassingen in uw organisatie, enzovoort.

Meer informatie over hoe u [een app kunt registreren](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="single-tenant-and-multi-tenant-apps"></a>Apps met één Tenant en meerdere tenants

Het inrichten wordt duidelijker wanneer u begrijpt dat er twee categorieën toepassingen zijn die kunnen worden ontwikkeld en geïntegreerd met Azure AD:

* **Toepassing met één Tenant** : Eén Tenant toepassing is bedoeld voor gebruik in één organisatie. Dit zijn doorgaans LoB-toepassingen (line-of-Business) die door een bedrijfs ontwikkelaar zijn geschreven. Eén Tenant toepassing hoeft alleen te worden geopend door gebruikers in de ene map en daarom hoeft deze niet te worden ingericht in één directory. Deze toepassingen worden meestal geregistreerd door een ontwikkelaar in de organisatie.
* **Multi tenant-toepassing** : een toepassing met meerdere tenants is bedoeld voor gebruik in veel organisaties, niet slechts op één organisatie. Dit zijn normaliter Software-as-a-Service-toepassingen (SaaS), geschreven door een onafhankelijke softwareleverancier (ISV). Multi tenant-toepassingen moeten worden ingericht in elke map waar ze worden gebruikt, waarvoor de gebruiker of de beheerder toestemming nodig heeft om deze te registreren. Het toestemmingsproces begint wanneer een toepassing in de map is geregistreerd en toegang verkrijgt tot de Graph-API of wellicht zelfs een andere web-API. Wanneer een gebruiker of beheerder van een andere organisatie zich aanmeldt om de toepassing te gebruiken, wordt er een dialoog venster weer gegeven met de machtigingen die de toepassing nodig heeft. De gebruiker of beheerder kan vervolgens toestemming geven voor de toepassing, die de toepassing toegang geeft tot de vermelde gegevens en de toepassing vervolgens registreert in hun Directory. Zie [overzicht van het toestemming raamwerk](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)voor meer informatie.

### <a name="additional-considerations-when-developing-single-tenant-or-multi-tenant-apps"></a>Aanvullende overwegingen bij het ontwikkelen van één Tenant of multi tenant-apps

Er zijn enkele aanvullende overwegingen bij het ontwikkelen van een toepassing met meerdere tenants in plaats van één Tenant toepassing. Als u uw toepassing bijvoorbeeld beschikbaar maakt voor gebruikers in meerdere directory's, hebt u een mechanisme nodig om te bepalen in welke Tenant ze zich bevinden. Een enkele Tenant toepassing hoeft alleen in een eigen map te zoeken voor een gebruiker, terwijl een multi tenant-toepassing een specifieke gebruiker moet identificeren uit alle directory's in azure AD. Voor het uitvoeren van deze taak biedt Azure AD een gemeen schappelijk verificatie-eind punt waarbij een multi tenant-toepassing kan worden gebruikt om aanmeldings aanvragen te sturen, in plaats van een Tenant-specifiek eind punt. Dit eind punt is `https://login.microsoftonline.com/common` voor alle directory's in azure AD, terwijl een Tenant-specifiek eind punt mogelijk is `https://login.microsoftonline.com/contoso.onmicrosoft.com` . Het gemeen schappelijke eind punt is vooral belang rijk bij het ontwikkelen van uw toepassing, omdat u de benodigde logica nodig hebt voor het verwerken van meerdere tenants tijdens het aanmelden, afmelden en validatie van tokens.

Als u momenteel één Tenant toepassing ontwikkelt, maar deze beschikbaar wilt maken voor veel organisaties, kunt u eenvoudig wijzigingen aanbrengen in de toepassing en de configuratie in azure AD om multi-tenant compatibel te maken. Bovendien gebruikt Azure AD dezelfde ondertekeningssleutel voor alle tokens in alle directory's, ongeacht of u verificatie in één Tenant of multi tenant-toepassing opgeeft.

Elk scenario dat in dit document wordt vermeld, bevat een Subsectie waarin de inrichtings vereisten worden beschreven. Zie [toepassingen integreren met Azure Active Directory](../develop/single-and-multi-tenant-apps.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) voor meer informatie over uitgebreide informatie over het inrichten van een toepassing in azure AD en de verschillen tussen toepassingen met één of meerdere tenants. Blijf lezen om inzicht te krijgen in de algemene toepassings scenario's in azure AD.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [basis beginselen](v1-authentication-scenarios.md) van Azure AD-verificatie