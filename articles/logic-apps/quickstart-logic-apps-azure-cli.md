---
title: Logische apps maken en beheren met Azure CLI
description: Gebruik Azure CLI om een logische app te maken, en beheer uw logische app vervolgens met bewerkingen zoals vermelden, weergeven (ophalen), bijwerken en verwijderen.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli, contperf-fy21q2
ms.date: 11/23/2020
ms.openlocfilehash: afc39673a30f5c99455696c7a075cb1a6a33ecd1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875499"
---
# <a name="quickstart-create-and-manage-logic-apps-using-the-azure-cli"></a>Quickstart: Logische apps maken en beheren met behulp van Azure CLI

Deze quickstart laat zien hoe u logische apps maakt en beheert door de [Azure CLI Logic Apps-extensie](/cli/azure/logic) (`az logic`) te gebruiken. Vanaf de opdrachtregel kunt u een logische app maken door het JSON-bestand te gebruiken voor een werkstroomdefinitie van een logische app. Vervolgens kunt u uw logische app beheren door bewerkingen zoals `list`, `show` (`get`), `update` en `delete` uit te voeren vanaf de opdrachtregel.

> [!WARNING]
> De Azure CLI Logic Apps-extensie is momenteel *experimenteel* en *niet gedekt door klantenondersteuning*. Wees voorzichtig wanneer u deze CLI-extensie gebruikt, vooral als u ervoor kiest de extensie in productieomgevingen te gebruiken.

