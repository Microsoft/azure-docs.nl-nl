---
title: Beheerders machtigingen voor toepassings beheer delegeren-Azure AD | Microsoft Docs
description: Machtigingen verlenen voor toegang tot beheer van toepassingen in Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1e8a0f1919da125a571429e1efff06589c7e85a
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103466708"
---
# <a name="delegate-app-registration-permissions-in-azure-active-directory"></a>Machtigingen voor app-registratie in Azure Active Directory delegeren

In dit artikel wordt beschreven hoe u machtigingen kunt gebruiken die worden verleend door aangepaste rollen in Azure Active Directory (Azure AD) om uw behoeften op het gebied van toepassings beheer te verhelpen. In azure AD kunt u de machtigingen voor het maken en beheren van toepassingen op de volgende manieren delegeren:

- [Beperken wie toepassingen kan maken](#restrict-who-can-create-applications) en de toepassingen kan beheren die ze maken. In azure AD kunnen alle gebruikers standaard toepassingen registreren en alle aspecten beheren van de toepassingen die ze maken. Dit kan worden beperkt zodat alleen geselecteerde personen deze machtiging kunnen toestaan.
- [Een of meer eigen aren toewijzen aan een toepassing](#assign-application-owners). Dit is een eenvoudige manier om iemand de mogelijkheid te geven alle aspecten van de Azure AD-configuratie voor een specifieke toepassing te beheren.
- [Wijs een ingebouwde](#assign-built-in-application-admin-roles) beheerdersrol toe die toegang verleent voor het beheren van configuratie in azure AD voor alle toepassingen. Dit is de aanbevolen manier om IT-experts toegang te geven tot het beheren van brede toepassings configuratie machtigingen zonder toegang te verlenen tot het beheren van andere delen van Azure AD die niet gerelateerd zijn aan de configuratie van de toepassing.
- Het [maken van een aangepaste rol](#create-and-assign-a-custom-role-preview) om zeer specifieke machtigingen te definiëren en deze toe te wijzen aan iemand, hetzij aan het bereik van één toepassing als een beperkte eigenaar, of in het bereik van de directory (alle toepassingen) als beperkte beheerder.

Het is belang rijk om de toegang te verlenen met behulp van een van de bovenstaande methoden om twee redenen. Eerst dedraagt het delegeren van de mogelijkheid om beheer taken uit te voeren, de overhead van de globale beheerder. Ten tweede verbetert het gebruik van beperkte machtigingen uw beveiligings postuur en vermindert de kans op onbevoegde toegang. Zie voor meer informatie over de planning van de functie beveiliging [privileged Access beveiligen voor hybride en Cloud implementaties in azure AD](security-planning.md).

## <a name="restrict-who-can-create-applications"></a>Beperk wie toepassingen kunnen maken

In azure AD kunnen alle gebruikers standaard toepassingen registreren en alle aspecten beheren van de toepassingen die ze maken. Iedereen heeft ook de mogelijkheid om apps toegang te geven tot Bedrijfs gegevens namens hen. U kunt deze machtigingen selectief verlenen door de algemene switches in te stellen op Nee en de geselecteerde gebruikers toe te voegen aan de ontwikkelaar van de toepassing.

### <a name="to-disable-the-default-ability-to-create-application-registrations-or-consent-to-applications"></a>De standaard instelling voor het maken van toepassings registraties of toestemming voor toepassingen uitschakelen

1. Meld u aan bij uw Azure AD-organisatie met een account dat in aanmerking komt voor de rol globale beheerder in uw Azure AD-organisatie.
1. Stel een of beide van de volgende opties in:

    - Stel op de [pagina gebruikers instellingen voor uw organisatie](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings)de instelling **gebruikers kunnen toepassingen registreren** in op Nee. Hiermee wordt de standaard mogelijkheid voor gebruikers om toepassings registraties te maken uitgeschakeld.
    - Stel de gebruikers in de [gebruikers instellingen voor zakelijke toepassingen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)in om **toestemming te geven aan toepassingen die toegang hebben tot Bedrijfs gegevens, namens** de instelling Nee. Hiermee wordt de standaard mogelijkheid voor gebruikers om toestemming te geven voor het verkrijgen van toegang tot Bedrijfs gegevens in hun naam uitgeschakeld.

### <a name="grant-individual-permissions-to-create-and-consent-to-applications-when-the-default-ability-is-disabled"></a>Afzonderlijke machtigingen verlenen om toepassingen te maken en ermee toestemming te geven wanneer de standaard mogelijkheid is uitgeschakeld

Wijs de ontwikkelaar van de toepassing toe om de mogelijkheid om toepassings registraties te maken wanneer de gebruikers de instelling **van toepassingen kunnen registreren** is ingesteld op Nee. Deze rol verleent ook toestemming om toestemming te geven aan de hand van een eigen naam wanneer de **gebruikers toestemming kunnen geven voor apps die toegang hebben tot Bedrijfs gegevens namens hun** instelling is ingesteld op Nee. Als systeem gedrag, wanneer een gebruiker een nieuwe toepassings registratie maakt, worden ze automatisch toegevoegd als de eerste eigenaar. Machtigingen voor eigendom bieden de gebruiker de mogelijkheid om alle aspecten te beheren van een toepassings registratie of bedrijfs toepassing waarvan ze eigenaar zijn.

## <a name="assign-application-owners"></a>Toepassings eigenaren toewijzen

Het toewijzen van eigen aren is een eenvoudige manier om de mogelijkheid te bieden om alle aspecten van de Azure AD-configuratie voor een specifieke toepassings registratie of bedrijfs toepassing te beheren. Als systeem gedrag, wanneer een gebruiker een nieuwe toepassings registratie maakt, worden deze automatisch toegevoegd als de eerste eigenaar. Machtigingen voor eigendom bieden de gebruiker de mogelijkheid om alle aspecten te beheren van een toepassings registratie of bedrijfs toepassing waarvan ze eigenaar zijn. De oorspronkelijke eigenaar kan worden verwijderd en aanvullende eigen aars kunnen worden toegevoegd.

### <a name="enterprise-application-owners"></a>Eigen aren van bedrijfs toepassingen

Als eigenaar kan een gebruiker de organisatie-specifieke configuratie van de bedrijfs toepassing beheren, zoals de configuratie van eenmalige aanmelding, inrichting en gebruikers toewijzingen. Een eigenaar kan ook andere eigenaren toevoegen of verwijderen. In tegens telling tot globale beheerders kunnen eigen aren alleen de bedrijfs toepassingen beheren waarvan ze eigenaar zijn.

In sommige gevallen bevatten bedrijfs toepassingen die zijn gemaakt in de toepassings galerie zowel een bedrijfs toepassing als een toepassings registratie. Als dit het geval is, voegt een eigenaar toe te voegen aan de bedrijfs toepassing automatisch de eigenaar toe aan de bijbehorende registratie van de toepassing als eigenaar.

### <a name="to-assign-an-owner-to-an-enterprise-application"></a>Een eigenaar toewijzen aan een bedrijfs toepassing

1. Meld u aan bij [uw Azure AD-organisatie](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) met een account dat in aanmerking komt voor de toepassings beheerder of de beheerder van de Cloud toepassing voor de organisatie.
1. Selecteer op de [pagina app-registraties](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) voor de organisatie een app om de overzichts pagina voor de app te openen.
1. Selecteer **eigen aars** om de lijst met eigen aars voor de app weer te geven.
1. Selecteer **toevoegen** om een of meer eigen aars te selecteren die u aan de app wilt toevoegen.

> [!IMPORTANT]
> Gebruikers en service-principals kunnen eigen aren van toepassings registraties zijn. Alleen gebruikers kunnen eigenaar zijn van bedrijfs toepassingen. Groepen kunnen niet worden toegewezen als eigen aren van ofwel.
>
> Eigen aren kunnen referenties toevoegen aan een toepassing en deze referenties gebruiken om de identiteit van de toepassing te imiteren. De toepassing heeft mogelijk meer machtigingen dan de eigenaar en zou daarom een uitbrei ding van bevoegdheden hebben ten opzichte van wat de eigenaar toegang heeft als een gebruiker of Service-Principal. Een toepassings eigenaar kan mogelijk gebruikers of andere objecten maken of bijwerken tijdens het imiteren van de toepassing, afhankelijk van de machtigingen van de toepassing.

## <a name="assign-built-in-application-admin-roles"></a>Ingebouwde toepassings beheerders rollen toewijzen

Azure AD heeft een aantal ingebouwde beheerders rollen voor het verlenen van toegang tot het beheren van de configuratie in azure AD voor alle toepassingen. Deze rollen zijn de aanbevolen manier om IT-experts toegang te geven tot het beheren van brede toepassings configuratie machtigingen zonder dat ze toegang hoeven te verlenen tot het beheren van andere delen van Azure AD die niet gerelateerd zijn aan de configuratie van de toepassing.

- Toepassings beheerder: gebruikers met deze rol kunnen alle aspecten van bedrijfs toepassingen, toepassings registraties en toepassings proxy-instellingen maken en beheren. Deze rol verleent ook de mogelijkheid om toestemming te geven aan gedelegeerde machtigingen en toepassings machtigingen met uitzonde ring van Microsoft Graph. Gebruikers die aan deze rol zijn toegewezen, worden niet toegevoegd als eigen aren bij het maken van nieuwe toepassings registraties of zakelijke toepassingen.
- Cloud toepassings beheerder: gebruikers met deze rol hebben dezelfde machtigingen als de rol van toepassings beheerder, met uitzonde ring van de mogelijkheid om toepassings proxy te beheren. Gebruikers die aan deze rol zijn toegewezen, worden niet toegevoegd als eigen aren bij het maken van nieuwe toepassings registraties of zakelijke toepassingen.

Zie [ingebouwde rollen van Azure AD](permissions-reference.md)voor meer informatie en om de beschrijving voor deze rollen weer te geven.

Volg de instructies in de [rollen toewijzen aan gebruikers met Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md) instructies om de beheerders rollen van de toepassings beheerder of de Cloud toepassing toe te wijzen.

> [!IMPORTANT]
> Toepassings beheerders en beheerders van Cloud toepassingen kunnen referenties toevoegen aan een toepassing en deze referenties gebruiken om de identiteit van de toepassing te imiteren. De toepassing heeft mogelijk machtigingen die een uitbrei ding van bevoegdheden hebben ten opzichte van de machtigingen van de rol beheerder. Een beheerder in deze rol kan mogelijk gebruikers of andere objecten maken of bijwerken tijdens het imiteren van de toepassing, afhankelijk van de machtigingen van de toepassing.
> Geen van de rollen geeft de mogelijkheid om instellingen voor voorwaardelijke toegang te beheren.

## <a name="create-and-assign-a-custom-role-preview"></a>Een aangepaste rol maken en toewijzen (preview-versie)

Het maken van aangepaste rollen en het toewijzen van aangepaste rollen is een afzonderlijke stap:

- [Een aangepaste *roldefinitie* maken](custom-create.md) en [er machtigingen aan toevoegen vanuit een vooraf ingestelde lijst](custom-available-permissions.md). Dit zijn dezelfde machtigingen die worden gebruikt in de ingebouwde rollen.
- [Maak een *roltoewijzing*](custom-assign-powershell.md) om de aangepaste rol toe te wijzen.

Met deze schei ding kunt u een definitie van één rol maken en deze vervolgens meerdere keren aan verschillende *bereiken* toewijzen. Een aangepaste rol kan worden toegewezen aan het hele organisatie bereik of kan worden toegewezen aan het bereik als één Azure AD-object. Een voor beeld van een object bereik is een enkele app-registratie. Als u verschillende bereiken gebruikt, kan dezelfde roldefinitie worden toegewezen aan Sandra via alle app-registraties in de organisatie en vervolgens Naveen alleen over de registratie van de app voor onkosten rapporten van contoso.

Tips voor het maken en gebruiken van aangepaste rollen voor het delegeren van toepassings beheer:
- Aangepaste rollen verlenen alleen toegang via de meest actuele Blade van de app-registratie van de Azure AD-Portal. Ze verlenen geen toegang via de Blade verouderde app-registraties.
- Aangepaste rollen verlenen geen toegang tot de Azure AD-Portal wanneer de gebruikers instelling toegang tot de Azure AD-beheer Portal beperken is ingesteld op Ja.
- App-registraties de gebruiker toegang heeft tot het gebruik van roltoewijzingen, wordt alleen weer gegeven op het tabblad alle toepassingen op de pagina app-registratie. Ze worden niet weer gegeven op het tabblad eigendoms toepassingen.

Voor meer informatie over de basis principes van aangepaste rollen, zie het [overzicht van aangepaste functies](custom-overview.md), en hoe u [een aangepaste rol maakt](custom-create.md) en hoe u [een rol kunt toewijzen](custom-assign-powershell.md).

## <a name="next-steps"></a>Volgende stappen

- [Subtypen en machtigingen voor toepassings registratie](custom-available-permissions.md)
- [Naslag informatie over Azure AD-beheerders rollen](permissions-reference.md)
