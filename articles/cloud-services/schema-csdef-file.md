---
title: Definitie schema voor Azure Cloud Services (klassiek) (. csdef-bestand) | Microsoft Docs
description: Een bestand met een service definitie (. csdef) definieert een service model voor een toepassing, met beschik bare rollen, eind punten en configuratie waarden voor de service.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: b98534b049698ea95c6738ce3404dd5ef8ff7a28
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102502261"
---
# <a name="azure-cloud-services-classic-definition-schema-csdef-file"></a>Definitie schema voor Azure Cloud Services (klassiek) (. csdef-bestand)

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

Het service definitie bestand definieert het service model voor een toepassing. Het bestand bevat de definities voor de functies die beschikbaar zijn voor een Cloud service, geeft de service-eind punten op en stelt configuratie-instellingen voor de service in. Waarden voor configuratie-instellingen worden ingesteld in het service configuratie bestand, zoals beschreven in het [Configuratie schema van de Cloud service (klassiek)](/previous-versions/azure/reference/ee758710(v=azure.100)).

Het Azure Diagnostics-configuratie schema bestand wordt standaard geïnstalleerd in de `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` map. Vervang door `<version>` de geïnstalleerde versie van de [Azure SDK](https://www.windowsazure.com/develop/downloads/).

De standaard extensie voor het service definitie bestand is. csdef.

## <a name="basic-service-definition-schema"></a>Basis schema voor service definitie
Het service definitie bestand moet één `ServiceDefinition` element bevatten. De service definitie moet ten minste één Role- `WebRole` element (of `WorkerRole` ) bevatten. Het kan Maxi maal 25 rollen bevatten die in één definitie zijn gedefinieerd, en u kunt typen van rollen combi neren. De service definitie bevat ook het optionele `NetworkTrafficRules` element waarmee wordt beperkt welke rollen kunnen communiceren met opgegeven interne eind punten. De service definitie bevat ook het optionele `LoadBalancerProbes` element dat door de klant gedefinieerde status controles van eind punten bevat.

De basis indeling van het service definitie bestand is als volgt.

```xml
<ServiceDefinition name="<service-name>" topologyChangeDiscovery="<change-type>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" upgradeDomainCount="<number-of-upgrade-domains>" schemaVersion="<version>">
  
  <LoadBalancerProbes>
         …
  </LoadBalancerProbes>
  
  <WebRole …>
         …
  </WebRole>
  
  <WorkerRole …>
         …
  </WorkerRole>
  
  <NetworkTrafficRules>
         …
  </NetworkTrafficRules>

</ServiceDefinition>
```

## <a name="schema-definitions"></a>Schema definities
In de volgende onderwerpen wordt het schema beschreven:

- [LoadBalancerProbe-schema](schema-csdef-loadbalancerprobe.md)
- [WebRole-schema](schema-csdef-webrole.md)
- [WorkerRole-schema](schema-csdef-workerrole.md)
- [NetworkTrafficRules-schema](schema-csdef-networktrafficrules.md)

##  <a name="servicedefinition-element"></a><a name="ServiceDefinition"></a> ServiceDefinition-element
Het `ServiceDefinition` element is het element op het hoogste niveau van het service definitie bestand.

In de volgende tabel worden de kenmerken van het `ServiceDefinition` element beschreven.

| Kenmerk               | Beschrijving |
| ----------------------- | ----------- |
| naam                    |Vereist. De naam van de service. De naam moet uniek zijn binnen het service account.|
| topologyChangeDiscovery | Optioneel. Hiermee geeft u het type melding voor de topologie wijziging op. Mogelijke waarden zijn:<br /><br /> -   `Blast` -Hiermee wordt de update zo snel mogelijk verzonden naar alle rolinstanties. Als u optie kiest, moet de rol de update van de topologie kunnen afhandelen zonder opnieuw te worden opgestart.<br />-   `UpgradeDomainWalk` – Verzendt de update naar elke rolinstantie op een opeenvolgende manier nadat het vorige exemplaar de update heeft geaccepteerd.|
| schemaVersion           | Optioneel. Hiermee geeft u de versie van het service definitie schema op. Met de schema versie kan Visual Studio de juiste SDK-hulpprogram ma's selecteren die voor schema validatie moeten worden gebruikt als meer dan één versie van de SDK naast elkaar is geïnstalleerd.|
| upgradeDomainCount      | Optioneel. Hiermee geeft u het aantal upgrade domeinen op waarmee rollen in deze service worden toegewezen. Rolinstanties worden toegewezen aan een upgrade domein wanneer de service wordt geïmplementeerd. Zie [Update a Cloud Service Role of Deployment](cloud-services-how-to-manage-portal.md#update-a-cloud-service-role-or-deployment)( [de beschik baarheid van virtuele machines beheren](../virtual-machines/availability.md) en [Wat is een Cloud service model](./cloud-services-model-and-package.md)) voor meer informatie.<br /><br /> U kunt Maxi maal 20 upgrade domeinen opgeven. Als u niets opgeeft, is het standaard aantal upgrade domeinen 5.|