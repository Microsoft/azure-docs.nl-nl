---
title: Overzicht van toegangsbeheer voor Synapse-werkruimte
description: In dit artikel worden de mechanismen beschreven die worden gebruikt voor het beheren van de toegang tot een Synapse-werkruimte, evenals de resources en codeartefacten die deze bevat.
services: synapse-analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 12/03/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 20614b1c397bdf24e807d48d3de33f0033da14bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100105110"
---
# <a name="synapse-access-control"></a>Toegangsbeheer voor Synapse 

Dit artikel bevat een overzicht van de mechanismen die beschikbaar zijn voor het beheren van de toegang tot rekenresources en gegevens van Synapse.  

## <a name="overview"></a>Overzicht

Synapse biedt een uitgebreid en nauwkeurig toegangscontrolesysteem dat de volgende elementen met elkaar integreert: 
- **Azure-rollen** voor het beheren van resources en voor toegang tot gegevens in de opslag; 
- **Synapse-rollen** voor het beheren van livetoegang tot code en de uitvoering ervan; 
- **SQL-rollen** voor gegevensvlaktoegang tot gegevens in SQL-pools en 
- **Git-machtigingen** voor broncodebeheer, inclusief ondersteuning voor continue integratie en implementatie.  

Synapse-rollen bieden machtigingensets die kunnen worden toegepast op verschillende bereiken. Deze granulariteit maakt het eenvoudig om de juiste toegang te verlenen aan beheerders, ontwikkelaars, beveiligingspersoneel en operators tot rekenresources en gegevens.

Toegangsbeheer kan worden vereenvoudigd door gebruik te maken van beveiligingsgroepen die zijn afgestemd op de functies van personen. Om toegang te beheren hoeft u alleen maar gebruikers toe te voegen aan de juiste beveiligingsgroepen of ze eruit te verwijderen.

## <a name="access-control-elements"></a>Elementen van toegangsbeheer

### <a name="creating-and-managing-synapse-compute-resources"></a>Synapse-rekenresources maken en beheren

Azure-rollen worden gebruikt voor het beheren van: 
- Toegewezen SQL-pools, 
- Apache Spark-pools en 
- integratieruntimes. 

Als u deze resources wilt *maken*, moet u een Azure-eigenaar of -inzender zijn voor de resourcegroep. Als u ze wilt *beheren* nadat ze zijn gemaakt, moet u een Azure-eigenaar of -inzender zijn voor de resourcegroep of voor de afzonderlijke resources. 

### <a name="developing-and-executing-code-in-synapse"></a>Code ontwikkelen en uitvoeren in Synapse 

Synapse ondersteunt twee ontwikkelmodellen.

- **Synapse-liveontwikkeling**. In Synapse Studio ontwikkelt u code en lost u fouten in code op, waarna u de code **publiceert** om deze op te slaan en uit te voeren.  De Synapse-service is de bron van waarheid als het gaat om het bewerken en uitvoeren van code.  Ongepubliceerd werk gaat verloren wanneer u Synapse Studio sluit.  
- **Ontwikkelen met Git-functionaliteit**. U kunt code ontwikkelen en fouten opsporen in Synapse Studio en wijzigingen **doorvoeren** in een werkende vertakking van een Git-opslagplaats. Werk van een of meer vertakkingen wordt geïntegreerd in een samenwerkingsvertakking, van waaruit u deze naar de service kunt **publiceren**. De Git-opslagplaats is de bron van waarheid voor het bewerken van code, terwijl de service de bron van waarheid is voor de uitvoering ervan. Wijzigingen moeten worden doorgevoerd in de Git-opslagplaats of naar de service worden gepubliceerd voordat Synapse Studio wordt gesloten. [Meer informatie](../cicd/continuous-integration-deployment.md) over het gebruik van Synapse Analytics met Git.

In beide ontwikkelmodellen kan elke gebruiker met toegang tot Synapse Studio codeartefacten maken. U hebt echter aanvullende machtigingen nodig om artefacten te publiceren naar de service, gepubliceerde artefacten te lezen, wijzigingen door te voeren in Git, code uit te voeren en om toegang te krijgen tot gekoppelde gegevens die worden beveiligd met referenties.

### <a name="synapse-roles"></a>Synapse-rollen

Synapse-rollen worden gebruikt voor het beheren van de toegang tot de Synapse-service waarmee u het volgende kunt doen: 
- Gepubliceerde codeartefacten weergeven; 
- Codeartefacten, gekoppelde services en referentiedefinities publiceren;
- Code of pijplijnen uitvoeren die gebruikmaken van Synapse -rekenresources;
- Code of pijplijnen uitvoeren die toegang hebben tot gekoppelde gegevens die zijn beveiligd met referenties;
- Uitvoer weergeven die is gekoppeld aan gepubliceerde codeartefacten;
- De status van rekenresources controleren en runtimelogboeken bekijken.

Synapse-rollen kunnen worden toegewezen aan het werkruimtebereik of aan meer specifieke bereiken om de machtigingen te beperken die aan specifieke Synapse-resources worden verleend.

### <a name="git-permissions"></a>Git-machtigingen

Wanneer u met Git-functionaliteit ontwikkelt in de Git-modus, zijn het uw Git-machtigingen die bepalen of u wijzigingen in codeartefacten kunt lezen en doorvoeren, waaronder definities van gekoppelde services en van referenties.   
   
### <a name="accessing-data-in-sql"></a>Toegang tot gegevens in SQL

