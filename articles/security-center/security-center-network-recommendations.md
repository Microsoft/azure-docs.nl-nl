---
title: Uw netwerk bronnen beveiligen in Azure Security Center
description: Dit document bevat aanbevelingen in Azure Security Center waarmee u uw Azure-netwerk bronnen kunt beveiligen en blijven voldoen aan het beveiligings beleid.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: memildin
ms.openlocfilehash: c24e3473c07759aa256a077d11edb20c616629f6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102439507"
---
# <a name="protect-your-network-resources"></a>Uw netwerk bronnen beveiligen
Azure Security Center de beveiligings status van uw Azure-resources voortdurend geanalyseerd op de aanbevolen procedures voor netwerk beveiliging. Wanneer Security Center mogelijke beveiligings problemen identificeert, worden er aanbevelingen gemaakt die u door het proces van het configureren van de benodigde besturings elementen leiden om uw resources te beschermen en te beveiligen.

Zie [aanbevelingen voor netwerken](recommendations-reference.md#recs-networking)voor een volledige lijst met aanbevelingen voor netwerken.

In dit artikel worden aanbevelingen gedaan die van toepassing zijn op uw Azure-resources op basis van een netwerk beveiligings perspectief. Aanbevelingen voor netwerken rond de volgende generatie firewalls, netwerk beveiligings groepen, JIT-VM-toegang, meer regels voor binnenkomend verkeer, en meer. Zie [beveiligings aanbevelingen beheren in azure Security Center](security-center-recommendations.md)voor een lijst met aanbevelingen en herstel acties voor netwerken.

De **netwerk** functies van Security Center zijn onder andere: 

- Netwerk toewijzing (vereist Azure Defender)
- [Adaptieve netwerk beveiliging](security-center-adaptive-network-hardening.md) (vereist Azure Defender)
- Aanbevelingen voor netwerk beveiliging
 
## <a name="view-your-networking-resources-and-their-recommendations"></a>Uw netwerk bronnen en hun aanbevelingen weer geven

Gebruik op de [pagina Asset Inventory](asset-inventory.md)het filter Resource type om de netwerk resources te selecteren die u wilt onderzoeken:

:::image type="content" source="./media/security-center-network-recommendations/network-filters-inventory.png" alt-text="Netwerk resource typen activa inventaris" lightbox="./media/security-center-network-recommendations/network-filters-inventory.png":::


## <a name="network-map"></a>Netwerkoverzicht

De interactieve netwerk kaart biedt een grafische weer gave met beveiligings-overlays waarmee u aanbevelingen en inzichten krijgt voor het versterken van uw netwerk bronnen. Met de kaart ziet u de netwerk topologie van uw Azure-workloads, verbindingen tussen uw virtuele machines en subnetten, en de mogelijkheid om in te zoomen op de kaart in specifieke bronnen en de aanbevelingen voor deze resources.

De netwerk kaart openen:

1. Open in het menu van Security Center het Azure Defender-dash board en selecteer **netwerk overzicht**.

    :::image type="content" source="./media/security-center-network-recommendations/opening-network-map.png" alt-text="De netwerk kaart openen vanuit het Azure Defender-dash board" lightbox="./media/security-center-network-recommendations/opening-network-map.png":::

1. Selecteer in het menu **lagen** de optie **topologie** kiezen.
 
De standaard weergave van de topologie kaart geeft het volgende weer:

- Abonnementen die u hebt geselecteerd in Azure. De kaart ondersteunt meerdere abonnementen.
- Vm's, subnetten en VNets van het Resource Manager-resource type (klassieke Azure-resources worden niet ondersteund)
- Peered VNets
- Alleen bronnen met [netwerk aanbevelingen](security-center-recommendations.md) met een hoge of gemiddelde Ernst  
- Internet gerichte bronnen
- De kaart is geoptimaliseerd voor de abonnementen die u hebt geselecteerd in Azure. Als u uw selectie wijzigt, wordt de kaart opnieuw berekend en opnieuw geoptimaliseerd op basis van de nieuwe instellingen.  

[![Netwerk topologie kaart](./media/security-center-network-recommendations/network-map-info.png)](./media/security-center-network-recommendations/network-map-info.png#lightbox)

## <a name="understanding-the-network-map"></a>Informatie over de netwerk kaart

De netwerk kaart kan uw Azure-resources weer geven in een **topologie** weergave en een **verkeers** weergave. 

### <a name="the-topology-view"></a>De topologie weergave

In de weer gave **topologie** van de netwerk kaart kunt u de volgende inzichten over uw netwerk bronnen bekijken:

- In de binnenste cirkel ziet u alle Vnets binnen de geselecteerde abonnementen, de volgende cirkel is alle subnetten, de buitenste cirkel is alle virtuele machines.
- Met de lijnen waarmee de resources in de kaart worden verbonden, kunt u zien welke resources aan elkaar zijn gekoppeld en hoe uw Azure-netwerk is gestructureerd. 
- Gebruik de ernst indicatoren om snel een overzicht te krijgen van de resources die de aanbevelingen van Security Center hebben geopend.
- U kunt klikken op een van de resources om in te zoomen en de details van die resource en de bijbehorende aanbevelingen direct te bekijken, en in de context van de netwerk kaart.  
- Als er te veel resources worden weer gegeven op de kaart, maakt Azure Security Center gebruik van het eigen algoritme voor het slim clusteren van uw resources, het markeren van de resources die de meest kritieke status hebben en hebben de meeste aanbevelingen voor de ernst van belang. 

Omdat de kaart interactief en dynamisch is, wordt elk knoop punt kan worden geklikt en kan de weer gave worden gewijzigd op basis van de filters:

1. U kunt wijzigen wat u ziet op de netwerk kaart door de filters bovenaan te gebruiken. U kunt de kaart richten op basis van:

   -  **Beveiligings status**: u kunt de kaart filteren op basis van de ernst (hoog, gemiddeld, laag) van uw Azure-resources.
   - **Aanbevelingen**: u kunt selecteren welke resources worden weer gegeven op basis waarvan de aanbevelingen actief zijn op deze resources. U kunt bijvoorbeeld alleen resources weer geven waarvoor Security Center raadt u aan om netwerk beveiligings groepen in te scha kelen.
   - **Netwerk zones**: standaard worden in de kaart alleen Internet gerichte bronnen weer gegeven. u kunt ook interne vm's selecteren.
 
2. U kunt op elk gewenst moment op **Reset** klikken in de linkerbovenhoek om de standaard status van de kaart te herstellen.

Inzoomen op een resource:

1. Wanneer u een specifieke resource op de kaart selecteert, wordt het rechterdeel venster geopend en krijgt u algemene informatie over de resource, verbonden beveiligings oplossingen, indien aanwezig, en de aanbevelingen die relevant zijn voor de resource. Dit is hetzelfde type gedrag voor elk type resource dat u selecteert. 
2. Wanneer u de muis aanwijzer boven een knoop punt in de kaart houdt, kunt u algemene informatie weer geven over de resource, inclusief het abonnement, het resource type en de resource groep.
3. Gebruik de koppeling om in te zoomen op de knop Info en richt de kaart op dat specifieke knoop punt. 
4. Uitzoomen om de kaart van een specifiek knoop punt te verplaatsen.

### <a name="the-traffic-view"></a>De verkeers weergave

De **verkeers** weergave biedt u een overzicht van alle mogelijke verkeer tussen uw resources. Hiermee krijgt u een visuele kaart van alle regels die u hebt geconfigureerd voor het definiëren van de resources waarmee kan worden gecommuniceerd. Zo kunt u de bestaande configuratie van de netwerk beveiligings groepen bekijken en snel mogelijke Risk ante configuraties in uw workloads identificeren.

### <a name="uncover-unwanted-connections"></a>Ongewenste verbindingen opsporen

De kracht van deze weer gave is in de mogelijkheid om deze toegestane verbindingen weer te geven met de beveiligings problemen die bestaan, zodat u deze Kruis sectie van gegevens kunt gebruiken om de benodigde beveiliging op uw resources uit te voeren. 

Zo kunt u bijvoorbeeld twee computers detecteren waarvan u niet weet dat ze kunnen communiceren, waardoor u de werk belastingen en subnetten beter kunt isoleren.

### <a name="investigate-resources"></a>Resources onderzoeken

Inzoomen op een resource:

1. Wanneer u een specifieke resource op de kaart selecteert, wordt het rechterdeel venster geopend en krijgt u algemene informatie over de resource, verbonden beveiligings oplossingen, indien aanwezig, en de aanbevelingen die relevant zijn voor de resource. Dit is hetzelfde type gedrag voor elk type resource dat u selecteert. 
2. Klik op **verkeer** om de lijst met mogelijk uitgaand en binnenkomend verkeer op de resource weer te geven. Dit is een uitgebreide lijst met personen die kunnen communiceren met de resource en waarmee kan worden gecommuniceerd en via welke protocollen en poorten. Wanneer u bijvoorbeeld een VM selecteert, worden alle virtuele machines die met de virtuele machine kunnen communiceren, weer gegeven en wanneer u een subnet selecteert, worden alle subnetten weer gegeven waarmee deze kan communiceren.

**Deze gegevens zijn gebaseerd op de analyse van de netwerk beveiligings groepen en geavanceerde machine learning algoritmen waarmee meerdere regels worden geanalyseerd om inzicht te krijgen in hun crossovers en interacties.** 

[![Netwerk verkeer toewijzing](./media/security-center-network-recommendations/network-map-traffic.png)](./media/security-center-network-recommendations/network-map-traffic.png#lightbox)


## <a name="next-steps"></a>Volgende stappen

Zie het volgende voor meer informatie over de aanbevelingen die van toepassing zijn op andere Azure-resource typen:

- [Uw machines en toepassingen beveiligen in Azure Security Center](./asset-inventory.md)