---
title: 'Quickstart: Azure API for FHIR implementeren met Azure CLI'
description: In deze quickstart wordt uitgelegd hoe u Azure API for FHIR implementeert in Azure met behulp van Azure CLI.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/15/2019
ms.author: cavoeg
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3340cd0a39b0f5311487fec5b05d37e3376d433a
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659247"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-cli"></a>Quickstart: Azure API for FHIR implementeren met Azure CLI

In deze quickstart wordt uitgelegd hoe u Azure API for FHIR implementeert in Azure met behulp van Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="add-healthcareapis-extension"></a>HealthcareAPIs-extensie toevoegen

```azurecli-interactive
az extension add --name healthcareapis
```

Een lijst met opdrachten voor HealthcareAPIs ophalen:

```azurecli-interactive
az healthcareapis --help
```

## <a name="create-azure-resource-group"></a>Azure-resourcegroep maken

Kies een naam voor de resourcegroep die de Azure API for FHIR bevat en maak deze:

```azurecli-interactive
az group create --name "myResourceGroup" --location westus2
```

## <a name="deploy-the-azure-api-for-fhir"></a>De Azure API for FHIR implementeren

```azurecli-interactive
az healthcareapis create --resource-group myResourceGroup --name nameoffhiraccount --kind fhir-r4 --location westus2 
```

## <a name="fetch-fhir-api-capability-statement"></a>FHIR API-mogelijheidsinstructie ophalen

Krijg een mogelijkheidsinstructie van de FHIR API met:

```azurecli-interactive
curl --url "https://nameoffhiraccount.azurehealthcareapis.com/metadata"
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet gaat gebruiken, verwijder dan de resourcegroep met de volgende stappen:

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u de Azure API for FHIR geïmplementeerd in uw abonnement. Als u aanvullende instellingen wilt instellen in uw Azure API for FHIR, gaat u verder met de handleiding bij aanvullende instellingen. Als u klaar bent om te beginnen met het gebruik van Azure API for FHIR, leest u de informatie over het registreren van toepassingen.

>[!div class="nextstepaction"]
>[Aanvullende instellingen in de Azure-API voor FHIR](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[Overzicht toepassingen registreren](fhir-app-registration.md)
