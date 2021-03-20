---
title: Richtlijnen voor grootte
description: Plan voor de grootte van een implementatie van Azure Arc enabled Data Services.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 6e7f2e445c3e4e8df7420c0587e156968f3a2c92
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94542674"
---
# <a name="sizing-guidance"></a>Richt lijnen voor grootte

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="overview-of-sizing-guidance"></a>Overzicht van richt lijnen voor formaat wijziging

Bij het plannen van de implementatie van Azure Arc Data Services moet u plannen voor de juiste hoeveelheid reken kracht, het geheugen en de opslag die nodig is voor het uitvoeren van de Azure-Arc-gegevens controller en voor het aantal SQL Managed instance-en PostgreSQL grootschalige-Server groepen dat u gaat implementeren.  Omdat Azure Arc enabled Data Services is geïmplementeerd op Kubernetes, hebt u de flexibiliteit om gedurende een periode extra capaciteit toe te voegen aan uw Kubernetes-cluster door extra reken knooppunten of opslag toe te voegen.  Deze hand leiding bevat richt lijnen voor de minimale vereisten en biedt richt lijnen voor de aanbevolen grootte voor enkele algemene vereisten.

## <a name="general-sizing-requirements"></a>Algemene grootte vereisten

> [!NOTE]
> Als u niet bekend bent met de concepten in dit artikel, kunt u meer lezen over de [Kubernetes resource governance](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) en [Kubernetes size-notatie](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes).

Het aantal kernen moet een geheel getal zijn dat groter is dan of gelijk is aan een waarde van 1.

Wanneer u azdata gebruikt voor de implementatie, moeten de geheugen waarden worden opgegeven in een macht van twee getallen, d.w.z. met behulp van de achtervoegsels: Ki, Mi of GI.

De limiet waarden moeten altijd groter zijn dan de aanvraag waarde, indien opgegeven.

De limiet waarden voor kern geheugens zijn de factureer bare metriek voor SQL Managed instance en PostgreSQL grootschalige server groups.

## <a name="minimum-deployment-requirements"></a>Minimale implementatie vereisten

De minimale grootte van de Azure Arc ingeschakelde Data Services-implementatie kan worden beschouwd als de Azure Arc-gegevens controller plus één SQL Managed instance plus één PostgreSQL grootschalige-Server groep met twee worker-knoop punten.  Voor deze configuratie hebt u ten minste 16 GB RAM en 4 kern geheugens van de _beschik bare_ capaciteit van uw Kubernetes-cluster nodig.  Zorg ervoor dat u beschikt over een minimale Kubernetes-knooppunt grootte van 8 GB RAM-geheugen en 4 kernen en een totale capaciteit van 16 GB RAM-geheugen dat beschikbaar is voor al uw Kubernetes knooppunten.  U kunt bijvoorbeeld één knoop punt hebben van 32 GB RAM-geheugen en 4 kernen, of u kunt 2 knoop punten hebben met een RAM-geheugen en 4 kernen per generatie.

Zie het artikel over [Storage-configuratie](storage-configuration.md) voor meer informatie over opslag grootte.

## <a name="data-controller-sizing-details"></a>Details van de grootte van gegevens controller

De gegevens controller is een verzameling van de peulen die worden geïmplementeerd in uw Kubernetes-cluster om een API, de controller service, de Boots Trapper en de bewakings databases en-dash boards te bieden.  In deze tabel worden de standaard waarden voor geheugen-en CPU-aanvragen en-limieten beschreven.

|Pod-naam|CPU-aanvraag|Geheugen aanvraag|CPU-limiet|Geheugen limiet|Notities|
|---|---|---|---|---|---|
|**Boots Trapper**|100 miljoen|100Mi|200m|200Mi||
|**over**|400m|2Gi|1800m|2Gi||
|**controldb**|200m|3Gi|800m|6Gi||
|**controlwd**|10 miljoen|100Mi|100 miljoen|200Mi||
|**logsdb**|200m|1600Mi|2|1600Mi||
|**logsui**|100 miljoen|500Mi|2|2Gi||
|**metricsdb**|200m|800Mi|400m|2Gi||
|**metricsdc**|100 miljoen|200Mi|200m|300Mi|Metricsdc is een daemonset die wordt gemaakt op elk van de Kubernetes-knoop punten in uw cluster.  De getallen in de tabel hier zijn _per knoop punt_. Als u allowNodeMetricsCollection = False instelt in het implementatie profiel bestand voordat u de gegevens controller maakt, wordt de metricsdc-daemonset niet gemaakt.|
|**metricsui**|20 miljoen|200Mi|2,5 miljard|200Mi||
|**mgmtproxy**|200m|250Mi|2,5 miljard|500Mi||

