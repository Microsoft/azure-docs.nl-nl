---
title: Azure Cloud Services (uitgebreide ondersteuning) NetworkConfiguration-schema | Microsoft Docs
description: Informatie met betrekking tot het netwerk configuratie schema voor Cloud Services (uitgebreide ondersteuning)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2650da2579f13ec1588af7a25e5b28908209bc82
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101700181"
---
# <a name="azure-cloud-services-extended-support-config-networkconfiguration-schema"></a>Azure Cloud Services (uitgebreide ondersteuning) config networkConfiguration-schema

Het `NetworkConfiguration` element van het service configuratie bestand bevat Virtual Network-en DNS-waarden. Deze instellingen zijn optioneel voor Cloud Services.

U kunt de volgende resource gebruiken voor meer informatie over virtuele netwerken en de bijbehorende schema's:

- [Configuratie schema van Cloud service (uitgebreide ondersteuning)](schema-cscfg-file.md).
- [Definitie schema van Cloud service (uitgebreide ondersteuning)](schema-csdef-file.md).
- [Maak een Virtual Network](../virtual-network/manage-virtual-network.md).

## <a name="networkconfiguration-element"></a>NetworkConfiguration-element
In het volgende voor beeld ziet u het `NetworkConfiguration` element en de onderliggende elementen.

```xml
<ServiceConfiguration>
  <NetworkConfiguration>
    <AccessControls>
      <AccessControl name="aclName1">
        <Rule order="<rule-order>" action="<rule-action>" remoteSubnet="<subnet-address>" description="rule-description"/>
      </AccessControl>
    </AccessControls>
    <EndpointAcls>
      <EndpointAcl role="<role-name>" endpoint="<endpoint-name>" accessControl="<acl-name>"/>
    </EndpointAcls>
    <Dns>
      <DnsServers>
        <DnsServer name="<server-name>" IPAddress="<server-address>" />
      </DnsServers>
    </Dns>
    <VirtualNetworkSite name="Group <RG-VNet> <VNet-name>"/>
    <AddressAssignments>
      <InstanceAddress roleName="<role-name>">
        <Subnets>
          <Subnet name="<subnet-name>"/>
        </Subnets>
      </InstanceAddress>
      <ReservedIPs>
        <ReservedIP name="<reserved-ip-name>"/>
      </ReservedIPs>
    </AddressAssignments>
  </NetworkConfiguration>
</ServiceConfiguration>
```

In de volgende tabel worden de onderliggende elementen van het `NetworkConfiguration` element beschreven.

| Element       | Beschrijving |
| ------------- | ----------- |
| AccessControl | Optioneel. Hiermee geeft u de regels voor toegang tot eind punten in een Cloud service. De naam van het toegangs beheer wordt gedefinieerd door een teken reeks voor het `name` kenmerk. Het `AccessControl` element bevat een of meer `Rule` elementen. Er kan meer dan één `AccessControl` element worden gedefinieerd.|
| Regel | Optioneel. Hiermee geeft u de actie op die moet worden uitgevoerd voor een opgegeven subnet-IP-adres bereik. De volg orde van de regel wordt gedefinieerd door een teken reeks waarde voor het `order` kenmerk. Hoe lager de regel, hoe hoger de prioriteit. U kunt bijvoorbeeld regels opgeven met order nummers van 100, 200 en 300. De regel met het order nummer 100 heeft voor rang op de regel met een bestelling van 200.<br /><br /> De actie voor de regel wordt gedefinieerd door een teken reeks voor het `action` kenmerk. Mogelijke waarden zijn:<br /><br /> -   `permit` -Geeft aan dat alleen pakketten van het opgegeven subnet-bereik kunnen communiceren met het eind punt.<br />-   `deny` -Geeft aan dat de toegang wordt geweigerd tot de eind punten in het opgegeven subnet-bereik.<br /><br /> Het subnetten van IP-adressen die worden beïnvloed door de regel, worden gedefinieerd door een teken reeks voor het `remoteSubnet` kenmerk. De beschrijving voor de regel wordt gedefinieerd door een teken reeks voor het `description` kenmerk.|
| EndpointAcl | Optioneel. Hiermee geeft u de toewijzing van regels voor toegangs beheer aan een eind punt op. De naam van de rol die het eind punt bevat, wordt gedefinieerd door een teken reeks voor het `role` kenmerk. De naam van het eind punt wordt gedefinieerd door een teken reeks voor het `endpoint` kenmerk. De naam van de set `AccessControl` regels die op het eind punt moet worden toegepast, wordt gedefinieerd in een teken reeks voor het `accessControl` kenmerk. Er kunnen meerdere `EndpointAcl` elementen worden gedefinieerd.|
| DnsServer | Optioneel. Hiermee geeft u de instellingen voor een DNS-server. U kunt instellingen opgeven voor DNS-servers zonder een Virtual Network. De naam van de DNS-server wordt gedefinieerd door een teken reeks voor het `name` kenmerk. Het IP-adres van de DNS-server wordt gedefinieerd door een teken reeks voor het `IPAddress` kenmerk. Het IP-adres moet een geldig IPv4-adres zijn.|
| VirtualNetworkSite | Optioneel. Hiermee geeft u de naam op van de Virtual Network site waarin u uw Cloud service wilt implementeren. Met deze instelling wordt geen Virtual Network-site gemaakt. Er wordt verwezen naar een site die eerder in het netwerk bestand voor uw Virtual Network is gedefinieerd. Een Cloud service kan alleen lid zijn van een Virtual Network. Als u deze instelling niet opgeeft, wordt de Cloud service niet geïmplementeerd op een Virtual Network. De naam van de Virtual Network site wordt gedefinieerd door een teken reeks voor het `name` kenmerk.|
| InstanceAddress | Optioneel. Hiermee geeft u de koppeling van een rol aan een subnet of een set subnetten in de Virtual Network. Wanneer u een rolnaam aan een exemplaar adres koppelt, kunt u de subnetten opgeven waaraan u deze rol wilt koppelen. Het `InstanceAddress` element bevat een subnets. De naam van de rol die aan het subnet of de subnetten is gekoppeld, wordt gedefinieerd door een teken reeks voor het `roleName` kenmerk.|
| Subnet | Optioneel. Hiermee geeft u het subnet op dat overeenkomt met de naam van het subnet in het netwerk configuratie bestand. De naam van het subnet wordt gedefinieerd door een teken reeks voor het `name` kenmerk.|
| ReservedIP | Optioneel. Hiermee geeft u het gereserveerde IP-adres op dat moet worden gekoppeld aan de implementatie. U moet Gereserveerd IP adres maken gebruiken om het gereserveerde IP-adres te maken. Elke implementatie in een Cloud service kan worden gekoppeld aan één gereserveerd IP-adres. De naam van het gereserveerde IP-adres wordt gedefinieerd door een teken reeks voor het `name` kenmerk.|

## <a name="see-also"></a>Zie ook
[Configuratie schema van Cloud service (uitgebreide ondersteuning)](schema-cscfg-file.md).