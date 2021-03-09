---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: e17f14d3acf8d74d1715d14fbd914ee536d29931
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510982"
---
De vermeldingen in de documenttoewijzing `deploymentconfig.json` voor de parameters voor [AksWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration). In de volgende tabel wordt de toewijzing tussen de entiteiten in het JSON-document en de parameters beschreven voor de methode:

| JSON-entiteit | Methodeparameter | Beschrijving |
| ----- | ----- | ----- |
| `computeType` | NA | Het rekendoel. Voor AKS moet de waarde `aks` zijn. |
| `autoScaler` | NA | Bevat configuratie-elementen voor automatische schaalaanpassing. Raadpleeg de tabel voor automatische schaalaanpassing. |
| &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | Hiermee wordt aangegeven of automatische schaalaanpassing moet worden ingeschakeld voor de webservice. Als `numReplicas` = `0`, `True`; anders, `False`. |
| &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | Het minimale aantal containers dat moet worden gebruikt wanneer deze webservice automatisch wordt geschaald. Standaard `1`. |
| &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | Het maximale aantal containers dat moet worden gebruikt wanneer deze webservice automatisch wordt geschaald. Standaard `10`. |
| &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | Hoe vaak de automatische schaalaanpassing probeert deze webservice te schalen. Standaard `1`. |
| &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | Het doelgebruik (in procenten van 100) dat de automatische schaalaanpassing moet proberen aan te houden voor deze webservice. Standaard `70`. |
| `dataCollection` | NA | Bevat configuratie-elementen voor gegevensverzameling. |
| &emsp;&emsp;`storageEnabled` | `collect_model_data` | Hiermee wordt aangegeven of gegevensverzameling voor modellen voor de webservice moet worden ingeschakeld. Standaard `False`. |
| `authEnabled` | `auth_enabled` | Hiermee wordt aangegeven of sleutelverificatie voor de webservice moet worden ingeschakeld. Zowel `tokenAuthEnabled` als `authEnabled` kunnen niet `True` zijn. Standaard `True`. |
| `tokenAuthEnabled` | `token_auth_enabled` | Hiermee wordt aangegeven of tokenverificatie voor de webservice moet worden ingeschakeld. Zowel `tokenAuthEnabled` als `authEnabled` kunnen niet `True` zijn. Standaard `False`. |
| `containerResourceRequirements` | NA | Container voor de CPU en geheugenentiteiten. |
| &emsp;&emsp;`cpu` | `cpu_cores` | Het aantal CPU-kernen dat moet worden toegewezen voor deze webservice. Standaard `0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | De hoeveelheid geheugen in GB dat moet worden toegewezen voor deze webservice. Standaard `0.5` |
| `appInsightsEnabled` | `enable_app_insights` | Hiermee wordt aangegeven of Application Insights-logboekregistratie voor de webservice moet worden ingeschakeld. Standaard `False`. |
| `scoringTimeoutMs` | `scoring_timeout_ms` | Een verplichte time-out voor het beoordelen van oproepen naar de webservice. Standaard `60000`. |
| `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | Het maximumaantal gelijktijdige aanvragen per knooppunt voor deze webservice. Standaard `1`. |
| `maxQueueWaitMs` | `max_request_wait_time` | De maximale tijd dat een verzoek in de wachtrij blijft (in milliseconden) voordat een 503-fout wordt geretourneerd. Standaard `500`. |
| `numReplicas` | `num_replicas` | Het aantal containers dat moet worden toegewezen voor deze webservice. Er is geen standaardwaarde. Als deze parameter niet is ingesteld, wordt automatisch schalen standaard ingeschakeld. |
| `keys` | NA | Bevat configuratie-elementen voor sleutels. |
| &emsp;&emsp;`primaryKey` | `primary_key` | Een primaire verificatiesleutel die voor deze webservice kan worden gebruikt |
| &emsp;&emsp;`secondaryKey` | `secondary_key` | Een secundaire verificatiesleutel die voor deze webservice kan worden gebruikt |
| `gpuCores` | `gpu_cores` | Het aantal GPU-kernen (per containerreplica) die voor deze webservice moet worden toegewezen. Standaard is 1. Ondersteunt allen gehele getallen. |
| `livenessProbeRequirements` | NA | Bevat configuratie-elementen voor activiteitstestsvereisten. |
| &emsp;&emsp;`periodSeconds` | `period_seconds` | Hoe vaak (in seconden) de activiteitstest moet worden uitgevoerd. De standaardwaarde is 10 seconden. Minimumwaarde is 1. |
| &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | Aantal seconden nadat de container is gestart voordat de activiteitstests worden gestart. Standaardwaarde is 310 |
| &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | Aantal seconden waarna de activiteitstests wordt gestopt. Standaardwaarde is 2 seconden. Minimumwaarde is 1 |
| &emsp;&emsp;`successThreshold` | `success_threshold` | De minimale opeenvolgende successen voor de activiteitstest voordat de test succesvol of mislukt is. Standaardwaarde is 1. Minimumwaarde is 1. |
| &emsp;&emsp;`failureThreshold` | `failure_threshold` | Wanneer een pod wordt gestart en de activiteitstest mislukt, probeert Kubernetes failureThreshold keer voordat hij stopt. Standaardwaarde is 3. Minimumwaarde is 1. |
| `namespace` | `namespace` | De Kubernetes-naamruimte waarin de webservice is geïmplementeerd. Maximaal 63 kleine alfanumerieke tekens ('a'-'z', '0'-'9') en streepjes ('-'). Het eerste en laatste teken kunnen geen streepjes zijn. |

De volgende JSON is een voorbeeldimplementatie die gebruikt kan worden met de CLI:

```json
{
    "computeType": "aks",
    "autoScaler":
    {
        "autoscaleEnabled": true,
        "minReplicas": 1,
        "maxReplicas": 3,
        "refreshPeriodInSeconds": 1,
        "targetUtilization": 70
    },
    "dataCollection":
    {
        "storageEnabled": true
    },
    "authEnabled": true,
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    }
}
```