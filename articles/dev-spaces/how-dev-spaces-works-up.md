---
title: Hoe u uw code uitvoert met Azure dev Spaces werkt
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Hierin worden de procedures beschreven voor het uitvoeren van uw code in azure Kubernetes service met Azure dev Spaces
keywords: azds. yaml, Azure dev Spaces, dev Spaces, docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: 1cace325f9415d46210636e5c04cc2d75589cc11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96014428"
---
# <a name="how-running-your-code-with-azure-dev-spaces-works"></a>Hoe u uw code uitvoert met Azure dev Spaces werkt

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Met Azure dev Spaces kunt u op verschillende manieren snel en probleem oplossing voor Kubernetes-toepassingen en samen werken met uw team op een AKS-cluster (Azure Kubernetes service). Zodra het [project is voor bereid om te worden uitgevoerd in een dev-ruimte][how-it-works-prep], kunt u ontwikkel ruimten gebruiken om uw project te bouwen en uit te voeren in uw AKS-cluster.

In dit artikel wordt beschreven wat er gebeurt met het uitvoeren van uw code in AKS met dev Spaces.

## <a name="run-your-code"></a>Uw code uitvoeren

Als u uw code in een ontwikkel ruimte wilt uitvoeren, geeft u de `up` opdracht op in dezelfde map als uw `azds.yaml` bestand:

```cmd
azds up
```

Met de `up` opdracht worden de bron bestanden van de toepassing en andere artefacten geüpload die nodig zijn om uw project te bouwen en uit te voeren op de dev-ruimte. Van daaruit, de controller in uw deel ruimte voor ontwikkel aars:

1. Hiermee maakt u de Kubernetes-objecten om uw toepassing te implementeren.
1. Bouwt de container voor uw toepassing.
1. Implementeert uw toepassing naar de ontwikkel ruimte.
1. Hiermee maakt u een openbaar toegankelijke DNS-naam voor het eind punt van uw toepassing, indien geconfigureerd.
1. Maakt gebruik van *port-forward* om toegang te bieden tot het eind punt van de toepassing met behulp van http://localhost .
1. Stdout en stderr worden doorgestuurd naar het hulp programma aan de client zijde.


## <a name="starting-a-service"></a>Een service starten

Wanneer u een service in een dev-ruimte start, werken het hulp programma en de controller aan de client zijde samen om uw bron bestanden te synchroniseren, uw container-en Kubernetes-objecten te maken en uw toepassing uit te voeren.

Op een meer gedetailleerd niveau ziet u hier wat er gebeurt wanneer u uitvoert `azds up` :

1. [Bestanden worden gesynchroniseerd][sync-section] van de computer van de gebruiker naar een Azure-bestands opslag die uniek is voor het AKS-cluster van de gebruiker. De bron code, het helm-diagram en de configuratie bestanden worden geüpload.
1. De controller maakt een aanvraag om een nieuwe sessie te starten. Deze aanvraag bevat verschillende eigenschappen, met inbegrip van een unieke ID, naam van de ruimte, het pad naar de bron code en een vlag voor fout opsporing.
1. De controller vervangt de tijdelijke aanduiding *$ (tag)* in het helm-diagram met de unieke sessie-id en installeert de helm-grafiek voor uw service. Door een verwijzing naar de unieke sessie-ID toe te voegen aan de helm-grafiek, kan de container die is geïmplementeerd op het AKS-cluster voor deze specifieke sessie worden gekoppeld aan de sessie aanvraag en de bijbehorende gegevens.
1. Tijdens de installatie van de helm-grafiek voegt de Kubernetes-webhook-Admission server extra containers toe aan de pod van uw toepassing voor instrumentatie en toegang tot de bron code van uw project. De containers devspaces-proxy en devspaces-proxy-init worden toegevoegd om HTTP-tracering en ruimte routering te bieden. De devspaces-build-container wordt toegevoegd om de pod toegang te geven tot het docker-exemplaar en de project bron code voor het bouwen van de container van uw toepassing.
1. Wanneer de pod van de toepassing is gestart, worden de container devspaces-build en de container devspaces-proxy-init gebruikt voor het bouwen van de toepassings container. Vervolgens worden de toepassings container-en devspaces-proxy containers gestart.
1. Nadat de toepassings container is gestart, gebruikt de client-side-functionaliteit de functionaliteit voor Kubernetes *-poorten* om HTTP-toegang tot uw toepassing mogelijk te maken http://localhost . Door deze poort door te sturen verbindt u uw ontwikkel computer met de service in uw dev-ruimte.
1. Wanneer alle containers in het Pod zijn gestart, wordt de service uitgevoerd. Op dit punt begint de client-side-functionaliteit de HTTP-traceringen, stdout en stderr te streamen. Deze informatie wordt weer gegeven door de client-side-functionaliteit voor de ontwikkelaar.

