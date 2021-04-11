---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 4983c1e1e7f235fa7a5b748a0ce5b1c79176c849
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102511066"
---
De vermeldingen in de documenttoewijzing `deploymentconfig.json` voor de parameters voor [AciWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration). In de volgende tabel wordt de toewijzing tussen de entiteiten in het JSON-document en de parameters beschreven voor de methode:

| JSON-entiteit | Methodeparameter | Beschrijving |
| ----- | ----- | ----- |
| `computeType` | NA | Het rekendoel. Voor ACI moet de waarde `ACI` zijn. |
| `containerResourceRequirements` | NA | Container voor de CPU en geheugenentiteiten. |
| &emsp;&emsp;`cpu` | `cpu_cores` | Het aantal CPU-kernen om toe te wijzen. Standaard `0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | De hoeveelheid geheugen in GB dat moet worden toegewezen voor deze webservice. Standaard `0.5` |
| `location` | `location` | De Azure-regio om deze webservice in te implementeren. Als deze niet wordt opgegeven, wordt de werkruimtelocatie gebruikt. Meer informatie over beschikbare regio's vindt u hier: [ACI-regio's](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
| `authEnabled` | `auth_enabled` | Hiermee wordt aangegeven of verificatie moet worden ingeschakeld voor deze webservice. Standaard ingesteld op False. |
| `sslEnabled` | `ssl_enabled` | Hiermee wordt aangegeven of SSL moet worden ingeschakeld voor deze webservice. Standaard ingesteld op False. |
| `appInsightsEnabled` | `enable_app_insights` | Hiermee wordt aangegeven of AppInsights moet worden ingeschakeld voor deze webservice. Standaard ingesteld op False. |
| `sslCertificate` | `ssl_cert_pem_file` | Het certificaatbestand dat nodig is als SSL is ingeschakeld |
| `sslKey` | `ssl_key_pem_file` | Het sleutelbestand dat nodig is als SSL is ingeschakeld |
| `cname` | `ssl_cname` | De CNAME die nodig is als SSL is ingeschakeld |
| `dnsNameLabel` | `dns_name_label` | Het DNS-naamlabel voor het score-eindpunt. Als er geen naamlabel wordt opgegeven, wordt er een uniek DNS-naamlabel voor het score-eindpunt gegenereerd. |

De volgende JSON is een voorbeeldimplementatie die gebruikt kan worden met de CLI:

```json
{
    "computeType": "aci",
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    },
    "authEnabled": true,
    "sslEnabled": false,
    "appInsightsEnabled": false
}
```