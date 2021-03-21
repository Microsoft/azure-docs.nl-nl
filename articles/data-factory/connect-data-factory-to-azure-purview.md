---
title: Een Data Factory verbinden met Azure Purview
description: Meer informatie over het verbinden van een Data Factory met Azure controle sfeer liggen
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: 44f093f96d0f4653a6fcca94aaa97264c93e3c7d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101727937"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Data Factory verbinding maken met Azure controle sfeer liggen (preview)
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt uitgelegd hoe u Data Factory verbinding maakt met Azure controle sfeer liggen en hoe u gegevens afkomst van Azure Data Factory activiteiten rapporteert gegevens, gegevens stroom en uitvoeren SSIS-pakket.


## <a name="connect-data-factory-to-azure-purview"></a>Data Factory verbinden met Azure controle sfeer liggen
Azure controle sfeer liggen is een nieuwe Cloud service die door gegevens gebruikers kan worden gebruikt om gegevens beheer centraal te beheren in hun data-erfgoed in de Cloud en on-premises omgevingen. U kunt uw Data Factory verbinden met Azure controle sfeer liggen en met de verbinding kunt u Azure controle sfeer liggen gebruiken voor het vastleggen van afkomst-gegevens van Copy, data flow en execute SSIS package. U hebt twee manieren om verbinding te maken data factory met Azure controle sfeer liggen:
### <a name="register-azure-purview-account-to-data-factory"></a>Azure controle sfeer liggen-account registreren bij Data Factory
1. Ga in de ADF-Portal naar   ->  **Azure controle sfeer liggen** beheren. Selecteer **Verbinding maken met een Purview-account**. 

:::image type="content" source="./media/data-factory-purview/register-purview-account.png" alt-text="Scherm afbeelding voor het registreren van een controle sfeer liggen-account.":::
2. U kunt kiezen voor **Vanuit een Azure-abonnement** of **Handmatig invoeren**. Bij **Vanuit een Azure-abonnement** kunt u het account selecteren waartoe u toegang hebt. 
3. Nadat de verbinding tot stand is gebracht, moet u de naam van het controle sfeer liggen-account in het tabblad **controle sfeer liggen account** kunnen zien. 
4. U kunt de zoek balk aan de bovenkant van Azure Data Factory Portal gebruiken om te zoeken naar gegevens. 

Als er in Azure Data Factory Portal een waarschuwing wordt weer gegeven nadat u Azure controle sfeer liggen-account hebt geregistreerd bij Data Factory, volgt u de onderstaande stappen om het probleem op te lossen:

:::image type="content" source="./media/data-factory-purview/register-purview-account-warning.png" alt-text="Scherm opname van een waarschuwing voor het registreren van een controle sfeer liggen-account.":::

1. Ga naar Azure Portal en zoek uw data factory. Kies sectie Tags en controleer of er een tag is met de naam **catalogUri**. Als dat niet het geval is, verbreekt u de verbinding met het Azure controle sfeer liggen-account in de ADF-Portal en maakt u het opnieuw

:::image type="content" source="./media/data-factory-purview/register-purview-account-tag.png" alt-text="Scherm opname van tags voor het registreren van een controle sfeer liggen-account.":::

2. Controleer of de machtiging is verleend voor het registreren van een Azure controle sfeer liggen-account voor Data Factory. Zie [Azure Data Factory en Azure controle sfeer liggen verbinding maken](../purview/how-to-link-azure-data-factory.md#create-new-data-factory-connection)

### <a name="register-data-factory-in-azure-purview"></a>Data Factory registreren in azure controle sfeer liggen
Zie [How to connect Azure Data Factory and Azure controle sfeer liggen](../purview/how-to-link-azure-data-factory.md)(Engelstalig) voor informatie over het registreren van Data Factory in azure controle sfeer liggen. 

## <a name="report-lineage-data-to-azure-purview"></a>Afkomst gegevens rapporteren aan Azure controle sfeer liggen
Wanneer klanten Kopieer-, gegevens stroom-of uitvoering van SSIS-pakket activiteit uitvoeren in Azure Data Factory, kunnen klanten de afhankelijkheids relatie verkrijgen en een globaal overzicht van het hele werk stroom proces over gegevens bronnen en bestemming hebben.
Zie [Data Factory afkomst](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities)voor meer informatie over het verzamelen van afkomst van Azure Data Factory.

## <a name="next-steps"></a>Volgende stappen
[Gebruikers handleiding voor catalogus afkomst](../purview/catalog-lineage-user-guide.md)

[Zelf studie: Push Data Factory afkomst-gegevens naar Azure controle sfeer liggen](turorial-push-lineage-to-purview.md)