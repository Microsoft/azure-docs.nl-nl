---
title: Servicequota en -limieten
description: Meer informatie over standaard Azure Batch quota's, limieten en beperkingen, en het aanvragen van quotum verhogingen
ms.topic: conceptual
ms.date: 04/06/2021
ms.custom: seodec18
ms.openlocfilehash: 6e17a90cc573205bcb964a0428e0b7320323b8a6
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553546"
---
# <a name="batch-service-quotas-and-limits"></a>Quota en limieten voor Batch-service

Net als bij andere Azure-Services gelden er limieten voor bepaalde resources die zijn gekoppeld aan de batch-service. Veel van deze limieten zijn standaard quota's die worden toegepast door Azure op het niveau van de abonnement of het account.

Houd bij het ontwerpen en opschalen van uw batch-workloads de volgende quota's in acht. Als uw pool bijvoorbeeld niet het doel aantal reken knooppunten bereikt dat u hebt opgegeven, hebt u mogelijk de kern quotum limiet voor uw batch-account bereikt.

U kunt in één batch-account meerdere batch-workloads uitvoeren of uw workloads verdelen over batch-accounts die zich in hetzelfde abonnement bevinden, maar in verschillende Azure-regio's.

Als u in batch productie werkbelastingen wilt uitvoeren, moet u mogelijk een of meer van de quota's boven de standaard waarde verhogen. Als u een quotum wilt verhogen, kunt u gratis [een quotum verhoging aanvragen](#increase-a-quota) .

## <a name="resource-quotas"></a>Resourcequota

Een quotum is een limiet, geen capaciteits garantie. Neem contact op met de ondersteuning van Azure als u de capaciteits behoeften op grote schaal hebt.

Houd er ook rekening mee dat quota's geen gegarandeerde waarden zijn. Quota kunnen variëren op basis van wijzigingen van de batch-service of een gebruikers aanvraag om een quotum waarde te wijzigen.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="core-quotas"></a>Kern quota

### <a name="cores-quotas-in-batch-service-mode"></a>Kernen quota's in de batch-service modus

Er bestaan kern quota's voor elke VM-serie die door batch wordt ondersteund. Deze kern quota worden weer gegeven op de pagina **quota's** in het Azure Portal. De quotum limieten van de VM-serie kunnen worden bijgewerkt met een ondersteunings aanvraag, zoals hieronder wordt beschreven. Voor toegewezen knoop punten dwingt batch een kern quotum limiet af voor elke VM-reeks, evenals een totale kern quotum limiet voor het hele batch-account. Voor knoop punten met een lage prioriteit wordt met batch alleen een totaal kern quotum voor het batch-account afgedwongen zonder onderscheid tussen de verschillende VM-reeksen.

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

