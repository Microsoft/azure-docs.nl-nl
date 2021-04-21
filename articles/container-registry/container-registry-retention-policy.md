---
title: Beleid voor het behouden van niet-getagged manifesten
description: Meer informatie over het inschakelen van een bewaarbeleid in uw Azure-containerregister, voor het automatisch verwijderen van manifesten zonder naam na een bepaalde periode.
ms.topic: article
ms.date: 10/02/2019
ms.openlocfilehash: 81ce92a2533cc8a688be43da9406cd5b0e726509
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781339"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>Een retentiebeleid instellen voor manifesten zonder aaneenlag

Azure Container Registry biedt u de mogelijkheid  om een retentiebeleid in te stellen voor opgeslagen afbeeldingsmanifests die geen gekoppelde tags hebben (*manifesten* zonder tag ). Wanneer een retentiebeleid is ingeschakeld, worden manifesten zonder aanmaak in het register automatisch verwijderd na een aantal dagen dat u hebt ingesteld. Met deze functie voorkomt u dat het register wordt gevuld met artefacten die niet nodig zijn en kunt u besparen op opslagkosten. Als het kenmerk van een manifest zondertagged is ingesteld op , kan het manifest niet worden verwijderd en is het bewaarbeleid `delete-enabled` `false` niet van toepassing.

U kunt de Azure Cloud Shell of een lokale installatie van de Azure CLI gebruiken om de opdrachtvoorbeelden in dit artikel uit te voeren. Als u deze lokaal wilt gebruiken, is versie 2.0.74 of hoger vereist. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

