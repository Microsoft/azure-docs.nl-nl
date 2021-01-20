---
title: 'Quickstart: Een Power BI-werkruimte koppelen aan een Synapse-werkruimte'
description: Koppel een Power BI-werkruimte aan een Azure Synapse Analytics-werkruimte door de stappen in deze handleiding uit te voeren.
services: synapse-analytics
author: jocaplan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: business-intelligence
ms.date: 10/27/2020
ms.author: jocaplan
ms.reviewer: jrasnick
ms.openlocfilehash: 9c63e5e24495f373f288d2789780a6c671a7cc24
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98218396"
---
# <a name="quickstart-linking-a-power-bi-workspace-to-a-synapse-workspace"></a>Quickstart: Een Power BI-werkruimte koppelen aan een Synapse-werkruimte

In deze quickstart leert u hoe u een Power BI-werkruimte koppelt aan een Azure Synapse Analytics-werkruimte om nieuwe Power BI-rapporten en -gegevenssets te maken vanuit Synapse Studio.

Als u geen Azure-abonnement hebt, [maakt u een gratis account voordat u begint](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Vereisten

- [Een Azure Synapse-werkruimte en een gekoppeld opslagaccount maken](quickstart-create-workspace.md)
- [Een Power BI Professional- of Premium-werkruimte](/power-bi/service-create-the-new-workspaces)

## <a name="link-power-bi-workspace-to-your-synapse-workspace"></a>Uw Power BI-werkruimte koppelen aan uw Synapse-werkruimte

1. Klik in Synapse Studio op **Manage**.

    ![Klik op Manage in Synapse Studio.](media/quickstart-link-powerbi/synapse-studio-click-manage.png)

2. Klik onder **External Connections** op **Linked services**.

    ![Linked services is gemarkeerd.](media/quickstart-link-powerbi/manage-click-linked-services.png)

3. Klik op **+ New**.

    ![+ New Linked services is gemarkeerd.](media/quickstart-link-powerbi/new-highlighted.png)

4. Klik op **Power BI** en klik op **Continue**.

    ![De gekoppelde Power BI-service wordt weergegeven.](media/quickstart-link-powerbi/powerbi-linked-service.png)

5. Voer een naam in voor de gekoppelde service en selecteer een werkruimte in de vervolgkeuzelijst.

    ![De installatie van de gekoppelde Power BI-service wordt weergegeven.](media/quickstart-link-powerbi/workspace-link-dialog.png)

6. Klik op **Create**.

## <a name="view-power-bi-workspace-in-synapse-studio"></a>Power BI-werkruimte weergeven in Synapse Studio

Zodra uw werkruimten zijn gekoppeld, kunt u door de Power BI-gegevenssets bladeren en nieuwe Power BI-rapporten bewerken en maken vanuit Synapse Studio.

1. Klik op **Develop**.

    ![Klik op Develop in Synapse Studio.](media/quickstart-link-powerbi/synapse-studio-click-develop.png)

2. Vouw Power BI en de werkruimte uit die u wilt gebruiken.

    ![Power BI en de werkruimte uitvouwen.](media/quickstart-link-powerbi/develop-expand-powerbi.png)

U kunt nieuwe rapporten maken door te klikken op **+** boven in het tabblad **Develop**. Bestaande rapporten kunnen worden bewerkt door te klikken op de naam van het rapport. Opgeslagen wijzigingen worden teruggeschreven naar de Power BI-werkruimte.

![Power BI-rapport weergeven en bewerken.](media/quickstart-link-powerbi/powerbi-report.png)


## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het maken van een Power BI-rapport voor bestanden die zijn opgeslagen op Azure Storage](sql/tutorial-connect-power-bi-desktop.md).