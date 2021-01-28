---
title: Een Data Factory verbinden met Azure Purview
description: Meer informatie over het verbinden van een Data Factory met Azure controle sfeer liggen
services: data-factory
ms.author: lle
author: lrtoyou1223
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: d1a07a3733bddc1516a4d2e19f3cac31a7702954
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944936"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Data Factory verbinding maken met Azure controle sfeer liggen (preview)
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt uitgelegd hoe u data factory verbinding maakt met Azure controle sfeer liggen en hoe u gegevens afkomst van ADF-activiteiten rapporteert gegevens, gegevens stroom en uitvoeren SSIS-pakket.

## <a name="connect-data-factory-to-azure-purview"></a>data factory verbinden met Azure controle sfeer liggen
Azure controle sfeer liggen is een nieuwe Cloud service die door gegevens gebruikers kan worden gebruikt om gegevens beheer centraal te beheren in hun data-erfgoed in de Cloud en on-premises omgevingen. U kunt uw data factory aansluiten op Azure controle sfeer liggen en met de verbinding kunt u gebruikmaken van Azure controle sfeer liggen voor het vastleggen van afkomst-gegevens van Copy, data flow en execute SSIS package. Zie [How to connect Azure Data Factory and Azure controle sfeer liggen](../purview/how-to-link-azure-data-factory.md)(Engelstalig) voor informatie over het registreren van Data Factory in azure controle sfeer liggen. 

## <a name="report-lineage-data-to-azure-purview"></a>Afkomst gegevens rapporteren aan Azure controle sfeer liggen
Wanneer klanten Kopieer-, gegevens stroom-of uitvoering van SSIS-pakket activiteit uitvoeren in Azure Data Factory, kunnen klanten de afhankelijkheids relatie verkrijgen en een globaal overzicht van het hele werk stroom proces over gegevens bronnen en bestemming hebben.
Zie [Data Factory afkomst](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities)voor meer informatie over het verzamelen van afkomst van Azure Data Factory.

## <a name="next-steps"></a>Volgende stappen
[Gebruikers handleiding voor catalogus afkomst](../purview/catalog-lineage-user-guide.md)

[Zelf studie: Push Data Factory afkomst-gegevens naar Azure controle sfeer liggen](turorial-push-lineage-to-purview.md)