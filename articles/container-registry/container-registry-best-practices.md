---
title: Best practices voor het register
description: Leer hoe u Azure Container Registry effectief gebruikt door deze aanbevolen procedures te volgen.
ms.topic: article
ms.date: 01/07/2021
ms.openlocfilehash: 0811cc4a5bffc21ffba19e64a3887eab6bc36fbb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784133"
---
# <a name="best-practices-for-azure-container-registry"></a>Aanbevolen procedures voor Azure Container Registry

Door deze best practices te volgen, kunt u de prestaties en het rendabele gebruik van uw privéregister in Azure maximaliseren voor het opslaan en implementeren van containerafbeeldingen en andere artefacten.

Zie About [registries, repositories, and images (Over registers, opslagplaatsen](container-registry-concepts.md)en afbeeldingen) voor achtergrondinformatie over registerconcepten. Zie ook [Recommendations for tagging and versioning container images](container-registry-image-tag-version.md) (Aanbevelingen voor het taggen en versieren van containerafbeeldingen) voor strategieën voor het taggen en versien van afbeeldingen in uw register. 

## <a name="network-close-deployment"></a>Implementatie dichtbij het netwerk

Maak uw containerregister in dezelfde Azure-regio waar u containers implementeert. Door uw register te plaatsen in een regio die zich dichtbij het netwerk bevindt, kunnen de hosts van uw container uw helpen zowel de latentie als de kosten te verlagen.

Implementatie dichtbij het netwerk is een van de belangrijkste redenen om een privé containerregister te gebruiken. Docker-installatiekopieën hebben een efficiënte [gelaagde constructie](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/) die incrementele implementaties mogelijk maakt. Nieuwe knooppunten moeten echter alle lagen ophalen die nodig zijn voor een bepaalde installatiekopie. Deze initiële `docker pull` kan al gauw meerdere gigabytes bedragen. Met een privéregister dichtbij uw implementatie wordt de netwerklatentie tot een minimum teruggebracht.
Bovendien brengen alle openbare clouds, waaronder Azure, netwerkkosten voor uitgaande gegevens in rekening. Het binnenhalen van installatiekopieën van het ene datacenter in het andere, verhoogt behalve de latentie ook de netwerkkosten voor uitgaande gegevens.

## <a name="geo-replicate-multi-region-deployments"></a>Geo-replicatie voor implementaties in meerdere regio’s

Gebruik de functie voor [geo-replicatie](container-registry-geo-replication.md) in Azure Container Registry om containers naar meerdere regio's te implementeren. Of u nu internationale klanten vanuit lokale datacentra helpt of uw dat uw ontwikkelteam zich op verschillende locaties bevindt, u kunt uw registerbeheer vereenvoudigen en latentie minimaliseren door geo-replicatie op uw register toe te passen. U kunt ook regionale [webhooks configureren om](container-registry-webhook.md) u op de hoogte te stellen van gebeurtenissen in specifieke replica's, zoals wanneer afbeeldingen worden pushen.

Geo-replicatie is [](container-registry-skus.md) beschikbaar met Premium-registers. Zie de driedelige zelfstudie [Geo-replicatie in Azure Container Registry](container-registry-tutorial-prepare-registry.md) voor informatie over het gebruik van geo-replicatie.

## <a name="maximize-pull-performance"></a>Pull-prestaties maximaliseren

Naast het plaatsen van afbeeldingen dicht bij uw implementaties, kunnen kenmerken van uw afbeeldingen zelf invloed hebben op de pull-prestaties.

