---
title: Informatie over het overdragen van beheerdersrol-Azure Active Directory | Microsoft Docs
description: Overdrachts modellen, voor beelden en rollen beveiliging in Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9074fdbf31cdd8516b8d42f7cc13b4bc16f6634a
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095730"
---
# <a name="delegate-administration-in-azure-active-directory"></a>Beheer delegeren in Azure Active Directory

De groei van de organisatie is complex. Een veelvoorkomende reactie is het verminderen van een deel van de werk belasting van toegangs beheer met Azure Active Directory (AD) beheerders rollen. U kunt de minst mogelijke bevoegdheid toewijzen aan gebruikers om toegang te krijgen tot hun apps en hun taken uit te voeren. Zelfs als u de rol van globale beheerder niet aan elke toepassings eigenaar toewijst, plaatst u de verantwoordelijkheden voor toepassings beheer op de bestaande globale beheerders. Er zijn verschillende redenen waarom een organisatie naar een meer gedecentraliseerd beheer gaat. Dit artikel kan u helpen bij het plannen van de overdracht in uw organisatie.

<!--What about reporting? Who has which role and how do I audit?-->

## <a name="centralized-versus-delegated-permissions"></a>Gecentraliseerd versus gedelegeerde machtigingen

Naarmate een organisatie groeit, kan het lastig zijn om bij te houden welke gebruikers specifieke beheerders rollen hebben. Als een werk nemer beheerders rechten heeft, kan uw organisatie gevoeliger worden voor beveiligings inbreuken. Over het algemeen is het aantal beheerders dat u ondersteunt en hoe gedetailleerd hun machtigingen zijn, afhankelijk van de grootte en complexiteit van uw implementatie.

* In kleine of middel groot concept implementaties, een of meer beheerders doen alles. Er is geen delegering. Maak in dit geval elke beheerder met de rol globale beheerder.
* In grotere implementaties met meer computers, toepassingen en Desk Tops is meer delegering nodig. Verschillende beheerders hebben mogelijk meer specifieke functionele verantwoordelijkheden (rollen). Sommige kunnen bijvoorbeeld bevoegde identiteits beheerders zijn, en andere kunnen toepassings beheerders zijn. Daarnaast kan een beheerder alleen bepaalde groepen objecten, zoals apparaten, beheren.
* Zelfs grotere implementaties vereisen mogelijk nog nauw keurigere machtigingen, plus mogelijk beheerders met ongebruikelijke of hybride rollen.

In de Azure AD-Portal kunt u [alle leden van elke rol weer geven](manage-roles-portal.md), waarmee u snel uw implementatie en machtigingen voor delegeren kunt controleren.

Zie [Azure-rollen toewijzen met behulp van de Azure Portal](../../role-based-access-control/role-assignments-portal.md)als u geïnteresseerd bent in het overdragen van de toegang tot Azure-resources in plaats van beheerders toegang in azure AD.

## <a name="delegation-planning"></a>Overdracht plannen

Het is aan te raden om een delegerings model te ontwikkelen dat aan uw behoeften voldoet. Het ontwikkelen van een delegerings model is een iteratief ontwerp proces en we raden u aan de volgende stappen uit te voeren:

* Definieer de functies die u nodig hebt
* App-beheer delegeren
* De mogelijkheid verlenen om toepassingen te registreren
* App-eigendom delegeren
* Een beveiligings plan ontwikkelen
* Nood accounts maken
* Uw beheerders rollen beveiligen
* Geprivilegieerde uitbrei ding tijdelijk uitvoeren

## <a name="define-roles"></a>Rollen definiëren

Bepaal de Active Directory taken die worden uitgevoerd door beheerders en hoe deze worden toegewezen aan rollen. U kunt [gedetailleerde beschrijvingen van functies weer geven](manage-roles-portal.md) in de Azure Portal.

Elke taak moet worden geëvalueerd voor frequentie, urgentie en moeilijkheids graad. Deze criteria zijn een belang rijke aspecten van de taak definitie, omdat ze bepalen of een machtiging moet worden gedelegeerd:

* Taken die u regel matig uitvoert, een beperkt risico hebben en zijn lastig te volt ooien, zijn uitstekende kandidaten voor delegering.
* Taken die u zelden onderneemt, maar een grote invloed heeft op de hele organisatie en die hoge vaardigheids niveaus vereisen, moeten zorgvuldig worden overwogen voordat ze worden gedelegeerd. In plaats daarvan kunt u [tijdelijk een account verhogen naar de vereiste rol](../privileged-identity-management/pim-configure.md) of de taak opnieuw toewijzen.

## <a name="delegate-app-administration"></a>App-beheer delegeren

De verspreiding van apps binnen uw organisatie kan uw delegerings model belemmeren. Als de belasting van het toegangs beheer voor toepassingen in de globale beheerder wordt geplaatst, is het waarschijnlijk dat het model de overhead toeneemt wanneer er tijd wordt bereikt. Als u personen de rol globale beheerder hebt verleend voor zaken als het configureren van bedrijfs toepassingen, kunt u deze nu offloaden naar de volgende minder privilegede rollen. Dit helpt om uw beveiligings postuur te verbeteren en de kans op vervelend fouten te verminderen. De meest privileged Application Administrator-rollen zijn:

