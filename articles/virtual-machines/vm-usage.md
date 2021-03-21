---
title: Meer informatie over het gebruik van virtuele machines in Azure
description: Meer informatie over het gebruik van virtuele machines
services: virtual-machines
documentationcenter: ''
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 07/28/2020
ms.openlocfilehash: ba973bd5609dacf05eca842025d4e828d8a9f841
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550944"
---
# <a name="understanding-azure-virtual-machine-usage"></a>Meer informatie over het gebruik van virtuele machines in Azure
Door uw Azure-gebruiks gegevens te analyseren, kunnen er krachtige verbruiks inzichten worden verworven: inzichten waarmee betere kosten beheer en toewijzing in uw organisatie mogelijk zijn. In dit document vindt u meer informatie over uw Azure Compute-verbruik. Ga voor meer informatie over het algemene Azure-gebruik naar [inzicht in uw factuur](../cost-management-billing/understand/review-individual-bill.md).

## <a name="download-your-usage-details"></a>Uw gebruiksgegevens downloaden
[Down load uw gebruiks gegevens](../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal)om te beginnen. De onderstaande tabel bevat de definitie-en voorbeeld waarden voor het gebruik van Virtual Machines die via de Azure Resource Manager zijn geïmplementeerd. Dit document bevat geen gedetailleerde informatie voor virtuele machines die via ons klassieke model zijn geïmplementeerd.


