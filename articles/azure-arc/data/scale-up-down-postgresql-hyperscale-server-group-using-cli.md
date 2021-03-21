---
title: Een Azure Database for PostgreSQL grootschalige-Server groep omhoog en omlaag schalen met CLI (azdata of kubectl)
description: Een Azure Database for PostgreSQL grootschalige-Server groep omhoog en omlaag schalen met CLI (azdata of kubectl)
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 4461fb6904d51ee8d740b633a2d0028658ac2ced
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101687546"
---
# <a name="scale-up-and-down-an-azure-database-for-postgresql-hyperscale-server-group-using-cli-azdata-or-kubectl"></a>Een Azure Database for PostgreSQL grootschalige-Server groep omhoog en omlaag schalen met CLI (azdata of kubectl)



Het kan zijn dat u de kenmerken of de definitie van een server groep moet wijzigen. Bijvoorbeeld:

- Omhoog of omlaag schalen van het aantal vCores dat door elk van de coördinator of de worker-knoop punten wordt gebruikt
- Omhoog of omlaag schalen van het geheugen dat door elk van de coördinator of de worker-knoop punten wordt gebruikt

In deze hand leiding wordt uitgelegd hoe u vCore en/of geheugen kunt schalen.

Als u de vCore of de geheugen instellingen van uw server groep omhoog of omlaag schaalt, hebt u de mogelijkheid om een minimum en/of maximum in te stellen voor elk van de vCore-en geheugen instellingen. Als u de Server groep wilt configureren voor het gebruik van een specifiek aantal vCore of een specifieke hoeveelheid geheugen, stelt u de minimale instellingen in op de waarde max.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="show-the-current-definition-of-the-server-group"></a>De huidige definitie van de Server groep weer geven

Voer een van de volgende opdrachten uit om de huidige definitie van de Server groep weer te geven en te zien wat de huidige vCore en geheugen instellingen zijn:

### <a name="cli-with-azdata"></a>CLI met azdata

```console
azdata arc postgres server show -n <server group name>
```
### <a name="cli-with-kubectl"></a>CLI met kubectl

```console
kubectl describe postgresql-12/<server group name> [-n <namespace name>]
```
> [!NOTE]
> Als u een server groep van PostgreSQL versie 11 hebt gemaakt, voert u `kubectl describe postgresql-11/<server group name>` in plaats daarvan uit.

Hiermee wordt de configuratie van de Server groep geretourneerd. Als u de Server groep hebt gemaakt met de standaard instellingen, ziet u de definitie als volgt:

```console
"scheduling": {
      "default": {
        "resources": {
          "requests": {
            "memory": "256Mi"
          }
        }
      }
    },
```

## <a name="interpret-the-definition-of-the-server-group"></a>De definitie van de Server groep interpreteren

In de definitie van een server groep is de sectie **' planning '** de sectie met de instellingen van het minimale/maximale vCore per knoop punt en het minimale/maximale geheugen per knoop punt. In deze sectie worden de maximum instellingen gehandhaafd in een Subsectie met de naam **' limieten '** en worden de minimale instellingen persistent gemaakt in de Subsectie **' aanvragen '**.

Als u de minimum instellingen instelt die afwijken van de maximale instellingen, zorgt de configuratie ervoor dat aan uw server groep de aangevraagde bronnen worden toegewezen als dat nodig is. De limiet die u hebt ingesteld, overschrijdt niet.

De resources (vCores en geheugen) die daad werkelijk door uw server groep worden gebruikt, zijn tot de maximale instellingen en zijn afhankelijk van de werk belastingen en de bronnen die beschikbaar zijn op het cluster. Als u de instellingen niet instelt op Maxi maal, kan uw server groep Maxi maal alle bronnen gebruiken die het Kubernetes-cluster toewijst aan de Kubernetes-knoop punten waarop uw server groep is gepland.

De instellingen vCore en geheugen zijn van toepassing op elk van de PostgreSQL grootschalige-knoop punten (coördinator knooppunt en worker-knoop punten). Het wordt nog niet ondersteund om de definities van het coördinator knooppunt en de worker-knoop punten afzonderlijk in te stellen.

In een standaard configuratie wordt alleen het minimale geheugen ingesteld op 256Mi, omdat dit de minimale hoeveelheid geheugen is die wordt aanbevolen om PostgreSQL grootschalige uit te voeren.

> [!NOTE]
> Het instellen van een minimum betekent niet dat de Server groep dat minimum moet gebruiken. Als de Server groep deze nodig heeft, is het gegarandeerd dat er mini maal dit minimum wordt toegewezen. Laten we bijvoorbeeld eens overwegen `--minCpu 2` . Dit betekent niet dat de Server groep altijd Mini maal twee vCores gebruikt. In plaats daarvan houdt dit in dat de Server groep kan beginnen met het gebruik van minder dan 2 vCores als het niet nodig is dat er een groot deel wordt toegewezen aan een van de twee vCores als deze later nodig is. Het impliceert dat het Kubernetes-cluster bronnen toewijst aan andere workloads op een zodanige manier dat er 2 vCores aan de Server groep kan worden toegewezen als deze ooit nodig is.

>[!NOTE]
>Voordat u de configuratie van uw systeem wijzigt, moet u ervoor zorgen dat u [hier](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities) vertrouwd bent met het Kubernetes-bron model

## <a name="scale-up-the-server-group"></a>De Server groep omhoog schalen

