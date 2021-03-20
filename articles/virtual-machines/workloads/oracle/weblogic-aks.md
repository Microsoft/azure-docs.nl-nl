---
title: Wat zijn oplossingen voor het uitvoeren van Oracle WebLogic Server op de Azure Kubernetes-service
description: Meer informatie over het uitvoeren van Oracle WebLogic Server op de Azure Kubernetes-service.
author: rezar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 02/23/2021
ms.author: rezar
ms.reviewer: cynthn
ms.openlocfilehash: ac9f81fbde33bdd10bc8374a566a4f2ba83fc253
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101669012"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-the-azure-kubernetes-service"></a>Wat zijn oplossingen voor het uitvoeren van Oracle WebLogic Server op de Azure Kubernetes-service?

Op deze pagina worden de oplossingen beschreven voor het uitvoeren van Oracle WebLogic Server (WLS) op de Azure Kubernetes-service (AKS). Deze oplossingen worden gezamenlijk ontwikkeld en ondersteund door Oracle en micro soft.

Het is ook mogelijk om WebLogic Server uit te voeren op Azure Virtual Machines. De oplossingen hiervoor worden beschreven in [dit micro soft-artikel](./oracle-weblogic.md).

WebLogic Server is een toonaangevende Java-toepassings server waarop enkele van de meest essentiële zakelijke Java-toepassingen over de hele wereld worden uitgevoerd. WebLogic Server vormt de middleware Foundation voor de Oracle-software suite. Oracle en micro soft zijn van belang om WebLogic Server-klanten keuze en flexibiliteit te bieden voor het uitvoeren van werk belastingen op Azure als een toonaangevend Cloud platform.

## <a name="wls-on-aks-certified-and-supported"></a>WLS op AKS gecertificeerd en ondersteund
WebLogic Server is gecertificeerd door Oracle en micro soft om goed te kunnen werken op AKS. De WebLogic-Server op AKS-oplossingen is bedoeld om het zo eenvoudig mogelijk te maken om uw container-en georganisatiede Java-toepassingen uit te voeren op docker en Kubernetes-infra structuur. De oplossingen zijn gericht op betrouw baarheid, schaal baarheid, beheersbaarheid en bedrijfs ondersteuning.

WebLogic-Server clusters zijn volledig ingeschakeld om te worden uitgevoerd op Kubernetes via de WebLogic Kubernetes-operator (waarnaar wordt verwezen, simpelweg de operator '). De operator volgt het standaard Kubernetes-operator patroon. Het vereenvoudigt het beheer en de werking van WebLogic domeinen en implementaties op Kubernetes door andere hand matige taken te automatiseren en extra functies voor operationele betrouw baarheid toe te voegen. De operator ondersteunt Oracle WebLogic Server 12c, Oracle Fusion Middleware Infrastructure 12c en hoger. We hebben de officiële docker-installatie kopieën voor WebLogic Server 12.2.1.3 en 12.2.1.4 getest met de operator. Raadpleeg de [officiële documentatie van Oracle](https://oracle.github.io/weblogic-kubernetes-operator/)voor meer informatie over de operator.

## <a name="guidance-scripts-and-samples-for-wls-on-aks"></a>Richt lijnen, scripts en voor beelden voor WLS op AKS
Naast het certificeren van WebLogic-Server op AKS, bieden Oracle en micro soft gezamenlijk gedetailleerde instructies, scripts en voor beelden voor het uitvoeren van WebLogic Server op AKS. De richt lijnen zijn opgenomen in de sectie voor beeld van de Azure Kubernetes-service van de [operator-documentatie](https://oracle.github.io/weblogic-kubernetes-operator/samples/simple/azure-kubernetes-service/). De richt lijnen zijn bedoeld om productie WebLogic Server zo eenvoudig mogelijk te maken op AKS-implementaties. De richt lijnen gebruiken officiële WebLogic Server-docker-installatie kopieën van Oracle. Failover wordt bereikt via Azure Files die toegankelijk zijn via Kubernetes permanente volume claims. Azure load balancers worden ondersteund bij het inrichten met behulp van een Kubernetes-service van het type Load Balancer. De Azure Container Registry (ACR) wordt ondersteund voor het implementeren van WLS-domeinen in aangepaste docker-installatie kopieën. De richt lijnen kunnen een hoge mate van configuratie en aanpassing bieden.

:::image type="content" source="media/oracle-weblogic/wls-on-aks.gif" alt-text="U kunt de voorbeeld scripts gebruiken om WebLogic Server te implementeren op AKS":::

De oplossingen zijn twee manieren om WLS-domeinen te implementeren in AKS. Domeinen kunnen rechtstreeks worden geïmplementeerd naar Kubernetes permanente volumes. Deze implementatie optie is handig als u wilt migreren naar AKS, maar nog steeds een WLS wilt beheren via de beheer console of het WebLogic scripting tool (WLST). Met deze optie kunt u ook overstappen op AKS zonder dat docker-ontwikkeling wordt aangenomen. De meer Kubernetes systeem eigen manier van het implementeren van WLS-domeinen naar AKS is het bouwen van aangepaste docker-installatie kopieën op basis van officiële WLS-installatie kopieën van de Oracle-Container Registry, het publiceren van de aangepaste installatie kopieën naar ACR en het implementeren van het domein naar AKS met behulp van de operator. Met deze optie in de oplossing kunt u het domein ook bijwerken via Kubernetes ConfigMaps nadat de implementatie is voltooid.

Meer gebruiks vriendelijke en Azure service-integraties zijn mogelijk in de toekomst via Marketplace-aanbiedingen die Oracle WebLogic Server op Azure Virtual Machines-oplossingen spie gelen.

_Deze oplossingen zijn uw eigen licentie_. Er wordt ervan uitgegaan dat u de juiste licenties voor Oracle al hebt en een juiste licentie hebt voor het uitvoeren van aanbiedingen in Azure.

_Als u geïnteresseerd bent in het samen werken met uw migratie scenario's met het technische team dat deze oplossingen ontwikkelt, vult u [deze korte enquête](https://aka.ms/wls-on-azure-survey) in en neemt u uw contact gegevens op_. Programma managers, Architects en technici zullen binnenkort een back-up maken en de samen werking sluiten. De kans om samen te werken aan een migratie scenario is gratis, terwijl de oplossingen onder actieve eerste ontwikkeling vallen.

## <a name="deployment-architectures"></a>Implementatie architecturen

Met de oplossingen voor het uitvoeren van Oracle WebLogic Server op de Azure Kubernetes-service kunt u een breed scala aan implementatie architecturen die klaar zijn voor productie, met relatief gemak.

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-aks.png" alt-text="Complexe WebLogic-Server implementaties zijn ingeschakeld op AKS":::

Meer informatie over de oplossingen biedt klanten de mogelijkheid om hun implementaties verder aan te passen. De configuratie van de implementatie van toepassingen wordt waarschijnlijk uitgebreid met de implementaties van andere Azure-resources. Klanten wordt aangeraden feedback te geven in de enquête over het verder verbeteren van de oplossingen.

## <a name="next-steps"></a>Volgende stappen

Verken Oracle WebLogic Server uitvoeren op de Azure Kubernetes-service.

> [!div class="nextstepaction"]
> [Richt lijnen, scripts en voor beelden voor het uitvoeren van WLS op AKS](https://oracle.github.io/weblogic-kubernetes-operator/samples/simple/azure-kubernetes-service/)

> [!div class="nextstepaction"]
> [WebLogic Kubernetes-operator](https://oracle.github.io/weblogic-kubernetes-operator/)
