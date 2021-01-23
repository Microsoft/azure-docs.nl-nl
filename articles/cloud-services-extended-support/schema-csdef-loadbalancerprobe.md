---
title: Azure Cloud Services (uitgebreide ondersteuning) def. LoadBalancerProbe-schema | Microsoft Docs
description: Informatie met betrekking tot het load balancer probe-schema voor Cloud Services (uitgebreide ondersteuning)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 10e42e502a1f435d06d52d22d5c1e1924a46e575
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744556"
---
# <a name="azure-cloud-services-extended-support-definition-loadbalancerprobe-schema"></a>Azure Cloud Services (Extended support) definitie LoadBalancerProbe-schema

De load balancer test is een door de klant gedefinieerde status test van UDP-eind punten en-eind punten in rolinstanties. Het `LoadBalancerProbe` is geen zelfstandig element. het wordt gecombineerd met de webrole-of worker-rol in een service definitie bestand. Een `LoadBalancerProbe` kan worden gebruikt door meer dan één rol.

De standaard extensie voor het service definitie bestand is csdef.

## <a name="the-function-of-a-load-balancer-probe"></a>De functie van een load balancer test
De Azure Load Balancer is verantwoordelijk voor het door sturen van binnenkomend verkeer naar uw rolinstanties. De load balancer bepaalt welke instanties verkeer kunnen ontvangen door regel matig een exemplaar te zoeken om de status van dat exemplaar te bepalen. De load balancer test elke instantie meerdere keren per minuut. Er zijn twee verschillende opties voor het leveren van de instantie status van de load balancer: de standaard load balancer test of een aangepaste load balancer test, die wordt geïmplementeerd door de LoadBalancerProbe in het csdef-bestand te definiëren.

De standaard load balancer test maakt gebruik van de gast agent in de virtuele machine, die luistert en reageert alleen met een HTTP 200 OK-antwoord wanneer het exemplaar de status gereed heeft (zoals wanneer het exemplaar zich niet in de bezet, recycling, stopte, enz. Staten bevindt). Als de gast agent niet reageert met HTTP 200 OK, markeert de Azure Load Balancer het exemplaar als niet-reagerend en wordt het verzenden van verkeer naar dat exemplaar gestopt. De Azure Load Balancer blijft de instantie pingen en als de gast agent reageert met een HTTP-200, stuurt de Azure Load Balancer het verkeer naar dat exemplaar opnieuw. Wanneer u een ondernemingsrol gebruikt, wordt de code van uw website doorgaans uitgevoerd in w3wp.exe die niet wordt bewaakt door de Azure-infra structuur of gast agent. Dit betekent storingen in w3wp.exe (bijvoorbeeld HTTP 500-antwoorden) worden niet gerapporteerd aan de gast agent en het load balancer weet niet dat het exemplaar niet kan worden gedraaid.

De aangepaste load balancer test overschrijft de standaard test voor de gast agent en maakt het mogelijk om uw eigen aangepaste logica te maken om de status van de rolinstantie te bepalen. De load balancer regelt uw eind punt (standaard elke 15 seconden) en het exemplaar wordt als gedraaid beschouwd als het reageert met een TCP-ACK of HTTP 200 binnen de time-outperiode (standaard van 31 seconden). Dit kan handig zijn om uw eigen logica te implementeren om instanties uit load balancer draaiing te verwijderen, bijvoorbeeld een niet-200-status retour neren als het exemplaar hoger is dan 90% CPU. Voor webrollen die gebruikmaken van w3wp.exe, betekent dit ook dat u de automatische controle van uw website krijgt, omdat storingen in de website code een niet-200 status retour neren naar de load balancer-test. Als u geen LoadBalancerProbe definieert in het csdef-bestand, wordt het standaard gedrag voor load balancer (zoals eerder beschreven) gebruikt.

