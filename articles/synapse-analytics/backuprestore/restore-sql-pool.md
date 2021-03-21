---
title: Een bestaande toegewezen SQL-groep herstellen
description: Instructies voor het herstellen van een bestaande, specifieke SQL-groep.
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
ms.openlocfilehash: 86150107273b247bef2c74e5b8c5272d7148587e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94332062"
---
# <a name="restore-an-existing-dedicated-sql-pool"></a>Een bestaande toegewezen SQL-groep herstellen

In dit artikel leert u hoe u een bestaande, specifieke SQL-groep in azure Synapse Analytics kunt herstellen met behulp van Azure Portal en Synapse Studio. Dit artikel is van toepassing op zowel herstel bewerkingen als geo-restores. 

## <a name="restore-an-existing-dedicated-sql-pool-through-the-synapse-studio"></a>Een bestaande, specifieke SQL-groep herstellen via de Synapse Studio

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Navigeer naar uw Synapse-werk ruimte. 
3. Ga naar aan de slag-> open Synapse Studio en selecteer **openen**.

    ![ Synapse Studio](../media/sql-pools/open-synapse-studio.png)
4. Selecteer **gegevens** in het navigatie deel venster aan de linkerkant.
5. Selecteer **groepen beheren**. 
6. Selecteer **+ Nieuw** om een nieuwe toegewezen SQL-groep te maken. 
7. Selecteer op het tabblad Extra instellingen een herstel punt waarvan u wilt herstellen. 

    Als u een geo-herstel bewerking wilt uitvoeren, selecteert u de werk ruimte en de toegewezen SQL-groep die u wilt herstellen. 

8. Selecteer **automatische herstel punten** of door de **gebruiker gedefinieerde herstel punten**. 

    ![Herstel punten](../media/sql-pools/restore-point.PNG)

    Als de toegewezen SQL-groep geen automatische herstel punten heeft, wacht u enkele uren of maakt u een door de gebruiker gedefinieerd herstel punt voordat u dit herstelt. Voor User-Defined herstel punten selecteert u een bestaande en maakt u een nieuw item.

    Als u een geo-back-up herstelt, selecteert u de werk ruimte die zich bevindt in de bron regio en de exclusieve SQL-groep die u wilt herstellen. 

9. Selecteer **Controleren + maken**.

## <a name="restore-an-existing-dedicated-sql-pool-through-the-azure-portal"></a>Een bestaande toegewezen SQL-groep herstellen via de Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Navigeer naar de exclusieve SQL-groep waarvan u wilt herstellen.
3. Klik boven aan de Blade overzicht op **herstellen**.

    ![ Overzicht van Herstellen](../media/sql-pools/restore-sqlpool-01.png)

4. Selecteer **automatische herstel punten** of door de **gebruiker gedefinieerde herstel punten**. 

    Als de toegewezen SQL-groep geen automatische herstel punten heeft, wacht u enkele uren of maakt u een door de gebruiker gedefinieerd herstel punt voordat u dit herstelt. 

    Als u een geo-herstel bewerking wilt uitvoeren, selecteert u de werk ruimte en de toegewezen SQL-groep die u wilt herstellen. 

5. Selecteer **Controleren + maken**.

## <a name="next-steps"></a>Volgende stappen

- [Een herstelpunt maken](sqlpool-create-restore-point.md)
