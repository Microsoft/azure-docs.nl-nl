---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 0eeb82245a53c93af75fc3ce3f37cb588295e5b7
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508092"
---
De vermeldingen in de documenttoewijzing `deploymentconfig.json` voor de parameters voor [LocalWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration). In de volgende tabel wordt de toewijzing tussen de entiteiten in het JSON-document en de parameters beschreven voor de methode:

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