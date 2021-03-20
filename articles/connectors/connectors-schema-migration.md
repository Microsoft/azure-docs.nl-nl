---
title: Apps migreren naar het laatste schema
description: De JSON-definities van de werk stroom voor logische apps migreren naar de meest recente schema versie van de werk stroom definitie taal
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: 114b8b32d4abb1fd9b7e641625cd1b132470bafd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87281443"
---
# <a name="migrate-logic-apps-to-latest-schema-version"></a>Logische apps migreren naar de meest recente schema versie

Voer de volgende stappen uit om uw bestaande Logic apps naar het nieuwste schema te verplaatsen: 

1. Open in de [Azure Portal](https://portal.azure.com)uw logische app in de ontwerp functie voor logische apps.

2. Kies **overzicht** in het menu van de logische app. Kies op de werk balk de optie **schema bijwerken**.

   > [!NOTE]
   > Wanneer u **schema bijwerken** kiest, worden de migratie stappen automatisch door Azure Logic apps uitgevoerd en wordt de code-uitvoer voor u verstrekt. U kunt deze uitvoer gebruiken voor het bijwerken van de definitie van de logische app. Zorg er echter voor dat u de aanbevolen procedures volgt, zoals beschreven in de sectie **Aanbevolen procedures** .

   ![Schema bijwerken](./media/connectors-schema-migration/update-schema.png)

   De pagina schema bijwerken wordt weer gegeven en toont een koppeling naar een document waarin de verbeteringen in het nieuwe schema worden beschreven.

## <a name="best-practices"></a>Aanbevolen procedures

Hier volgen enkele aanbevolen procedures voor het migreren van uw Logic apps naar de meest recente schema versie:

* Kopieer het gemigreerde script naar een nieuwe logische app. Overschrijf de oude versie pas nadat u de tests hebt voltooid en controleer of uw gemigreerde app werkt zoals verwacht.

* Test uw logische app **voordat** u deze in productie neemt.

* Nadat u de migratie hebt voltooid, start u de Logic apps zo dat mogelijk wordt uitgevoerd met behulp van de [beheerde api's](../connectors/apis-list.md) . Begin bijvoorbeeld met behulp van Dropbox v2, overal waar u DropBox v1 gebruikt.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [hand matig migreren van uw Logic apps](../logic-apps/logic-apps-schema-2016-04-01.md)

