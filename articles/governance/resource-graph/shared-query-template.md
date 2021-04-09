---
title: 'Quickstart: Een gedeelde query maken met sjablonen'
description: In deze quickstart gebruikt u een Azure Resource Manager-sjabloon (ARM-sjabloon) om een gedeelde Resource Graph-query te maken die virtuele machines per OS telt.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 8d631ffcb14af93f10e578097470efc6156287d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99594313"
---
# <a name="quickstart-create-a-shared-query-by-using-an-arm-template"></a>Quickstart: Een gedeelde query maken met behulp van een ARM-sjabloon

Resource Graph-query's kunnen worden opgeslagen als een _persoonlijke query_ of als een _gedeelde query_. Een persoonlijke query wordt opgeslagen in het profiel van de portal van de persoon en is niet zichtbaar voor anderen. Een gedeelde query is een Resource Manager-object dat met anderen kan worden gedeeld via machtigingen en toegang op basis van rollen. Een gedeelde query biedt een algemene en consistente uitvoering van resourcedetectie. In deze quickstart wordt gebruikgemaakt van een Azure Resource Manager-sjabloon (ARM-sjabloon) om een gedeelde query te maken.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="De ARM-sjabloon implementeren om een gedeelde query te maken in Azure" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json":::

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

In deze quickstart maakt u een gedeelde query met de naam _Aantal VM's per besturingssysteem_. Als u deze query wilt proberen in SDK of in de portal met Resource Graph Explorer, raadpleegt u [Voorbeelden: aantal VM's per besturingssysteemtype](./samples/starter.md#count-os).

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/resourcegraph-sharedquery-countos/).

:::code language="json" source="~/quickstart-templates/resourcegraph-sharedquery-countos/azuredeploy.json":::

De resource die is gedefinieerd in de sjabloon:

- [Microsoft.ResourceGraph/queries](/azure/templates/microsoft.resourcegraph/queries)

## <a name="deploy-the-template"></a>De sjabloon implementeren

> [!NOTE]
> Azure Resource Graph-service is gratis. Zie [Overzicht van Azure Resource Graph](./overview.md) voor meer informatie.

1. Selecteer de volgende afbeelding om u aan te melden bij de Azure-portal en open de sjabloon:

   :::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="De ARM-sjabloon implementeren om een gedeelde query te maken in Azure" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json":::

1. Typ of selecteer de volgende waarden:

   | Naam | Waarde |
   |------|-------|
   | Abonnement | Selecteer uw Azure-abonnement. |
   | Resourcegroep | Selecteer **Nieuwe maken**, geef een naam op en selecteer vervolgens **OK**. |
   | Locatie | Selecteer een regio. Bijvoorbeeld **VS - centraal**. |
   | Querynaam | Wijzig de standaardwaarde niet: **Aantal VM's per besturingssysteem tellen**. |
   | Querycode | Wijzig de standaardwaarde niet: `Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)` |
   | Querybeschrijving | Wijzig de standaardwaarde niet: **Deze gedeelde query telt alle resources van de virtuele machine en geeft een samenvatting per type besturingssysteem.** |
   | Ik ga akkoord met de bovenstaande voorwaarden | (Selecteren) |

1. Selecteer **Aankoop**.

Een aantal aanvullende bronnen:

- Zie [Azure Snel Starten-sjabloon](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular) voor meer voorbeelden van sjablonen.
- Ga naar [Azure-sjabloonverwijzing](/azure/templates/microsoft.resourcegraph/allversions) om de sjabloonverwijzing te zien.
- Raadpleeg [Azure Resource Manager-documentatie](../../azure-resource-manager/management/overview.md) voor meer informatie over het ontwikkelen van ARM-sjablonen.
- Raadpleeg [Resourcegroepen en resources maken op abonnementsniveau](../../azure-resource-manager/templates/deploy-to-subscription.md) voor informatie over implementatie op abonnementsniveau.

## <a name="validate-the-deployment"></a>De implementatie valideren

Om de nieuwe gedeelde query uit te voeren, volgt u deze stappen:

1. Zoek in de zoekbalk van de portal naar **Resource Graph-query's** en selecteer deze.

1. Selecteer de gedeelde query met de naam **Aantal VM's per besturingssysteem** en selecteer vervolgens het tabblad **Resultaten** op de pagina **Overzicht**.

U kunt de gedeelde query openen vanuit Resource Graph Explorer:

1. Zoek in de zoekbalk van de portal naar **Resource Graph Explorer** en selecteer deze.

1. Selecteer de knop **Een query openen**.

1. Wijzig **Type** in _Gedeelde query's_. Als u **Aantal VM's per besturingssysteem** niet in de lijst ziet, gebruikt u het filtervak om de resultaten te beperken. Wanneer de gedeelde query **Aantal VM's per besturingssysteem** zichtbaar is, selecteert u de naam.

1. Wanneer de query is geladen, selecteert u de knop **Query uitvoeren**. De resultaten worden weergegeven op het tabblad **Resultaten**.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de gedeelde query wilt verwijderen, volgt u deze stappen:

1. Zoek in de zoekbalk van de portal naar **Resource Graph-query's** en selecteer deze.

1. Stel het selectievakje in naast de gedeelde query met de naam **Aantal VM's per besturingssysteem**.

1. Selecteer de knop **Verwijderen** aan de bovenkant van de pagina.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een gedeelde query voor Resource Graph gemaakt.

Ga voor meer informatie over gedeelde query's verder met de zelfstudie voor:

> [!div class="nextstepaction"]
> [Query's beheren in Azure Portal](./tutorials/create-share-query.md)