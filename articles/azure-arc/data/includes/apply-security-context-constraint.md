---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 01/15/2021
ms.author: mikeray
ms.openlocfilehash: 6c8dbeea83cba306cfb788cf447236088045ffc9
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99494006"
---
In deze sectie wordt uitgelegd hoe u een beveiligings context beperking (SCC) kunt Toep assen. Voor de preview-versie versoepelt deze de beveiligings beperkingen. 

1. Down load de aangepaste beveiligings context beperking (SCC). Gebruik een van de volgende opties: 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - [Onbewerkt](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml)
   - `curl`
   
      Met de volgende opdracht worden Arc-data-SCC. yaml gedownload:

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

   > [!NOTE]
   > RedHat open Shift 4,5 of hoger, wijzigingen in de manier waarop u het SCC toepast op het service account.
   > Gebruik hier dezelfde naam ruimte en in de `azdata arc dc create` onderstaande opdracht. Voor beeld is `arc` . 
   > 
   > Als u RedHat open Shift 4,5 of hoger gebruikt, voert u de volgende handelingen uit: 
   >
   >```console
   >oc create rolebinding arc-data-rbac --clusterrole=system:openshift:scc:arc-data-scc --serviceaccount=arc:default
   >```
