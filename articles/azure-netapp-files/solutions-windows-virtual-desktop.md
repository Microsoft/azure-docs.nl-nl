---
title: Virtuele Windows-bureau blad gebruiken met Azure NetApp Files | Microsoft Docs
description: Biedt best practice richt lijnen en voorbeeld blauw drukken bij het implementeren van Windows virtueel bureau blad met Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: b-juche
ms.openlocfilehash: 0cd1f6210fbdb74e3fd511150157dccca3e92dda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91932461"
---
# <a name="benefits-of-using-azure-netapp-files-with-windows-virtual-desktop"></a>Voordelen van het gebruik van Azure NetApp Files met Windows Virtual Desktop 

Dit artikel bevat best practice richt lijnen voor het implementeren van Windows virtueel bureau blad (WVD) met Azure NetApp Files.

Azure NetApp Files is een zeer krachtige service voor bestands opslag van Azure. Het kan Maxi maal 450.000 IOPS en submilliseconde latentie bieden, zodat extreem grote schaal van implementaties van virtuele Windows-Bureau bladen kan worden ondersteund. U kunt de band breedte aanpassen en het service niveau van uw Azure NetApp Files volumes op aanvraag bijna onmiddellijk wijzigen zonder dat er IO wordt onderbroken terwijl de toegang tot het gegevens vlak wordt behouden. Met deze mogelijkheid kunt u de schaal van uw WVD-implementatie eenvoudig optimaliseren voor kosten. U kunt ook ruimte-efficiënte volume momentopnamen maken zonder dat dit van invloed is op de prestaties van het volume. Op deze manier kunt u afzonderlijke [containers voor FSLogix-gebruikers profielen](../virtual-desktop/store-fslogix-profile.md) terugdraaien via een kopie van de `~snapshot` Directory, of om het hele volume direct terug te draaien via de mogelijkheid om het volume te herstellen.  Met Maxi maal 255 (rotatie) moment opnamen om een volume te beschermen tegen gegevens verlies of-beschadiging, hebben beheerders veel kans op ongedaan maken wat er is gebeurd.

## <a name="sample-blueprints"></a>Voorbeeld blauw drukken

In het volgende voor beeld blauw drukken wordt de integratie van virtueel bureau blad van Windows met Azure NetApp Files weer gegeven. In een gegroepd bureau blad worden gebruikers omgeleid naar de beste beschik bare sessie (de [breedte-eerste modus](../virtual-desktop/host-pool-load-balancing.md#breadth-first-load-balancing-method)) in de groep, met behulp van [virtuele machines met meerdere sessies](../virtual-desktop/windows-10-multisession-faq.md#what-is-windows-10-enterprise-multi-session). Aan de andere kant zijn persoonlijke bureau bladen gereserveerd voor scenario's waarin elke gebruiker hun eigen virtuele machine heeft.

### <a name="pooled-desktop-scenario"></a>Gegroepeerde bureaublad scenario

Voor het gegroepeerde scenario [beveelt](/windows-server/remote/remote-desktop-services/virtual-machine-recs#multi-session-recommendations) het virtueel bureau blad-team van Windows de volgende richt lijnen aan op vCPU. Houd er rekening mee dat in deze aanbeveling geen grootte van de virtuele machine is opgegeven.

|     Type werk belasting     |     Licht    |     Normaal    |     Zwaar    |
|-----------------------|--------------|---------------|--------------|
|     Gebruikers per vCPU    |     6        |     4         |     2        |


Deze aanbeveling wordt bevestigd door een 500-gebruiker LoginVSI-test, logboek registratie ongeveer 62 ' kennis/middel grote gebruikers ' op elke virtuele machine van D16as_V4. 

Een voor beeld: bij 62 gebruikers per D16as_V4 virtuele machine kan Azure NetApp Files eenvoudig 60.000 gebruikers per omgeving ondersteunen. Testen om de bovengrens van de virtuele machine van D32as_v4 te evalueren. Als de aanbeveling WVD gebruiker per vCPU is ingesteld op True voor de D32as_v4, zouden meer dan 120.000 gebruikers binnen 1.000 virtuele machines passen voordat broaching [de 1.000 IP VNet-limiet](./azure-netapp-files-network-topologies.md), zoals wordt weer gegeven in de volgende afbeelding.  

![Windows-bureau blad met virtueel bureau blad-bureaublad scenario](../media/azure-netapp-files/solutions-pooled-desktop-scenario.png)   

### <a name="personal-desktop-scenario"></a>Persoonlijk bureaublad scenario 

In een persoonlijk bureaublad scenario toont de volgende afbeelding de aanbeveling van de architectuur voor algemeen gebruik. Gebruikers worden toegewezen aan specifiek bureau blad en elke Pod heeft net onder 1.000 virtuele machines, waardoor er ruimte is voor IP-adressen die worden door gegeven vanuit het beheer-VNet. Azure NetApp Files kunt op eenvoudige wijze 900 + persoonlijke bureau bladen per VNet van een hostgroep met één sessie afhandelen, waarbij het werkelijke aantal virtuele machines gelijk is aan 1.000 min het aantal clusterhosts dat is gevonden in de hub VNet. Als er meer persoonlijke bureau bladen nodig zijn, is het eenvoudig om vaker (hostgroepen en virtuele netwerken) toe te voegen, zoals wordt weer gegeven in de volgende afbeelding. 

![Windows virtueel bureau blad persoonlijk bureau blad-scenario](../media/azure-netapp-files/solutions-personal-desktop-scenario.png)  

Bij het bouwen van een op POD gebaseerde architectuur is het belang rijk om gebruikers toe te wijzen aan de juiste pod bij het aanmelden, om te voor komen dat gebruikers hun gebruikers profielen altijd vinden. 

## <a name="next-steps"></a>Volgende stappen

- [Oplossingsarchitecturen op basis van Azure NetApp Files](azure-netapp-files-solution-architectures.md)