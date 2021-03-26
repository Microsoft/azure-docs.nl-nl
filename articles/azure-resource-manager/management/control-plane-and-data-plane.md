---
title: Beheer vlak-en gegevenslaag bewerkingen
description: Hierin wordt het verschil tussen besturings vlak en gegevenslaag bewerkingen beschreven. Bewerkingen voor het beheer vlak worden verwerkt door Azure Resource Manager. Data-vlak bewerkingen worden verwerkt door een service.
ms.topic: conceptual
ms.date: 09/10/2020
ms.openlocfilehash: 76304c81a1af1eef87d12cfd4130867851a61d28
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105544091"
---
# <a name="azure-control-plane-and-data-plane"></a>Beheer vlak en gegevenslaag van Azure

Azure-bewerkingen kunnen worden onderverdeeld in twee categorieën: besturings vlak en gegevens vlak. In dit artikel worden de verschillen tussen deze twee typen bewerkingen beschreven.

U kunt het besturings vlak gebruiken om resources in uw abonnement te beheren. U gebruikt het gegevens vlak om mogelijkheden te gebruiken die worden weer gegeven door uw exemplaar van een resource type.

Bijvoorbeeld:

* U maakt een virtuele machine via het besturings vlak. Nadat de virtuele machine is gemaakt, kunt u hiermee communiceren via gegevenslaag bewerkingen, zoals Remote Desktop Protocol (RDP).

* U kunt een opslag account maken via het besturings vlak. U gebruikt het gegevens vlak voor het lezen en schrijven van gegevens in het opslag account.

* U maakt een Cosmos-data base via het besturings vlak. Als u gegevens wilt opvragen in de Cosmos-data base, gebruikt u het vlak data.

## <a name="control-plane"></a>Besturingsvlak

Alle aanvragen voor beheer vlak bewerkingen worden verzonden naar de URL van de Azure Resource Manager. Deze URL is afhankelijk van de Azure-omgeving.

* Voor Azure Global is de URL `https://management.azure.com` .
* Voor Azure Government is de URL `https://management.usgovcloudapi.net/` .
* Voor Azure Duitsland is de URL `https://management.microsoftazure.de/` .
* Voor Microsoft Azure-China 21Vianet is de URL `https://management.chinacloudapi.cn` .

Zie [Azure rest API](/rest/api/azure/)als u wilt weten welke bewerkingen de Azure Resource Manager URL gebruiken. De [bewerking maken of bijwerken](/rest/api/mysql/databases/createorupdate) voor MySQL is bijvoorbeeld een bewerking voor een besturings vlak, omdat de aanvraag-URL:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{serverName}/databases/{databaseName}?api-version=2017-12-01
```

Azure Resource Manager verwerkt alle Control-aanvragen. De Azure-functies die u hebt geïmplementeerd voor het beheren van uw resources, worden automatisch toegepast, zoals:

* [Azure RBAC (op rollen gebaseerd toegangsbeheer van Azure)](../../role-based-access-control/overview.md)
* [Azure Policy](../../governance/policy/overview.md)
* [Beheer vergrendelingen](lock-resources.md)
* [Activiteitenlogboeken](view-activity-logs.md)

Nadat de aanvraag is geverifieerd, verzendt Azure Resource Manager deze naar de resource provider, waarmee de bewerking wordt voltooid.

Het besturings vlak bevat twee scenario's voor het verwerken van aanvragen: "groen veld" en "bruin veld". Groen veld verwijst naar nieuwe resources. Het veld bruin verwijst naar bestaande resources. Wanneer u resources implementeert, Azure Resource Manager begrijpt wanneer u nieuwe resources maakt en wanneer u bestaande resources bijwerkt. U hoeft zich geen zorgen te maken dat er identieke resources worden gemaakt.

## <a name="data-plane"></a>Gegevenslaag

Aanvragen voor gegevens vlak bewerkingen worden verzonden naar een eind punt dat specifiek is voor uw exemplaar. De [bewerking taal detecteren](/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-language-detection) in cognitive Services is bijvoorbeeld een gegevensvlak bewerking, omdat de aanvraag-URL:

```http
POST {Endpoint}/text/analytics/v2.0/languages
```

Data-vlak bewerkingen zijn niet beperkt tot REST API. Hiervoor zijn mogelijk aanvullende referenties vereist, zoals aanmelden bij een virtuele machine of database server.

Functies die beheer en governance afdwingen, worden mogelijk niet toegepast op Data vlak bewerkingen. U moet rekening houden met de verschillende manieren waarop gebruikers communiceren met uw oplossingen. Een vergren deling waarmee wordt voor komen dat gebruikers een Data Base verwijderen, voor komt bijvoorbeeld niet dat gebruikers gegevens verwijderen via query's.

U kunt bepaalde beleids regels gebruiken om gegevenslaag bewerkingen te regelen. Zie voor meer informatie [resource provider modi (preview) in azure Policy](../../governance/policy/concepts/definition-structure.md#resource-provider-modes).

## <a name="next-steps"></a>Volgende stappen

* Zie [Wat is Azure Resource Manager?](overview.md) voor een overzicht van Azure Resource Manager.

* Zie [de impact van een nieuwe Azure Policy definitie evalueren](../../governance/policy/concepts/evaluate-impact.md)voor meer informatie over het effect van beleids definities op nieuwe resources en bestaande resources...
