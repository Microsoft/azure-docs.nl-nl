---
title: Een Azure Content Delivery Network (CDN)-profiel en-eind punt maken met behulp van de Azure CLI
description: Azure CLI-voorbeeld scripts voor het maken van een Azure CDN profiel, een eind punt, een oorspronkelijke groep, een oorsprong en een aangepast domein.
author: asudbring
ms.author: allensu
manager: danielgi
ms.date: 03/09/2021
ms.topic: sample
ms.service: azure-cdn
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: ce01c1f851a5dbaedc85d848b12bf0b0831a16ef
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726833"
---
# <a name="create-an-azure-cdn-profile-and-endpoint-using-the-azure-cli"></a>Een Azure CDN profiel en eind punt maken met behulp van de Azure CLI

Als alternatief voor de Azure Portal kunt u deze voor beelden van Azure CLI-scripts gebruiken voor het beheren van de volgende CDN-bewerkingen:

- Een CDN-profiel maken.
- Een CDN-eindpunt maken.
- Maak een CDN-oorsprongs groep en stel deze in als de standaard groep.
- Maak een CDN-oorsprong.
- Een aangepast domein maken en HTTPS inschakelen.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-scripts"></a>Voorbeeldscripts

Als u nog geen resource groep voor uw CDN-profiel hebt, maakt u deze met de `az group create` volgende opdracht:

```azurecli
# Create a resource group to use for the CDN.
az group create --name MyResourceGroup --location eastus

```

Met het volgende Azure CLI-script maakt u een CDN-profiel en een CDN-eind punt:

```azurecli
# Create a CDN profile.
az cdn profile create --resource-group MyResourceGroup --name MyCDNProfile --sku Standard_Microsoft

# Create a CDN endpoint.
az cdn endpoint create --resource-group MyResourceGroup --name MyCDNEndpoint --profile-name MyCDNProfile --origin www.contoso.com

```

Met het volgende Azure CLI-script maakt u een CDN-oorsprongs groep, stelt u de standaard oorspronkelijke groep voor een eind punt in en maakt u een nieuwe oorsprong:

```azurecli
# Create an origin group.
az cdn origin-group create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyOriginGroup --origins origin-0

# Make the origin group the default group of an endpoint.
az cdn endpoint update --resource-group MyResourceGroup --name MyCDNEndpoint --profile-name MyCDNProfile --default-origin-group MyOriginGroup
                           
# Create another origin for an endpoint.
az cdn origin create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name origin-1 --host-name example.contoso.com

```

Met het volgende Azure CLI-script maakt u een aangepast CDN-domein en schakelt u HTTPS in. Voordat u een aangepast domein met een Azure CDN-eind punt kunt koppelen, moet u eerst een canonieke-naam record (CNAME) maken met Azure DNS of uw DNS-provider om te verwijzen naar uw CDN-eind punt. Zie [een CNAME DNS-record maken](../../../cdn/cdn-map-content-to-custom-domain.md#create-a-cname-dns-record)voor meer informatie.

```azurecli
# Associate a custom domain with an endpoint.
az cdn custom-domain create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyCustomDomain --hostname www.example.com

# Enable HTTPS on the custom domain.
az cdn custom-domain enable-https --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyCustomDomain

```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met het uitvoeren van de voorbeeld scripts, gebruikt u de volgende opdracht om de resource groep en alle bijbehorende resources te verwijderen.

```azurecli
# Delete the resource group.
az group delete --name MyResourceGroup

```

## <a name="azure-cli-commands-used-in-this-article"></a>Azure CLI-opdrachten die in dit artikel worden gebruikt

- [AZ CDN endpoint Create](/cli/azure/cdn/endpoint#az_cdn_endpoint_create)
- [AZ CDN endpoint update](/cli/azure/cdn/endpoint#az_cdn_endpoint_update)
- [AZ CDN Origin Create](/cli/azure/cdn/origin#az_cdn_origin_create)
- [AZ CDN Origin-groep maken](/cli/azure/cdn/origin-group#az_cdn_origin_group_create)
- [AZ CDN profile Create](/cli/azure/cdn/profile#az_cdn_profile_create)
- [az group create](/cli/azure/group#az_group_create)
- [az group delete](/cli/azure/group#az_group_delete)
- [AZ CDN Custom-Domain Create](/cli/azure/cdn/custom-domain#az_cdn_custom_domain_create)
- [AZ CDN Custom-Domain Enable-https](/cli/azure/cdn/custom-domain#az_cdn_custom_domain_enable_https)
