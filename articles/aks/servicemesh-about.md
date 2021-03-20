---
title: Over service netten
description: Verkrijg een overzicht van de service netten, hun architectuur en mogelijkheden, en welke criteria u moet overwegen wanneer u er een moet selecteren om te implementeren.
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: eca49a3fac1ea0398ebe1d05bde20fbca3c81232
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "77594308"
---
# <a name="about-service-meshes"></a>Over service netten

Een service-net biedt mogelijkheden als verkeers beheer, tolerantie, beleid, beveiliging, sterke identiteit en de waarneem baarheid van uw workloads. Uw toepassing is losgekoppeld van deze operationele mogelijkheden en het service-net verplaatst deze uit de toepassingslaag en omlaag naar de laag van de infra structuur.

## <a name="scenarios"></a>Scenario's

Dit zijn enkele van de scenario's die kunnen worden ingeschakeld voor uw workloads wanneer u een service-net gebruikt:

- **Alle verkeer in het cluster versleutelen** : Hiermee schakelt u wederzijdse TLS in tussen opgegeven services in het cluster. Dit kan worden uitgebreid tot binnenkomend en uitgaand verkeer op de netwerk verbinding. Biedt een veilige standaard optie zonder wijzigingen die nodig zijn voor toepassings code en-infra structuur.

- **Canarische en gefaseerde implementaties** : Geef voor waarden op voor een subset van verkeer dat wordt gerouteerd naar een set nieuwe services in het cluster. Bij een geslaagde test van de Canarische versie verwijdert u de voorwaardelijke route ring en de fase neemt geleidelijk% van al het verkeer naar de nieuwe service toe. Uiteindelijk wordt al het verkeer omgeleid naar de nieuwe service.

- **Verkeers beheer en manipulatie** : een beleid maken voor een service waarmee het aantal verkeer naar een versie van een service van een bepaalde oorsprong wordt beperkt. Of een beleid waarmee een strategie voor opnieuw proberen wordt toegepast op klassen van fouten tussen opgegeven services. Gespiegeld live verkeer naar nieuwe versies van services tijdens een migratie of om problemen op te lossen. Injectie fouten tussen services in een test omgeving om de tolerantie te testen.

- Bedieping **: krijg** inzicht in de manier waarop uw services het verkeer aansluiten dat tussen hen loopt. U kunt metrische gegevens, logboeken en traceringen verkrijgen voor al het verkeer in het cluster, en uit-en uitgangen. Voeg gedistribueerde tracerings mogelijkheden toe aan uw toepassingen.

## <a name="architecture"></a>Architectuur

Een service-net bestaat doorgaans uit een besturings vlak en het gegevens vlak.

Het **besturings vlak** bevat een aantal onderdelen die ondersteuning bieden voor het beheer van het service-net. Dit geldt meestal voor een beheer interface die een gebruikers interface of API kan zijn. Er zijn ook onderdelen waarmee de regel en beleids definities worden beheerd die bepalen hoe het service-net specifieke mogelijkheden moet implementeren. Er zijn ook onderdelen die aspecten van beveiliging beheren, zoals sterke identiteit en certificaten voor mTLS. Service netten hebben doorgaans meestal een meet-of waarneem bare component waarmee metrische gegevens en telemetrie van de werk belastingen worden verzameld en geaggregeerd.

Het **gegevens vlak** bestaat doorgaans uit een proxy die transparant is geïnjecteerd als een zijspan wagen voor uw werk belastingen. Deze proxy is geconfigureerd voor het beheren van al het netwerk verkeer in en uit de Pod met uw werk belasting. Hierdoor kan de proxy worden geconfigureerd om verkeer te beveiligen via mTLS, dynamisch verkeer te routeren, beleid toe te passen op verkeer en metrische gegevens en tracerings informatie te verzamelen. 

![Typische architectuur voor Service-Mesh](media/servicemesh/typical-architecture.png)

## <a name="capabilities"></a>Functies

Elk van de service netten hebben een natuurlijke en focus op het ondersteunen van specifieke scenario's, maar u zult doorgaans ontdekken dat er een aantal van de volgende mogelijkheden wordt geïmplementeerd.

### <a name="traffic-management"></a>Verkeers beheer 

- **Protocol** -laag 7 (http, grpc)
- **Dynamische route ring** : voorwaardelijk, gewicht, spie gelen
- **Tolerantie** : time-outs, nieuwe pogingen, circuit onderbrekers
- **Beleid** : toegangs beheer, frequentie limieten, quota's
- **Testen** -fout injectie

