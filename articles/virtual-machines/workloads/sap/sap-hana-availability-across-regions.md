---
title: Beschik baarheid SAP HANA over Azure-regio's | Microsoft Docs
description: Een overzicht van de beschik baarheid van overwegingen bij het uitvoeren van SAP HANA op virtuele Azure-machines in meerdere Azure-regio's.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 799034d9060e8afe101e86e7b98ac7e01c28d191
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019307"
---
# <a name="sap-hana-availability-across-azure-regions"></a>Beschik baarheid van SAP HANA in azure-regio's

In dit artikel worden scenario's beschreven met betrekking tot de beschik baarheid van SAP HANA in verschillende Azure-regio's. Vanwege de afstand tussen Azure-regio's is het instellen van SAP HANA Beschik baarheid in meerdere Azure-regio's speciale overwegingen.

## <a name="why-deploy-across-multiple-azure-regions"></a>Waarom implementeren in meerdere Azure-regio's

Azure-regio's worden vaak gescheiden door grote afstanden. Afhankelijk van de geopolitieke regio, kan de afstand tussen Azure-regio's honderden kilo meters of zelfs duizenden kilo meters zijn, zoals in de Verenigde Staten. Vanwege de afstand heeft het netwerk verkeer tussen assets die in twee verschillende Azure-regio's worden geïmplementeerd, een aanzienlijke vertraging in de netwerk retour latentie. De latentie is significant genoeg om synchrone gegevens uitwisseling tussen twee SAP HANA instanties onder normale SAP-workloads uit te sluiten. 

