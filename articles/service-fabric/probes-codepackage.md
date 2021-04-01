---
title: Tests voor Azure Service Fabric
description: Een beproefde test voor de liveiteit in azure Service Fabric model leren met behulp van de manifest bestanden van de toepassing en service.
ms.topic: conceptual
author: tugup
ms.author: tugup
ms.date: 3/12/2020
ms.openlocfilehash: 07a1b836ca7ea79244e303f54654dfcaa6e5fcb9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "82137583"
---
# <a name="liveness-probe"></a>Test voor de duur van de liveiteit
Vanaf versie 7,1 ondersteunt Azure Service Fabric een mechanisme voor het testen van de levens duur van toepassingen in [containers][containers-introduction-link] . Met een onlineresponder-test kunt u de levens duur van een container toepassing rapporteren. deze wordt opnieuw opgestart als deze niet snel reageert.
Dit artikel bevat een overzicht van hoe u een bewerkings sonde kunt definiëren met behulp van manifest bestanden.

Voordat u verder gaat met dit artikel, kunt u vertrouwd raken met het [service Fabric-toepassings model][application-model-link] en het [model van service Fabric hosting][hosting-model-link].

> [!NOTE]
> De duur van de live-test wordt alleen ondersteund voor containers in de NAT-netwerk modus.

## <a name="semantics"></a>Semantiek
U kunt slechts één duur van de eerste maal per container opgeven en het gedrag ervan bepalen door gebruik te maken van de volgende velden:

* `initialDelaySeconds`: De eerste vertraging in seconden voor het uitvoeren van de test na het starten van de container. De ondersteunde waarde is **int**. De standaard waarde is 0 en het minimum is 0.

* `timeoutSeconds`: De periode in seconden waarna de test is mislukt, als deze nog niet is voltooid. De ondersteunde waarde is **int**. De standaard waarde is 1 en het minimum is 1.

* `periodSeconds`: De periode in seconden om de frequentie van de test op te geven. De ondersteunde waarde is **int**. De standaard waarde is 10 en het minimum is 1.

* `failureThreshold`: Als we deze waarde hebben bereikt, wordt de container opnieuw opgestart. De ondersteunde waarde is **int**. De standaard waarde is 3 en het minimum is 1.

* `successThreshold`: Bij een fout kan de test als geslaagd worden beschouwd, dan moet deze met succes worden uitgevoerd voor deze waarde. De ondersteunde waarde is **int**. De standaard waarde is 1 en het minimum is 1.

Er kan Maxi maal één test op één wille keurig moment zijn. Als de test niet wordt voltooid in de tijd die is ingesteld in **timeoutSeconds**, wacht u en telt u de tijd op naar de **failureThreshold**. 

Daarnaast genereren Service Fabric de volgende test [status rapporten][health-introduction-link] op **DeployedServicePackage**:

* `OK`: De test slaagt voor de waarde die is ingesteld in **successThreshold**.

* `Error`: De test **failureCount**  ==   **failureThreshold** voordat de container opnieuw wordt opgestart.

* `Warning`: 
    * De test is mislukt en **failureCount**  <  **failureThreshold**. Dit status rapport blijft totdat **failureCount** de waarde die is ingesteld in **failureThreshold** of **successThreshold** bereikt.
    * Als de fout is opgetreden, blijft de waarschuwing aanwezig, maar worden opeenvolgende geslaagde successen bijgewerkt.

## <a name="specifying-a-liveness-probe"></a>Een test voor een liveiteit opgeven

U kunt een sonde opgeven in het ApplicationManifest.xml bestand onder **ServiceManifestImport**.

De test kan een van de volgende zijn:

* HTTP
* TCP
* Exec 

### <a name="http-probe"></a>HTTP-test

Voor een HTTP-test stuurt Service Fabric een HTTP-aanvraag naar de poort en het pad dat u opgeeft. Een retour code die groter is dan of gelijk is aan 200, en kleiner is dan 400, geeft aan dat de bewerking is geslaagd.

Hier volgt een voor beeld van hoe u een HTTP-test opgeeft:

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <HttpGet Path="/" Port="8081" Scheme="http">
              <HttpHeader Name="Foo" Value="Val"/>
              <HttpHeader Name="Bar" Value="val1"/>
            </HttpGet>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

De HTTP-test heeft aanvullende eigenschappen die u kunt instellen:

* `path`: Het pad dat in de HTTP-aanvraag moet worden gebruikt.

* `port`: De poort die moet worden gebruikt voor tests. Deze eigenschap is verplicht. Het bereik is 1 tot en met 65535.

* `scheme`: Het schema dat moet worden gebruikt om verbinding te maken met het code pakket. Als deze eigenschap is ingesteld op HTTPS, wordt de verificatie van het certificaat overgeslagen. De standaard instelling is HTTP.

* `httpHeader`: De headers die in de aanvraag moeten worden ingesteld. U kunt meerdere headers opgeven.

* `host`: Het IP-adres van de host waarmee verbinding moet worden gemaakt.

### <a name="tcp-probe"></a>TPC-test

Voor een TCP-test probeert Service Fabric een socket in de container te openen met behulp van de opgegeven poort. Als er een verbinding tot stand kan worden gebracht, wordt de test als geslaagd beschouwd. Hier volgt een voor beeld van hoe u een test kunt opgeven die gebruikmaakt van een TCP-socket:

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <TcpSocket Port="8081"/>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

### <a name="exec-probe"></a>Exec-test

Met deze test wordt een **exec** -opdracht in de container uitgegeven en wordt gewacht tot de opdracht is voltooid.

> [!NOTE]
> De **exec** -opdracht gebruikt een door komma's gescheiden teken reeks. De opdracht in het volgende voor beeld werkt voor een Linux-container.
> Als u een Windows-container wilt testen, gebruikt u **cmd**.

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <Exec>
              <Command>ping,-c,2,localhost</Command>
            </Exec>
          </Probe>        
       </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

## <a name="next-steps"></a>Volgende stappen
Zie het volgende artikel voor verwante informatie:
* [Service Fabric en containers][containers-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[health-introduction-link]: service-fabric-health-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md

