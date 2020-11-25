---
title: Over het verplaatsingsproces in Azure Resource Mover
description: Meer informatie over het proces van het verplaatsen van resources tussen regio's met Azure Resource Mover
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: overview
ms.date: 09/09/2020
ms.author: raynew
ms.openlocfilehash: 5261904dd1ee7f280209015d8f756a055dfab57e
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95522938"
---
# <a name="about-the-move-process"></a>Over het verplaatsingsproces

Met [Azure Resource Mover](overview.md) kunt u Azure-resources tussen Azure-regio's verplaatsen. In dit artikel vindt u een overzicht van de onderdelen die door Resource Mover worden gebruikt. Tevens wordt het verplaatsingsproces beschreven. 


## <a name="components"></a>Onderdelen

Deze onderdelen worden gebruikt tijdens het verplaatsen tussen regio's.

**Onderdeel** | **Details**
--- | ---
**Resource Mover** |  Resource Mover wordt met [Azure-resourceproviders](../azure-resource-manager/management/resource-providers-and-types.md) gecoördineerd om de verplaatsing van resources tussen regio's te organiseren. Resource Mover analyseert bronafhankelijkheden en onderhoudt en beheert de status van resources tijdens het verplaatsingsproces. 
**Verplaatsingsverzameling** |  Een verplaatsingsverzameling is een object van [Azure Resource Manager](../azure-resource-manager/management/overview.md).<br/><br/> De verplaatsingsverzameling wordt voor elke gekoppelde combinatie van bron- en doelregio's in een abonnement gemaakt tijdens het verplaatsingsproces tussen regio's. De verzameling bevat metagegevens en configuratie-informatie over de resources die u wilt verplaatsen.<br/><br/>Resources die zijn toegevoegd aan een verplaatsingsverzameling moeten zich in hetzelfde abonnement bevinden, maar kunnen zich in verschillende resourcegroepen bevindt. 
**Verplaatsingsresource** | Wanneer u een resource aan een verzameling toevoegt, wordt de resource door Resource Mover als een verplaatsingsresource getraceerd.<br/><br/> Resource Mover houdt informatie bij over alle verplaatsingsresources in de verplaatsingsverzameling en onderhoudt een een-op-een-relatie tussen de bron- en de doelresource. 
**Afhankelijkheden** | Resources die u aan een verzameling toevoegt, worden gevalideerd en er wordt gecontroleerd of resources afhankelijkheden hebben die zich niet in de verplaatsingsverzameling bevinden.<br/><br/> Na het identificeren van afhankelijkheden voor een resource, kunt u deze afhankelijkheden toevoegen aan de verplaatsingsverzameling en ze ook verplaatsen, of u kunt alternatieve, bestaande resources in de doelregio selecteren. Alle afhankelijkheden moeten worden opgelost voordat u met de verplaatsing begint. 



## <a name="move-region-process"></a>Proces van het verplaatsen van regio's 

![Diagram met de verplaatsingsstappen](./media/about-move-process/move-steps.png)

Elke verplaatsingsresource voor volgt de stappen in het overzicht.