De instellingen die u gaat instellen, moeten worden overwogen in de configuratie die u voor uw Kubernetes-cluster hebt ingesteld. Zorg ervoor dat u geen waarden instelt die niet aan uw Kubernetes-cluster kunnen voldoen. Dat kan leiden tot fouten of onvoorspelbaar gedrag. Als de status van uw server groep bijvoorbeeld gedurende een lange periode na het wijzigen van de configuratie wordt _bijgewerkt_ , is het mogelijk dat u de onderstaande para meters instelt op waarden die niet voldoen aan uw Kubernetes-cluster. Als dat het geval is, moet u de wijziging ongedaan maken of de _troubleshooting_section lezen.

Als voor beeld gaan we ervan uit dat u de definitie van de Server groep wilt opschalen naar:

- Min vCore = 2
- Maximum aantal vCore = 4
- Min. geheugen = 512 MB
- Maxi maal geheugen = 1 GB

U gebruikt een van de volgende methoden:

### <a name="cli-with-azdata"></a>CLI met azdata

```console
azdata arc postgres server edit -n <name of your server group> --cores-request <# core-request>  --cores-limit <# core-limit>  --memory-request <# memory-request>Mi  --memory-limit <# memory-limit>Mi
```

> [!CAUTION]
> Hieronder ziet u een voor beeld waarin wordt uitgelegd hoe u de opdracht kunt gebruiken. Voordat u een bewerkings opdracht uitvoert, moet u ervoor zorgen dat u de para meters instelt op waarden die het Kubernetes-cluster kan voldoen.

```console
azdata arc postgres server edit -n <name of your server group> --cores-request 2  --cores-limit 4  --memory-request 512Mi  --memory-limit 1024Mi
```

De opdracht wordt uitgevoerd wanneer wordt weer gegeven:

```console
<name of your server group> is Ready
```

> [!NOTE]
> Voer uit voor meer informatie over deze para meters `azdata arc postgres server edit --help` .

### <a name="cli-with-kubectl"></a>CLI met kubectl

```console
kubectl edit postgresql-12/<server group name> [-n <namespace name>]
```

Hiermee gaat u naar de VI-editor waarin u de configuratie kunt door lopen en wijzigen. Gebruik het volgende om de gewenste instelling toe te wijzen aan de naam van het veld in de specificatie:

> [!CAUTION]
> Hieronder ziet u een voor beeld waarin wordt uitgelegd hoe u de configuratie kunt bewerken. Voordat u de configuratie bijwerkt, moet u ervoor zorgen dat u de para meters instelt op waarden die het Kubernetes-cluster kan voldoen.

Bijvoorbeeld:
- Min vCore = 2-> scheduling\default\resources\requests\cpu
- Maximale vCore = 4-> scheduling\default\resources\limits\cpu
- Min. geheugen = 512 MB-> scheduling\default\resources\requests\cpu
- Maxi maal geheugen = 1 GB > scheduling\default\resources\limits\cpu

Als u niet bekend bent met de VI-editor, raadpleegt u een beschrijving van de opdrachten die u [hier](https://www.computerhope.com/unix/uvi.htm)mogelijk nodig hebt:
- bewerkings modus: `i`
- navigeren met pijlen
- _stop bewerken: `esc`
- _exit zonder op te slaan: `:qa!`
- _exit na het opslaan: `:qw!`


## <a name="show-the-scaled-up-definition-of-the-server-group"></a>De opgeschaalde definitie van de Server groep weer geven

Voer de opdracht opnieuw uit om de definitie van de Server groep weer te geven en controleer of deze is ingesteld als gewenst:

### <a name="cli-with-azdata"></a>CLI met azdata

```console
azdata arc postgres server show -n <the name of your server group>
```
### <a name="cli-with-kubectl"></a>CLI met kubectl

```console
kubectl describe postgresql-12/<server group name>  [-n <namespace name>]
```
> [!NOTE]
> Als u een server groep van PostgreSQL versie 11 hebt gemaakt, voert u `kubectl describe postgresql-11/<server group name>` in plaats daarvan uit.


Hier wordt de nieuwe definitie van de Server groep weer gegeven:

```console
"scheduling": {
      "default": {
        "resources": {
          "limits": {
            "cpu": "4",
            "memory": "1024Mi"
          },
          "requests": {
            "cpu": "2",
            "memory": "512Mi"
          }
        }
      }
    },
```

## <a name="scale-down-the-server-group"></a>De Server groep omlaag schalen

Als u de Server groep omlaag wilt schalen, voert u dezelfde opdracht uit, maar stelt u lagere waarden in voor de instellingen die u omlaag wilt schalen. Als u de aanvragen en/of limieten wilt verwijderen, geeft u de waarde ervan op als lege teken reeks.

## <a name="reset-to-default-values"></a>Standaard waarden opnieuw instellen
Als u de standaard waarden voor het aantal kern geheugens of aanvragen opnieuw wilt instellen, moet u de para meters bewerken en een lege teken reeks door geven in plaats van een werkelijke waarde. Voer de volgende opdrachten uit als u de para meter voor de kern limiet (CL) wilt resetten:
- op een Linux-client:

```console
    azdata arc postgres server edit -n <servergroup name> -cl ""
```

- op een Windows-client: 
 
```console
    azdata arc postgres server edit -n <servergroup name> -cl '""'
```


## <a name="next-steps"></a>Volgende stappen

- [Uw Azure Database for PostgreSQL Hyperscale-servergroep uitschalen](scale-out-postgresql-hyperscale-server-group.md)
- [Opslag configuraties en Kubernetes opslag concepten](storage-configuration.md)
- [Kubernetes-resource model](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
