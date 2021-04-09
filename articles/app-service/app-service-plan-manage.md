---
title: App Service-abonnement beheren
description: Meer informatie over het uitvoeren van verschillende taken voor het beheren van een App Service-abonnement, zoals maken, verplaatsen, schalen en verwijderen.
keywords: app service, Azure app service, Scale, app service-plan, wijzigen, maken, beheren, beheer
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: 58ddf60a842a9583eae25a875f55227cee772a8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100582539"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Een App Service-abonnement beheren in azure

Een [Azure app service plan](overview-hosting-plans.md) bevat de resources die een app service app moet uitvoeren. In deze hand leiding wordt uitgelegd hoe u een App Service plan beheert.

## <a name="create-an-app-service-plan"></a>Een App Service-plan maken

> [!TIP]
> Als u een App Service Environment hebt, raadpleegt u [een app service plan maken in een app service Environment](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

U kunt een leeg App Service plan maken of u kunt een plan maken als onderdeel van het maken van een app.

1. Selecteer in de [Azure Portal](https://portal.azure.com) **een resource maken**.

   ![Maak een resource in de Azure Portal.][createResource] 

1. Selecteer een **nieuwe**  >  **Web-app** of een ander type app service-app.

   ![Maak een app in de Azure Portal.][createWebApp] 

2. Configureer de sectie met **instantie Details** voordat u het app service-abonnement configureert. Instellingen zoals **publiceren** en **besturings systemen** kunnen de beschik bare prijs categorieën voor uw app service plan wijzigen. De **regio** bepaalt waar uw app service plan wordt gemaakt. 
   
3. Selecteer in de sectie **app service plan** een bestaand abonnement of maak een plan door **Nieuw maken** te selecteren.

   ![Een App Service-abonnement maken.][createASP] 

4. Wanneer u een plan maakt, kunt u de prijs categorie van het nieuwe plan selecteren. In **SKU en grootte** selecteert u **grootte wijzigen** om de prijs categorie te wijzigen. 

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Een app verplaatsen naar een andere App Service plan

U kunt een app naar een andere App Service plan verplaatsen, zolang het bron plan en het doel abonnement zich in _dezelfde resource groep en geografische regio_ bevinden.

> [!NOTE]
> Azure implementeert elk nieuw App Service plan in een implementatie-eenheid, intern een webruimte genoemd. Elke regio kan veel webruimten hebben, maar uw app kan alleen worden verplaatst tussen plannen die zijn gemaakt in dezelfde webruimte. Een App Service Environment is een geïsoleerde webruimte, zodat apps kunnen worden verplaatst tussen plannen in dezelfde App Service Environment, maar niet tussen plannen in verschillende App Service omgevingen.
>
> U kunt de gewenste webruimte niet opgeven wanneer u een plan maakt, maar het is mogelijk om ervoor te zorgen dat een plan wordt gemaakt in dezelfde webruimte als een bestaand abonnement. In het kort worden alle abonnementen die zijn gemaakt met dezelfde resource groep en regio combinatie, geïmplementeerd in dezelfde webruimte. Als u bijvoorbeeld een plan hebt gemaakt in resource groep A en regio B, wordt een plan dat u vervolgens in resource groep A en regio B maakt, geïmplementeerd in dezelfde webruimte. Houd er rekening mee dat het niet mogelijk is om webruimten te verplaatsen nadat ze zijn gemaakt. u kunt een plan niet verplaatsen naar dezelfde webruimte als een ander abonnement door het te verplaatsen naar een andere resource groep.
> 

1. Zoek in het [Azure Portal](https://portal.azure.com)naar en selecteer **app Services** en selecteer de app die u wilt verplaatsen.

2. Selecteer in het menu links de optie **app service abonnement wijzigen**.

3. Selecteer in de vervolg keuzelijst **app service plan** een bestaand plan om de app naar te verplaatsen. In de vervolg keuzelijst worden alleen abonnementen weer gegeven die deel uitmaken van dezelfde resource groep en geografische regio als het huidige App Service plan. Als dat plan niet bestaat, kunt u standaard een plan maken. U kunt ook hand matig een nieuw plan maken door **Nieuw maken** te selecteren.

4. Als u een plan maakt, kunt u de prijs categorie van het nieuwe plan selecteren. Selecteer in **prijs categorie** de bestaande laag om deze te wijzigen. 
   
   > [!IMPORTANT]
   > Als u een app van een abonnement op een hoger niveau verplaatst naar een onderlaagd abonnement, bijvoorbeeld van **D1** naar **F1**, kan de app bepaalde mogelijkheden in het doel plan verliezen. Als uw app bijvoorbeeld gebruikmaakt van TLS/SSL-certificaten, ziet u dit fout bericht:
   >
   > `Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

5. Selecteer **OK** als u klaar bent.
   
   ![App Service plan kiezer.][change] 

## <a name="move-an-app-to-a-different-region"></a>Een app verplaatsen naar een andere regio

De regio waarin uw app wordt uitgevoerd, is de regio van het App Service plan waarin deze zich bevindt. U kunt de regio van een App Service abonnement echter niet wijzigen. Als u uw app in een andere regio wilt uitvoeren, is een alternatief voor het klonen van apps. Bij het klonen wordt een kopie van uw app in een nieuw of bestaand App Service plan in elke regio gemaakt.

U vindt de **kloon-app** in de sectie **hulp middelen voor ontwikkel aars** van het menu.

> [!IMPORTANT]
> Voor klonen gelden enkele beperkingen. U kunt deze informatie lezen in [Azure app service het klonen van apps](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Een App Service plan schalen

Zie [een app omhoog schalen in azure](manage-scale-up.md)om de prijs categorie van een app service plan omhoog te schalen.

Als u het aantal exemplaren van een app wilt uitschalen, raadpleegt u [aantal exemplaren hand matig of automatisch schalen](../azure-monitor/autoscale/autoscale-get-started.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Een App Service-abonnement verwijderen

Om onverwachte kosten te voor komen, wordt het plan ook standaard verwijderd wanneer u de laatste app in een App Service App Service plan verwijdert. Als u ervoor kiest om het plan te houden, moet u het abonnement wijzigen in de laag **gratis** , zodat u niet in rekening wordt gebracht.

> [!IMPORTANT]
> App Service plannen waaraan geen apps zijn gekoppeld, worden nog steeds kosten in rekening gebracht omdat ze de geconfigureerde VM-instanties blijven reserveren.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een app omhoog schalen in Azure](manage-scale-up.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
[createResource]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-a-resource.png