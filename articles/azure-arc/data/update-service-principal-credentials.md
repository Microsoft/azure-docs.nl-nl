---
title: Referenties voor service-principal bijwerken
description: Referentie voor een Service-Principal bijwerken
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: mikeray
ms.reviewer: mikeray
ms.date: 12/09/2020
ms.topic: how-to
ms.openlocfilehash: 1c7ccec6228a6dcfb457bda8f6ecd384775d4b27
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "104669539"
---
# <a name="update-service-principal-credentials"></a>Referenties voor service-principal bijwerken

Wanneer de referenties van de Service-Principal veranderen, moet u de geheimen in de gegevens controller bijwerken.

Als u bijvoorbeeld de gegevens controller hebt geïmplementeerd met een specifieke set waarden voor de Tenant-ID van de Service-Principal, de client-ID en het client geheim en vervolgens een of meer van deze waarden wijzigt, moet u de geheimen in de gegevens controller bijwerken.  Hieronder vindt u de instructies voor het bijwerken van de Tenant-ID, client-ID of het client geheim. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="background"></a>Achtergrond

De Service-Principal is gemaakt bij het maken van de [Service-Principal](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal). 

## <a name="steps"></a>Stappen

1. Open het Service-Principal Secret in de standaard editor.

   ```console
   kubectl edit secret/upload-service-principal-secret -n <name of namespace>
   ```

   Als u bijvoorbeeld het geheim van de Service-Principal wilt bewerken in een gegevens controller in de `arc` naam ruimte, voert u de volgende opdracht uit:

   ```console
   kubectl edit secret/upload-service-principal-secret -n arc
   ```

   `kubecl edit`Met de opdracht opent u het bestand referenties. yml in de standaard editor. 


1. Bewerk het geheim van de Service-Principal. 

   Vervang in de standaard editor de waarden in de sectie gegevens met de bijgewerkte referentie gegevens.

   Bijvoorbeeld:

   ```console
   # Please edit the object below. Lines beginning with a '#' will be ignored,
   # and an empty file will abort the edit. If an error occurs while saving this file will be
   # reopened with the relevant failures.
   #
   apiVersion: v1
   data:
     authority: aHR0cHM6Ly9sb2dpbi5taWNyb3NvZnRvbmxpbmUuY29t
     clientId: NDNiNDcwYrFTGWYzOC00ODhkLTk0ZDYtNTc0MTdkN2YxM2Uw
     clientSecret: VFA2RH125XU2MF9+VVhXenZTZVdLdECXFlNKZi00Lm9NSw==
     tenantId: NzJmOTg4YmYtODZmMRFVBGTJLSATkxYWItMmQ3Y2QwMTFkYjQ3
   kind: Secret
   metadata:
     creationTimestamp: "2020-12-02T05:02:04Z"
     name: upload-service-principal-secret
     namespace: arc
     resourceVersion: "7235659"
     selfLink: /api/v1/namespaces/arc/secrets/upload-service-principal-secret
     uid: 7fb693ff-6caa-4a31-b83e-9bf22be4c112
   type: Opaque
   ```

   Bewerk de waarden voor `clientID` `clientSecret` en/of `tenantID` indien van toepassing. 

> [!NOTE]
>De waarden moeten base64-gecodeerd zijn. Bewerk geen andere eigenschappen.

Als er een onjuiste waarde wordt gegeven voor `clientId` , `clientSecret` of `tenantID` Als u een fout bericht ziet, wordt het volgende weer gegeven in de `control-xxxx` container logboeken pod/controller:

```output
YYYY-MM-DD HH:MM:SS.mmmm | ERROR | [AzureUpload] Upload task exception: A configuration issue is preventing authentication - check the error message from the server for details.You can modify the configuration in the application registration portal. See https://aka.ms/msal-net-invalid-client for details.  Original exception: AADSTS7000215: Invalid client secret is provided.
```



## <a name="next-steps"></a>Volgende stappen

[De gebruikersnaam en het wachtwoord ophalen om verbinding te maken met de Arc-gegevenscontroller](retrieve-the-username-password-for-data-controller.md)

[Een service-principal maken](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)
