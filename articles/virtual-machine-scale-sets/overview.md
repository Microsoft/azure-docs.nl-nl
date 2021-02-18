---
title: Overzicht van schaalsets voor virtuele Azure-machines
description: Leer alles over schaalsets voor virtuele Azure-machines en hoe u toepassingen automatisch kunt schalen
author: mimckitt
ms.author: mimckitt
ms.topic: overview
ms.service: virtual-machine-scale-sets
ms.subservice: ''
ms.date: 06/30/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 4f741c1317f70079755b61f7ad94a415cd039865
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578880"
---
# <a name="what-are-virtual-machine-scale-sets"></a>Wat zijn schaalsets voor virtuele machines?
Met behulp van schaalsets voor virtuele Azure-machines kunt u een groep VM's met gelijke taakverdeling maken en beheren. Het aantal VM-exemplaren kan automatisch toenemen of afnemen in reactie op vraag of een ingesteld schema. Schaalsets bieden een hoge beschikbaarheid voor uw toepassingen. Een ander voordeel is dat u vanaf één plek een groot aantal virtuele machines kunt beheren, configureren en bijwerken. Met schaalsets voor virtuele machines kunt u grootschalige services bouwen voor zaken zoals rekenkracht, big data en containerworkloads.


## <a name="why-use-virtual-machine-scale-sets"></a>Waarom schaalsets voor virtuele machines gebruiken?
Om redundantie en verbeterde prestaties te bieden, worden toepassingen meestal verdeeld over meerdere exemplaren of instanties. Klanten kunnen toegang krijgen tot uw toepassing via een load balancer die aanvragen verdeelt naar een van de toepassingsexemplaren. Als u onderhoud wilt uitvoeren of een exemplaar van een toepassing wilt bijwerken, moeten uw klanten worden gedistribueerd naar een ander beschikbaar toepassingsexemplaar. Om steeds te kunnen voldoen aan de extra vraag van klanten, kan het nodig zijn om het aantal exemplaren te verhogen waarop uw toepassing wordt uitgevoerd.

Schaalsets voor virtuele Azure-machines bieden beheermogelijkheden voor toepassingen die worden uitgevoerd op een groot aantal virtuele machines, [automatisch schalen van resources](virtual-machine-scale-sets-autoscale-overview.md) en taakverdeling van verkeer. Dit zijn de belangrijkste voordelen van schaalsets:

- **Eenvoudig maken en beheren van meerdere virtuele machines**
    - Wanneer uw toepassing wordt uitgevoerd op veel virtuele machines, is het belangrijk om binnen de hele omgeving een consistente configuratie te onderhouden. Voor betrouwbare prestaties van uw toepassing moeten de VM grootte, de schijfconfiguratie en de installaties van de toepassing op alle VM's overeenkomen.
    - Als u kiest voor schaalsets, wordt voor alle VM-exemplaren dezelfde installatiekopie en configuratie van het besturingssysteem gebruikt. Deze aanpak maakt het mogelijk om zonder aanvullende configuratietaken of netwerkbeheer eenvoudig honderden VM's te beheren.
    - Schaalsets ondersteunen het gebruik van de [Azure load balancer](../load-balancer/load-balancer-overview.md) voor standaarddistributie van verkeer op laag 4 en [Azure Application Gateway](../application-gateway/overview.md) voor meer geavanceerde distributie van verkeer op laag 7 en TSL-beëindiging.

- **Hoge beschikbaarheid en tolerantie voor toepassing**
    - Schaalsets worden gebruikt voor het uitvoeren van meerdere exemplaren van een toepassing. Als op een van deze VM-exemplaren een probleem optreedt, hebben klanten nog steeds toegang tot uw toepassing, omdat er dan met minimale onderbreking wordt overgeschakeld naar een van de andere VM-exemplaren.
    - Voor extra beschikbaarheid kunt u [beschikbaarheidszones](../availability-zones/az-overview.md) gebruiken om VM-exemplaren in een schaalset automatisch te verdelen binnen één datacenter of in meerdere datacenters.

- **Automatisch schalen van de toepassing als de vraag naar resources verandert**
    - De vraag naar uw toepassing kan veranderen gedurende de dag of week. Om steeds in te spelen op deze wisselende vraag van klanten, kunnen schaalsets het aantal VM-exemplaren automatisch verhogen als de vraag toeneemt en vervolgens verlagen als de vraag weer afneemt.
    - Automatisch schalen betekent enerzijds dat er geen overbodige VM-exemplaren actief zijn om uw toepassing uit te voeren op momenten dat de vraag klein is, en anderzijds dat klanten ook een aanvaardbaar prestatieniveau krijgen wanneer de vraag toeneemt omdat er dan automatisch extra VM-exemplaren worden toegevoegd. Hierdoor is het mogelijk om de kosten te verlagen en op een efficiënte manier Azure-resources te maken zodra deze nodig zijn.