Anderzijds hebben organisaties vaak een afstands vereiste tussen de locatie van het primaire Data Center en een secundair Data Center. Een afstands vereiste helpt de beschik baarheid te bieden als er sprake is van een natuur ramp in een bredere geografische locatie. Voor beelden zijn de orkanen die het Caribisch gebied en Florida in september en oktober 2017 hebben bereikt. Uw organisatie heeft mogelijk ten minste een minimum afstand vereist. Voor de meeste Azure-klanten moet u een definitie voor een minimum afstand ontwerpen voor de beschik baarheid in [Azure-regio's](https://azure.microsoft.com/regions/). Omdat de afstand tussen twee Azure-regio's te groot is om de HANA synchrone replicatie modus te kunnen gebruiken, kunnen RTO-en RPO-vereisten ertoe leiden dat u beschikbaarheids configuraties implementeert in één regio en vervolgens aanvullen met aanvullende implementaties in een tweede regio.

Een ander aspect om in dit scenario te overwegen is een failover-en client omleiding. De veronderstelling is dat een failover tussen SAP HANA exemplaren in twee verschillende Azure-regio's altijd een hand matige failover is. Omdat de replicatie modus van SAP HANA systeem replicatie is ingesteld op asynchroon, is er een potentieel dat gegevens die zijn vastgelegd in het primaire HANA-exemplaar, nog niet zijn gemaakt in het secundaire HANA-exemplaar. Automatische failover is daarom geen optie voor configuraties waarbij replicatie asynchroon is. Zelfs als er hand matig een failover is uitgevoerd, moet u, net als bij een failover-oefening, maat regelen nemen om ervoor te zorgen dat alle vastgelegde gegevens op de primaire zijde het secundaire exemplaar hebben gemaakt voordat u hand matig overstapt naar de andere Azure-regio.
 
Azure Virtual Network maakt gebruik van een ander IP-adres bereik. De IP-adressen worden in de tweede Azure-regio geïmplementeerd. U moet dus de configuratie van de SAP HANA-client wijzigen of u moet stappen maken om de naam omzetting te wijzigen. Op deze manier worden de clients omgeleid naar het IP-adres van de nieuwe secundaire site server. Zie voor meer informatie het SAP-artikel [client verbindings herstel na overname](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html).   

## <a name="simple-availability-between-two-azure-regions"></a>Eenvoudige Beschik baarheid tussen twee Azure-regio's

U kunt ervoor kiezen geen beschikbaarheids configuratie in te stellen binnen één regio, maar wel over de vraag om de werk belasting op te nemen als er sprake is van een nood geval. Typische gevallen voor dergelijke scenario's zijn systemen die niet van het product zijn. Hoewel het systeem gedurende een halve dag of zelfs een dag duurzaam is, kunt u niet toestaan dat het systeem 48 uur of langer niet beschikbaar is. Als u de installatie minder kostbaar wilt maken, voert u een ander systeem uit dat nog minder belang rijk is in de virtuele machine. Het andere systeem functioneert als een bestemming. U kunt ook de grootte van de virtuele machine in de secundaire regio kleiner maken en ervoor kiezen om de gegevens niet vooraf te laden. Omdat de failover hand matig is en veel meer stappen nodig hebben om een failover uit te voeren voor de volledige toepassings stack, de extra tijd om de virtuele machine af te sluiten, het formaat ervan te wijzigen en de virtuele machine opnieuw op te starten is acceptabel.

Als u het scenario voor het gebruik van het DR-doel met een QA-systeem in één VM gebruikt, moet u rekening houden met de volgende overwegingen:

- Er zijn twee [bewerkings modi](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html) met delta_datashipping en logreplay, die beschikbaar zijn voor een dergelijk scenario
- Beide bewerkings modi hebben andere geheugen vereisten zonder gegevens vooraf te laden
- Delta_datashipping kan mogelijk aanzienlijk minder geheugen vereisen zonder de optie preload dan logreplay kan vereisen. Zie hoofd stuk 4,3 van het SAP-document een [systeem replicatie uitvoeren voor SAP Hana](https://archive.sap.com/kmuuid2/9049e009-b717-3110-ccbd-e14c277d84a3/How%20to%20Perform%20System%20Replication%20for%20SAP%20HANA.pdf)
- De bewerkings modus voor het geheugen van de logreplay zonder vooraf laden is niet deterministisch en is afhankelijk van de geladen column Store-structuren. In uitzonderlijke gevallen is het mogelijk dat er 50% van het geheugen van de primaire instantie nodig is. Het geheugen voor de bewerkings modus logreplay is onafhankelijk van het feit of u ervoor hebt gekozen om de vooraf geladen gegevens in te stellen of niet.


![Diagram van twee virtuele machines in twee regio's](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> In deze configuratie kunt u geen RPO = 0 opgeven, omdat de HANA-systeem replicatie modus asynchroon is. Als u een RPO = 0 moet opgeven, is deze configuratie niet de configuratie van de keuze.

Een kleine wijziging die u in de configuratie kunt aanbrengen, is het mogelijk om gegevens te configureren als vooraf laden. Gezien de hand matige aard van de failover en het feit dat toepassings lagen ook moeten worden verplaatst naar de tweede regio, is het wellicht niet zinvol om gegevens vooraf te laden. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>Beschik baarheid in één regio en in verschillende regio's combi neren 

Een combi natie van Beschik baarheid binnen en tussen regio's kan worden aangestuurd door de volgende factoren:

- Een vereiste van RPO = 0 binnen een Azure-regio.
- De organisatie is niet bereid of kan globale bewerkingen hebben die worden beïnvloed door een grote natuurlijke rampen die invloed heeft op een grotere regio. Dit was het geval voor sommige orkanen die het Caribisch gebied in de afgelopen jaren hebben bereikt.
- Voor Schriften die de afstand tussen de primaire en secundaire sites vereisen die duidelijk zijn voor wat de Azure-beschikbaarheids zones zijn.

In dergelijke gevallen kunt u instellen wat SAP een [SAP Hana configuratie met een systeem replicatie](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) met meerdere lagen aanroept door gebruik te maken van de replicatie van Hana-systemen. De architectuur ziet er als volgt uit:

![Diagram van drie virtuele machines in twee regio's](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

SAP heeft [multi-TARGET-systeem replicatie](https://help.sap.com/viewer/42668af650f84f9384a3337bcd373692/2.0.03/en-US/0b2c70836865414a8c65463180d18fec.html) geïntroduceerd met Hana 2,0 SPS3. Multi-TARGET-systeem replicatie biedt enkele voor delen in update scenario's. De DR-site (regio 2) wordt bijvoorbeeld niet beïnvloed wanneer de secundaire HA-site niet beschikbaar is voor onderhoud of updates. U kunt [hier](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/ba457510958241889a459e606bbcf3d3.html)meer informatie vinden over Hana-replicatie van meerdere doel systemen.
Mogelijke architectuur met replicatie met meerdere doelen zou er als volgt uitzien:

![Diagram van drie virtuele machines in twee regio's milti-doel](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_3VMs.PNG)

Als de organisatie vereisten heeft voor de gereedheid voor hoge Beschik baarheid in de tweede Azure-regio (DR), ziet de architectuur er als volgt uit:

![Diagram waarin een organisatie wordt weer gegeven met vereisten voor de gereedheid voor hoge Beschik baarheid in de tweede (DR) Azure-regio.](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_4VMs.PNG)


Met behulp van logreplay als bewerkings modus levert deze configuratie een RPO = 0, met een lage RTO binnen de primaire regio. De configuratie biedt ook een goede RPO als er sprake is van een overgang naar de tweede regio. De RTO-tijden in de tweede regio zijn afhankelijk van het feit of gegevens vooraf worden geladen. Veel klanten gebruiken de virtuele machine in de secundaire regio om een test systeem uit te voeren. In dat geval kan de gegevens niet vooraf worden geladen.

> [!IMPORTANT]
> De bewerkings modi tussen de verschillende lagen moeten homo geen zijn. U **kunt logreply niet** gebruiken als bewerkings modus tussen laag 1 en laag 2 en delta_datashipping naar de leverings niveau 3. U kunt alleen de ene of de andere bewerkings modus kiezen die consistent moet zijn voor alle lagen. Omdat delta_datashipping niet geschikt is om u een RPO te geven = 0, blijft de enige redelijke bewerkings modus voor een configuratie met meerdere lagen logreplay. Voor meer informatie over bewerkings modi en enkele beperkingen raadpleegt u de SAP-artikel [bewerkings modi voor SAP Hana systeem replicatie](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html). 

## <a name="next-steps"></a>Volgende stappen

Ga voor stapsgewijze instructies voor het instellen van deze configuraties in azure naar:

- [SAP HANA systeem replicatie in virtuele Azure-machines instellen](sap-hana-high-availability.md)
- [Hoge Beschik baarheid voor SAP HANA met behulp van systeem replicatie](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
