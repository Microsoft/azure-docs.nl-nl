---
title: Service lagen en-functies van Regi ster
description: Meer informatie over de functies en limieten (quota's) in de Basic-, Standard-en Premium-Service lagen (Sku's) van Azure Container Registry.
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: b55fc16dcd6dcb544ed4597ce4bdc6ba17b52646
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98015658"
---
# <a name="azure-container-registry-service-tiers"></a>Service lagen Azure Container Registry

Azure Container Registry is beschikbaar in meerdere service lagen (ook wel Sku's genoemd). Deze lagen bieden voorspel bare prijzen en verschillende opties om af te stemmen op de capaciteits-en gebruiks patronen van uw privé-docker-REGI ster in Azure.

| Laag | Beschrijving |
| --- | ----------- |
| **Basic** | Een rendabel toegangspunt voor ontwikkelaars die meer willen leren over Azure Container Registry. Basis registers hebben dezelfde programmatische mogelijkheden als standaard en Premium (zoals integratie van Azure Active Directory [verificatie](container-registry-authentication.md#individual-login-with-azure-ad), het [verwijderen van afbeeldingen][container-registry-delete]en [webhooks][container-registry-webhook]). De inbegrepen opslag-en afbeeldings doorvoer zijn echter het meest geschikt voor lagere gebruiks scenario's. |
| **Standard** | Standaard-registers bieden dezelfde mogelijkheden als basis, met meer inbegrepen opslag en de door Voer van de installatie kopie. Standard-registers moeten voldoen aan de behoeften van de meeste productiescenario's. |
| **Premium** | Premium-registers bieden de hoogste hoeveelheid inbegrepen opslag en gelijktijdige bewerkingen, waardoor scenario's met hoge volumes mogelijk zijn. Naast de hogere door Voer van een afbeelding voegt Premium functies toe, zoals [geo-replicatie][container-registry-geo-replication] voor het beheren van één REGI ster in meerdere regio's, [inhouds vertrouwen](container-registry-content-trust.md) voor het ondertekenen van installatie kopieën, [persoonlijke koppelingen met persoonlijke eind punten](container-registry-private-link.md) om de toegang tot het REGI ster te beperken. |

De lagen Basic, Standard en Premium bieden allemaal dezelfde programmatische mogelijkheden. Ze profiteren ook van [installatie kopie opslag][container-registry-storage] die volledig wordt beheerd door Azure. Het kiezen van een laag op een hoger niveau biedt meer prestaties en schaal baarheid. Met meerdere service lagen kunt u aan de slag met Basic en vervolgens converteren naar Standard en Premium als uw register gebruik toeneemt.

## <a name="service-tier-features-and-limits"></a>Functies en limieten voor de service tier

De volgende tabel bevat een overzicht van de functies en register limieten van de service lagen Basic, Standard en Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-tiers"></a>Lagen wijzigen

U kunt de servicelaag van een REGI ster wijzigen met de Azure CLI of in de Azure Portal. U kunt vrij verplaatsen tussen lagen, zolang de laag die u omschakelt, de vereiste maximale opslag capaciteit heeft. 

Er is geen register downtime of gevolgen voor register bewerkingen wanneer u overschakelt tussen service lagen.

### <a name="azure-cli"></a>Azure CLI

Als u wilt scha kelen tussen service lagen in de Azure CLI, gebruikt u de opdracht [AZ ACR update][az-acr-update] . Als u bijvoorbeeld wilt overschakelen op Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure Portal

Selecteer in het **overzicht** van het container register in de Azure Portal **Update** en selecteer vervolgens een nieuwe **SKU** in de vervolg keuzelijst SKU.

![SKU'S voor container Registry bijwerken in Azure Portal][update-registry-sku]

## <a name="pricing"></a>Prijzen

Zie [container Registry prijzen][container-registry-pricing]voor de prijs informatie over elk van de Azure container Registry-service lagen.

Zie [prijs informatie voor band breedte](https://azure.microsoft.com/pricing/details/bandwidth/)voor meer informatie over de prijzen voor gegevens overdracht. 

## <a name="next-steps"></a>Volgende stappen

**Azure Container Registry-schema**

Ga naar het [ACR-plan][acr-roadmap] op github voor informatie over aanstaande functies in de service.

**Azure Container Registry UserVoice**

Bied en stem op nieuwe functie suggesties in [ACR UserVoice][container-registry-uservoice].

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md