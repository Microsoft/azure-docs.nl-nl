---
title: Gegevens in ADF detecteren en verkennen met controle sfeer liggen
description: Meer informatie over het detecteren, verkennen van gegevens in Azure Data Factory met behulp van controle sfeer liggen
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
manager: shwang
ms.custom: seo-lt-2019
ms.date: 01/15/2021
ms.openlocfilehash: accb9bbf195daa3d25e1aed109e36ef309083385
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2021
ms.locfileid: "99805306"
---
# <a name="discover-and-explore-data-in-adf-using-purview"></a>Gegevens in ADF detecteren en verkennen met controle sfeer liggen

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel gaat u een Azure controle sfeer liggen-account registreren bij een Data Factory. Met deze verbinding kunt u Azure controle sfeer liggen-assets ontdekken en ermee werken via ADF-mogelijkheden. 

U kunt de volgende taken uitvoeren in ADF: 
- Het zoekvak bovenaan gebruiken om Purview-assets te vinden op basis van trefwoorden 
- De gegevens begrijpen op basis van metagegevens, afkomst en aantekeningen 
- Deze gegevens koppelen aan uw data factory met gekoppelde services of gegevens sets 

## <a name="prerequisites"></a>Vereisten 
- [Azure Purview-account](../purview/create-catalog-portal.md) 
- [Data Factory](./quickstart-create-data-factory-portal.md) 
- [Een Azure controle sfeer liggen-account verbinden met Data Factory](./connect-data-factory-to-azure-purview.md) 

## <a name="using-azure-purview-in-data-factory"></a>Azure controle sfeer liggen gebruiken in Data Factory 

Het gebruik van Azure controle sfeer liggen in Data Factory vereist dat u toegang hebt tot dat controle sfeer liggen-account. Data Factory door uw controle sfeer liggen-machtiging. Als u bijvoorbeeld een curator-machtiging hebt, kunt u meta gegevens bewerken die zijn gescand door Azure controle sfeer liggen. 

### <a name="data-discovery-search-datasets"></a>Gegevens detectie: Zoek gegevens sets 

Als u gegevens wilt detecteren die zijn geregistreerd en gescand door Azure controle sfeer liggen, kunt u de zoek balk aan de bovenkant van Data Factory Portal gebruiken. Zorg ervoor dat u Azure controle sfeer liggen selecteert om te zoeken naar alle gegevens van uw organisatie. 

:::image type="content" source="./media/data-factory-purview/search-dataset.png" alt-text="Scherm afbeelding voor het uitvoeren van gegevens sets.":::

### <a name="actions-that-you-can-perform-over-datasets-with-data-factory-resources"></a>Acties die u kunt uitvoeren over gegevens sets met Data Factory resources 
U kunt rechtstreeks een gekoppelde service, gegevensset of gegevens stroom maken via de data die u zoekt door Azure controle sfeer liggen.

:::image type="content" source="./media/data-factory-purview/actions-over-purview-data.png" alt-text="Scherm afbeelding die laat zien hoe u rechtstreeks een gekoppelde service, gegevensset of gegevens stroom kunt maken via de zoek opdracht van Azure controle sfeer liggen.":::

##  <a name="nextsteps"></a>Volgende stappen 

- [Azure Data Factory assets registreren en scannen in azure controle sfeer liggen](../purview/register-scan-azure-synapse-analytics.md)
- [Gegevens zoeken in azure controle sfeer liggen Data Catalog](../purview/how-to-search-catalog.md)