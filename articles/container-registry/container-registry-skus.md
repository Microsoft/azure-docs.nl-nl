---
title: Registerservicelagen en -functies
description: Meer informatie over de functies en limieten (quota) in de servicelagen Basic, Standard en Premium van Azure Container Registry.
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: 323d36fe022d8b8e9618b8beb1facae93d22df4e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781249"
---
# <a name="azure-container-registry-service-tiers"></a>Azure Container Registry servicelagen

Azure Container Registry is beschikbaar in meerdere servicelagen (ook wel bekend als SKU's). Deze lagen bieden voorspelbare prijzen en verschillende opties voor afstemming op de capaciteit en gebruikspatronen van uw persoonlijke Docker-register in Azure.

| Laag | Beschrijving |
| --- | ----------- |
| **Basic** | Een rendabel toegangspunt voor ontwikkelaars die meer willen leren over Azure Container Registry. Basic-registers hebben dezelfde programmatische mogelijkheden als Standard en Premium (zoals [Azure Active Directory-](container-registry-authentication.md#individual-login-with-azure-ad) [en][container-registry-delete]verwijdering van afbeeldingen en [webhooks).][container-registry-webhook] De inbegrepen opslag en doorvoer van de afbeelding zijn echter het meest geschikt voor scenario's met een lager gebruik. |
| **Standard** | Standard-registers bieden dezelfde mogelijkheden als Basic, met meer inbegrepen opslag en doorvoer van afbeeldingen. Standard-registers moeten voldoen aan de behoeften van de meeste productiescenario's. |
| **Premium** | Premium-registers bieden de hoogste hoeveelheid inbegrepen opslag en gelijktijdige bewerkingen, waardoor scenario's met een hoog volume mogelijk zijn. Naast een hogere doorvoer van de afbeelding voegt Premium functies toe, zoals [](container-registry-content-trust.md) geo-replicatie voor het beheren van één register in meerdere regio's, inhoud vertrouwen voor het ondertekenen van [afbeeldingentags,](container-registry-private-link.md) private link met [privé-eindpunten][container-registry-geo-replication] om de toegang tot het register te beperken. |

De lagen Basic, Standard en Premium bieden allemaal dezelfde programmatische mogelijkheden. Ze profiteren ook allemaal van de [opslag van afbeeldingen die][container-registry-storage] volledig door Azure worden beheerd. Het kiezen van een laag op een hoger niveau biedt meer prestaties en schaal. Met meerdere servicelagen kunt u aan de slag met Basic en vervolgens converteren naar Standard en Premium naarmate het registergebruik toeneemt.

## <a name="service-tier-features-and-limits"></a>Functies en limieten voor servicelagen

De volgende tabel bevat de functies en registerlimieten van de servicelagen Basic, Standard en Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-tiers"></a>Lagen wijzigen

U kunt de servicelaag van een register wijzigen met de Azure CLI of in de Azure Portal. U kunt zich vrij verplaatsen tussen lagen zolang de laag waar u naar overschakelt de vereiste maximale opslagcapaciteit heeft. 

Er is geen downtime in het register of heeft geen invloed op registerbewerkingen wanneer u tussen servicelagen overstapt.

### <a name="azure-cli"></a>Azure CLI

Als u wilt verplaatsen tussen servicelagen in de Azure CLI, gebruikt u [de opdracht az acr update.][az-acr-update] Als u bijvoorbeeld wilt overschakelen naar Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure Portal

Selecteer in het **containerregister Overzicht** in Azure Portal update **en** selecteer vervolgens een nieuwe **SKU** in de vervolgkeuzelijst SKU.

![Container Registry-SKU bijwerken in Azure Portal][update-registry-sku]

## <a name="pricing"></a>Prijzen

Zie prijzen voor Azure Container Registry Container Registry prijsinformatie over Azure Container Registry [servicelagen.][container-registry-pricing]

Zie Prijsinformatie voor bandbreedte voor meer informatie over prijzen [voor gegevensoverdracht.](https://azure.microsoft.com/pricing/details/bandwidth/) 

## <a name="next-steps"></a>Volgende stappen

**Azure Container Registry Roadmap**

Ga naar [de ACR-roadmap][acr-roadmap] op GitHub voor informatie over toekomstige functies in de service.

**Azure Container Registry UserVoice**

Verzend en stem op nieuwe functiesuggesties in [ACR UserVoice.][container-registry-uservoice]

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az_acr_update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md