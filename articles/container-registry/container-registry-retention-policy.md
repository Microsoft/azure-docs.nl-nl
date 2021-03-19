---
title: Beleid voor het bewaren van niet-gecodeerde manifesten
description: Meer informatie over het inschakelen van een Bewaar beleid in uw Azure container Registry voor het automatisch verwijderen van niet-gecodeerde manifesten na een opgegeven periode.
ms.topic: article
ms.date: 10/02/2019
ms.openlocfilehash: 5dda85934bb10cf16fd90381539b892df4f5445c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "83683453"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>Een Bewaar beleid voor niet-gecodeerde manifesten instellen

Azure Container Registry biedt u de mogelijkheid om een *Bewaar beleid* in te stellen voor opgeslagen afbeeldings manifesten die geen gekoppelde labels (niet-*gecodeerde manifesten*) hebben. Wanneer een Bewaar beleid is ingeschakeld, worden niet-gecodeerde manifesten in het REGI ster automatisch verwijderd na een aantal dagen dat u instelt. Deze functie voor komt dat het REGI ster wordt gevuld met artefacten die niet nodig zijn en helpt u bij het besparen van opslag kosten. Als het `delete-enabled` kenmerk van een niet-gelabeld manifest is ingesteld op `false` , kan het manifest niet worden verwijderd en is het Bewaar beleid niet van toepassing.

U kunt de Azure Cloud Shell of een lokale installatie van de Azure CLI gebruiken om de opdracht voorbeelden in dit artikel uit te voeren. Als u het lokaal wilt gebruiken, is versie 2.0.74 of hoger vereist. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

