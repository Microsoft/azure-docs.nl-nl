---
title: Servicequota en -limieten
description: Meer informatie over standaard Azure Batch quota's, limieten en beperkingen, en het aanvragen van quotum verhogingen
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: b2039794a0c8a13070c9d81b83869ca4097bd02e
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325962"
---
# <a name="batch-service-quotas-and-limits"></a>Quota en limieten voor Batch-service

Net als bij andere Azure-Services gelden er limieten voor bepaalde resources die zijn gekoppeld aan de batch-service. Veel van deze limieten zijn standaard quota's die worden toegepast door Azure op het niveau van de abonnement of het account.

Houd bij het ontwerpen en opschalen van uw batch-workloads de volgende quota's in acht. Als uw pool bijvoorbeeld niet het doel aantal reken knooppunten bereikt dat u hebt opgegeven, hebt u mogelijk de kern quotum limiet voor uw batch-account bereikt.

U kunt in één batch-account meerdere batch-workloads uitvoeren of uw workloads verdelen over batch-accounts die zich in hetzelfde abonnement bevinden, maar in verschillende Azure-regio's.

Als u in batch productie werkbelastingen wilt uitvoeren, moet u mogelijk een of meer van de quota's boven de standaard waarde verhogen. Als u een quotum wilt verhogen, kunt u gratis een online [klant ondersteunings aanvraag](#increase-a-quota) openen.

## <a name="resource-quotas"></a>Resourcequota

Een quotum is een limiet, geen capaciteits garantie. Neem contact op met de ondersteuning van Azure als u de capaciteits behoeften op grote schaal hebt.

Houd er ook rekening mee dat quota's geen gegarandeerde waarden zijn. Quota kunnen variëren op basis van wijzigingen van de batch-service of een gebruikers aanvraag om een quotum waarde te wijzigen.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="core-quotas"></a>Kern quota

### <a name="cores-quotas-in-batch-service-mode"></a>Kernen quota's in de batch-service modus

Het afdwingen van specifieke kern quota wordt verbeterd, waarbij de wijzigingen in fasen beschikbaar worden gemaakt en voor alle batch-accounts aan het eind december 2020 worden uitgevoerd.

Er bestaan kern quota's voor elke VM-serie die wordt ondersteund door batch en die worden weer gegeven op de pagina **quota's** in de portal. De quotum limieten van de VM-serie kunnen worden bijgewerkt met een ondersteunings aanvraag, zoals hieronder wordt beschreven.

Als het bestaande mechanisme is uitgeschakeld, worden de quotum limieten voor VM-reeksen niet gecontroleerd; alleen de totale quotum limiet voor het account wordt afgedwongen. Dit betekent dat het mogelijk is om meer kernen toe te wijzen voor een VM-serie dan wordt aangegeven door het quotum van de VM-reeks, tot aan de limiet voor het totale account quotum.

Het bijgewerkte mechanisme dwingt de VM-reeks quota's naast het totale account quotum af. Als onderdeel van de overgang naar het nieuwe mechanisme kunnen de quota waarden van de VM-reeks worden bijgewerkt om toewijzings fouten te voor komen. voor elke VM-serie die in de afgelopen maanden wordt gebruikt, wordt het quotum van de VM-reeks bijgewerkt zodat deze overeenkomt met het totale account quotum. Met deze wijziging wordt het gebruik van meer capaciteit niet ingeschakeld dan al beschikbaar was.

Het is mogelijk om te bepalen of het quotum voor het afdwingen van VM-Series is ingeschakeld voor een batch-account door het volgende te controleren:

