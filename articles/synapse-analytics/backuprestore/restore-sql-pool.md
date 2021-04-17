---
title: Een bestaande toegewezen SQL-pool herstellen
description: Instructiegids voor het herstellen van een bestaande toegewezen SQL-pool.
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: f2fb6f809794781559683907a806e6d30ca9bed6
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567975"
---
# <a name="restore-an-existing-dedicated-sql-pool"></a>Een bestaande toegewezen SQL-pool herstellen

In dit artikel leert u hoe u een bestaande toegewezen SQL-pool in Azure Synapse Analytics herstellen met Azure Portal en Synapse Studio. Dit artikel is van toepassing op zowel herstel- als geo-herstel. 

## <a name="restore-an-existing-dedicated-sql-pool-through-the-synapse-studio"></a>Een bestaande toegewezen SQL-pool herstellen via de Synapse Studio

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Navigeer naar uw Synapse-werkruimte. 
3. Selecteer onder Aan de slag -> Open Synapse Studio de optie **Openen.**

    ![ Synapse Studio](../media/sql-pools/open-synapse-studio.png)
4. Selecteer gegevens in het navigatiedeelvenster aan de **linkerkant.**
5. Selecteer **Pools beheren.** 
6. Selecteer **+ Nieuw om** een nieuwe toegewezen SQL-pool te maken. 
7. Selecteer op het tabblad Extra instellingen een herstelpunt om van te herstellen. 

    Als u geo-herstel wilt uitvoeren, selecteert u de werkruimte en toegewezen SQL-pool die u wilt herstellen. 

8. Selecteer Automatische **herstelpunten of** door **de gebruiker gedefinieerde herstelpunten.** 

    ![Herstelpunten](../media/sql-pools/restore-point.PNG)

    Als de toegewezen SQL-pool geen automatische herstelpunten heeft, wacht u een paar uur of maakt u een door de gebruiker gedefinieerd herstelpunt voordat u het herstel herstelt. Voor User-Defined herstelpunten selecteert u een bestaand herstelpunt of maakt u een nieuwe.

    Als u een geo-back-up herstelt, selecteert u de werkruimte in de bronregio en de toegewezen SQL-pool die u wilt herstellen. 

9. Selecteer **Controleren + maken**.

## <a name="restore-an-existing-dedicated-sql-pool-through-the-azure-portal"></a>Een bestaande toegewezen SQL-pool herstellen via de Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Navigeer naar de toegewezen SQL-pool van waar u wilt herstellen.
3. Selecteer bovenaan de blade Overzicht de optie **Herstellen.**

    ![ Overzicht van Herstellen](../media/sql-pools/restore-sqlpool-01.png)

4. Selecteer Automatische **herstelpunten of** door **de gebruiker gedefinieerde herstelpunten.** 

    Als de toegewezen SQL-pool geen automatische herstelpunten heeft, wacht u een paar uur of maakt u een door de gebruiker gedefinieerd herstelpunt voordat u het herstel herstelt. 

    Als u geo-herstel wilt uitvoeren, selecteert u de werkruimte en toegewezen SQL-pool die u wilt herstellen. 

5. Selecteer **Controleren + maken**.

## <a name="next-steps"></a>Volgende stappen

- [Een herstelpunt maken](sqlpool-create-restore-point.md)
