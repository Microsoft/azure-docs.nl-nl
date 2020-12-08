---
title: Resources en quota's beheren
titleSuffix: Azure Machine Learning
description: Meer informatie over de quota's en limieten voor resources voor Azure Machine Learning en hoe u quotum verhogingen kunt aanvragen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 12/1/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperfq4, contperfq2
ms.openlocfilehash: 30859593e240c4143dc298cff446ce8bc116a993
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780583"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-machine-learning"></a>Quota voor resources beheren en verg Roten met Azure Machine Learning

Azure gebruikt limieten en quota's om budget overschrijdingen door fraude te voor komen en om te voldoen aan de Azure-capaciteits beperkingen. Houd rekening met deze beperkingen als u schaalt voor werk belastingen voor productie. In dit artikel vindt u meer informatie over:

> [!div class="checklist"]
> + Standaard limieten op Azure-resources met betrekking tot [Azure machine learning](overview-what-is-azure-ml.md).
> + Quota's op werkruimte niveau maken.
> + Uw quota's en limieten weer geven.
> + Het aanvragen van quota wordt verhoogd.
> + Persoonlijk eind punt en DNS-quota.

Naast het beheren van quota's, leert u hoe u de [kosten voor Azure machine learning kunt plannen en beheren](concept-plan-manage-cost.md).

## <a name="special-considerations"></a>Speciale overwegingen

