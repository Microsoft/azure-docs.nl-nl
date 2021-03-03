---
title: Een PostgreSQL Hyperscale-servergroep met Azure Arc maken
description: Een PostgreSQL Hyperscale-servergroep met Azure Arc maken
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: 046f9d80c034e1ac1f2e7ffe144b4f389861b043
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101687937"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Een PostgreSQL Hyperscale-servergroep met Azure Arc maken

In dit document worden de stappen beschreven voor het maken van een PostgreSQL grootschalige-Server groep in azure Arc.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="getting-started"></a>Aan de slag
Als u al bekend bent met de onderstaande onderwerpen, kunt u deze alinea overs Laan.
Er zijn belang rijke onderwerpen die u mogelijk wilt lezen voordat u doorgaat met maken:
- [Overzicht van gegevens services die zijn ingeschakeld voor Azure Arc](overview.md)
- [Connectiviteitsmodi en -vereisten](connectivity.md)
- [Opslag configuraties en Kubernetes opslag concepten](storage-configuration.md)
- [Kubernetes-resource model](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

Als u liever dingen wilt proberen zonder zelf een volledige omgeving in te richten, kunt u snel aan de slag met [Azure Arc](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) op Azure Kubernetes service (AKS), AWS elastische Kubernetes service (EKS), Google Cloud Kubernetes Engine (GKE) of in een Azure-VM.


## <a name="login-to-the-azure-arc-data-controller"></a>Meld u aan bij de Azure-Arc-gegevens controller

Voordat u een exemplaar kunt maken, moet u zich eerst aanmelden bij de Azure Arc-gegevens controller. Als u al bent aangemeld bij de gegevens controller, kunt u deze stap overs Laan.

```console
azdata login
```

Vervolgens wordt u gevraagd om de gebruikers naam, het wacht woord en de naam ruimte van het systeem.  

> Als u het script hebt gebruikt om de gegevens controller te maken, moet de naam ruimte **Arc** zijn

```console
Namespace: arc
Username: arcadmin
Password:
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>Voorlopige en tijdelijke stap voor alleen openshift-gebruikers
Implementeer deze stap voordat u verdergaat met de volgende stap. Als u een PostgreSQL grootschalige-Server groep wilt implementeren op Red Hat open Shift in een ander project dan de standaard, moet u de volgende opdrachten uitvoeren op uw cluster om de beveiligings beperkingen bij te werken. Met deze opdracht worden de benodigde bevoegdheden verleend aan de service accounts die uw PostgreSQL grootschalige-Server groep zullen uitvoeren. De beveiligings context constraint (SCC) Arc-data-SCC is het account dat u hebt toegevoegd tijdens de implementatie van de Azure Arc-gegevens controller.

```Console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

**Server-group-name is de naam van de Server groep die u tijdens de volgende stap gaat maken.**

Raadpleeg voor meer informatie over SCCs in open Shift de documentatie van open [SHIFT](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html). U kunt nu de volgende stap implementeren.


## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Een PostgreSQL Hyperscale-servergroep met Azure Arc maken

Als u een Azure-PostgreSQL grootschalige-Server groep wilt maken op uw Arc-gegevens controller, gebruikt u de opdracht `azdata arc postgres server create` voor het door geven van verschillende para meters.

Bekijk de uitvoer van de opdracht voor meer informatie over de para meters die u kunt instellen tijdens het aanmaak tijdstip:
```console
azdata arc postgres server create --help
```

De belangrijkste para meters moeten rekening houden met het volgende:
- **de naam van de Server groep** die u wilt implementeren. Geef aan `--name` of `-n` gevolgd door een naam waarvan de lengte mag niet langer zijn dan 11 tekens.

- **de versie van de postgresql-engine** die u wilt implementeren: standaard is dit versie 12. Als u versie 12 wilt implementeren, kunt u deze para meter weglaten of een van de volgende para meters door geven: `--engine-version 12` of `-ev 12` . Geef of op om versie 11 te implementeren `--engine-version 11` `-ev 11` .

- **het aantal worker-knoop punten** dat u wilt implementeren om uit te schalen en mogelijk betere prestaties te bereiken. Lees de [concepten over post gres grootschalige](concepts-distributed-postgres-hyperscale.md)voordat u verder gaat. Als u het aantal worker-knoop punten wilt opgeven dat moet worden geïmplementeerd, gebruikt u de para meter `--workers` of `-w` gevolgd door een geheel getal dat groter is dan of gelijk is aan 2. Als u bijvoorbeeld een server groep wilt implementeren met twee worker-knoop punten, geeft u op `--workers 2` of `-w 2` . Hiermee maakt u drie verschillende versies, een voor het coördinator knooppunt/-exemplaar en twee voor de worker-knoop punten/instanties (één voor elk van de werk nemers).

- **de opslag klassen** die u wilt gebruiken voor uw server groep. Het is belang rijk dat u de opslag klasse instelt op het moment dat u een server groep implementeert, omdat deze niet kan worden gewijzigd nadat u de implementatie hebt geïmplementeerd. Als u na de implementatie de opslag klasse wilde wijzigen, moet u de gegevens extra heren, uw server groep verwijderen, een nieuwe server groep maken en de gegevens importeren. U kunt opgeven welke opslag klassen moeten worden gebruikt voor de gegevens, logboeken en de back-ups. Als u geen opslag klassen opgeeft, worden standaard de opslag klassen van de gegevens controller gebruikt.
    - Als u de opslag klasse voor de gegevens wilt instellen, geeft u de para meter op `--storage-class-data` of `-scd` gevolgd door de naam van de opslag klasse.
    - Als u de opslag klasse voor de logboeken wilt instellen, geeft u de para meter op `--storage-class-logs` of `-scl` gevolgd door de naam van de opslag klasse.
    - de opslag klasse voor de back-ups instellen: in deze preview van de Azure Arc enabled PostgreSQL grootschalige zijn er twee manieren om opslag klassen in te stellen, afhankelijk van welke typen back-up-en herstel bewerkingen u wilt uitvoeren. We werken eraan om deze ervaring te vereenvoudigen. U geeft een opslag klasse of een volume claim koppeling op. Een volume claim koppeling is een combi natie van een bestaande permanente volume claim (in dezelfde naam ruimte) en het volume type (en optionele meta gegevens, afhankelijk van het type volume), gescheiden door een dubbele punt. Het permanente volume wordt in elke pod voor de PostgreSQL-Server groep gekoppeld.
        - Als u wilt dat alleen volledige data base wordt teruggezet, stelt u de para meter in `--storage-class-backups` of `-scb` gevolgd door de naam van de opslag klasse.
        - Als u van plan bent om zowel de volledige data base opnieuw op te slaan als herstel punt in tijd, stelt u de para meter in `--volume-claim-mounts` of `-vcm` gevolgd door de naam van een volume claim en een volume type.

Houd er rekening mee dat wanneer u de opdracht maken uitvoert, u wordt gevraagd het wacht woord van de standaard gebruiker met beheerders rechten in te voeren `postgres` . De naam van die gebruiker kan niet worden gewijzigd in deze preview. U kunt de interactieve prompt overs Laan door de `AZDATA_PASSWORD` sessie omgevings variabele in te stellen voordat u de opdracht maken uitvoert.

### <a name="examples"></a>Voorbeelden

**Voer de volgende opdracht uit om een server groep van post gres versie 12 met de naam postgres01 te implementeren met twee worker-knoop punten die dezelfde opslag klassen gebruiken als de gegevens controller:**
```console
azdata arc postgres server create -n postgres01 --workers 2
```

**Voer de volgende stappen uit om een server groep van post gres versie 12 met de naam postgres01 te implementeren met twee worker-knoop punten die dezelfde opslag klassen gebruiken als de gegevens controller voor gegevens en Logboeken, maar de specifieke opslag klasse voor het uitvoeren van zowel volledige herstel bewerkingen als herstel tijdstippen.**

 In dit voor beeld wordt ervan uitgegaan dat uw server groep wordt gehost in een Azure Kubernetes service-cluster (AKS). In dit voor beeld wordt azurefile-Premium als opslag klassen naam gebruikt. U kunt het onderstaande voor beeld aanpassen zodat dit overeenkomt met uw omgeving. Houd er rekening mee dat **AccessModes ReadWriteMany is vereist** voor deze configuratie.  

Maak eerst een YAML-bestand dat de onderstaande beschrijving van het back-uppvc bevat en geef het de naam CreateBackupPVC. yml bijvoorbeeld:
```console
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: backup-pvc
  namespace: arc
spec:
  accessModes:
    - ReadWriteMany
  volumeMode: Filesystem
  resources:
    requests:
      storage: 100Gi
  storageClassName: azurefile-premium
```

Maak vervolgens een PVC met behulp van de definitie die is opgeslagen in het YAML-bestand:

```console
kubectl create -f e:\CreateBackupPVC.yml -n arc
``` 

Maak vervolgens de Server groep:

```console
azdata arc postgres server create -n postgres01 --workers 2 -vcm backup-pvc:backup
```

> [!IMPORTANT]
> - Lees de [huidige beperkingen met betrekking tot het maken/herstellen van back-ups](limitations-postgresql-hyperscale.md#backup-and-restore).


> [!NOTE]  
> - Als u de gegevens controller hebt geïmplementeerd `AZDATA_USERNAME` met `AZDATA_PASSWORD` de variabelen voor de sessie omgeving in dezelfde terminal sessie, worden de waarden voor `AZDATA_PASSWORD` gebruikt voor het implementeren van de postgresql grootschalige-Server groep. Als u liever een ander wacht woord gebruikt, moet u (1) de waarde bijwerken voor `AZDATA_PASSWORD` of (2) de `AZDATA_PASSWORD` omgevings variabele verwijderen of (3) de waarde verwijderen om een wacht woord interactief in te voeren wanneer u een server groep maakt.
> - Als u een PostgreSQL grootschalige-Server groep maakt, worden resources in azure niet direct geregistreerd. Als onderdeel van het proces van het uploaden van [resource-inventaris](upload-metrics-and-logs-to-azure-monitor.md)  -of [gebruiks gegevens](view-billing-data-in-azure.md) naar Azure, worden de resources in azure gemaakt en kunt u uw resources in de Azure Portal bekijken.



## <a name="list-the-postgresql-hyperscale-server-groups-deployed-in-your-arc-data-controller"></a>De PostgreSQL grootschalige-Server groepen weer geven die zijn geïmplementeerd in uw Arc-gegevens controller

Voer de volgende opdracht uit om een lijst te geven van de PostgreSQL grootschalige-Server groepen die zijn geïmplementeerd in uw Arc-gegevens controller:

```console
azdata arc postgres server list
```


```output
Name        State     Workers
----------  --------  ---------
postgres01  Ready     2
```

## <a name="get-the-endpoints-to-connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Haal de eind punten op om verbinding te maken met uw PostgreSQL grootschalige-Server groepen van Azure Arc.

Als u de eind punten voor een PostgreSQL-Server groep wilt weer geven, voert u de volgende opdracht uit:

```console
azdata arc postgres endpoint list -n <server group name>
```
Bijvoorbeeld:
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

U kunt het eind punt PostgreSQL-exemplaar gebruiken om verbinding te maken met de PostgreSQL grootschalige-Server groep vanuit uw favoriete hulp programma:  [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio), [Pgcli](https://www.pgcli.com/) psql, pgAdmin, enzovoort. Wanneer u dit doet, maakt u verbinding met het coördinator knooppunt/-exemplaar, waarmee de query wordt doorgestuurd naar de juiste worker-knoop punten/-instanties als u gedistribueerde tabellen hebt gemaakt. Lees de [concepten van Azure Arc enabled postgresql grootschalige](concepts-distributed-postgres-hyperscale.md)voor meer informatie.

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Speciale opmerking over implementaties van virtuele Azure-machines

Wanneer u een virtuele machine van Azure gebruikt, wordt het _open bare_ IP-adres niet weer gegeven op het IP-adres van het eind punt. Als u het open bare IP-adres wilt zoeken, gebruikt u de volgende opdracht:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

U kunt het open bare IP-adres vervolgens combi neren met de poort om verbinding te maken.

Mogelijk moet u ook de poort van de PostgreSQL grootschalige-Server groep beschikbaar maken via de netwerk beveiligings gateway (NSG). Als u verkeer wilt toestaan via de (NSG), moet u een regel toevoegen die u kunt doen met behulp van de volgende opdracht:

Als u een regel wilt instellen, moet u de naam van uw NSG kennen. U bepaalt de NSG met behulp van de volgende opdracht:

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

Zodra u de naam van de NSG hebt, kunt u een firewall regel toevoegen met behulp van de volgende opdracht. De voorbeeld waarden maken hier een NSG-regel voor poort 30655 en staan de verbinding vanaf **elk** bron-IP-adres toe.  Dit is geen beveiligings best practice!  U kunt de mogelijkheden beter vergren delen door een waarde voor de voor voegsels voor de bron adressen op te geven die specifiek is voor uw client-IP-adres of een IP-adres bereik dat de IP-adressen van uw team of organisatie omvat.

Vervang de waarde van de para meter--destination-port-Ranges hieronder door het poort nummer dat u hebt ontvangen van de bovenstaande opdracht ' azdata-Arc post gres-server lijst '.

```azurecli
az network nsg rule create -n db_port --destination-port-ranges 30655 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="connect-with-azure-data-studio"></a>Verbinding maken met Azure Data Studio

Open Azure Data Studio en maak verbinding met uw exemplaar met het externe IP-adres van het eind punt en het bovenstaande poort nummer, en het wacht woord dat u hebt opgegeven op het moment dat u het exemplaar hebt gemaakt.  Als PostgreSQL niet beschikbaar is in de vervolg keuzelijst *verbindings type* , kunt u de postgresql-extensie installeren door te zoeken naar postgresql op het tabblad extensies.

> [!NOTE]
> U moet op de knop [Geavanceerd] klikken in het deel venster verbinding om het poort nummer in te voeren.

Als u een virtuele Azure-machine gebruikt, hebt u het _open bare_ IP-adres nodig dat toegankelijk is via de volgende opdracht:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

## <a name="connect-with-psql"></a>Verbinding maken met psql

Om toegang te krijgen tot uw PostgreSQL grootschalige-Server groep, geeft u het externe eind punt door voor de PostgreSQL grootschalige-Server groep die u hebt opgehaald van de bovenstaande:

U kunt nu verbinding maken met een psql:

```console
psql postgresql://postgres:<EnterYourPassword>@10.0.0.4:30655
```

## <a name="next-steps"></a>Volgende stappen

- Lees de concepten en hand leidingen van Azure Database for PostgreSQL grootschalige om uw gegevens over meerdere grootschalige-knoop punten van PostgreSQL te verdelen en om te profiteren van betere prestaties mogelijk:
    * [Knooppunten en tabellen](../../postgresql/concepts-hyperscale-nodes.md)
    * [Toepassingstype bepalen](../../postgresql/concepts-hyperscale-app-type.md)
    * [Een distributiekolom kiezen](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Tabelcolocatie](../../postgresql/concepts-hyperscale-colocation.md)
    * [Tabellen distribueren en bewerken](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Een multi tenant-data base ontwerpen](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Een real-time analyse dashboard ontwerpen](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* In de bovenstaande documenten slaat u de secties **voor het aanmelden bij de Azure Portal**, & **een Azure database for PostgreSQL-grootschalige maken (Citus)**. Implementeer de resterende stappen in de implementatie van Azure Arc. Deze secties zijn specifiek voor de Azure Database for PostgreSQL grootschalige (Citus) die worden aangeboden als een PaaS-service in de Azure-Cloud, maar de andere onderdelen van de documenten zijn rechtstreeks van toepassing op uw PostgreSQL grootschalige van Azure Arc.

- [Uitschalen van uw Azure-boog ingeschakeld voor PostgreSQL grootschalige-Server groep](scale-out-postgresql-hyperscale-server-group.md)
- [Opslag configuraties en Kubernetes opslag concepten](storage-configuration.md)
- [Claims voor permanente volumes uitbreiden](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Kubernetes-resource model](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
