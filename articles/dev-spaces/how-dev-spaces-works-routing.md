---
title: Hoe route ring werkt met Azure dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Hierin worden de processen beschreven die Power Azure dev Spaces en hoe route ring werkt
keywords: Azure dev Spaces, dev Spaces, docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: 2d2c6f336222b4ae0907d6579289a8cad8d73aa6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91977966"
---
# <a name="how-routing-works-with-azure-dev-spaces"></a>Hoe route ring werkt met Azure dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Met Azure dev Spaces kunt u op verschillende manieren snel en probleem oplossing voor Kubernetes-toepassingen en samen werken met uw team op een AKS-cluster (Azure Kubernetes service). Zodra het project wordt uitgevoerd in een dev Space, biedt Azure dev Spaces extra netwerk-en routerings mogelijkheden voor uw project.

In dit artikel wordt beschreven hoe route ring werkt met dev Spaces.

## <a name="how-routing-works"></a>Hoe werkt route ring?

Een dev-ruimte is gebaseerd op AKS en maakt gebruik van dezelfde [netwerk concepten](../aks/concepts-network.md). Azure dev Spaces heeft ook een gecentraliseerde *ingressmanager* -service en implementeert zijn eigen ingangs controller naar het AKS-cluster. De *ingressmanager* -Service bewaakt AKS-clusters met ontwikkel ruimten en breidt de Azure dev Spaces-controller uit in het cluster met ingangs objecten voor route ring naar toepassing. De devspaces-proxy container in elke pod voegt een `azds-route-as` http-header voor HTTP-verkeer toe aan een ontwikkel ruimte op basis van de URL. Zo krijgt een aanvraag naar de URL *http://azureuser.s.default.serviceA.fedcba09...azds.io* een HTTP-header met `azds-route-as: azureuser` . De devspaces-proxy container voegt geen header toe `azds-route-as` als deze al aanwezig is.

Wanneer een HTTP-aanvraag wordt gedaan bij een service van buiten het cluster, gaat de aanvraag naar de ingangs controller. De ingangs controller stuurt de aanvraag rechtstreeks naar de juiste pod op basis van de inkomende objecten en regels. De devspaces-proxy container in de Pod ontvangt de aanvraag, voegt de `azds-route-as` header toe op basis van de URL en stuurt de aanvraag door naar de toepassings container.

Wanneer een HTTP-aanvraag wordt gedaan bij een service van een andere service binnen het cluster, loopt de aanvraag eerst door de devspaces-proxy container van de aanroepende service. De devspaces-proxy container zoekt naar de HTTP-aanvraag en controleert de `azds-route-as` header. Op basis van de header zoekt de devspaces-proxy container het IP-adres van de service die is gekoppeld aan de waarde van de header. Als er een IP-adres wordt gevonden, wordt de aanvraag door de devspaces-proxy container omgeleid naar dat IP-adres. Als er geen IP-adres wordt gevonden, stuurt de devspaces-proxy container de aanvraag door naar de bovenliggende toepassings container.

De toepassingen *servicea* en *serviceB* worden bijvoorbeeld geïmplementeerd naar een bovenliggende ontwikkel ruimte met de naam *standaard*. *servicea* is afhankelijk van *serviceB* en maakt http-aanroepen. Azure-gebruiker maakt een onderliggende ontwikkel ruimte op basis van de *standaard* ruimte met de naam *azureuser*. Azure-gebruiker implementeert ook hun eigen versie van *servicea* op hun onderliggende ruimte. Wanneer een aanvraag wordt ingediend bij *http://azureuser.s.default.serviceA.fedcba09...azds.io* :

![Route ring van Azure dev Spaces](media/how-dev-spaces-works/routing.svg)

