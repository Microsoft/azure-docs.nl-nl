---
title: Een bestaande .NET-app in een container plaatsen voor Service Fabric Mesh
description: Voeg ondersteuning toe voor de indeling van Service Fabric mesh-container in ASP.NET-en console projecten die gebruikmaken van het volledige .NET Framework.
author: georgewallace
ms.author: gwallace
ms.date: 11/08/2018
ms.topic: conceptual
ms.openlocfilehash: b9c5053a2a49c942cc89bd50c65e13f3a2f8d9d7
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625874"
---
# <a name="containerize-an-existing-net-app-for-service-fabric-mesh"></a>Een bestaande .NET-app in een container plaatsen voor Service Fabric Mesh

> [!IMPORTANT]
> De preview-versie van Azure Service Fabric Mesh is buiten gebruik gesteld. Nieuwe implementaties zijn niet langer toegestaan via de API van Service Fabric net. Ondersteuning voor bestaande implementaties gaat door tot 28 april 2021.
> 
> Zie [Azure service Fabric Netpreview buiten](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)gebruik stellen voor meer informatie.

In dit artikel leert u hoe u ondersteuning voor containerindeling van Service Fabric Mesh toevoegt aan een bestaande .NET-app.

In Visual Studio 2017 kunt u ondersteuning voor plaatsing in containers toevoegen aan ASP.NET- en Console-projecten die gebruikmaken van het volledige .NET-framework.

> [!NOTE]
> .NET **Core**-projecten worden momenteel niet ondersteund.

## <a name="prerequisites"></a>Vereisten

* Als u nog geen abonnement op Azure hebt, kunt u een [gratis account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

* Zorg er voor dat u [uw ontwikkelomgeving hebt ingesteld](service-fabric-mesh-howto-setup-developer-environment-sdk.md). Dit omvat het installeren van de Service Fabric-runtime, SDK, Docker, Visual Studio 2017 en het maken van een lokaal cluster.

## <a name="open-an-existing-net-app"></a>Een bestaande .NET-app openen

Open de app waaraan u ondersteuning voor containerindeling wilt toevoegen.

Als u een voorbeeld wilt uitproberen, kunt u het codevoorbeeld [eShop](https://github.com/MikkelHegn/ContainersSFLab) gebruiken. In de rest van dit artikel wordt ervan uitgegaan dat dit project wordt gebruikt, hoewel u deze stappen ook op uw eigen project kunt toepassen.

Download een exemplaar van het **eShop**-project:

```git
git clone https://github.com/MikkelHegn/ContainersSFLab.git
```

Als het downloaden is voltooid, opent u **ContainersSFLab\eShopLegacyWebFormsSolution\eShopLegacyWebForms.sln** in Visual Studio 2017.

## <a name="add-container-support"></a>Ondersteuning voor containers toevoegen
 
Voeg als volgt ondersteuning voor containerindeling toe aan een bestaand ASP.NET- of Console-project met de Service Fabric Mesh-hulpprogramma’s:

Klik in de oplossingsverkenner van Visual Studio met de rechtermuisknop op de projectnaam (in het voorbeeld is dat **eShopLegacyWebForms**) en kies **Toevoegen** > **Container Orchestrator Support**.
Het dialoogvenster **Add Container Orchestrator Support** verschijnt.

![Dialoogvenster containerindeling toevoegen in Visual Studio](./media/service-fabric-mesh-howto-containerize-vs/add-container-orchestration-support.png)

Kies **Service Fabric Mesh** in de vervolgkeuzelijst en klik op **OK**.


>[!NOTE]
> Met ingang van 2 november 2020 zijn [snelheidslimieten voor downloaden van toepassing](https://docs.docker.com/docker-hub/download-rate-limit/) op anonieme en geverifieerde aanvragen bij Docker Hub, vanuit accounts van gratis Docker-abonnementen. Deze aanvragen worden afgedwongen op IP-adres. Zie [Verifiëren met Docker Hub](../container-registry/buffer-gate-public-content.md#authenticate-with-docker-hub) voor meer informatie.
>
> Om te voor komen dat de tarieven beperkt zijn, moet u ervoor zorgen dat de standaard waarde `FROM microsoft/aspnet:4.7.2-windowsservercore-1803 AS base` in uw Dockerfile wordt vervangen door `FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-1803 AS base`

Er wordt gecontroleerd of Docker is geïnstalleerd; er wordt een Docker-bestand aan het project toegevoegd en er wordt een Docker-installatiekopie voor het project opgehaald.  
Er wordt een Service Fabric Mesh-toepassingsproject aan uw oplossing toegevoegd. Het bevat uw Mesh-publicatieprofielen en -configuratiebestanden. De naam van het project is dezelfde als die van uw project, waarbij er Application aan het eind is toegevoegd, bijvoorbeeld **eShopLegacyWebFormsApplication**. 

In het nieuwe Mesh-project ziet u twee mappen:
- **App-resources**: deze bevat YAML-bestanden waarmee aanvullende Mesh-resources worden beschreven, zoals het netwerk.
- **Serviceresources**: deze bevat het bestand service.yaml, waarmee wordt beschreven hoe uw app moet worden uitgevoerd als deze is geïmplementeerd.

Zodra ondersteuning voor containerindeling aan de app is toegevoegd, kunt u op **F5** drukken om fouten in uw .NET-app op te sporen in uw lokale Service Fabric Mesh-cluster. Hier ziet u de ASP.NET-app van eShop die wordt uitgevoerd op een Service Fabric Mesh-cluster: 

![eShop-app](./media/service-fabric-mesh-howto-containerize-vs/eshop-running.png)

U kunt nu de app publiceren naar Azure Service Fabric Mesh.

## <a name="next-steps"></a>Volgende stappen

Een app publiceren naar Service Fabric Mesh: [Zelfstudie: een Service Fabric Mesh-toepassing implementeren](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)