* De [dedicatedCoreQuotaPerVMFamilyEnforced](/rest/api/batchmanagement/batchaccount/get#batchaccount) API-eigenschap van het batch-account.

* Tekst op de pagina **quota** van de batch-account in de portal.

### <a name="cores-quotas-in-user-subscription-mode"></a>Kernen quota's in de modus gebruikers abonnement

Als u een [batch-account](accounts.md) hebt gemaakt met de groeps toewijzings modus ingesteld op **gebruikers abonnement**, worden batch-vm's en andere resources rechtstreeks in uw abonnement gemaakt wanneer een groep wordt gemaakt of waarvan het formaat wordt gewijzigd. De kern quota van Azure Batch zijn niet van toepassing en de quota's in uw abonnement voor regionale reken kernen, reken kernen per serie en andere bronnen worden gebruikt en afgedwongen.

Zie [Azure-abonnement en service limieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md)voor meer informatie over deze quota's.

## <a name="pool-size-limits"></a>Limieten voor groeps grootte

Limieten voor groeps grootte worden ingesteld door de batch-service. In tegens telling tot [resource quota](#resource-quotas)kunnen deze waarden niet worden gewijzigd. Alleen groepen met communicatie tussen knoop punten en aangepaste installatie kopieën hebben andere beperkingen dan het standaard quotum.

| **Resource** | **Maximum limiet** |
| --- | --- |
| **Reken knooppunten in de [groep communicatie tussen knoop](batch-mpi.md) punten**  ||
| Pool toewijzings modus Batch-service | 100 |
| Groeps toewijzings modus Batch-abonnement | 80 |
| **Reken knooppunten in [de groep die zijn gemaakt met een beheerde installatie kopie bron](batch-custom-images.md)**<sup>1</sup> ||
| Toegewezen knooppunten | 2000 |
| Knooppunten met lage prioriteit | 1000 |

<sup>1</sup> voor groepen waarvoor geen communicatie tussen knoop punten is ingeschakeld.

## <a name="other-limits"></a>Andere limieten

Aanvullende limieten die zijn ingesteld door de batch-service. In tegens telling tot [resource quota](#resource-quotas)kunnen deze waarden niet worden gewijzigd.

| **Resource** | **Maximum limiet** |
| --- | --- |
| [Gelijktijdige taken](batch-parallel-node-tasks.md) per reken knooppunt | 4 x het aantal knooppunt kernen |
| [Toepassingen](batch-application-packages.md) per batch-account | 20 |
| Toepassings pakketten per toepassing | 40 |
| Toepassings pakketten per pool | 10 |
| Maximale levens duur van taken | 180 dagen<sup>1</sup> |
| [Koppelingen](virtual-file-mount.md) per Compute-knoop punt | 10 |

<sup>1</sup> de maximale levens duur van een taak, van wanneer deze wordt toegevoegd aan de taak wanneer deze is voltooid, is 180 dagen. Voltooide taken blijven zeven dagen geldig; gegevens voor taken die niet binnen de maximale levens duur zijn voltooid, zijn niet toegankelijk.

## <a name="view-batch-quotas"></a>Batch quota's weer geven

U kunt als volgt uw batch-account quota's weer geven in de [Azure Portal](https://portal.azure.com):

1. Selecteer **batch-accounts** en selecteer vervolgens het batch-account waarin u bent geïnteresseerd.
1. Selecteer **quota's** in het menu van het batch-account.
1. De quota's weer geven die momenteel zijn toegepast op het batch-account.

:::image type="content" source="./media/batch-quota-limit/account-quota-portal.png" alt-text="Quota voor batch-account":::

## <a name="increase-a-quota"></a>Een quotum verhogen

U kunt een quota verhoging aanvragen voor uw batch-account of uw abonnement met behulp van de [Azure Portal](https://portal.azure.com). Het type quotum toename is afhankelijk van de pool toewijzings modus van uw batch-account. Als u een quotum toename wilt aanvragen, moet u de VM-reeks toevoegen waarvoor u het quotum wilt verhogen. Wanneer de quota toename wordt toegepast, wordt deze toegepast op alle reeksen Vm's.

1. Selecteer de tegel **Help + ondersteuning** in het dash board van de portal of het vraag teken (**?**) in de rechter bovenhoek van de portal.
1. Selecteer basis principes **nieuwe ondersteunings aanvraag**  >  **Basics**.
1. In de **basis beginselen**:
   
    1. **Probleem type**  >  **Service-en abonnements limieten (quota's)**
   
    1. Selecteer uw abonnement.
   
    1. **Quotum type**  >  **Batch**
      
       Selecteer **Next**.
    
1. In **Details**:
      
    1. Geef in **Details** opgeven de locatie, het quotum type en het batch-account op.
    
       ![Verhoging van batch quotum][quota_increase]

       De volgende quota typen zijn beschikbaar:

       * **Per batch-account**  
         Waarden die specifiek zijn voor één batch-account, inclusief specifieke kernen met een lage prioriteit en het aantal taken en Pools.
        
       * **Per regio**  
         Waarden die van toepassing zijn op alle batch-accounts in een regio en bevat het aantal batch-accounts per regio per abonnement.

       Quota met lage prioriteit is één waarde in alle VM-reeksen. Als u beperkte Sku's nodig hebt, moet u **kernen met een lage prioriteit** selecteren en de te aanvragen VM-families toevoegen.

    1. Selecteer een **Ernst** op basis van uw [bedrijfs impact](https://aka.ms/supportseverity).

       Selecteer **Next**.

1. Bij **contact gegevens**:
   
    1. Selecteer een **Voorkeurs methode voor contact personen**.
   
    1. Controleer de vereiste contact gegevens en geef deze op.
   
       Selecteer **maken** om de ondersteunings aanvraag in te dienen.

Zodra u uw ondersteunings aanvraag hebt ingediend, neemt de ondersteuning van Azure contact met u op. Quota aanvragen kunnen binnen een paar minuten of Maxi maal twee werk dagen worden voltooid.

## <a name="related-quotas-for-vm-pools"></a>Gerelateerde quota's voor VM-groepen

Batch-Pools in de virtuele-machine configuratie die is geïmplementeerd in een virtueel Azure-netwerk, worden automatisch extra Azure-netwerk resources toegewezen. De volgende resources zijn nodig voor elk 50 pool-knoop punten in een virtueel netwerk:

- Eén [netwerk beveiligings groep](../virtual-network/network-security-groups-overview.md#network-security-groups)
- Eén [openbaar IP-adres](../virtual-network/public-ip-addresses.md)
- Een [Load Balancer](../load-balancer/load-balancer-overview.md)

Deze resources worden toegewezen in het abonnement met het virtuele netwerk dat is geleverd bij het maken van de batch-pool. De beperkingen die voor deze resources gelden, worden bepaald door de [resourcequota](../azure-resource-manager/management/azure-subscription-service-limits.md) van het abonnement. Als u implementaties van grote groepen plant in een virtueel netwerk, controleert u de quota van het abonnement voor deze resources. Vraag, indien nodig, een verhoging van de Azure Portal aan door **Help en ondersteuning** te selecteren.

## <a name="next-steps"></a>Volgende stappen

* [Maak een Azure batch-account met behulp van de Azure Portal](batch-account-create-portal.md).
* Meer informatie over de [Werkstroom van de batch-service en primaire resources](batch-service-workflow-features.md) als pools, knooppunten, jobs en taken.
* Meer informatie over [Azure-abonnement en service limieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md).

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
[quota_increase]: ./media/batch-quota-limit/quota-increase.png