U kunt de standaard instellingen voor de controldb en het gehele besturings element in uw implementatie profiel bestand of datacontroller YAML-bestand overschrijven.  Voorbeeld:

```yaml
  resources:
    controller:
      limits:
        cpu: "1000m"
        memory: "3Gi"
      requests:
        cpu: "800m"
        memory: "2Gi"
    controllerDb:
      limits:
        cpu: "800m"
        memory: "8Gi"
      requests:
        cpu: "200m"
        memory: "4Gi"
```

Zie het artikel over [Storage-configuratie](storage-configuration.md) voor meer informatie over opslag grootte.

## <a name="sql-managed-instance-sizing-details"></a>Details van de grootte van SQL Managed instance

Elk SQL-beheerd exemplaar moet de volgende minimale resource aanvragen hebben:
- Geheugen: 2Gi
- Kernen: 1

Elk exemplaar van SQL Managed instance pod dat wordt gemaakt, heeft drie containers:

|Containernaam|CPU-aanvraag|Geheugen aanvraag|CPU-limiet|Geheugen limiet|Notities|
|---|---|---|---|---|---|
|fluentbit|100 miljoen|100Mi|Niet opgegeven|Niet opgegeven|De fluentbit container resource aanvragen bevinden zich _naast_ de aanvragen die zijn opgegeven voor het SQL Managed instance.||
|Arc-sqlmi|Gebruiker opgegeven of niet opgegeven.|Gebruiker opgegeven of niet opgegeven.|Gebruiker opgegeven of niet opgegeven.|Gebruiker opgegeven of niet opgegeven.||
|verzamelde|Niet opgegeven|Niet opgegeven|Niet opgegeven|Niet opgegeven||

De standaard grootte van het volume voor alle permanente volumes is 5Gi.

## <a name="postgresql-hyperscale-server-group-sizing-details"></a>Details van de PostgreSQL grootschalige-Server groep

Elk PostgreSQL grootschalige-server groeps knooppunt moet de volgende minimale resource aanvragen hebben:
- Geheugen: 256Mi
- Kernen: 1

Elke PostgreSQL grootschalige-server groeps coördinator of werk pod die wordt gemaakt, heeft drie containers:

|Containernaam|CPU-aanvraag|Geheugen aanvraag|CPU-limiet|Geheugen limiet|Notities|
|---|---|---|---|---|---|
|fluentbit|100 miljoen|100Mi|Niet opgegeven|Niet opgegeven|De fluentbit-container resource aanvragen bevinden zich _naast_ de aanvragen die zijn opgegeven voor de knoop punten van de postgresql grootschalige-Server groep.|
|postgres|Gebruiker opgegeven of niet opgegeven.|Gebruiker opgegeven of 256Mi (standaard).|Gebruiker opgegeven of niet opgegeven.|Gebruiker opgegeven of niet opgegeven.||
|telegrafie|Niet opgegeven|Niet opgegeven|Niet opgegeven|Niet opgegeven||

## <a name="cumulative-sizing"></a>Cumulatieve grootte

De totale grootte van een omgeving die vereist is voor Azure Arc ingeschakelde gegevens Services, is voornamelijk een functie van het aantal en de grootte van de data base-exemplaren die worden gemaakt.  De totale grootte kan moeilijk te voors pellen dat het aantal instanties groeit en krimpen en dat de hoeveelheid resources die nodig zijn voor elk data base-exemplaar wordt gewijzigd.

De basislijn grootte voor een bepaalde Azure Arc enabled Data Services-omgeving is de grootte van de gegevens controller waarvoor 4 kernen en 16 GB RAM-geheugen vereist zijn.  Hier kunt u het cumulatieve totaal aan kernen en geheugen dat is vereist voor de data base-exemplaren toevoegen.  Voor een SQL-beheerd exemplaar is het aantal peulen gelijk aan het aantal SQL Managed instances dat is gemaakt.  Voor PostgreSQL grootschalige-Server groepen wordt het aantal peulen gelijk aan het aantal worker-knoop punten plus één voor het coördinator knooppunt.  Als u bijvoorbeeld een PostgreSQL-Server groep met 3 worker-knoop punten hebt, is het totale aantal peulen vier.

Naast de kernen en het geheugen dat u voor elk data base-exemplaar vraagt, moet u 250m van kernen en 250Mi van het RAM-geheugen voor de agent containers toevoegen.

