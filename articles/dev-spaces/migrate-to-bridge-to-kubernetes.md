---
title: Bridge migreren naar Kubernetes
services: azure-dev-spaces
ms.date: 10/21/2020
ms.topic: conceptual
description: Beschrijft het migratieproces van Azure Dev Spaces naar Bridge naar Kubernetes
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Bridge to Kubernetes
ms.openlocfilehash: 8ffb7693ff223a9cb952964ded1e6967ceeb326e
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499288"
---
# <a name="migrating-to-bridge-to-kubernetes"></a>Bridge migreren naar Kubernetes

> [!IMPORTANT]
> Azure Dev Spaces wordt op 31 oktober 2023 buiten gebruik genomen. Klanten moeten over naar Bridge naar Kubernetes, een hulpprogramma voor clientontwikkelaars.
>
> Het doel van Azure Dev Spaces was om gebruikers te helpen bij het ontwikkelen op Kubernetes. Een belangrijke afweging in de benadering van Azure Dev Spaces was het extra belasten van gebruikers om inzicht te krijgen in Docker- en Kubernetes-configuraties en kubernetes-implementatieconcepten. In de loop van de tijd werd ook duidelijk dat de aanpak van Azure Dev Spaces de snelheid van ontwikkeling van interne lussen in Kubernetes niet effectief heeft verkleind. Bridge to Kubernetes vermindert de snelheid van ontwikkeling van interne lussen effectief en voorkomt onnodige belasting voor gebruikers.
>
> De kernmissie blijft ongewijzigd: Bouw de beste ervaringen voor het ontwikkelen, testen en opsporen van microservicecode in de context van de grotere toepassing.

Bridge to Kubernetes biedt een lichter gewicht als alternatief voor veel ontwikkelscenario's die werken met Azure Dev Spaces. Bridge to Kubernetes is een ervaring aan de clientzijde die alleen gebruik maakt van extensies in [Visual Studio][vs]   en Visual Studio [Code][vsc].  

Bridge to Kubernetes helpt uw ontwikkelervaring door een bestaande Kubernetes-toepassing toe te staan een service op te nemen die wordt uitgevoerd op uw lokale ontwikkelwerkstation. In tegenstelling tot Dev Spaces vermindert Bridge to Kubernetes de complexiteit van interne lussen door de noodzaak om Docker- en Kubernetes-configuraties te maken naast elkaar, zodat ontwikkelaars zich alleen kunnen richten op de bedrijfslogica van hun microservicecode.

Bridge to Kubernetes helpt u bij het itereren van code die wordt uitgevoerd op uw ontwikkelcomputer terwijl u afhankelijkheden en bestaande configuratie van uw Kubernetes-omgeving verbruikt. Azure Dev Spaces implementeert daarentegen uw microservice in de Kubernetes-omgeving voordat u op afstand fouten in uw service kunt opsporen en uw code kunt itereren.

Dit artikel bevat een vergelijking tussen Azure Dev Spaces en Bridge to Kubernetes, evenals instructies voor het migreren van Azure Dev Spaces naar Bridge naar Kubernetes.

## <a name="development-approaches"></a>Ontwikkelingsmethoden

![Ontwikkelingsmethoden](media/migrate-to-btk/development-approaches.svg)

Azure Dev Spaces heeft Kubernetes-ontwikkelaars geholpen met het werken met code die rechtstreeks in hun AKS-cluster wordt uitgevoerd, waardoor er geen lokale omgeving nodig is die niet lijkt op de geïmplementeerde omgeving. Met deze aanpak zijn bepaalde aspecten van ontwikkeling verbeterd, maar er is ook een vereiste geïntroduceerd voor het leren en onderhouden van aanvullende concepten, zoals Docker, Kubernetes en Helm, voordat u Azure Dev Spaces kunt gaan gebruiken.

Dankzij Bridge to Kubernetes kunnen ontwikkelaars rechtstreeks op hun ontwikkelcomputers werken tijdens interactie met de rest van hun cluster. Deze aanpak maakt gebruik van de bekendheid en snelheid van het rechtstreeks uitvoeren van code op hun ontwikkelcomputers terwijl de afhankelijkheden en omgeving van hun cluster worden gedeeld. Deze aanpak maakt ook gebruik van de betrouwbaarheid en schaalbaarheid die het gebruik van Kubernetes met zich mee brengt.

