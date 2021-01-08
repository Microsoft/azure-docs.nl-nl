---
title: Resources implementeren met Azure Portal
description: Gebruik Azure Portal en Azure resource Manage om uw resources te implementeren in een resource groep in uw abonnement.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: d8467bb4e51fc4e6ba89a84f1260a8d2743758d2
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028672"
---
# <a name="deploy-resources-with-arm-templates-and-azure-portal"></a>Resources implementeren met ARM-sjablonen en Azure Portal

Meer informatie over het gebruik van de [Azure Portal](https://portal.azure.com) met [Azure Resource Manager sjablonen (arm-sjablonen)](overview.md) voor het implementeren van uw Azure-resources. Zie [Azure-resources beheren met behulp van de Azure Portal](../management/manage-resources-portal.md)voor meer informatie over het beheren van uw resources.

Het implementeren van Azure-resources met behulp van de Azure Portal omvat meestal twee stappen:

- Een resourcegroep maken.
- Resources implementeren in de resource groep.

U kunt ook een aangepaste ARM-sjabloon maken om Azure-resources te implementeren.

In dit artikel worden beide methoden weer gegeven.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

1. Als u een nieuwe resource groep wilt maken, selecteert u **resource groepen** uit het [Azure Portal](https://portal.azure.com).

   ![Resource groepen selecteren](./media/deploy-portal/select-resource-groups.png)

1. Selecteer onder resource groepen de optie **toevoegen**.

   ![Brongroep toevoegen](./media/deploy-portal/add-resource-group.png)

1. Selecteer of voer de volgende eigenschaps waarden in:

    - **Abonnement**: Selecteer een Azure-abonnement.
    - **Resource groep**: Geef de resource groep een naam.
    - **Regio**: een Azure-locatie opgeven. Deze locatie is waar de resource groep meta gegevens over de resources opslaat. Voor nalevings redenen wilt u mogelijk opgeven waar de meta gegevens worden opgeslagen. Over het algemeen is het raadzaam om een locatie op te geven waar de meeste resources zullen worden. Met dezelfde locatie kan uw sjabloon worden vereenvoudigd.

   ![Groeps waarden instellen](./media/deploy-portal/set-group-properties.png)

1. Selecteer **Controleren + maken**.
1. Controleer de waarden en selecteer vervolgens **maken**.
1. Selecteer **vernieuwen** voordat u de nieuwe resource groep in de lijst kunt zien.

## <a name="deploy-resources-to-a-resource-group"></a>Resources implementeren in een resource groep

Nadat u een resource groep hebt gemaakt, kunt u resources in de groep implementeren vanuit de Marketplace. Marketplace biedt vooraf gedefinieerde oplossingen voor veelvoorkomende scenario's.

1. Als u een implementatie wilt starten, selecteert u **een resource maken** op basis van de [Azure Portal](https://portal.azure.com).

   ![Nieuwe resource](./media/deploy-portal/new-resources.png)

1. Zoek het type resource dat u wilt implementeren. De resources zijn ingedeeld in categorieën. Als u de specifieke oplossing die u wilt implementeren niet ziet, kunt u de Marketplace hiervoor doorzoeken. Op de volgende scherm afbeelding ziet u dat de Ubuntu-Server is geselecteerd.

   ![Resource type selecteren](./media/deploy-portal/select-resource-type.png)

1. Afhankelijk van het type van de geselecteerde resource, hebt u een verzameling relevante eigenschappen die moeten worden ingesteld vóór de implementatie. Voor alle typen moet u een doel resource groep selecteren. In de volgende afbeelding ziet u hoe u een virtuele Linux-machine maakt en deze implementeert in de resource groep die u hebt gemaakt.

   ![Een resourcegroep maken](./media/deploy-portal/select-existing-group.png)

   U kunt ervoor kiezen om een resource groep te maken bij het implementeren van uw resources. Selecteer **nieuwe maken** en geef de resource groep een naam.

1. Uw implementatie begint. De implementatie kan enkele minuten duren. Sommige resources nemen meer tijd in beslag dan andere resources. Wanneer de implementatie is voltooid, ziet u een melding. Selecteer **Ga naar resource** om te openen

   ![Melding weer geven](./media/deploy-portal/view-notification.png)

1. Nadat u uw resources hebt geïmplementeerd, kunt u meer resources toevoegen aan de resource groep door **toevoegen** te selecteren.

   ![Resource toevoegen](./media/deploy-portal/add-resource.png)

Hoewel u dit niet hebt gezien, heeft de portal een ARM-sjabloon gebruikt om de resources te implementeren die u hebt geselecteerd. U kunt de sjabloon vinden in de implementatie geschiedenis. Zie [sjabloon exporteren na implementatie](export-template-portal.md#export-template-after-deployment)voor meer informatie.

## <a name="deploy-resources-from-custom-template"></a>Resources implementeren vanuit een aangepaste sjabloon

Als u een implementatie wilt uitvoeren maar geen van de sjablonen in de Marketplace wilt gebruiken, kunt u een aangepaste sjabloon maken die de infra structuur voor uw oplossing definieert. Zie [inzicht krijgen in de structuur en syntaxis van arm-sjablonen](template-syntax.md)voor meer informatie over het maken van sjablonen.

> [!NOTE]
> De portal interface biedt geen ondersteuning voor het verwijzen naar een [geheim vanuit een Key Vault](key-vault-parameter.md). Gebruik in plaats daarvan [Power shell](deploy-powershell.md) of [Azure cli](deploy-cli.md) om uw sjabloon lokaal of vanuit een externe URI te implementeren.

1. Als u een aangepaste sjabloon via de portal wilt implementeren, selecteert u **een resource maken** en zoekt u naar **sjabloon**. en selecteer vervolgens **Sjabloonimlementatie**.

   ![Sjabloon implementatie zoeken](./media/deploy-portal/search-template.png)

1. Selecteer **Maken**.
1. U ziet een aantal opties voor het maken van een sjabloon:

    - **Uw eigen sjabloon bouwen in editor**: Maak uw eigen sjabloon in de sjabloon editor van de portal.
    - **Algemene sjablonen**: Selecteer een van de algemene oplossingen.
    - **Een github Quick Start-sjabloon laden**: Selecteer uit Quick Start- [sjablonen](https://azure.microsoft.com/resources/templates/).

   ![Opties weer geven](./media/deploy-portal/see-options.png)

    Deze zelf studie bevat de instructies voor het laden van een Quick Start-sjabloon.

1. Onder **een github Quick Start-sjabloon laden** typt of selecteert u **101-Storage-account-create**.

    U hebt hiervoor twee opties:

    - **Sjabloon selecteren**: de sjabloon implementeren.
    - **Sjabloon bewerken**: Bewerk de Quick Start-sjabloon voordat u deze implementeert.

1. Selecteer **sjabloon bewerken** om de sjabloon editor van de portal te verkennen. De sjabloon wordt geladen in de editor. U ziet dat er twee para meters zijn: `storageAccountType` en `location` .

   ![Sjabloon maken](./media/deploy-portal/show-json.png)

1. Breng een kleine wijziging aan in de sjabloon. Werk bijvoorbeeld de `storageAccountName` variabele bij naar:

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. Selecteer **Opslaan**. Nu ziet u de implementatie-interface van de Portal sjabloon. Let op de twee para meters die u in de sjabloon hebt gedefinieerd.
1. Voer de eigenschaps waarden in of Selecteer deze:

    - **Abonnement**: Selecteer een Azure-abonnement.
    - **Resource groep**: Selecteer **nieuwe maken** en geef een naam op.
    - **Locatie**: Selecteer een Azure-locatie.
    - **Type opslag account**: gebruik de standaard waarde.
    - **Locatie**: gebruik de standaard waarde.
    - **Ik ga akkoord met de bovenstaande voor waarden**: (selecteren)

1. Selecteer **Aankoop**.

## <a name="next-steps"></a>Volgende stappen

- Zie [bewerkingen controleren met Resource Manager](../management/view-activity-logs.md)voor meer informatie over het weer geven van audit Logboeken.
- Zie [implementatie bewerkingen weer geven](deployment-history.md)om implementatie fouten op te lossen.
- Zie [arm-sjablonen exporteren](export-template-portal.md)voor meer informatie over het exporteren van een sjabloon uit een implementatie of resource groep.
- Zie [Azure Deployment Manager](deployment-manager-overview.md)voor een veilige implementatie van uw service in meerdere regio's.