- **Werkt op grote schaal**
    - Schaalsets ondersteunen maximaal 1000 VM-exemplaren. Als u uw eigen aangepaste VM-installatiekopieën maakt en upload, is de limiet 600 VM-exemplaren.
    - Gebruik [Azure Managed Disks](../virtual-machines/managed-disks-overview.md) voor de beste prestaties met productieworkloads.


## <a name="differences-between-virtual-machines-and-scale-sets"></a>Verschillen tussen virtuele machines en schaalsets
Schaalsets zijn opgebouwd uit virtuele machines. Met schaalsets beschikt u automatisch over de beheer- en automatiseringslagen voor het uitvoeren en schalen van uw toepassingen. U kunt in plaats daarvan ook zelf afzonderlijke virtuele machines maken en beheren of bestaande hulpprogramma's integreren om een vergelijkbaar automatiseringsniveau te bereiken. In de volgende tabel worden de voordelen van schaalsets beschreven ten opzichte van het handmatig beheren van meerdere VM-exemplaren.

| Scenario                           | Handmatig samengestelde groep VM's                                                                    | Schaalset voor virtuele machines |
|------------------------------------|----------------------------------------------------------------------------------------|---------------------------|
| Aanvullende VM-exemplaren toevoegen        | Handmatige proces voor maken, configureren en garanderen van naleving                             | Automatisch maken op basis van centrale configuratie |
| Lastenverdeling en distributie van verkeer | Handmatig proces voor maken en configureren van Azure load balancer of Application Gateway      | Automatisch maken en integreren met Azure load balancer of Application Gateway |
| Hoge beschikbaarheid en redundantie   | Handmatig maken van beschikbaarheidsset of VM's distribueren en traceren tussen beschikbaarheidszones | Automatische distributie van VM-exemplaren over beschikbaarheidszones of beschikbaarheidssets |
| Schalen van VM's                     | Handmatige controle en Azure Automation                                                 | Automatisch schalen op basis van metrische gegevens van de host, metrische gegevens van de gast, Application Insights of schema |

Er zijn geen extra kosten verbonden aan het gebruik van schaalsets. U betaalt alleen voor de onderliggende rekenresources, zoals de VM-exemplaren, load balancer of opslag van beheerde schijven. Er worden naast het gebruik van de VM's geen extra kosten in rekening gebracht voor de beheer- en automatiseringsfuncties, zoals automatisch schalen en redundantie.

## <a name="how-to-monitor-your-scale-sets"></a>Uw schaalsets bewaken

Gebruik [Azure Monitor voor VM's](../azure-monitor/vm/vminsights-overview.md). Deze tool heeft een eenvoudig voorbereidingsproces en automatiseert de verzameling van belangrijke prestatiemeteritems voor CPU, geheugen, schijf en netwerk uit de virtuele machines in uw schaalset. De tool bevat ook extra bewakingsmogelijkheden en vooraf gedefinieerde visualisaties waarmee u zich kunt concentreren op de beschikbaarheid en prestaties van uw schaalsets.

Schakel bewaking in voor uw [toepassing voor de schaalset voor virtuele machines](../azure-monitor/app/azure-vm-vmss-apps.md) met Application Insights om gedetailleerde informatie te verzamelen over uw toepassing, waaronder paginaweergaven, toepassingsaanvragen en uitzonderingen. Controleer de beschikbaarheid van uw toepassing door een [beschikbaarheidstest ](../azure-monitor/app/monitor-web-app-availability.md) te configureren om gebruikersverkeer te simuleren.

## <a name="data-residency"></a>Gegevenslocatie

In Azure is de functie om het opslaan van klantgegevens in één regio in te schakelen, momenteel alleen beschikbaar in de regio Azië - zuidoost (Singapore) van het geografisch gebied Azië en Stille Oceaan en in Brazilië - zuid van het geografisch gebied Brazilië (staat Sao Paulo). Voor alle andere regio's worden klantgegevens opgeslagen in Geo. Zie [Trust Center](https://azuredatacentermap.azurewebsites.net/) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
U kunt nu aan de slag door uw eerste schaalset voor virtuele machines te maken in Azure Portal.

> [!div class="nextstepaction"]
> [Een schaalset maken in Azure Portal](quick-create-portal.md)
