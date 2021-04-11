---
title: 'Quickstart: een web-app maken en implementeren in Azure Service Fabric Mesh'
description: In deze quickstart kunt u zien hoe u een ASP.NET Core-website maakt en publiceert in Azure Service Fabric Mesh met behulp van Visual Studio.
author: georgewallace
ms.topic: quickstart
ms.date: 07/17/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: 665988f37d0afdb91bb074d8653cc3c24155966e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99627008"
---
# <a name="quickstart-create-and-deploy-a-web-app-to-azure-service-fabric-mesh"></a>Snelstart, een web-app maken en implementeren in Azure Service Fabric Mesh

> [!IMPORTANT]
> De preview-versie van Azure Service Fabric Mesh is buiten gebruik gesteld. Nieuwe implementaties zijn niet langer toegestaan via de API van Service Fabric net. Ondersteuning voor bestaande implementaties gaat door tot 28 april 2021.
> 
> Zie [Azure service Fabric Netpreview buiten](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)gebruik stellen voor meer informatie.

Azure Service Fabric Mesh is een volledig beheerde service waarmee ontwikkelaars microservices-toepassingen kunnen implementeren zonder virtuele machines, opslag of netwerken hoeven te beheren.

In deze snelstart maakt u een nieuwe Service Fabric Mesh-toepassing bestaande uit een ASP.NET Core-webapp, voert u die uit in het lokale ontwikkelcluster en publiceert u die voor uitvoering in Azure.