## <a name="updating-a-running-service"></a>Een actieve service bijwerken

Terwijl een service wordt uitgevoerd, kunnen Azure dev Spaces de service bijwerken als een van de project bron bestanden wordt gewijzigd. Dev Spaces zorgt er ook voor dat de service anders wordt bijgewerkt, afhankelijk van het type bestand dat wordt gewijzigd. Er zijn drie manieren om een actieve service bij te werken:

* Een bestand rechtstreeks bijwerken
* Het proces van de toepassing in de container van de actieve toepassing opnieuw samen stellen en opnieuw starten
* De container van de toepassing opnieuw samen stellen en implementeren

![Bestands synchronisatie voor Azure dev Spaces](media/how-dev-spaces-works/file-sync.svg)

Bepaalde project bestanden met statische activa, zoals HTML-, CSS-en cshtml-bestanden, kunnen rechtstreeks in de container van de toepassing worden bijgewerkt zonder dat er iets opnieuw hoeft te worden opgestart. Als een statisch activum wordt gewijzigd, wordt het nieuwe bestand gesynchroniseerd met de ontwikkel ruimte en vervolgens gebruikt door de container die wordt uitgevoerd.

Wijzigingen in bestanden zoals bron code-of toepassings configuratie bestanden kunnen worden toegepast door het proces van de toepassing opnieuw te starten binnen de container die wordt uitgevoerd. Zodra deze bestanden zijn gesynchroniseerd, wordt het proces van de toepassing opnieuw gestart binnen de container die wordt uitgevoerd met behulp van het *devhostagent* -proces. Bij het maken van de container van de toepassing vervangt de controller de opstart opdracht voor de toepassing met een ander proces met de naam *devhostagent*. Het daad werkelijke proces van de toepassing wordt vervolgens als een onderliggend proces uitgevoerd onder *devhostagent* en de uitvoer ervan wordt door gegeven met behulp van de uitvoer van *devhostagent*. Het *devhostagent* -proces maakt ook deel uit van dev Spaces en kan opdrachten uitvoeren in de container die wordt uitgevoerd namens ontwikkel ruimten. Bij het opnieuw opstarten *devhostagent*:

* Stopt het huidige proces of de processen die zijn gekoppeld aan de toepassing
* De toepassing opnieuw samen stellen
* Hiermee worden de processen die zijn gekoppeld aan de toepassing opnieuw gestart

De manier waarop *devhostagent* de voor gaande stappen uitvoert, wordt [geconfigureerd in `azds.yaml`][azds-yaml-section].

Voor updates voor Project bestanden zoals Dockerfiles, csproj-bestanden of een deel van de helm-grafiek moet de container van de toepassing opnieuw worden opgebouwd en geïmplementeerd. Wanneer een van deze bestanden wordt gesynchroniseerd met de dev-ruimte, voert de controller de [helm-upgrade opdracht uit][helm-upgrade] en wordt de container van de toepassing opnieuw opgebouwd en geïmplementeerd.

## <a name="file-synchronization"></a>Bestands synchronisatie