Wanneer u met toegewezen en serverloze SQL-pools werkt, wordt de toegang tot het gegevensvlak beheerd met behulp van SQL-machtigingen. 

De maker van een werkruimte wordt als Active Directory-beheerder aan de werkruimte toegewezen. Nadat deze rol is gemaakt, kan deze in Azure Portal worden toegewezen aan een andere gebruiker of aan een beveiligingsgroep.

**Serverloze SQL-pools**: Aan Synapse-beheerders worden `db_owner` (`DBO`) machtigingen verleend voor de serverloze SQL-pool 'Ingebouwd'. Synapse-beheerders moeten SQL-scripts uitvoeren op elke serverloze pool om andere gebruikers toegang te verlenen tot serverloze SQL-pools.  

**Toegewezen SQL-pools**: Aan de maker van de werkruimte en de MSI van de werkruimte wordt de Active Directory-beheerdersmachtiging verleend.  Anders worden machtigingen voor toegang tot toegewezen SQL-pools niet automatisch verleend. Als de Active Directory-beheerder andere gebruikers of groepen toegang wil geven tot toegewezen SQL-pools, moet deze op elke toegewezen SQL-pool SQL-scripts uitvoeren.

In [Toegangsbeheer voor Synapse instellen](./how-to-set-up-access-control.md) vindt u voorbeelden van SQL-scripts voor het verlenen van SQL-machtigingen in SQL-pools.  

 ### <a name="accessing-system-managed-data-in-storage"></a>Toegang tot door het systeem beheerde gegevens in de opslag

Serverloze SQL-pools en Apache Spark-tabellen slaan hun gegevens op in een ADLS Gen2-container die aan de werkruimte is gekoppeld. Door de gebruiker geïnstalleerde Apache Spark-bibliotheken worden ook in hetzelfde opslagaccount beheerd. Om deze gebruikstoepassingen mogelijk te maken moet aan gebruikers en aan de MSI van de werkruimte toegang als **Gegevensbijdrager voor opslagblob** zijn verleend voor deze ADLS Gen2-opslagcontainer.  

## <a name="using-security-groups-as-a-best-practice"></a>Beveiligingsgroepen gebruiken als best practice

Als u het toegangsbeheer wilt vereenvoudigen, kunt u beveiligingsgroepen gebruiken om rollen aan individuen en groepen toe te wijzen. Beveiligingsgroepen kunnen worden gemaakt om persona's of functies in uw organisatie die toegang nodig hebben tot Synapse-resources of -artefacten, te spiegelen.  Aan deze beveiligingsgroepen op basis van een persona kunnen vervolgens een of meer Azure-rollen, Synapse-rollen, SQL-machtigingen of Git-machtigingen worden toegewezen. Met goed gekozen beveiligingsgroepen is het makkelijk om aan een gebruiker de vereiste machtigingen toe te wijzen door deze toe te voegen aan de juiste beveiligingsgroep. 

>[!Note]
>Als u beveiligingsgroepen gebruikt voor het beheren van toegang, wordt er door Azure Active Directory extra latentie geïntroduceerd, waardoor het even duurt totdat wijzigingen merkbaar zijn. 

## <a name="access-control-enforcement-in-synapse-studio"></a>Afdwinging van toegangsbeheer in Synapse Studio

Synapse Studio werkt anders afhankelijk van uw machtigingen en de huidige modus:
- **De modus Synapse live:** Synapse Studio voorkomt dat u gepubliceerde inhoud kunt bekijken, inhoud kunt publiceren of andere acties kunt ondernemen als u niet over de vereiste machtigingen beschikt.  In sommige gevallen wordt voorkomen dat u codeartefacten maakt die u niet kunt gebruiken of opslaan. 
- **De Git-modus:** Als u Git-machtigingen hebt waarmee u wijzigingen in de huidige vertakking kunt doorvoeren, is de doorvoeractie toegestaan, zelfs als u niet gemachtigd bent om wijzigingen naar de llveservice te publiceren.  

In sommige gevallen is het mogelijk om codeartefacten te maken, zelfs als u niet beschikt over de toestemming om te publiceren of door te voeren. Op die manier kunt u code uitvoeren (met de vereiste uitvoeringsmachtigingen). [Meer informatie](./synapse-workspace-understand-what-role-you-need.md) over de rollen die voor algemene taken zijn vereist. 

Als een functie is uitgeschakeld in Synapse Studio, wordt in de knop Info de vereiste machtiging weergegeven. Gebruik de [Handleiding Synapse RBAC-rollen](./synapse-workspace-synapse-rbac-roles.md#synapse-rbac-actions-and-the-roles-that-permit-them) als u wilt opzoeken welke rol de ontbrekende machtiging heeft.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [RBAC voor Synapse](./synapse-workspace-synapse-rbac.md)
- Meer informatie over [RBAC-rollen voor Synapse](./synapse-workspace-synapse-rbac-roles.md)
- Meer informatie over het [instellen van toegangsbeheer](./how-to-set-up-access-control.md) voor een Synapse-werkruimte met behulp van beveiligingsgroepen.
- Meer informatie over [hoe u Synapse RBAC-roltoewijzingen kunt controleren](./how-to-review-synapse-rbac-role-assignments.md)
- Meer informatie over [hoe u Synapse RBAC-roltoewijzingen kunt beheren](./how-to-manage-synapse-rbac-role-assignments.md)
