---
title: 'Zelfstudie: Resources opschonen | Azure'
description: In deze zelfstudie leert u hoe u de Azure-resources kunt opschonen die zijn toegewezen toen u de web-app maakte.
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 10/27/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: bf03fe9d920298d49e79a9a0febf7e09e94eb6ff
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905516"
---
# <a name="tutorial-clean-up-resources"></a>Zelfstudie: Resources opschonen

Als u alle stappen in deze meerdelige zelfstudie hebt voltooid, hebt u een app-service, een App Service-hostingplan en een opslagaccount gemaakt in een resourcegroep. U hebt ook een app-registratie gemaakt in Azure Active Directory. Wanneer de resources niet meer nodig zijn, verwijdert u deze en de app-registratie, zodat er geen kosten meer bij u in rekening worden gebracht.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * De Azure-resources verwijderen die tijdens de zelfstudie zijn gemaakt.

## <a name="delete-the-resource-group"></a>De resourcegroep verwijderen

In de [Azure-portal](https://portal.azure.com) selecteert u **Resourcegroepen** in het portalmenu en selecteert u vervolgens de resourcegroep die uw app-service en App Service-plan bevat.

Selecteer **Resourcegroep verwijderen** om de resourcegroep en alle resources te verwijderen.

:::image type="content" alt-text="Schermopname waarin de resourcegroep wordt verwijderd." source="./media/scenario-secure-app-clean-up-resources/delete-resource-group.png":::

Het uitvoeren van deze opdracht kan enkele minuten duren.

## <a name="delete-the-app-registration"></a>De app-registratie verwijderen

Selecteer in het portalmenu **Azure Active Directory** > **App-registraties**. Selecteer vervolgens de toepassing die u heeft gemaakt.
:::image type="content" alt-text="Schermopname van de selectie app-registratie." source="./media/scenario-secure-app-clean-up-resources/select-app-registration.png":::

Selecteer **Verwijderen** in het app-registratieoverzicht.
:::image type="content" alt-text="Schermopname waarin de app-registratie wordt verwijderd." source="./media/scenario-secure-app-clean-up-resources/delete-app-registration.png":::

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
>
> * De Azure-resources verwijderen die tijdens de zelfstudie zijn gemaakt.

Ontdek hoe u een [.NET Core-app](tutorial-dotnetcore-sqldb-app.md), [Python-app](tutorial-python-postgresql-app.md), [Java-app](tutorial-java-spring-cosmosdb.md) of [Node.js-app](tutorial-nodejs-mongodb-app.md) verbindt met een database.