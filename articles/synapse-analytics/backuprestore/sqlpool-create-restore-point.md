---
title: Een door de gebruiker gedefinieerd herstel punt maken voor een toegewezen SQL-groep
description: Meer informatie over het gebruik van de Azure Portal voor het maken van een door de gebruiker gedefinieerd herstel punt voor een toegewezen SQL-groep in azure Synapse Analytics.
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 21fd20100095040fda9f72b00e17147ff560fbca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94579535"
---
# <a name="user-defined-restore-points"></a>Door de gebruiker gedefinieerde herstel punten

In dit artikel leert u hoe u een nieuw, door de gebruiker gedefinieerd herstel punt maakt voor een toegewezen SQL-groep in azure Synapse Analytics met behulp van de Azure Portal.

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Door de gebruiker gedefinieerde herstel punten maken via de Azure Portal

Door de gebruiker gedefinieerde herstel punten kunnen ook worden gemaakt via Azure Portal.

1. Meld u aan bij uw [Azure Portal](https://portal.azure.com/) -account.

2. Navigeer naar de exclusieve SQL-groep waarvoor u een herstel punt wilt maken.

3. Selecteer **overzicht** in het linkerdeel venster en selecteer **+ Nieuw herstel punt**. Als de knop Nieuw herstel punt niet is ingeschakeld, moet u ervoor zorgen dat de toegewezen SQL-groep niet wordt onderbroken.

    ![Nieuw herstel punt](../media/sql-pools/create-sqlpool-restore-point-01.png)

4. Geef een naam op voor het herstel punt dat door de gebruiker is gedefinieerd en klik op **Toep assen**. Door de gebruiker gedefinieerde herstel punten hebben een standaard Bewaar periode van zeven dagen.

    ![Naam van herstel punt](../media/sql-pools/create-sqlpool-restore-point-02.png)

## <a name="next-steps"></a>Volgende stappen

- [Een bestaande toegewezen SQL-groep herstellen](restore-sql-pool.md)