1. De ingangs controller zoekt naar het IP-adres voor de pod die is gekoppeld aan de URL. Dit is *servicea. azureuser*.
1. De ingangs controller vindt het IP-adres voor de pod in de ontwikkel ruimte van Azure User en stuurt de aanvraag door naar de *servicea. azureuser* pod.
1. De devspaces-proxy container in de *servicea. azureuser* Pod ontvangt de aanvraag en voegt deze toe `azds-route-as: azureuser` als een http-header.
1. De devspaces-proxy container in de *servicea. azureuser* pod stuurt de aanvraag door naar de *servicea* -toepassings container in de *servicea. azureuser* pod.
1. De *servicea* -toepassing in de *servicea. azureuser* pod maakt een aanroep van *serviceB*. De *servicea* -toepassing bevat ook code voor het bewaren `azds-route-as` van de bestaande header, in dit geval `azds-route-as: azureuser` .
1. De devspaces-proxy container in de *servicea. azureuser* Pod ontvangt de aanvraag en zoekt het IP-adres van *serviceB* op basis van de waarde van de `azds-route-as` header.
1. De devspaces-proxy container in de *servicea. azureuser* Pod heeft geen IP-adres voor *serviceB. azureuser*.
1. De devspaces-proxy container in de *servicea. azureuser* pod zoekt het IP-adres voor *serviceB* in de bovenliggende ruimte. Dit is *serviceB. default*.
1. De devspaces-proxy container in de *servicea. azureuser* pod vindt het IP-adres voor *serviceB. default* en stuurt de aanvraag door naar de *serviceB. default* pod.
1. De devspaces-proxy container in de *serviceB. default* Pod ontvangt de aanvraag en stuurt de aanvraag door naar de container *serviceB* -toepassing in de *serviceB. standaard* pod.
1. De *serviceB* -toepassing in de *serviceB. default* pod retourneert een antwoord op de *servicea. azureuser* pod.
1. De devspaces-proxy container in de *servicea. azureuser* Pod ontvangt het antwoord en stuurt het antwoord naar de container *servicea* -toepassing in de *servicea. azureuser* pod.
1. De *servicea* -toepassing ontvangt het antwoord en retourneert vervolgens een eigen antwoord.
1. De devspaces-proxy container in de *servicea. azureuser* Pod ontvangt de reactie van de *servicea* -toepassings container en stuurt het antwoord naar de oorspronkelijke aanroeper buiten het cluster.

Alle andere TCP-verkeer dat niet HTTP wordt door gegeven via de ingangs controller en devspaces-proxy containers, worden niet gewijzigd.

## <a name="sharing-a-dev-space"></a>Een ontwikkelings ruimte delen

Wanneer u met een team werkt, kunt u een dev-ruimte delen binnen een heel team en afgeleide ontwikkel ruimten maken. Een ontwikkel ruimte kan worden gebruikt door iedereen met Inzender toegang tot de resource groep van de ontwikkelings ruimte.

U kunt ook een nieuwe ontwikkel ruimte maken die is afgeleid van een andere dev-ruimte. Wanneer u een afgeleide dev-ruimte maakt, wordt het label *azds.io/Parent-Space=Parent-Space-name* toegevoegd aan de naam ruimte van de afgeleide dev-ruimte. Daarnaast worden alle toepassingen van de bovenliggende ontwikkel ruimte gedeeld met de afgeleide dev-ruimte. Als u een bijgewerkte versie van een toepassing implementeert in de afgeleide dev-ruimte, bestaat deze alleen in de afgeleide dev-ruimte en blijft de bovenliggende ontwikkel ruimte ongewijzigd. U kunt Maxi maal drie niveaus afgeleide dev-ruimten of groot *ouders* hebben.

Met de afgeleide ontwikkel ruimte worden aanvragen ook intelligent gerouteerd tussen de eigen toepassingen en de toepassingen die worden gedeeld vanuit het bovenliggende knoop punt. De route ring werkt door het routeren van aanvragen naar een toepassing in de afgeleide dev-ruimte en terug te vallen op de gedeelde toepassing vanuit de bovenliggende ontwikkel ruimte. De route ring gaat terug naar de gedeelde toepassing in de grootst-ruimte als de toepassing zich niet in de bovenliggende ruimte bevindt.

Bijvoorbeeld:
* De ontwikkel ruimte *standaard* heeft toepassingen *servicea* en *serviceB*.
* De *azureuser* voor de ontwikkelings ruimte wordt afgeleid van de *standaard waarde*.
* Er is een bijgewerkte versie van *servicea* geïmplementeerd op *azureuser*.

Wanneer *azureuser* wordt gebruikt, worden alle aanvragen naar *servicea* doorgestuurd naar de bijgewerkte versie in *azureuser*. Er wordt eerst een aanvraag naar *serviceB* verzonden naar de *azureuser* -versie van *serviceB*. Omdat deze niet bestaat, wordt deze doorgestuurd naar de *standaard* versie van *serviceB*. Als de *azureuser* -versie van *servicea* wordt verwijderd *, worden alle aanvragen van servicea* teruggestuurd naar het gebruik van de *standaard* versie van *servicea*.

## <a name="next-steps"></a>Volgende stappen

Voor een voor beeld van hoe Azure dev Spaces route ring gebruiken om snel te kunnen iteratieen en te ontwikkelen, raadpleegt u [hoe u uw code op afstand kunt debuggen met Azure dev Spaces][how-it-works-remote-debugging].


[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md