---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 01/15/2021
ms.author: mikeray
ms.openlocfilehash: 17a8c9580a8e69213c7f34e8ec889f7e46c6d17d
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696058"
---
In deze sectie wordt uitgelegd hoe u een beveiligings context beperking (SCC) kunt Toep assen. Voor de preview-versie versoepelt deze de beveiligings beperkingen. 

1. Down load de aangepaste beveiligings context beperking (SCC). Gebruik een van de volgende opties: 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - ([Onbewerkt](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml))
   - `curl` Met de volgende opdracht worden Arc-data-SCC. yaml gedownload:

      ```console
      curl https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml -o arc-data-scc.yaml
      ```

1. Maak SCC.

   ```console
   oc create -f arc-data-scc.yaml
   ```

1. Pas het SCC toe op het service account.

   > [!NOTE]
   > Gebruik hier dezelfde naam ruimte en in de `azdata arc dc create` onderstaande opdracht. Voor beeld is `arc` .

   ```console
   oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
   ```