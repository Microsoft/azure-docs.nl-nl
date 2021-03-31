---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 04/15/2020
ms.author: cephalin
ms.openlocfilehash: 0fccb1ed092190f88d197c9bf2cd17678a4d1731
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "83649046"
---
De platform onderdelen van App Service, waaronder Azure Vm's, opslag, netwerk verbindingen, Web frameworks, beheer-en integratie functies, zijn actief beveiligd en gehard. App Service doorlopende controle van de naleving door de krachtige controles om ervoor te zorgen dat:

- Uw app-resources worden [beveiligd](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) vanuit de Azure-resources van de andere klanten.
- [VM-exemplaren en runtime-software worden regel matig bijgewerkt](../articles/app-service/overview-patch-os-runtime.md) om onlangs ontdekte beveiligings problemen op te lossen. 
- Communicatie van geheimen (zoals verbindings reeksen) tussen uw app en andere Azure-resources (zoals [SQL database](https://azure.microsoft.com/services/sql-database/)) blijft binnen Azure en heeft geen netwerk grenzen. Geheimen worden altijd versleuteld wanneer ze worden opgeslagen.
- Alle communicatie via de App Service connectiviteits functies, zoals [hybride verbinding](../articles/app-service/app-service-hybrid-connections.md), wordt versleuteld. 
- Verbindingen met hulpprogram ma's voor extern beheer zoals Azure PowerShell, Azure CLI, Azure Sdk's, REST Api's, worden allemaal versleuteld.
- 24-uurs Threat Management beschermt de infra structuur en het platform tegen malware, gedistribueerde Denial-of-service (DDoS), man-in-the-Middle (MITM) en andere bedreigingen.

Zie [Vertrouwenscentrum van Azure](https://azure.microsoft.com/overview/trusted-cloud/)voor meer informatie over de infra structuur en platform beveiliging in Azure.