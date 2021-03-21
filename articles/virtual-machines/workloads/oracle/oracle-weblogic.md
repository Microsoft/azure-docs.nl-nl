---
title: Wat zijn oplossingen voor het uitvoeren van Oracle WebLogic Server op Azure Virtual Machines
description: Meer informatie over het uitvoeren van Oracle WebLogic Server op Microsoft Azure Virtual Machines.
author: rezar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 02/24/2021
ms.author: rezar
ms.openlocfilehash: a5675b313586615d4bad733aec6eabf0360f8489
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694700"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-azure-virtual-machines"></a>Wat zijn oplossingen om Oracle WebLogic Server uit te voeren in Azure-VM's?

Op deze pagina worden de oplossingen beschreven voor het uitvoeren van een WebLogic-WLS-server op Azure virtual machines. Deze oplossingen worden gezamenlijk ontwikkeld en ondersteund door Oracle en micro soft.

U kunt ook WLS uitvoeren op de Azure Kubernetes-service. De oplossingen hiervoor worden beschreven in [dit micro soft-artikel](./weblogic-aks.md).

WLS is een toonaangevende Java-toepassings server waarop enkele van de meest essentiële zakelijke Java-toepassingen op de hele wereld worden uitgevoerd. WLS vormt de middleware Foundation voor de Oracle-software suite. Oracle en micro soft zijn van belang om WLS-klanten keuze en flexibiliteit te bieden voor het uitvoeren van werk belastingen op Azure als een toonaangevend Cloud platform.

De Azure WLS-oplossingen zijn bedoeld om het zo eenvoudig mogelijk te maken om uw Java-toepassingen te migreren naar Azure virtual machines. De oplossingen doen dit door geïmplementeerde resources te genereren voor de meeste algemene scenario's voor het inrichten van Clouds. Met de oplossingen worden automatisch virtuele netwerk-, opslag-, Java-, WLS-en Linux-Resources ingericht. Met minimale inspanning wordt WebLogic-Server geïnstalleerd. De oplossingen kunnen beveiliging instellen met een netwerk beveiligings groep, taak verdeling met Azure-app gateway, verificatie met Azure Active Directory, gecentraliseerde logboek registratie met behulp van ELK en gedistribueerde caching met Oracle-coherentie. U kunt ook automatisch verbinding maken met uw bestaande data base, inclusief Azure PostgreSQL, Azure SQL en Oracle DB op de Oracle-Cloud of Azure. 

:::image type="content" source="media/oracle-weblogic/wls-on-azure.gif" alt-text="U kunt de Azure Portal gebruiken om WebLogic Server te implementeren op Azure":::

Er zijn vier aanbiedingen beschikbaar om te voldoen aan verschillende scenario's: [Eén knoop punt zonder een beheer server](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls), [één knoop punt met een beheer server](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin), [cluster](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)en [dynamisch cluster](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster). De aanbiedingen zijn gratis beschikbaar. Deze aanbiedingen worden hieronder beschreven en gekoppeld.

_Deze aanbiedingen zijn uw eigen licentie_. Er wordt ervan uitgegaan dat u de juiste licenties voor Oracle al hebt en een juiste licentie hebt voor het uitvoeren van aanbiedingen in Azure.

De aanbiedingen bieden ondersteuning voor verschillende besturings systemen, Java-en WLS-versies via basis installatie kopieën (zoals WebLogic Server 14 en JDK 11 op Oracle Linux 7,6). Deze basis installatie kopieën zijn ook op hun eigen beschikbaar in Azure. De basis installatie kopieën zijn geschikt voor klanten die complexe, aangepaste Azure-implementaties nodig hebben. De huidige set basis installatie kopieën is [hier](https://azuremarketplace.microsoft.com/marketplace/apps?search=WebLogic%20Server%20Base%20Image&page=1)beschikbaar.

_Als u geïnteresseerd bent in het samen werken met uw migratie scenario's met het technische team dat deze aanbiedingen ontwikkelt, selecteert u de knop [contact opnemen](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview)_ op de [pagina overzicht van Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview). Programma managers, Architects en technici zullen binnenkort een back-up maken en de samen werking sluiten. De kans om samen te werken aan een migratie scenario is gratis, terwijl de aanbiedingen onder actieve ontwikkeling vallen.

## <a name="oracle-weblogic-server-single-node"></a>Oracle WebLogic-Server met één knoop punt

[Deze aanbieding biedt](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls) een enkele virtuele machine en installeert WLS hierop. Er wordt geen domein gemaakt of de beheer server wordt gestart. De aanbieding met één knoop punt is handig voor scenario's met een zeer aangepaste domein configuratie.

## <a name="oracle-weblogic-server-with-admin-server"></a>Oracle WebLogic Server met de-beheer server

[Deze aanbieding biedt](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin) een enkele virtuele machine en installeert WLS hierop. Er wordt een domein gemaakt en de beheer server wordt gestart. U kunt het domein beheren en meteen aan de slag met toepassings implementaties.

## <a name="oracle-weblogic-server-cluster"></a>Oracle WebLogic Server-cluster

Met [Deze aanbieding](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster) maakt u een Maxi maal beschikbaar cluster van virtuele WLS-machines. De-beheer server en alle beheerde servers worden standaard gestart. U kunt het cluster beheren en meteen aan de slag met Maxi maal beschik bare toepassingen.

## <a name="oracle-weblogic-server-dynamic-cluster"></a>Dynamische Oracle WebLogic Server-cluster

Met [Deze aanbieding](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster) maakt u een Maxi maal beschik bare en schaalbaar dynamisch cluster van virtuele WLS-machines. De-beheer server en alle beheerde servers worden standaard gestart.

Met de oplossingen wordt een breed scala aan productie-Kante implementatie architecturen met relatief gemak ingeschakeld. U kunt de meeste migratie aanvragen op de meest productieve manier voldoen door de ontwikkeling van zakelijke toepassingen te richten.

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-vms.png" alt-text="Er zijn complexe WebLogic-Server implementaties ingeschakeld in azure":::

Behalve wat automatisch door de oplossingen wordt ingericht, hebben klanten volledige flexibiliteit om hun implementaties verder aan te passen. De configuratie van de implementatie van toepassingen wordt waarschijnlijk uitgebreid met de implementaties van andere Azure-resources. Klanten wordt aangeraden [verbinding te maken met het ontwikkel team](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) en feedback te geven over het verbeteren van de oplossingen.

## <a name="next-steps"></a>Volgende stappen

Verken de aanbiedingen op Azure.

> [!div class="nextstepaction"]
> [Oracle WebLogic-Server met één knoop punt](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server met de-beheer server](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server-cluster](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Dynamische Oracle WebLogic Server-cluster](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)
