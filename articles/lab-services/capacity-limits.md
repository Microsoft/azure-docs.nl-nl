---
title: Capaciteits limieten in Azure Lab Services
description: Meer informatie over capaciteits limieten (limieten voor virtuele machines) in Azure Lab Services.
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: 9866628cd11ec8df67e6fe16ae8806f0f30ae9a1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94491015"
---
# <a name="capacity-limits-in-azure-lab-services"></a>Capaciteits limieten in Azure Lab Services
Azure Lab Services heeft standaard capaciteits limieten voor Azure-abonnementen om te voldoen aan de limieten voor Azure Compute-quota en om fraude te verhelpen. Alle Azure-abonnementen hebben een aanvankelijke capaciteits limiet die kan variëren op basis van het abonnements type, het aantal standaard Compute-kernen en GPU-kernen die beschikbaar zijn in Azure Lab Services. Zo beperkt u het aantal virtuele machines dat u in uw Labs kunt maken voordat u een limiet voor het beperken van aanvragen nodig hebt.  

Als u de limiet voor de kernen van de virtuele machine van uw abonnement dicht of hebt bereikt, worden er berichten van Azure Lab Services weer gegeven wanneer u probeert acties uit te voeren waarmee extra virtuele machines worden gemaakt. Bijvoorbeeld: 

- Een lab maken
- Een Lab publiceren
- Lab-capaciteit aanpassen om meer virtuele machines toe te voegen aan een bestaand Lab

Deze acties kunnen ook worden uitgeschakeld als u de limiet voor kern geheugenen al hebt bereikt. 

![Kern limieten-waarschuwings bericht](./media/capacity-limits/warning-message.png)

## <a name="subscriptions-with-default-limit-of-zero-cores"></a>Abonnementen met een standaard limiet van nul kernen
Bepaalde typen zeldzame abonnementen die vaker voor fraude worden gebruikt, kunnen een standaard limiet hebben van 0 standaard kernen en 0 GPU-kernen. Als u een van deze typen abonnementen gebruikt, moet de beheerder die uw Lab-account maakt, een limiet verhoging aanvragen voordat u Azure Lab Services kunt gebruiken. 

De beheerder kan deze stappen volgen voor het aanvragen van een limiet verhoging:  

1.  [Maak een Lab-account](tutorial-setup-lab-account.md)in uw abonnement.
2.  Klik op de pagina **overzicht** van het lab-account op de knop **limiet verhogen** aan de bovenkant. 
3.  Volg de stappen in het formulier om een ondersteunings aanvraag in te dienen om de limiet te verhogen.

## <a name="request-a-limit-increase"></a>Een verhoging van de limiet aanvragen
Als de limiet voor kern geheugens is bereikt, kunt u een verhoging van de limiet aanvragen om Azure Lab Services te blijven gebruiken. Het aanvraag proces is een controle punt om ervoor te zorgen dat uw abonnement niet betrokken is bij fraude of onbedoelde, plotselinge implementaties met een grote schaal.

De berichten over de limieten van de kernen van virtuele machines in de Azure Lab Services portal bevatten een koppeling om een limiet voor het aantal aanvragen te verg Roten. Met de koppeling wordt een nieuw browser tabblad geopend, waar u een nieuwe ondersteunings aanvraag kunt maken. De gegevens voor het type probleem, het abonnement en het quotum type worden automatisch ingevuld, zoals wordt weer gegeven in de volgende afbeelding: 

![Nieuwe ondersteuningsaanvraag](./media/capacity-limits/new-support-request.png)


Vervolgens wordt u gevraagd om meer informatie op te geven over de limiet verhoging. Geef in het veld **Beschrijving** de volgende gegevens op:

- Wat u probeert te doen (bijvoorbeeld het maken van een Lab voor het leren van een computer wetenschappen-klasse, het uitvoeren van een hackathon, enzovoort)
- Grootte van de virtuele machine die u voor dit Lab gebruikt
- Aantal virtuele machines dat u nodig hebt

Zodra u het ondersteunings verzoek hebt ingediend, worden de aanvragen gecontroleerd. Als dat nodig is, nemen we contact met u op om meer informatie te krijgen. 

## <a name="next-steps"></a>Volgende stappen
Raadpleeg het volgende artikel:
- [Beheerders handleiding: VM-grootte](administrator-guide.md#vm-sizing).
- [Veelgestelde vragen](classroom-labs-faq.md).