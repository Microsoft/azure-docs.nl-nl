---
title: Bridge migreren naar Kubernetes
services: azure-dev-spaces
ms.date: 10/21/2020
ms.topic: conceptual
description: Beschrijft het migratie proces van Azure dev Spaces om te Bridgepen naar Kubernetes
keywords: Azure dev Spaces, dev Spaces, docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, Bridge to Kubernetes
ms.openlocfilehash: d48814df30c17f9b51d8642efa0960a26bbd24f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94888518"
---
# <a name="migrating-to-bridge-to-kubernetes"></a>Bridge migreren naar Kubernetes

> [!IMPORTANT]
> Azure dev Spaces worden op 31 oktober 2023 ingetrokken. Klanten moeten overstappen op het gebruik van Bridge naar Kubernetes, een hulp programma voor ontwikkel aars van clients.
>
> Het doel van Azure dev Spaces is het versnellen van gebruikers in het ontwikkelen op Kubernetes. Een aanzienlijke afweging van de aanpak van Azure dev Spaces is een extra last te stellen voor gebruikers om te begrijpen van docker-en Kubernetes-configuraties en Kubernetes-implementatie concepten. In de loop van de tijd werd ook duidelijk dat de aanpak van Azure dev Spaces de snelheid van de interne herhalings ontwikkeling op Kubernetes niet effectief vermindert. Bridge to Kubernetes verkleint de snelheid van de interne loop-ontwikkeling en vermijdt onnodig last voor gebruikers.
>
> De kern missie blijft ongewijzigd: bouw de beste ervaring voor het ontwikkelen, testen en fout opsporing van micro service-code in de context van de grotere toepassing.

Bridge to Kubernetes biedt een lichtere gewicht alternatief voor veel van de ontwikkelings scenario's die werken met Azure dev Spaces. Bridge to Kubernetes is een client-side ervaring met het gebruik van extensies in [Visual Studio][vs]   en [Visual Studio code][vsc].  

Bridge to Kubernetes helpt u bij het ontwikkelen van ervaring door een bestaande Kubernetes-toepassing toe te staan een service die wordt uitgevoerd op uw lokale ontwikkel werkstation. In tegens telling tot ontwikkel ruimten verlaagt Bridge to Kubernetes het aantal ingewikkelde lussen met behulp van gelijktijdige uitvoering van de nood zaak om docker-en Kubernetes-configuraties te maken, waardoor ontwikkel aars zich kunnen richten op de bedrijfs logica van hun micro service-code.

Bridge to Kubernetes helpt u bij het herhalen van code die wordt uitgevoerd op uw ontwikkel computer en het gebruik van afhankelijkheden en de bestaande configuratie van uw Kubernetes-omgeving. Azure dev Spaces implementeert daarentegen uw micro service in de Kubernetes-omgeving voordat u een externe fout opsporing kunt uitvoeren op uw service en de code herhaalt.

In dit artikel vindt u een vergelijking tussen Azure dev Spaces en Bridge to Kubernetes, evenals instructies voor het migreren van Azure dev Spaces naar Kubernetes.

## <a name="development-approaches"></a>Ontwikkelings benaderingen

![Ontwikkelings benaderingen](media/migrate-to-btk/development-approaches.svg)

Azure dev Spaces heeft Kubernetes-ontwikkel aars geholpen met code die rechtstreeks in hun AKS-cluster wordt uitgevoerd, zodat er geen lokale omgeving nodig is die niet lijkt op de geïmplementeerde omgeving. Met deze aanpak worden bepaalde aspecten van de ontwikkeling verbeterd, maar er is ook een vereiste voor het leren en onderhouden van aanvullende concepten, zoals docker, Kubernetes en helm, voordat u Azure dev Spaces kunt gaan gebruiken.

