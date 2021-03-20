---
title: Update van Azure Traffic Analytics-schema-maart 2020 | Microsoft Docs
description: Voorbeeld query's met nieuwe velden in het Traffic Analytics schema.
services: network-watcher
documentationcenter: na
author: vinigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: d7c4f1853ff8dcb9249ab6ec4f536e1f8cfa10e5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98018221"
---
# <a name="sample-queries-with-new-fields-in-the-traffic-analytics-schema-august-2019-schema-update"></a>Voorbeeld query's met nieuwe velden in het Traffic Analytics schema (augustus 2019-schema-update)

Het [Traffic Analytics-logboek schema](./traffic-analytics-schema.md) bevat de volgende nieuwe velden: **SrcPublicIPs_s**, **DestPublicIPs_s** **NSGRule_s**. De nieuwe velden bevatten informatie over de bron-en doel-Ip's en vereenvoudigen query's.

In de komende maanden worden de volgende oudere velden afgeschaft: **VMIP_s**, **Subscription_g**, **Region_s**, **NSGRules_s**, **Subnet_s**, **VM_s**, **NIC_s**, **PublicIPs_s** FlowCount_d. 

In de volgende drie voor beelden ziet u hoe u de oude velden vervangt door de nieuwe.

## <a name="example-1-vmip_s-subscription_g-region_s-subnet_s-vm_s-nic_s-and-publicips_s-fields"></a>Voor beeld 1: VMIP_s, Subscription_g, Region_s, Subnet_s, VM_s, NIC_s en PublicIPs_s velden

We hoeven geen bron-en doel cases af te leiden van het **FlowDirection_s** veld voor AzurePublic-en ExternalPublic-stromen. Het kan ook ongeschikt zijn voor het gebruik van het **FlowDirection_s** veld voor een virtueel netwerk apparaat.

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend isAzureOrExternalPublicFlows = FlowType_s in ("AzurePublic", "ExternalPublic")
| extend SourceAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), VM1_s),
SourceAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), SrcIP_s),
SourceAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subscription_g, "N/A"), Subscription1_g),
SourceAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Region_s, "N/A"), Region1_s),
SourceAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subnet_s, "N/A"), Subnet1_s),
SourceAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', NIC_s, "N/A"), NIC1_s),
DestAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), VM2_s),
DestAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), DestIP_s),
DestAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subscription_g, "N/A"), Subscription2_g),
DestAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Region_s, "N/A"), Region2_s),
DestAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subnet_s, "N/A"), Subnet2_s),
DestAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', NIC_s, "N/A"), NIC2_s),
SourcePublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'I', PublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'O', PublicIPs_s, "N/A")
```


```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend SourceAzureVM = iif(isnotempty(VM1_s), VM1_s, "N/A"),
SourceAzureVMIP = iif(isnotempty(SrcIP_s), SrcIP_s, "N/A"),
SourceAzureVMSubscription = iif(isnotempty(Subscription1_g), Subscription1_g, "N/A"),
SourceAzureRegion = iif(isnotempty(Region1_s), Region1_s, "N/A"),
SourceAzureSubnet = iif(isnotempty(Subnet1_s), Subnet1_s, "N/A"),
SourceAzureNIC = iif(isnotempty(NIC1_s), NIC1_s, "N/A"),
DestAzureVM = iif(isnotempty(VM2_s), VM2_s, "N/A"),
DestAzureVMIP = iif(isnotempty(DestIP_s), DestIP_s, "N/A"),
DestAzureVMSubscription = iif(isnotempty(Subscription2_g), Subscription2_g, "N/A"),
DestAzureRegion = iif(isnotempty(Region2_s), Region2_s, "N/A"),
DestAzureSubnet = iif(isnotempty(Subnet2_s), Subnet2_s, "N/A"),
DestAzureNIC = iif(isnotempty(NIC2_s), NIC2_s, "N/A"),
SourcePublicIPsAggregated = iif(isnotempty(SrcPublicIPs_s), SrcPublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isnotempty(DestPublicIPs_s), DestPublicIPs_s, "N/A")
```

## <a name="example-2-nsgrules_s-field"></a>Voor beeld 2: NSGRules_s veld

Het oude veld heeft de volgende indeling gebruikt:

`<Index value 0)>|<NSG_ RuleName>|<Flow Direction>|<Flow Status>|<FlowCount ProcessedByRule>`

Er worden geen gegevens meer verzameld over een netwerk beveiligings groep (NSG). In het bijgewerkte schema bevat **NSGList_s** slechts één NSG. Ook **NSGRules** bevat slechts één regel. De gecompliceerde opmaak is hier en in andere velden verwijderd, zoals in het voor beeld wordt weer gegeven.

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| extend NSGName = NSGList_s // remains same
| extend NSGRuleName = NSGRuleComponents[1],
         FlowDirection = NSGRuleComponents[2],
         FlowStatus = NSGRuleComponents[3],
         FlowCountProcessedByRule = NSGRuleComponents[4]
| project NSGName, NSGRuleName, FlowDirection, FlowStatus, FlowCountProcessedByRule
```

```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| project NSGName = NSGList_s,
NSGRuleName = NSGRule_s ,
FlowDirection = FlowDirection_s,
FlowStatus = FlowStatus_s,
FlowCountProcessedByRule = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d
```

## <a name="example-3-flowcount_d-field"></a>Voor beeld 3: FlowCount_d veld

Omdat we geen gegevens meer over de NSG, is de **FlowCount_d** gewoon:

**AllowedInFlows_d**  +  **DeniedInFlows_d**  +  **AllowedOutFlows_d**  +  **DeniedOutFlows_d**

Slechts een van de vier velden is niet nul. De andere drie velden zijn nul. De velden vullen de status en het aantal aan in de NIC waar de stroom is vastgelegd.

Om deze voor waarden te illustreren:

- Als de stroom is toegestaan, wordt een van de "toegestane" velden gevuld.
- Als de stroom is geweigerd, wordt een van de vooraf ingestelde velden ' geweigerd ' ingevuld.
- Als de stroom inkomend is, wordt een van de velden met het achtervoegsel InFlows_d ingevuld.
- Als de stroom is uitgaand, wordt een van de velden met het achtervoegsel OutFlows_d ingevuld.

Afhankelijk van de voor waarden weten we dat een van de vier velden wordt ingevuld.

## <a name="next-steps"></a>Volgende stappen

- Zie [Traffic Analytics Veelgestelde vragen](traffic-analytics-faq.md)voor antwoorden op veelgestelde vragen.
- Zie [Traffic Analytics-documentatie](traffic-analytics.md)voor meer informatie over de functionaliteit.