Hier volgt een voor beeld van een berekenings grootte.

Vereisten:

- **"SQL1"**: 1 SQL Managed instance met 16 GB RAM, 4 kern geheugens
- **"SQL2"**: 1 SQL Managed instance met 256 GB RAM, 16 kernen
- **"Postgres1"**: 1 postgresql grootschalige-Server groep met vier werk nemers van 12 GB aan RAM-geheugen, 4 kernen

Grootte berekeningen:

- De grootte van "SQL1" is: 1 pod * ([16 GI RAM, 4 kernen] + [250Mi RAM, 250m cores]) voor de agents per pod = 16,25 GI RAM, 4,25 kernen.
- De grootte van "SQL2" is: 1 pod * ([256 GI RAM, 16 kernen] + + [250Mi RAM, 250m cores]) voor de agents per pod = 256,25 GI RAM, 16,25 kernen.
- De totale grootte van SQL 1 en SQL 2 is: (16,25 GB + 256,25 GI) = 272,5 GB RAM, (4,25 kernen + 16,25 cores) = 20,5 kernen.

- De grootte van ' Postgres1 ' is: (4 worker peul + 1 coördinator pod) * ([12 GB RAM, 4 kernen] + [250Mi RAM, 250m cores]) voor de agents per pod = 61,25 GB RAM, 21,25 kernen.

- De totale capaciteit die is vereist voor de data base-exemplaren is: 272,5 GB RAM, 20,5 kernen voor SQL + 61,25 GB RAM, 21,25 kernen voor PostgreSQL grootschalige = 333,75 GB RAM, 42,5 kernen.

- De totale capaciteit die is vereist voor de data base-exemplaren plus de gegevens controller is: 333,75 GB RAM, 42,5 kernen voor de data base-exemplaren + 16 GB RAM, 4 kernen voor de gegevens controller = 349,75 GB RAM, 46,5 kernen.

Zie het artikel over [Storage-configuratie](storage-configuration.md) voor meer informatie over opslag grootte.

## <a name="other-considerations"></a>Andere overwegingen

Houd er wel voor dat een bepaalde aanvraag voor het instellen van data base-instanties voor kern geheugens of RAM niet groter mag zijn dan de beschik bare capaciteit van de Kubernetes-knoop punten in het cluster.  Als het grootste Kubernetes-knoop punt dat u in uw Kubernetes-cluster hebt, bijvoorbeeld 256 GB aan RAM-geheugen en 24 kernen is, kunt u geen data base-exemplaar maken met een aanvraag van 512 GB RAM-geheugen en 48-kernen.  

Het is een goed idee om ten minste 25% van de beschik bare capaciteit over de Kubernetes-knoop punten te onderhouden, zodat Kubernetes efficiënt kan worden gepland en de groei op aanvraag kan worden geschaald en langere termijn.  

Vergeet niet om in de berekenings berekeningen de resource vereisten van het Kubernetes-systeem en alle andere werk belastingen toe te voegen die capaciteit kunnen delen met Azure-Arc-gegevens Services op hetzelfde Kubernetes-cluster.

Als u de maximale Beschik baarheid wilt hand haven tijdens gepland onderhoud en nood continuïteit, moet u plannen dat ten minste één van de Kubernetes-knoop punten in uw cluster op een bepaald moment niet beschikbaar is.  Kubernetes probeert het meren schema dat werd uitgevoerd op een bepaald knoop punt opnieuw te plannen dat is opgetreden voor onderhoud of vanwege een fout.  Als er geen beschik bare capaciteit is op de resterende knoop punten, worden deze niet opnieuw gepland om te worden gemaakt totdat de capaciteit weer beschikbaar is.  Wees extra voorzichtig met grote data base-exemplaren.  Als er bijvoorbeeld slechts één Kubernetes-knoop punt groot genoeg is om te voldoen aan de resource vereisten van een grote data base-instantie en dat knoop punt mislukt, kan Kubernetes de pod van het data base-exemplaar niet plannen op een ander Kubernetes-knoop punt.

Houd de [maximum limieten voor de grootte van een Kubernetes-cluster](https://kubernetes.io/docs/setup/best-practices/cluster-large/) in acht.

Uw Kubernetes-beheerder heeft mogelijk [resource quota](https://kubernetes.io/docs/concepts/policy/resource-quotas/) ingesteld voor uw naam ruimte/project.  Houd bij het plannen van de grootte van uw data base-exemplaar de volgende voor delen.
