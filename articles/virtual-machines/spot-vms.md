---
title: Azure Spot Virtual Machines gebruiken
description: Meer informatie over het gebruik van Azure Spot Virtual Machines om kosten op te slaan.
author: JagVeerappan
ms.author: jagaveer
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/05/2020
ms.reviewer: cynthn
ms.openlocfilehash: fb53fc37227e040ed7bd7fc8e47de9aed538bc2e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104721389"
---
# <a name="use-azure-spot-virtual-machines"></a>Azure Spot Virtual Machines gebruiken 

Met behulp van Azure Spot Virtual Machines kunt u profiteren van onze ongebruikte capaciteit tegen een aanzienlijke kosten besparing. Op elk moment dat Azure de capaciteit nodig heeft, verwijdert de Azure-infra structuur Azure Spot Virtual Machines. Daarom zijn Azure Spot Virtual Machines geweldig voor workloads die onderbrekingen kunnen afhandelen, zoals batch verwerkings taken, ontwikkel-en test omgevingen, grootschalige werk belastingen en meer.

De hoeveelheid beschik bare capaciteit kan variëren op basis van grootte, regio, tijd van de dag en meer. Wanneer u Azure Spot Virtual Machines implementeert, worden de Vm's door Azure toegewezen als er capaciteit beschikbaar is, maar er is geen SLA voor deze Vm's. Een Azure spot-virtuele machine biedt geen garanties voor hoge Beschik baarheid. Op elk moment dat Azure de capaciteit nodig heeft, verwijdert de Azure-infra structuur Azure Spot Virtual Machines met een kennisgeving van dertig seconden. 


## <a name="eviction-policy"></a>Verwijderingsbeleid

Vm's kunnen worden verwijderd op basis van de capaciteit of de maximale prijs die u hebt ingesteld. Wanneer u een virtuele machine van Azure spot maakt, kunt u het verwijderings beleid instellen op *toewijzing* (standaard) of *verwijderen*. 

Het beleid voor het ongedaan maken van de *toewijzing* verplaatst de virtuele machine naar de status stopped-deallocated, zodat u deze later opnieuw kunt implementeren. Er is echter geen garantie dat de toewijzing slaagt. De toegewezen Vm's worden geteld bij uw quotum en er worden kosten in rekening gebracht voor de onderliggende schijven. 

Als u wilt dat de virtuele machine wordt verwijderd wanneer deze wordt gewist, kunt u het verwijderings beleid instellen op *verwijderen*. De verwijderde Vm's worden samen met de onderliggende schijven verwijderd, zodat er geen kosten in rekening worden gebracht voor de opslag. 

U kunt zich aanmelden om in-VM-meldingen te ontvangen via [Azure Scheduled Events](./linux/scheduled-events.md). Hiermee wordt u op de hoogte gesteld als uw Vm's worden verwijderd en u 30 seconden hebt om taken te volt ooien en afsluit taken uit te voeren vóór de verwijdering. 


| Optie | Resultaat |
|--------|---------|
| De maximale prijs is ingesteld op >= de huidige prijs. | VM wordt geïmplementeerd als de capaciteit en het quotum beschikbaar zijn. |
| De maximale prijs is ingesteld op < van de huidige prijs. | De virtuele machine is niet geïmplementeerd. Er wordt een fout bericht weer gegeven dat de maximum prijs >= huidige prijs moet zijn. |
| Een virtuele machine voor stoppen/toewijzing opnieuw starten als de maximum prijs >= de huidige prijs | Als er sprake is van capaciteit en quotum, wordt de VM geïmplementeerd. |
| Een virtuele machine voor stoppen/toewijzing opnieuw starten als de maximum prijs < de huidige prijs is | Er wordt een fout bericht weer gegeven dat de maximum prijs >= huidige prijs moet zijn. | 
| De prijs voor de virtuele machine is voltooid en is nu > de maximum prijs. | De virtuele machine wordt verwijderd. U krijgt een 30s-melding vóór de werkelijke verwijdering. | 
| Nadat de prijs voor de virtuele machine is verwijderd, wordt deze weer < de maximum prijs. | De virtuele machine wordt niet automatisch opnieuw gestart. U kunt de virtuele machine zelf opnieuw opstarten en er worden kosten in rekening gebracht voor de huidige prijs. |
| Als de maximum prijs is ingesteld op `-1` | De virtuele machine wordt om prijs redenen niet verwijderd. De maximale prijs is de huidige prijs, tot de prijs voor standaard-Vm's. Er worden nooit kosten in rekening gebracht boven de standaard prijs.| 
| De maximum prijs wijzigen | U moet de toewijzing van de virtuele machine ongedaan maken om de maximale prijs te wijzigen. U kunt de toewijzing van de virtuele machine ongedaan maken, een nieuwe maximum prijs instellen en vervolgens de virtuele machine bijwerken. |


## <a name="limitations"></a>Beperkingen