## <a name="feature-comparison"></a>Functievergelijking

Azure Dev Spaces en Bridge to Kubernetes hebben vergelijkbare functies en verschillen ook op verschillende gebieden:

| Vereiste  | Azure Dev Spaces  | Bridge to Kubernetes  |
|---------------|-------------------|--------------------------------|
| Azure Kubernetes Service | In 15 Azure-regio's | Een AKS-serviceregio    |
| **Beveiliging** |
| Toegang tot de beveiliging die nodig is in uw cluster  | AKS-clusterbijdrager  | Kubernetes RBAC - Implementatie-update   |
| Beveiligingstoegang nodig op uw ontwikkelcomputer  | N.v.t.  | Lokale beheerder /sudo   |
| **Bruikbaarheid** |
| Onafhankelijk van Kubernetes- en Docker-artefacten  | Nee  | Ja   |
| Automatisch terugdraaien van wijzigingen, na foutopsporing  | Nee  | Ja   |
| **Ondersteunde clienthulpprogramma's** |
| Werkt met Visual Studio 2019  | Ja  | Ja   |
| Werkt met Visual Studio Code  | Ja  | Ja   |
| Werkt met een CLI  | Ja  | Nee   |
| **Compatibiliteit van besturingssystemen** |
| Werkt op Windows 10  | Ja  | Ja  |
| Werkt op Linux  | Ja  | Ja  |
| Werkt op macOS  | Ja  | Ja  |
| **Functies** |
| Isolatie van ontwikkelaars of teamontwikkeling  | Ja  | Ja  |
| Omgevingsvariabelen selectief overschrijven  | Nee  | Ja  |
| Dockerfile- en Helm-grafiek maken  | Ja  | Nee  |
| Permanente implementatie van code in Kubernetes  | Ja  | Nee  |
| Externe debugging in een Kubernetes-pod  | Ja  | Nee  |
| Lokale debugging, verbonden met Kubernetes  | Nee  | Ja  |
| Meerdere services tegelijkertijd op hetzelfde werkstation debuggen  | Ja  | Ja  |

## <a name="kubernetes-inner-loop-development"></a>Ontwikkeling van interne Kubernetes-lus

Het grootste verschil tussen Azure Dev Spaces en Bridge to Kubernetes is waar uw code wordt uitgevoerd. Azure Dev Spaces helpt bij het ontwikkelen en opsporen van fouten in uw microservicecode, maar vereist dat u die code in uw cluster kunt uitvoeren. Met Bridge to Kubernetes kunt u uw microservicecode rechtstreeks op uw ontwikkelcomputer ontwikkelen en er fouten in opsporen, terwijl u nog steeds in de context van de grotere toepassing die wordt uitgevoerd in Kubernetes. Bridge to Kubernetes breidt de perimeter van het Kubernetes-cluster uit en staat lokale processen toe configuratie over te nemen van Kubernetes.

![Ontwikkeling van interne lus](media/migrate-to-btk/btk-graphic-non-isolated.gif)

Wanneer u Bridge to Kubernetes gebruikt, wordt er een netwerkverbinding tot stand gebracht tussen uw ontwikkelcomputer en uw cluster.Tijdens de levensduur van deze verbinding wordt er een proxy toegevoegd aan uw cluster in plaats van uw Kubernetes-implementatie die aanvragen naar de service omleiden naar uw ontwikkelcomputer. Wanneer u de verbinding verbreekt, wordt de implementatie van de toepassing teruggekoppeld naar de oorspronkelijke versie van de implementatie die op het cluster wordt uitgevoerd. Deze aanpak wijkt af van de manier waarop Azure Dev Spaces werkt, waarbij code wordt gesynchroniseerd met het cluster, wordt gebouwd en vervolgens wordt uitgevoerd. Azure Dev Spaces kan uw code ook niet terugdraaien.