* De rol **toepassings beheerder** , die de mogelijkheid biedt om alle toepassingen in de Directory te beheren, met inbegrip van registraties, instellingen voor eenmalige aanmelding, gebruikers-en groeps toewijzingen en licenties, toepassings proxy-instellingen en toestemming. De functie voor het beheren van voorwaardelijke toegang wordt niet door deze service verleend.
* De rol van de beheerder van de **Cloud toepassing** , die alle mogelijkheden van de toepassings beheerder verleent, met uitzonde ring van het verlenen van toegang tot de instellingen van de toepassings proxy (omdat deze geen lokale machtiging heeft).

## <a name="delegate-app-registration"></a>App-registratie delegeren

Standaard kunnen alle gebruikers toepassings registraties maken. De mogelijkheid om toepassings registraties te maken selectief verlenen:

* Stel **gebruikers kunnen toepassingen registreren** op Nee in **gebruikers instellingen**
* De gebruiker toewijzen aan de ontwikkelaar van de toepassing

De mogelijkheid om toestemming te geven voor het toestaan van toegang tot gegevens selectief verlenen:

* Stel in dat **gebruikers de toegang tot Bedrijfs gegevens in hun naam kunnen** toestaan op Nee in **gebruikers instellingen**
* De gebruiker toewijzen aan de ontwikkelaar van de toepassing

Wanneer een toepassings ontwikkelaar een nieuwe toepassings registratie maakt, worden deze automatisch toegevoegd als de eerste eigenaar.

## <a name="delegate-app-ownership"></a>App-eigendom delegeren

Voor een nog nauw keurigere machtiging voor toegang tot de app kunt u het eigendom toewijzen aan afzonderlijke bedrijfs toepassingen. Dit is een aanvulling op de bestaande ondersteuning voor het toewijzen van eigen aren van toepassings registratie. Het eigendom wordt toegewezen op basis van een toepassing per bedrijf op de Blade bedrijfs toepassingen. Het voor deel is dat eigen aren alleen de bedrijfs toepassingen kunnen beheren waarvan ze eigenaar zijn. U kunt bijvoorbeeld een eigenaar toewijzen voor de Sales Force-toepassing en die eigenaar kan de toegang tot en configuratie voor Sales Force beheren en geen andere toepassingen. Een bedrijfs toepassing kan veel eigen aren hebben en een gebruiker kan de eigenaar zijn voor veel zakelijke toepassingen. Er zijn twee rollen van de eigenaar van de app:

* De rol van de eigenaar van de **bedrijfs toepassing** biedt de mogelijkheid om de bedrijfs toepassingen te beheren waarvan de gebruiker eigenaar is, met inbegrip van instellingen voor eenmalige aanmelding, gebruikers-en groeps toewijzingen en het toevoegen van extra eigen aren. De mogelijkheid om toepassings proxy-instellingen of voorwaardelijke toegang te beheren, wordt niet door de IT-service verleend.
* De rol van de eigenaar van de **toepassings registratie** verleent de mogelijkheid om toepassings registraties te beheren voor de app waarvan de gebruiker eigenaar is, met inbegrip van het toepassings manifest en het toevoegen van extra eigen aren.

## <a name="develop-a-security-plan"></a>Een beveiligings plan ontwikkelen

Azure AD biedt een uitgebreide hand leiding voor het plannen en uitvoeren van een beveiligings plan voor uw Azure AD-beheerders rollen, het [beveiligen van bevoegde toegang voor hybride en Cloud implementaties](security-planning.md).

## <a name="establish-emergency-accounts"></a>Nood accounts maken

Als u de toegang tot uw identiteits beheer archief wilt behouden wanneer het probleem zich voordoet, moet u accounts voor toegang tot nood gevallen voorbereiden op basis van het [maken van administratieve accounts voor nood toegang](security-emergency-access.md).

## <a name="secure-your-administrator-roles"></a>Uw beheerders rollen beveiligen

Aanvallers die het beheer van geprivilegieerde accounts verkrijgen, kunnen grote schade ondervinden, dus Beveilig deze accounts eerst met behulp van het [basis beleid voor toegang](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/22/baseline-security-policy-for-azure-ad-admin-accounts-in-public-preview/) dat standaard beschikbaar is voor alle Azure AD-organisaties (in open bare preview). Het beleid dwingt multi-factor Authentication af op privileged Azure AD-accounts. De volgende Azure AD-rollen worden gedekt door het Azure AD Baseline-beleid:

* Globale beheerder
* SharePoint-beheerder
* Exchange-beheerder
* Beheerder van voorwaardelijke toegang
* Beveiligingsbeheerder

## <a name="elevate-privilege-temporarily"></a>Bevoegdheid om tijdelijk bevoegdheden uit te breiden

Voor de meeste dagelijkse activiteiten hebben niet alle gebruikers globale beheerders rechten nodig en moeten ze niet allemaal permanent worden toegewezen aan de rol van globale beheerder. Wanneer gebruikers de machtigingen van een globale beheerder nodig hebben, moeten ze de roltoewijzing activeren in azure AD [privileged Identity Management](../privileged-identity-management/pim-configure.md) op hun eigen account of een alternatief beheerders account.

## <a name="next-steps"></a>Volgende stappen

Zie [beheerders rollen toewijzen in azure AD](permissions-reference.md) voor een verwijzing naar de beschrijvingen van Azure AD-functies.