Een bewaarbeleid is  een functie van Premium-containerregisters. Zie Azure Container Registry servicelagen voor meer informatie over [registerservicelagen.](container-registry-skus.md)

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-versie en er [gelden enkele beperkingen.](#preview-limitations) Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

> [!WARNING]
> Stel een bewaarbeleid in met zorg: verwijderde afbeeldingsgegevens kunnen niet worden teruggehaald. Als u systemen hebt die afbeeldingen op halen via manifest digest (in plaats van de naam van de afbeelding), moet u geen bewaarbeleid instellen voor manifesten zonder aanmaak. Door niet-getagged afbeeldingen te verwijderen, kunnen deze systemen de afbeeldingen niet uit het register halen. In plaats van een manifest op te halen, kunt u overwegen een uniek *tagschema* te gebruiken, een [aanbevolen best practice](container-registry-image-tag-version.md).

## <a name="preview-limitations"></a>Preview-beperkingen

* U kunt alleen een bewaarbeleid instellen voor manifesten zonder aaneenlag.
* Het bewaarbeleid is momenteel alleen van toepassing op manifesten die niet zijn getagged *nadat* het beleid is ingeschakeld. Bestaande manifesten zonder aanmaak in het register zijn niet onderhevig aan het beleid. Zie Voorbeelden in Containerafbeeldingen verwijderen in Azure Container Registry om [bestaande manifesten zonder Azure Container Registry.](container-registry-delete.md)

## <a name="about-the-retention-policy"></a>Over het retentiebeleid

Azure Container Registry telt wel voor manifesten in het register. Wanneer een manifest niet is getagged, wordt het bewaarbeleid gecontroleerd. Als een bewaarbeleid is ingeschakeld, wordt een manifestbewerking in de wachtrij geplaatst, met een specifieke datum, afhankelijk van het aantal dagen dat in het beleid is ingesteld.

Een afzonderlijke taak voor wachtrijbeheer verwerkt voortdurend berichten en schaalt naar behoefte. Stel bijvoorbeeld dat u twee manifesten, 1 uur uit elkaar, hebt verwijderd in een register met een bewaarbeleid van 30 dagen. Er worden twee berichten in de wachtrij geplaatst. 30 dagen later, ongeveer 1 uur later, worden de berichten opgehaald uit de wachtrij en verwerkt, ervan uitgaande dat het beleid nog steeds van kracht is.

## <a name="set-a-retention-policy---cli"></a>Een retentiebeleid instellen - CLI

In het volgende voorbeeld ziet u hoe u de Azure CLI gebruikt om een bewaarbeleid in te stellen voor manifesten zonder aaneenvolgend beleid in een register.

### <a name="enable-a-retention-policy"></a>Een retentiebeleid inschakelen

Standaard is er geen bewaarbeleid ingesteld in een containerregister. Als u een bewaarbeleid wilt instellen of bijwerken, moet u [de opdracht az acr config retention update][az-acr-config-retention-update] uitvoeren in de Azure CLI. U kunt een aantal dagen tussen 0 en 365 opgeven om de manifesten zonder naam te behouden. Als u een aantal dagen niet opgeeft, stelt de opdracht een standaardwaarde van 7 dagen in. Na de bewaarperiode worden alle manifesten zonder aaneen ving in het register automatisch verwijderd.

In het volgende voorbeeld wordt een bewaarbeleid van 30 dagen voor manifesten zondertagged in het register *myregistry instellen:*

```azurecli
az acr config retention update --registry myregistry --status enabled --days 30 --type UntaggedManifests
```

In het volgende voorbeeld wordt een beleid voor het verwijderen van manifesten in het register instellen zodra het zonder is getagged. Maak dit beleid door een bewaarperiode van 0 dagen in te stellen. 

```azurecli
az acr config retention update --registry myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="validate-a-retention-policy"></a>Een bewaarbeleid valideren

Als u het voorgaande beleid inschakelen met een bewaarperiode van 0 dagen, kunt u snel controleren of manifesten zonder aaneengetaste items zijn verwijderd:

1. Push een testafbeelding `hello-world:latest` naar het register of vervang een andere testafbeelding van uw keuze.
1. Onttag de `hello-world:latest` afbeelding, bijvoorbeeld met de [opdracht az acr repository untag.][az-acr-repository-untag] Het manifest zondertagged blijft in het register.
    ```azurecli
    az acr repository untag --name myregistry --image hello-world:latest
    ```
1. Binnen een paar seconden wordt het manifest zonder de aaneenlijst verwijderd. U kunt de verwijdering controleren door manifesten in de opslagplaats weer te geven, bijvoorbeeld met behulp van de [opdracht az acr repository show-manifests.][az-acr-repository-show-manifests] Als de testafbeelding de enige in de opslagplaats is, wordt de opslagplaats zelf verwijderd.

### <a name="disable-a-retention-policy"></a>Een retentiebeleid uitschakelen

Voer de opdracht [az acr config retention show][az-acr-config-retention-show] uit om het retentiebeleid weer te geven dat is ingesteld in een register:

```azurecli
az acr config retention show --registry myregistry
```

Als u een bewaarbeleid in een register wilt uitschakelen, moet u de [opdracht az acr config retention update uitvoeren][az-acr-config-retention-update] en `--status disabled` instellen:

```azurecli
az acr config retention update --registry myregistry --status disabled --type UntaggedManifests
```

## <a name="set-a-retention-policy---portal"></a>Een bewaarbeleid instellen - portal

U kunt ook het bewaarbeleid van een register instellen in de [Azure Portal.](https://portal.azure.com) In het volgende voorbeeld ziet u hoe u de portal kunt gebruiken om een bewaarbeleid in te stellen voor manifesten zonder aaneenvolgend beleid in een register.

### <a name="enable-a-retention-policy"></a>Een retentiebeleid inschakelen

1. Navigeer naar uw Azure-containerregister. Selecteer **onder Beleid** de optie **Retentie** (preview).
1. Selecteer **in Status** de optie **Ingeschakeld.**
1. Selecteer een aantal dagen tussen 0 en 365 om de manifesten zondertagged te behouden. Selecteer **Opslaan**.

![Een retentiebeleid inschakelen in Azure Portal](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>Een retentiebeleid uitschakelen

1. Navigeer naar uw Azure-containerregister. Selecteer **onder Beleid** de optie **Retentie** (preview).
1. Selecteer **in Status** de optie **Uitgeschakeld.** Selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over opties voor [het verwijderen van afbeeldingen en opslagplaatsen](container-registry-delete.md) in Azure Container Registry

* Meer informatie over hoe [u geselecteerde afbeeldingen en](container-registry-auto-purge.md) manifesten automatisch opsteert uit een register

* Meer informatie over opties voor het [vergrendelen van afbeeldingen en manifesten](container-registry-image-lock.md) in een register

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az_acr_config_retention_update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az_acr_config_retention_show
[az-acr-repository-untag]: /cli/azure/acr/repository#az_acr_repository_untag
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
