---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 9240f2616134055d6c97b9b85c264aa7635139cc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028437"
---
De vermeldingen in de documenttoewijzing `deploymentconfig.json` voor de parameters voor [LocalWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). In de volgende tabel wordt de toewijzing tussen de entiteiten in het JSON-document en de parameters beschreven voor de methode:

| JSON-entiteit | Methodeparameter | Beschrijving |
| ----- | ----- | ----- |
| `computeType` | NA | Het rekendoel. Voor lokale doelen moet de waarde `local` zijn. |
| `port` | `port` | De lokale poort waarop het HTTP-eindpunt van de service beschikbaar wordt gemaakt. |

De volgende JSON is een voorbeeld van een implementatieconfiguratie die gebruikt kan worden met de CLI:

```json
{
    "computeType": "local",
    "port": 32267
}
```