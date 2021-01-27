---
title: Uw apps controleren zonder code wijzigingen-automatische instrumentatie voor Azure Monitor Application Insights | Microsoft Docs
description: Overzicht van automatische instrumentatie voor het beheer van de toepassings prestaties van Azure Monitor Application Insights code
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/31/2020
ms.reviewer: mbullwin
ms.openlocfilehash: efa951ce5a15460e3eacfd4c7abecfac17106b4e
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880505"
---
# <a name="what-is-auto-instrumentation-or-codeless-attach---azure-monitor-application-insights"></a>Wat is het automatisch instrumenteren of koppelen Azure Monitor Application Insights?

Met automatische instrumentatie, of codeloos koppelen, kunt u toepassings bewaking met Application Insights inschakelen zonder uw code te wijzigen.  

Application Insights is geïntegreerd met verschillende resource providers en werkt in verschillende omgevingen. In essentie moet u, en in sommige gevallen: de agent configureren, die de telemetrie uit het vak wordt verzameld. In geen enkele tijd ziet u de metrieken, gegevens en afhankelijkheden in uw Application Insights resource, waarmee u de bron van potentiële problemen kunt herkennen voordat ze optreden, en de hoofd oorzaak analyseren met een end-to-end-transactie weergave.

## <a name="supported-environments-languages-and-resource-providers"></a>Ondersteunde omgevingen, talen en resource providers

Wanneer we meer en meer integraties toevoegen, wordt de matrix voor automatische instrumentatie complex. In de volgende tabel ziet u de huidige status van de materie, als ondersteuning voor verschillende resource providers, talen en omgevingen.

|Omgeving/resource provider          | .NET            | .NET Core       | Java            | Node.js         | Python          |
|---------------------------------------|-----------------|-----------------|-----------------|-----------------|-----------------|
|Azure App Service in Windows           | GA, OnBD *       | GA, opt-in      | Persoonlijke preview | Persoonlijke preview | Niet ondersteund   |
|Azure App Service in Linux             | N.v.t.             | Niet ondersteund   | Persoonlijke preview | Openbare preview  | Niet ondersteund   |
|Azure App Service op AKS               | N.v.t.             | In de ontwerp       | In de ontwerp       | In de ontwerp       | Niet ondersteund   |
|Azure Functions-basis                | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       |
|Azure Functions Windows-afhankelijkheden | Niet ondersteund   | Niet ondersteund   | Openbare preview  | Niet ondersteund   | Niet ondersteund   |
|Azure Kubernetes Service               | N.v.t.             | In de ontwerp       | Via agent   | In de ontwerp       | Niet ondersteund   |
|Azure Vm's Windows                      | Openbare preview  | Niet ondersteund   | Niet ondersteund   | Niet ondersteund   | Niet ondersteund   |
|On-premises Vm's Windows                | GA, opt-in      | Niet ondersteund   | Via agent   | Niet ondersteund   | Niet ondersteund   |
|Zelfstandige agent: elke env.            | Niet ondersteund   | Niet ondersteund   | Algemene beschikbaarheid              | Niet ondersteund   | Niet ondersteund   |

* OnBD is op de standaard waarde ingeschakeld. de Application Insights wordt automatisch geactiveerd wanneer u uw app in ondersteunde omgevingen implementeert. 

## <a name="azure-app-service"></a>Azure App Service

### <a name="windows"></a>Windows

[Toepassings bewaking op Azure app service](./azure-web-apps.md?tabs=net) is beschikbaar voor .NET-toepassingen en is standaard ingeschakeld, .net core kan worden ingeschakeld met één klik en Java en Node.js zijn in een persoonlijke preview.

### <a name="linux"></a>Linux 

Bewaking van Java-en Node.js-toepassingen in App Service is in open bare preview en kan worden ingeschakeld in Azure Portal, beschikbaar in alle regio's.

## <a name="azure-functions"></a>Azure Functions

De basis bewaking voor Azure Functions is standaard ingeschakeld voor het verzamelen van logboek-, prestatie-, fout gegevens-en HTTP-aanvragen. Voor Java-toepassingen kunt u uitgebreide bewaking met gedistribueerde tracering inschakelen en de end-to-end-transactie Details ophalen. Deze functionaliteit voor Java bevindt zich in de open bare preview en u kunt [deze functie inschakelen in azure Portal](./monitor-functions.md).

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Er is momenteel code instrumentatie van de Azure Kubernetes-service beschikbaar voor Java-toepassingen via de [zelfstandige agent](./java-in-process-agent.md). 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>Azure Windows-Vm's en schaal sets voor virtuele machines

[Automatische instrumentatie voor Azure-vm's en schaal sets voor virtuele machines](./azure-vm-vmss-apps.md) is beschikbaar voor .NET-toepassingen 

## <a name="on-premises-servers"></a>On-premises servers
U kunt de bewaking eenvoudig inschakelen voor uw [on-premises Windows-servers voor .NET-toepassingen](./status-monitor-v2-overview.md) en voor [Java-apps](./java-in-process-agent.md).

## <a name="other-environments"></a>Andere omgevingen
De veelzijdige Java-agent werkt op elke omgeving, maar u hoeft uw code niet te instrumenteren. [Volg de richt lijnen](./java-in-process-agent.md) voor het inschakelen van Application Insights en lees meer over de fantastische mogelijkheden van de Java-Agent. De agent bevindt zich in de open bare preview-versie en is beschikbaar voor alle regio's. 

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Application Insights](./app-insights-overview.md)
* [Toepassings overzicht](./app-map.md)
* [End-to-end prestatie bewaking](../learn/tutorial-performance.md)

