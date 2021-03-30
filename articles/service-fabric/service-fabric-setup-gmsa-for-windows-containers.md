---
title: GMSA instellen voor Azure Service Fabric container Services
description: Meer informatie over het instellen van door een groep beheerde service accounts (gMSA) voor een container die wordt uitgevoerd in azure Service Fabric.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: d34b4c6e11628b6a4843f8a9077ebf69c9e023fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260879"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>GMSA instellen voor Windows-containers die worden uitgevoerd op Service Fabric

Voor het instellen van gMSA (door een groep beheerde service accounts), wordt een referentie-specificatie bestand ( `credspec` ) op alle knoop punten in het cluster geplaatst. Het bestand kan worden gekopieerd op alle knoop punten met behulp van een VM-extensie.  Het `credspec` bestand moet de gMSA-account gegevens bevatten. `credspec`Zie [een referentie specificatie maken](/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec)voor meer informatie over het bestand. De referentie specificatie en de- `Hostname` tag worden opgegeven in het manifest van de toepassing. Het `Hostname` label moet overeenkomen met de naam van het gMSA-account dat de container wordt uitgevoerd.  `Hostname`Met de tag kan de container zichzelf verifiëren bij andere services in het domein met behulp van Kerberos-verificatie.  In het volgende code fragment wordt een voor beeld gegeven voor het opgeven `Hostname` en de `credspec` in het manifest van de toepassing:

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
Lees de volgende artikelen als volgende stap:

* [Een Windows-container implementeren voor het Service Fabric op Windows Server 2016](service-fabric-get-started-containers.md)
* [Een docker-container implementeren voor Service Fabric op Linux](service-fabric-get-started-containers-linux.md)
