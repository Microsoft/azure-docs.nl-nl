---
title: Azure Active Directory overwegingen bij het ontwerpen van hybride identiteiten-overzicht | Microsoft Docs
description: Overzicht en inhouds overzicht van de hand leiding voor het ontwerpen van hybride identiteiten
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 100509c4-0b83-4207-90c8-549ba8372cf7
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7f8dd49f3668b8f68753681123a04d21edac46c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997729"
---
# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Ontwerpoverwegingen voor Azure Active Directory Hybrid Identity
Apparaten op basis van consumenten maken gebruik van de bedrijfs wereld en SaaS-toepassingen (Software-as-a-Service) in de Cloud zijn gemakkelijk te nemen. Als gevolg hiervan kan het beheer van de toepassings toegang van gebruikers via interne data centers en Cloud platforms lastig zijn.  

De identiteitsoplossingen van Microsoft omvatten mogelijkheden voor zowel on-premises als in de cloud. Er wordt één gebruikersidentiteit gemaakt voor verificatie en autorisatie bij alle resources, ongeacht hun locatie. Dit concept wordt een hybride identiteit genoemd. Er zijn verschillende ontwerp-en configuratie opties voor hybride identiteiten met behulp van micro soft-oplossingen. in sommige gevallen kan het lastig zijn om te bepalen welke combi natie het beste voldoet aan de behoeften van uw organisatie. 

In deze hand leiding voor het ontwerpen van hybride identiteiten wordt uitgelegd hoe u een hybride identiteits oplossing kunt ontwerpen die het beste past bij de zakelijke en technologische behoeften van uw organisatie.  In deze hand leiding wordt een reeks stappen en taken beschreven die u kunt volgen om u te helpen een hybride identiteits oplossing te ontwerpen die voldoet aan de unieke vereisten van uw organisatie. Tijdens de stappen en taken worden de relevante technologieën en functie opties beschikbaar gesteld aan organisaties om te voldoen aan de functionele en service kwaliteit (zoals Beschik baarheid, schaalbaarheids, prestaties, beheer baarheid en beveiligings niveau). 

In het bijzonder zijn de doel stellingen voor het ontwerp van de hybride identiteits handleiding de volgende vragen beantwoord: 

* Welke vragen moet ik stellen en antwoorden op een hybride identiteits ontwerp voor een technologie of probleem domein dat het beste voldoet aan mijn vereisten?
* Welke volg orde van activiteiten moet ik volt ooien om een hybride identiteits oplossing te ontwerpen voor de technologie of het probleem domein? 
* Welke hybride identiteits technologie en configuratie opties zijn beschikbaar om mij te helpen voldoen aan mijn vereisten? Wat zijn de verschillen tussen deze opties, zodat ik de beste optie voor mijn bedrijf kan selecteren?

## <a name="who-is-this-guide-intended-for"></a>Voor wie is deze handleiding bedoeld?
 CIO, CITO, Chief Identity Architects, Enter prise Architects en IT-architecten die verantwoordelijk zijn voor het ontwerpen van een hybride identiteits oplossing voor gemiddeld of grote organisaties.

## <a name="how-can-this-guide-help-you"></a>Hoe kan deze handleiding u helpen?
U kunt deze hand leiding gebruiken om te begrijpen hoe u een hybride identiteits oplossing ontwerpt waarmee een op de cloud gebaseerd identiteits beheersysteem kan worden geïntegreerd met uw huidige on-premises identiteits oplossing. 

In de volgende afbeelding ziet u een voor beeld van een hybride identiteits oplossing waarmee IT-beheerders hun huidige Windows Server Active Directory-oplossing op locatie kunnen integreren met Microsoft Azure Active Directory om gebruikers in staat te stellen één Sign-On (SSO) te gebruiken voor toepassingen die zich in de Cloud en on-premises bevinden.

![Voorbeeld](media/plan-hybrid-identity-design-considerations/hybridID-example.png)

De bovenstaande afbeelding is een voor beeld van een hybride identiteits oplossing die gebruikmaakt van Cloud Services om te integreren met on-premises mogelijkheden om één ervaring te bieden aan het verificatie proces van de eind gebruiker en om het beheer van deze resources mogelijk te maken. Hoewel dit voor beeld een veelvoorkomend scenario kan zijn, kan het hybride identiteits ontwerp van elke organisatie waarschijnlijk afwijken van het voor beeld in afbeelding 1 vanwege verschillende vereisten. 

Deze hand leiding bevat een reeks stappen en taken die u kunt volgen om een hybride identiteits oplossing te ontwerpen die voldoet aan de unieke vereisten van uw organisatie. In de volgende stappen en taken biedt de hand leiding de relevante technologieën en functie opties die u kunt gebruiken om te voldoen aan de vereisten voor het functionele en service niveau voor uw organisatie.

**Veronderstellingen**: u hebt enige ervaring met Windows Server, Active Directory Domain Services en Azure Active Directory. In dit document wordt ervan uitgegaan dat u wilt weten hoe deze oplossingen kunnen voldoen aan uw bedrijfs behoeften, of in een geïntegreerde oplossing.

## <a name="design-considerations-overview"></a>Overzicht van ontwerp overwegingen
Dit document bevat een reeks stappen en taken die u kunt uitvoeren om een hybride identiteits oplossing te ontwerpen die het beste voldoet aan uw vereisten. De stappen worden op een geordende wijze weergegeven. Ontwerp overwegingen die u in latere stappen leert, vereisen mogelijk dat u de beslissingen die u in eerdere stappen hebt aangebracht, moet wijzigen, omdat er conflicten zijn met de ontwerp opties. Elke poging wordt gedaan om u te waarschuwen voor potentiële ontwerp conflicten in het hele document. 

U ontvangt het ontwerp dat het beste voldoet aan uw vereisten nadat u de stappen zo vaak als nodig hebt door lopen om alle overwegingen in het document op te nemen. 

| Fase hybride identiteit | Onderwerpen lijst |
| --- | --- |
| Identiteitsvereisten vaststellen |[Bedrijfs behoeften bepalen](plan-hybrid-identity-design-considerations-business-needs.md)<br> [Vereisten voor adreslijst synchronisatie bepalen](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [Vereisten voor multi-factor Authentication bepalen](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [Een strategie voor het implementeren van een hybride identiteit definiëren](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md) |
| Plan voor het verbeteren van de gegevens beveiliging door middel van een oplossing met een hoge identiteit |[Vereisten voor gegevens beveiliging bepalen](plan-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [Vereisten voor inhoudsbeheer vaststellen](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [Vereisten voor toegangsbeheer vaststellen](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [Vereisten voor reageren op incidenten vaststellen](plan-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [Strategie voor gegevens beveiliging definiëren](plan-hybrid-identity-design-considerations-data-protection-strategy.md) |
| De levenscyclus van een hybride identiteit plannen |[Taken voor het beheren van hybride identiteiten bepalen](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [Synchronisatie beheer](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [De strategie voor het implementeren van hybride identiteits beheer bepalen](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |

## <a name="next-steps"></a>Volgende stappen
[Identiteitsvereisten vaststellen](plan-hybrid-identity-design-considerations-business-needs.md)

