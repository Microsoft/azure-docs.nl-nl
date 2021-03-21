---
title: Een preview-verbinding voor peering service registreren met behulp van de Azure CLI
description: Meer informatie over het registreren van een peering service-verbinding met behulp van de Azure CLI
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/2/2020
ms.author: derekol
ms.openlocfilehash: e7b696ba052b2aca9e14628327c07275845607ad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94540583"
---
# <a name="register-a-peering-service-connection-by-using-the-azure-cli"></a>Een peering service-verbinding registreren met behulp van de Azure CLI

Azure Peering Service is een netwerkservice die de klantconnectiviteit verbetert met Microsoft-cloudservices, zoals Microsoft 365, Dynamics 365, SaaS-services (Software as a Service), Azure of een andere Microsoft-service die toegankelijk is via openbaar internet. In dit artikel leert u hoe u een peering service-verbinding kunt registreren met behulp van de Azure CLI.

- Voor dit artikel is versie 2.0.28 of hoger van Azure CLI vereist. Voer [az version](/cli/azure/reference-index#az_version) uit om de geïnstalleerde versie en afhankelijke bibliotheken te vinden. Voer [az upgrade](/cli/azure/reference-index#az_upgrade) uit om te upgraden naar de nieuwste versie.

## <a name="prerequisites"></a>Vereisten 

U moet het volgende hebben:

### <a name="azure-account"></a>Azure-account

U moet beschikken over een geldig en actief Microsoft Azure-account. Dit account is vereist voor het instellen van de Peering Service-verbinding. Peering Service is een resource binnen Azure-abonnementen.

### <a name="connectivity-provider"></a>Connectiveitsprovider

U kunt samenwerken met een internetprovider of een Internet Exchange-partner om Peering Service te verkrijgen om verbinding te maken tussen uw netwerk en het Microsoft-netwerk.

Zorg ervoor dat de connectiviteitsproviders een partnerschap hebben met Microsoft.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

- Voor dit artikel is versie 2.0.28 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

### <a name="1-select-your-subscription"></a>1. Selecteer uw abonnement

Selecteer het abonnement waarvoor u de peering service-verbinding wilt registreren.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

Als u nog geen resource groep hebt, moet u er een maken voordat u de verbinding van de peering-service registreert. U kunt een resourcegroep maken door de volgende opdracht uit te voeren:

```azurecli-interactive
az group create -n MyResourceGroup -l "West US"
```

### <a name="2-register-your-subscription-with-the-resource-provider-and-feature-flag"></a>2. Registreer uw abonnement met de resource provider en functie vlag

Voordat u verdergaat met de stappen voor het registreren van de peering service-verbinding met behulp van de Azure CLI, registreert u uw abonnement met de resource provider en functie vlag door gebruik te maken van de Azure CLI. De Azure CLI-opdrachten worden hier opgegeven:

```azurecli-interactive

az feature register --namespace Microsoft.Peering --name AllowPeeringService

```

### <a name="3-register-the-peering-service-connection"></a>3. de peering service-verbinding registreren

Registreer de peering service-verbinding met behulp van de volgende reeks opdrachten via de Azure CLI. In dit voor beeld wordt de peering service-verbinding met de naam myPeeringService geregistreerd.

```azurecli-interactive
az peering service create : Create peering service\
  --location -l \
  --name myPeeringService\
  --resource-group -g MyResourceGroup\
  --peering-service-location\
  --peering-service-provider\
  --tags
```

### <a name="4-register-the-prefix"></a>4. het voor voegsel registreren

Registreer het voor voegsel dat wordt verschaft door de connectiviteits provider door de volgende opdrachten uit te voeren via de Azure CLI. In dit voorbeeld wordt het voorvoegsel met de naam myPrefix geregistreerd.

```azurecli-interactive
az peering service prefix create \
  --name  myPrefix\
  --peering-service-name myPeeringService\
  --resource-group  -g myResourceGroup\
```

## <a name="next-steps"></a>Volgende stappen

- Zie [verbinding met peering service](connection.md)voor meer informatie over peering service-verbinding.
- Zie [Peering Service-verbindingstelemetrie](connection-telemetry.md) voor meer informatie over de Peering Service-verbindingstelemetrie.
- Zie [Verbindingstelemetrie meten](measure-connection-telemetry.md) om telemetrie te meten.
- Zie [Een Peering Service-verbinding registreren - Azure PowerShell](powershell.md) als u de verbinding wilt registreren met behulp van Azure PowerShell.
- Als u de verbinding wilt registreren met behulp van de Azure Portal, raadpleegt u [een peering service-verbinding registreren-Azure Portal](azure-portal.md).