**Stap** | **Details** | **Status/problemen**
--- | --- | --- 
**Stap 1: Resources selecteren** | Selecteer een resource. De resource wordt toegevoegd aan de verplaatsingsverzameling. | De resourcestatus gaat over *Voorbereiding in behandeling*.<br/><br/> Als de resource afhankelijkheden heeft, geeft *Afhankelijkheid valideren* aan dat u afhankelijke resources moet toevoegen aan de verplaatsingsverzameling.
**Stap 2: Afhankelijkheden valideren** | Het validatieproces starten.<br/><br/> Als bij validatie blijkt dat afhankelijke resources in behandeling zijn, voegt u deze toe aan de verplaatsingsverzameling. <br/><br/> Voeg alle afhankelijke resources toe, ook als u deze niet wilt verplaatsen. U kunt later opgeven dat de resources die u verplaatst, gebruik moeten maken van andere resources in de doelregio.<br/><br/> U voert opnieuw een validatie uit totdat er geen openstaande afhankelijkheden meer zijn. | Nadat alle afhankelijkheden zijn toegevoegd en de validatie is uitgevoerd, gaat de status van de resource probleemloos over in *Voorbereiding in behandeling*.
**Stap 3: Voorbereiden** | Het voorbereidingsproces starten. De voorbereidingsstappen zijn afhankelijk van de resources die u wilt verplaatsen:<br/><br/> - **Staatloze resources**: Staatloze resources bevatten alleen configuratiegegevens. Voor deze resources is geen doorlopende replicatie van gegevens nodig om ze te kunnen verplaatsen. Voorbeelden hiervan zijn virtuele Azure-netwerken (VNets), netwerkadapters, load balancers en netwerkbeveiligingsgroepen. Voor dit type resource genereert het voorbereidingsproces een Azure Resource Manager-sjabloon.<br/><br/> - **Stateful resources**: Stateful resources bevatten informatie over de configuratie en gegevens die moeten worden verplaatst. Voorbeelden hiervan zijn Azure-VM's en Azure SQL-databases. Het voorbereidingsproces verschilt voor elke resource. Het kan bijvoorbeeld gaan om het repliceren van de bronresource naar de doelresource. | Bij het starten gaat de resourcestatus over in *Voorbereiden wordt uitgevoerd*.<br/><br/> Nadat de voorbereiding is voltooid, gaat de resourcestatus probleemloos over in *Verplaatsing starten in behandeling*.<br/><br/> Na een mislukte verwerking gaat de status over in *Voorbereiden mislukt*.
**Stap 4: Verplaatsing starten** | Het verplaatsingsproces starten. De verplaatsingsmethode is afhankelijk van het resourcetype:<br/><br/> - **Staatloos**: Voor stateless resources implementeert het verplaatsingsproces meestal een geïmporteerde sjabloon in de doelregio. De sjabloon is gebaseerd op de instellingen van de bronresource en op eventuele handmatige wijzigingen die u aan de doelinstellingen aanbrengt.<br/><br/> - **Stateful**: Voor stateful resources kan het verplaatsingsproces ook de resource maken of een kopie inschakelen in de doel regio.<br/><br/>  Alleen voor stateful resources kan het starten van een verplaatsing leiden tot downtime van bronresources. Bijvoorbeeld VM's en SQL. | Bij het starten gaat de status over in *Startverplaatsing wordt uitgevoerd*.<br/><br/> Na een geslaagde startverplaatsing gaat de status probleemloos over in *Doorvoerverplaatsing in behandeling*. <br/><br/> Na een mislukte verplaatsing gaat de status over in *Startverplaatsing mislukt*.
**Stap 5, optie 1: Verplaatsing negeren** | Na de startverplaatsing kunt u beslissen of u door wilt gaan met een volledige verplaatsing. Als dat niet het geval is, kunt u de verplaatsing negeren. Resource Mover verwijdert de resources die in het doel zijn gemaakt. Het replicatieproces voor stateful resources wordt na het verwijderen voortgezet. Deze optie is handig voor het testen. | Tijdens het verwijderen van de resources gaat de status over in *Verwijderen wordt uitgevoerd*.<br/><br/> Als de resources zijn verwijderd, gaat de status probleemloos over in *Startverplaatsing in behandeling*.<br/><br/> Als de verwijdering mislukt, gaat de status over in *Verwijderingsverplaatsing mislukt*. 
**Stap 5, optie 2: Doorvoerverplaatsing** | Als u na de startverplaatsing wilt doorgaan met een volledige verplaatsing, controleert u de resources in de doelregio en wanneer u klaar bent, voert u de verplaatsing door.<br/><br/> Alleen voor stateful resources kan het doorvoeren ertoe leiden dat bronresources, zoals VM's of SQL, ontoegankelijk worden. | Als u de verplaatsing doorvoert, gaat de resourcestatus over in *Doorvoerverplaatsing wordt uitgevoerd**.<br/><br/> Na een geslaagde doorvoer gaat de resourcestatus probleemloos over in *Doorvoerverplaatsing voltooid*.<br/><br/> Bij een mislukte doorvoer gaat de status over in *Doorvoerverplaatsing mislukt*.
**Stap 6: Bron verwijderen** | Nadat u de verplaatsing hebt doorgevoerd en de resources in de doelregio hebt gecontroleerd, kunt u de bronresource verwijderen. | Nadat de verplaatsing is doorgevoerd, gaat de resourcestatus over in *Verwijderen bron in behandeling*.


## <a name="move-region-states"></a>Status van verplaatsingsregio

Het verplaatsingsproces kent een aantal statussen en problemen die zich tijdens elke status kunnen voordoen. Deze worden in het stroomdiagram opgesomd.

![Stroomdiagram voor mogelijke statussen en problemen](./media/about-move-process/process.png)


### <a name="dependency-analysis"></a>Afhankelijkheidsanalyse

Tijdens het verplaatsingsproces wordt u mogelijk gevraagd om afhankelijkheden te valideren indien:
- een resource afhankelijke resources gebruikt die zich niet in de verplaatsingsverzameling bevinden;
- een afhankelijke resource in de verplaatsingsverzameling eigen afhankelijkheden heeft die zich niet in de verplaatsingsverzameling bevinden;
- u de doelinstellingen voor de resource hebt gewijzigd en u afhankelijkheden opnieuw moet valideren.


### <a name="remove-resources"></a>Resources verwijderen

Als u een resource niet wilt verplaatsen, kunt u deze verwijderen uit de verplaatsingsverzameling. Over het algemeen wordt de resource vervolgens uit de verzameling verwijderd, samen met eventuele gekoppelde acties of objecten, zoals replicaties of opgeslagen sjablonen. Wat er precies gebeurt wanneer u een resource verwijdert, is afhankelijk van het type resource en de status van de resource wanneer u deze verwijdert. [Meer informatie](remove-move-resources.md).

## <a name="move-impact"></a>Impact verplaatsen

De tabel geeft een overzicht van wat er wordt beïnvloed wanneer u tussen verschillende regio's verplaatsingen uitvoert.

Gedrag | **Tussen regio's**
--- | --- | --- 
**Gegevens** | Resourcegegevens en metagegevens worden verplaatst.<br/><br/> Metagegevens worden tijdelijk opgeslagen om de status van resourceafhankelijkheden en -bewerkingen bij te houden.
**Resource** | De bronresource blijft intact om ervoor te zorgen dat apps blijven werken. Ze kunnen desgewenst na het verplaatsen worden verwijderd.<br/><br/> Er wordt een resource in de doelregio gemaakt.
**Verplaatsingsproces** | Proces in meerdere stappen waarvoor handmatige interventie en bewaking is vereist.
Testen. | Het testen van de verplaatsing is belangrijk, omdat de apps na de verplaatsing in de doelregio moeten blijven werken zoals mag worden verwacht.
Uitvaltijd (%) |  Er wordt geen gegevensverlies verwacht, maar wel enige downtime om resources te verplaatsen.



## <a name="next-steps"></a>Volgende stappen

Virtuele Azure-machines naar een andere regio [verplaatsen](tutorial-move-region-virtual-machines.md).
Azure SQL-resources naar een andere regio [verplaatsen](tutorial-move-region-sql.md).