De eerste keer dat een toepassing wordt gestart in een dev-ruimte, worden alle bron bestanden van de toepassing geüpload. Terwijl de toepassing wordt uitgevoerd en later opnieuw wordt opgestart, worden alleen de gewijzigde bestanden geüpload. Er worden twee bestanden gebruikt om dit proces te coördineren: een bestand aan de client zijde en een bestand aan de spel besturing.

Het bestand aan de client zijde wordt opgeslagen in een tijdelijke map en krijgt de naam op basis van een hash van de projectmap die u uitvoert in dev Spaces. In Windows hebt u bijvoorbeeld een bestand zoals *Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog* voor uw project. In Linux wordt het bestand aan de client zijde opgeslagen in de *map/tmp* -map. U kunt de directory op macOS vinden door de opdracht uit te voeren `echo $TMPDIR` .

Dit bestand bevindt zich in JSON-indeling en bevat:

* Een vermelding voor elk project bestand dat wordt gesynchroniseerd met de ontwikkelaars ruimte
* Een synchronisatie-ID
* De tijds tempel van de laatste synchronisatie bewerking

Elke project bestands vermelding bevat een pad naar het bestand en de tijds tempel.

Het bestand aan de spel besturing wordt opgeslagen op het AKS-cluster. Het bevat de synchronisatie-ID en de tijds tempel van de laatste synchronisatie.

Er treedt een synchronisatie op wanneer de tijds tempels van de synchronisatie niet overeenkomen tussen de bestanden aan de client zijde en de controller. Tijdens een synchronisatie worden de bestands ingangen in het client-side-bestand herhaald. Als de tijds tempel van het bestand na de synchronisatie-tijds tempel ligt, wordt dat bestand gesynchroniseerd met de dev-ruimte. Zodra de synchronisatie is voltooid, worden de synchronisatie-tijds tempels bijgewerkt op zowel de bestanden aan de client zijde als aan de controller zijde.

Alle project bestanden worden gesynchroniseerd als het bestand aan de client zijde niet aanwezig is. Met dit gedrag kunt u een volledige synchronisatie afdwingen door het client-side-bestand te verwijderen.

## <a name="how-running-your-code-is-configured"></a>Hoe het uitvoeren van uw code is geconfigureerd

Azure dev Spaces maken gebruik van het `azds.yaml` bestand om uw service te installeren en te configureren. De controller gebruikt de `install` eigenschap in het `azds.yaml` bestand voor het installeren van het helm-diagram en het maken van de Kubernetes-objecten:

```yaml
...
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
      # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
      # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
      # For more information see https://aka.ms/devspaces/routing
      - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
...
```

Standaard `prep` wordt de helm-grafiek gegenereerd met de opdracht. Ook de eigenschap *install. Chart* wordt ingesteld op de map van het helm-diagram. Als u een helm-diagram wilt gebruiken op een andere locatie, kunt u deze eigenschap bijwerken om die locatie te gebruiken.

Bij de installatie van de helm-grafieken biedt Azure dev Spaces een manier om waarden in de helm-grafiek te overschrijven. De standaard waarden voor de helm-grafiek zijn in `charts/APP_NAME/values.yaml` .

Met de eigenschap *install. values* kunt u een of meer bestanden weer geven waarmee waarden worden gedefinieerd die u wilt vervangen in het helm-diagram. Als u bijvoorbeeld een hostnaam of database configuratie specifiek wilt gebruiken voor het uitvoeren van uw toepassing in een dev-ruimte, kunt u deze onderdrukkings functionaliteit. U kunt ook een *?* toevoegen aan het einde van de bestands namen om deze in te stellen als optioneel.

Met de eigenschap *install. set* kunt u een of meer waarden configureren die u wilt vervangen in het helm-diagram. Alle waarden die zijn geconfigureerd in *install. set* overschrijven de waarden die zijn geconfigureerd in de bestanden die worden vermeld in *install. values*. De eigenschappen onder *install. set* zijn afhankelijk van de waarden in het helm-diagram en kunnen verschillen, afhankelijk van het gegenereerde helm-diagram.

