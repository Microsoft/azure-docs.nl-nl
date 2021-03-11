---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c305cc356351019bb3c94ca80a873ff94b53c6ed
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102610764"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Voor Azure Data Explorer-versleuteling at-rest moet een door de klant beheerde sleutel worden gebruikt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81e74cea-30fd-40d5-802f-d72103c2aaaa) |Als u versleuteling at rest inschakelt waarbij gebruik wordt gemaakt van een door de klant beheerde sleutel in uw Azure Data Explorer-cluster, hebt u meer controle over de sleutel die wordt gebruikt door de versleuteling at rest. Deze functie is vaak van toepassing op klanten met speciale nalevingsvereisten, en vereist een sleutelkluis voor het beheren van de sleutels. |Controleren, Weigeren, Uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_CMK.json) |
|[Schijfversleuteling moet zijn ingeschakeld voor Azure Data Explorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |Door schijfversleuteling in te schakelen, beveiligt en beschermt u uw gegevens conform de beveiligings- en nalevingsvereisten van uw organisatie. |Controleren, Weigeren, Uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|[Dubbele versleuteling moet zijn ingeschakeld voor Azure Data Explorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |Door dubbele versleuteling in te schakelen, beveiligt en beschermt u uw gegevens conform de beveiligings- en nalevingsvereisten van uw organisatie. Wanneer dubbele versleuteling is ingeschakeld, worden de gegevens in het opslagaccount tweemaal versleuteld, eenmaal op serviceniveau en eenmaal op infrastructuurniveau, met behulp van twee verschillende versleutelingsalgoritmes en twee verschillende sleutels. |Controleren, Weigeren, Uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|[Virtueelnetwerkinjectie moet zijn ingeschakeld voor Azure Data Explorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ad2fd1f-b25f-47a2-aa01-1a5a779e6413) |Beveilig uw netwerkperimeter met virtueelnetwerkinjectie. Hiermee kunt u groepsregels voor netwerkbeveiliging afdwingen, on-premises verbinden en uw gegevensverbindingsbronnen beveiligen met service-eindpunten. |Controleren, Weigeren, Uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_VNET_configured.json) |
