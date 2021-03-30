---
title: Een toewijzing van een gegevensset configureren in een Azure-gegevens share
description: Meer informatie over het configureren van een toewijzing van een gegevensset voor een ontvangen share met behulp van de Azure-gegevens share.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 08/14/2020
ms.openlocfilehash: eed3e8275400a3e677df53b9d62cf0e0bc70271c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88257835"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Een toewijzing van een gegevensset configureren voor een ontvangen share in de Azure-gegevens share

In dit artikel wordt uitgelegd hoe u een toewijzing van een gegevensset configureert voor een ontvangen share met behulp van de Azure-gegevens share. U kunt een gegevensset-toewijzing configureren om een doel gegevens archief op te geven waar u gegevens wilt ontvangen, of als u de doel gegevens opslag wilt wijzigen.

## <a name="navigate-to-a-received-data-share"></a>Navigeer naar een ontvangen gegevens share

Navigeer in de Azure data share-service naar de share die u hebt ontvangen en selecteer het tabblad **gegevens sets** . 

![Toewijzing van gegevensset](./media/dataset-mapping.png "Toewijzing van gegevensset") 

Schakel het selectie vakje in naast de gegevensset waaraan u een bestemming wilt toewijzen. Selecteer **+ toewijzen aan doel** om een nieuw doel archief te kiezen. Selecteer eerst **toewijzing** afwijzen als de gegevensset al is toegewezen en u het doel gegevens archief wilt wijzigen.

![Toewijzen aan doel](./media/dataset-map-target.png "Toewijzen aan doel") 

## <a name="select-a-target-store"></a>Selecteer een doel archief

Selecteer het type doelgegevensarchief waarin u de gegevens wilt opslaan. Voor het delen op basis van moment opnamen worden alle gegevens die al in een eerder toegewezen opslag account bestaan, niet automatisch verplaatst naar het nieuwe doel archief. Selecteer voor in-place delen een gegevensarchief op de opgegeven locatie. De locatie is het Azure-datacenter waar het brongegevensarchief van de gegevensprovider zich bevindt.

![Doelopslagaccount](./media/dataset-map-target-sql.png "Doelopslag") 

## <a name="select-a-file-format-sql-sources-only"></a>Selecteer een bestands indeling (alleen SQL-bronnen)

Als de bron gegevens afkomstig zijn van een bron op basis van SQL en u deze als een bestand wilt ontvangen, kunt u kiezen in welke indeling deze wordt ontvangen. 

![Indeling kiezen](./media/sql-file-formats.png "SQL-bestands indelingen")

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over hoe u gegevens kunt beginnen delen, gaat u verder naar de zelfstudie [uw gegevens delen](share-your-data.md).



