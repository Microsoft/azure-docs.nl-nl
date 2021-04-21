---
title: Inleiding tot containergroepen
description: Meer informatie over containergroepen in Azure Container Instances, een verzameling exemplaren die een levenscyclus en resources delen, zoals CPU's, opslag en netwerk
ms.topic: article
ms.date: 11/01/2019
ms.custom: mvc
ms.openlocfilehash: a2cb3eac5baa5b1035749d28b9fb99bbb45b9ee6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790883"
---
# <a name="container-groups-in-azure-container-instances"></a>Containergroepen in Azure Container Instances

De resource op het hoogste niveau in Azure Container Instances is de *containergroep*. In dit artikel wordt beschreven wat containergroepen zijn en wat de typen scenario's zijn die ze mogelijk maken.

## <a name="what-is-a-container-group"></a>Wat is een containergroep?

Een containergroep is een verzameling containers die op dezelfde hostmachine worden gepland. De containers in een containergroep delen een levenscyclus, resources, lokaal netwerk en opslagvolumes. Dit is in concept vergelijkbaar met een *pod* in [Kubernetes.][kubernetes-pod]

In het volgende diagram ziet u een voorbeeld van een containergroep die meerdere containers bevat:

![Diagram containergroepen][container-groups-example]

Dit voorbeeld van een containergroep:

* Is gepland op één hostmachine.
* Er wordt een DNS-naamlabel toegewezen.
* Geeft één openbaar IP-adres weer, met één opengemaakte poort.
* Bestaat uit twee containers. De ene container luistert op poort 80, terwijl de andere luistert op poort 5000.
* Bevat twee Azure-bestands shares als volume-mounts en elke container wordt lokaal aan een van de shares bevestigd.

