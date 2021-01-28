---
title: Data Factory-herkomstgegevens pushen naar Azure Purview
description: Meer informatie over het pushen van Data Factory afkomst-gegevens naar Azure controle sfeer liggen
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
ms.openlocfilehash: e87a9d677fee94d410099db1da80a56b5539048c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935470"
---
# <a name="push-data-factory-lineage-data-to-azure-purview-preview"></a>Push Data Factory afkomst-gegevens naar Azure controle sfeer liggen (preview-versie)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In deze zelf studie gebruikt u de Data Factory gebruikers interface (UI) om een pijp lijn te maken die activiteiten uitvoert en afkomst-gegevens rapporteert aan een Azure controle sfeer liggen-account. Vervolgens kunt u alle afkomst-informatie in uw Azure controle sfeer liggen-account weer geven.

## <a name="prerequisites"></a>Vereisten
* **Azure-abonnement**. Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* **Azure Data Factory**. Als u geen Azure Data Factory hebt, raadpleegt u [een Azure Data Factory maken](./quickstart-create-data-factory-portal.md).
* **Azure controle sfeer liggen-account**. Het controle sfeer liggen-account legt alle afkomst-gegevens vast die zijn gegenereerd door data factory. Als u geen Azure controle sfeer liggen-account hebt, raadpleegt u [een Azure-controle sfeer liggen maken](../purview/create-catalog-portal.md).


## <a name="run-data-factory-activities-and-push-lineage-data-to-azure-purview"></a>Data Factory activiteiten uitvoeren en afkomst-gegevens naar Azure controle sfeer liggen pushen
### <a name="step-1--connect-data-factory-to-your-purview-account"></a>Stap 1: Data Factory verbinding maken met uw controle sfeer liggen-account
Meld u aan bij uw controle sfeer liggen-account in de controle sfeer liggen-Portal en ga naar **Management Center**. Kies **Data Factory** in **externe verbindingen** en klik op de knop **Nieuw** om een verbinding met een nieuwe Data Factory te maken. 
[![Scherm afbeelding voor het maken van een verbinding tussen Data Factory-en controle sfeer liggen-account ](./media/data-factory-purview/connect-adf-to-purview.png)](./media/data-factory-purview/connect-adf-to-purview.png#lightbox)

Op de pop-uppagina kunt u de Data Factory kiezen waarmee u verbinding wilt maken met dit controle sfeer liggen-account. 
![Scherm afbeelding voor een nieuwe verbinding](./media/data-factory-purview/new-adf-purview-connection.png)

U kunt de status controleren nadat u de verbinding hebt gemaakt. **Verbonden** betekent dat de verbinding tussen Data Factory en dat deze controle sfeer liggen met succes is verbonden. 
> [!NOTE]
> U moet een van de volgende rollen toewijzen in het controle sfeer liggen-account en Data Factory **Inzender** rol om de verbinding tussen Data Factory en Azure controle sfeer liggen te maken.
> - Eigenaar
> - Beheerder van gebruikerstoegang

### <a name="step-2-run-copy-and-dataflow-activities-in-data-factory"></a>Stap 2: Kopieer-en gegevensstroom activiteiten uitvoeren in Data Factory
U kunt pijp lijnen maken, activiteiten kopiëren en gegevensstroom activiteiten in Data Factory. U hebt geen aanvullende configuratie nodig voor het vastleggen van afkomst-gegevens. De afkomst-gegevens worden automatisch vastgelegd tijdens de uitvoering van de activiteiten.
![Scherm afbeelding van de activiteit Copy en gegevensfeed ](./media/data-factory-purview/adf-activities-for-lineage.png) Als u niet weet hoe u een Kopieer-en gegevensstroom activiteiten kunt maken, raadpleegt u [gegevens kopiëren van Azure Blob-opslag naar een data base in Azure SQL database met behulp van Azure Data Factory](./tutorial-copy-data-portal.md) en [gegevens transformeren met behulp van gegevens stromen toewijzen](./tutorial-data-flow.md).

### <a name="step-3-run-execute-ssis-package-activities-in-data-factory"></a>Stap 3: Voer de uitvoering van SSIS-pakket activiteiten uit in Data Factory
U kunt pijp lijnen maken, SSIS-pakket activiteiten uitvoeren in Data Factory. U hebt geen aanvullende configuratie nodig voor het vastleggen van afkomst-gegevens. De afkomst-gegevens worden automatisch vastgelegd tijdens de uitvoering van de activiteiten.
![Scherm afbeelding van de activiteit uitvoeren SSIS-pakket](./media/data-factory-purview/ssis-activities-for-lineage.png)

Zie [SSIS-pakketten uitvoeren in azure](./tutorial-deploy-ssis-packages-azure.md)als u niet weet hoe u uitvoeren SSIS-pakket activiteiten kunt maken.

### <a name="step-4-view-lineage-information-in-your-purview-account"></a>Stap 4: afkomst-informatie in uw controle sfeer liggen-account weer geven
Ga terug naar uw controle sfeer liggen-account. Selecteer op de start pagina **Bladeren assets**. Kies het gewenste activum en klik op het tabblad afkomst. Alle afkomst-gegevens worden weer geven.
[![Scherm opname van controle sfeer liggen-account ](./media/data-factory-purview/view-dataset.png)](./media/data-factory-purview/view-dataset.png#lightbox)

U kunt afkomst-gegevens weer geven voor kopieer activiteit.
[![Scherm opname van afkomst ](./media/data-factory-purview/copy-lineage.png) kopiëren ](./media/data-factory-purview/copy-lineage.png#lightbox)

U kunt ook afkomst-gegevens voor de activiteit gegevensstroom weer geven.
[![Scherm opname van gegevensstroom afkomst ](./media/data-factory-purview/dataflow-lineage.png)](./media/data-factory-purview/dataflow-lineage.png#lightbox)

> [!NOTE] 
> Voor de afkomst van de gegevensstroom activiteit worden alleen bron-en Sink-gegevens ondersteund. De afkomst voor de trans formatie van de gegevens stroom wordt nog niet ondersteund.

U kunt ook afkomst-gegevens zien voor de activiteit SSIS-pakket uitvoeren.
[![Scherm afbeelding van SSIS afkomst ](./media/data-factory-purview/ssis-lineage.png)](./media/data-factory-purview/ssis-lineage.png#lightbox)

> [!NOTE] 
> Voor de afkomst van de activiteit uitvoeren SSIS-pakket wordt alleen de bron en het doel ondersteund. De afkomst voor trans formatie wordt nog niet ondersteund.

## <a name="next-steps"></a>Volgende stappen
[Gebruikers handleiding voor catalogus afkomst](../purview/catalog-lineage-user-guide.md)

[Data Factory verbinden met Azure controle sfeer liggen](connect-data-factory-to-azure-purview.md)