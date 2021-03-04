---
title: CI/CD-werk stroom met GitOps-Azure Arc enabled Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: conceptual
author: tcare
ms.author: tcare
description: Dit artikel bevat een conceptueel overzicht van een CI/CD-werk stroom met behulp van GitOps
keywords: GitOps, Kubernetes, K8s, azure, helm, Arc, AKS, Azure Kubernetes service, containers, CI, CD, Azure DevOps
ms.openlocfilehash: a51a9f2b32f1088cec390dc4d74300a38f37b160
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121776"
---
# <a name="cicd-workflow-using-gitops---azure-arc-enabled-kubernetes"></a>CI/CD-werk stroom met GitOps-Azure Arc enabled Kubernetes

Moderne Kubernetes-implementaties zijn meerdere toepassingen, clusters en omgevingen. Met GitOps kunt u deze complexe instellingen eenvoudiger beheren, waarbij u de gewenste status van de Kubernetes-omgevingen declaratief met git bijhoudt. Met het gebruik van common Git-hulp middelen voor het bijhouden van de cluster status kunt u de verantwoordelijkheid verhogen, het fout onderzoek vergemakkelijken en automatisering inschakelen om omgevingen te beheren.

In dit conceptuele overzicht wordt GitOps uitgelegd als een realiteit van de volledige levens cyclus van de toepassing met Azure Arc, Azure opslag plaatsen en Azure pipelines. [Ga naar een voor beeld](#example-workflow) van een enkele toepassings wijziging in GitOps Kubernetes omgevingen.

## <a name="architecture"></a>Architectuur

Houd rekening met een toepassing die is geïmplementeerd in een of meer Kubernetes-omgevingen.

![GitOps CI/CD-architectuur](./media/gitops-arch.png)

### <a name="application-repo"></a>Opslag plaats van toepassing
De opslag plaats van de toepassing bevat de toepassings code die ontwikkel aars tijdens hun interne lus gebruiken. De implementatie sjablonen van de toepassing zijn Live in deze opslag plaats in een algemeen formulier, zoals helm of Kustomize. Omgeving-specifieke waarden worden niet opgeslagen. Wijzigingen in deze opslag plaats roepen een pull-of CI-pijp lijn op waarmee het implementatie proces wordt gestart.
### <a name="container-registry"></a>Container Registry
Het container register bevat alle afbeeldingen van de eerste en derde partij die worden gebruikt in de Kubernetes-omgevingen. Codeer installatie kopieën van toepassingen met menselijke Lees bare Tags en de Git-door Voer die wordt gebruikt om de installatie kopie te bouwen. Cache kopieën van derden voor beveiliging, snelheid en flexibiliteit. Stel een plan in voor tijdige testen en integratie van beveiligings updates. Zie voor meer informatie de hand leiding [ACR verbruik en onderhoud van open bare inhoud](https://docs.microsoft.com/azure/container-registry/tasks-consume-public-content) voor een voor beeld.
### <a name="pr-pipeline"></a>PR-pijp lijn
Pull aan de toepassings opslag plaats worden gegatedeerd op een geslaagde uitvoering van de PR-pijp lijn. Deze pijp lijn voert de basis kwaliteits poorten uit, zoals linting en eenheids tests voor de toepassings code. De pijp lijn test de toepassing en de Dockerfiles-en helm-sjablonen die worden gebruikt voor implementatie naar een Kubernetes-omgeving. Docker-installatie kopieën moeten worden gebouwd en getest, maar niet gepusht. Behoud de duur van de pijp lijn relatief kort om snelle herhalingen mogelijk te maken.
### <a name="ci-pipeline"></a>CI-pijp lijn
De Application CI-pijp lijn voert alle stappen uit en breidt de test-en implementatie controles uit. De pijp lijn kan worden uitgevoerd voor elke door Voer of op een regel matige uitgebracht met een groep door voeren. In deze fase voert u toepassings tests uit die te lang zijn voor een PR-pijp lijn. Push docker-installatie kopieën naar de Container Registry na het maken van de voor bereiding voor de implementatie. De vervangen sjabloon kan worden gelintd met een set test waarden. Installatie kopieën die worden gebruikt bij service runtime moeten op dit moment worden geconstrueerd, gebouwd en getest. In de CI-build worden artefacten gepubliceerd voor de CD-stap om te gebruiken voor de implementatie.
### <a name="flux"></a>Flux
Stroom is een service die in elk cluster wordt uitgevoerd en die verantwoordelijk is voor het onderhouden van de gewenste status. De service pollt regel matig de GitOps-opslag plaats voor wijzigingen in het cluster en past deze toe.
### <a name="cd-pipeline"></a>CD-pijp lijn
De CD-pijp lijn wordt automatisch geactiveerd door succes volle CI-builds. De eerder gepubliceerde sjablonen worden gebruikt, de omgevings waarden worden vervangen en er wordt een PR-waarde voor de GitOps opslag plaats om een wijziging aan te vragen voor de gewenste status van een of meer Kubernetes-clusters. Cluster beheerders controleren de status wijzigings PR en goed keuren de samen voeging naar de GitOps opslag plaats. De pijp lijn wordt vervolgens gewacht tot de PR is voltooid, waardoor de status wijziging kan worden opgehaald.
### <a name="gitops-repo"></a>GitOps opslag plaats
De GitOps opslag plaats vertegenwoordigt de huidige gewenste status van alle omgevingen in clusters. Elke wijziging in deze opslag plaats wordt door de stroom service in elk cluster opgehaald en geïmplementeerd. Pull worden gemaakt met wijzigingen in de gewenste status, gecontroleerd en samengevoegd. Deze pull bevatten wijzigingen in beide implementatie sjablonen en de resulterende gerenderde Kubernetes-manifesten. Gerenderde manifesten op laag niveau bieden een meer zorgvuldige inspectie van wijzigingen, normaal gesp roken op sjabloon niveau.
### <a name="kubernetes-clusters"></a>Kubernetes-clusters
Ten minste één Azure Arc enabled Kubernetes-clusters worden uitgevoerd op de verschillende omgevingen die nodig zijn voor de toepassing. Eén cluster kan bijvoorbeeld fungeren als een ontwikkel-en QA-omgeving via verschillende naam ruimten. Een tweede cluster kan eenvoudige schei ding van omgevingen en meer nauw keurige controle bieden.
## <a name="example-workflow"></a>Voorbeeld werk stroom
Als ontwikkelaar van de toepassing, Anne:
* Schrijft toepassings code.
* Hiermee wordt bepaald hoe de toepassing moet worden uitgevoerd in een docker-container.
* Hiermee worden de sjablonen gedefinieerd waarmee de container en afhankelijke services worden uitgevoerd in een Kubernetes-cluster.

ANNEER weet dat de toepassing de mogelijkheid moet hebben om in meerdere omgevingen te worden uitgevoerd, maar niet de specifieke instellingen voor elke omgeving.

Stel dat Anja een wijziging van de toepassing wil aanbrengen waarmee de docker-installatie kopie wordt gewijzigd die wordt gebruikt in de sjabloon voor toepassings implementatie.

1. Anne wijzigt de implementatie sjabloon, duwt deze naar een externe vertakking op de toepassing opslag plaats en opent een PR voor controle.
2. Anja vraagt haar team om de wijziging te controleren.
    * De PR-pijp lijn voert validatie uit.
    * Nadat een geslaagde pijp lijn is uitgevoerd, wordt het team afgemeld en wordt de wijziging samengevoegd.
3. De CI-pijp lijn valideert de wijziging van Alice en is voltooid.
    * De wijziging is veilig in het cluster te implementeren en de artefacten worden opgeslagen in de CI-pijplijn uitvoering.
4. Wijzigingen in de CD-pijp lijn worden samengevoegd en geactiveerd.
    * De CD-pijp lijn neemt de artefacten op die zijn opgeslagen door de CI-pipeline-uitvoering van Alice.
    * De CD-pijp lijn vervangt de sjablonen door omgevings specifieke waarden en brengt wijzigingen in de bestaande cluster status aan in de GitOps-opslag plaats.
    * De CD-pijp lijn maakt een PR-GitOps-opslag plaats met de gewenste wijzigingen in de cluster status.
5. Het team van Alice bekijkt en keurt haar PR goed.
    * De wijziging wordt samengevoegd in de doel vertakking die overeenkomt met de omgeving.
6. Binnen enkele minuten merkt de stroom een wijziging in de GitOps-opslag plaats en haalt hij de wijziging van Alice op.
    * Vanwege de wijziging van de docker-installatie kopie, is voor de toepassing pod een update vereist.
    * De wijziging wordt toegepast op het cluster.
7. Lisa test het eind punt van de toepassing om te controleren of de implementatie is voltooid.
   > [!NOTE]
   > Voor meer omgevingen die zijn gericht op implementatie, wordt door de CD-pipeline herhaald door een PR te maken voor de volgende omgeving en de stappen 4-7 te herhalen. Het proces heeft veel behoefte aan extra goed keuring voor riskier-implementaties of-omgevingen, zoals een beveiligings wijziging of een productie omgeving.
8.  Zodra alle omgevingen geslaagde implementaties hebben ontvangen, is de pijp lijn voltooid.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het maken van verbindingen tussen uw cluster en een Git-opslag plaats als een [configuratie bron waarbij Azure Arc is ingeschakeld Kubernetes](./conceptual-configurations.md)
