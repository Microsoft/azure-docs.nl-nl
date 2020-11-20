---
title: Wat zijn oplossingen voor het uitvoeren van Oracle WebLogic Server op de Azure Kubernetes-service
description: Meer informatie over het uitvoeren van Oracle WebLogic Server op de Azure Kubernetes-service.
author: rezar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 10/28/2020
ms.author: rezar
ms.reviewer: cynthn
ms.openlocfilehash: a57d5a00fad9a17e01b96ebdf395fb7a4b857935
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968686"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-the-azure-kubernetes-service"></a>Wat zijn oplossingen voor het uitvoeren van Oracle WebLogic Server op de Azure Kubernetes-service?

Op deze pagina worden de oplossingen beschreven voor het uitvoeren van Oracle WebLogic Server (WLS) op de Azure Kubernetes-service (AKS). Deze oplossingen worden gezamenlijk ontwikkeld en ondersteund door Oracle en micro soft.

Het is ook mogelijk om WebLogic Server uit te voeren op Azure Virtual Machines. De oplossingen hiervoor worden beschreven in [dit micro soft-artikel](/azure/virtual-machines/workloads/oracle/oracle-weblogic).

WebLogic Server is een toonaangevende Java-toepassings server waarop enkele van de meest essentiële zakelijke Java-toepassingen over de hele wereld worden uitgevoerd. WebLogic Server vormt de middleware Foundation voor de Oracle-software suite. Oracle en micro soft zijn van belang om WebLogic Server-klanten keuze en flexibiliteit te bieden voor het uitvoeren van werk belastingen op Azure als een toonaangevend Cloud platform.

## <a name="wls-on-aks-certified-and-supported"></a>WLS op AKS gecertificeerd en ondersteund
WebLogic Server is gecertificeerd door Oracle en micro soft om goed te kunnen werken op AKS. De WebLogic-Server op AKS-oplossingen is bedoeld om het zo eenvoudig mogelijk te maken om uw containers en georganisatiede Java EE-toepassingen op docker en Kubernetes-infra structuur uit te voeren. De oplossingen zijn gericht op betrouw baarheid, schaal baarheid, beheersbaarheid en bedrijfs ondersteuning.

WebLogic-Server clusters zijn volledig ingeschakeld om te worden uitgevoerd op Kubernetes via de WebLogic Kubernetes-operator (waarnaar wordt verwezen, simpelweg de operator '). De operator volgt het standaard Kubernetes-operator patroon. Het vereenvoudigt het beheer en de werking van WebLogic domeinen en implementaties op Kubernetes door andere hand matige taken te automatiseren en aanvullende functies voor operationele betrouw baarheid toe te voegen. De operator ondersteunt Oracle WebLogic Server 12c, Oracle Fusion Middleware Infrastructure 12c en hoger. We hebben de officiële docker-installatie kopieën voor WebLogic Server 12.2.1.3 en 12.2.1.4 getest met de operator. Raadpleeg de [officiële documentatie van Oracle](https://oracle.github.io/weblogic-kubernetes-operator/)voor meer informatie over de operator.

## <a name="guidance-scripts-and-samples-for-wls-on-aks"></a>Richt lijnen, scripts en voor beelden voor WLS op AKS
Naast het certificeren van WebLogic-Server op AKS, bieden Oracle en micro soft gezamenlijk gedetailleerde instructies, scripts en voor beelden voor het uitvoeren van WebLogic Server op AKS. De richt lijnen zijn opgenomen in de sectie voor beeld van de Azure Kubernetes-service van de [operator-documentatie](https://oracle.github.io/weblogic-kubernetes-operator/samples/simple/azure-kubernetes-service/). De richt lijnen zijn bedoeld om productie WebLogic Server zo eenvoudig mogelijk te maken op AKS-implementaties. De richt lijnen gebruiken officiële WebLogic Server-docker-installatie kopieën van Oracle. Failover wordt bereikt via Azure Files die toegankelijk zijn via Kubernetes permanente volume claims. Azure load balancers worden ondersteund bij het inrichten met behulp van een Kubernetes-service van het type Load Balancer. De richt lijnen kunnen een hoge mate van configuratie en aanpassing bieden.

:::image type="content" source="media/oracle-weblogic/wls-on-aks.gif" alt-text="U kunt de voorbeeld scripts gebruiken om WebLogic Server te implementeren op AKS":::

In de richt lijnen wordt ervan uitgegaan dat u het domein buiten de docker-installatie kopie implementeert en gebruikmaakt van de standaard docker-installatie kopieën van Oracle. We voegen hulp toe voor het inschakelen van aangepaste installatie kopieën met uw domein in een docker-installatie kopie. Meer gebruiks vriendelijke en Azure service-integraties zijn mogelijk in de toekomst via Marketplace-aanbiedingen die Oracle WebLogic Server op Azure Virtual Machines-oplossingen spie gelen.

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
