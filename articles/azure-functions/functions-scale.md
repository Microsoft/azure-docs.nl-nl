---
title: Schaal en hosting van Azure Functions
description: Meer informatie over hoe u kunt kiezen tussen het Azure Functions verbruiks abonnement en het Premium-abonnement.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 08/17/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 490fa46deabc822e416705fe9bf9c5cdb58f8cd6
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936755"
---
# <a name="azure-functions-hosting-options"></a>Opties voor Azure Functions hosten

Wanneer u een functie-app in azure maakt, moet u een hosting abonnement kiezen voor uw app. Er zijn drie elementaire hosting plannen beschikbaar voor Azure Functions: [verbruiks abonnement](consumption-plan.md), [Premium plan](functions-premium-plan.md)en [toegewezen (app service)-abonnement](dedicated-plan.md). Alle hosting plannen zijn algemeen beschikbaar (GA) op virtuele machines met Linux en Windows.

Het hosting abonnement dat u kiest, bepaalt het volgende gedrag:

* Hoe de functie-app wordt geschaald.
* De bronnen die beschikbaar zijn voor elk exemplaar van de functie-app.
* Ondersteuning voor geavanceerde functionaliteit, zoals Azure Virtual Network-connectiviteit.

In dit artikel vindt u een gedetailleerde vergelijking tussen de verschillende hosting plannen, samen met op Kubernetes gebaseerde hosting.

## <a name="overview-of-plans"></a>Overzicht van plannen

Hieronder vindt u een overzicht van de voor delen van de drie belangrijkste hosting plannen voor-functies:

