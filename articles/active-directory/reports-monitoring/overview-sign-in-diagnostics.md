---
title: Wat zijn diagnostische aanmeldingsgegevens in Azure AD? | Microsoft Docs
description: Biedt een algemeen overzicht van de diagnostische aanmeldingsgegevens in Azure AD.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 12/15/2020
ms.author: markvi
ms.reviewer: tspring
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6aedf41fbf1ed0d70467a2efe97431fdecaa4fa
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585906"
---
# <a name="what-is-sign-in-diagnostic-in-azure-ad"></a>Wat zijn diagnostische aanmeldingsgegevens in Azure AD?

Azure AD biedt een flexibel beveiligingsmodel waarmee u kunt beheren wat gebruikers kunnen doen met de beheerde resources. Toegang tot deze resources is niet alleen afhankelijk van **wie** u bent, maar ook van **hoe** u de resources opent. Doorgaans gaat flexibiliteit gepaard met een zekere mate van complexiteit, vanwege het aantal configuratieopties dat u hebt. Complexiteit kan het risico op fouten verhogen.

Als IT-beheerder hebt u een oplossing nodig waarmee u het juiste inzichtniveau krijgt in de activiteiten in uw systeem, zodat u gemakkelijk problemen kunt vaststellen en oplossen wanneer deze zich voordoen. De diagnostische aanmeldingsgegevens voor Azure AD zijn een voorbeeld van een dergelijke oplossing. Gebruik de diagnostische gegevens om te analyseren wat er is gebeurd tijdens een aanmelding, en welke acties u kunt ondernemen om problemen op te lossen zonder dat u hiervoor Microsoft-ondersteuning hoeft in te schakelen.

In dit artikel vindt u een overzicht van wat de oplossing doet, en hoe u deze kunt gebruiken.


## <a name="requirements"></a>Vereisten

De diagnostische aanmeldingsgegevens zijn beschikbaar in alle edities van Azure AD.<br> U moet een globale beheerder zijn in Azure AD om deze functie te kunnen gebruiken.

## <a name="how-it-works"></a>Uitleg

In Azure AD is het antwoord op een aanmeldingspoging gekoppeld aan **wie** u bent en **hoe** u toegang wilt krijgen tot de tenant. Als beheerder kunt u doorgaans bijvoorbeeld alle aspecten van uw tenant configureren wanneer u zich aanmeldt vanuit uw bedrijfsnetwerk. Het kan echter zo zijn dat u bent geblokkeerd wanneer u zich met hetzelfde account aanmeldt vanuit een niet-vertrouwd netwerk.
 
Als gevolg van de grotere flexibiliteit van het systeem om te reageren op een aanmeldingspoging, kan het gebeuren dat u problemen met aanmelden moet oplossen. Met diagnostische aanmeldingsgegevens kunt u:

- Aanmeldingsgegevens analyseren. 

- Weergeven wat er is gebeurd, en aanbevelingen doen voor het oplossen van problemen. 

Diagnostische aanmeldingsgegevens voor Azure AD zijn ontworpen om zelf een diagnose uit te voeren wanneer zich fouten voordoen tijdens het aanmelden. Om het diagnostische proces te voltooien, moet u het volgende doen:

![Diagnostische aanmeldingsgegevens - het proces](./media/overview-sign-in-diagnostics/process.png)
 
1. **Definiëren** wat het bereik is van de aanmeldingsgebeurtenissen die belangrijk zijn voor u

2. **Selecteren** welke aanmelding u wilt beoordelen

3. Het diagnostische resultaat **beoordelen**

4. Actie **ondernemen**

 
### <a name="define-scope"></a>Bereik definiëren

Het doel van deze stap is het definiëren van het bereik voor de aanmeldingen die u wilt onderzoeken. Uw bereik is gebaseerd op een gebruiker of id (correlationId, aanvraag-URI) en op een tijdsbereik. Als u het bereik verder wilt beperken, kunt u ook een app-naam opgeven. In Azure AD worden de bereikgegevens gebruikt om de juiste gebeurtenissen voor u te vinden.  

### <a name="select-sign-in"></a>Aanmelding selecteren  

Op basis van uw zoekcriteria worden in Azure AD alle overeenkomende aanmeldingen opgehaald en weergegeven in een lijstweergave met een verificatieoverzicht. 

![Verificatieoverzicht](./media/overview-sign-in-diagnostics/authentication-summary.png)
 
U kunt de kolommen aanpassen die in deze weergave te zien zijn.

### <a name="review-diagnostic"></a>Diagnose beoordelen 

Azure AD biedt u een diagnostisch resultaat voor de geselecteerde aanmeldingsgebeurtenis. 

![Diagnostische resultaten](./media/overview-sign-in-diagnostics/diagnostics-results.png)

 
Het resultaat begint met een evaluatie. In de evaluatie wordt in enkele zinnen uitgelegd wat er is gebeurd. De uitleg helpt u om het gedrag van het systeem te begrijpen. 

Als volgende stap krijgt u een overzicht van gerelateerd beleid voor voorwaardelijke toegang dat is toegepast op de geselecteerde aanmelding. Dit onderdeel wordt voltooid met aanbevolen herstelstappen voor het oplossen van het probleem. Omdat het niet altijd mogelijk is om problemen op te lossen zonder extra hulp, kan het openen van een ondersteuningsticket een aanbevolen stap zijn. 

