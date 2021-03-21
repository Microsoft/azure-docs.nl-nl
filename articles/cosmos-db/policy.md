---
title: Azure Policy gebruiken voor het implementeren van governance en controles voor Azure Cosmos DB resources
description: Meer informatie over het gebruik van Azure Policy voor het implementeren van governance en controles voor Azure Cosmos DB resources.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 1390f5db6e0f0370788bef60d5a2cafee1e8a96d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93080648"
---
# <a name="use-azure-policy-to-implement-governance-and-controls-for-azure-cosmos-db-resources"></a>Azure Policy gebruiken voor het implementeren van governance en controles voor Azure Cosmos DB resources
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

[Azure Policy](../governance/policy/overview.md) helpt de governance-standaarden van organisaties af te dwingen, bron naleving te beoordelen en automatisch herstel te implementeren. Veelvoorkomende gebruiks gevallen zijn beveiliging, kosten beheer en consistentie van de configuratie.

Azure Policy biedt ingebouwde beleids definities. U kunt aangepaste beleids definities maken voor scenario's die niet worden behandeld door de ingebouwde beleids definities. Raadpleeg de [Azure Policy-documentatie](../governance/policy/overview.md) voor meer informatie.

> [!IMPORTANT]
> Azure Policy wordt afgedwongen op het niveau van de resource provider voor Azure-Services. Cosmos DB Sdk's kunnen de meeste beheer bewerkingen uitvoeren op Data Base-, container-en doorvoer resources die de resource provider van Cosmos DB overs Laan, waardoor beleids regels die zijn gemaakt met behulp van Azure Policy worden genegeerd. Om ervoor te zorgen dat beleids regels worden nageleefd, kunt u voor [komen dat de Azure Cosmos DB sdk's worden gewijzigd](role-based-access-control.md#prevent-sdk-changes)

## <a name="assign-a-built-in-policy-definition"></a>Een ingebouwde beleidsdefinitie toewijzen

Beleids definities beschrijven de nalevings voorwaarden voor bronnen en het effect dat moet worden genomen als aan een voor waarde wordt voldaan. Beleids _toewijzingen_ worden gemaakt op basis van beleids _definities_. U kunt ingebouwde of aangepaste beleids definities gebruiken voor uw Azure Cosmos DB-resources. Beleids toewijzingen bevinden zich in een bereik van een Azure-beheer groep, een Azure-abonnement of een resource groep en ze worden toegepast op de resources binnen de geselecteerde scope. U kunt eventueel specifieke resources uitsluiten van het bereik.

U kunt beleids toewijzingen maken met de sjabloon [Azure Portal](../governance/policy/assign-policy-portal.md), [Azure POWERSHELL](../governance/policy/assign-policy-powershell.md), [Azure cli](../governance/policy/assign-policy-azurecli.md)of [arm](../governance/policy/assign-policy-template.md).

Als u een beleids toewijzing wilt maken op basis van een ingebouwde beleids definitie voor Azure Cosmos DB, gebruikt u de stappen in [een beleids toewijzing maken met het Azure Portal](../governance/policy/assign-policy-portal.md) artikel.

Bij de stap om een beleids definitie te selecteren, voert u `Cosmos DB` in het zoek veld in om de lijst met beschik bare ingebouwde beleids definities te filteren. Selecteer een van de beschik bare ingebouwde beleids definities en kies vervolgens **selecteren** om door te gaan met het maken van de beleids toewijzing.

> [!TIP]
> U kunt ook de namen van ingebouwde beleids regels die worden weer gegeven in het deel venster **beschik bare definities** met Azure PowerShell, Azure CLI of arm-sjablonen gebruiken om beleids toewijzingen te maken.

:::image type="content" source="./media/policy/available-definitions.png" alt-text="Zoeken naar Azure Cosmos DB ingebouwde beleids definities":::

## <a name="create-a-custom-policy-definition"></a>Een aangepaste beleidsdefinitie maken

Voor specifieke scenario's die niet worden behandeld door ingebouwde beleids regels, kunt u [een aangepaste beleids definitie](../governance/policy/tutorials/create-custom-policy-definition.md)maken. Later maakt u een beleids _toewijzing_ van uw aangepaste beleids _definitie_.

### <a name="property-types-and-property-aliases-in-policy-rules"></a>Eigenschaps typen en aliassen van eigenschappen in beleids regels

Gebruik de [stappen voor aangepaste beleids definities](../governance/policy/tutorials/create-custom-policy-definition.md) om de bron eigenschappen en eigenschaps aliassen te identificeren die vereist zijn voor het maken van beleids regels.

Als u Azure Cosmos DB specifieke eigenschappen aliassen wilt identificeren, gebruikt u de naam ruimte `Microsoft.DocumentDB` met een van de methoden die worden weer gegeven in het artikel definitie stappen van het aangepaste beleid.

#### <a name="use-the-azure-cli"></a>Azure CLI gebruiken:
```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for namespace Microsoft.DocumentDB
az provider show --namespace Microsoft.DocumentDB --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

#### <a name="use-azure-powershell"></a>Azure PowerShell gebruiken:
```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.DocumentDB namespace
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.DocumentDB').Aliases
```

Met deze opdrachten wordt de lijst met eigenschaps alias namen voor de eigenschap Azure Cosmos DB uitgevoerd. Hier volgt een fragment van de uitvoer:

```json
[
  "Microsoft.DocumentDB/databaseAccounts/sku.name",
  "Microsoft.DocumentDB/databaseAccounts/virtualNetworkRules[*]",
  "Microsoft.DocumentDB/databaseAccounts/virtualNetworkRules[*].id",
  "Microsoft.DocumentDB/databaseAccounts/isVirtualNetworkFilterEnabled",
  "Microsoft.DocumentDB/databaseAccounts/consistencyPolicy.defaultConsistencyLevel",
  "Microsoft.DocumentDB/databaseAccounts/enableAutomaticFailover",
  "Microsoft.DocumentDB/databaseAccounts/Locations",
  "Microsoft.DocumentDB/databaseAccounts/Locations[*]",
  "Microsoft.DocumentDB/databaseAccounts/Locations[*].locationName",
  "..."
]
```

U kunt elk van deze eigenschaps alias namen gebruiken in de [regels voor aangepaste beleids definities](../governance/policy/tutorials/create-custom-policy-definition.md#policy-rule).

Hier volgt een voor beeld van een beleids definitie waarmee wordt gecontroleerd of een Azure Cosmos DB account is geconfigureerd voor meerdere schrijf locaties. De definitie van het aangepaste beleid bevat twee regels: een om te controleren op het specifieke type eigenschaps alias en de tweede voor de specifieke eigenschap van het type, in dit geval het veld waarin de instelling voor meerdere schrijf locaties wordt opgeslagen. Beide regels gebruiken de alias namen.

```json
"policyRule": {
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.DocumentDB/databaseAccounts"
      },
      {
        "field": "Microsoft.DocumentDB/databaseAccounts/enableMultipleWriteLocations",
        "notEquals": true
      }
    ]
  },
  "then": {
    "effect": "Audit"
  }
}
```

Aangepaste beleids definities kunnen worden gebruikt om beleids toewijzingen te maken, net zoals de ingebouwde beleids definities worden gebruikt.

## <a name="policy-compliance"></a>Beleidsnaleving

Nadat de beleids toewijzingen zijn gemaakt, worden de resources in het bereik van de toewijzing door Azure Policy geëvalueerd. De _naleving_ van elke bron met het beleid wordt geëvalueerd. Het _effect_ dat is opgegeven in het beleid wordt vervolgens toegepast op niet-compatibele resources.

U kunt de compatibiliteits resultaten en Details voor herstel in de [Azure Portal](../governance/policy/how-to/get-compliance-data.md#portal) of via de [Azure CLI](../governance/policy/how-to/get-compliance-data.md#command-line) of de [Azure monitor-logboeken](../governance/policy/how-to/get-compliance-data.md#azure-monitor-logs)bekijken.

In de volgende scherm afbeelding ziet u twee voor beelden van beleids toewijzingen.

Een toewijzing is gebaseerd op een ingebouwde beleids definitie, waarmee wordt gecontroleerd of de Azure Cosmos DB resources alleen worden geïmplementeerd voor de toegestane Azure-regio's. Bron compatibiliteit toont het resultaat van de beleids evaluatie (compatibel of niet-compatibel) voor bronnen in de scope.

De andere toewijzing is gebaseerd op een aangepaste beleids definitie. Met deze toewijzing wordt gecontroleerd of Cosmos DB accounts zijn geconfigureerd voor meerdere schrijf locaties.

Nadat de beleids toewijzingen zijn geïmplementeerd, worden in het nalevings dashboard evaluatie resultaten weer gegeven. Houd er rekening mee dat dit Maxi maal 30 minuten kan duren nadat een beleids toewijzing is geïmplementeerd. Daarnaast [kunnen scans van beleids evaluaties](../governance/policy/how-to/get-compliance-data.md#on-demand-evaluation-scan) direct na het maken van beleids toewijzingen op aanvraag worden gestart.

De scherm afbeelding toont de volgende resultaten van de compliantie-evaluatie voor in-Scope Azure Cosmos DB accounts:

- Nul van twee accounts voldoet aan een beleid dat Virtual Network (VNet)-filtering moet worden geconfigureerd.
- Nul van twee accounts voldoet aan een beleid waarvoor het account moet worden geconfigureerd voor meerdere schrijf locaties
- Nul van twee accounts voldoet aan een beleid dat is geïmplementeerd op de toegestane Azure-regio's.

:::image type="content" source="./media/policy/compliance.png" alt-text="Nalevings resultaten voor Azure Policy toewijzingen in de lijst":::

Zie [resources herstellen met Azure Policy](../governance/policy/how-to/remediate-resources.md)om de niet-compatibele resources te herstellen.

## <a name="next-steps"></a>Volgende stappen

- [Bekijk voor beelden van aangepaste beleids definities voor Azure Cosmos DB](https://github.com/Azure/azure-policy/tree/master/samples/CosmosDB), met inbegrip van de meerdere schrijf locatie en het beleid voor VNet-filtering hierboven weer gegeven.
- [Een beleids toewijzing maken in de Azure Portal](../governance/policy/assign-policy-portal.md)
- [Bekijk Azure Policy ingebouwde beleids definities voor Azure Cosmos DB](./policy-reference.md)