---
title: 'Zelfstudie: Ankers delen in sessies en apparaten'
description: In deze zelfstudie leert u hoe u Azure Spatial Anchor-id's deelt tussen Android-/iOS-apparaten in Unity met een back-endservice.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3ed9b18144bb727af751fbcbbdd5cc6631afc40f
ms.sourcegitcommit: 91361cbe8fff7c866ddc4835251dcbbe2621c055
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "105727226"
---
# <a name="tutorial-share-spatial-anchors-across-sessions-and-devices"></a>Zelfstudie: Ruimtelijke ankers delen in sessies en apparaten

Azure Spatial Anchors is een platformoverstijgende ontwikkelaarsservice waarmee u mixed reality-ervaringen kunt maken met behulp van objecten die hun locatie in de loop van de tijd op meerdere apparaten behouden. 

In deze zelfstudie gebruikt u [Azure Spatial Anchors](../overview.md) om tijdens één sessie ankers te maken en deze vervolgens op hetzelfde apparaat of op een ander apparaat terug te vinden. Dezelfde ankers kunnen ook worden teruggevonden door meerdere apparaten op dezelfde plaats op hetzelfde moment.

![Animatie die ruimtelijke ankers toont die zijn gemaakt met een mobiel apparaat en die gedurende een aantal dagen worden gebruikt met een ander apparaat.](./media/persistence.gif)


In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Implementeer een ASP.NET Core-web-app in Azure die u kunt gebruiken om ankers te delen en de ankers in het geheugen op te slaan voor een opgegeven periode.
> * Configureer de scène AzureSpatialAnchorsLocalSharedDemo binnen het Unity-voorbeeld van onze quickstarts om te profiteren van de web-app Sharing Anchors.
> * Implementeer de ankers en voer deze uit op een of meer apparaten.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

> [!NOTE]
> U zult in deze zelfstudie gebruikmaken van Unity en een ASP.NET Core-web-app, maar deze aanpak is alleen een voorbeeld om te laten zien hoe het delen van Azure Spatial Anchor-id's naar andere apparaten werkt. U kunt andere talen en back-endtechnologieën gebruiken om hetzelfde doel te bereiken.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-sample-project"></a>Het voorbeeldproject downloaden

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-the-sharing-anchors-service"></a>De Sharing Anchor-service implementeren

## <a name="visual-studio"></a>[Visual Studio](#tab/VS)

Open Visual Studio en open vervolgens het project in de map *Sharing\SharingServiceSample*.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="visual-studio-code"></a>[Visual Studio Code](#tab/VSC)

U moet een resourcegroep en een App Service-plan maken voordat u de service in Visual Studio Code implementeert.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Ga naar de <a href="https://portal.azure.com/" target="_blank">de Azure-portal</a> en meld u vervolgens aan bij uw Azure-abonnement.

### <a name="create-a-resource-group"></a>Een resourcegroep maken

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Selecteer **Nieuw** naast **Resourcegroep**.

Geef de resourcegroep de naam **myResourceGroup** en selecteer vervolgens **OK**.

### <a name="create-an-app-service-plan"></a>Een App Service-plan maken

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

Selecteer bij **Hostingabonnement****Nieuw**.

Gebruik deze instellingen in het deelvenster **Hostingabonnement configureren**:

| Instelling | Voorgestelde waarde | Beschrijving |
|-|-|-|
|App Service-plan| MySharingServicePlan | De naam van het App Service-plan |
| Locatie | VS - west | Het datacentrum waar de web-app wordt gehost |
| Grootte | Gratis | De [prijscategorie](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) die de hosting-functies bepaalt |

Selecteer **OK**.

Open Visual Studio Code en open vervolgens het project in de map *Sharing\SharingServiceSample*. 

Volg de instructies in <a href="/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode?view=aspnetcore-2.2&preserve-view=true#open-it-with-visual-studio-code" target="_blank">Een ASP.NET Core-app publiceren in Azure met Visual Studio Code</a> als u de service voor delen via Visual Studio Code wilt implementeren. Begin met de sectie 'Openen met Visual Studio Code'. Maak geen ander ASP.NET-project zoals uitgelegd in de voorgaande stap, want u beschikt al over een project dat moet worden geïmplementeerd en gepubliceerd: SharingServiceSample.

---

## <a name="deploy-the-sample-app"></a>De voorbeeld-app implementeren

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een ASP.NET Core-web-app in Azure geïmplementeerd en u hebt een Unity-app geconfigureerd en geïmplementeerd. U hebt ruimtelijk ankers met de app gemaakt en u hebt deze gedeeld met andere apparaten met behulp van uw ASP.NET Core-web-app.

U kunt uw ASP.NET Core-web-app verbeteren zodat deze gebruikmaakt van Azure Cosmos DB om de opslag van uw gedeelde ruimtelijke anker-id's te behouden. Door ondersteuning voor Azure Cosmos DB toe te voegen, kunt u vandaag nog een anker maken met uw ASP.NET Core web-app. Met de anker-id die is opgeslagen in uw web-app, kunt u de app later terugsturen om het anker opnieuw te vinden.

> [!div class="nextstepaction"]
> [Azure Cosmos DB gebruiken om ankers op te slaan](./tutorial-use-cosmos-db-to-store-anchors.md)