---
title: Azure DNS gebruiken met andere Azure-Services
description: In dit leer traject leert u hoe u Azure DNS kunt gebruiken om namen voor andere Azure-Services op te lossen
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
tags: azure dns
ms.assetid: e9b5eb94-7984-4640-9930-564bb9e82b78
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: rohink
ms.openlocfilehash: fa2c1ced6405c967ca33562d6215b304b8507e5a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "76937242"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Hoe Azure DNS werkt met andere Azure-Services?

Azure DNS is een gehoste DNS-beheer-en service voor naam omzetting. U kunt deze gebruiken om open bare DNS-namen te maken voor andere toepassingen en services die u in azure implementeert. Het maken van een naam voor een Azure-service in uw aangepaste domein is eenvoudig. U hoeft alleen maar een record van het juiste type voor uw service toe te voegen.

* Voor dynamisch toegewezen IP-adressen kunt u een DNS CNAME-record maken die is toegewezen aan de DNS-naam die Azure voor uw service heeft gemaakt. DNS-standaarden verhinderen dat u een CNAME-record voor de zone Apex kunt gebruiken. U kunt in plaats daarvan een alias record gebruiken. Zie [zelf studie: een alias record configureren om te verwijzen naar een openbaar IP-adres van Azure](tutorial-alias-pip.md)voor meer informatie.
* Voor statisch toegewezen IP-adressen kunt u een DNS A-record maken met behulp van een wille keurige naam, die een *Blot-domein* naam bevat op de zone Apex.

De volgende tabel geeft een overzicht van de ondersteunde record typen die u voor verschillende Azure-Services kunt gebruiken. Zoals in de tabel wordt weer gegeven, ondersteunt Azure DNS alleen DNS-records voor netwerk bronnen op internet. Azure DNS kan niet worden gebruikt voor naam omzetting van interne, persoonlijke adressen.

| Azure-service | Netwerkinterface | Beschrijving |
| --- | --- | --- |
| Azure Application Gateway |[Openbaar IP-adres voor front-end](dns-custom-domain.md#public-ip-address) |U kunt een DNS A-of CNAME-record maken. |
| Azure Load Balancer |[Openbaar IP-adres voor front-end](dns-custom-domain.md#public-ip-address) |U kunt een DNS A-of CNAME-record maken. Load Balancer kunnen een openbaar IP-adres voor IPv6 zijn dat dynamisch wordt toegewezen. Maak een CNAME-record voor een IPv6-adres. |
| Azure Traffic Manager |Open bare naam |U kunt een alias record maken die verwijst naar de naam van de trafficmanager.net die is toegewezen aan uw Traffic Manager profiel. Zie [zelf studie: een alias record configureren ter ondersteuning van Apex-domein namen met Traffic Manager](tutorial-alias-tm.md)voor meer informatie. |
| Azure Cloud Services |[Openbare IP](dns-custom-domain.md#public-ip-address) |Voor statisch toegewezen IP-adressen kunt u een DNS A-record maken. Voor dynamisch toegewezen IP-adressen moet u een CNAME-record maken die is toegewezen aan de naam van de *cloudapp.net* .|
| Azure App Service | [Extern IP-adres](dns-custom-domain.md#app-service-web-apps) |Voor externe IP-adressen kunt u een DNS A-record maken. Als dat niet het geval is, moet u een CNAME-record maken die is toegewezen aan de naam van de azurewebsites.net. Zie [een aangepaste domein naam toewijzen aan een Azure-app](../app-service/app-service-web-tutorial-custom-domain.md)voor meer informatie. |
| Azure Resource Manager Vm's |[Openbare IP](dns-custom-domain.md#public-ip-address) |Resource Manager-Vm's kunnen open bare IP-adressen hebben. Een virtuele machine met een openbaar IP-adres kan zich ook achter een load balancer bevindt. U kunt een DNS A-, CNAME-of alias-record voor het open bare adres maken. U kunt deze aangepaste naam gebruiken om het VIP op de load balancer over te slaan. |
| Klassieke VM's |[Openbare IP](dns-custom-domain.md#public-ip-address) |Klassieke Vm's die zijn gemaakt met behulp van Power shell of CLI, kunnen worden geconfigureerd met een dynamisch of statisch (gereserveerd) virtueel adres. U kunt respectievelijk een DNS CNAME of een A-record maken. |