### <a name="take-action"></a>Actie ondernemen 
Hier aangekomen beschikt u, als het goed is, over de benodigde informatie om het probleem op te lossen.


## <a name="scenarios"></a>Scenario's

Deze sectie biedt een overzicht van de beschikbare diagnostische scenario's. De volgende scenario's worden geïmplementeerd: 
 
- Geblokkeerd op basis van beleid voor voorwaardelijke toegang

- Mislukte vanwege voorwaardelijke toegang

- MFA vanwege voorwaardelijke toegang

- MFA vanwege andere vereisten

- Proof-up voor MFA is vereist

- Proof-up voor MFA is vereist, maar de aanmeldingspoging van de gebruiker komt niet vanaf een veilige locatie

- Geslaagde aanmelding


### <a name="blocked-by-conditional-access"></a>Geblokkeerd op basis van beleid voor voorwaardelijke toegang

Dit scenario is gebaseerd op een aanmelding die is geblokkeerd vanwege beleid voor voorwaardelijke toegang.

![Toegang blokkeren](./media/overview-sign-in-diagnostics/block-access.png)

De sectie diagnostische gegevens voor dit scenario bevat details over de gebruikersaanmelding en het toegepaste beleid.


### <a name="failed-conditional-access"></a>Mislukte vanwege voorwaardelijke toegang

Dit scenario is doorgaans het gevolg van een aanmelding die is mislukt omdat niet is voldaan aan de vereisten van beleid voor voorwaardelijke toegang. Enkele typische voorbeelden:

![Besturingselementen vereisen](./media/overview-sign-in-diagnostics/require-controls.png)

- Hybride Azure AD-gekoppeld apparaat vereisen

- Goedgekeurde client-apps vereisen

- Beleid voor app-beveiliging vereisen   


De sectie diagnostische gegevens voor dit scenario bevat details over de gebruikersaanmelding en het toegepaste beleid.


### <a name="mfa-from-conditional-access"></a>MFA vanwege voorwaardelijke toegang

Dit scenario is gebaseerd op beleid voor voorwaardelijke toegang waarvoor aanmelden met behulp van meervoudige verificatie is vereist.

![Multi-Factor Authentication vereisen](./media/overview-sign-in-diagnostics/require-mfa.png)

De sectie diagnostische gegevens voor dit scenario bevat details over de gebruikersaanmelding en het toegepaste beleid.



### <a name="mfa-from-other-requirements"></a>MFA vanwege andere vereisten

Dit scenario is gebaseerd op een vereiste voor meervoudige verificatie die niet is afgedwongen op basis van beleid voor voorwaardelijke toegang. Bijvoorbeeld meervoudige verificatie per gebruiker.


![Meervoudige verificatie per gebruiker vereisen](./media/overview-sign-in-diagnostics/mfa-per-user.png)


De intentie van dit diagnostische scenario is om meer details te bieden over:

- De bron van de onderbreking van meervoudige verificatie. 
- Het resultaat van de clientinteractie.

Daarnaast biedt deze sectie ook alle details over de aanmeldingspoging van de gebruiker. 


### <a name="mfa-proof-up-required"></a>Proof-up voor MFA is vereist

Dit scenario is gebaseerd op aanmeldingen die zijn onderbroken vanwege aanvragen om meervoudige verificatie in te stellen. Deze configuratie wordt ook wel 'proof up' genoemd.

Proof-up van meervoudige verificatie vindt plaats wanneer een gebruiker verplicht is meervoudige verificatie te gebruiken, maar deze functie nog niet heeft geconfigureerd. Of als een beheerder heeft bepaald dat de gebruiker deze functie moet configureren.

De intentie van dit diagnostische scenario is om aan te geven dat de onderbreking vanwege meervoudige verificatie is bedoeld om de functie te configureren, en de gebruiker te adviseren om de proof-up te voltooien.

### <a name="mfa-proof-up-required-from-a-risky-sign-in"></a>Proof up voor MFA vereist vanaf een riskante aanmelding

Dit scenario doet zich voor wanneer aanmeldingen zijn onderbroken vanwege een aanvraag om meervoudige verificatie te configureren via een risicovolle aanmelding. 

De intentie van dit diagnostische scenario is om aan te geven dat de onderbreking vanwege meervoudige verificatie is bedoeld om de functie te configureren, en de gebruiker te adviseren om de proof-up te voltooien maar dit te doen vanaf een netwerklocatie die geen risico vormt. Als een bedrijfsnetwerk bijvoorbeeld is gedefinieerd als een benoemde locatie, probeert u in plaats hiervan de proof-up uit te voeren vanuit het bedrijfsnetwerk.


### <a name="successful-sign-in"></a>Geslaagde aanmelding

Dit scenario is gebaseerd op aanmeldingen die niet zijn onderbroken vanwege voorwaardelijke toegang of meervoudige verificatie.

De intentie van dit diagnostische scenario is om inzicht te bieden in welke info de gebruiker heeft geleverd tijdens de aanmelding als er sprake was van beleid voor voorwaardelijke toegang dat moest worden toegepast, of van geconfigureerde meervoudige verificatie die de gebruikersaanmelding had moeten onderbreken.



## <a name="next-steps"></a>Volgende stappen

* [Wat zijn Azure Active Directory-rapporten?](overview-reports.md)
* [Wat is Azure Active Directory-controle?](overview-monitoring.md)