* **Afbeeldingsgrootte:** minimaliseer de grootte van uw afbeeldingen door onnodige lagen te [verwijderen](container-registry-concepts.md#manifest) of de grootte van lagen te verkleinen. Een manier om de grootte van de afbeelding te verkleinen, is door de [Docker-build-benadering](https://docs.docker.com/develop/develop-images/multistage-build/) met meerdere fases te gebruiken om alleen de benodigde runtime-onderdelen op te nemen. 

  Controleer ook of uw afbeelding een lichtere basis-OS-afbeelding kan bevatten. En als u een implementatieomgeving gebruikt, zoals Azure Container Instances die bepaalde basisafbeeldingen in de cache opgeslagen, controleert u of u een afbeeldingslaag kunt wisselen voor een van de afbeeldingen in de cache. 
* **Aantal lagen: een balans** vinden tussen het aantal gebruikte lagen. Als u te weinig hebt, profiteert u niet van het hergebruik van lagen en opslaan in de caching op de host. Te veel en uw implementatieomgeving besteedt meer tijd aan het binnenhalen en decomprimeren. Vijf tot tien lagen is optimaal.

Kies ook een [servicelaag van](container-registry-skus.md) Azure Container Registry die voldoet aan uw prestatiebehoeften. De Premium-laag biedt de grootste bandbreedte en de hoogste snelheid van gelijktijdige lees- en schrijfbewerkingen wanneer u implementaties met een hoog volume hebt.

## <a name="repository-namespaces"></a>Opslagplaatsnaamruimten

Door naamruimten voor opslagplaatsen te gebruiken, kunt u toestaan dat één register wordt gedeeld in meerdere groepen binnen uw organisatie. Registers kunnen worden gedeeld tussen implementaties en teams. Azure Container Registry biedt ondersteuning voor geneste naamruimten, waardoor met geïsoleerde groepen kan worden gewerkt. Het register beheert echter alle opslagplaatsen onafhankelijk, niet als een hiërarchie.

Neem bijvoorbeeld de volgende container installatiekopielabels in overweging. Afbeeldingen die bedrijfsbreed worden gebruikt, zoals , worden in de hoofdnaamruimte geplaatst, terwijl containerafbeeldingen die eigendom zijn van de groepen Producten en Marketing elk hun eigen `aspnetcore` naamruimten gebruiken.

- *contoso.azurecr.io/aspnetcore:2.0*
- *contoso.azurecr.io/products/widget/web:1*
- *contoso.azurecr.io/products/bettermousetrap/refundapi:12.3*
- *contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42*

## <a name="dedicated-resource-group"></a>Toegewezen resourcegroep

Omdat containerregisters resources zijn die worden gebruikt op meerdere containerhosts, moet een register zich in een eigen resourcegroep bevinden.

Hoewel u kunt experimenteren met een specifiek hosttype, zoals [Azure Container Instances,](../container-instances/container-instances-overview.md)wilt u waarschijnlijk de container-instantie verwijderen wanneer u klaar bent. Maar misschien wilt u de verzameling installatiekopieën die u naar Azure Container Registry hebt gepusht, wel houden. Door uw register in een eigen resourcegroep te plaatsen, verkleint u het risico dat u de verzameling installatiekopieën in het register per ongeluk verwijdert als u de resourcegroep van de containerinstantie verwijdert.

## <a name="authentication-and-authorization"></a>Verificatie en autorisatie

Voor de verificatie van een Azure-containerregister bestaan er twee primaire scenario's: afzonderlijke verificatie en serviceverificatie (ook wel een 'headless'-verificatie genoemd). De volgende tabel bevat een kort overzicht van deze scenario's en de verificatiemethode die voor elk ervan wordt aanbevolen.

| Type | Voorbeeldscenario | Aanbevolen methode |
|---|---|---|
| Afzonderlijke identiteit | Een ontwikkelaar die installatiekopieën binnenhaalt op of pusht vanaf zijn ontwikkelcomputer. | [az acr login](/cli/azure/acr#az_acr_login) |
| Headless/service-identiteit | Bouw en implementeer pijplijnen waarbij de gebruiker niet direct is betrokken. | [Service-principal](container-registry-authentication.md#service-principal) |

Zie Verifiëren met een [Azure-containerregister](container-registry-authentication.md)voor Azure Container Registry uitgebreide informatie over deze en andere verificatiescenario's.

Azure Container Registry ondersteunt beveiligingsprocedures in uw organisatie om taken en bevoegdheden naar verschillende identiteiten te distribueren. Wijs met op rollen gebaseerd toegangsbeheer de juiste machtigingen toe aan verschillende gebruikers, [service-principals](container-registry-roles.md)of andere identiteiten die verschillende registerbewerkingen uitvoeren. Wijs bijvoorbeeld pushmachtigingen toe aan een service-principal die wordt gebruikt in een build-pijplijn en wijs pull-machtigingen toe aan een andere identiteit die wordt gebruikt voor implementatie. Maak [tokens](container-registry-repository-scoped-permissions.md) voor een fijnafgepatrieerde, tijdsbegrensde toegang tot specifieke opslagplaatsen.

## <a name="manage-registry-size"></a>Registergrootte beheren      

De opslagbeperkingen van elke [servicelaag van het containerregister][container-registry-skus] zijn bedoeld om te worden afgestemd op een typisch scenario: **Basic** om aan de slag te gaan, **Standard** voor de meeste productietoepassingen en **Premium** voor hyperschaalprestaties en [geo-replicatie.][container-registry-geo-replication] Tijdens de levensduur van het register moet u de grootte ervan beheren door regelmatig ongebruikte inhoud te verwijderen.

Gebruik de Azure CLI-opdracht [az acr show-usage om][az-acr-show-usage] de huidige grootte van uw register weer te geven:

```azurecli
az acr show-usage --resource-group myResourceGroup --name myregistry --output table
```

```output
NAME      LIMIT         CURRENT VALUE    UNIT
--------  ------------  ---------------  ------
Size      536870912000  185444288        Bytes
Webhooks  100                            Count
```

U kunt de huidige opslag die wordt gebruikt ook vinden in het **overzicht** van uw register in de Azure Portal:

![Informatie over het registergebruik in Azure Portal][registry-overview-quotas]

### <a name="delete-image-data"></a>Afbeeldingsgegevens verwijderen

Azure Container Registry ondersteunt verschillende methoden voor het verwijderen van afbeeldingsgegevens uit uw containerregister. U kunt afbeeldingen verwijderen met een tag of manifest digest, of een hele opslagplaats verwijderen.

Zie Containerafbeeldingen verwijderen in Azure Container Registry voor meer informatie over het verwijderen van afbeeldingsgegevens uit [het register,](container-registry-delete.md)inclusief niet-getagged (ook wel zwevende of zwevende) afbeeldingen genoemd.

## <a name="next-steps"></a>Volgende stappen

Azure Container Registry is beschikbaar in verschillende lagen (ook wel SKU's genoemd) die verschillende mogelijkheden bieden. Zie servicelagen Azure Container Registry [voor meer informatie over de beschikbare servicelagen.](container-registry-skus.md)

Zie Azure-beveiligingsbasislijn voor meer informatie over het verbeteren van de beveiligingsstatus van uw containerregisters [Azure Container Registry.](security-baseline.md)

<!-- IMAGES -->
[delete-repository-portal]: ./media/container-registry-best-practices/delete-repository-portal.png
[registry-overview-quotas]: ./media/container-registry-best-practices/registry-overview-quotas.png

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
[az-acr-show-usage]: /cli/azure/acr#az_acr_show_usage
[azure-cli]: /cli/azure
[azure-portal]: https://portal.azure.com
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-skus]: container-registry-skus.md