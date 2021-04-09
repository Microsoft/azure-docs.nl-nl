---
title: Een Windows-ontwikkel omgeving instellen voor Service Fabric mesh
description: Stel uw Windows-ontwikkelomgeving in, zodat u een Service Fabric Mesh-toepassing kunt maken en deze implementeren voor Azure Service Fabric Mesh.
author: georgewallace
ms.author: gwallace
ms.date: 12/12/2018
ms.topic: conceptual
ms.openlocfilehash: fc234f6954cf263423cc517bb3dda2ba2efa3358
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625732"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-mesh-apps"></a>Uw Windows-ontwikkelomgeving instellen voor het bouwen van Service Fabric-apps

> [!IMPORTANT]
> De preview-versie van Azure Service Fabric Mesh is buiten gebruik gesteld. Nieuwe implementaties zijn niet langer toegestaan via de API van Service Fabric net. Ondersteuning voor bestaande implementaties gaat door tot 28 april 2021.
> 
> Zie [Azure service Fabric Netpreview buiten](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)gebruik stellen voor meer informatie.

Als u Azure Service Fabric Mesh-toepassingen wilt bouwen en uitvoeren op een Windows-ontwikkelmachine, hebt u het volgende nodig:

* Docker
* Visual Studio 2017 of hoger
* Service Fabric Mesh runtime
* Service Fabric Mesh-SDK en -hulpprogramma's.

Plus een van de volgende versies van Windows:

* Windows 10 (Enterprise, Professional of Education) versie 1709 (Fall Creators update) of 1803 (Windows 10 April 2018 update)
* Windows Server versie 1709
* Windows Server versie 1803

Aan de hand van de volgende instructies kunt u alles installeren op basis van de Windows-versie die u gebruikt.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="visual-studio"></a>Visual Studio

Visual Studio 2017 of hoger is vereist voor de implementatie van Service Fabric-mesh-toepassingen. [Installeer versie 15.6.0][download-visual-studio] of hoger en schakel de volgende werkbelastingen in:

* ASP.NET-ontwikkeling en webontwikkeling
* Azure-ontwikkeling

## <a name="install-docker"></a>Docker installeren

Als Docker al is geïnstalleerd, moet u nagaan of u wel de meest recente versie hebt. Docker vraagt u mogelijk wanneer een nieuwe versie uitvalt, maar controleer hand matig of u de meest recente versie hebt.

#### <a name="install-docker-on-windows-10"></a>Docker installeren in Windows 10

Download en installeer de nieuwste versie van [Docker Community Edition voor Windows][download-docker] ter ondersteuning van de in containers verpakte Service Fabric-apps die door Service Fabric Mesh worden gebruikt.

Tijdens de installatie selecteert u **Use Windows containers instead of Linux containers** als daarom wordt gevraagd.

Als Hyper-V niet is ingeschakeld op uw computer, wordt het installatie programma van docker aangeboden om dit in te scha kelen. Klik op **OK** om dit te doen als u hierom wordt gevraagd.

#### <a name="install-docker-on-windows-server-2016"></a>Docker installeren in Windows Server 2016

Als Hyper-V niet is ingeschakeld, opent u PowerShell als een beheerder en voert u de volgende opdracht uit om Hyper-V in te schakelen. Start vervolgens uw computer opnieuw op. Zie [Docker Enterprise Edition for Windows Server][download-docker-server] (Docker Enterprise Edition voor Windows Server) voor meer informatie.

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

Start de computer opnieuw op.

Open PowerShell als een beheerder en voer de volgende opdracht uit om Docker te installeren:

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

## <a name="sdk-and-tools"></a>SDK's en hulpprogramma's

Installeer de Service Fabric Mesh-runtime, SDK en hulpprogramma's in de volgende volgorde.

1. Installeer de [Service Fabric Mesh SDK][download-sdkmesh] met behulp van Web Platform Installer. Hierdoor worden ook de Microsoft Azure Service Fabric SDK en -runtime geïnstalleerd.
2. Installeer de [extensie Visual Studio Service Fabric Mesh Tools (preview)][download-tools] vanaf Visual Studio Marketplace.

## <a name="build-a-cluster"></a>Een cluster bouwen

> [!IMPORTANT]
> Docker **moet** worden uitgevoerd voordat u een cluster kunt bouwen.
> Test of Docker wordt uitgevoerd door een terminalvenster te openen en `docker ps` uit te voeren om te zien of er een fout optreedt. Als er geen fout wordt aangegeven, wordt Docker uitgevoerd en kunt u een cluster gaan bouwen.

> [!Note]
> Als u een ontwikkelcomputer met Windows Fall Creators update (versie 1709) gebruikt, kunt u alleen Docker-installatiekopieën voor Windows-versie 1709 gebruiken.
> Als u een ontwikkelcomputer met Windows 10 april 2018 update (versie 1803) gebruikt, kunt u Docker-installatiekopieën voor Windows-versie 1709 en 1803 gebruiken.

Als u Visual Studio gebruikt, kunt u deze sectie overs Laan omdat Visual Studio een lokaal cluster maakt als u er nog geen hebt.

Voor de beste prestaties bij het opsporen van fouten bij het maken en uitvoeren van een enkele Service Fabric-app tegelijk maakt u een lokaal ontwikkel cluster met één knoop punt. Als u meerdere toepassingen tegelijk uitvoert, maakt u een lokaal ontwikkelings cluster met vijf knoop punten. Het cluster moet worden uitgevoerd wanneer u een Service Fabric Mesh-project implementeert of er fouten in opspoort.

Als u de runtime, SDK's en hulpprogramma's van Visual Studio Docker hebt geïnstalleerd en Docker wordt uitgevoerd, maakt u een ontwikkelcluster.

1. Sluit het PowerShell-venster.
2. Open als beheerder een nieuw PowerShell-venster met verhoogde bevoegdheid. Deze stap is nodig om de Service Fabric-modules te laden die onlangs zijn geïnstalleerd.
3. Voer de volgende PowerShell-opdracht uit om een ontwikkelcluster te maken:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateMeshCluster -CreateOneNodeCluster
    ```
4. Voer de volgende PowerShell-opdracht uit om het lokale hulpprogramma voor clusterbeheer te starten:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```
5. Zodra het hulpprogramma voor het beheer van het Service-cluster wordt uitgevoerd (dit wordt weergegeven in het systeemvak), klikt u er met de rechtermuisknop op en klikt u op **Lokaal cluster starten**.

![Afbeelding 1: Het lokale cluster starten](./media/service-fabric-mesh-howto-setup-developer-environment-sdk/start-local-cluster.png)

U kunt nu Service Fabric Mesh-toepassingen gaan maken.

## <a name="next-steps"></a>Volgende stappen

Lees de zelfstudie [Create an Azure Service Fabric app](service-fabric-mesh-tutorial-create-dotnetcore.md) (Een Azure Service Fabric-app maken).

Zoek antwoorden op [veelgestelde vragen en bekende problemen](service-fabric-mesh-faq.md).

[azure-cli-install]: /cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://aka.ms/sfmesh_vs2017tools
[download-visual-studio]: https://www.visualstudio.com/downloads/
