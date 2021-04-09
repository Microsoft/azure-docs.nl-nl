---
title: Beheerde privé-eind punten maken en verwijderen in een Azure Stream Analytics-cluster
description: Meer informatie over het beheren van privé-eindpunten in een Azure Stream Analytics cluster.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: 9939130782594c03a497d98ce6cd9b33b28eadec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101718400"
---
# <a name="create-and-delete-managed-private-endpoints-in-an-azure-stream-analytics-cluster"></a>Beheerde privé-eind punten maken en verwijderen in een Azure Stream Analytics-cluster

U kunt uw Azure Stream Analytics-taken die worden uitgevoerd op een cluster verbinden met invoer- en uitvoerbronnen die zich achter een firewall of een Azure Virtual Network (VNet) bevinden. Eerst maakt u een beheerd privé-eind punt voor een resource, zoals Azure Event hub of Azure SQL Database, in uw Stream Analytics cluster. Vervolgens keurt u de verbinding met het privé-eindpunt goed vanuit uw invoer of uitvoer.

Zodra u de verbinding hebt goedgekeurd, heeft elke taak die in uw Stream Analytics-cluster wordt uitgevoerd toegang tot de resource via het privé-eindpunt. In dit artikel wordt beschreven hoe u privé-eindpunten maakt en verwijdert in een Stream Analytics-cluster. U kunt privé-eindpunten maken voor Azure SQL Database, Azure Storage, Azure Data Lake Storage Gen2, Azure Event Hub en Azure Service Bus. Privé-eindpunten voor andere services worden binnenkort toegevoegd. 

## <a name="create-managed-private-endpoint-in-stream-analytics-cluster"></a>Een beheerd privé-eind punt in Stream Analytics cluster maken

In dit gedeelte leert u hoe u een privé-eindpunt in een Stream Analytics cluster maakt.

1. Zoek en selecteer uw Stream Analytics-cluster in Azure Portal.

1. Onder **instellingen** selecteert u **beheerde persoonlijke eind punten**.

1. Selecteer **Nieuw** en voer de volgende informatie in om de resource te kiezen die u veilig wilt openen via een persoonlijk eind punt.

   |Instelling|Waarde|
   |---|---|
   |Naam|Voer een naam in voor uw privé-eindpunt. Als deze naam al wordt gebruikt, maakt u een unieke naam.|
   |Verbindingsmethode|Selecteer **Verbinding maken met een Azure-resource in mijn directory**.<br><br>U kunt een van uw resources kiezen om veilig verbinding te maken met het privé-eindpunt, of u kunt verbinding maken met de resource van iemand anders met behulp van een resource-id of alias die met u is gedeeld.|
   |Abonnement|Selecteer uw abonnement.|
   |Resourcetype|Kies het [resourcetype dat aan uw resource is toegewezen](../private-link/private-endpoint-overview.md#private-link-resource).|
   |Resource|Selecteer de resource waarmee u verbinding wilt maken met behulp van een privé-eindpunt.|
   |Subresource van doel|Het type subresource voor de resource die hierboven is geselecteerd en die door uw privé-eindpunt kan worden geopend.|

   ![Het maken van het privé-eindpunt](./media/private-endpoints/create-private-endpoint.png)

1. Keur de verbinding goed vanaf de doelresource. Als u in de vorige stap bijvoorbeeld een privé-eindpunt hebt gemaakt voor een Azure SQL Database-exemplaar, gaat u naar dit SQL Database exemplaar en ziet u een verbinding die in behandeling is en moet worden goedgekeurd. Het kan een paar minuten duren voordat de verbindingsaanvraag wordt weergegeven.

    ![privé-eindpunt goedkeuren](./media/private-endpoints/approve-private-endpoint.png)

1. U kunt teruggaan naar uw Stream Analytics-cluster om de status van **Goedkeuring klant in behandeling** in **In afwachting van de DNS-instelling** in **Setup voltooid** te zien wijzigen. Dit duurt enkele minuten.

## <a name="delete-a-managed-private-endpoint-in-a-stream-analytics-cluster"></a>Een beheerd privé-eind punt in een Stream Analytics cluster verwijderen

1. Zoek en selecteer uw Stream Analytics-cluster in Azure Portal.

1. Onder **instellingen** selecteert u **beheerde persoonlijke eind punten**.

1. Selecteer het privé-eindpunt dat u wilt verwijderen en selecteer **Verwijderen**.

   ![privé-eindpunt verwijderen](./media/private-endpoints/delete-private-endpoint.png)

## <a name="next-steps"></a>Volgende stappen

U hebt nu een algemeen beeld van het beheren van privé-eindpunten in een Azure Stream Analytics cluster. U kunt nu gaan leren hoe u uw clusters schaalt en taken uitvoert in uw cluster:

* [Een Azure Stream Analytics-cluster schalen](scale-cluster.md)
* [Stream Analytics-taken beheren in een Stream Analytics-cluster](manage-jobs-cluster.md)