| | |
| --- | --- |  
|**[Verbruiksabonnement](consumption-plan.md)**| Schaal automatisch en betaal alleen voor reken resources wanneer uw functies worden uitgevoerd.<br/><br/>In het verbruiks plan worden instanties van de functions-host dynamisch toegevoegd en verwijderd op basis van het aantal binnenkomende gebeurtenissen.<br/><br/> ✔ Standaard hosting plan.<br/>✔ Betaal alleen wanneer uw functies worden uitgevoerd.<br/>✔ Automatisch schalen, zelfs tijdens het aantal Peri Oden van hoge belasting.|  
|**[Premium-abonnement](functions-premium-plan.md)**|Schaalt automatisch op basis van de vraag met vooraf gewarmde werk rollen die toepassingen uitvoeren zonder vertraging na inactiviteit, worden uitgevoerd op krachtigere instanties en maakt verbinding met virtuele netwerken. <br/><br/>Bekijk het Azure Functions Premium-abonnement in de volgende situaties: <br/><br/>✔ Uw functie-apps continu of bijna continu worden uitgevoerd.<br/>✔ U een groot aantal kleine uitvoeringen en een hoge uitvoerings factuur hebt, maar in het verbruiks abonnement is er minder dan een seconde.<br/>✔ U meer CPU-of geheugen opties nodig hebt dan wat wordt aangegeven door het verbruiks abonnement.<br/>✔ De code moet langer worden uitgevoerd dan de Maxi maal toegestane uitvoerings tijd voor het verbruiks abonnement.<br/>✔ U functies nodig hebt die niet beschikbaar zijn in het verbruiks abonnement, zoals verbinding met het virtuele netwerk.|  
|**[Toegewezen abonnement](dedicated-plan.md)** |Voer uw functies uit binnen een App Service plan op regel matige [app service plan tarieven](https://azure.microsoft.com/pricing/details/app-service/windows/).<br/><br/>Geschikt voor langlopende scenario's waarbij [Durable functions](durable/durable-functions-overview.md) niet kan worden gebruikt. Houd rekening met een App Service-abonnement in de volgende situaties:<br/><br/>✔ U bestaande, geApp Servicede virtuele machines die al worden uitgevoerd, worden gebruikt.<br/>✔ U een aangepaste installatie kopie wilt opgeven waarop uw functies moeten worden uitgevoerd. <br/>✔ Voorspellende schaling en kosten zijn vereist.|  

De vergelijkings tabellen in dit artikel bevatten ook de volgende hosting opties, die de hoogste mate van controle en isolatie bieden waarin u uw functie-apps kunt uitvoeren.  

| | |
| --- | --- |  
|**[ASE](dedicated-plan.md)** | App Service Environment (ASE) is een App Service functie die een volledig geïsoleerde en toegewezen omgeving biedt voor het veilig uitvoeren van App Service-apps op grote schaal.<br/><br/>As zijn geschikt voor werk belastingen van toepassingen die nodig zijn voor: <br/><br/>✔ Zeer grote schaal.<br/>✔ Volledige Compute-isolatie en beveiligde netwerk toegang.<br/>✔ Hoog geheugen gebruik.|  
| **[Kubernetes](functions-kubernetes-keda.md)** | Kubernetes biedt een volledig geïsoleerde en toegewezen omgeving die boven op het Kubernetes-platform wordt uitgevoerd.<br/><br/> Kubernetes is geschikt voor werk belastingen van toepassingen die het volgende vereisen: <br/>Aangepaste hardwarevereisten ✔.<br/>✔ Isolatie en beveiligde netwerk toegang.<br/>✔ Mogelijkheid om uit te voeren in een hybride of multi-cloud omgeving.<br/>✔ Uitgevoerd naast bestaande Kubernetes-toepassingen en-services.|  

In de overige tabellen in dit artikel worden de plannen vergeleken met verschillende functies en gedragingen. Zie de [pagina met prijzen voor Azure functions](https://azure.microsoft.com/pricing/details/functions/)voor een kosten vergelijking tussen dynamische hosting plannen (verbruik en Premium). Zie de [pagina met app service prijzen](https://azure.microsoft.com/pricing/details/app-service/windows/)voor prijzen van de verschillende opties voor toegewezen abonnementen. 

## <a name="operating-systemruntime"></a>Besturings systeem/runtime

De volgende tabel geeft een lijst van ondersteunde besturingssysteem-en taal runtime-ondersteuning voor de hosting plannen.

| | Linux<sup>1</sup><br/>Alleen code | Windows<sup>2</sup><br/>Alleen code | Linux<sup>1, 3</sup><br/>Docker-container |
| --- | --- | --- | --- |
| **[Verbruiksabonnement](consumption-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python | .NET Core<br/>Node.js<br/>Java<br/>PowerShell Core | Geen ondersteuning  |
| **[Premium-abonnement](functions-premium-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python  | 
| **[Toegewezen abonnement](dedicated-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |
| **[ASE](dedicated-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core  |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python | 
| **[Kubernetes](functions-kubernetes-keda.md)** | n.v.t. | n.v.t. |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |

<sup>1</sup> Linux is het enige besturings systeem dat wordt ondersteund voor de python-runtime stack. <br/>
<sup>2</sup> Windows is het enige besturings systeem dat wordt ondersteund voor de Power shell-runtime stack.<br/>
<sup>3</sup> Linux is het enige besturings systeem dat wordt ondersteund voor docker-containers.<br/>

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="scale"></a>Schalen

De volgende tabel vergelijkt het schaal gedrag van de verschillende hosting plannen.

| | Uitschalen | Maximum aantal instanties |
| --- | --- | --- |
| **[Verbruiksabonnement](consumption-plan.md)** | [Gestuurde gebeurtenis](event-driven-scaling.md). Automatisch uitschalen, zelfs tijdens het aantal Peri Oden van hoge belasting. Met Azure Functions-infra structuur worden CPU-en geheugen bronnen geschaald door extra exemplaren van de functions-host toe te voegen, op basis van het aantal inkomende trigger gebeurtenissen. | 200 |
| **[Premium-abonnement](functions-premium-plan.md)** | [Gestuurde gebeurtenis](event-driven-scaling.md). Automatisch uitschalen, zelfs tijdens het aantal Peri Oden van hoge belasting. Met Azure Functions-infra structuur worden CPU-en geheugen bronnen geschaald door extra exemplaren van de functions-host toe te voegen op basis van het aantal gebeurtenissen waarvoor de functies zijn geactiveerd. |100|
| **[Toegewezen abonnement](dedicated-plan.md)**<sup>1</sup> | Hand matig/automatisch schalen |10-20|
| **[ASE](dedicated-plan.md)**<sup>1</sup> | Hand matig/automatisch schalen |100 |
| **[Kubernetes](functions-kubernetes-keda.md)**  | Gebeurtenis gerichte automatisch schalen voor Kubernetes-clusters met behulp van [KEDA](https://keda.sh). | Is afhankelijk &nbsp; van het &nbsp; cluster&nbsp;&nbsp;|

<sup>1</sup> zie de [app service plan limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)voor specifieke limieten voor de verschillende opties voor het app service plan.

## <a name="cold-start-behavior"></a>Gedrag van koude start

|    |    | 
| -- | -- |
| **[Verbruiks &nbsp; abonnement](consumption-plan.md)** | Apps kunnen op nul worden geschaald wanneer ze niet actief zijn, wat betekent dat sommige aanvragen mogelijk extra latentie bij het opstarten hebben.  Het verbruiks plan heeft een aantal optimalisaties waarmee u de koude start tijd kunt verlagen, inclusief het ophalen van vooraf gewarmde tijdelijke aanduidingen waarvoor al de host-en taal processen van de functie worden uitgevoerd. |
| **[Premium-abonnement](functions-premium-plan.md)** | Ongebruikte en warme exemplaren om te voor komen dat ze koud worden gestart. |
| **[Toegewezen abonnement](dedicated-plan.md)** | Bij uitvoering in een speciaal plan kan de host functions continu uitvoeren, wat betekent dat koude start niet echt een probleem is. |
| **[ASE](dedicated-plan.md)** | Bij uitvoering in een speciaal plan kan de host functions continu uitvoeren, wat betekent dat koude start niet echt een probleem is. |
| **[Kubernetes](functions-kubernetes-keda.md)**  | Afhankelijk van de configuratie van KEDA kunnen apps worden geconfigureerd om een koude start te voor komen. Indien geconfigureerd om te worden geschaald naar nul, is er een koude start voor nieuwe gebeurtenissen. 

## <a name="service-limits"></a>Servicelimieten

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]

## <a name="networking-features"></a>Netwerkfuncties

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="billing"></a>Billing

| | | 
| --- | --- |
| **[Verbruiksabonnement](consumption-plan.md)** | Betaal alleen voor de tijd dat uw functies worden uitgevoerd. De facturering is dan ook gebaseerd op het aantal uitvoeringen, de uitvoeringstijd en het gebruikte geheugen. |
| **[Premium-abonnement](functions-premium-plan.md)** | Het Premium-abonnement is gebaseerd op het aantal kern seconden en het geheugen dat wordt gebruikt voor alle benodigde en vooraf gewarmde instanties. Ten minste één exemplaar per plan moet altijd warme worden bewaard. Dit abonnement biedt de meest voorspel bare prijzen. |
| **[Toegewezen plan](dedicated-plan.md)* | U betaalt hetzelfde voor functie-apps in een App Service plan zoals u zou doen voor andere App Service resources, zoals web-apps.|
| **[App Service Environment (ASE)](dedicated-plan.md)** | Er is een vast maand bedrag voor een ASE dat betaalt voor de infra structuur en niet wordt gewijzigd door de grootte van de ASE. Er zijn ook kosten per App Service plan vCPU. Alle apps die worden gehost in een AS-omgeving, vallen onder de Geïsoleerde prijs-SKU. |
| **[Kubernetes](functions-kubernetes-keda.md)**| U betaalt alleen de kosten van uw Kubernetes-cluster. geen extra facturering voor-functies. De functie-app wordt uitgevoerd als een toepassings workload boven op het cluster, net als bij een gewone app. |

## <a name="next-steps"></a>Volgende stappen

+ [Implementatie technologieën in Azure Functions](functions-deployment-technologies.md) 
+ [Ontwikkelaarshandleiding voor Azure Functions](functions-reference.md)