Als u geen ervaring hebt met Logic Apps, kunt u ook leren hoe u uw eerste logische apps maakt [via Azure Portal](quickstart-create-first-logic-app-workflow.md), [in Visual Studio](quickstart-create-logic-apps-with-visual-studio.md) en [in Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md).

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. Als u nog geen abonnement op Azure hebt, [maak dan een gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Azure CLI](/cli/azure/install-azure-cli), geïnstalleerd op uw lokale computer.
* De [Azure CLI Logic Apps-extensie](/cli/azure/azure-cli-extensions-list), geïnstalleerd op uw computer. Gebruik de volgende opdracht om deze extensie te installeren: `az extension add --name logic`
* Een [Azure-resourcegroep](#example---create-resource-group) om uw logische app in te maken.

### <a name="prerequisite-check"></a>Controle van vereisten

Valideer uw omgeving voordat u begint:

* Meld u aan bij de Azure-portal en controleer of uw abonnement actief is door `az login` uit te voeren.
* Controleer uw Azure CLI-versie in een terminal- of opdrachtvenster door `az --version` uit te voeren. Bekijk de [meest recente releaseopmerkingen](/cli/azure/release-notes-azure-cli?tabs=azure-cli) voor de nieuwste versie.
  * Als u de nieuwste versie niet hebt, werkt u uw installatie bij door de [installatiehandleiding voor uw besturingssysteem of platform](/cli/azure/install-azure-cli) te volgen.

### <a name="example---create-resource-group"></a>Voorbeeld - Resourcegroep maken

Als u nog geen resourcegroep voor uw logische app hebt, maakt u de groep met de opdracht `az group create`. Met de volgende opdracht wordt bijvoorbeeld een resourcegroep met de naam `testResourceGroup` gemaakt op de locatie `westus`.

```azurecli-interactive

az group create --name testResourceGroup --location westus

```

De uitvoer toont de `provisioningState` als `Succeeded` wanneer uw resourcegroep is gemaakt:

```output

<...>
  "name": "testResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
<...>

```

## <a name="workflow-definition"></a>Werkstroomdefinitie

Voordat u [een nieuwe logische app maakt](#create-logic-apps-from-cli) of [een bestaande logische app bijwerkt](#update-logic-apps-from-cli) door Azure CLI te gebruiken, hebt u een werkstroomdefinitie voor uw logische app nodig. In de Azure-portal kunt u de onderliggende werkstroomdefinitie van uw logische app in JSON-indeling bekijken door over te schakelen van de **ontwerpfunctieweergave** naar de **codeweergave**.

Wanneer u de opdrachten uitvoert om uw logische app te maken of bij te werken, wordt uw werkstroomdefinitie geüpload als een vereiste parameter (`--definition`). U moet uw werkstroomdefinitie maken als een JSON-bestand dat het [taalschema voor werkstroomdefinities](./logic-apps-workflow-definition-language.md) volgt.

## <a name="create-logic-apps-from-cli"></a>Logische apps maken met CLI

U kunt een werkstroom voor logische apps maken via Azure CLI met behulp van de opdracht [`az logic workflow create`](/cli/azure/logic/workflow#az_logic_workflow_create) met een JSON-bestand voor de definitie.

```azurecli

az logic workflow create --definition
                         --location
                         --name
                         --resource-group
                         [--access-control]
                         [--endpoints-configuration]
                         [--integration-account]
                         [--integration-service-environment]
                         [--state {Completed, Deleted, Disabled, Enabled, NotSpecified, Suspended}]
                         [--tags]

```

Uw opdracht moet de volgende [vereiste parameters](/cli/azure/logic/workflow#az_logic_workflow_create-required-parameters) bevatten:

| Parameter | Waarde | Beschrijving |
| --------- | ----- | ----------- |
| Werkstroomdefinitie | `--definition` | Een JSON-bestand met de [werkstroomdefinitie](#workflow-definition) van uw logische app. |
| Locatie | `--location -l` | De Azure-regio waarin uw logische app zich bevindt. |
| Naam | `--name -n` | De naam van uw logische app. De naam mag alleen letters, cijfers, afbreekstreepjes (`-`), onderstrepingstekens (`_`), haakjes (`()`) en punten (`.`) bevatten. De naam moet ook uniek zijn binnen alle regio’s. |
| Naam van de resourcegroep | `--resource-group -g` | De [Azure-resourcegroep](../azure-resource-manager/management/overview.md) waarin u uw logische app wilt maken. [Maak een resourcegroep](#example---create-resource-group) voor uw logische app voordat u begint, als u die nog niet hebt. |

U kunt ook aanvullende [optionele parameters](/cli/azure/logic/workflow#az_logic_workflow_create-optional-parameters) opnemen om het toegangsbeheer, de eindpunten, het integratieaccount, de integratieserviceomgeving, de status en de resourcetags van uw logische app te configureren.

### <a name="example---create-logic-app"></a>Voorbeeld - Logische app maken

In dit voorbeeld wordt er een werkstroom met de naam `testLogicApp` gemaakt in de resourcegroep `testResourceGroup` op de locatie `westus`. Het JSON-bestand `testDefinition.json` bevat de werkstroomdefinitie.

```azurecli-interactive

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

Wanneer uw werkstroom is gemaakt, toont CLI de JSON-code van uw nieuwe werkstroomdefinitie. Als het maken van uw werkstroom is mislukt, bekijkt u de [lijst met mogelijke fouten](#errors).

## <a name="update-logic-apps-from-cli"></a>Logische apps bijwerken met CLI

U kunt de werkstroom van een logische app ook bijwerken via Azure CLI met behulp van de opdracht [`az logic workflow create`](/cli/azure/logic/workflow#az_logic_workflow_create).

Uw opdracht moet dezelfde [vereiste parameters](/cli/azure/logic/workflow#az_logic_workflow_create-required-parameters) bevatten als wanneer u [een logische app maakt](#create-logic-apps-from-cli). U kunt ook dezelfde [optionele parameters](/cli/azure/logic/workflow#az_logic_workflow_create-optional-parameters) toevoegen als wanneer u een logische app maakt.

```azurecli

az logic workflow create --definition
                         --location
                         --name
                         --resource-group
                         [--access-control]
                         [--endpoints-configuration]
                         [--integration-account]
                         [--integration-service-environment]
                         [--state {Completed, Deleted, Disabled, Enabled, NotSpecified, Suspended}]
                         [--tags]

```

### <a name="example---update-logic-app"></a>Voorbeeld - Logische app bijwerken

In dit voorbeeld wordt de [voorbeeldwerkstroom die in de vorige sectie is gemaakt](#example---create-logic-app), bijgewerkt om een ander JSON-definitiebestand (`newTestDefinition.json`) te gebruiken en twee resourcetags (`testTag1` en `testTag2`) toe te voegen met beschrijvingswaarden.

```azurecli-interactive

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "newTestDefinition.json" --tags "testTag1=testTagValue1" "testTag2=testTagValue"

```

Wanneer uw werkstroom is bijgewerkt, toont CLI de bijgewerkte werkstroomdefinitie van uw logische app. Als het bijwerken van uw werkstroom is mislukt, bekijkt u de [lijst met mogelijke fouten](#errors).

## <a name="delete-logic-apps-from-cli"></a>Logische apps verwijderen met CLI

U kunt de werkstroom van een logische app verwijderen via Azure CLI met behulp van de opdracht [`az logic workflow delete`](/cli/azure/logic/workflow#az_logic_workflow_delete).

Uw opdracht moet de volgende [vereiste parameters](/cli/azure/logic/workflow#az_logic_workflow_delete-required-parameters) bevatten:

| Parameter | Waarde | Beschrijving |
| --------- | ----- | ----------- |
| Naam | `--name -n` | De naam van uw logische app. |
| Naam van de resourcegroep | `-resource-group -g` | De resourcegroep waarin uw logische app zich bevindt. |

U kunt ook een [optionele parameter](/cli/azure/logic/workflow#az_logic_workflow_delete-optional-parameters) opnemen om bevestigingsprompts over te slaan, namelijk `--yes -y`.

```azurecli

az logic workflow delete --name
                         --resource-group
                         [--yes]

```

CLI vraagt u vervolgens de verwijdering van uw logische app te bevestigen. U kunt de bevestigingsprompt overslaan door de optionele parameter `--yes -y` te gebruiken met uw opdracht.

```azurecli

Are you sure you want to perform this operation? (y/n):

```

U kunt de verwijdering van een logische app bevestigen door [uw logische apps in CLI te vermelden](#list-logic-apps-in-cli) of door uw logische apps in de Azure-portal te bekijken.

### <a name="example---delete-logic-app"></a>Voorbeeld - Logische app verwijderen

In dit voorbeeld wordt de [voorbeeldwerkstroom die in een vorige sectie is gemaakt](#example---create-logic-app), verwijderd.

```azurecli-interactive

az logic workflow delete --resource-group "testResourceGroup" --name "testLogicApp"

```

Nadat u op de bevestigingsprompt hebt gereageerd met `y`, wordt de logische app verwijderd.

## <a name="show-logic-apps-in-cli"></a>Logische apps weergeven in CLI

U kunt een specifieke werkstroom voor een logische app ophalen met behulp van de opdracht [`az logic workflow show`](/cli/azure/logic/workflow#az_logic_workflow_show).

```azurecli

az logic workflow show --name
                       --resource-group

```

Uw opdracht moet de volgende [vereiste parameters](/cli/azure/logic/workflow#az_logic_workflow_show-required-parameters) bevatten

| Parameter | Waarde | Beschrijving |
| --------- | ----- | ----------- |
| Naam | `--name -n` | De naam van uw logische app. |
| Naam van de resourcegroep | `--resource-group -g` | De naam van de resourcegroep waarin uw logische app zich bevindt. |

### <a name="example---get-logic-app"></a>Voorbeeld - Logische app ophalen

In dit voorbeeld wordt de logische app `testLogicApp` in de resourcegroep `testResourceGroup` geretourneerd met volledige logboeken voor foutopsporing.

```azurecli-interactive

az logic workflow show --resource-group "testResourceGroup" --name "testLogicApp" --debug

```

## <a name="list-logic-apps-in-cli"></a>Logische apps vermelden in CLI

U kunt uw logische apps per abonnement vermelden met behulp van de opdracht [`az logic workflow list`](/cli/azure/logic/workflow#az_logic_workflow_list). Deze opdracht retourneert de JSON-code voor de werkstromen van uw logische apps.

U kunt uw resultaten filteren op de volgende [optionele parameters](/cli/azure/logic/workflow#az_logic_workflow_list-optional-parameters):

| Parameter | Waarde | Beschrijving |
| --------- | ----- | ----------- |
| Naam van de resourcegroep | `--resource-group -g` | De naam van de resourcegroep waarop u uw resultaten wilt filteren. |
| Aantal items | `--top` | Het aantal items dat in uw resultaten is opgenomen. |
| Filteren | `--filter` | Het type filter dat u op uw lijst gebruikt. U kunt filteren op status (`State`), trigger (`Trigger`) en de id van de resource waarnaar wordt verwezen (`ReferencedResourceId`). |

```azurecli

az logic workflow list [--filter]
                       [--resource-group]
                       [--top]

```

### <a name="example---list-logic-apps"></a>Voorbeeld - Logische apps vermelden

In dit voorbeeld worden alle ingeschakelde werkstromen in de resourcegroep `testResourceGroup` geretourneerd in een ASCII-tabelindeling.

```azurecli-interactive

az logic workflow list --resource-group "testResourceGroup" --filter "(State eq 'Enabled')" --output "table"

```

## <a name="errors"></a>Fouten

De volgende fout geeft aan dat de Azure CLI Logic Apps-extension niet is geïnstalleerd. Volg de stappen in de vereisten om [de Logic Apps-extensie](#prerequisites) op uw computer te installeren.

```output

az: 'logic' is not in the 'az' command group. See 'az --help'. If the command is from an extension, please make sure the corresponding extension is installed. To learn more about extensions, please visit https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview

```

De volgende fout geeft mogelijk aan dat het bestandspad voor het uploaden van uw werkstroomdefinitie onjuist is.

```output

Expecting value: line 1 column 1 (char 0)

```

## <a name="global-parameters"></a>Globale parameters

U kunt de volgende optionele globale Azure CLI-parameters gebruiken met uw `az logic`-opdrachten:

| Parameter | Waarde | Beschrijving |
| --------- | ----- | ----------- |
| Uitvoerindeling | `--output -o` | Wijzig de [uitvoerindeling](/cli/azure/format-output-azure-cli) in iets anders dan de standaard JSON-indeling. |
| Alleen fouten weergeven | `--only-show-errors` | Hiermee worden waarschuwingen onderdrukt en alleen fouten weergegeven. |
| Uitgebreid | `--verbose` | Hiermee worden uitgebreide logboeken weergegeven. |
| Fouten opsporen | `--debug` | Hiermee worden alle logboeken voor foutopsporing weergegeven. |
| Help-bericht | `--help -h` | Hiermee wordt het Help-dialoogvenster weergegeven. |
| Query’s uitvoeren | `--query` | Hiermee wordt een JMESPath-queryreeks ingesteld voor JSON-uitvoer. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure/) voor meer informatie over de Azure CLI.

Aanvullende Logic Apps CLI-voorbeeldscripts vindt u in [Microsoft's voorbeeldcodebrowser](/samples/browse/?products=azure-logic-apps).

Vervolgens kunt u een logische voorbeeld-app maken met de Azure CLI met behulp van een voorbeeldscript en werkstroomdefinitie.

> [!div class="nextstepaction"]
> [Een logische app maken met behulp van een voorbeeldscript](sample-logic-apps-cli-script.md).
