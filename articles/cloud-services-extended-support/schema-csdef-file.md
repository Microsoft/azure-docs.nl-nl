---
title: Definitie schema voor Azure Cloud Services (Extended support) (csdef-bestand) | Microsoft Docs
description: Informatie met betrekking tot het definitie schema (csdef) voor Cloud Services (uitgebreide ondersteuning)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: ab85067184ebe5b34097a3c81aa521d509ae4b9a
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744604"
---
# <a name="azure-cloud-services-extended-support-definition-schema-csdef-file"></a>Definitie schema voor Azure Cloud Services (uitgebreide ondersteuning) (csdef-bestand)

Het service definitie bestand definieert het service model voor een toepassing. Het bestand bevat de definities voor de functies die beschikbaar zijn voor een Cloud service, geeft de service-eind punten op en stelt configuratie-instellingen voor de service in. Waarden voor configuratie-instellingen worden ingesteld in het service configuratie bestand, zoals beschreven in het [Configuratie schema van de Cloud service (uitgebreide ondersteuning)](schema-cscfg-file.md).

Het Azure Diagnostics-configuratie schema bestand wordt standaard geïnstalleerd in de `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` map. Vervang door `<version>` de geïnstalleerde versie van de [Azure SDK](https://www.windowsazure.com/develop/downloads/).

De standaard extensie voor het service definitie bestand is csdef.

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
| upgradeDomainCount      | Optioneel. Hiermee geeft u het aantal upgrade domeinen op waarmee rollen in deze service worden toegewezen. Rolinstanties worden toegewezen aan een upgrade domein wanneer de service wordt geïmplementeerd. Zie [een Cloud service-rol of-implementatie bijwerken](sample-update-cloud-service.md) en [de beschik baarheid van virtuele machines beheren](../virtual-machines/manage-availability.md) voor meer informatie. u kunt Maxi maal 20 upgrade domeinen opgeven. Als u niets opgeeft, is het standaard aantal upgrade domeinen 5.|

## <a name="see-also"></a>Zie ook

[Azure Cloud Services (Extended support) configuratie schema (cscfg-bestand)](schema-cscfg-file.md).