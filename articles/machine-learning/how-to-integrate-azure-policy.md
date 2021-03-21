---
title: Naleving van beleid controleren en beheren
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van Azure Policy voor het gebruik van ingebouwde beleids regels voor Azure Machine Learning om ervoor te zorgen dat uw werk ruimten voldoen aan uw vereisten.
author: aashishb
ms.author: aashishb
ms.date: 03/12/2021
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: 21b07130e99ad4fac9a0a9b2d11aca852a1f205f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584309"
---
# <a name="audit-and-manage-azure-machine-learning-using-azure-policy"></a>Azure Machine Learning controleren en beheren met behulp van Azure Policy

[Azure Policy](../governance/policy/index.yml) is een beheer programma waarmee u ervoor kunt zorgen dat Azure-resources voldoen aan uw beleid. Met Azure Machine Learning kunt u de volgende beleids regels toewijzen:

* Door de **klant beheerde sleutel**: controleren of afdwingen of werk ruimten een door de klant beheerde sleutel moeten gebruiken.
* **Persoonlijke koppeling**: controleren of afdwingen of werk ruimten een persoonlijk eind punt gebruiken om te communiceren met een virtueel netwerk.
* **Persoonlijk eind punt**: Configureer het Azure Virtual Network-subnet waar het persoonlijke eind punt moet worden gemaakt.
* **Privé-DNS zone**: Configureer de privé-DNS-zone die moet worden gebruikt voor de privé-koppeling.

Beleids regels kunnen worden ingesteld op verschillende bereiken, zoals op het niveau van het abonnement of de resource groep. Zie de [Azure Policy-documentatie](../governance/policy/overview.md)voor meer informatie.

## <a name="built-in-policies"></a>Ingebouwd beleid

Azure Machine Learning biedt een set beleids regels die u kunt gebruiken voor algemene scenario's met Azure Machine Learning. U kunt deze beleids definities toewijzen aan uw bestaande abonnement of gebruiken als basis voor het maken van uw eigen aangepaste definities. Zie voor een volledige lijst van het ingebouwde beleid voor Azure Machine Learning [ingebouwde beleids regels voor Azure machine learning](../governance/policy/samples/built-in-policies.md#machine-learning).

Gebruik de volgende stappen om de ingebouwde beleids definities weer te geven die betrekking hebben op Azure Machine Learning:

1. Ga naar __Azure Policy__ in het [Azure Portal](https://portal.azure.com).
1. Selecteer __Definities__.
1. Selecteer bij __type__ de optie _ingebouwd_ en selecteer bij __categorie__ de optie __machine learning__.

Hier kunt u beleids definities selecteren om ze weer te geven. Tijdens het weer geven van een definitie kunt u de koppeling __toewijzen__ gebruiken om het beleid toe te wijzen aan een specifiek bereik en de para meters voor het beleid configureren. Zie [een beleid toewijzen-Portal](../governance/policy/assign-policy-portal.md)voor meer informatie.

U kunt ook beleids regels toewijzen met behulp van [Azure PowerShell](../governance/policy/assign-policy-powershell.md), [Azure cli](../governance/policy/assign-policy-azurecli.md)en [sjablonen](../governance/policy/assign-policy-template.md).

## <a name="workspace-encryption-with-customer-managed-key"></a>Werkruimte versleuteling met door de klant beheerde sleutel

Hiermee wordt bepaald of een werk ruimte moet worden versleuteld met een door de klant beheerde sleutel of met een door micro soft beheerde sleutel voor het versleutelen van metrische gegevens en meta gegevens. Zie de sectie [Azure Cosmos DB](concept-data-encryption.md#azure-cosmos-db) van het artikel voor gegevens versleuteling voor meer informatie over het gebruik van door de klant beheerde sleutel.

Als u dit beleid wilt configureren, stelt u de para meter effect in op __controleren__ of __weigeren__. Als deze is ingesteld op __audit__, kunt u een werk ruimte maken zonder een door de klant beheerde sleutel en wordt er een waarschuwings gebeurtenis in het activiteiten logboek gemaakt.

Als het beleid is ingesteld op __weigeren__, kunt u geen werk ruimte maken, tenzij er een door de klant beheerde sleutel wordt opgegeven. Als u probeert een werk ruimte te maken zonder een door de klant beheerde sleutel, resulteert dit in een fout die vergelijkbaar is met `Resource 'clustername' was disallowed by policy` en wordt er een fout in het activiteiten logboek gemaakt. De beleids-id wordt ook geretourneerd als onderdeel van deze fout.

## <a name="workspace-should-use-private-link"></a>De werk ruimte moet een persoonlijke koppeling gebruiken

Hiermee bepaalt u of een werk ruimte Azure private link moet gebruiken om te communiceren met Azure Virtual Network. Zie [persoonlijke koppeling voor een werk ruimte configureren](how-to-configure-private-link.md)voor meer informatie over het gebruik van een persoonlijke koppeling.

Als u dit beleid wilt configureren, stelt u de para meter effect in op __controleren__ of __weigeren__. Als deze is ingesteld op __audit__, kunt u een werk ruimte maken zonder een persoonlijke koppeling te gebruiken en wordt er een waarschuwings gebeurtenis in het activiteiten logboek gemaakt.

Als het beleid is ingesteld op __weigeren__, kunt u geen werk ruimte maken, tenzij u een persoonlijke koppeling gebruikt. Als u een werk ruimte probeert te maken zonder een persoonlijke koppeling, resulteert dit in een fout. De fout wordt ook vastgelegd in het activiteiten logboek. De beleids-id wordt geretourneerd als onderdeel van deze fout.

## <a name="workspace-should-use-private-endpoint"></a>De werk ruimte moet een persoonlijk eind punt gebruiken

Hiermee configureert u een werk ruimte voor het maken van een persoonlijk eind punt binnen het opgegeven subnet van een Azure-Virtual Network.

Als u dit beleid wilt configureren, stelt u de effect parameter in op __DeployIfNotExists__. Stel de __privateEndpointSubnetID__ in op de Azure Resource Manager id van het subnet.
## <a name="workspace-should-use-private-dns-zones"></a>De werk ruimte moet gebruikmaken van particuliere DNS-zones

Hiermee configureert u een werk ruimte voor het gebruik van een privé-DNS-zone, waarbij de standaard-DNS-omzetting voor een persoonlijk eind punt wordt genegeerd.

Als u dit beleid wilt configureren, stelt u de effect parameter in op __DeployIfNotExists__. Stel de __privateDnsZoneId__ in op de Azure Resource Manager-id van de privé-DNS-zone die moet worden gebruikt. 

## <a name="next-steps"></a>Volgende stappen

* [Documentatie voor Azure Policy](../governance/policy/overview.md)
* [Ingebouwd beleid voor Azure Machine Learning](policy-reference.md)
* [Werken met beveiligings beleid met Azure Security Center](../security-center/tutorial-security-policy.md)