U hebt een Azure-abonnement nodig. Als u nog geen abonnement op Azure hebt, kunt u eenvoudig een gratis Azure-abonnement maken, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint. U moet ook uw [ontwikkelomgeving instellen](service-fabric-mesh-howto-setup-developer-environment-sdk.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="create-a-service-fabric-mesh-project"></a>Een Service Fabric Mesh-project maken

Open Visual Studio en selecteer **Bestand** > **Nieuw** > **Project...**

In het dialoogvenster **New Project** typt u `mesh` in het vak **Search**. Selecteer de sjabloon **Service Fabric Mesh Application**. (Als u de sjabloon niet ziet, controleert u of u de Mesh-SDK en de previewversie van de hulpmiddelen van Visual Studio hebt geïnstalleerd, zoals beschreven in [set up your development environment](service-fabric-mesh-howto-setup-developer-environment-sdk.md) (uw ontwikkelomgeving instellen).) 

Typ **ServiceFabricMesh1** in het vak **Naam** en stel in het vak **Locatie** het mappad in naar de plaats waar de bestanden voor het project worden opgeslagen.

Controleer of het selectievakje **Create directory for solution** is ingeschakeld en klik op **OK** om het Service Fabric Mesh-project te maken.

![Schermopname van het maken van een Service Fabric Mesh-project.](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-project.png)

### <a name="create-a-service"></a>Een service maken

Nadat u op **OK** hebt geklikt, wordt het dialoogvenster **New Service Fabric Service** weergegeven. Selecteer het projecttype **ASP.NET Core** en controleer of **Container OS** is ingesteld op **Windows** en klik op **OK** om een ASP.NET Core-project te maken. 

![Dialoogvenster Nieuw project van Service Fabric Mesh in Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-service-fabric-service.png)

Het dialoogvenster **New ASP.NET Core Web Application** wordt weergegeven. Selecteer **Webtoepassing** en klik op **OK**.

![Nieuwe ASP.NET Core-toepassing in Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-aspnetcore-app.png)

Visual Studio maakt het Service Fabric Mesh- en het ASP.NET Core-project.

## <a name="build-and-publish-to-your-local-cluster"></a>In uw lokale cluster bouwen en publiceren

Zodra uw project is geladen, wordt er automatisch een Docker-installatiekopie gemaakt en op uw lokale cluster gepubliceerd. Dit proces kan enige tijd duren. U kunt als u wilt de voortgang van de Service Fabric-hulpprogramma’s volgen in het venster **Uitvoer** door het item **Service Fabric Tools** te selecteren in de vervolgkeuzelijst in het venster **Uitvoer**. U kunt verder werken terwijl de Docker-installatiekopie wordt geïmplementeerd.

Nadat het project is gemaakt, klikt u op **F5** om lokaal fouten op te sporen in uw service. Als de lokale implementatie is voltooid en uw project in Visual Studio wordt uitgevoerd, wordt een browservenster geopend met een voorbeeld van een webpagina.

Als u klaar bent met de geïmplementeerde service te bekijken, stopt u met het fouten opsporen van uw project door in Visual Studio op **Shift+F5** te drukken.

## <a name="publish-to-azure"></a>Publiceren naar Azure

Als u uw Service Fabric Mesh-project in Azure wilt publiceren, klikt u met de rechtermuisknop op **Service Fabric Mesh-project** in Visual Studio en selecteert u **Publiceren**.

![Service Fabric Mesh-project in Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-right-click-publish.png)

Vervolgens ziet u het dialoogvenster **Service Fabric-toepassing publiceren**.

![Dialoogvenster in Visual Studio over Service Fabric Mesh-publicatie](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-dialog.png)

Selecteer uw Azure-account en -abonnement. Kies een **Locatie**. In dit artikel wordt gebruikgemaakt van **VS - oost**.

Selecteer onder **Resourcegroep** de optie **\<Create New Resource Group...>** . Het dialoogvenster **Resourcegroep maken** wordt weergegeven. Stel de **naam van de resourcegroup** en **Locatie** in.  In deze snelstart wordt de locatie **VS - oost** gebruikt en krijgt de groep de naam **sfmeshTutorial1RG** (kies een unieke resourcegroepsnaam als meerdere mensen in uw organisatie gebruikmaken van hetzelfde abonnement).  Klik op **Maken** om de resourcegroep te maken en ga terug naar het publicatiedialoogvenster.

![Schermopname van het maken van een nieuwe resourcegroep.](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-resource-group-dialog.png)

In het dialoogvenster **Service Fabric-toepassing publiceren** selecteert u onder **Azure Container Registry** **\<Create New Container Registry...>** . In het dialoogvenster **Containerregister maken** gebruikt u een unieke naam voor de **Containerregisternaam**. Geef een **Locatie** op (in deze snelstart wordt gebruikgemaakt van **VS - oost**). Selecteer in de vervolgkeuzelijst de **Resourcegroep** die u eerder hebt gemaakt, bijvoorbeeld **sfmeshTutorial1RG**. Stel de **SKU** in op **Basic** en klik vervolgens op **Maken** om terug te keren naar het publicatiedialoogvenster.

![Dialoogvenster in Visual Studio over nieuwe resourcegroep in Service Fabric Mesh](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-container-registry-dialog.png)

In het publicatiedialoogvenster klikt u op de knop **Publiceren** om uw Service Fabric Mesh-toepassing te implementeren in Azure.

Wanneer u voor het eerst in Azure implementeert, wordt de docker-installatiekopie naar de Azure Container Registry (ACR) gepushed, wat afhankelijk van de grootte van de kopie even kan duren. Volgende publicaties voor hetzelfde project zullen sneller verlopen. U kunt de voortgang van de implementatie bekijken door de vervolgkeuzelijst **Service Fabric Tools** in het Visual Studio-venster **Uitvoer** te selecteren. Nadat de implementatie is voltooid, toont de uitvoer van de **Service Fabric Tools** het IP-adres en de poort van uw toepassing in de vorm van een URL.

```
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMesh1\Web1\bin\Any CPU\Release\netcoreapp2.0\Web1.dll
Uploading the images to Azure Container Registry...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://...
```

Open een webbrowser en navigeer naar de URL om de website in Azure uitgevoerd te zien worden:

![Een Service Fabric Mesh-webtoepassing uitvoeren](media/service-fabric-mesh-tutorial-deploy-dotnetcore/deployed-web-project.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de resources die u hebt gemaakt voor deze snelstart niet langer nodig hebt, kunt u deze verwijderen. Aangezien u een nieuwe resourcegroep hebt gemaakt om zowel de ACR- als de Service Fabric Mesh-serviceresources te hosten, kunt u deze resourcegroep veilig verwijderen, dit is een eenvoudige manier om alle bijbehorende resources te verwijderen.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Connect-AzureRmAccount
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

U kunt de resourcegroep ook [vanuit de Azure Portal](https://portal.azure.com) verwijderen.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het maken en implementeren van Service Fabric Mesh-toepassingen, gaat u naar de zelfstudie.
> [!div class="nextstepaction"]
> [Een web-app voor meerdere services maken, foutvrij maken en implementeren in Service Fabric Mesh](service-fabric-mesh-tutorial-create-dotnetcore.md)