In het bovenstaande voor beeld vertelt de eigenschap *install. set. replicaCount* aan de controller hoeveel exemplaren van uw toepassing moeten worden uitgevoerd in uw dev-ruimte. Afhankelijk van uw scenario kunt u deze waarde verg Roten, maar dit heeft gevolgen voor het koppelen van een fout opsporingsprogramma aan de pod van uw toepassing. Zie het [artikel over probleem oplossing][troubleshooting]voor meer informatie.

In de gegenereerde helm-grafiek is de container installatie kopie ingesteld op *{{. Values. image. repository}}: {{. Values. image. tag}}*. In het bestand wordt de `azds.yaml` eigenschap *install. set. image. tag* gedefinieerd als *$ (tag)* standaard, die wordt gebruikt als de waarde voor *{{. Values. image. tag}}*. Door de eigenschap *install. set. image. tag* op deze manier in te stellen, kan de container installatie kopie voor uw toepassing op een unieke manier worden gelabeld wanneer Azure-ontwikkel ruimten worden uitgevoerd. In dit specifieke geval wordt de afbeelding gelabeld als *\<value from image.repository> : $ (tag)*. U moet de variabele *$ (tag)* gebruiken als de waarde   *install. set. image. tag* , zodat ontwikkel ruimten de container herkennen en vinden in het AKS-cluster.

In het bovenstaande voor beeld `azds.yaml` definieert u *install. set. ingress. hosts*. De eigenschap *install. set. ingress. hosts* definieert een indeling voor de hostnaam voor open bare eind punten. Deze eigenschap gebruikt ook *$ (spacePrefix)*, *$ (rootSpacePrefix)* en *$ (hostSuffix)*. Dit zijn de waarden die door de controller worden verschaft.

*$ (SpacePrefix)* is de naam van de onderliggende dev-ruimte, waarbij de notatie Space naam *. s* wordt gebruikt. *$ (RootSpacePrefix)* is de naam van de bovenliggende ruimte. Als *azureuser* bijvoorbeeld een onderliggend gebied *is, is* de waarde voor *$ (rootSpacePrefix)* *standaard* en is de waarde van *$ (spacePrefix)* *azureuser. s*. Als de ruimte geen onderliggende ruimte is, *$ (spacePrefix)* is leeg. Als de *standaard* ruimte bijvoorbeeld geen bovenliggende ruimte heeft, is de waarde voor *$ (rootSpacePrefix)* *standaard* en is de waarde van *$ (spacePrefix)* leeg. *$ (HostSuffix)* is een DNS-achtervoegsel dat verwijst naar de Azure dev Spaces-controller die wordt uitgevoerd in uw AKS-cluster. Dit DNS-achtervoegsel komt bijvoorbeeld overeen met een DNS-vermelding met Joker tekens *\* . RANDOM_VALUE. Eus. azds. io*, dat is gemaakt toen de Azure dev Spaces-controller werd toegevoegd aan uw AKS-cluster.

In het bovenstaande `azds.yaml` bestand kunt u ook *install. set. ingress. hosts* bijwerken om de hostnaam van uw toepassing te wijzigen. Bijvoorbeeld, als u de hostnaam van uw toepassing wilt vereenvoudigen van *$ (spacePrefix) $ (rootSpacePrefix) webfrontend $ (hostSuffix)* naar $ ( *spacePrefix) $ (rootSpacePrefix) Web $ (hostSuffix*).

De controller maakt gebruik van de volgende secties van het configuratie bestand om de container voor uw toepassing te maken `azds.yaml` :

```yaml
build:
  context: .
  dockerfile: Dockerfile
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
...
```

De controller gebruikt een Dockerfile om uw toepassing te bouwen en uit te voeren.