Bridge to Kubernetes stelt ontwikkel aars in staat om rechtstreeks op hun ontwikkel computers te werken terwijl ze met de rest van hun cluster communiceren. Deze aanpak maakt gebruik van de bekendheid en snelheid van het rechtstreeks uitvoeren van code op hun ontwikkel computers en het delen van de afhankelijkheden en de omgeving van het cluster. Deze benadering maakt ook gebruik van de betrouw baarheid en schaal baarheid die wordt uitgevoerd in Kubernetes.

## <a name="feature-comparison"></a>Vergelijking van functies

Azure dev Spaces en Bridge to Kubernetes hebben vergelijk bare functies, maar ze verschillen ook op verschillende gebieden:

| Vereiste  | Azure Dev Spaces  | Bridge to Kubernetes  |
|---------------|-------------------|--------------------------------|
| Azure Kubernetes Service | In 15 Azure-regio's | Een AKS-service regio    |
| **Beveiliging** |
| Benodigde beveiligings toegang voor uw cluster  | Inzender voor AKS-clusters  | Kubernetes RBAC-implementatie-update   |
| Beveiligings toegang vereist op uw ontwikkel computer  | N.v.t.  | Lokale beheerder-sudo   |
| **Betrouwbaarheid** |
| Onafhankelijk van Kubernetes en docker-artefacten  | Nee  | Ja   |
| Automatisch terugdraaien van wijzigingen, na fout opsporing  | Nee  | Ja   |
| **Ondersteunde client Hulpprogramma's** |
| Werkt met Visual Studio 2019  | Ja  | Ja   |
| Werkt met Visual Studio code  | Ja  | Ja   |
| Werkt met een CLI  | Ja  | Nee   |
| **Compatibiliteit van besturings systeem** |
| Werkt op Windows 10  | Ja  | Ja  |
| Werkt op Linux  | Ja  | Ja  |
| Werkt op macOS  | Ja  | Ja  |
| **Functies** |
| Isolatie van ontwikkel aars of team ontwikkeling  | Ja  | Ja  |
| Omgevings variabelen selectief overschrijven  | Nee  | Ja  |
| Dockerfile-en helm-grafiek maken  | Ja  | Nee  |
| Permanente implementatie van code naar Kubernetes  | Ja  | Nee  |
| Fout opsporing op afstand in een Kubernetes-pod  | Ja  | Nee  |
| Lokale fout opsporing, verbonden met Kubernetes  | Nee  | Ja  |
| Fout opsporing op meerdere services op hetzelfde moment op hetzelfde werk station  | Ja  | Ja  |

## <a name="kubernetes-inner-loop-development"></a>Kubernetes binnenste lus ontwikkelen

Het grootste verschil tussen Azure dev Spaces en Bridge to Kubernetes is waar uw code wordt uitgevoerd. Met Azure dev Spaces kunt u uw micro service-code ontwikkelen en fouten opsporen, maar hiervoor moet u de code in uw cluster uitvoeren. Bridge to Kubernetes biedt u de mogelijkheid om uw micro service-code rechtstreeks op uw ontwikkel computer te ontwikkelen en fouten op te sporen terwijl u nog steeds in de context van de grotere toepassing die in Kubernetes wordt uitgevoerd. Bridge to Kubernetes breidt de omtrek van het Kubernetes-cluster uit en zorgt ervoor dat lokale processen configuratie kunnen overnemen van Kubernetes.

![Interne loop-ontwikkeling](media/migrate-to-btk/btk-graphic-non-isolated.gif)

Wanneer u Bridge gebruikt voor Kubernetes, wordt er een netwerk verbinding tot stand gebracht tussen uw ontwikkel computer en uw cluster.Voor de levens duur van deze verbinding wordt een proxy toegevoegd aan uw cluster in plaats van uw Kubernetes-implementatie waarmee aanvragen naar de service worden omgeleid naar uw ontwikkel computer. Wanneer u de verbinding verbreekt, wordt de implementatie van de toepassing teruggezet naar de oorspronkelijke versie van de implementatie die op het cluster wordt uitgevoerd. Deze benadering wijkt af van de werking van Azure dev Spaces, waarmee code wordt gesynchroniseerd met het cluster, gebouwd en uitgevoerd. Met Azure dev Spaces wordt uw code ook niet teruggezet.

