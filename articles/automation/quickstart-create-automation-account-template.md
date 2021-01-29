---
title: 'Quickstart: Een Automation-account maken met een Azure-sjabloon'
titleSuffix: Azure Automation
description: In deze quickstart leest u hoe u een Automation-account kunt maken met de Azure Resource Manager-sjabloon.
services: automation
Customer intent: I want to create an Automation account by using an Azure Resource Manager template so that I can automate processes with runbooks.
ms.topic: quickstart
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: magoedte
ms.custom: mvc,subject-armqs
ms.openlocfilehash: d0582876a08b433a10106b97179ab603d4ec5762
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050665"
---
# <a name="quickstart-create-an-automation-account-by-using-arm-template"></a>Quickstart: Een Automation-account maken met een ARM-sjabloon

Azure Automation biedt een cloud-gebaseerde automatiserings- en configuratieservice die consistent beheer in uw Azure- en niet-Azure-omgevingen ondersteunt. In deze quickstart leest u hoe u een Azure Resource Manager-sjabloon (ARM-sjabloon) kunt implementeren waarmee een Automation-account wordt gemaakt. Als u een ARM-sjabloon gebruikt, hoeft u minder stappen uit te voeren in vergelijking met andere implementatiemethoden.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-automation%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

Met deze voorbeeldsjabloon worden de volgende bewerkingen uitgevoerd:

* Automatisch maken van een Azure Monitor Log Analytics-werkruimte.
* Automatisch maken van een Azure Automation-account.
* Automation-account koppelen aan de Log Analytics-werkruimte.
* Automation-runbooks toevoegen aan het account.

>[!NOTE]
>Als u een ARM-sjabloon gebruikt, wordt het maken van een Uitvoeren als-account voor Automation niet ondersteund. Zie [Run as-account maken](create-run-as-account.md)als u een uitvoeren als-account hand matig wilt maken vanuit de portal of met Power shell.

Nadat u deze stappen hebt voltooid, moet u [diagnostische instellingen configureren](automation-manage-send-joblogs-log-analytics.md) voor uw Automation-account om de taakstatus van de runbook en taakstromen te verzenden naar de gekoppelde Log Analytics-werkruimte.

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-automation/).

:::code language="json" source="~/quickstart-templates/101-automation/azuredeploy.json":::

### <a name="api-versions"></a>API-versies

De volgende tabel geeft een overzicht van de API-versie voor de resources die in dit voorbeeld worden gebruikt.

| Resource | Resourcetype | API-versie |
|:---|:---|:---|
| [Werkruimte](/azure/templates/microsoft.operationalinsights/workspaces) | workspaces | 2020-03-01-preview |
| [Automation-account](/azure/templates/microsoft.automation/automationaccounts) | automation | 2020-01-13-preview |
| [Services gekoppeld aan werkruimte](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | workspaces | 2020-03-01-preview |

### <a name="before-you-use-the-template"></a>Voordat u de sjabloon gebruikt

De sjabloon met JSON-parameters kunt u gebruiken om het volgende op te geven:

* De naam van de werkruimte.
* De regio waarin u de werkruimte wilt maken.
* De naam van het Automation-account.
* De regio waarin u het Automation-account wilt maken.

De volgende parameters in de sjabloon hebben een standaardwaarde voor de Log Analytics-werkruimte:

* *sku* wordt standaard ingesteld op de prijscategorie per GB die is geïntroduceerd in het prijsmodel van april 2018.
* *dataRetention* is standaard ingesteld op 30 dagen.

>[!WARNING]
>Als u een Log Analytics-werkruimte wilt maken of configureren in een abonnement dat is gekoppeld aan het prijsmodel van april 2018, is de enige mogelijke Log Analytics-prijscategorie *PerGB2018*.
>

De JSON-sjabloon bevat standaardwaarden voor de andere parameters die waarschijnlijk worden gebruikt in een standaardconfiguratie in uw omgeving. U kunt de sjabloon opslaan in een Azure-opslagaccount voor gedeelde toegang in uw organisatie. Zie [Resources implementeren met ARM-sjablonen en de Azure CLI](../azure-resource-manager/templates/deploy-cli.md) voor meer informatie over het werken met sjablonen.

Als u geen ervaring hebt met Azure Automation en Azure Monitor, is het belangrijk dat u de volgende configuratiedetails begrijpt. Deze kennis helpt fouten te voorkomen bij het maken, configureren en gebruiken van een Log Analytics werkruimte die aan uw nieuwe Automation-account is gekoppeld.

* Bekijk deze [aanvullende informatie](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace) om de configuratieopties voor de werkruimte volledig te begrijpen, zoals de modus voor toegangsbeheer, de prijscategorie, retentie en het capaciteitsreserveringsniveau.

* Bekijk [werkruimtetoewijzingen](how-to/region-mappings.md) om de ondersteunde regio's inline of in een parameterbestand op te geven. Bij het koppelen van een Log Analytics-werkruimte aan een Automation-Account in uw abonnement, worden slechts bepaalde regio's ondersteund.

* Als u geen ervaring hebt met Azure Monitor-logboeken en nog niet eerder een werkruimte hebt geïmplementeerd, raadpleegt u de [richtlijnen voor het ontwerpen van werkruimten](../azure-monitor/platform/design-logs-deployment.md). U weet dan meer over toegangsbeheer en hebt een beter beeld van de strategieën voor de implementatie van ontwerpen die worden aanbevolen voor uw organisatie.

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de volgende afbeelding om u aan te melden bij Azure en een sjabloon te openen. Met de sjabloon maakt u een Azure Automation account en een Log Analytics-werkruimte, en koppelt u het Automation-account aan de werkruimte.

    [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-automation%2Fazuredeploy.json)

2. Voer de waarden in.

3. De implementatie kan enkele minuten duren. Als de bewerking is voltooid, ziet u uitvoer zoals deze:

    ![Voorbeeld van uitvoer als de implementatie is voltooid](media/quickstart-create-automation-account-template/template-output.png)

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Open in de Azure-portal het Automation-account dat u net hebt gemaakt. 

3. Selecteer **Runbooks** in het linkerdeelvenster. Op de pagina **Runbooks** worden drie zelfstudie-runbooks vermeld die zijn gemaakt met het Automation-account.

    ![Zelfstudie-runbooks die zijn gemaakt met het Automation-account](./media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. Selecteer in het linkerdeel venster **Gekoppelde werkruimte**. Op de pagina **Gekoppelde werkruimte** wordt de Log Analytics werkruimte weergegeven die u eerder hebt opgegeven en die nu is gekoppeld aan uw Automation-account.

    ![Automation-account gekoppeld aan de Log Analytics-werkruimte](./media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de gemaakte resources niet meer nodig hebt, koppelt u het Automation-account los van de Log Analytics-werkruimte en verwijdert u vervolgens het Automation-account en de werkruimte.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Automation-account en een Log Analytics-werkruimte gemaakt en deze vervolgens aan elkaar gekoppeld.

Als u meer wilt weten over dit onderwerp, gaat u verder met de zelfstudies over Azure Automation.

> [!div class="nextstepaction"]
> [Zelfstudies voor Azure Automation](learn/automation-tutorial-runbook-graphical.md)