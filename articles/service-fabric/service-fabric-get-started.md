---
title: Een Windows-ontwikkel omgeving instellen
description: Installeer de runtime, SDK en hulpprogramma's en maak een lokaal ontwikkelcluster. Zodra u dit hebt gedaan, kunt u toepassingen bouwen in Windows.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/16/2020
ms.custom: sfrev, devx-track-azurepowershell
ms.openlocfilehash: 4568b791db07eaa7a513c42066b22df04b24cb6d
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103010300"
---
# <a name="prepare-your-development-environment-on-windows"></a>Uw ontwikkelomgeving voorbereiden in Windows

> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [Mac OS X](service-fabric-get-started-mac.md)
>
>

Als u [Azure Service Fabric-toepassingen][1] wilt bouwen en uitvoeren op een Windows-ontwikkelmachine, moet u de Service Fabric-runtime, SDK en hulpprogramma's installeren. U moet ook [de uitvoering van de Windows Power shell-scripts inschakelen](#enable-powershell-script-execution) die in de SDK zijn opgenomen.

## <a name="prerequisites"></a>Vereisten

### <a name="supported-operating-system-versions"></a>Ondersteunde versies van besturingssystemen

De volgende versies van besturingssystemen worden ondersteund voor de ontwikkeling:

* Windows 7
* Windows 8/Windows 8.1
* Windows Server 2012 R2
* Windows Server 2016
* Windows 10

> [!NOTE]
> Ondersteuning voor Windows 7:
> - Windows 7 bevat standaard alleen Windows PowerShell 2.0. Voor Service Fabric PowerShell-cmdlets is PowerShell 3.0 of hoger vereist. U kunt [Windows Power shell 5,1 downloaden][powershell5-download] van het micro soft Download centrum.
> - Er is geen omgekeerde proxy voor Service Fabric beschikbaar in Windows 7.

## <a name="install-the-sdk-and-tools"></a>De SDK en hulpprogramma's installeren

Web platform Installer (WebPI) is de aanbevolen manier om de SDK en hulpprogram ma's te installeren. Als er runtime-fouten worden ontvangen met behulp van WebPI, kunt u ook directe koppelingen naar de installatie Programma's in de release opmerkingen vinden voor een specifieke Service Fabric versie. De release opmerkingen vindt u in de verschillende release mededelingen op het [service Fabric team blog](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric).

> [!NOTE]
> Lokale Service Fabric ontwikkeling van cluster upgrades worden niet ondersteund.

### <a name="to-use-visual-studio-2017-or-2019"></a>Visual Studio 2017 of 2019 gebruiken

De Service Fabric-Hulpprogram Ma's maken deel uit van de Azure Development-werk belasting in Visual Studio 2017 en 2019. Schakel deze workload in als onderdeel van de Visual Studio-installatie.
Bovendien moet u de Microsoft Azure Service Fabric SDK en runtime installeren met behulp van het webplatforminstallatieprogramma.

* [Microsoft Azure Service Fabric SDK installeren][core-sdk]

### <a name="sdk-installation-only"></a>Alleen SDK-installatie

Als u alleen de SDK nodig hebt, kunt u dit pakket installeren:

* [Microsoft Azure Service Fabric SDK installeren][core-sdk]

De huidige versies zijn:

* Service Fabric SDK en Hulpprogram Ma's 4.2.477
* Service Fabric runtime-7.2.477

Zie [service Fabric versies](service-fabric-versions.md) voor een lijst met ondersteunde versies

> [!NOTE]
> Clusters met één computer (OneBox) worden niet ondersteund voor upgrades van toepassingen of clusters; Verwijder het OneBox-cluster en maak het opnieuw als u een cluster upgrade moet uitvoeren of als u problemen hebt met het uitvoeren van een toepassings upgrade. 

## <a name="enable-powershell-script-execution"></a>Uitvoering van PowerShell-script inschakelen

Service Fabric gebruikt Windows PowerShell-scripts om een lokaal ontwikkelcluster te maken en om toepassingen vanuit Visual Studio te implementeren. Standaard worden deze scripts door Windows geblokkeerd zodat ze niet worden uitgevoerd. Als u ze wilt inschakelen, moet u het PowerShell-uitvoeringsbeleid wijzigen. Open PowerShell als een beheerder en voer de volgende opdracht in:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="install-docker-optional"></a>Docker installeren (optioneel)

[Service Fabric is een container Orchestrator](service-fabric-containers-overview.md) voor het implementeren van micro Services op een cluster van machines. Als u Windows-container toepassingen wilt uitvoeren op uw lokale ontwikkel cluster, moet u eerst docker voor Windows installeren. Haal [docker CE voor Windows (stabiel) op](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Nadat u Docker hebt geïnstalleerd en gestart, klikt u met de rechtermuisknop op het systeemvakpictogram en selecteert u **Overschakelen naar Windows-containers**. Deze stap is vereist voor het uitvoeren van Docker-installatiekopieën onder Windows.

## <a name="next-steps"></a>Volgende stappen

Nu u uw ontwikkelingsomgeving hebt ingesteld, kunt u apps ontwikkelen en uitvoeren.

* [Meer informatie over het maken, implementeren en beheren van toepassingen](service-fabric-tutorial-create-dotnet-app.md)
* [Meer informatie over de programmeermodellen: Reliable Services en Reliable Actors](service-fabric-choose-framework.md)
* [Voorbeelden van Service Fabric-code op GitHub bekijken](/samples/browse/?products=azure)
* [Uw cluster visualiseren door gebruik te maken van Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
* [Een Linux-ontwikkelomgeving voorbereiden in Windows](service-fabric-local-linux-cluster-windows.md)
* Meer informatie over [ondersteuningsopties voor Service Fabric](service-fabric-support.md)

[1]: https://azure.microsoft.com/campaigns/service-fabric/ "Service Fabric-campagnepagina"
[2]: https://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI-koppeling"
[full-bundle-dev15]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI-koppeling"
[core-sdk]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Core SDK WebPI-koppeling"
[powershell5-download]:https://www.microsoft.com/download/details.aspx?id=54616
