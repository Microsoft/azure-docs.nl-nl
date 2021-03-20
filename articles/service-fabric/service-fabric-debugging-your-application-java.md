---
title: Fouten opsporen in uw toepassing in eclips
description: Verbeter de betrouw baarheid en prestaties van uw services door deze te ontwikkelen en fouten op te sporen in eclips op een lokaal ontwikkelings cluster.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-java
ms.openlocfilehash: d321e0c10b66a15e6cb309cefe711602fa12957c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91534105"
---
# <a name="debug-your-java-service-fabric-application-using-eclipse"></a>Fout opsporing voor uw Java Service Fabric-toepassing met behulp van eclips
> [!div class="op_single_selector"]
> * [Visual Studio-CSharp](service-fabric-debugging-your-application.md) 
> * [Eclips/java](service-fabric-debugging-your-application-java.md)
> 

1. Start een lokaal ontwikkel cluster door de stappen in [uw service Fabric-ontwikkel omgeving](service-fabric-get-started-linux.md)in te stellen.

2. Update entryPoint.sh van de service waarvoor u fouten wilt opsporen, zodat het Java-proces wordt gestart met externe debug-para meters. Dit bestand bevindt zich op de volgende locatie: `ApplicationName\ServiceNamePkg\Code\entrypoint.sh` . Poort 8001 is in dit voorbeeld voor foutopsporing ingesteld.

    ```sh
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar myapp.jar
    ```
3. Werk het toepassings manifest bij door het aantal exemplaren of het aantal replica's voor de service waarvoor fouten worden opgespoord, in te stellen op 1. Deze instelling voorkomt conflicten voor de poort die wordt gebruikt voor het opsporen van fouten. Stel bijvoorbeeld voor stateless services `InstanceCount="1"` in en stel voor stateful services de doel- en min-replicasetgrootten als volgt in op 1: `TargetReplicaSetSize="1" MinReplicaSetSize="1"`.

4. De toepassing implementeren.

5. Selecteer in de eclips IDE de optie **Run-> debug-configuraties: > externe Java-toepassing en eigenschappen van de invoer verbinding** en stel de eigenschappen als volgt in:

   ```
   Host: ipaddress
   Port: 8001
   ```
6.  Stel onderbrekings punten in op de gewenste posities en spoor fouten op in de toepassing.

Als de toepassing vastloopt, wilt u mogelijk ook coredumps inschakelen. Voer `ulimit -c` uit in een shell en als deze 0 retourneert, zijn coredumps niet ingeschakeld. Als u onbeperkte coredumps wilt inschakelen, voert u de volgende opdracht uit: `ulimit -c unlimited` . U kunt ook de status controleren met behulp van de opdracht `ulimit -a` .  Als u het pad voor het genereren van coredump wilt bijwerken, voert u uit `echo '/tmp/core_%e.%p' | sudo tee /proc/sys/kernel/core_pattern` . 

### <a name="next-steps"></a>Volgende stappen

* [Verzamelen van logboeken met Linux Azure Diagnostics](./service-fabric-diagnostics-event-aggregation-lad.md).
* [Services lokaal controleren en diagnosticeren](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).
