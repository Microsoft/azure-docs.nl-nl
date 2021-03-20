---
title: Container instanties en container indeling
description: Begrijp hoe Azure container instances met container Orchestrator werken.
ms.topic: article
ms.date: 04/15/2019
ms.custom: mvc
ms.openlocfilehash: c7faeecfcc3a1d006cc923b48339a1242a0b9e9b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92148596"
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Azure Container Instances en container Orchestrator

Vanwege hun geringe grootte en toepassings stand zijn containers geschikt voor flexibele bezorgings omgevingen en architecturen op basis van micro Services. De taak van het automatiseren en beheren van een groot aantal containers en hoe deze *communiceren, wordt bekend als indeling*. Populaire container-Orchestrator zijn onder andere Kubernetes, DC/OS en docker Swarm.

Azure Container Instances biedt een aantal basis mogelijkheden voor het plannen van Orchestration-platforms. En hoewel het geen betrekking heeft op de services met hogere waarden die deze platformen bieden, kan Azure Container Instances complementair zijn. In dit artikel wordt het bereik beschreven van wat Azure Container Instances ingangen, en hoe de volledige container Orchestrator ermee kan werken.

## <a name="traditional-orchestration"></a>Traditionele indeling

De standaard definitie van indeling omvat de volgende taken:

- **Planning**: voor het uitvoeren van een container installatie kopie en een resource aanvraag vindt u een geschikte computer waarop de container moet worden uitgevoerd.
- **Affiniteit/anti-affiniteit**: Geef op dat een set containers naast elkaar moet worden uitgevoerd (voor prestaties) of te veel uit elkaar (voor Beschik baarheid).
- **Status controle**: controleren op container fouten en deze automatisch opnieuw plannen.
- **Failover**: Houd bij wat er op elke machine wordt uitgevoerd en plan de containers van mislukte machines opnieuw in goede knoop punten.
- **Schalen**: container instanties toevoegen of verwijderen om te voldoen aan de vraag, hetzij hand matig of automatisch.
- **Netwerken**: bieden een overlay-netwerk voor het coördineren van containers om te communiceren op meerdere hostcomputers.
- **Service detectie**: Hiermee kunnen containers automatisch worden gevonden, zelfs als ze tussen hostcomputers worden verplaatst en IP-adressen worden gewijzigd.
- **Gecoördineerde toepassings upgrades**: container upgrades beheren om uitval tijd van toepassingen te voor komen en terugdraai actie in te scha kelen als er iets fout gaat.

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Indeling met Azure Container Instances: een gelaagde benadering

Azure Container Instances maakt een gelaagde benadering van de indeling mogelijk, waarbij alle plannings-en beheer mogelijkheden zijn vereist voor het uitvoeren van één container, terwijl Orchestrator-platformen de mogelijkheid hebben om meerdere-container taken te beheren.

Omdat de onderliggende infra structuur voor container instanties wordt beheerd door Azure, hoeft een Orchestrator-platform zich niet te houden aan het vinden van een geschikte hostmachine waarop één container kan worden uitgevoerd. De elasticiteit van de cloud zorgt ervoor dat er altijd een beschikbaar is. In plaats daarvan kan de Orchestrator zich richten op de taken die de ontwikkeling van architecturen met meerdere containers vereenvoudigen, met inbegrip van schalen en gecoördineerde upgrades.

## <a name="scenarios"></a>Scenario's

Hoewel Orchestrator-integratie met Azure Container Instances nog steeds nascent is, anticiperen we dat een aantal verschillende omgevingen zich voordoen:

### <a name="orchestration-of-container-instances-exclusively"></a>Container instanties uitsluitend organiseren

Omdat deze snel worden gestart en gefactureerd op basis van de tweede, biedt een omgeving die uitsluitend is gebaseerd op Azure Container Instances de snelste manier om aan de slag te gaan en om zeer variabele workloads te verwerken.

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Combi natie van container instanties en containers in Virtual Machines

Voor langlopende, stabiele werk belastingen is het organiseren van containers in een cluster met toegewezen virtuele machines doorgaans goed koper dan het uitvoeren van dezelfde containers met Azure Container Instances. Container instanties bieden echter een fantastische oplossing om snel uit te breiden en te voldoen aan uw totale capaciteit om te kunnen omgaan met onverwachte of korte pieken in het gebruik.

In plaats van het aantal virtuele machines in uw cluster te schalen en vervolgens extra containers op deze computers te implementeren, kan de Orchestrator de extra containers in Azure Container Instances plannen en deze verwijderen wanneer ze niet meer nodig zijn.

## <a name="sample-implementation-virtual-nodes-for-azure-kubernetes-service-aks"></a>Voorbeeld implementatie: virtuele knoop punten voor Azure Kubernetes service (AKS)

Als u werk belastingen van toepassingen snel wilt schalen in een [Azure Kubernetes service](../aks/intro-kubernetes.md) -cluster (AKS), kunt u *virtuele knoop punten* die dynamisch zijn gemaakt, gebruiken in azure container instances. Virtuele knoop punten scha kelen netwerk communicatie in tussen de peulen die worden uitgevoerd in ACI en het AKS-cluster. 

Virtuele knoop punten ondersteunen momenteel Linux-container exemplaren. Ga aan de slag met virtuele knoop punten met behulp van [Azure cli](../aks/virtual-nodes-cli.md) of [Azure Portal](../aks/virtual-nodes-portal.md).

Virtuele knoop punten gebruiken het open-source [virtuele-Kubelet][aci-connector-k8s] om de Kubernetes- [Kubelet][kubelet-doc] te simuleren door te registreren als een knoop punt met onbeperkte capaciteit. De virtuele-Kubelet verzendt het maken van een [peul][pod-doc] als container groepen in azure container instances.

Bekijk het [virtuele-Kubelet](https://github.com/virtual-kubelet/virtual-kubelet) -project voor meer voor beelden van het uitbreiden van de KUBERNETES-API naar serverloze container platforms.

## <a name="next-steps"></a>Volgende stappen

Maak uw eerste container met Azure Container Instances met behulp van de [Snelstartgids](container-instances-quickstart.md).

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/azure-aci
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/