---
title: Azure Portal voor het implementeren van de Service catalogus-app gebruiken
description: Toont gebruikers van beheerde toepassingen hoe een service catalogus-app kan worden geïmplementeerd via de Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: ce58fc69496f54c078b0a0a55a8a3c7cad82a051
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "81391714"
---
# <a name="quickstart-deploy-service-catalog-app-through-azure-portal"></a>Snelstartgids: een service catalogus-app implementeren via Azure Portal

In de [vorige Snelstartgids](publish-service-catalog-app.md)hebt u een definitie van een beheerde toepassing gepubliceerd. In deze Quick Start maakt u een service catalogus-app op basis van die definitie.

## <a name="create-service-catalog-app"></a>Een service catalogus-app maken

Gebruik de volgende stappen in de Azure Portal:

1. Selecteer **Een resource maken**.

   ![Een resource maken](./media/deploy-service-catalog-quickstart/create-new.png)

1. Zoek naar de **beheerde toepassing voor de Service catalogus** en selecteer deze in de beschik bare opties.

   ![Zoeken naar een service catalogus toepassing](./media/deploy-service-catalog-quickstart/select-service-catalog.png)

1. U ziet een beschrijving van de beheerde toepassings service. Selecteer **Maken**.

   ![Selecteer Maken](./media/deploy-service-catalog-quickstart/create-service-catalog.png)

1. In de portal worden de definities van beheerde toepassingen weer gegeven waartoe u toegang hebt. Selecteer in de beschik bare definities het abonnement dat u wilt implementeren. In deze Quick Start gebruikt u de definitie van het **beheerde opslag account** dat u in de vorige Snelstartgids hebt gemaakt. Selecteer **Maken**.

   ![Te implementeren definitie selecteren](./media/deploy-service-catalog-quickstart/select-definition.png)

1. Geef waarden op voor het tabblad **basis beginselen** . Selecteer het Azure-abonnement waarop u uw service catalogus-app wilt implementeren. Maak een nieuwe resource groep met de naam **variabele applicationgroup**. Selecteer een locatie voor uw app. Selecteer **OK** als u klaar bent.

   ![Geef waarden op voor basis](./media/deploy-service-catalog-quickstart/provide-basics.png)

1. Geef een voorvoegsel op voor de opslagaccountnaam. Selecteer het type opslag account dat u wilt maken. Selecteer **OK** als u klaar bent.

   ![Geef waarden op voor opslag](./media/deploy-service-catalog-quickstart/provide-storage.png)

1. Bekijk de samenvatting. Nadat de validatie is voltooid, selecteert u **OK** om de implementatie te starten.

   ![Samen vatting weer geven](./media/deploy-service-catalog-quickstart/view-summary.png)

## <a name="view-results"></a>Resultaten weergeven

Nadat de Service catalogus-app is geïmplementeerd, hebt u twee nieuwe resource groepen. De Service catalogus-app bevindt zich in één resource groep. De andere resource groep bevat de resources voor de Service catalogus-app.

1. Bekijk de resource groep met de naam **variabele applicationgroup** om de Service catalogus-app weer te geven.

   ![Toepassing weergeven](./media/deploy-service-catalog-quickstart/view-managed-application.png)

1. Bekijk de resource groep met de naam **variabele applicationgroup {hash-tekens}** om de resources voor de Service catalogus-app weer te geven.

   ![Resources weergeven](./media/deploy-service-catalog-quickstart/view-resources.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [een definitie van een beheerde toepassing maken en publiceren](publish-service-catalog-app.md)voor meer informatie over het maken van definitie bestanden voor een beheerde toepassing.
* Zie [Service catalogus-app implementeren met Azure cli](./scripts/managed-application-cli-sample-create-application.md)voor meer informatie over Azure cli.
* Voor Power shell raadpleegt u [Service Catalog-app implementeren met Power shell](./scripts/managed-application-poweshell-sample-create-application.md).