| Veld | Betekenis | Voorbeeld waarden | 
|---|---|---|
| Gebruiksdatum | De datum waarop de resource is gebruikt | `11/23/2017` |
| Meter ID | Identificeert de service op het hoogste niveau waartoe dit gebruik behoort| `Virtual Machines`|
| Meter Sub-Category | De id van de gefactureerde meter. <br><br> Voor het gebruik van reken uren is er een meter voor elke VM-grootte plus OS (Windows, niet-Windows) en regio. <br><br> Voor het gebruik van Premium-software geldt er een meter voor elk software type. De meeste Premium software-installatie kopieën hebben verschillende meters voor elke kern grootte. Ga voor meer informatie naar de [pagina met prijzen voor berekeningen](https://azure.microsoft.com/pricing/details/virtual-machines/)</li></ul>| `2005544f-659d-49c9-9094-8e0aea1be3a5`|
| Meter Name| Dit is specifiek voor elke service in Azure. Voor Compute is het altijd Rekenuren.| `Compute Hours`|
| Meter Region| De datacenterlocatie voor bepaalde services waarbij de prijs is gebaseerd op de datacenterlocatie.|  `JA East`|
| Eenheid| Hiermee wordt de eenheid geïdentificeerd waarin de service wordt gefactureerd. Reken bronnen worden per uur gefactureerd.| `Hours`|
| Verbruikt| De hoeveelheid van de resource die voor die dag is verbruikt. Voor Compute worden voor elke minuut voor de VM een bepaald uur gefactureerd (Maxi maal 6 decimalen nauw keurigheid).| `1, 0.5`|
| Resource Location  | Hiermee wordt het datacenter geïdentificeerd waarop de resource wordt uitgevoerd.| `JA East`|
| Consumed Service | De Azure-platform service die u hebt gebruikt.| `Microsoft.Compute`|
| Resourcegroep | De resourcegroep waarin de geïmplementeerde resource wordt uitgevoerd. Zie [Azure Resource Manager Overview](../azure-resource-manager/management/overview.md) voor meer informatie.|`MyRG`|
| Instance ID | De id voor de resource. De id bevat de naam die u voor de resource opgeeft wanneer deze wordt gemaakt. Voor Vm's bevat de exemplaar-ID de SubscriptionId, ResourceGroupName en VMName (of de naam van de schaalset voor het gebruik van schaal sets).| `/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/ resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachines/MyVM1`<br><br>of<br><br>`/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/ resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachineScaleSets/MyVMSS1`|
| Tags| Label dat u toewijst aan de resource. Tags gebruiken om factureringsrecords te groeperen. Meer informatie over het coderen van uw Virtual Machines met behulp van de [cli](./tag-cli.md) of [Power shell](./tag-portal.md) dit is alleen beschikbaar voor virtuele machines van Resource Manager.| `{"myDepartment":"RD","myUser":"myName"}`|
| Aanvullende informatie | Servicespecifieke metagegevens. Voor Vm's vullen we de volgende gegevens in het veld aanvullende gegevens in: <br><br> Afbeeldings type-specifieke installatie kopie die u hebt uitgevoerd. Zoek de volledige lijst met ondersteunde teken reeksen onder afbeeldings typen.<br><br> Service type: de grootte die u hebt geïmplementeerd.<br><br> VMName: naam van de virtuele machine. Dit veld wordt alleen ingevuld voor virtuele machines met schaal sets. Als u de VM-naam voor virtuele machines van de schaalset nodig hebt, kunt u deze vinden in de bovenstaande teken reeks voor exemplaar-ID.<br><br> UsageType: Hiermee geeft u het type gebruik op dat dit vertegenwoordigt.<br><br> ComputeHR is het reken uren-gebruik voor de onderliggende VM, zoals Standard_D1_v2.<br><br> ComputeHR_SW is de Premium-software kosten als de virtuele machine Premium-software gebruikt, zoals Microsoft R Server. | Virtual Machines<br>`{"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR"}`<br><br>Virtuele-machineschaalsets<br> `{"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"myVM1", "UsageType":"ComputeHR"}`<br><br>Premium-software<br> `{"ImageType":"","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR_SW"}` |

## <a name="image-type"></a>Type installatiekopie
Voor sommige installatie kopieën in de Azure-galerie wordt het type afbeelding ingevuld in het veld aanvullende informatie. Hierdoor kunnen gebruikers begrijpen en bijhouden wat ze op hun virtuele machine hebben geïmplementeerd. De volgende waarden die in dit veld worden ingevuld op basis van de installatie kopie die u hebt geïmplementeerd:
- BitRock 
- Canonieke FreeBSD 
- Logica openen 
- Oracle 
- SLES voor SAP 
- SQL Server 14 preview op Windows Server 2012 R2 preview 
- SUSE
- SUSE Premium
- StorSimple Cloud Appliance 
- Red Hat
- Red Hat for SAP Business Applications     
- Red Hat for SAP HANA 
- Windows-client BYOL 
- Windows Server-BYOL 
- Windows Server-preview 

## <a name="service-type"></a>Servicetype
Het veld Service type in het veld aanvullende informatie komt overeen met de exacte VM-grootte die u hebt geïmplementeerd. Virtuele machines met Premium-opslag (SSD) en niet-Premium-opslag-Vm's (op basis van schijven) zijn hetzelfde als de prijs. Als u een op SSD gebaseerde grootte implementeert, zoals Standard \_ DS2 \_ v2, ziet u de grootte van niet-SSD ( `Standard\_D2\_v2 VM` ) in de meter Sub-Category kolom en de SSD-grootte ( `Standard\_DS2\_v2` ) in het veld aanvullende informatie.

## <a name="region-names"></a>Regio namen
De regio naam die is ingevuld in het veld Resource locatie in de gebruiks gegevens verschilt van de regio naam die wordt gebruikt in de Azure Resource Manager. Hier volgt een toewijzing tussen de regio waarden:

| **Regio naam Resource Manager** | **Resource locatie in gebruiks gegevens** |
|---|---|
| australiaeast |AU - oost|
| australiasoutheast | Australië - zuidoost|
| brazilsouth | BR - zuid|
| CanadaCentral | CA - centraal|
| CanadaEast | CA - oost|
| CentralIndia | IN - centraal|
| centralus | VS - centraal|
| chinaeast | China East|
| chinanorth | China - noord|
| eastasia | Azië - oost|
| eastus | VS - oost|
| eastus2 | VS - oost 2|
| GermanyCentral | DE - centraal|
| GermanyNortheast | DE - noordoost|
| japaneast | JA - oost|
| japanwest | JA - west|
| KoreaCentral | KR - centraal|
| KoreaSouth | KR - zuid|
| northcentralus | VS - noord-centraal|
| northeurope | Europa - noord|
| southcentralus | VS - zuid-centraal|
| southeastasia | Azië - zuidoost|
| SouthIndia | IN - zuid|
| UKNorth | VS Noord|
| uksouth | Verenigd Koninkrijk Zuid|
| UKSouth2 | VK - zuid 2|
| ukwest | Verenigd Koninkrijk West|
| USDoDCentral | US DoD Central|
| USDoDEast | US DoD East|
| USGovArizona | USGov Arizona|
| usgoviowa | USGov Iowa|
| USGovTexas | USGov Texas|
| usgovvirginia | USGov Virginia|
| US - west-centraal | US - west-centraal|
| westeurope | Europa -west|
| WestIndia | IN - west|
| westus | VS - west|
| westus2 | US - west 2|


## <a name="virtual-machine-usage-faq"></a>Veelgestelde vragen over het gebruik van virtuele machines
### <a name="what-resources-are-charged-when-deploying-a-vm"></a>Welke resources worden in rekening gebracht bij het implementeren van een VM?    
Vm's halen kosten voor de virtuele machine zelf, alle Premium-software die op de virtuele machine wordt uitgevoerd, de opslag account\managed schijf die aan de virtuele machine is gekoppeld en de netwerk bandbreedte overdracht van de virtuele machine.
### <a name="how-can-i-tell-if-a-vm-is-using-azure-hybrid-benefit-in-the-usage-csv"></a>Hoe kan ik zien of een virtuele machine Azure Hybrid Benefit gebruikt in het CSV-gebruik?
Als u de [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)implementeert, wordt het tarief van niet-Windows-vm's in rekening gebracht, omdat u uw eigen licentie naar de Cloud brengt. In uw factuur kunt u een onderscheid maken tussen de virtuele machines van Resource Manager Azure Hybrid Benefit, omdat ze ' Windows \_ Server BYOL ' of ' Windows \_ client BYOL ' hebben in de kolom ImageType.
### <a name="how-are-basic-vs-standard-vm-types-differentiated-in-the-usage-csv"></a>Hoe worden basis-en standaard VM-typen onderscheiden in het CSV-gebruik?
Zowel de standaard als de standaard virtuele machines uit de A-serie worden aangeboden. Als u een basis-VM implementeert in de subcategorie meter, heeft deze de teken reeks "Basic". Als u een virtuele machine van de A-serie implementeert, wordt de VM-grootte als ' a1 VM ' weer gegeven, omdat de standaard instelling is. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/virtual-machines/)voor meer informatie over de verschillen tussen basis en standaard.
### <a name="what-are-extrasmall-small-medium-large-and-extralarge-sizes"></a>Wat zijn ExtraSmall, Small, medium, large en ExtraLarge?
ExtraSmall-ExtraLarge zijn de verouderde namen voor standaard \_ a0-standaard \_ a4. In klassieke VM-gebruiks records kan deze Conventie worden gebruikt als u deze omvang hebt geïmplementeerd.
### <a name="what-is-the-difference-between-meter-region-and-resource-location"></a>Wat is het verschil tussen de meter regio en de resource locatie?
De meter regio is gekoppeld aan de meter. Voor sommige Azure-Services die één prijs voor alle regio's gebruiken, kan het veld voor de meter regio leeg zijn. Omdat Vm's echter speciale prijzen hebben per regio voor Virtual Machines, wordt dit veld ingevuld. Op dezelfde manier is de resource locatie voor Virtual Machines de locatie waar de virtuele machine wordt geïmplementeerd. De Azure-regio's in beide velden zijn hetzelfde, hoewel ze mogelijk een andere teken reeks Conventie voor de regio naam hebben.
### <a name="why-is-the-imagetype-value-blank-in-the-additional-info-field"></a>Waarom is de waarde van ImageType leeg in het veld aanvullende informatie?
Het veld ImageType wordt alleen ingevuld voor een subset van installatie kopieën. Als u een van de bovenstaande installatie kopieën niet hebt geïmplementeerd, is de ImageType leeg.
### <a name="why-is-the-vmname-blank-in-the-additional-info"></a>Waarom is het VMName leeg in de aanvullende informatie?
De VMName wordt alleen ingevuld in het veld aanvullende informatie voor virtuele machines in een schaalset. Het veld InstanceID bevat de naam van de virtuele machine voor virtuele machines die geen schaal sets zijn.
### <a name="what-does-computehr-mean-in-the-usagetype-field-in-the-additional-info"></a>Wat betekent ComputeHR in het veld UsageType in de aanvullende informatie?
ComputeHR staat voor het reken uur dat de gebruiks gebeurtenis voor de onderliggende infrastructuur kosten vertegenwoordigt. Als de UsageType ComputeHR \_ SW is, vertegenwoordigt de gebruiks gebeurtenis de kosten voor de Premium-software voor de virtuele machine.
### <a name="how-do-i-know-if-i-am-charged-for-premium-software"></a>Hoe kan ik weet ik of er kosten in rekening worden gebracht voor Premium-software?
Als u wilt weten welke VM-installatie kopie het meest geschikt is voor uw behoeften, raadpleegt u [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute). De afbeelding heeft de software plan frequentie. Als u ' gratis ' ziet voor het aantal, zijn er geen extra kosten voor de software. 
### <a name="what-is-the-difference-between-microsoftclassiccompute-and-microsoftcompute-in-the-consumed-service"></a>Wat is het verschil tussen micro soft. ClassicCompute en micro soft. Compute in de verbruikte service?
Micro soft. ClassicCompute vertegenwoordigt de klassieke resources die worden geïmplementeerd via de Azure-Service Manager. Als u implementeert via de Resource Manager, wordt micro soft. Compute in de verbruikte service ingevuld. Meer informatie over de [Azure-implementatie modellen](../azure-resource-manager/management/deployment-models.md).
### <a name="why-is-the-instanceid-field-blank-for-my-virtual-machine-usage"></a>Waarom is het veld InstanceID leeg voor het gebruik van mijn virtuele machine?
Als u via het klassieke implementatie model implementeert, is de InstanceID-teken reeks niet beschikbaar.
### <a name="why-are-the-tags-for-my-vms-not-flowing-to-the-usage-details"></a>Waarom worden de tags voor mijn Vm's niet naar de gebruiks Details gestroomd?
Labels worden alleen gebruikt voor het gebruik van CSV voor Resource Manager-Vm's. Klassieke resource Tags zijn niet beschikbaar in de gebruiks gegevens.
### <a name="how-can-the-consumed-quantity-be-more-than-24-hours-one-day"></a>Hoe kan de verbruikte hoeveelheid meer dan 24 uur per dag duren?
In het klassieke model wordt de facturering voor resources geaggregeerd op het niveau van de Cloud service. Als u meer dan één virtuele machine in een Cloud service hebt die dezelfde facturerings meter gebruikt, wordt uw gebruik samen geaggregeerd. Vm's die via Resource Manager zijn geïmplementeerd, worden in rekening gebracht op het niveau van de virtuele machine, zodat deze aggregatie niet van toepassing is.
### <a name="why-is-pricing-not-available-for-dsfsgsls-sizes-on-the-pricing-page"></a>Waarom zijn de prijzen voor DS/FS/GS/LS-grootten niet beschikbaar op de pagina met prijzen?
Virtuele machines met Premium-opslag worden in rekening gebracht tegen hetzelfde tarief als Vm's waarvoor geen Premium-opslag mogelijk is. Alleen uw opslag kosten verschillen. Ga naar de [pagina met prijzen voor opslag](https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Zie [inzicht in uw factuur voor Microsoft Azure](../cost-management-billing/understand/review-individual-bill.md) voor meer informatie over uw gebruiks gegevens.