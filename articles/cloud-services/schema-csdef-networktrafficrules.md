---
title: Cloud Services van Azure (klassiek) def. NetworkTrafficRules-schema | Microsoft Docs
description: Meer informatie over NetworkTrafficRules, die de rollen beperkt die toegang hebben tot de interne eind punten van een rol. Deze combi natie met rollen in een service definitie bestand.
ms.topic: article
ms.service: cloud-services
ms.subservice: deployment-files
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 499b0f2b7e02dabbde906e0d347a17389b2e5753
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934101"
---
# <a name="azure-cloud-services-classic-definition-networktrafficrules-schema"></a>NetworkTrafficRules-schema voor Azure Cloud Services (klassiek)

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

Het `NetworkTrafficRules` knoop punt is een optioneel element in het service definitie bestand dat aangeeft hoe rollen met elkaar communiceren. Hiermee wordt beperkt welke rollen toegang hebben tot de interne eind punten van de specifieke rol. Het `NetworkTrafficRules` is geen zelfstandig element; het wordt gecombineerd met twee of meer rollen in een service definitie bestand.

De standaard extensie voor het service definitie bestand is. csdef.

> [!NOTE]
>  Het `NetworkTrafficRules` knoop punt is alleen beschikbaar via de Azure SDK-versie 1,3 of hoger.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>Basis schema van de service definitie voor de regels voor netwerk verkeer
De basis indeling van een service definitie bestand met definities van netwerk verkeer is als volgt.

```xml
<ServiceDefinition …>
   <NetworkTrafficRules>
      <OnlyAllowTrafficTo>
         <Destinations>
            <RoleEndpoint endpointName="<name-of-the-endpoint>" roleName="<name-of-the-role-containing-the-endpoint>"/>
         </Destinations>
         <AllowAllTraffic/>
         <WhenSource matches="[AnyRule]">
            <FromRole roleName="<name-of-the-role-to-allow-traffic-from>"/>
         </WhenSource>
      </OnlyAllowTrafficTo>
   </NetworkTrafficRules>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Schema-elementen
Het `NetworkTrafficRules` knoop punt van het service definitie bestand bevat deze elementen, zoals beschreven in de volgende secties in dit onderwerp:

[NetworkTrafficRules-element](#NetworkTrafficRules)

[OnlyAllowTrafficTo-element](#OnlyAllowTrafficTo)

[Doel element](#Destinations)

[RoleEndpoint-element](#RoleEndpoint)

AllowAllTraffic-element

[WhenSource-element](#WhenSource)

[FromRole-element](#FromRole)

##  <a name="networktrafficrules-element"></a><a name="NetworkTrafficRules"></a> NetworkTrafficRules-element
Het `NetworkTrafficRules` element geeft aan welke rollen met welk eind punt met een andere rol kunnen communiceren. Een service kan één `NetworkTrafficRules` definitie bevatten.

##  <a name="onlyallowtrafficto-element"></a><a name="OnlyAllowTrafficTo"></a> OnlyAllowTrafficTo-element
Het `OnlyAllowTrafficTo` element beschrijft een verzameling bestemmings eindpunten en de rollen die met hen kunnen communiceren. U kunt meerdere `OnlyAllowTrafficTo` knoop punten opgeven.

##  <a name="destinations-element"></a><a name="Destinations"></a> Doel element
Het `Destinations` element beschrijft een verzameling RoleEndpoints dan kan worden gecommuniceerd met.

##  <a name="roleendpoint-element"></a><a name="RoleEndpoint"></a> RoleEndpoint-element
Het `RoleEndpoint` element beschrijft een eind punt voor een rol om communicatie toe te staan. U kunt meerdere `RoleEndpoint` elementen opgeven als er meer dan één eind punt voor de rol is.

| Kenmerk      | Type     | Description |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Vereist. De naam van het eind punt waarnaar verkeer moet worden toegestaan.|
| `roleName`     | `string` | Vereist. De naam van de webrole waarmee communicatie moet worden toegestaan.|

## <a name="allowalltraffic-element"></a>AllowAllTraffic-element
Het `AllowAllTraffic` element is een regel waarmee alle rollen kunnen communiceren met de eind punten die in het `Destinations` knoop punt zijn gedefinieerd.

##  <a name="whensource-element"></a><a name="WhenSource"></a> WhenSource-element
Het `WhenSource` element beschrijft een verzameling functies die kunnen communiceren met de eind punten die in het `Destinations` knoop punt zijn gedefinieerd.

| Kenmerk | Type     | Description |
| --------- | -------- | ----------- |
| `matches` | `string` | Vereist. Hiermee geeft u de regel op die moet worden toegepast bij het toestaan van communicaties. De enige geldige waarde is momenteel `AnyRule` .|
  
##  <a name="fromrole-element"></a><a name="FromRole"></a> FromRole-element
`FromRole`Met het element worden de rollen opgegeven die kunnen communiceren met de eind punten die in het `Destinations` knoop punt zijn gedefinieerd. U kunt meerdere `FromRole` elementen opgeven als er meer dan één rol met de eind punten kan communiceren.

| Kenmerk  | Type     | Description |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Vereist. De naam van de rol waarmee communicatie moet worden toegestaan.|

## <a name="see-also"></a>Zie ook
[Schema voor Cloud service (klassiek)](schema-csdef-file.md)




