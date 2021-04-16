---
title: Zichtbaarheid en controle van apps met Microsoft Cloud App Security
description: Meer informatie over manieren om app-risiconiveaus te identificeren, schendingen en lekken in realtime te stoppen en app-connectors te gebruiken om te profiteren van provider-API's voor zichtbaarheid en governance.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: iangithinji
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9a9f56d70e049200cee0c3655a9998feccfa301
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377833"
---
# <a name="cloud-app-visibility-and-control"></a>Zichtbaarheid en beheer van cloud-apps

Om het volledige voordeel van cloud-apps en -services te krijgen, moet een IT-team de juiste balans vinden tussen het ondersteunen van toegang en het behouden van controle om kritieke gegevens te beveiligen. Microsoft Cloud App Security biedt uitgebreide zichtbaarheid, controle over het reizen van gegevens en geavanceerde analyses voor het identificeren en bestrijden van cyberbedreigingen in al uw Microsoft- en externe cloudservices.

## <a name="discover-and-manage-shadow-it-in-your-network"></a>Schaduw-IT in uw netwerk detecteren en beheren

Wanneer IT-beheerders wordt gevraagd hoeveel cloud-apps ze denken te gebruiken door hun werknemers, zeggen ze gemiddeld 30 of 40, terwijl het gemiddelde in werkelijkheid meer dan 1000 afzonderlijke apps is die worden gebruikt door werknemers in uw organisatie. Schaduw-IT helpt u te bepalen welke apps worden gebruikt en wat uw risiconiveau is. 80 procent van de werknemers gebruikt niet-geanctioneerde apps die niemand heeft beoordeeld en die mogelijk niet compatibel zijn met uw beveiligings- en nalevingsbeleid. En omdat uw werknemers toegang hebben tot uw resources en apps van buiten uw bedrijfsnetwerk, is het niet meer voldoende om regels en beleidsregels op uw firewalls te hebben.

Gebruik Microsoft Cloud App Discovery (een Azure Active Directory Premium P1-functie) om te ontdekken welke apps worden gebruikt, om het risico van deze apps te verkennen, beleidsregels te configureren voor het identificeren van nieuwe riskante apps en om deze apps niet toe te staan om ze systeemeigen te blokkeren met behulp van uw proxy- of firewallapparaat.

- Schaduw-IT detecteren en identificeren
- Evalueren en analyseren
- Uw apps beheren
- Geavanceerde detectierapportage voor Schaduw-IT
- Goedgekeurde apps beheren
 
### <a name="learn-more"></a>Lees meer

- [Schaduw-IT in uw netwerk ontdekken en beheren ](/cloud-app-security/tutorial-shadow-it)
- [Apps met Cloud App Security ](/cloud-app-security/discovered-apps)
 
## <a name="user-session-visibility-and-control"></a>Zichtbaarheid en controle van gebruikerssessies 

Op de huidige werkplek is het vaak niet voldoende om na afloop te weten wat er in uw cloudomgeving gebeurt. U wilt schendingen en lekken in realtime stoppen voordat werknemers opzettelijk of per ongeluk uw gegevens en uw organisatie in gevaar brengen. Samen met Azure Active Directory (Azure AD) biedt Microsoft Cloud App Security deze mogelijkheden in een holistische en geïntegreerde ervaring met App-beheer voor voorwaardelijke toegang. 

Sessiebeheer maakt gebruik van een reverse proxy-architectuur en is uniek geïntegreerd met voorwaardelijke toegang van Azure AD. Met voorwaardelijke toegang van Azure AD kunt u toegangsbesturingselementen afdwingen voor de apps van uw organisatie op basis van bepaalde voorwaarden. De voorwaarden bepalen op wie (gebruiker of groep gebruikers) en op welke (welke cloud-apps) en op welke (welke locaties en netwerken) een beleid voor voorwaardelijke toegang wordt toegepast. Nadat u de voorwaarden hebt bepaald, kunt u gebruikers door Cloud App Security waar u gegevens in realtime kunt beveiligen.  

Met dit besturingselement kunt u het volgende doen:  
- Bestanddownloads controleren
- B2B-scenario's bewaken  
- Toegang tot bestanden beheren  
- Documenten beveiligen bij downloaden  
 
### <a name="learn-more"></a>Lees meer

- [Apps beveiligen met sessiebeheer in Cloud App Security ](/cloud-app-security/proxy-intro-aad)
 
## <a name="advanced-app-visibility-and-controls"></a>Geavanceerde zichtbaarheid en besturingselementen voor apps 

App-connectors gebruiken de API's van app-providers om meer zichtbaarheid en controle te Microsoft Cloud App Security over de apps waarmee u verbinding maakt. Cloud App Security maakt gebruik van de API's van de cloudprovider. Elke service heeft een eigen framework en API-beperkingen, zoals beperking, API-limieten, dynamisch verschuivende API-vensters en andere. Het Cloud App Security productteam heeft met deze services gewerkt om het gebruik van API's te optimaliseren en de beste prestaties te leveren. Rekening houdend met verschillende beperkingen die services opleggen aan hun API's, gebruiken de Cloud App Security-engines hun maximaal toegestane capaciteit. Sommige bewerkingen, zoals het scannen van alle bestanden in de tenant, vereisen talrijke API-aanroepen, zodat ze over een langere periode worden verspreid. Verwacht dat sommige beleidsregels enkele uren of dagen worden uitgevoerd. 
 
### <a name="learn-more"></a>Lees meer  

- [Apps verbinden in Cloud App Security ](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)

## <a name="next-steps"></a>Volgende stappen

- [Schaduw-IT in uw netwerk ontdekken en beheren ](/cloud-app-security/tutorial-shadow-it)
- [Apps met Cloud App Security ](/cloud-app-security/discovered-apps)
- [Apps beveiligen met sessiebeheer in Cloud App Security ](/cloud-app-security/proxy-intro-aad)
- [Apps verbinden in Cloud App Security ](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)