Deze aanvullende limieten worden ingesteld door de batch-service. In tegens telling tot [resource quota](#resource-quotas)kunnen deze waarden niet worden gewijzigd.

| **Resource** | **Maximum limiet** |
| --- | --- |
| [Gelijktijdige taken](batch-parallel-node-tasks.md) per reken knooppunt | 4 x het aantal knooppunt kernen |
| [Toepassingen](batch-application-packages.md) per batch-account | 20 |
| Toepassings pakketten per toepassing | 40 |
| Toepassings pakketten per pool | 10 |
| Maximale levens duur van taken | 180 dagen<sup>1</sup> |
| [Koppelingen](virtual-file-mount.md) per Compute-knoop punt | 10 |
| Certificaten per pool | 12 |

<sup>1</sup> de maximale levens duur van een taak, van wanneer deze wordt toegevoegd aan de taak wanneer deze is voltooid, is 180 dagen. Voltooide taken blijven zeven dagen geldig; gegevens voor taken die niet binnen de maximale levens duur zijn voltooid, zijn niet toegankelijk.

## <a name="view-batch-quotas"></a>Batch quota's weer geven

U kunt als volgt uw batch-account quota's weer geven in de [Azure Portal](https://portal.azure.com):

1. Selecteer **batch-accounts** en selecteer vervolgens het batch-account waarin u bent geïnteresseerd.
1. Selecteer **quota's** in het menu van het batch-account.
1. De quota's weer geven die momenteel zijn toegepast op het batch-account.

:::image type="content" source="./media/batch-quota-limit/account-quota-portal.png" alt-text="Scherm opname van de batch-account quota's in het Azure Portal.":::

## <a name="increase-a-quota"></a>Een quotum verhogen

U kunt een quota verhoging aanvragen voor uw batch-account of uw abonnement met behulp van de [Azure Portal](https://portal.azure.com) of door gebruik te maken van de [Azure-quotum rest API](#azure-quota-rest-api).

Het type quotum toename is afhankelijk van de pool toewijzings modus van uw batch-account. Als u een quotum toename wilt aanvragen, moet u de VM-reeks toevoegen waarvoor u het quotum wilt verhogen. Wanneer de quota toename wordt toegepast, wordt deze toegepast op alle reeksen Vm's.

Zodra u uw ondersteunings aanvraag hebt ingediend, neemt de ondersteuning van Azure contact met u op. Quota aanvragen kunnen binnen een paar minuten of Maxi maal twee werk dagen worden voltooid.

### <a name="azure-portal"></a>Azure Portal

1. Selecteer op de pagina **quota's** de optie **quotum toename aanvragen**. U kunt ook de tegel **Help en ondersteuning** selecteren in het dash board van de portal (of van het vraag teken (**?**) in de rechter bovenhoek van de portal) en vervolgens een **nieuwe ondersteunings aanvraag selecteren.**

1. In **Basisbeginselen**:

    1. Selecteer voor **probleem type** **service-en abonnements limieten (quota's)**.
    1. Selecteer uw abonnement.
    1. Selecteer **batch** bij **quotum type**.
    1. Selecteer **Volgende** om door te gaan.

1. In **Details**:

    1. Geef in de sectie **Details** opgeven de locatie, het quotum type en het batch-account op (indien van toepassing) en selecteer vervolgens de quota (s) die u wilt verg Roten.

       :::image type="content" source="media/batch-quota-limit/quota-increase.png" alt-text="Scherm afbeelding van het venster met quotum details bij het aanvragen van een quota verhoging.":::

       De volgende quota typen zijn beschikbaar:

       - **Per batch-account**  
         Gebruik deze optie om quotum verhogingen te vragen die specifiek zijn voor één batch-account, inclusief specifieke kernen met een lage prioriteit en het aantal taken en Pools.

         Als u deze optie selecteert, geeft u het batch-account op waarop deze aanvraag moet worden toegepast en selecteert u vervolgens de quota (s) die u wilt bijwerken. Geef de nieuwe limiet op die u voor elke resource wilt aanvragen.

         Quota met lage prioriteit is één waarde in alle VM-reeksen. Als u beperkte Sku's nodig hebt, moet u **kernen met een lage prioriteit** selecteren en de te aanvragen VM-families toevoegen.

       - **Alle accounts in deze regio**  
         Gebruik deze optie om quotum verhogingen te vragen die van toepassing zijn op alle batch-accounts in een regio, zoals het aantal batch-accounts per regio per abonnement.

    1. Selecteer in de **ondersteunings methode** een **Ernst** op basis van uw [bedrijfs impact](https://aka.ms/supportseverity) en de gewenste contact wijze en ondersteunings taal van uw voor keur.

    1. Controleer en voer bij **contact gegevens** de vereiste contact gegevens in.

1. Selecteer **controleren + maken** en selecteer vervolgens **maken** om de ondersteunings aanvraag in te dienen.

### <a name="azure-quota-rest-api"></a>Azure-quotum REST API

U kunt de Azure-quotum REST API gebruiken om een quotum verhoging te vragen op het abonnements niveau of op het niveau van de batch-account.

Zie [een quotum verhoging aanvragen met behulp van de Ondersteunings rest API voor Azure](/rest/api/support/quota-payload#azure-batch)voor meer informatie en voor beelden.

## <a name="related-quotas-for-vm-pools"></a>Gerelateerde quota's voor VM-groepen

[Batch-Pools in de virtuele-machine configuratie die is geïmplementeerd in een virtueel Azure-netwerk](batch-virtual-network.md) , worden automatisch extra Azure-netwerk resources toegewezen. Deze resources worden gemaakt in het abonnement dat het virtuele netwerk bevat dat is opgegeven bij het maken van de batch-pool.

De volgende resources worden gemaakt voor elk 100-groeps knooppunt in een virtueel netwerk:

- Eén [netwerk beveiligings groep](../virtual-network/network-security-groups-overview.md#network-security-groups)
- Eén [openbaar IP-adres](../virtual-network/public-ip-addresses.md)
- Een [Load Balancer](../load-balancer/load-balancer-overview.md)

De beperkingen die voor deze resources gelden, worden bepaald door de [resourcequota](../azure-resource-manager/management/azure-subscription-service-limits.md) van het abonnement. Als u implementaties van grote groepen plant in een virtueel netwerk, moet u mogelijk een quotum verhoging aanvragen voor een of meer van deze resources.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [Werkstroom van de batch-service en primaire resources](batch-service-workflow-features.md) als pools, knooppunten, jobs en taken.
- Meer informatie over [Azure-abonnement en service limieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md).