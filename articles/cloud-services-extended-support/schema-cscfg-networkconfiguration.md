---
title: Azure Cloud Services (uitgebreide ondersteuning) NetworkConfiguration Schema | Microsoft Docs
description: Informatie met betrekking tot het netwerkconfiguratieschema voor Cloud Services (uitgebreide ondersteuning)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: ed2d48288bf97fe3ebaa1e8ffc1336d8a82d940e
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719021"
---
# <a name="azure-cloud-services-extended-support-config-networkconfiguration-schema"></a>Azure Cloud Services (uitgebreide ondersteuning) configuratienetwerkConfiguratieschema

Het `NetworkConfiguration` element van het serviceconfiguratiebestand geeft de Virtual Network dns-waarden op. Deze instellingen zijn optioneel voor Cloud Services.

U kunt de volgende resource gebruiken voor meer informatie over virtuele netwerken en de bijbehorende schema's:

- [Configuratieschema voor cloudservice (uitgebreide ondersteuning).](schema-cscfg-file.md)
- [Cloud Service (uitgebreide ondersteuning) Definitieschema](schema-csdef-file.md).
- [Maak een Virtual Network](../virtual-network/manage-virtual-network.md).

## <a name="networkconfiguration-element"></a>Het element NetworkConfiguration
In het volgende voorbeeld ziet u `NetworkConfiguration` het element en de onderliggende elementen.

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
| AccessControl | Optioneel. Hiermee geeft u de regels op voor toegang tot eindpunten in een cloudservice. De naam van het toegangsbeheer wordt gedefinieerd door een tekenreeks voor `name` kenmerk. Het `AccessControl` element bevat een of meer `Rule` elementen. Er kan meer `AccessControl` dan één element worden gedefinieerd.|
| Regel | Optioneel. Hiermee geeft u de actie op die moet worden uitgevoerd voor een opgegeven subnetbereik van IP-adressen. De volgorde van de regel wordt gedefinieerd door een tekenreekswaarde voor het `order` kenmerk . Hoe lager het regelnummer, hoe hoger de prioriteit. Regels kunnen bijvoorbeeld worden opgegeven met ordernummers van 100, 200 en 300. De regel met het ordernummer 100 heeft voorrang op de regel met een order van 200.<br /><br /> De actie voor de regel wordt gedefinieerd door een tekenreeks voor het `action` kenmerk . Mogelijke waarden zijn:<br /><br /> -   `permit` – Geeft aan dat alleen pakketten uit het opgegeven subnetbereik met het eindpunt kunnen communiceren.<br />-   `deny` – Geeft aan dat toegang tot de eindpunten in het opgegeven subnetbereik wordt geweigerd.<br /><br /> Het subnetbereik van IP-adressen die worden beïnvloed door de regel, wordt gedefinieerd door een tekenreeks voor het `remoteSubnet` kenmerk . De beschrijving voor de regel wordt gedefinieerd door een tekenreeks voor het `description` kenmerk .|
| EndpointAcl | Optioneel. Hiermee geeft u de toewijzing van toegangsbeheerregels aan een eindpunt op. De naam van de rol die het eindpunt bevat, wordt gedefinieerd door een tekenreeks voor het `role` kenmerk . De naam van het eindpunt wordt gedefinieerd door een tekenreeks voor het `endpoint` kenmerk . De naam van de set regels die moet worden toegepast op het eindpunt, wordt `AccessControl` gedefinieerd in een tekenreeks voor het kenmerk `accessControl` . Er kunnen meer `EndpointAcl` dan één elementen worden gedefinieerd.|
| DnsServer | Optioneel. Hiermee geeft u de instellingen voor een DNS-server. U kunt instellingen opgeven voor DNS-servers zonder Virtual Network. De naam van de DNS-server wordt gedefinieerd door een tekenreeks voor het `name` kenmerk . Het IP-adres van de DNS-server wordt gedefinieerd door een tekenreeks voor het `IPAddress` kenmerk . Het IP-adres moet een geldig IPv4-adres zijn.|
| VirtualNetworkSite | Optioneel. Hiermee geeft u de naam op van Virtual Network site waarin u uw cloudservice wilt implementeren. Met deze instelling wordt geen Virtual Network gemaakt. Deze verwijst naar een site die eerder is gedefinieerd in het netwerkbestand voor uw Virtual Network. Een cloudservice kan slechts lid zijn van één Virtual Network. Als u deze instelling niet opgeeft, wordt de cloudservice niet geïmplementeerd op een Virtual Network. De naam van de Virtual Network site wordt gedefinieerd door een tekenreeks voor het `name` kenmerk .|
| InstanceAddress | Optioneel. Hiermee geeft u de associatie van een rol met een subnet of een set subnetten in de Virtual Network. Wanneer u een rolnaam aan een exemplaaradres koppelt, kunt u de subnetten opgeven waaraan u deze rol wilt koppelen. De `InstanceAddress` bevat het element Subnetten. De naam van de rol die is gekoppeld aan het subnet of de subnetten wordt gedefinieerd door een tekenreeks voor het `roleName` kenmerk .|
| Subnet | Optioneel. Hiermee geeft u het subnet dat overeenkomt met de naam van het subnet in het netwerkconfiguratiebestand. De naam van het subnet wordt gedefinieerd door een tekenreeks voor het `name` kenmerk .|
| ReservedIP | Optioneel. Hiermee geeft u het gereserveerde IP-adres op dat aan de implementatie moet worden gekoppeld. De toewijzingsmethode voor een gereserveerd IP-adres moet worden opgegeven als `Static` voor sjabloon- en PowerShell-implementaties. Elke implementatie in een cloudservice kan worden gekoppeld aan slechts één gereserveerd IP-adres. De naam van het gereserveerde IP-adres wordt gedefinieerd door een tekenreeks voor het `name` kenmerk .|

## <a name="see-also"></a>Zie ook
[Configuratieschema voor cloudservice (uitgebreide ondersteuning).](schema-cscfg-file.md)
