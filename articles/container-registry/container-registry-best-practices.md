---
title: Best practices voor het register
description: Leer hoe u Azure Container Registry effectief gebruikt door deze aanbevolen procedures te volgen.
ms.topic: article
ms.date: 01/07/2021
ms.openlocfilehash: 01c8c7f547be9dd225022fb3315a4bdecc48c2bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100578140"
---
# <a name="best-practices-for-azure-container-registry"></a>Aanbevolen procedures voor Azure Container Registry

Door deze aanbevolen procedures te volgen, kunt u de prestaties en het rendabele gebruik van uw persoonlijke REGI ster in azure maximaliseren om container installatie kopieën en andere artefacten op te slaan en te implementeren.

Zie voor achtergrond informatie over Register concepten [over registers, opslag plaatsen en installatie kopieën](container-registry-concepts.md). Zie ook [aanbevelingen voor het labelen en versie beheer van container installatie kopieën](container-registry-image-tag-version.md) voor strategieën voor het labelen en de installatie kopieën in het REGI ster. 

## <a name="network-close-deployment"></a>Implementatie dichtbij het netwerk

Maak uw containerregister in dezelfde Azure-regio waar u containers implementeert. Door uw register te plaatsen in een regio die zich dichtbij het netwerk bevindt, kunnen de hosts van uw container uw helpen zowel de latentie als de kosten te verlagen.

Implementatie dichtbij het netwerk is een van de belangrijkste redenen om een privé containerregister te gebruiken. Docker-installatiekopieën hebben een efficiënte [gelaagde constructie](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/) die incrementele implementaties mogelijk maakt. Nieuwe knooppunten moeten echter alle lagen ophalen die nodig zijn voor een bepaalde installatiekopie. Deze initiële `docker pull` kan al gauw meerdere gigabytes bedragen. Met een privéregister dichtbij uw implementatie wordt de netwerklatentie tot een minimum teruggebracht.
Bovendien brengen alle openbare clouds, waaronder Azure, netwerkkosten voor uitgaande gegevens in rekening. Het binnenhalen van installatiekopieën van het ene datacenter in het andere, verhoogt behalve de latentie ook de netwerkkosten voor uitgaande gegevens.

## <a name="geo-replicate-multi-region-deployments"></a>Geo-replicatie voor implementaties in meerdere regio’s

Gebruik de functie voor [geo-replicatie](container-registry-geo-replication.md) in Azure Container Registry om containers naar meerdere regio's te implementeren. Of u nu internationale klanten vanuit lokale datacentra helpt of uw dat uw ontwikkelteam zich op verschillende locaties bevindt, u kunt uw registerbeheer vereenvoudigen en latentie minimaliseren door geo-replicatie op uw register toe te passen. U kunt ook regionale [webhooks](container-registry-webhook.md) configureren om u op de hoogte te stellen van gebeurtenissen in specifieke replica's, zoals wanneer installatie kopieën worden gepusht.

Geo-replicatie is beschikbaar voor [Premium](container-registry-skus.md) -registers. Zie de driedelige zelfstudie [Geo-replicatie in Azure Container Registry](container-registry-tutorial-prepare-registry.md) voor informatie over het gebruik van geo-replicatie.

## <a name="maximize-pull-performance"></a>Optimale pull-prestaties

Naast het plaatsen van installatie kopieën, kunnen de kenmerken van uw installatie kopieën zelf de prestaties van het pull-systeem beïnvloeden.

