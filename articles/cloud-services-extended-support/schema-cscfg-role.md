---
title: Rollen schema voor Azure Cloud Services (uitgebreide ondersteuning) | Microsoft Docs
description: Informatie met betrekking tot het Role-schema voor Cloud Services (uitgebreide ondersteuning)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2567f5bb817a34f6274d5e265a266d67a9c81413
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98744302"
---
# <a name="azure-cloud-services-extended-support-config-role-schema"></a>Schema voor configuratie van Azure Cloud Services (uitgebreide ondersteuning)

Het `Role` element van het configuratie bestand bevat het aantal rolinstanties dat moet worden geïmplementeerd voor elke rol in de service, de waarden van configuratie-instellingen en de vinger afdrukken voor alle certificaten die aan een rol zijn gekoppeld.

Zie voor meer informatie over het configuratie schema van de Azure-service [Cloud service (uitgebreide ondersteuning) configuratie schema](schema-cscfg-file.md). Zie voor meer informatie over het Azure service definition-schema het [definitie schema voor Cloud service (uitgebreide ondersteuning)](schema-csdef-file.md).

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
[Configuratie schema van Cloud service (uitgebreide ondersteuning)](schema-cscfg-file.md).