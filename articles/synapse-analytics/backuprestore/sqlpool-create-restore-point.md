---
title: Een door de gebruiker gedefinieerd herstelpunt maken voor een toegewezen SQL-pool
description: Meer informatie over het gebruik van Azure Portal om een door de gebruiker gedefinieerd herstelpunt te maken voor toegewezen SQL-pool in Azure Synapse Analytics.
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: b695f6c7aabc21541fcc48efed54bbecd022f65a
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567924"
---
# <a name="user-defined-restore-points"></a>Door de gebruiker gedefinieerde herstelpunten

In dit artikel leert u hoe u een nieuw door de gebruiker gedefinieerd herstelpunt maakt voor een toegewezen SQL-pool in Azure Synapse Analytics met behulp van de Azure Portal.

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Door de gebruiker gedefinieerde herstelpunten maken via de Azure Portal

Door de gebruiker gedefinieerde herstelpunten kunnen ook worden gemaakt via Azure Portal.

1. Meld u aan bij [uw Azure Portal account.](https://portal.azure.com/)

2. Navigeer naar de toegewezen SQL-pool waar u een herstelpunt voor wilt maken.

3. Selecteer **Overzicht** in het linkerdeelvenster en **selecteer + Nieuw herstelpunt.** Als de knop Nieuw herstelpunt niet is ingeschakeld, moet u ervoor zorgen dat de toegewezen SQL-pool niet is onderbroken.

    ![Nieuw herstelpunt](../media/sql-pools/create-sqlpool-restore-point-01.png)

4. Geef een naam op voor het door de gebruiker gedefinieerde herstelpunt en klik op **Toepassen.** Door de gebruiker gedefinieerde herstelpunten hebben een standaardretentieperiode van zeven dagen.

    ![Naam van herstelpunt](../media/sql-pools/create-sqlpool-restore-point-02.png)

## <a name="next-steps"></a>Volgende stappen

- [Een bestaande toegewezen SQL-pool herstellen](restore-sql-pool.md)