* **Afbeeldings grootte** : Verklein de grootte van uw afbeeldingen door overbodige [lagen](container-registry-concepts.md#manifest) te verwijderen of de grootte van lagen te verminderen. Eén manier om de grootte van de installatie kopie te verkleinen is door gebruik te maken van de oplossing voor het bouwen van de [multi fase docker](https://docs.docker.com/develop/develop-images/multistage-build/) om alleen de benodigde runtime componenten op te nemen. 

  Controleer ook of de installatie kopie een lichtere basis installatie kopie van het besturings systeem kan bevatten. En als u een implementatie omgeving gebruikt, zoals Azure Container Instances die bepaalde basis installatie kopieën in de cache opslaat, controleert u of u een afbeelding slaag kunt wisselen voor een van de in de cache opgeslagen installatie kopieën. 
* **Aantal lagen** : Hiermee wordt het aantal gebruikte lagen gebalanceerd. Als u te weinig hebt, kunt u niet profiteren van het opnieuw gebruiken en opslaan van lagen op de host. Te veel, en uw implementatie omgeving brengt meer tijd in betrekken en decomprimeren. Vijf tot tien lagen zijn optimaal.

Kies [ook een servicelaag](container-registry-skus.md) van Azure container Registry die voldoet aan de prestatie behoeften. De Premium-laag biedt de grootste band breedte en het hoogste tarief van gelijktijdige lees-en schrijf bewerkingen wanneer u grote hoeveel heden implementaties hebt.

## <a name="repository-namespaces"></a>Opslagplaatsnaamruimten

Door opslagplaats naam ruimten te gebruiken, kunt u het delen van één REGI ster op meerdere groepen binnen uw organisatie toestaan. Registers kunnen worden gedeeld tussen implementaties en teams. Azure Container Registry biedt ondersteuning voor geneste naamruimten, waardoor met geïsoleerde groepen kan worden gewerkt. Het REGI ster beheert echter alle opslag plaatsen onafhankelijk, niet als een-hiërarchie.

Neem bijvoorbeeld de volgende container installatiekopielabels in overweging. Installatie kopieën die voor het hele bedrijf worden gebruikt, `aspnetcore` worden in de hoofd naam ruimte geplaatst, terwijl container installatie kopieën die eigendom zijn van de producten en marketing groepen elk hun eigen naam ruimten gebruiken.

- *contoso.azurecr.io/aspnetcore:2.0*
- *contoso.azurecr.io/products/widget/web:1*
- *contoso.azurecr.io/products/bettermousetrap/refundapi:12.3*
- *contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42*

## <a name="dedicated-resource-group"></a>Toegewezen resourcegroep

Omdat container registers resources zijn die worden gebruikt in meerdere container-hosts, moet een REGI ster zich in een eigen resource groep bevinden.

Hoewel u kunt experimenteren met een specifiek type host, zoals [Azure container instances](../container-instances/container-instances-overview.md), wilt u waarschijnlijk het container exemplaar verwijderen wanneer u klaar bent. Maar misschien wilt u de verzameling installatiekopieën die u naar Azure Container Registry hebt gepusht, wel houden. Door uw register in een eigen resourcegroep te plaatsen, verkleint u het risico dat u de verzameling installatiekopieën in het register per ongeluk verwijdert als u de resourcegroep van de containerinstantie verwijdert.

## <a name="authentication-and-authorization"></a>Verificatie en autorisatie

Voor de verificatie van een Azure-containerregister bestaan er twee primaire scenario's: afzonderlijke verificatie en serviceverificatie (ook wel een 'headless'-verificatie genoemd). De volgende tabel bevat een kort overzicht van deze scenario's en de verificatiemethode die voor elk ervan wordt aanbevolen.

| Type | Voorbeeldscenario | Aanbevolen methode |
|---|---|---|
| Afzonderlijke identiteit | Een ontwikkelaar die installatiekopieën binnenhaalt op of pusht vanaf zijn ontwikkelcomputer. | [az acr login](/cli/azure/acr#az-acr-login) |
| Headless/service-identiteit | Bouw en implementeer pijplijnen waarbij de gebruiker niet direct is betrokken. | [Service-principal](container-registry-authentication.md#service-principal) |

Zie [verifiëren met een Azure container Registry](container-registry-authentication.md)voor uitgebreide informatie over deze en andere Azure container Registry verificatie scenario's.

Azure Container Registry ondersteunt beveiligings procedures in uw organisatie om taken en bevoegdheden naar verschillende identiteiten te distribueren. Met [op rollen gebaseerd toegangs beheer](container-registry-roles.md)kunt u de juiste machtigingen toewijzen aan verschillende gebruikers, service-principals of andere identiteiten die verschillende register bewerkingen uitvoeren. U kunt bijvoorbeeld push machtigingen toewijzen aan een service-principal die wordt gebruikt in een build-pijp lijn en pull-machtigingen toewijzen aan een andere identiteit die wordt gebruikt voor de implementatie. [Tokens](container-registry-repository-scoped-permissions.md) maken voor nauw keurige, beperkte tijd toegang tot specifieke opslag plaatsen.

## <a name="manage-registry-size"></a>Registergrootte beheren      

De opslag beperkingen van elke [container register service-laag][container-registry-skus] zijn bedoeld om te worden uitgelijnd met een typisch scenario: **Basic** om aan de slag te gaan, **standaard** voor de meeste productie toepassingen en **Premium** voor de prestaties van de Hyper-schaal en [geo-replicatie][container-registry-geo-replication]. Tijdens de levensduur van het register moet u de grootte ervan beheren door regelmatig ongebruikte inhoud te verwijderen.

Gebruik de Azure CLI [-opdracht AZ ACR show-Usage][az-acr-show-usage] om de huidige grootte van het REGI ster weer te geven:

```azurecli
az acr show-usage --resource-group myResourceGroup --name myregistry --output table
```

```output
NAME      LIMIT         CURRENT VALUE    UNIT
--------  ------------  ---------------  ------
Size      536870912000  185444288        Bytes
Webhooks  100                            Count
```

U kunt ook de huidige opslag locatie vinden die wordt gebruikt in het **overzicht** van het REGI ster in de Azure portal:

![Informatie over het registergebruik in Azure Portal][registry-overview-quotas]

### <a name="delete-image-data"></a>Afbeeldings gegevens verwijderen

Azure Container Registry ondersteunt verschillende methoden voor het verwijderen van afbeeldings gegevens uit het container register. U kunt installatie kopieën verwijderen via tag of manifest Digest of een hele opslag plaats verwijderen.

Zie [container installatie kopieën in azure container Registry verwijderen](container-registry-delete.md)voor meer informatie over het verwijderen van afbeeldings gegevens uit het REGI ster, inclusief niet-gelabeld (ook wel ' Dangling ' of ' zwevende ') installatie kopieën genoemd.

## <a name="next-steps"></a>Volgende stappen

Azure Container Registry is beschikbaar in verschillende lagen (ook wel Sku's genoemd) die verschillende mogelijkheden bieden. Zie [Azure container Registry service lagen](container-registry-skus.md)voor meer informatie over de beschik bare service lagen.

Zie [Azure-beveiligings basislijn voor Azure container Registry](security-baseline.md)voor aanbevelingen voor het verbeteren van de beveiligings postuur van uw container registers.

<!-- IMAGES -->
[delete-repository-portal]: ./media/container-registry-best-practices/delete-repository-portal.png
[registry-overview-quotas]: ./media/container-registry-best-practices/registry-overview-quotas.png

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-show-usage]: /cli/azure/acr#az-acr-show-usage
[azure-cli]: /cli/azure
[azure-portal]: https://portal.azure.com
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-skus]: container-registry-skus.md