De eigenschap *Build. context* bevat een lijst met de map waarin de Dockerfiles bestaat. De eigenschap kerfile van de *build.doc* definieert de naam van de Dockerfile voor het bouwen van de productie versie van de toepassing. Met de eigenschap *configurations.develop.build.dockerfile* wordt de naam van de Dockerfile voor de ontwikkelings versie van de toepassing geconfigureerd.

Met verschillende Dockerfiles voor ontwikkeling en productie kunt u bepaalde dingen tijdens de ontwikkeling inschakelen en deze items uitschakelen voor productie-implementaties. U kunt bijvoorbeeld fout opsporing of uitgebreide logboek registratie inschakelen tijdens de ontwikkeling en uitschakelen in een productie omgeving. U kunt deze eigenschappen ook bijwerken als uw Dockerfiles een andere naam hebben of zich op een andere locatie bevinden.

Om u te helpen snel tijdens het ontwikkelen te herhalen, zullen Azure dev Spaces wijzigingen van uw lokale project synchroniseren en uw toepassing incrementeel bijwerken. De onderstaande sectie in het `azds.yaml` configuratie bestand wordt gebruikt voor het configureren van de synchronisatie en de update:

```yaml
...
configurations:
  develop:
    ...
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
...
```

De bestanden en mappen waarmee wijzigingen worden gesynchroniseerd, worden weer gegeven in de eigenschap *configuraties. ontwikkeling. container. Sync* . Deze directory's worden in eerste instantie gesynchroniseerd wanneer u de `up` opdracht uitvoert en wanneer er wijzigingen worden gedetecteerd. Als er extra of andere directory's zijn die u wilt synchroniseren met uw dev-ruimte, kunt u deze eigenschap wijzigen.

De eigenschap *configurations. develope. container. ITER. buildCommands* geeft aan hoe de toepassing in een ontwikkelings scenario moet worden gebouwd. De eigenschap *configurations. Development. container. Command* biedt de opdracht voor het uitvoeren van de toepassing in een ontwikkelings scenario. Het is raadzaam om een van deze eigenschappen bij te werken als er aanvullende build-of runtime-vlaggen of-para meters zijn die u tijdens de ontwikkeling wilt gebruiken.

De *configuraties. develope. container. ITER. processesToKill* geeft een lijst van de processen die u wilt beëindigen om de toepassing te stoppen. U kunt deze eigenschap bijwerken als u tijdens de ontwikkeling het gedrag voor opnieuw opstarten van uw toepassing wilt wijzigen. Als u bijvoorbeeld de configuraties hebt bijgewerkt *. ontwikkelen. container. ITER. buildCommands* of configurations. de eigenschappen van de *opdracht develope. container. Command* om te wijzigen hoe de toepassing wordt gebouwd of gestart, moet u mogelijk wijzigen welke processen worden gestopt.

Wanneer u de code voorbereidt met behulp `azds prep` van de opdracht, kunt u de `--enable-ingress` markering toevoegen. Als u de vlag toevoegt, `--enable-ingress` maakt u een openbaar toegankelijke URL voor uw toepassing. Als u deze vlag weglaat, is de toepassing alleen toegankelijk binnen het cluster of met behulp van de localhost-tunnel. Nadat u de opdracht hebt uitgevoerd `azds prep` , kunt u deze instelling wijzigen van de eigenschap binnenkomend *. enabled* in `charts/APPNAME/values.yaml` :

```yaml
ingress:
  enabled: true
```

## <a name="next-steps"></a>Volgende stappen

Zie [hoe route ring werkt met Azure dev Spaces][how-it-works-routing]voor meer informatie over netwerken en hoe aanvragen worden gerouteerd in azure dev Spaces.

Voor meer informatie over het snel herhalen en ontwikkelen met Kubernetes raadpleegt u [hoe Bridge to Kubernetes werkt][how-it-works-bridge-to-kubernetes] en [hoe u uw code op afstand kunt debuggen met Azure dev Spaces][how-it-works-remote-debugging].


[azds-yaml-section]: #how-running-your-code-is-configured
[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[sync-section]: #file-synchronization
[troubleshooting]: troubleshooting.md