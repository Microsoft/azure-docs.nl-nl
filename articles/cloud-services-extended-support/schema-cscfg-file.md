---
title: Definitie schema voor Azure Cloud Services (uitgebreide ondersteuning) (cscfg-bestand) | Microsoft Docs
description: Informatie met betrekking tot het definitie schema voor Cloud Services (uitgebreide ondersteuning)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 3aac5ca8d146f0cd74f483846d22fdf0052f86f2
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744296"
---
# <a name="azure-cloud-services-extended-support-config-schema-cscfg-file"></a>Configuratie schema van Azure Cloud Services (uitgebreide ondersteuning) (cscfg-bestand)

Het configuratie bestand van de service specificeert het aantal rolinstanties dat moet worden geïmplementeerd voor elke rol in de service, de waarden van de configuratie-instellingen en de vinger afdrukken voor alle certificaten die aan een rol zijn gekoppeld. Als de service deel uitmaakt van een Virtual Network, moet de configuratie-informatie voor het netwerk worden verstrekt in het service configuratie bestand en in het configuratie bestand voor virtuele netwerken. De standaard extensie voor het service configuratie bestand is cscfg.

Het service model wordt beschreven in het [definitie schema van Cloud service (Extended support)](schema-csdef-file.md).

Het Azure Diagnostics-configuratie schema bestand wordt standaard geïnstalleerd in de `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` map. Vervang door `<version>` de geïnstalleerde versie van de [Azure SDK](https://azure.microsoft.com/downloads/).

Zie [Wat is het Cloud service model](../cloud-services/cloud-services-model-and-package.md)voor meer informatie over het configureren van rollen in een service.

## <a name="basic-service-configuration-schema"></a>Basis schema voor service configuratie
De basis indeling van het service configuratie bestand is als volgt.

```xml
<ServiceConfiguration serviceName="<service-name>" osFamily="<osfamily-number>" osVersion="<os-version>" schemaVersion="<schema-version>">

  <Role …>
    …
  </Role>

  <NetworkConfiguration>
    …
  </NetworkConfiguration>

</ServiceConfiguration>
```

## <a name="schema-definitions"></a>Schema definities
In de volgende onderwerpen wordt het schema voor het- `ServiceConfiguration` element beschreven:

- [Role-schema](schema-cscfg-role.md)
- [NetworkConfiguration-schema](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>Service configuratie naam ruimte
De XML-naam ruimte voor het service configuratie bestand is: `http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration` .

##  <a name="serviceconfiguration-element"></a><a name="ServiceConfiguration"></a> ServiceConfiguration-element
Het `ServiceConfiguration` element is het element op het hoogste niveau van het service configuratie bestand.

In de volgende tabel worden de kenmerken van het `ServiceConfiguration` element beschreven. Alle kenmerk waarden zijn teken reeks typen.

| Kenmerk | Beschrijving |
| --------- | ----------- |
|serviceName|Vereist. De naam van de Cloud service. De naam die u hier opgeeft, moet overeenkomen met de naam die is opgegeven in het service definitie bestand.|
|osFamily|Optioneel. Hiermee geeft u het gast besturingssysteem op dat wordt uitgevoerd op rolinstanties in de Cloud service. Zie [Azure Guest OS Releases and SDK Compatibility Matrix](../cloud-services/cloud-services-guestos-update-matrix.md)(Engelstalig) voor meer informatie over ondersteunde versies van gast besturingssystemen.<br /><br /> Als u geen `osFamily` waarde opgeeft en u het kenmerk niet hebt ingesteld `osVersion` op een specifieke versie van het gast besturingssysteem, wordt een standaard waarde van 1 gebruikt.|
|osVersion|Optioneel. Hiermee geeft u de versie van het gast besturingssysteem op dat wordt uitgevoerd op rolinstanties in de Cloud service. Zie [Azure Guest OS releases en SDK Compatibility Matrix](../cloud-services/cloud-services-guestos-update-matrix.md)(Engelstalig) voor meer informatie over de versies van het gast besturingssysteem.<br /><br /> U kunt opgeven dat het gast besturingssysteem automatisch moet worden bijgewerkt naar de nieuwste versie. Als u dit wilt doen, stelt u de waarde van het `osVersion` kenmerk in op `*` . Als deze functie is ingesteld op `*` , worden de rolinstanties geïmplementeerd met de nieuwste versie van het gast besturingssysteem voor de opgegeven besturingssysteem familie en worden deze automatisch bijgewerkt wanneer nieuwe versies van het gast besturingssysteem worden vrijgegeven.<br /><br /> Als u een specifieke versie hand matig wilt opgeven, gebruikt u de `Configuration String` sectie uit de tabel in de **toekomst, huidig en voorlopig gast besturingssysteem versies** van [Azure Guest OS releases en SDK Compatibility Matrix](../cloud-services/cloud-services-guestos-update-matrix.md).<br /><br /> De standaard waarde voor het `osVersion` kenmerk is `*` .|
|schemaVersion|Optioneel. Hiermee geeft u de versie van het service configuratie schema op. Met de schema versie kan Visual Studio de juiste SDK-hulpprogram ma's selecteren die voor schema validatie moeten worden gebruikt als meer dan één versie van de SDK naast elkaar is geïnstalleerd. Zie [Azure Guest OS Releases and SDK Compatibility Matrix](../cloud-services/cloud-services-guestos-update-matrix.md) (Engelstalig) voor meer informatie over schema-en versie compatibiliteit|

Het service configuratie bestand moet één `ServiceConfiguration` element bevatten. Het `ServiceConfiguration` element kan een wille keurig aantal `Role` elementen en nul of 1 `NetworkConfiguration` elementen bevatten.

## <a name="see-also"></a>Zie ook

[Definitie schema voor Azure Cloud Services (uitgebreide ondersteuning) (csdef-bestand)](schema-csdef-file.md)