Als u een aangepaste load balancer test gebruikt, moet u ervoor zorgen dat uw logica rekening houdt met de methode RoleEnvironment. OnStop. Wanneer u de standaard load balancer test gebruikt, wordt het exemplaar uit de rotatie gehaald voordat de OnStop wordt aangeroepen, maar een aangepaste load balancer test kan blijven terugkeren naar een 200 OK tijdens de gebeurtenis OnStop. Als u de OnStop-gebeurtenis gebruikt voor het opschonen van de cache, het stoppen van de service of het wijzigen van wijzigingen die van invloed kunnen zijn op het runtime-gedrag van uw service, moet u ervoor zorgen dat uw aangepaste load balancer probe Logic het exemplaar uit de rotatie verwijdert.

## <a name="basic-service-definition-schema-for-a-load-balancer-probe"></a>Basis-service definitie schema voor een load balancer test
 De basis indeling van een service definitie bestand met een load balancer test is als volgt.

```xml
<ServiceDefinition …>
   <LoadBalancerProbes>
      <LoadBalancerProbe name="<load-balancer-probe-name>" protocol="[http|tcp]" path="<uri-for-checking-health-status-of-vm>" port="<port-number>" intervalInSeconds="<interval-in-seconds>" timeoutInSeconds="<timeout-in-seconds>"/>
   </LoadBalancerProbes>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Schema-elementen
Het `LoadBalancerProbes` element van het service definitie bestand bevat de volgende elementen:

- [LoadBalancerProbes-element](#LoadBalancerProbes)
- [LoadBalancerProbe-element](#LoadBalancerProbe)

##  <a name="loadbalancerprobes-element"></a><a name="LoadBalancerProbes"></a> LoadBalancerProbes-element
Met het `LoadBalancerProbes` element wordt de verzameling Load Balancer tests beschreven. Dit element is het bovenliggende element van het [element LoadBalancerProbe](#LoadBalancerProbe). 

##  <a name="loadbalancerprobe-element"></a><a name="LoadBalancerProbe"></a> LoadBalancerProbe-element
Het `LoadBalancerProbe` element definieert de status test voor een model. U kunt meerdere load balancer tests definiëren. 

In de volgende tabel worden de kenmerken van het `LoadBalancerProbe` element beschreven:

|Kenmerk|Type|Beschrijving|
| ------------------- | -------- | -----------------|
| `name`              | `string` | Vereist. De naam van de load balancer-test. De naam moet uniek zijn.|
| `protocol`          | `string` | Vereist. Hiermee geeft u het Protocol van het eind punt. Mogelijke waarden zijn `http` en `tcp`. Als `tcp` is opgegeven, is een ontvangen ack vereist om de test te laten slagen. Als `http` is opgegeven, is er een antwoord van 200 OK van de opgegeven URI vereist om de test te laten slagen.|
| `path`              | `string` | De URI die wordt gebruikt voor het aanvragen van de integriteits status van de virtuele machine. `path` is vereist als `protocol` is ingesteld op `http` . Als dat niet het geval is, is dit niet toegestaan.<br /><br /> Er is geen standaard waarde.|
| `port`              | `integer` | Optioneel. De poort voor het communiceren van de test. Dit is optioneel voor elk eind punt, omdat dezelfde poort wordt gebruikt voor de test. U kunt ook een andere poort voor hun zoek opdracht configureren. Mogelijke waarden variëren van 1 tot 65535, inclusief.<br /><br /> De standaard waarde wordt ingesteld door het eind punt.|
| `intervalInSeconds` | `integer` | Optioneel. Het interval in seconden voor hoe vaak het eind punt moet worden getest op de status. Normaal gesp roken is het interval iets minder dan de helft van de toegewezen time-outperiode (in seconden) waarmee twee volledige tests kunnen worden uitgevoerd voordat het exemplaar uit de rotatie kan worden gehaald.<br /><br /> De standaard waarde is 15, de minimum waarde is 5.|
| `timeoutInSeconds`  | `integer` | Optioneel. De time-outperiode, in seconden, die wordt toegepast op de test, waarbij geen enkele reactie leidt tot het stoppen van verdere verkeer van de bezorging bij het eind punt. Met deze waarde kunnen eind punten sneller of langzamer worden gemaakt dan de typische tijden die worden gebruikt in azure (dit zijn de standaard waarden).<br /><br /> De standaard waarde is 31, de minimum waarde is 11.|

## <a name="see-also"></a>Zie ook
[Definitie schema van Cloud service (uitgebreide ondersteuning)](schema-csdef-file.md).