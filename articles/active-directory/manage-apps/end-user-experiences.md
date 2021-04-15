---
title: Ervaringen van eindgebruikers voor toepassingen - Azure Active Directory
description: Azure Active Directory (Azure AD) biedt verschillende aanpasbare manieren om toepassingen te implementeren voor eindgebruikers in uw organisatie.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: iangithinji
ms.reviewer: arvindh
ms.openlocfilehash: c555899a65a5e8cf4c8fcc6214e4dcbda3931f08
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374230"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Ervaringen van eindgebruikers voor toepassingen in Azure Active Directory

Azure Active Directory (Azure AD) biedt verschillende aanpasbare manieren om toepassingen te implementeren voor eindgebruikers in uw organisatie:

* Azure AD-Mijn apps
* Microsoft 365 starten van toepassingen
* Directe aanmelding bij federatieve apps
* Dieptekoppelingen naar federatieve apps, op basis van wachtwoorden, of bestaande apps

Welke methode(s) u in uw organisatie wilt implementeren, is uw keuze.

## <a name="azure-ad-my-apps"></a>Azure AD-Mijn apps

Mijn apps is een webportal waarmee een eindgebruiker met een organisatieaccount in Azure Active Directory toepassingen kan weergeven en starten waarvoor ze toegang hebben gekregen door de https://myapps.microsoft.com Azure AD-beheerder. Als u een eindgebruiker bent met [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/), kunt u ook selfservice groepsbeheermogelijkheden gebruiken via Mijn apps.

Standaard worden alle toepassingen samen op één pagina weergegeven. Maar u kunt verzamelingen gebruiken om gerelateerde toepassingen te groeperen en op een afzonderlijk tabblad te presenteren, zodat ze gemakkelijker te vinden zijn. U kunt bijvoorbeeld verzamelingen gebruiken om logische groeperingen van toepassingen te maken voor specifieke functierollen, taken, projecten, en meer. Zie Verzamelingen [maken in de Mijn apps portal voor meer informatie.](access-panel-collections.md) 

Mijn apps is gescheiden van de Azure Portal en vereist niet dat gebruikers een Azure-abonnement of Microsoft 365 hebben.

Zie voor meer informatie over Azure AD Mijn apps de [inleiding tot Mijn apps](../user-help/my-apps-portal-end-user-access.md).

## <a name="microsoft-365-application-launcher"></a>Microsoft 365 starten van toepassingen

Voor organisaties die een Microsoft 365 geïmplementeerd, worden toepassingen die zijn toegewezen aan gebruikers via Azure AD ook weergegeven in de Office 365-portal op [https://portal.office.com/myapps](https://portal.office.com/myapps) . Dit maakt het eenvoudig en handig voor gebruikers in een organisatie om hun apps te starten zonder een tweede portal te gebruiken. Dit is de aanbevolen oplossing voor het starten van apps voor organisaties die Microsoft 365.

Zie Uw app laten verschijnen in het start programma voor Office 365-apps voor meer informatie over het start programma voor [Office 365-toepassingen.](/previous-versions/office/office-365-api/)

## <a name="direct-sign-on-to-federated-apps"></a>Directe aanmelding bij federatieve apps

De meeste federatieve toepassingen die ondersteuning bieden voor SAML 2.0, WS-Federation of OpenID Connect, bieden ook ondersteuning voor de mogelijkheid voor gebruikers om bij de toepassing te beginnen en zich vervolgens via Azure AD aan te melden via automatische omleiding of door te klikken op een koppeling om zich aan te melden. Dit wordt ook wel een door de serviceprovider geïnitieerde aanmelding genoemd. De meeste federatief toepassingen in de Azure AD-toepassingsgalerie ondersteunen dit (zie de documentatie die is gekoppeld vanuit de configuratiewizard voor een aantal aanmeldingen van de app in de Azure Portal voor meer informatie).

## <a name="direct-sign-on-links"></a>Koppelingen voor directe aanmelding

Azure AD biedt ook ondersteuning voor directe koppelingen naar afzonderlijke toepassingen die ondersteuning bieden voor een aanmelding op basis van een wachtwoord, een gekoppelde eengemaakte aanmelding en een vorm van federatief een aanmelding.

Deze koppelingen zijn speciaal samengestelde URL's die een gebruiker via het Azure AD-aanmeldingsproces voor een specifieke toepassing verzenden zonder dat de gebruiker deze moet starten vanuit Azure AD Mijn apps of Microsoft 365. Deze **URL's voor gebruikerstoegang** vindt u onder de eigenschappen van de beschikbare bedrijfstoepassingen. Selecteer **Azure Active Directory** > **Bedrijfstoepassingen** in de Azure Portal. Selecteer de toepassing en selecteer vervolgens **Eigenschappen.**

![Voorbeeld van de URL voor gebruikerstoegang in Twitter-eigenschappen](media/end-user-experiences/direct-sign-on-link.png)

Deze koppelingen kunnen overal worden gekopieerd en gekopieerd waar u een aanmeldingskoppeling naar de geselecteerde toepassing wilt verstrekken. Dit kan een e-mailbericht zijn of in een aangepaste webportal die u hebt ingesteld voor toegang tot gebruikerstoepassing. Hier is een voorbeeld van een directe AZURE AD-URL voor een aanmelding voor Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Net als bij organisatiespecifieke URL's voor Mijn apps kunt u deze URL verder aanpassen door een van de actieve of geverifieerde domeinen voor uw directory toe te voegen na myapps.microsoft.com *domein.* Dit zorgt ervoor dat elke huisstijl van een organisatie onmiddellijk op de aanmeldingspagina wordt geladen zonder dat de gebruiker eerst de gebruikers-id hoeft in te voeren:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Wanneer een geautoriseerde gebruiker op een van deze toepassingsspecifieke koppelingen klikt, zien ze eerst de aanmeldingspagina van hun organisatie (ervan uitgaande dat ze nog niet zijn aangemeld) en nadat de aanmelding is omgeleid naar de app zonder eerst bij Mijn apps te stoppen. Als aan de gebruiker vereisten voor toegang tot de toepassing ontbreken, zoals de browserextensie met één teken op basis van een wachtwoord, wordt de gebruiker via de koppeling gevraagd de ontbrekende extensie te installeren. De koppelings-URL blijft ook constant als de configuratie voor een een aanmelding voor de toepassing wordt gewijzigd.

Deze koppelingen gebruiken dezelfde mechanismen voor toegangsbeheer als Mijn apps en Microsoft 365, en alleen de gebruikers of groepen die zijn toegewezen aan de toepassing in de Azure Portal kunnen zich met succes verifiëren. Elke gebruiker die niet gemachtigd is, ziet echter een bericht waarin wordt uitgelegd dat ze geen toegang hebben gekregen en een koppeling krijgen om Mijn apps te laden om beschikbare toepassingen weer te geven waarvoor ze wel toegang hebben.

## <a name="next-steps"></a>Volgende stappen

* [Quickstartreeks over toepassingsbeheer](view-applications-portal.md)
* [Wat is een aanmelding?](what-is-single-sign-on.md)
* [Aan de slag Azure Active Directory integreren met toepassingen](plan-an-application-integration.md)