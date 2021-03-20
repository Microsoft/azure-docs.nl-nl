---
title: Wat is Azure Resource Mover?
description: Over Azure Resource Mover
author: rayne-wiselman
manager: evansma
ms.service: azure-migrate
ms.topic: overview
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 06d6352f018238318c3bb4625ae86a2974f14569
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99820160"
---
# <a name="what-is-azure-resource-mover"></a>Wat is Azure Resource Mover?

In dit artikel vindt u een overzicht van de service Azure Resource Mover. Met Resource Mover kunt u Azure-resources tussen Azure-regio's verplaatsen.

U kunt resources vanwege de volgende doeleinden naar verschillende Azure-regio's verplaatsen:

- **Afstemming met de start van een regio**: Verplaats resources naar een nieuw geïntroduceerde Azure-regio die niet eerder beschikbaar was.
- **Afstemming op services/functies**: Verplaats resources om te profiteren van services of functies die in een bepaalde regio beschikbaar zijn.
- **Reageren op bedrijfsontwikkelingen**: Verplaats resources naar een regio als reactie op bedrijfswijzigingen, zoals fusies of overnames.
- **Afstemming op nabijheid**: Verplaats resources naar een regio die lokaal is voor uw bedrijf.
- **Voldoen aan de gegevensvereisten**: Verplaats resources om af te stemmen met de vereisten voor gegevenslocatie of de behoeften aan gegevensclassificatie.
- **Reageren op implementatievereisten**: Verplaats resources die foutief zijn geïmplementeerd of verplaats als reactie op capaciteitsbehoeften.
- **Reageren uit buitengebruikstelling**: Verplaats resources omdat een regio buiten gebruik is gesteld.


## <a name="why-use-resource-mover"></a>Waarom zou u Azure Resource Mover gebruiken?

Resource Mover biedt:

- Eén hub voor het verplaatsen van resources binnen regio's.
- Beperkte verplaatsingstijd en complexiteit. Alles wat u nodig hebt, bevindt zich op één locatie.
- Een eenvoudige en consistente ervaring voor het verplaatsen van verschillende typen Azure-resources.
- Een eenvoudige manier om afhankelijkheden te identificeren binnen resources die u wilt verplaatsen. Dit helpt om gerelateerde resources samen te verplaatsen zodat alles na de verplaatsing naar verwachting werkt in de doelregio.
- Automatische opschoning van resources in de bronregio, als u deze na de verplaatsing wilt verwijderen.
- Testen. U kunt een verplaatsing uitproberen en deze vervolgens verwijderen als u geen volledige verplaatsing wilt uitvoeren.

## <a name="move-across-regions"></a>Verplaatsen tussen regio's

Als u resources wilt verplaatsen tussen regio's, selecteert u de resources die u wilt verplaatsen. Resource Mover valideert deze resources en lost eventuele afhankelijkheden van andere resources op. Als er afhankelijkheden zijn, hebt u een aantal opties:
- Verplaats de afhankelijke resources naar de doelregio.
- Verplaats de afhankelijke resources niet, maar gebruik in plaats daarvan gelijkwaardige resources in de doelregio.

Nadat alle afhankelijkheden zijn opgelost, wordt u door middel van een eenvoudig verplaatsingsproces geleid.

1. U voert een eerste verplaatsing uit.
2. Na de eerste verplaatsing kunt u beslissen of u de verplaatsing wilt doorvoeren en verwijderen of deze wilt negeren.
3. Na de verplaatsing, kunt u beslissen of u de resources op de bronlocatie wilt verwijderen.

U kunt resources verplaatsen tussen regio's in de Resource Mover-hub of vanuit een resourcegroep. [Meer informatie](select-move-tool.md)

## <a name="what-resources-can-i-move-across-regions"></a>Welke resources kan ik tussen regio's verplaatsen?

Met Resource Mover kunt u momenteel de volgende resources tussen regio's verplaatsen:

- Azure-VM's en gekoppelde schijven
- Versleutelde virtuele machines en gekoppelde schijven van Azure. Dit omvat Vm's waarvoor Azure Disk Encryption is ingeschakeld en Azure-Vm's met behulp van standaard versleuteling aan de server zijde (beide met door het platform beheerde sleutels en door de klant beheerde sleutels)
- NIC’s
- Beschikbaarheidssets 
- Virtuele netwerken van Azure. 
- Openbare IP-adressen
- Netwerkbeveiligingsgroepen (NSG's)
- Interne en openbare load balancers 
- Azure SQL-databases en elastische pools


## <a name="next-steps"></a>Volgende stappen

[Meer informatie](about-move-process.md) over Resource Mover-onderdelen en het verplaatsingsproces.