Bridge to Kubernetes biedt de flexibiliteit om te werken met toepassingen die worden uitgevoerd in Kubernetes, ongeacht hun implementatiemethode. Als u CI/CD gebruikt om uw toepassing te bouwen en uit te voeren, ongeacht of u bestaande hulpprogramma's of aangepaste scripts gebruikt, kunt u nog steeds Bridge to Kubernetes gebruiken om uw code te ontwikkelen en fouten op te sporen.

> [!TIP]
> Met [de Microsoft Kubernetes-extensie][kubernetes-extension] kunt u snel Kubernetes-manifesten ontwikkelen met IntelliSense en Helm-grafieken maken.  

### <a name="transition-to-bridge-to-kubernetes-from-azure-dev-spaces"></a>Overgang van Azure Dev Spaces naar Bridge naar Kubernetes

1. Als u Visual Studio gebruikt, moet u uw Visual Studio IDE bijwerken naar versie 16.7 of hoger en de extensie Bridge to Kubernetes installeren vanuit [de Visual Studio Marketplace.][vs-marketplace] Als u code voor Visual Studio gebruikt, installeert u de [extensie Bridge to Kubernetes][vsc-marketplace].
1. Schakel de Azure Dev Spaces-controller uit met behulp van de Azure Portal of de [Azure Dev Spaces CLI][azds-delete].
1. Gebruik [Azure Cloud Shell](https://shell.azure.com). Of open op Mac, Linux of Windows met bash geïnstalleerd een bash-shellprompt. Zorg ervoor dat de volgende hulpprogramma's beschikbaar zijn in uw opdrachtregelomgeving: Azure CLI, docker, kubectl, curl, tar en gunzip.
1. Maak een containerregister of gebruik een bestaand containerregister. U kunt een containerregister maken in Azure met behulp [van Azure Container Registry](https://azure.microsoft.com/services/container-registry/) of met behulp [van Docker Hub](https://hub.docker.com/). Wanneer u Azure Cloud Shell, is alleen Azure Container Registry beschikbaar voor het hosten van Docker-afbeeldingen.
1. Voer het migratiescript uit om Azure Dev Spaces-assets te converteren naar Bridge naar Kubernetes-assets. Het script bouwt een nieuwe afbeelding die compatibel is met Bridge to Kubernetes, uploadt deze naar het aangewezen register en gebruikt [Helm](https://helm.sh) om het cluster bij te werken met de -afbeelding. U moet de resourcegroep, de naam van het AKS-cluster en een containerregister verstrekken. Er zijn andere opdrachtregelopties zoals hier wordt weergegeven:

   ```azure-cli
   curl -sL https://aka.ms/migrate-tool | bash -s -- -g ResourceGroupName -n AKSName -h ContainerRegistryName -r PathOfTheProject -y
   ```

   Het script ondersteunt de volgende vlaggen:

   ```cmd  
    -g Name of resource group of AKS Cluster [required]
    -n Name of AKS Cluster [required]
    -h Container registry name. Examples: ACR, Docker [required]
    -k Kubernetes namespace to deploy resources (uses 'default' otherwise)
    -r Path to root of the project that needs to be migrated (default = current working directory)
    -t Image name & tag in format 'name:tag' (default is 'projectName:stable')
    -i Enable a public endpoint to access your service over internet. (default is false)
    -c Docker build context path. (default = project root path passed to '-r' option)
    -y Doesn't prompt for non-tty terminals
    -d Helm Debug switch
   ```

1. Migreert handmatig eventuele aanpassingen, zoals omgevingsvariabele-instellingen, in *azds.yaml* naar het *bestand values.yml van uw* project.
1. (optioneel) Verwijder het `azds.yaml` bestand uit uw project.
1. Configureer Bridge to Kubernetes in uw geïmplementeerde toepassing. Zie Bridge to Kubernetes gebruiken in Visual Studio voor meer informatie over het gebruik van [Bridge to Kubernetes in Visual Studio][use-btk-vs]. Zie Bridge [to Kubernetes in VS Code][use-btk-vsc]gebruiken voor VS Code.
1. Start de foutopsporing met behulp van het zojuist gemaakte Profiel voor foutopsporing/starten van Bridge to Kubernetes.
1. U kunt het script zo nodig opnieuw uitvoeren om het opnieuw te kunnen uitvoeren in uw cluster.

## <a name="team-development-in-a-shared-cluster"></a>Teamontwikkeling in een gedeeld cluster

U kunt ook ontwikkelaarsspecifieke routering gebruiken met Bridge to Kubernetes. In het ontwikkelscenario van het Azure Dev Spaces-team worden meerdere Kubernetes-naamruimten gebruikt om een service te isoleren van de rest van de toepassing met behulp van het concept bovenliggende en onderliggende naamruimten. Bridge to Kubernetes biedt dezelfde mogelijkheid, maar met verbeterde prestatiekenmerken en binnen dezelfde toepassingsnaamruimte.

Zowel Bridge to Kubernetes als Azure Dev Spaces vereist dat HTTP-headers aanwezig zijn en worden doorgegeven in de toepassing. Als u uw toepassing al hebt geconfigureerd voor het afhandelen van header-door propagatie voor Azure Dev Spaces, moet de header worden bijgewerkt. Als u vanuit Azure Dev Spaces wilt overstappen op Bridge naar Kubernetes, moet u de geconfigureerde header bijwerken van *azds-route-as* naar *kubernetes-route-as.*

## <a name="evaluate-bridge-to-kubernetes"></a>Bridge naar Kubernetes evalueren

Als u wilt experimenteren met Bridge to Kubernetes voordat u Azure Dev Spaces in uw cluster uit schakelen, is de eenvoudigste manier om een nieuw cluster te gebruiken. Als u op hetzelfde moment Azure Dev Spaces en Bridge to Kubernetes op hetzelfde cluster probeert te gebruiken, hebt u problemen wanneer u de routeringsfuncties op beide gebruikt.

### <a name="use-visual-studio-to-evaluate-bridge-to-kubernetes"></a>Gebruik Visual Studio om Bridge to Kubernetes te evalueren

1. Werk uw Visual Studio IDE bij naar versie 16.7 of hoger en installeer de Bridge to Kubernetes-extensie vanuit [Visual Studio Marketplace.][vs-marketplace]
1. Maak een nieuw AKS-cluster en implementeer uw toepassing. U kunt ook een [voorbeeldtoepassing gebruiken.][btk-sample-app]
1. Configureer Bridge to Kubernetes in uw geïmplementeerde toepassing. Zie Bridge to [Kubernetes][use-btk-vs]gebruiken voor meer informatie over het gebruik van Bridge to Kubernetes in Visual Studio.
1. Start de foutopsporing in Visual Studio met behulp van het zojuist gemaakte foutopsporingsprofiel Bridge to Kubernetes.

### <a name="use-visual-studio-code-to-evaluate-bridge-to-kubernetes"></a>Gebruik Visual Studio Code om Bridge to Kubernetes te evalueren

1. Installeer de [extensie Bridge to Kubernetes][vsc-marketplace].
1. Maak een nieuw AKS-cluster en implementeer uw toepassing. U kunt ook een [voorbeeldtoepassing gebruiken.][btk-sample-app]
1. Configureer Bridge to Kubernetes in uw geïmplementeerde toepassing. Zie Bridge to [Kubernetes][use-btk-vsc]gebruiken voor meer informatie over het gebruik van Bridge to Kubernetes in Visual Studio Code.
1. Begin met debuggen in Visual Studio het zojuist gemaakte startprofiel Bridge to Kubernetes te gebruiken.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe Bridge to Kubernetes werkt.

> [!div class="nextstepaction"]
> [Hoe Bridge to Kubernetes werkt][how-it-works-bridge-to-kubernetes]


[kubernetes-extension]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-kubernetes-tools
[btk-sample-app]: /visualstudio/containers/bridge-to-kubernetes#install-the-sample-application
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[use-btk-vs]: /visualstudio/containers/bridge-to-kubernetes#connect-to-your-cluster-and-debug-a-service
[use-btk-vsc]: https://code.visualstudio.com/docs/containers/bridge-to-kubernetes
[vs]: https://visualstudio.microsoft.com/
[vsc-marketplace]: https://marketplace.visualstudio.com/items?itemName=mindaro.mindaro
[vs-marketplace]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.mindaro
[vsc]: https://code.visualstudio.com/
