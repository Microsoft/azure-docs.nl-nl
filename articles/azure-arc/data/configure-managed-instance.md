---
title: Door Azure Arc ingeschakeld SQL Managed instance configureren
description: Door Azure Arc ingeschakeld SQL Managed instance configureren
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 37ba4f10365fca4292171c3bd2d9a3e7d00045bb
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98985866"
---
# <a name="configure-azure-arc-enabled-sql-managed-instance"></a>Door Azure Arc ingeschakeld SQL Managed instance configureren

In dit artikel wordt uitgelegd hoe u Azure Arc enabled SQL Managed Instance kunt configureren.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configure-resources"></a>Resources configureren

### <a name="configure-using-azure-data-cli-azdata"></a>Configureren met [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]

U kunt de configuratie van Azure Arc enabled SQL Managed instances bewerken met de [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] . Voer de volgende opdracht uit om configuratie opties te bekijken. 

```
azdata arc sql mi edit --help
```

In het volgende voor beeld worden de CPU-kern-en geheugen aanvragen en-limieten ingesteld.

```
azdata arc sql mi edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n <NAME_OF_SQL_MI>
```

Als u de wijzigingen wilt weer geven die zijn aangebracht in het SQL Managed instance, kunt u de volgende opdrachten gebruiken om het yaml-bestand van de configuratie weer te geven:

```
azdata arc sql mi show -n <NAME_OF_SQL_MI>
```

## <a name="configure-server-options"></a>Server opties configureren

U kunt de configuratie-instellingen van de server configureren voor Azure Arc enabled SQL Managed instance na de aanmaak tijd. In dit artikel wordt beschreven hoe u instellingen configureert, zoals het in-of uitschakelen van de MSSQL-agent, het inschakelen van specifieke traceer vlaggen voor scenario's voor probleem oplossing.

Ga als volgt te werk om een van deze instellingen te wijzigen:

1. Maak een aangepast `mssql-custom.conf` bestand dat de betreffende instellingen bevat. In het volgende voor beeld wordt SQL Agent ingeschakeld en wordt tracerings vlag 1204 ingeschakeld.:

   ```
   [sqlagent]
   enabled=true
   
   [traceflag]
   traceflag0 = 1204
   ```

1. Kopieer het `mssql-custom.conf` bestand naar `/var/opt/mssql` in de `arc-sqlmi` container in het `master-0` pod. Vervang door `<namespaceName>` de naam ruimte waarin dit exemplaar is geïmplementeerd.

   ```bash
   kubectl cp mssql-custom.conf master-0:/var/opt/mssql/mssql-custom.conf -c mssql-server -n <namespaceName>
   ```

1. Start het SQL Managed instance opnieuw.  Vervang door `<namespaceName>` de naam ruimte waarin dit exemplaar is geïmplementeerd.

   ```bash
   kubectl exec -it master-0  -c mssql-server -n <namespaceName> -- /bin/bash
   supervisorctl restart mssql-server
   exit
   ```


**Bekende beperkingen**
- Voor de bovenstaande stappen zijn Kubernetes-cluster beheerders machtigingen vereist
- Dit is onderhevig aan wijzigingen in de preview-periode