De volgende VM-grootten worden niet ondersteund voor Azure Spot Virtual Machines:
 - B-serie
 - Promotie versies van elke grootte (zoals dv2, NV, NC, H promotie grootten)

Azure Spot Virtual Machines kan worden geïmplementeerd in elke regio, met uitzonde ring Microsoft Azure China 21Vianet.

<a name="channel"></a>

De volgende [aanbiedings typen](https://azure.microsoft.com/support/legal/offer-details/) worden momenteel ondersteund:

-   Enterprise Agreement 
-   Betalen per gebruik-aanbiedings code (003P)
-   Gesponsord (0036P en 0136P)
- Neem contact op met uw partner voor Cloud service provider (CSP)


## <a name="pricing"></a>Prijzen

Prijzen voor Azure Spot Virtual Machines is variabel, op basis van de regio en de SKU. Zie prijzen voor VM'S voor [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) en [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)voor meer informatie. 

U kunt ook een query uitvoeren op prijs informatie met behulp van de [Azure retail prijs API](/rest/api/cost-management/retail-prices/azure-retail-prices) om te zoeken naar informatie over de prijzen. De `meterName` en `skuName` zijn beide opgenomen `Spot` .

Met variabele prijzen kunt u een maximum prijs instellen, in Amerikaanse dollars (USD), met Maxi maal vijf decimalen. De waarde `0.98765` is bijvoorbeeld een maximum prijs van $0,98765 USD per uur. Als u de maximale prijs instelt op `-1` , wordt de VM niet verwijderd op basis van de prijs. De prijs voor de virtuele machine is de huidige prijs voor steun of de prijs voor een standaard-VM, die ooit kleiner is, zolang er capaciteit en quota beschikbaar zijn.

## <a name="pricing-and-eviction-history"></a>Prijs-en verwijderings geschiedenis

U kunt historische prijzen en verwijderings tarieven weer geven per grootte in een regio in de portal. Selecteer **prijs geschiedenis weer geven en vergelijk prijzen in nabijgelegen regio's** om een tabel of grafiek met prijzen voor een bepaalde grootte te bekijken.  De prijzen en verwijderings tarieven in de volgende afbeeldingen zijn alleen voor beelden. 

**Grafiek**:

:::image type="content" source="./media/spot-chart.png" alt-text="Scherm afbeelding van de regio opties met het verschil in prijs-en verwijderings tarieven als diagram.":::

**Tabel**:

:::image type="content" source="./media/spot-table.png" alt-text="Scherm afbeelding van de regio opties met het verschil in prijs-en verwijderings tarieven als een tabel.":::



##  <a name="frequently-asked-questions"></a>Veelgestelde vragen

**V:** Eenmaal gemaakt, is de virtuele machine van Azure op dezelfde manier als de normale standaard-VM?

**A:** Ja, behalve als er geen SLA voor Azure Spot Virtual Machines is en deze op elk gewenst moment kunnen worden verwijderd.


**V:** Wat te doen wanneer u weggaat, maar nog steeds capaciteit nodig heeft?

**A:** U wordt aangeraden standaard Vm's te gebruiken in plaats van Azure Spot Virtual Machines als u capaciteit direct nodig hebt.


**V:** Hoe wordt het quotum beheerd voor Azure Spot Virtual Machines?

**A:** Azure Spot Virtual Machines heeft een afzonderlijke quota groep. Het steun quotum wordt gedeeld tussen Vm's en scale-set-exemplaren. Zie [Azure-abonnement- en servicelimieten, quota en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md) voor meer informatie.


**V:** Kan ik een extra quotum voor Azure Spot Virtual Machines aanvragen?

**A:** Ja, u kunt de aanvraag indienen om uw quotum voor Azure Spot Virtual Machines te verhogen via het [standaard quotum aanvraag proces](../azure-portal/supportability/per-vm-quota-requests.md).


**V:** Waar kan ik vragen plaatsen?

**A:** U kunt uw vraag met `azure-spot` op [Q&A](/answers/topics/azure-spot.html)plaatsen en labelen. 


**V:** Hoe kan ik de maximum prijs voor een steun-VM wijzigen?

**A:** Voordat u de maximum prijs kunt wijzigen, moet u de toewijzing van de virtuele machine ongedaan maken. Vervolgens kunt u de maximum prijs wijzigen in de portal, vanuit de **configuratie** sectie voor de virtuele machine. 

## <a name="next-steps"></a>Volgende stappen
Gebruik de [cli](./linux/spot-cli.md), [Portal](spot-portal.md), [arm-sjabloon](./linux/spot-template.md)of [Power shell](./windows/spot-powershell.md) om Azure spot virtual machines te implementeren.

U kunt ook een [schaalset implementeren met Azure spot-exemplaren van virtuele machines](../virtual-machine-scale-sets/use-spot.md).

Als er een fout optreedt, raadpleegt u [fout codes](./error-codes-spot.md).
