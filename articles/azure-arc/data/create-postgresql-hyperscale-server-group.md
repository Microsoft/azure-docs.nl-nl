---
title: Een Azure Database for PostgreSQL grootschalige-Server groep maken op Azure Arc
description: Een Azure Database for PostgreSQL grootschalige-Server groep maken op Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: 4ff45eea8e07a282d8529c745344c11706bc27bb
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387985"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Een PostgreSQL Hyperscale-servergroep met Azure Arc maken

In dit document worden de stappen beschreven voor het maken van een PostgreSQL grootschalige-Server groep in azure Arc.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="getting-started"></a>Aan de slag
Als u al bekend bent met de onderwerpen onder kunt u deze alinea overs Laan.
Er zijn belang rijke onderwerpen die u mogelijk wilt lezen voordat u doorgaat met maken:
- [Overzicht van gegevens services die zijn ingeschakeld voor Azure Arc](overview.md)
- [Connectiviteitsmodi en -vereisten](connectivity.md)
- [Opslag configuraties en Kubernetes opslag concepten](storage-configuration.md)
- [Kubernetes-resource model](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

Als u liever zonder zelf een volledige omgeving hoeft in te richten, kunt u snel aan de slag met [Azure Arc](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) op Azure Kubernetes service (AKS), AWS elastische Kubernetes service (EKS), Google Cloud Kubernetes Engine (GKE) of in een Azure-VM.


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

Implementeer deze stap voordat u verdergaat met de volgende stap. Als u een PostgreSQL grootschalige-Server groep wilt implementeren op Red Hat open Shift in een ander project dan de standaard, moet u de volgende opdrachten uitvoeren op uw cluster om de beveiligings beperkingen bij te werken. Met deze opdracht worden de benodigde bevoegdheden verleend aan de service accounts die uw PostgreSQL grootschalige-Server groep zullen uitvoeren. De beveiligings context constraint (SCC) **_Arc-data-SCC_** is het account dat u hebt toegevoegd tijdens de implementatie van de Azure Arc-gegevens controller.

```console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

_**Server-group-name** is de naam van de Server groep die u tijdens de volgende stap gaat maken._
   
Raadpleeg voor meer informatie over SCCs in open Shift de documentatie van open [SHIFT](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html).
U kunt nu de volgende stap implementeren.

## <a name="create-an-azure-database-for-postgresql-hyperscale-server-group"></a>Een Azure Database for PostgreSQL grootschalige-Server groep maken

Als u een Azure Database for PostgreSQL grootschalige-Server groep wilt maken op Azure Arc, gebruikt u de volgende opdracht:

```console
azdata arc postgres server create -n <name> --workers <# worker nodes with #>=2> --storage-class-data <storage class name> --storage-class-logs <storage class name> --storage-class-backups <storage class name>

#Example
#azdata arc postgres server create -n postgres01 --workers 2
```

> [!IMPORTANT]
> - De opslag klasse die wordt gebruikt voor back-ups (_--Storage-Class-backups-SCB_) wordt standaard ingesteld op de gegevens opslag klasse van de gegevens controller als deze niet is ingevoerd.
> - Als u een server groep wilt herstellen naar een afzonderlijke server groep (zoals herstel op tijdstip van punt), moet u uw server groep configureren om Pvc's te gebruiken met de ReadWriteMany-toegangs modus. Dit is vereist om dit te doen bij het maken van de Server groep. Het kan niet worden gewijzigd nadat het is gemaakt. Lees voor meer informatie:
>    - [Een server groep maken die gereed is voor back-ups en herstel bewerkingen](backup-restore-postgresql-hyperscale.md#create-a-server-group-that-is-ready-for-backups-and-restores)
>    - [Beperkingen van Azure Arc enabled PostgreSQL grootschalige](limitations-postgresql-hyperscale.md)


> [!NOTE]
> - **Er zijn andere opdracht regel parameters beschikbaar.  Bekijk de volledige lijst met opties door uit te voeren `azdata arc postgres server create --help` .**
>
> - De eenheid die wordt geaccepteerd door de para meter--volume-size-* is een Kubernetes-resource hoeveelheid (een geheel getal gevolgd door een van deze SI-toereikende waarden (T, G, M, K, m) of hun kracht van twee equivalenten (TI, GI, MI, ki)).
> - Namen moeten uit 12 tekens of minder bestaan en voldoen aan de naamgevings regels voor DNS.
> - U wordt gevraagd het wacht woord voor de _post gres_ Standard-gebruiker met beheerders rechten in te voeren.  U kunt de interactieve prompt overs Laan door de `AZDATA_PASSWORD` sessie omgevings variabele in te stellen voordat u de opdracht maken uitvoert.
> - Als u de gegevens controller hebt geïmplementeerd met behulp van AZDATA_USERNAME en AZDATA_PASSWORD sessie omgevingsvariabelen in dezelfde terminal sessie, worden de waarden voor AZDATA_PASSWORD ook gebruikt voor het implementeren van de Server groep PostgreSQL grootschalige. Als u liever een ander wacht woord gebruikt, moet u (1) de waarde voor AZDATA_PASSWORD bijwerken of (2) de AZDATA_PASSWORD omgevings variabele verwijderen of de waarde ervan verwijderen wordt gevraagd een wacht woord interactief in te voeren wanneer u een server groep maakt.
> - De naam van de standaard gebruiker van de beheerder voor de PostgreSQL grootschalige-data base-engine is _post gres_ en kan op dit moment niet worden gewijzigd.
> - Als u een PostgreSQL grootschalige-Server groep maakt, worden resources in azure niet direct geregistreerd. Als onderdeel van het proces van het uploaden van [resource-inventaris](upload-metrics-and-logs-to-azure-monitor.md)  -of [gebruiks gegevens](view-billing-data-in-azure.md) naar Azure, worden de resources in azure gemaakt en kunt u uw resources in de Azure Portal bekijken.



## <a name="list-your-azure-database-for-postgresql-server-groups-created-in-your-arc-setup"></a>Uw Azure Database for PostgreSQL Server groepen weer geven die zijn gemaakt in uw Arc-installatie

Als u de PostgreSQL grootschalige-Server groepen op Azure Arc wilt weer geven, gebruikt u de volgende opdracht:

```console
azdata arc postgres server list
```


```output
Name        State     Workers
----------  --------  ---------
postgres01  Ready     2
```

## <a name="get-the-endpoints-to-connect-to-your-azure-database-for-postgresql-server-groups"></a>De eind punten ophalen om verbinding te maken met uw Azure Database for PostgreSQL Server-groepen

Als u de eind punten voor een PostgreSQL-exemplaar wilt weer geven, voert u de volgende opdracht uit:

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

U kunt het eind punt PostgreSQL-exemplaar gebruiken om verbinding te maken met de PostgreSQL grootschalige-Server groep vanuit uw favoriete hulp programma:  [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio), [Pgcli](https://www.pgcli.com/) psql, pgAdmin, enzovoort.

Als u een virtuele machine van Azure gebruikt om te testen, volgt u de onderstaande instructies:

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

- Lees de concepten en instructies van Azure Database for PostgreSQL grootschalige om uw gegevens over meerdere grootschalige-knoop punten van PostgreSQL te verdelen en om te profiteren van de kracht van Azure Database for PostgreSQL grootschalige. :
    * [Knooppunten en tabellen](../../postgresql/concepts-hyperscale-nodes.md)
    * [Toepassingstype bepalen](../../postgresql/concepts-hyperscale-app-type.md)
    * [Een distributiekolom kiezen](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Tabelcolocatie](../../postgresql/concepts-hyperscale-colocation.md)
    * [Tabellen distribueren en bewerken](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Een multi tenant-data base ontwerpen](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Een real-time analyse dashboard ontwerpen](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* In de bovenstaande documenten slaat u de secties **voor het aanmelden bij de Azure Portal**, & **een Azure database for PostgreSQL-grootschalige maken (Citus)**. Implementeer de resterende stappen in de implementatie van Azure Arc. Deze secties zijn specifiek voor de Azure Database for PostgreSQL grootschalige (Citus) die worden aangeboden als een PaaS-service in de Azure-Cloud, maar de andere onderdelen van de documenten zijn rechtstreeks van toepassing op uw PostgreSQL grootschalige van Azure Arc.

- [Uw Azure Database for PostgreSQL Hyperscale-servergroep uitschalen](scale-out-postgresql-hyperscale-server-group.md)
- [Opslag configuraties en Kubernetes opslag concepten](storage-configuration.md)
- [Claims voor permanente volumes uitbreiden](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Kubernetes-resource model](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
