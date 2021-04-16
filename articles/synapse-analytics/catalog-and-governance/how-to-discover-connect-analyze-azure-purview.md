---
title: Gegevens in Synapse ontdekken, verbinden en verkennen met behulp van Azure Purview
description: Handleiding voor het ontdekken van gegevens, het verbinden ervan en het verkennen ervan in Synapse
author: Rodrigossz
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: how-to
ms.date: 12/16/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.openlocfilehash: f98507fa72f4503700bf39393063dd1ecc650e91
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567890"
---
# <a name="discover-connect-and-explore-data-in-synapse-using-azure-purview"></a>Gegevens in Synapse ontdekken, verbinden en verkennen met behulp van Azure Purview 

In dit document leert u het type interacties dat u kunt uitvoeren bij het registreren van een Azure Purview-account bij Synapse. 

## <a name="prerequisites"></a>Vereisten 

- [Azure Purview-account](../../purview/create-catalog-portal.md) 
- [Synapse-werkruimte](../quickstart-create-workspace.md) 
- [Een Azure Purview-account verbinden met Synapse](quickstart-connect-azure-purview.md) 

## <a name="using-azure-purview-in-synapse"></a>Azure Purview gebruiken in Synapse 

Voor het gebruik van Azure Purview in Synapse moet u toegang hebben tot dat Purview-account. Synapse geeft uw machtiging Voor beeld door. Als u bijvoorbeeld de machtigingsrol beheerder hebt, kunt u metagegevens bewerken die zijn gescand door Azure Purview. 

### <a name="data-discovery-search-datasets"></a>Gegevensdetectie: gegevenssets zoeken 

Als u gegevens wilt ontdekken die zijn geregistreerd en gescand door Azure Purview, kunt u de zoekbalk bovenaan de Synapse-werkruimte gebruiken. Zorg ervoor dat u Azure Purview selecteert om te zoeken naar al uw organisatiegegevens. 

[![Zoeken naar Azure Purview-assets](./media/purview-access.png)](./media/purview-access.png#lightbox)

## <a name="azure-purview-actions"></a>Azure Purview-acties 

Hier is een lijst met de Azure Purview-functies die beschikbaar zijn in Synapse: 
- **Overzicht** van de metagegevens 
- Bekijk en **bewerk het schema** van de metagegevens met classificaties, woordenlijsttermen, gegevenstypen en beschrijvingen 
- View **lineage to** understand dependencies and do impact analysis. Zie gegevenseedage voor [meer informatie over](../../purview/catalog-lineage-user-guide.md)
- Contactpersonen weergeven en **bewerken om** te weten te komen wie een eigenaar of expert is voor een gegevensset 
- **Gerelateerd** aan inzicht in de hiërarchische afhankelijkheden van een specifieke gegevensset. Deze ervaring is handig om door de gegevenshiërarchie te bladeren.

## <a name="actions-that-you-can-perform-over-datasets-with-synapse-resources"></a>Acties die u kunt uitvoeren voor gegevenssets met Synapse-resources 

### <a name="connect-data-to-synapse"></a>Gegevens verbinden met Synapse 

- U kunt een nieuwe **gekoppelde service maken** in Synapse. Deze actie is vereist om gegevens te kopiëren naar Synapse of om ze in uw gegevenshub te hebben (voor ondersteunde gegevensbronnen zoals ADLSg2) 
- Voor objecten zoals bestanden, mappen of tabellen kunt  u rechtstreeks een nieuwe integratieset maken en gebruikmaken van een bestaande gekoppelde service als deze al is gemaakt 

We kunnen nog niet afleiden of er een bestaande gekoppelde service of integratie-gegevensset is. 

###  <a name="develop-in-synapse"></a>Ontwikkelen in Synapse 

Er zijn drie acties die u kunt uitvoeren: **Nieuw SQL-script,** **Nieuw notebook** en Nieuw **Gegevensstroom**. 

Met **New SQL Script** kunt u, afhankelijk van het type ondersteuning, het volgende doen: 
- Bekijk de bovenste 100 rijen om inzicht te krijgen in de vorm van de gegevens. 
- Een externe tabel maken op Synapse SQL database 
- De gegevens laden in een Synapse SQL database 
 
Met **Nieuw notebook** kunt u het volgende doen: 
- Gegevens laden in een Spark DataFrame 
- Maak een Spark-tabel (als u dat in parquet-indeling doet, wordt er ook een serverloze SQL-pooltabel gemaakt). 
 
Met **Nieuwe gegevensstroom kunt** u een integratiegegevensset maken die kan worden gebruikt als bron in een gegevensstroompijplijn. Gegevensstroom is een ontwikkelfunctie zonder code om gegevenstransformatie uit te voeren. Voor meer informatie over het [gebruik van de gegevensstroom in Synapse](../quickstart-data-flow.md).

##  <a name="nextsteps"></a>Volgende stappen 

- [Azure Synapse-assets registreren en scannen in Azure Purview](../../purview/register-scan-azure-synapse-analytics.md)
- [Gegevens doorzoeken in Azure Purview Data Catalog](../../purview/how-to-search-catalog.md)
