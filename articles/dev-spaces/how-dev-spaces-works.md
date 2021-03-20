---
title: Hoe Azure Dev Spaces werkt
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Hierin worden de processen beschreven die Power Azure dev Spaces
keywords: Azure dev Spaces, dev Spaces, docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: 8de2c27ce03c871e60b6437656ad630fc8de8408
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91963700"
---
# <a name="how-azure-dev-spaces-works"></a>Hoe Azure Dev Spaces werkt

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Het ontwikkelen van een Kubernetes-toepassing kan lastig zijn. U hebt configuratie bestanden voor docker en Kubernetes nodig. U moet nagaan hoe u uw toepassing lokaal kunt testen en interactie met andere afhankelijke services. U moet mogelijk meerdere services tegelijk en met een team van ontwikkel aars verwerken en testen.

Met Azure dev Spaces kunt u op verschillende manieren snel en probleem oplossing voor Kubernetes-toepassingen en samen werken met uw team. In dit artikel wordt beschreven wat Azure dev Spaces kan doen en hoe het werkt.

## <a name="rapidly-iterate-and-debug-your-kubernetes-application"></a>Uw Kubernetes-toepassing snel herhalen en fouten opsporen

Azure dev Spaces vermindert de inspanningen voor het ontwikkelen, testen en herhalen van uw Kubernetes-toepassing in de context van uw AKS-cluster. Met deze reductie in inspanningen kunnen ontwikkel aars zich richten op de bedrijfs logica van hun toepassingen en hun services niet configureren om te worden uitgevoerd in Kubernetes.

### <a name="bridge-to-kubernetes"></a>Bridge to Kubernetes

Met Bridge to Kubernetes kunt u uw ontwikkel computer verbinden met uw Kubernetes-cluster, zodat u code op uw ontwikkel computer uitvoert, alsof deze op het cluster wordt uitgevoerd. Brug naar Kubernetes omleidt verkeer tussen uw verbonden cluster door een pod uit te voeren op uw cluster dat fungeert als een externe agent om verkeer tussen uw ontwikkel computer en het cluster om te leiden. Met deze omleidings functie voor verkeer kan code op uw ontwikkel computer en services die in uw cluster worden uitgevoerd, communiceren alsof ze zich in hetzelfde cluster bevonden. Zie [hoe Bridge to Kubernetes werkt][how-it-works-bridge-to-kubernetes]voor meer informatie over het verbinden van uw ontwikkel computer met een Kubernetes-cluster.

### <a name="run-your-code-in-aks"></a>Uw code uitvoeren in AKS

Naast het omleiden van verkeer tussen uw ontwikkel computer en uw AKS-cluster kunt u met Azure dev Spaces uw code rechtstreeks in AKS configureren en snel uitvoeren. Met Visual Studio, Visual Studio code of de Azure dev Spaces-CLI uploadt Azure dev Spaces uw code naar het cluster en bouwt en voert u deze uit. Met Azure dev Spaces kunt u ook code wijzigingen intelligent synchroniseren en uw service opnieuw starten om zo nodig wijzigingen weer te geven. Tijdens het uitvoeren van uw code worden build-logboeken en HTTP-traceringen weer gestreamd naar uw client, zodat u de voortgang kunt bewaken en eventuele problemen moet vaststellen. U kunt ook Azure dev Spaces gebruiken om de fout opsporing in Visual Studio en Visual Studio code te koppelen aan Java, Node.js en .NET Core Services. Zie How to premate [a project for Azure dev Spaces][how-it-works-prep](Engelstalig) voor meer informatie, [hoe u uw code kunt uitvoeren met Azure dev Spaces werkt][how-it-works-up]en [hoe u uw code op afstand kunt debuggen met Azure dev Spaces][how-it-works-remote-debugging].

## <a name="team-development"></a>Teamontwikkeling

Met Azure dev Spaces kunnen teams productief werken aan hun toepassing op hetzelfde AKS-cluster zonder dat dit wordt verstoord.

### <a name="intelligent-routing-between-dev-spaces"></a>Intelligente route ring tussen dev-ruimten

Met Azure dev Spaces kan een team een single AKS-cluster met een Cloud toepassing delen en geïsoleerde ontwikkel ruimten maken waar het team kan ontwikkelen, testen en fouten opsporen zonder de andere ontwikkel ruimten te verstoren. Een basislijn versie van de toepassing wordt uitgevoerd in een hoofd dev-ruimte. Team leden maken vervolgens onafhankelijke onderliggende ontwikkel ruimten op basis van de hoofd ruimte voor het ontwikkelen, testen en fout opsporing van wijzigingen in de toepassing. Met de routerings mogelijkheden in dev Spaces kunnen onderliggende ontwikkel ruimten aanvragen routeren tussen services die worden uitgevoerd in de onderliggende ontwikkel ruimte en de bovenliggende ontwikkel ruimte. Met deze route ring kunnen ontwikkel aars hun eigen versie van een service uitvoeren terwijl ze afhankelijke services van de bovenliggende ruimte opnieuw gebruiken. Elke onderliggende ruimte heeft een eigen unieke URL, die door anderen kan worden gedeeld en geopend voor samen werking. Zie [hoe route ring werkt met Azure dev Spaces][how-it-works-routing]voor meer informatie over hoe route ring werkt in azure dev Spaces.

### <a name="live-testing-an-open-pull-request"></a>Live testen van een open pull-aanvraag

U kunt ook GitHub-acties met Azure-ontwikkel ruimten gebruiken om wijzigingen in uw toepassing in een pull-aanvraag rechtstreeks in uw cluster te testen voordat u ze samenvoegt. Met Azure dev Spaces kan automatisch een beoordelings versie van de toepassing naar uw cluster worden geïmplementeerd, zodat de auteur en andere team leden de wijzigingen in de context van de hele toepassing kunnen controleren. Door gebruik te maken van de routerings mogelijkheden van Azure dev Spaces, wordt deze beoordelings versie van de toepassing ook geïmplementeerd in uw cluster zonder dat dit van invloed is op andere ontwikkel ruimten. Al deze mogelijkheden bieden u de mogelijkheid om pull-aanvragen goed te keuren en samen te voegen. Zie [github actions & Azure Kubernetes service][pr-flow](Engelstalig) voor een voor beeld van github-acties en Azure dev Spaces.

## <a name="next-steps"></a>Volgende stappen

Zie [uw ontwikkel computer verbinden met een AKS-cluster][connect]om aan de slag te gaan met het verbinden van uw lokale ontwikkel computer met uw AKS-cluster.


[connect]: https://code.visualstudio.com/docs/containers/bridge-to-kubernetes
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[pr-flow]: how-to/github-actions.md
[routing]: #team-development