---
title: Een app in een zelfstandig cluster installeren
description: In deze zelfstudie leert u hoe u een toepassing kunt installeren in uw zelfstandige Service Fabric-cluster.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: ae946321b34f12c816a717db4a3d07f57feefe52
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96485357"
---
# <a name="tutorial-deploy-an-application-on-your-service-fabric-standalone-cluster"></a>Zelfstudie: Een toepassing implementeren in een zelfstandig Service Fabric-cluster

Zelfstandige Service Fabric-clusters bieden u de mogelijkheid om uw eigen omgeving te kiezen en een cluster te maken als onderdeel van de benadering "Elk besturingssysteem, elke cloud" die we in Service Fabric hanteren. In deze serie zelfstudies maakt u een op AWS gehost zelfstandig cluster en implementeert u er een toepassing in.

Deze zelfstudie is deel drie van een serie.  Zelfstandige Service Fabric-clusters bieden u de mogelijkheid om uw eigen omgeving te kiezen en een cluster te maken als onderdeel van de benadering "Elk besturingssysteem, elke cloud" die we in Service Fabric hanteren. Deze zelfstudie laat zien hoe u de AWS-infrastructuur kunt maken die nodig is om dit zelfstandige cluster te hosten.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * De voorbeeld-app downloaden
> * In het cluster implementeren

## <a name="prerequisites"></a>Vereisten

Voor u met deze zelfstudie begint:

* [Installeer Visual Studio 2019](https://www.visualstudio.com/) en installeer de workloads **Azure-ontwikkeling** en **ASP.NET-ontwikkeling en webontwikkeling**.
* [Installeer de Service Fabric-SDK](service-fabric-get-started.md).

## <a name="download-the-voting-sample-application"></a>De voorbeeldtoepassing om te stemmen downloaden

Voer in een opdrachtvenster de volgende opdracht uit om de voorbeeld-app-opslagplaats te klonen op de lokale computer.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>De app in het Service Fabric-cluster implementeren

Wanneer de toepassing is gedownload, kunt u deze rechtstreeks vanuit Visual Studio implementeren naar een cluster.

1. Open Visual Studio

2. Selecteer **Bestand** > **Openen**

3. Navigeer naar de map waarin u de git-opslagplaats hebt gekloond en selecteer Voting.sln

4. Klik met de rechtermuisknop op het toepassingsproject `Voting` in Solution Explorer en kies **Publiceren**

5. Selecteer de vervolgkeuzelijst voor het **verbindingseindpunt** en voer de openbare DNS-naam van een van de knooppunten in uw cluster in.  Bijvoorbeeld `ec2-34-215-183-77.us-west-2.compute.amazonaws.com:19000`. Er wordt niet automatisch een FQDN (Fully Qualified Domain Name) toegewezen in Azure. U kunt deze echter eenvoudig [instellen op de overzichtspagina van de VM.](../virtual-machines/create-fqdn.md)

6. Open uw voorkeursbrowser en typ het clusteradres in (het eindpunt van de verbinding, deze app wordt geïmplementeerd op poort 8080 - bijvoorbeeld ec2-34-215-183-77.us-west-2.compute.amazonaws.com:8080).

    ![API-reactie van cluster](./media/service-fabric-tutorial-standalone-cluster/deployed-app.png)

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een toepassing in uw cluster kunt implementeren:

> [!div class="checklist"]
> * De voorbeeld-app downloaden
> * In het cluster implementeren

Ga door naar deel van de serie om uw cluster op te schonen.

> [!div class="nextstepaction"]
> [Uw resources opschonen](service-fabric-tutorial-standalone-clean-up.md)