### <a name="security"></a>Beveiliging

- **Versleuteling** – mTLS, certificaat beheer, externe certificerings instantie
- **Sterke identiteit** – SPIFFE of vergelijkbaar
- **Verificatie:** authenticatie, autorisatie

### <a name="observability"></a>Waarneembaarheid

- **Metrische** gegevens: gouden metrische gegevens, Prometheus, grafana
- **Tracering** -traceringen over workloads
- **Verkeer** : cluster, binnenkomend/uitgaand

### <a name="mesh"></a>Mesh

- **Ondersteunde Compute** -Kubernetes, virtual machines
- **Multi-cluster** -gateways, Federatie

## <a name="selection-criteria"></a>Selectie criteria

Voordat u een service-net selecteert, moet u ervoor zorgen dat u inzicht krijgt in uw vereisten en de redenen voor het installeren van een service-net. Probeer de volgende vragen te beantwoorden.

- **Is een ingangs controller voldoende voor mijn behoeften?** -Soms is het mogelijk dat een functie zoals een/b-test of het splitsen van verkeer bij de inkomen voldoende is om het vereiste scenario te ondersteunen. Voeg geen ingewikkelde complexiteit toe aan uw omgeving.

- **Kunnen mijn werk belastingen en omgeving de extra overhead verdragen?** -Alle extra onderdelen die vereist zijn voor de ondersteuning van het service-net vereisen extra resources zoals CPU en geheugen. Daarnaast voegen alle proxy's en de bijbehorende beleids controles latentie toe aan uw verkeer. Als u werk belastingen hebt die zeer gevoelig zijn voor latentie of als u de extra resources niet kunt leveren voor de service mesh-onderdelen, kunt u het opnieuw overwegen.

- **Is het toevoegen van extra complexiteit onnodig?** -Als de reden voor het installeren van een service-net is het verkrijgen van een mogelijkheid die niet nood zakelijk is voor het bedrijf of operationele teams, moet u nagaan of de extra complexiteit van installatie, onderhoud en configuratie waard is.

- **Kan dit in een incrementele benadering worden goedgekeurd?** -Sommige service netten die een groot aantal mogelijkheden bieden, kunnen in een meer incrementele benadering worden aangenomen. Installeer alleen de onderdelen die u nodig hebt om ervoor te zorgen dat uw succes is. Als u meer vertrouwen hebt en er aanvullende mogelijkheden zijn vereist, kunt u deze verkennen. Het is niet meer nodig om *Alles* vanaf het begin te installeren.

Als u na een zorgvuldige overweging hebt besloten dat u een service-net nodig hebt om de vereiste mogelijkheden te bieden, dan is uw volgende beslissing het *service-net?*

Houd rekening met de volgende gebieden en welke hiervan het meest zijn afgestemd op uw behoeften. Dit leidt u naar de beste aanpassing voor uw omgeving en workloads. In het gedeelte [volgende stappen](#next-steps) vindt u meer informatie over specifieke service netten en hoe deze worden toegewezen aan deze gebieden.

- Beheer van **technische** verkeer, beleid, beveiliging, waarneembaar

- **Business** -Commercial Support, Foundation (CNCF), OSS-licentie, governance

- **Operationeel** : installatie/upgrades, resource vereisten, prestatie vereisten, integraties (metrische gegevens, telemetrie, Dash boards, hulpprogram MA'S, SMI), gemengde werk belastingen (Linux en Windows-knooppunt groepen), compute (Kubernetes, virtual machines), multi-cluster

- **Beveiliging** -verificatie, identiteit, certificaat beheer en rotatie, pluggable externe CA


## <a name="next-steps"></a>Volgende stappen

De volgende documentatie bevat meer informatie over service netten die u kunt uitproberen op Azure Kubernetes service (AKS):

> [!div class="nextstepaction"]
> [Meer informatie over Istio...][istio-about]

> [!div class="nextstepaction"]
> [Meer informatie over Linkerd...][linkerd-about]

> [!div class="nextstepaction"]
> [Meer informatie over consul...][consul-about]

Het is ook mogelijk dat u SMI (Service Mesh Interface) wilt verkennen, een standaard interface voor service netten op Kubernetes:

- [Service net interface (SMI)][smi]


<!-- LINKS - external -->
[smi]: https://smi-spec.io/

<!-- LINKS - internal -->
[istio-about]: ./servicemesh-istio-about.md
[linkerd-about]: ./servicemesh-linkerd-about.md
[consul-about]: ./servicemesh-consul-about.md