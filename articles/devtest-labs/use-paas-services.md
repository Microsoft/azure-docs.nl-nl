---
title: PaaS-Services (platform-as-a-Service) gebruiken in Azure DevTest Labs
description: Meer informatie over het gebruik van platform-as-a-Service-(Pass) services in Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: eec37527386098174906dc2737d7b763241da3f2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85478736"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>PaaS-Services (platform-as-a-Service) gebruiken in Azure DevTest Labs
PaaS wordt ondersteund in DevTest Labs via de omgevings functie. Omgevingen in DevTest Labs worden ondersteund door vooraf geconfigureerde Azure Resource Manager sjablonen in een Git-opslag plaats. Omgevingen kunnen zowel PaaS-als IaaS-resources bevatten. Ze bieden u de mogelijkheid om complexe systemen te maken die Azure-resources zoals virtuele machines, data bases, virtuele netwerken en web-apps kunnen bevatten, die zijn aangepast om samen te werken. Deze sjablonen bieden een consistente implementatie en een verbeterd beheer van omgevingen met broncode beheer. 

Een correct ingesteld systeem biedt de volgende scenario's: 

- Ontwikkel aars die onafhankelijke en meerdere omgevingen hebben
- Asynchroon testen op verschillende configuraties
- Integratie in faserings-en productie pijplijnen zonder sjabloon wijzigingen
- Als zowel ontwikkel machines als omgevingen binnen hetzelfde lab worden ontwikkeld, wordt het gemak voor beheer en kosten rapportage verbeterd.  

De resource provider DevTest Labs maakt bronnen voor de gebruikers van de test omgeving, zodat er geen extra machtigingen aan de test gebruiker moeten worden gegeven om het gebruik van de PaaS-resources in te scha kelen. In de volgende afbeelding ziet u een Service Fabric cluster als een omgeving in het lab.

![Service Fabric cluster als een omgeving](./media/create-environment-service-fabric-cluster/cluster-created.png)

Zie voor meer informatie over het instellen van omgevingen [multi-VM-omgevingen en Paas-resources maken met Azure Resource Manager sjablonen](devtest-lab-create-environment-from-arm.md). DevTest Labs heeft een open bare opslag plaats van Azure Resource Manager sjablonen die u kunt gebruiken om omgevingen te maken zonder dat u zelf een externe GitHub-bron hoeft te maken. Zie [open bare omgevingen configureren en gebruiken in azure DevTest Labs](devtest-lab-configure-use-public-environments.md)voor meer informatie over open bare omgevingen.

In grote organisaties bieden ontwikkel teams doorgaans omgevingen zoals aangepaste/geïsoleerde test omgevingen. Er zijn mogelijk omgevingen die door alle teams in een bedrijfs eenheid of een divisie moeten worden gebruikt. De IT-groep wil mogelijk hun omgevingen leveren die kunnen worden gebruikt door alle teams in de organisatie.  

## <a name="customizations"></a>Aanpassingen

#### <a name="sandbox"></a>Sandbox 
De eigenaar van het Lab kan de test omgevingen aanpassen om de rol van de gebruiker van de **lezer** te wijzigen in **Inzender** binnen de resource groep. Deze mogelijkheid bevindt zich op de pagina **Lab-instellingen** onder de **configuratie en het beleid** van het lab. Met deze wijziging in de rol kan de gebruiker resources toevoegen aan of verwijderen uit die omgeving. Als u de toegang verder wilt beperken, gebruikt u Azure-beleid. Met deze functie kunt u de resources of configuratie aanpassen zonder de toegang op het abonnements niveau.

#### <a name="custom-tokens"></a>Aangepaste tokens
Er zijn een aantal aangepaste Lab-gegevens die zich buiten de resource groep bevinden en die specifiek zijn voor omgevingen waar de sjabloon toegang toe heeft. Hier volgen een paar van deze manieren: 

- Lab-netwerk identificatie
- Locatie
- Het opslag account waarin de Resource Manager-sjabloon bestanden worden opgeslagen. 
 
#### <a name="lab-virtual-network"></a>Virtueel Lab-netwerk
In de [verbinding met omgevingen met het virtuele netwerk van het lab](connect-environment-lab-virtual-network.md) wordt beschreven hoe u de Resource Manager-sjabloon voor het gebruik van het `$(LabSubnetId)` token wijzigt. Wanneer een omgeving wordt gemaakt, `$(LabSubnetId)` wordt het token vervangen door het eerste subnet, waarbij de optie **gebruiken bij het maken van virtuele machines** is ingesteld op **waar**. Zo kan onze omgeving eerder gemaakte netwerken gebruiken. Als u de Resource Manager-sjablonen in omgevingen testen als fase ring en productie wilt gebruiken, gebruikt u `$(LabSubnetId)` als standaard waarde in een resource manager-sjabloon parameter. 