+ Een quotum is een krediet limiet, geen capaciteits garantie. Als u grote capaciteits behoeften hebt, [neemt u contact op met de ondersteuning van Azure om uw quotum te verhogen](#request-quota-increases).

+ Een quotum wordt gedeeld door alle services in uw abonnementen, met inbegrip van Azure Machine Learning. Bereken het gebruik over alle services als u de capaciteit evalueert.
 
  Azure Machine Learning Compute is een uitzonde ring. Het heeft een afzonderlijke quota van het core Compute-quotum. 

+ De standaard limieten variëren per aanbiedings categorie type, zoals een gratis proef versie, een betalen per gebruik-en virtuele-machine (VM)-serie (zoals dv2, F en G).

## <a name="default-resource-quotas"></a>Standaard resource quota

In deze sectie vindt u informatie over de standaard-en maximum quotum limieten voor de volgende resources:

+ Azure Machine Learning assets
  + Azure Machine Learning compute
  + Azure Machine Learning pijp lijnen
+ Virtuele machines
+ Azure Container Instances
+ Azure Storage

> [!IMPORTANT]
> Limieten zijn onderhevig aan wijzigingen. Zie [Azure-abonnement en service limieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md) voor alle Azure voor de meest recente informatie.

### <a name="azure-machine-learning-assets"></a>Azure Machine Learning assets
De volgende limieten gelden voor assets per werk ruimte. 

| **Resource** | **Maximumlimiet** |
| --- | --- |
| Gegevenssets | 10 miljoen |
| Wordt uitgevoerd | 10 miljoen |
| Modellen | 10 miljoen|
| Artifacts | 10 miljoen |

Daarnaast is de maximale **uitvoerings tijd** 30 dagen en het maximum aantal **metrische gegevens dat per run wordt geregistreerd** , 1.000.000.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
[Azure machine learning Compute](concept-compute-target.md#azure-machine-learning-compute-managed) heeft een standaard quotum limiet op het aantal kernen (gesplitst door elke VM-serie en cumulatieve totale kernen), evenals het aantal unieke reken resources dat per regio in een abonnement is toegestaan. Dit quotum is gescheiden van het VM-kern quotum dat wordt weer gegeven in de vorige sectie, aangezien het alleen van toepassing is op beheerde reken resources van Azure Machine Learning.

[Vraag een quotum toename](#request-quota-increases) aan om de limieten voor verschillende kern quota van de VM-serie, het totale aantal quota's en bronnen van het abonnement in deze sectie te verhogen.

Beschik bare resources:
+ **Toegewezen kern geheugens per regio** hebben een standaard limiet van 24 tot 300, afhankelijk van het type abonnement. U kunt het aantal toegewijde kernen per abonnement verg Roten voor elke VM-serie. Gespecialiseerde VM-families zoals NCv2, NCv3, of ND Series beginnen met een standaard waarde van nul kernen.

+ **Kernen met een lage prioriteit per regio** hebben een standaard limiet van 100 tot 3.000, afhankelijk van het type abonnement. Het aantal kernen met lage prioriteit per abonnement kan worden verhoogd en is één waarde voor alle VM-families.

+ **Clusters per regio** hebben een standaard limiet van 200. Deze worden gedeeld tussen een trainings cluster en een reken instantie. (Een reken instantie wordt beschouwd als een cluster met één knoop punt voor quotum doeleinden.)

> [!TIP]
> Bekijk de [grootte van virtuele machines in azure](https://docs.microsoft.com/azure/virtual-machines/sizes)voor meer informatie over de VM-serie waarvoor een quotum verhoging moet worden aangevraagd. Voor instance GPU VM-families beginnen met een ' N ' in hun familie naam (bijvoorbeeld NCv3-serie)

In de volgende tabel worden extra limieten in het platform weer gegeven. Neem contact op met het AzureML-product team via een **technische** ondersteunings ticket om een uitzonde ring aan te vragen.

| **Resource of actie** | **Maximumlimiet** |
| --- | --- |
| Werk ruimten per resource groep | 800 |
| Knoop punten in een AmlCompute- **cluster** installatie (single Azure machine learning Compute) als een niet-communicatie voorziening (d.w.z. kan geen mpi-taken uitvoeren) | 100-knoop punten, maar kunnen worden geconfigureerd met Maxi maal 65000 knoop punten |
| Knoop punten in één parallelle uitvoerings stap worden **uitgevoerd** op een AmlCompute-cluster (Azure machine learning Compute) | 100-knoop punten, maar kunnen worden geconfigureerd met Maxi maal 65000 knoop punten als het cluster zo is ingesteld dat het kan worden geschaald. |
| Knoop punten in een AmlCompute- **cluster** installatie (single Azure machine learning Compute) als een groep met communicatie mogelijkheden | 300-knoop punten, maar kunnen worden geconfigureerd met Maxi maal 4000 knoop punten |
| Knoop punten in een AmlCompute- **cluster** installatie (single Azure machine learning Compute) als een groep met communicatie mogelijkheden voor een VM-serie met RDMA-functionaliteit | 100 knoop punten |
| Knoop punten in één MPI worden **uitgevoerd** op een AmlCompute-cluster (Azure machine learning Compute) | 100-knoop punten, maar kunnen worden verhoogd tot 300-knoop punten |
| GPU MPI-processen per knoop punt | 1-4 |
| GPU-werk nemers per knoop punt | 1-4 |
| Taak levensduur | 21 dagen<sup>1</sup> |
| Levens duur van taken op een knoop punt met een lage prioriteit | 7 dagen<sup>2</sup> |
| Parameter servers per knoop punt | 1 |

de maximale levens duur van <sup>1</sup> is de duur tussen het moment waarop een uitvoering wordt gestart en wanneer deze is voltooid. Voltooide uitvoeringen blijven voor onbepaalde tijd oneindig. Gegevens voor uitvoeringen die niet binnen de maximale levens duur zijn voltooid, zijn niet toegankelijk.
<sup>2</sup> taken op een knoop punt met een lage prioriteit kunnen worden afgebroken wanneer er een capaciteits beperking is. U wordt aangeraden om controle punten in uw taak te implementeren.

#### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning pijp lijnen
[Azure machine learning pijp lijnen](concept-ml-pipelines.md) hebben de volgende limieten.

| **Resource** | **Limiet** |
| --- | --- |
| Stappen in pijplijn | 30.000 |
| Werk ruimten per resource groep | 800 |

### <a name="virtual-machines"></a>Virtuele machines
Elk Azure-abonnement heeft een limiet voor het aantal virtuele machines in alle services. De kernen van virtuele machines hebben een regionale limiet en een regionale limiet per grootte reeks. Beide limieten worden afzonderlijk afgedwongen.

Neem bijvoorbeeld een abonnement met een limiet van 30 VM-cores voor US - oost, een limiet van 30 cores voor de A-serie en een limiet van 30 cores voor de D-serie. Dit abonnement mag 30 a1-vm's of 30 D1 Vm's implementeren, of een combi natie van de twee die niet meer dan 30 kernen overschrijdt.

U kunt geen limieten genereren voor virtuele machines boven de waarden die in de volgende tabel worden weer gegeven.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="container-instances"></a>Container Instances

Zie [container instances limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#container-instances-limits)voor meer informatie.

### <a name="storage"></a>Storage
Azure Storage heeft een limiet van 250 opslag accounts per regio, per abonnement. Deze limiet is inclusief standaard-en Premium-opslag accounts.

Als u de limiet wilt verhogen, kunt u een aanvraag indienen via [ondersteuning voor Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). Het Azure Storage team controleert uw aanvraag en kan Maxi maal 250 opslag accounts voor een regio goed keuren.


## <a name="workspace-level-quotas"></a>Quota op werkruimte niveau

Quota op werk ruimte niveau gebruiken om de toewijzing van Azure Machine Learning Compute-doelen te beheren tussen meerdere [werk ruimten](concept-workspace.md) in hetzelfde abonnement.

Standaard delen alle werk ruimten dezelfde quota als het quotum op abonnements niveau voor VM-families. U kunt echter een maximum quotum instellen voor afzonderlijke VM-families in werk ruimten in een abonnement. Dit biedt u de mogelijkheid capaciteit te delen en problemen met bronnen te voor komen.

1. Ga naar een werk ruimte in uw abonnement.
1. Selecteer in het linkerdeel venster de optie **gebruik en quota's**.
1. Selecteer het tabblad **Quota's configureren** om de quota's weer te geven.
1. Vouw een VM-serie uit.
1. Stel een quotum limiet in voor elke werk ruimte die wordt vermeld onder die VM-serie.

U kunt geen negatieve waarde of een hogere waarde dan het quotum op abonnements niveau instellen.

[![Scherm afbeelding met een Azure Machine Learning quotum op werkruimte niveau.](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)

> [!NOTE]
> U hebt machtigingen op abonnements niveau nodig om een quotum op het niveau van de werk ruimte in te stellen.

## <a name="view-your-usage-and-quotas"></a>Uw gebruik en quota's weer geven

Als u uw quotum voor verschillende Azure-resources, zoals virtuele machines, opslag of netwerk, wilt weer geven, gebruikt u de Azure Portal:

1. Selecteer in het linkerdeel venster **alle services** en selecteer vervolgens **abonnementen** onder de categorie **Algemeen** .

2. Selecteer in de lijst met abonnementen het abonnement waarvoor u het quotum zoekt.

3. Selecteer **gebruik + quota's** om uw huidige quotum limieten en gebruik te bekijken. Gebruik de filters om de provider en locaties te selecteren. 

U beheert het Azure Machine Learning Reken quotum voor uw abonnement afzonderlijk van andere Azure-quota's: 

1. Ga naar uw **Azure machine learning** -werk ruimte in de Azure Portal.

2. Selecteer in het linkerdeel venster, in de sectie **ondersteuning en probleem oplossing** , de optie **gebruik en quota's** om uw huidige quotum limieten en het gebruik weer te geven.

3. Selecteer een abonnement om de quotum limieten weer te geven. Filter op de regio waarin u bent geïnteresseerd.

4. U kunt scha kelen tussen een weer gave op abonnements niveau en een weer gave op werk ruimte niveau.

## <a name="request-quota-increases"></a>Verhogingen van quotum aanvragen

Als u de limiet of het quotum boven de standaard limiet wilt verhogen, opent u gratis [een online aanvraag voor klant ondersteuning](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) .

U kunt limieten boven de maximum waarden die in de voor gaande tabellen worden weer gegeven, niet verhogen. Als er geen limiet is, kunt u de limiet voor de resource niet aanpassen.

Wanneer u een quotum toename wilt aanvragen, selecteert u de gewenste service. Selecteer bijvoorbeeld Azure Machine Learning, Container Instances of opslag. Voor Azure Machine Learning compute, kunt u de knop **aanvraag quotum** selecteren tijdens het weer geven van het quotum in de voor gaande stappen.

> [!NOTE]
> [Gratis proef abonnementen](https://azure.microsoft.com/offers/ms-azr-0044p) komen niet in aanmerking voor limiet of quotum verhoging. Als u een gratis proef abonnement hebt, kunt u een upgrade uitvoeren naar een abonnement met [betalen per gebruik](https://azure.microsoft.com/offers/ms-azr-0003p/) . Zie [gratis proef versie van Azure upgraden naar betalen per gebruik](../cost-management-billing/manage/upgrade-azure-subscription.md) en [gratis Azure-account](https://azure.microsoft.com/free/free-account-faq)voor meer informatie.

## <a name="private-endpoint-and-private-dns-quota-increases"></a>Quotumverhogingen voor Privé-eindpunt en Privé-DNS

Er gelden limieten voor het aantal privé-eind punten en persoonlijke DNS-zones dat u kunt maken in een abonnement.

Azure Machine Learning maakt resources in uw (klant)-abonnement, maar sommige scenario's maken resources in een micro soft-abonnement.

 In de volgende scenario's moet u mogelijk een quotum toelage aanvragen in het micro soft-abonnement:

* Een persoonlijke Azure-koppelings werkruimte met een door de klant beheerde sleutel (CMK)
* Azure Container Registry voor de werkruimte achter uw virtuele netwerk
* Een Azure Kubernetes-servicecluster met Private Link koppelen aan uw werkruimte

Als u een limiet wilt aanvragen voor deze scenario's, gebruikt u de volgende stappen:

1. [Maak een ondersteunings aanvraag voor Azure](../azure-portal/supportability/how-to-create-azure-support-request.md#create-a-support-request) en selecteer de volgende opties in de sectie __basis beginselen__ :

    | Veld | Selectie |
    | ----- | ----- |
    | Type probleem | **Technisch** |
    | Service | **Mijn services**. Selecteer vervolgens __machine learning__ in de vervolg keuzelijst. |
    | Probleem type | **Werkruimte configuratie en-beveiliging** |
    | Subtype van probleem | **Aanvraag voor het privé-eind punt en de Privé-DNS zone** |

2. Gebruik in de sectie __Details__ het veld __Beschrijving__ om de Azure-regio en het scenario te bieden dat u wilt gebruiken. Als u quotum verhogingen moet aanvragen voor meerdere abonnementen, vermeldt u de abonnement-Id's in dit veld.

3. Selecteer __maken__ om de aanvraag te maken.

:::image type="content" source="media/how-to-manage-quotas/quota-increase-private-endpoint.png" alt-text="Scherm afbeelding van een verg Roten aanvraag voor een persoonlijk eind punt en een privé-DNS-quotum.":::

## <a name="next-steps"></a>Volgende stappen

+ [Kosten plannen en beheren voor Azure Machine Learning](concept-plan-manage-cost.md)