Een Bewaar beleid is een functie van **Premium** -container registers. Zie [Azure container Registry service lagen](container-registry-skus.md)voor meer informatie over de service lagen van het REGI ster.

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-versie en er [zijn enkele beperkingen van toepassing](#preview-limitations). Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

> [!WARNING]
> Een Bewaar beleid met caret instellen: verwijderde afbeeldings gegevens zijn onherstelbaar. Als u systemen hebt die installatie kopieën pullen per manifest Digest (in plaats van de naam van de installatie kopie), moet u geen Bewaar beleid voor niet-gecodeerde manifesten instellen. Als u niet-gelabelde afbeeldingen verwijdert, kunnen die systemen de installatie kopieën niet uit het REGI ster halen. In plaats van op manifest te halen, kunt u overwegen om een uniek schema voor *labels* te gebruiken, een [Aanbevolen best practice](container-registry-image-tag-version.md).

## <a name="preview-limitations"></a>Preview-beperkingen

* U kunt alleen een Bewaar beleid voor niet-gecodeerde manifesten instellen.
* Het Bewaar beleid is momenteel alleen van toepassing op manifesten die niet zijn gelabeld *nadat* het beleid is ingeschakeld. Bestaande niet-gecodeerde manifesten in het REGI ster vallen niet onder het beleid. Als u bestaande niet-gecodeerde manifesten wilt verwijderen, raadpleegt u voor beelden in [container installatie kopieën verwijderen in azure container Registry](container-registry-delete.md).

## <a name="about-the-retention-policy"></a>Over het Bewaar beleid

Azure Container Registry verwijst naar het tellen van manifesten in het REGI ster. Wanneer een manifest wordt ontlabeld, wordt het Bewaar beleid gecontroleerd. Als een Bewaar beleid is ingeschakeld, wordt een manifest verwijderings bewerking in de wachtrij geplaatst, met een specifieke datum, op basis van het aantal dagen dat is ingesteld in het beleid.

Een afzonderlijke taak wachtrij beheer verwerkt voortdurend berichten en schaalt naar behoefte. Stel dat u twee manifesten, 1 uur gescheiden, in een REGI ster hebt gelabeld met een Bewaar beleid van 30 dagen. Er worden twee berichten in de wachtrij geplaatst. Vervolgens worden de berichten 30 dagen later, ongeveer 1 uur van elkaar, opgehaald uit de wachtrij en verwerkt, ervan uitgaande dat het beleid nog steeds van kracht is.

## <a name="set-a-retention-policy---cli"></a>Een Bewaar beleid instellen-CLI

In het volgende voor beeld ziet u hoe u de Azure CLI gebruikt om een Bewaar beleid voor niet-gecodeerde manifesten in een REGI ster in te stellen.

### <a name="enable-a-retention-policy"></a>Een Bewaar beleid inschakelen

Standaard is geen Bewaar beleid ingesteld in een container register. Als u een Bewaar beleid wilt instellen of bijwerken, voert u de opdracht [AZ ACR config retention update][az-acr-config-retention-update] uit in de Azure cli. U kunt een aantal dagen tussen 0 en 365 opgeven om de niet-gecodeerde manifesten te bewaren. Als u geen aantal dagen opgeeft, stelt de opdracht een standaard waarde van 7 dagen in. Na de Bewaar periode worden alle niet-gelabelde manifesten in het REGI ster automatisch verwijderd.

In het volgende voor beeld wordt een Bewaar beleid van 30 dagen voor niet-gecodeerde manifesten in het REGI ster *myregistry*:

```azurecli
az acr config retention update --registry myregistry --status enabled --days 30 --type UntaggedManifests
```

In het volgende voor beeld wordt een beleid ingesteld voor het verwijderen van een manifest in het REGI ster zodra het een label heeft. Maak dit beleid door een Bewaar periode van 0 dagen in te stellen. 

```azurecli
az acr config retention update --registry myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="validate-a-retention-policy"></a>Een Bewaar beleid valideren

Als u het voor gaande beleid inschakelt met een Bewaar periode van 0 dagen, kunt u snel controleren of niet-gecodeerde manifesten worden verwijderd:

1. Push een test installatie kopie `hello-world:latest` naar het REGI ster of vervang een andere test installatie kopie naar keuze.
1. Tag de `hello-world:latest` installatie kopie, bijvoorbeeld met behulp van de opdracht [AZ ACR repository tag][az-acr-repository-untag] . Het niet-gelabelde manifest blijft in het REGI ster.
    ```azurecli
    az acr repository untag --name myregistry --image hello-world:latest
    ```
1. Binnen een paar seconden wordt het niet-gelabelde manifest verwijderd. U kunt het verwijderen controleren door manifesten in de opslag plaats weer te geven, bijvoorbeeld met behulp van de opdracht [AZ ACR repository show-manifests][az-acr-repository-show-manifests] . Als de test installatie kopie de enige is in de opslag plaats, wordt de opslag plaats zelf verwijderd.

### <a name="disable-a-retention-policy"></a>Een Bewaar beleid uitschakelen

Als u wilt zien hoe het Bewaar beleid is ingesteld in een REGI ster, voert u de opdracht [AZ ACR config retentie show][az-acr-config-retention-show] uit:

```azurecli
az acr config retention show --registry myregistry
```

Als u een Bewaar beleid wilt uitschakelen in een REGI ster, voert u de opdracht [AZ ACR config retention update][az-acr-config-retention-update] uit en stelt u het `--status disabled` volgende in:

```azurecli
az acr config retention update --registry myregistry --status disabled --type UntaggedManifests
```

## <a name="set-a-retention-policy---portal"></a>Een Bewaar beleid instellen-Portal

U kunt ook het Bewaar beleid van een REGI ster instellen in de [Azure Portal](https://portal.azure.com). In het volgende voor beeld ziet u hoe u de portal kunt gebruiken om een Bewaar beleid voor niet-gecodeerde manifesten in een REGI ster in te stellen.

### <a name="enable-a-retention-policy"></a>Een Bewaar beleid inschakelen

1. Navigeer naar uw Azure container Registry. Onder **beleids regels** selecteert u **bewaren** (preview-versie).
1. Selecteer in **status** **ingeschakeld**.
1. Selecteer een aantal dagen tussen 0 en 365 om de niet-gecodeerde manifesten te bewaren. Selecteer **Opslaan**.

![Een Bewaar beleid inschakelen in Azure Portal](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>Een Bewaar beleid uitschakelen

1. Navigeer naar uw Azure container Registry. Onder **beleids regels** selecteert u **bewaren** (preview-versie).
1. Selecteer in **status** de optie **uitgeschakeld**. Selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over opties voor het [verwijderen van installatie kopieën en opslag](container-registry-delete.md) plaatsen in azure container Registry

* Meer informatie over het [automatisch verwijderen](container-registry-auto-purge.md) van geselecteerde installatie kopieën en manifesten uit een REGI ster

* Meer informatie over opties voor het [vergren delen van afbeeldingen en manifesten](container-registry-image-lock.md) in een REGI ster

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az-acr-config-retention-update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az-acr-config-retention-show
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