#### <a name="environment-storage-account"></a>Omgevings opslag account
DevTest Labs ondersteunt het gebruik van [geneste Resource Manager-sjablonen](../azure-resource-manager/templates/linked-templates.md). In het artikel [[geneste Azure Resource Manager sjablonen implementeren voor test omgevingen](deploy-nested-template-environments.md) wordt uitgelegd hoe u  `_artifactsLocation` en `_artifactsLocationSasToken` tokens gebruikt om een URI te maken naar een resource manager-sjabloon in dezelfde map als of in een geneste map van de hoofd sjabloon. Zie de sectie **implementatie artefacten** in de [hand leiding voor de aanbevolen procedures van Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)voor meer informatie over deze twee tokens.

## <a name="user-experience"></a>Gebruikerservaring

## <a name="developer"></a>Ontwikkelaar
Ontwikkel aars gebruiken dezelfde werk stroom voor het maken van een virtuele machine voor het maken van een specifieke omgeving. Ze selecteren de omgeving versus de installatie kopie van de computer en voeren de benodigde gegevens in die vereist zijn voor de sjabloon. Elke ontwikkelaar die een omgeving heeft, kan wijzigingen aanbrengen en verbeterde fout opsporing in de interne lus. De omgeving kan op elk gewenst moment worden gemaakt met behulp van de meest recente sjabloon.  Met deze functie kunnen de omgevingen worden vernietigd en opnieuw worden gemaakt om de downtime te verminderen van het hand matig maken van het systeem of het herstellen van fouten testen.  

### <a name="testing"></a>Testen
Met DevTest Labs-omgevingen kunnen specifieke code en configuraties asynchroon worden getest. De algemene procedure is het instellen van de omgeving met de code van de afzonderlijke pull-aanvraag en het starten van geautomatiseerd testen. Zodra de geautomatiseerde tests zijn uitgevoerd om te worden voltooid, kunnen hand matige tests worden uitgevoerd op basis van de specifieke omgeving. Dit proces wordt doorgaans uitgevoerd als onderdeel van de CI/CD-pijp lijn. 

## <a name="management-experience"></a>Beheer ervaring

### <a name="cost-tracking"></a>Kosten bijhouden
De functie voor het bijhouden van kosten omvat Azure-resources in de verschillende omgevingen als onderdeel van de totale kosten trend. De kosten per resources hebben geen uitsplitsing van de verschillende resources binnen de omgeving, maar de omgeving wordt als één kosten weer gegeven.

### <a name="security"></a>Beveiliging
Een correct geconfigureerd Azure-abonnement met DevTest Labs kan [de toegang tot Azure-bronnen alleen beperken via het lab](devtest-lab-add-devtest-user.md). Met omgevingen kan een Lab-eigenaar gebruikers toegang geven tot PaaS-bronnen met goedgekeurde configuraties zonder dat ze toegang hebben tot andere Azure-resources. In het scenario waarin Lab-gebruikers omgevingen aanpassen, kan de eigenaar van het lab toegang tot de Inzender toestaan. Met de Inzender toegang kan de test gebruiker Azure-resource alleen toevoegen aan of verwijderen uit de beheerde resource groep. Het maakt het gemakkelijker om te traceren en te beheren, en Hiermee wordt de gebruiker in staat stellen om toegang te krijgen tot het abonnement.

### <a name="automation"></a>Automation
Automation is een belang rijk onderdeel voor een grote schaal, effectief ecosysteem. Automatisering is nodig voor het beheren of bijhouden van meerdere omgevingen in abonnementen en Labs.

### <a name="cicd-pipeline"></a>CI/CD-pijp lijn
PaaS Services in DevTest Labs kunnen helpen de CI/CD-pijp lijn te verbeteren door gerichte implementaties te hebben, waarbij toegang wordt bepaald door het lab.

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over omgevingen: 

- 
- [Multi-VM-omgevingen en PaaS-resources maken met Azure Resource Manager-sjablonen](devtest-lab-create-environment-from-arm.md)
- [Open bare omgevingen configureren en gebruiken in Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Een omgeving met een zelfstandig Service Fabric cluster maken in Azure DevTest Labs](create-environment-service-fabric-cluster.md)
- [Een omgeving verbinden met het virtuele netwerk van uw Lab in Azure DevTest Labs](connect-environment-lab-virtual-network.md)
- [Omgevingen integreren in uw Azure DevOps CI/CD-pijp lijnen](integrate-environments-devops-pipeline.md)
 





