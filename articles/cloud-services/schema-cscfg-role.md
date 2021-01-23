---
title: Azure Cloud Services (klassiek)-rollen schema | Microsoft Docs
description: Het element Role van een service configuratie bestand geeft aan hoeveel rolinstanties er moeten worden geïmplementeerd voor elke rol, configuratie waarden en certificaat vingerafdrukken.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2dc8e14a4e4d8855abb615632bb7d43b9034d360
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743437"
---
# <a name="azure-cloud-services-classic-config-role-schema"></a>Rollen schema voor Azure Cloud Services (klassiek)

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

Het `Role` element van het configuratie bestand bevat het aantal rolinstanties dat moet worden geïmplementeerd voor elke rol in de service, de waarden van configuratie-instellingen en de vinger afdrukken voor alle certificaten die aan een rol zijn gekoppeld.

Zie [Cloud service (klassiek)-configuratie schema](schema-cscfg-file.md)voor meer informatie over het configuratie schema van de Azure-service. Zie het [definitie schema voor Cloud service (klassiek)](schema-csdef-file.md)voor meer informatie over het Azure service definition-schema.

##  <a name="role-element"></a><a name="Role"></a> Role-element
In het volgende voor beeld ziet u het `Role` element en de onderliggende elementen.

```xml 
<ServiceConfiguration>
  <Role name="<role-name>" vmName="<vm-name>">
    <Instances count="<number-of-instances>"/>
    <ConfigurationSettings>
      <Setting name="<setting-name>" value="<setting-value>" />
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="<certificate-name>" thumbprint="<certificate-thumbprint>" thumbprintAlgorithm="<algorithm>"/>
    </Certificates>
  </Role>
</ServiceConfiguration>
```

In de volgende tabel worden de kenmerken voor het `Role` element beschreven.

| Kenmerk | Beschrijving |
| --------- | ----------- |
| naam   | Vereist. Hiermee geeft u de naam van de rol op. De naam moet overeenkomen met de naam die is opgegeven voor de rol in het service definitie bestand.|
| vmName | Optioneel. Hiermee geeft u de DNS-naam voor een virtuele machine. De naam mag Maxi maal 10 tekens bevatten.|

In de volgende tabel worden de onderliggende elementen van het `Role` element beschreven.

| Element | Beschrijving |
| ------- | ----------- |
| exemplaren | Vereist. Hiermee geeft u het aantal instanties op dat moet worden geïmplementeerd voor de rol. Het aantal exemplaren wordt gedefinieerd door een geheel getal voor het `count` kenmerk.|
| Instelling   | Optioneel. Hiermee geeft u een naam en waarde voor de instelling op in een verzameling instellingen voor een rol. De naam van de instelling wordt gedefinieerd door een teken reeks voor het `name` kenmerk en de waarde van de instelling wordt gedefinieerd door een teken reeks voor het `value` kenmerk.|
| Certificaat | Optioneel. Hiermee geeft u de naam, vinger afdruk en het algoritme op van een service certificaat dat moet worden gekoppeld aan de rol. De naam van het certificaat wordt gedefinieerd door een teken reeks voor het `name` kenmerk. De vinger afdruk van het certificaat wordt gedefinieerd door een reeks hexadecimale getallen die geen spaties voor het `thumbprint` kenmerk bevatten. De hexadecimale getallen moeten worden weer gegeven met cijfers en hoofd letters. Het certificaat algoritme wordt gedefinieerd door een teken reeks voor het `thumbprintAlgorithm` kenmerk.|

## <a name="see-also"></a>Zie ook
[Configuratie schema van Cloud service (klassiek)](schema-cscfg-file.md)