Bridge to Kubernetes heeft de flexibiliteit om te werken met toepassingen die worden uitgevoerd in Kubernetes, ongeacht hun implementatie methode. Als u CI/CD gebruikt voor het bouwen en uitvoeren van uw toepassing, ongeacht of u bestaande hulpprogram ma's of aangepaste scripts gebruikt, kunt u nog steeds Bridge gebruiken om Kubernetes te ontwikkelen en fouten in uw code op te sporen.

> [!TIP]
> Met de [micro soft Kubernetes-extensie][kubernetes-extension] kunt u snel Kubernetes-manifesten ontwikkelen met IntelliSense en helm-grafieken ontwerpen.  

### <a name="transition-to-bridge-to-kubernetes-from-azure-dev-spaces"></a>Overgang naar brug naar Kubernetes vanuit Azure dev Spaces

1. Als u Visual Studio gebruikt, werkt u uw Visual Studio IDE bij naar versie 16,7 of hoger en installeert u de Bridge in de Kubernetes-extensie van de [Visual Studio Marketplace][vs-marketplace]. Als u Visual Studio code gebruikt, installeert u de [Bridge in de Kubernetes-extensie][vsc-marketplace].
1. Schakel de Azure dev Spaces-controller uit met behulp van de Azure Portal of de [Azure dev Spaces-cli][azds-delete].
1. Gebruik [Azure Cloud shell](https://shell.azure.com). Of op Mac, Linux of Windows waarop bash is geïnstalleerd, opent u een bash shell-prompt. Zorg ervoor dat de volgende hulpprogram ma's beschikbaar zijn in uw opdracht regel omgeving: Azure CLI, docker, kubectl, krul, tar en gunzip.
1. Een container register maken of een bestaand REGI ster gebruiken. U kunt een container register maken in azure met behulp van [Azure container Registry](https://azure.microsoft.com/services/container-registry/) of met behulp van [docker hub](https://hub.docker.com/). Wanneer u Azure Cloud Shell gebruikt, is alleen Azure Container Registry beschikbaar voor het hosten van docker-installatie kopieën.
1. Voer het migratie script uit om activa van Azure-ontwikkel ruimten te converteren naar Kubernetes-assets. Het script bouwt een nieuwe installatie kopie die compatibel is met Bridge naar Kubernetes, uploadt deze naar het aangewezen REGI ster en gebruikt vervolgens [helm](https://helm.sh) om het cluster bij te werken met de installatie kopie. U moet de resource groep, de naam van het AKS-cluster en een container register opgeven. Er zijn andere opdracht regel opties, zoals hier wordt weer gegeven:

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

1. Migreer hand matig aanpassingen, zoals omgevings variabele-instellingen, in *azds. yaml* naar het bestand *Values. yml* van het project.
1. Beschrijving Verwijder het `azds.yaml` bestand uit uw project.
1. Configureer de brug naar Kubernetes op uw geïmplementeerde toepassing. Zie [Bridge gebruiken voor Kubernetes in Visual Studio][use-btk-vs]voor meer informatie over het gebruik van Bridge voor Kubernetes in Visual Studio. Zie [Bridge gebruiken voor Kubernetes in VS code][use-btk-vsc]voor VS code.
1. Start fout opsporing met behulp van de zojuist gemaakte brug naar Kubernetes debug/start profile.
1. U kunt het script opnieuw uitvoeren als dat nodig is om het opnieuw te implementeren in uw cluster.

## <a name="team-development-in-a-shared-cluster"></a>Team ontwikkeling in een gedeeld cluster

U kunt ook een specifiek routerings-specifieke route ring gebruiken met Bridge naar Kubernetes. Het scenario voor het ontwikkelen van Azure dev Spaces maakt gebruik van meerdere Kubernetes-naam ruimten om een service van de rest van de toepassing te isoleren met behulp van het concept van de bovenliggende en onderliggende naam ruimten. Bridge to Kubernetes biedt dezelfde functionaliteit, maar met verbeterde prestatie kenmerken en binnen dezelfde toepassings naam ruimte.

Voor zowel de brug naar Kubernetes-als Azure-ontwikkel ruimten moeten HTTP-headers aanwezig zijn en door gegeven worden in de gehele toepassing. Als u uw toepassing al hebt geconfigureerd voor het afhandelen van header doorgifte voor Azure dev Spaces, moet de header worden bijgewerkt. Als u wilt overstappen naar een brug naar Kubernetes vanuit Azure dev Spaces, werkt u de geconfigureerde header bij van *azds-route-as* to *Kubernetes-route-as*.

## <a name="evaluate-bridge-to-kubernetes"></a>Brug evalueren naar Kubernetes

Als u wilt experimenteren met Bridge naar Kubernetes voordat u Azure dev-ruimten in uw cluster uitschakelt, is de eenvoudigste manier om een nieuw cluster te gebruiken. Als u probeert Azure dev Spaces te gebruiken en op hetzelfde moment een brug naar Kubernetes te maken, worden er problemen ondervinden bij het gebruik van de routerings functies van beide.

### <a name="use-visual-studio-to-evaluate-bridge-to-kubernetes"></a>Visual Studio gebruiken om de brug te evalueren naar Kubernetes

1. Werk uw Visual Studio IDE bij naar versie 16,7 of hoger en installeer de Bridge in de Kubernetes-extensie van [Visual Studio Marketplace][vs-marketplace].
1. Maak een nieuw AKS-cluster en implementeer uw toepassing. U kunt ook een [voorbeeld toepassing][btk-sample-app]gebruiken.
1. Configureer de brug naar Kubernetes op uw geïmplementeerde toepassing. Zie [Bridge gebruiken voor Kubernetes][use-btk-vs]voor meer informatie over het gebruik van Bridge voor Kubernetes in Visual Studio.
1. Start de fout opsporing in Visual Studio met behulp van de zojuist gemaakte brug naar het Kubernetes-profiel voor fout opsporing.

### <a name="use-visual-studio-code-to-evaluate-bridge-to-kubernetes"></a>Visual Studio code gebruiken om de brug te evalueren naar Kubernetes

1. Installeer de [Bridge in de Kubernetes-extensie][vsc-marketplace].
1. Maak een nieuw AKS-cluster en implementeer uw toepassing. U kunt ook een [voorbeeld toepassing][btk-sample-app]gebruiken.
1. Configureer de brug naar Kubernetes op uw geïmplementeerde toepassing. Zie [Bridge gebruiken voor Kubernetes][use-btk-vsc]voor meer informatie over het gebruik van Bridge voor Kubernetes in Visual Studio code.
1. Start de fout opsporing in Visual Studio met behulp van de zojuist gemaakte brug naar het Kubernetes-start profiel.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe Bridge to Kubernetes werkt.

> [!div class="nextstepaction"]
> [Hoe Bridge to Kubernetes werkt][how-it-works-bridge-to-kubernetes]


[azds-delete]: how-to/install-dev-spaces.md#remove-azure-dev-spaces-using-the-cli
[kubernetes-extension]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-kubernetes-tools
[btk-sample-app]: /visualstudio/containers/bridge-to-kubernetes#install-the-sample-application
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[use-btk-vs]: /visualstudio/containers/bridge-to-kubernetes#connect-to-your-cluster-and-debug-a-service
[use-btk-vsc]: https://code.visualstudio.com/docs/containers/bridge-to-kubernetes
[vs]: https://visualstudio.microsoft.com/
[vsc-marketplace]: https://marketplace.visualstudio.com/items?itemName=mindaro.mindaro
[vs-marketplace]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.mindaro
[vsc]: https://code.visualstudio.com/