> [!NOTE]
> Groepen met meerdere containers ondersteunen momenteel alleen Linux-containers. Voor Windows-containers ondersteunt Azure Container Instances implementatie van één containerin exemplaar. We werken aan alle functies voor Windows-containers, maar u kunt de huidige platformverschillen vinden in overzicht van [de service](container-instances-overview.md#linux-and-windows-containers).

## <a name="deployment"></a>Implementatie

Hier zijn twee algemene manieren om een groep met meerdere containers te implementeren: een Resource Manager [of][resource-manager template] een [YAML-bestand gebruiken.][yaml-file] Een Resource Manager wordt aanbevolen wanneer u extra Azure-serviceresources moet implementeren (bijvoorbeeld een [Azure Files share][azure-files]) wanneer u de container-exemplaren implementeert. Vanwege de beknoptere aard van de YAML-indeling wordt een YAML-bestand aanbevolen wanneer uw implementatie alleen container-exemplaren bevat. Zie de referentie voor Resource Manager of [YAML-referentie](container-instances-reference-yaml.md) voor meer [informatie](/azure/templates/microsoft.containerinstance/containergroups) over eigenschappen die u kunt instellen.

Als u de configuratie van een containergroep wilt behouden, kunt u de configuratie exporteren naar een YAML-bestand met behulp van de Azure CLI-opdracht [az container export.][az-container-export] Met Exporteren kunt u de configuraties van uw containergroep opslaan in versiebeheer voor 'configuratie als code'. Of gebruik het geëxporteerde bestand als uitgangspunt bij het ontwikkelen van een nieuwe configuratie in YAML.



## <a name="resource-allocation"></a>Resourcetoewijzing

Azure Container Instances resources zoals CPU's, geheugen en optioneel [][gpus] GPU's (preview) toe aan een groep met meerdere containers door de [resourceaanvragen][resource-requests] van de exemplaren in de groep toe te voegen. Als u bijvoorbeeld een containergroep met twee container-exemplaren maakt, waarbij elk 1 CPU wordt gevraagd, krijgt de containergroep 2 CPU's toegewezen.

### <a name="resource-usage-by-container-instances"></a>Resourcegebruik door container instances

Aan elke container-instantie in een groep worden de resources toegewezen die zijn opgegeven in de resourceaanvraag. Het maximum aantal resources dat door een container-exemplaar in een groep wordt gebruikt, kan echter verschillen als u de eigenschap van de optionele [resourcelimiet configureert.][resource-limits] De resourcelimiet van een container-instantie moet groter zijn dan of gelijk zijn aan de verplichte [eigenschap resourceaanvraag.][resource-requests]

* Als u geen resourcelimiet opgeeft, is het maximale resourcegebruik van de container-instantie hetzelfde als de resourceaanvraag.

* Als u een limiet opgeeft voor een container-instantie, kan het maximale gebruik van het exemplaar groter zijn dan de aanvraag, tot de limiet die u hebt ingesteld. Overeenkomstig kan het resourcegebruik door andere container instances in de groep afnemen. De maximale resourcelimiet die u voor een container-exemplaar kunt instellen, is het totale aantal resources dat aan de groep is toegewezen.
    
In een groep met twee container-exemplaren die elk 1 CPU aanvragen, kan een van uw containers bijvoorbeeld een workload uitvoeren die meer CPU's vereist dan de andere.

In dit scenario kunt u een resourcelimiet van maximaal 2 CPU's instellen voor de container-instantie. Met deze configuratie kan de container-instantie maximaal 2 CPU's gebruiken, indien beschikbaar.

> [!NOTE]
> Een kleine hoeveelheid resources van een containergroep wordt gebruikt door de onderliggende infrastructuur van de service. Uw containers hebben toegang tot de meeste, maar niet alle resources die aan de groep zijn toegewezen. Plan daarom een kleine resourcebuffer wanneer u resources aanvraagt voor containers in de groep.

### <a name="minimum-and-maximum-allocation"></a>Minimale en maximale toewijzing

* Wijs **minimaal** 1 CPU en 1 GB geheugen toe aan een containergroep. Afzonderlijke container instances binnen een groep kunnen worden ingericht met minder dan 1 CPU en 1 GB geheugen. 

* Zie de **beschikbaarheid van** resources voor resources in de implementatieregio voor de maximale [Azure Container Instances][region-availability] in een containergroep.

## <a name="networking"></a>Netwerken

Containergroepen kunnen een extern gericht IP-adres, een of meer poorten op dat IP-adres en een DNS-label met een FQDN (Fully Qualified Domain Name) delen. Als u wilt dat externe clients een container binnen de groep kunnen bereiken, moet u de poort op het IP-adres en vanuit de container blootstellen. Het IP-adres en de FQDN van een containergroep worden vrijgegeven wanneer de containergroep wordt verwijderd. 

Binnen een containergroep kunnen container instances elkaar bereiken via localhost op elke poort, zelfs als deze poorten niet extern beschikbaar zijn op het IP-adres van de groep of vanuit de container.

Implementeer eventueel containergroepen in een [virtueel Azure-netwerk zodat][virtual-network] containers veilig kunnen communiceren met andere resources in het virtuele netwerk.

## <a name="storage"></a>Storage

U kunt externe volumes opgeven die in een containergroep moeten worden bevestigd. Ondersteunde volumes zijn onder andere:
* [Azure-bestandsshare][azure-files]
* [Geheim][secret]
* [Lege map][empty-directory]
* [Gekloonde Git-repo][volume-gitrepo]

U kunt deze volumes in specifieke paden binnen de afzonderlijke containers in een groep in kaart brengen. 

## <a name="common-scenarios"></a>Algemene scenario's

Groepen met meerdere containers zijn handig in gevallen waarin u één functionele taak wilt onderverdelen in een klein aantal containerafbeeldingen. Deze afbeeldingen kunnen vervolgens worden geleverd door verschillende teams en hebben afzonderlijke resourcevereisten.

Een voorbeeld van een gebruik kan zijn:

* Een container voor een webtoepassing en een container die de meest recente inhoud uit broncodebeheer haalt.
* Een toepassingscontainer en een container voor logboekregistratie. De container voor logboekregistratie verzamelt de logboeken en uitvoer van metrische gegevens door de hoofdtoepassing en schrijft deze naar langetermijnopslag.
* Een toepassingscontainer en een bewakingscontainer. De bewakingscontainer doet periodiek een aanvraag naar de toepassing om ervoor te zorgen dat deze wordt uitgevoerd en correct reageert, en geeft een waarschuwing als dat niet het juiste is.
* Een front-endcontainer en een back-endcontainer. De front-end kan een webtoepassing bedienen, met de back-end die een service gebruikt om gegevens op te halen. 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het implementeren van een containergroep met meerdere containers met een Azure Resource Manager sjabloon:

> [!div class="nextstepaction"]
> [Een containergroep implementeren][resource-manager template]

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/

<!-- LINKS - Internal -->
[resource-manager template]: container-instances-multi-container-group.md
[yaml-file]: container-instances-multi-container-yaml.md
[region-availability]: container-instances-region-availability.md
[resource-requests]: /rest/api/container-instances/containergroups/createorupdate#resourcerequests
[resource-limits]: /rest/api/container-instances/containergroups/createorupdate#resourcelimits
[resource-requirements]: /rest/api/container-instances/containergroups/createorupdate#resourcerequirements
[azure-files]: container-instances-volume-azure-files.md
[virtual-network]: container-instances-virtual-network-concepts.md
[secret]: container-instances-volume-secret.md
[volume-gitrepo]: container-instances-volume-gitrepo.md
[gpus]: container-instances-gpu.md
[empty-directory]: container-instances-volume-emptydir.md
[az-container-export]: /cli/azure/container#az_container_export
