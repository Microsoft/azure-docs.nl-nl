---
title: Een Service Fabric-toepassing implementeren in een cluster in Azure
description: Meer informatie over hoe u een bestaande toepassing implementeert in een nieuw Azure Service Fabric-cluster vanuit Visual Studio.
author: athinanthny
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: mikhegn
ms.custom: mvc
ms.openlocfilehash: e35b655dc8b735214de891884fe40fb951dd16cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91441279"
---
# <a name="tutorial-deploy-a-service-fabric-application-to-a-cluster-in-azure"></a>Zelfstudie: Een Service Fabric-toepassing implementeren naar een cluster in Azure

Deze zelfstudie is deel twee van een serie. In deze zelfstudie ziet u hoe u een Azure Service Fabric-toepassing implementeert in een nieuw cluster in Azure.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een cluster maken.
> * Een toepassing implementeren in een extern cluster met behulp van Visual Studio.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * [Een .NET Service Fabric-toepassing bouwen](service-fabric-tutorial-create-dotnet-app.md).
> * De toepassing implementeren in een extern cluster.
> * [Een HTTPS-eindpunt toevoegen aan een front-end-service van ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md).
> * [CI/CD configureren met behulp van Azure-pijplijnen](service-fabric-tutorial-deploy-app-with-cicd-vsts.md).
> * [Controle en diagnostische gegevens voor de toepassing instellen](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="prerequisites"></a>Vereisten

Voor u met deze zelfstudie begint:

* Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan.
* [Installeer Visual Studio 2019](https://www.visualstudio.com/) en installeer de **Azure-ontwikkeling** en **ASP.NET-ontwikkeling en webontwikkeling**.
* [Installeer de Service Fabric-SDK](service-fabric-get-started.md).

> [!NOTE]
> Een gratis account voldoet mogelijk niet aan de vereisten voor het maken van een virtuele machine. Hiermee voorkomt u dat de zelfstudie is voltooid. Daarnaast kan een niet-werk-of niet-school account machtigingsproblemen ondervinden tijdens het maken van het certificaat op de sleutelkluis die is gekoppeld aan het cluster. Als er een fout optreedt die betrekking heeft op het maken van een certificaat, gebruikt u de portal om in plaats daarvan het cluster te maken. 

## <a name="download-the-voting-sample-application"></a>De voorbeeldtoepassing om te stemmen downloaden

Als u in [deel één van deze zelfstudiereeks](service-fabric-tutorial-create-dotnet-app.md) niet het voorbeeld van een stemtoepassing hebt gemaakt, kunt u dit downloaden. Voer in een opdrachtvenster de volgende code uit om de opslagplaats van de voorbeeldtoepassing te klonen op de lokale computer.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart 
```

Open als beheerder de toepassing in Visual Studio en maak de toepassing.

## <a name="create-a-cluster"></a>Een cluster maken

Nu de toepassing klaar is, kunt u een Service Fabric-cluster maken en vervolgens de toepassing in het cluster implementeren. Een [Service Fabric-cluster](./service-fabric-deploy-anywhere.md) is een met het netwerk verbonden reeks virtuele of fysieke machines waarop uw microservices worden geïmplementeerd en beheerd.

In deze zelfstudie maakt u een nieuw testcluster met drie knooppunten in de Visual Studio IDE en publiceert u de toepassing vervolgens naar dat cluster. Zie de [zelfstudie over het maken en beheren van een cluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) voor informatie over het maken van een productiecluster. U kunt de toepassing ook implementeren in een bestaand cluster dat u eerder hebt gemaakt via de [Microsoft Azure-portal](https://portal.azure.com), met behulp van [PowerShell](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)- of [Azure CLI](./scripts/cli-create-cluster.md)-scripts of vanuit een [Azure Resource Manager-sjabloon](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

> [!NOTE]
> De stemtoepassing, en veel andere toepassingen, maken gebruik van de omgekeerde proxy van Service Fabric voor de communicatie tussen services. Clusters die zijn gemaakt vanuit Visual Studio hebben de omgekeerde proxy standaard ingeschakeld. Als u implementeert in een bestaand cluster, moet u [de omgekeerde proxy inschakelen in het cluster](service-fabric-reverseproxy-setup.md) als u wilt dat de stemtoepassing naar behoren werkt.


### <a name="find-the-votingweb-service-endpoint"></a>Het service-eindpunt van VotingWeb vinden

De front-end webservice van de stemtoepassing luistert op een specifieke poort (8080 als u de stappen in [deel één van deze zelfstudiereeks](service-fabric-tutorial-create-dotnet-app.md) hebt gevolgd). Wanneer de toepassing in een cluster in Azure wordt geïmplementeerd, worden zowel het cluster als de toepassing achter een load balancer van Azure uitgevoerd. De toepassingspoort moet met behulp van een regel worden geopend in de load balancer voor het cluster van Azure. De regel verzendt binnenkomend verkeer via de load balancer door naar de webservice. De poort wordt gevonden in het bestand **VotingWeb/PackageRoot/ServiceManifest.xml** in het element **Eindpunt**. 

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

Noteer het service-eindpunt. Dit is nodig in een latere stap.  Als u in een bestaand cluster implementeert, opent u deze poort door een load-balancingregel te maken en te testen in de Azure load balancer met behulp van een [PowerShell-script](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) of via de load balancer voor dit cluster in de [Azure Portal ](https://portal.azure.com).

### <a name="create-a-test-cluster-in-azure"></a>Een testcluster maken in Azure
Klik in Solution Explorer met de rechtermuisknop op **Stemmen** en selecteer **Publiceren**.

Selecteer in **Verbindingseindpunt** de optie **Nieuw cluster maken**.  Als u in een bestaand cluster implementeert, selecteert u het clustereindpunt in de lijst.  Het dialoogvenster Service Fabric-cluster maken wordt geopend.

Voer op het tabblad **Cluster** de **Clusternaam** (bijvoorbeeld 'mytestcluster') in, selecteer uw abonnement, selecteer een regio voor het cluster (zoals US - zuid-centraal), voer het aantal clusterknooppunten in (we raden drie knooppunten voor een testcluster aan) en voer een resourcegroep (zoals 'mytestclustergroup') in. Klik op **Volgende**.

![Schermafbeelding met het tabblad Cluster van het dialoogvenster Service Fabric-cluster maken.](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

Voer op het tabblad **Certificaat** het wachtwoord en het uitvoerpad voor het clustercertificaat in. Er wordt een zelfondertekend certificaat als een PFX-bestand gemaakt en opgeslagen in het opgegeven uitvoerpad.  Het certificaat wordt gebruikt voor de beveiliging van zowel knooppunt-naar-knooppunt als client-naar-knooppunt.  Een zelfondertekend certificaat moet niet worden gebruikt voor productieclusters.  Dit certificaat wordt gebruikt door Visual Studio voor verificatie bij het cluster en de implementatie van toepassingen. Selecteer **Certificaat importeren** om de PFX in het certificaatarchief CurrentUser\My van uw computer te installeren.  Klik op **Volgende**.

![Schermafbeelding met het tabblad Certificaat van het dialoogvenster Service Fabric-cluster maken.](./media/service-fabric-tutorial-deploy-app-to-party-cluster/certificate.png)

Voer op het tabblad **VM-details** de **Gebruikersnaam** en het **Wachtwoord** in voor het clusterbeheeraccount.  Selecteer de **VM-installatiekopie** voor de clusterknooppunten en de **Grootte van virtuele machine** voor elk clusterknooppunt.  Klik op het tabblad **Geavanceerd**.

![Schermafbeelding met het tabblad Details VM van het dialoogvenster Service Fabric-cluster maken.](./media/service-fabric-tutorial-deploy-app-to-party-cluster/vm-detail.png)

Voer in **Poorten** het VotingWeb-service-eindpunt van de vorige stap (bijvoorbeeld 8080) in.  Wanneer het cluster is gemaakt, worden deze poorten geopend in de Azure load balancer voor het doorsturen van verkeer naar het cluster.  Klik op **Maken** om het cluster te maken. Dit duurt enkele minuten.

![Schermafbeelding met het tabblad Geavanceerd van het dialoogvenster Service Fabric-cluster maken.](./media/service-fabric-tutorial-deploy-app-to-party-cluster/advanced.png)

## <a name="publish-the-application-to-the-cluster"></a>De toepassing publiceren in het cluster

Wanneer het nieuwe cluster klaar is, kunt u het rechtstreeks vanuit Visual Studio implementeren naar de stemtoepassing.

Klik in Solution Explorer met de rechtermuisknop op **Stemmen** en selecteer **Publiceren**. Het dialoogvenster **Publiceren** wordt weergegeven.

Selecteer in **Verbindingseindpunt** het eindpunt van het cluster dat u in de vorige stap hebt gemaakt.  Bijvoorbeeld 'mytestcluster.southcentral.cloudapp.azure.com:19000'. Als u **Geavanceerde verbindingsparameters** selecteert, moet informatie over het certificaat automatisch worden ingevuld.  
![Een Service Fabric-toepassing publiceren](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

Selecteer **Publiceren**.

Wanneer de toepassing is geïmplementeerd, opent u een browser en voert u het clusteradres in gevolgd door **:8080**. Of voer een andere poort in, als er een is geconfigureerd. Een voorbeeld is `http://mytestcluster.southcentral.cloudapp.azure.com:8080`. U ziet dat de toepassing in het cluster in Azure wordt uitgevoerd. Probeer op de pagina voting web enkele stemopties toe te voegen en te verwijderen en stem op een of meer van deze opties.

![Voorbeeld van een Service Fabric-stemtoepassing](./media/service-fabric-tutorial-deploy-app-to-party-cluster/application-screenshot-new-azure.png)


## <a name="next-steps"></a>Volgende stappen
In dit deel van de zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een cluster maken.
> * Een toepassing implementeren in een extern cluster met behulp van Visual Studio.

Ga door naar de volgende zelfstudie:
> [!div class="nextstepaction"]
> [HTTPS inschakelen](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
