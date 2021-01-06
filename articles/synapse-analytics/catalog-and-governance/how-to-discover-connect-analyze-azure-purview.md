---
title: Gegevens detecteren, verbinden en verkennen in Synapse met behulp van Azure controle sfeer liggen
description: Meer informatie over het detecteren van gegevens, verbinding maken en ze verkennen in Synapse
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 12/16/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 7c6b25fd3615fa76bc76e6d360f4c76a21a9ad02
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918264"
---
# <a name="discover-connect-and-explore-data-in-synapse-using-azure-purview"></a>Gegevens detecteren, verbinden en verkennen in Synapse met behulp van Azure controle sfeer liggen 

> [!IMPORTANT]
> De integratie tussen Azure Synapse Analytics en Azure controle sfeer liggen is momenteel in de preview-versie. Als u geïnteresseerd bent in azure controle sfeer liggen in Synapse, kunt u contact maken met uw micro soft-verkoop medewerker. 

In dit document vindt u informatie over het type interacties dat u kunt uitvoeren bij het registreren van een Azure controle sfeer liggen-account in Synapse. 

## <a name="prerequisites"></a>Vereisten 

- [Azure controle sfeer liggen-account](../../purview/create-catalog-portal.md) 
- [Synapse-werk ruimte](../quickstart-create-workspace.md) 
- [Een Azure controle sfeer liggen-account verbinden met Synapse](quickstart-connect-azure-purview.md) 

## <a name="using-azure-purview-in-synapse"></a>Azure controle sfeer liggen gebruiken in Synapse 

Voor het gebruik van Azure controle sfeer liggen in Synapse moet u toegang hebben tot dat controle sfeer liggen-account. Synapse door uw controle sfeer liggen-machtiging. Als u bijvoorbeeld een curator-machtiging hebt, kunt u meta gegevens bewerken die zijn gescand door Azure controle sfeer liggen. 

### <a name="data-discovery-search-datasets"></a>Gegevens detectie: Zoek gegevens sets 

Als u gegevens wilt detecteren die zijn geregistreerd en gescand door Azure controle sfeer liggen, kunt u de zoek balk in het midden van de Synapse-werk ruimte gebruiken. Zorg ervoor dat u Azure controle sfeer liggen selecteert om te zoeken naar alle gegevens van uw organisatie. 

## <a name="azure-purview-actions"></a>Azure controle sfeer liggen-acties 

Hier volgt een overzicht van de Azure controle sfeer liggen-functies die beschikbaar zijn in Synapse: 
- **Overzicht** van de meta gegevens 
- Het **schema** van de meta gegevens weer geven en bewerken met classificaties, terminologie van de woorden lijst, gegevens typen en beschrijvingen 
- Bekijk **afkomst** om de afhankelijkheden te begrijpen en impact analyse uit te voeren. Zie [afkomst](../../purview/catalog-lineage-user-guide.md) voor meer informatie over.
- **Contact personen** weer geven en bewerken om te weten wie een eigenaar of expert is via een gegevensset 
- **Gerelateerd** om inzicht te krijgen in de hiërarchische afhankelijkheden van een specifieke gegevensset. Deze ervaring is handig om door de gegevens hiërarchie te bladeren.

## <a name="actions-that-you-can-perform-over-datasets-with-synapse-resources"></a>Acties die u kunt uitvoeren over gegevens sets met Synapse-resources 

### <a name="connect-data-to-synapse"></a>Gegevens verbinden met Synapse 

- U kunt een **nieuwe gekoppelde service** maken op Synapse. Deze actie is vereist om gegevens te kopiëren naar Synapse of ze te hebben in uw data hub (voor ondersteunde gegevens bronnen zoals ADLSg2) 
- Voor objecten als bestanden, mappen of tabellen kunt u rechtstreeks een **nieuwe integratie gegevensset** maken en gebruikmaken van een bestaande gekoppelde service als deze al is gemaakt 

We kunnen nog niet afleiden als er een bestaande gekoppelde service of integratie-gegevensset bestaat. 

###  <a name="develop-in-synapse"></a>Ontwikkelen in Synapse 

Er zijn drie acties die u kunt uitvoeren: **Nieuw SQL-script**, **Nieuw notitie blok** en **nieuwe gegevens stroom**. 

Met het **nieuwe SQL-script**, afhankelijk van het type ondersteuning, kunt u het volgende doen: 
- Bekijk de bovenste 100 rijen om inzicht te krijgen in de vorm van de gegevens. 
- Een externe tabel maken op basis van Synapse SQL database 
- De gegevens laden in een Synapse-SQL database 
 
Met een **nieuwe notebook** kunt u het volgende doen: 
- Gegevens laden in een Spark-data frame 
- Een Spark-tabel maken (als u dat wel doet, wordt er ook een serverloze SQL-groeps tabel gemaakt). 
 
Met een **nieuwe gegevens stroom** kunt u een integratie-gegevensset maken die kan worden gebruikt als bron in een gegevensstroom pijplijn. De gegevens stroom is een ontwikkelaar zonder code om gegevens transformatie uit te voeren. Voor meer informatie over het [gebruik van data flow in Synapse](../quickstart-data-flow.md).

##  <a name="nextsteps"></a>Volgende stappen 

- [Azure Synapse-assets registreren en controleren in azure controle sfeer liggen](../../purview/register-scan-azure-synapse-analytics.md)
- [Gegevens zoeken in azure controle sfeer liggen Data Catalog](../../purview/how-to-search-catalog.md)