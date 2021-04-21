---
title: Voorbeeld van Azure CLI-script - een logische app maken
description: Voorbeeldscript voor het maken van een logische app via de Logic Apps-extensie in de Azure CLI.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc, devx-track-azurecli
ms.date: 07/30/2020
ms.openlocfilehash: b81d9b4a637965dd103d8fa89305424686a0c72c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789911"
---
# <a name="azure-cli-script-sample---create-a-logic-app"></a>Voorbeeld van Azure CLI-script - een logische app maken

Met dit script maakt u een voorbeeld van een logische app via de [Azure CLI Logic Apps extensie](/cli/azure/ext/logic/logic), ( `az logic` ). Voor een gedetailleerde handleiding voor het maken en beheren van logische apps via de Azure CLI, zie de Logic Apps [quickstart voor de Azure CLI.](quickstart-logic-apps-azure-cli.md)

> [!WARNING]
> De Azure CLI Logic Apps-extensie is momenteel *experimenteel* en *niet gedekt door klantenondersteuning*. Wees voorzichtig wanneer u deze CLI-extensie gebruikt, vooral als u ervoor kiest de extensie in productieomgevingen te gebruiken.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. Als u nog geen abonnement op Azure hebt, [maak dan een gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Azure CLI](/cli/azure/install-azure-cli), geïnstalleerd op uw lokale computer.
* De [Azure CLI Logic Apps-extensie](/cli/azure/azure-cli-extensions-list), geïnstalleerd op uw computer. Gebruik de volgende opdracht om deze extensie te installeren: `az extension add --name logic`
* Een [werkstroomdefinitie](quickstart-logic-apps-azure-cli.md#workflow-definition) voor uw logische app. Dit JSON-bestand moet het taalschema [van de werkstroomdefinitie volgen.](logic-apps-workflow-definition-language.md)
* Een API-verbinding met een e-mailaccount via een [Logic Apps connector](../connectors/apis-list.md) in dezelfde resourcegroep als uw logische app. In dit voorbeeld wordt de [Office 365 Outlook-connector](../connectors/connectors-create-api-office365-outlook.md) gebruikt, maar u kunt ook andere connectors gebruiken, [zoals Outlook.com](../connectors/connectors-create-api-outlook.md).

### <a name="prerequisite-check"></a>Controle van vereisten

Valideer uw omgeving voordat u begint:

* Meld u aan bij de Azure-portal en controleer of uw abonnement actief is door `az login` uit te voeren.

* Controleer uw Azure CLI-versie in een terminal- of opdrachtvenster door `az --version` uit te voeren. Bekijk de [meest recente releaseopmerkingen](/cli/azure/release-notes-azure-cli) voor de nieuwste versie.

  * Als u de nieuwste versie niet hebt, werkt u uw installatie bij door de [installatiehandleiding voor uw besturingssysteem of platform](/cli/azure/install-azure-cli) te volgen.

### <a name="sample-workflow-explanation"></a>Uitleg van voorbeeldwerkstroom

In dit voorbeeld van een werkstroomdefinitiebestand wordt dezelfde eenvoudige logische app gemaakt als [Logic Apps quickstart voor de Azure Portal](quickstart-create-first-logic-app-workflow.md). 

Deze voorbeeldwerkstroom: 

1. Hiermee geeft u een schema, `$schema` , op voor de logische app.

1. Hiermee definieert u een trigger voor de logische app in de lijst met triggers, `triggers` . De trigger wordt elke 3 uur `recurrence` recurseert ( ). De acties worden geactiveerd wanneer een nieuw feeditem wordt gepubliceerd ( `When_a_feed_item_is_published` ) voor de opgegeven RSS-feed ( `feedUrl` ).

1. Hiermee definieert u een actie voor de logische app in de lijst met acties, `actions` . Met de actie wordt een e-mail ( ) verzonden via Microsoft 365 met details van de RSS-feeditems zoals opgegeven in de sectie body () van de invoer van de `Send_an_email_(V2)` `body` actie ( `inputs` ).

## <a name="sample-workflow-definition"></a>Voorbeeldwerkstroomdefinitie

Voordat u het voorbeeldscript gaat uitvoeren, moet u eerst een voorbeeldwerkstroomdefinitie [maken.](#prerequisites)

1. Maak een JSON-bestand `testDefinition.json` op uw computer. 

1. Kopieer de volgende inhoud naar het JSON-bestand: 
    ```json
    
    {
        "definition": {
            "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
            "actions": {
                "Send_an_email_(V2)": {
                    "inputs": {
                        "body": {
                            "Body": "<p>@{triggerBody()?['publishDate']}<br>\n@{triggerBody()?['title']}<br>\n@{triggerBody()?['primaryLink']}</p>",
                            "Subject": "@triggerBody()?['title']",
                            "To": "test@example.com"
                        },
                        "host": {
                            "connection": {
                                "name": "@parameters('$connections')['office365']['connectionId']"
                            }
                        },
                        "method": "post",
                        "path": "/v2/Mail"
                    },
                    "runAfter": {},
                    "type": "ApiConnection"
                }
            },
            "contentVersion": "1.0.0.0",
            "outputs": {},
            "parameters": {
                "$connections": {
                    "defaultValue": {},
                    "type": "Object"
                }
            },
            "triggers": {
                "When_a_feed_item_is_published": {
                    "inputs": {
                        "host": {
                            "connection": {
                                "name": "@parameters('$connections')['rss']['connectionId']"
                            }
                        },
                        "method": "get",
                        "path": "/OnNewFeed",
                        "queries": {
                            "feedUrl": "https://www.pbs.org/now/rss.xml"
                        }
                    },
                    "recurrence": {
                        "frequency": "Hour",
                        "interval": 3
                    },
                    "splitOn": "@triggerBody()?['value']",
                    "type": "ApiConnection"
                }
            }
        },
        "parameters": {
            "$connections": {
                "value": {
                    "office365": {
                        "connectionId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup/providers/Microsoft.Web/connections/office365",
                        "connectionName": "office365",
                        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Web/locations/westus/managedApis/office365"
                    },
                    "rss": {
                        "connectionId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup/providers/Microsoft.Web/connections/rss",
                        "connectionName": "rss",
                        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Web/locations/westus/managedApis/rss"
                    }
                }
            }
        }
    }
    
    ```

1. Werk de waarden van de tijdelijke aanduidingen bij met uw eigen gegevens:

    1. Vervang het e-mailadres van de tijdelijke aanduiding ( `"To": "test@example.com"` ). U moet een e-mailadres gebruiken dat compatibel is met Logic Apps-connectors. Zie de vereisten voor [meer informatie.](#prerequisites)

    1. Vervang aanvullende connectorgegevens als u een andere e-mailconnector gebruikt dan de Office 365 Outlook-connector.

    1. Vervang de tijdelijke aanduiding abonnementswaarden ( ) voor uw `00000000-0000-0000-0000-000000000000` verbindings-id's ( en ) onder de `connectionId` `id` parameter connections ( ) door uw `$connections` eigen abonnementswaarden.

1. Sla uw wijzigingen op.

## <a name="sample-script"></a>Voorbeeldscript

> [!NOTE]
> Dit voorbeeld is geschreven voor de `bash` shell. Als u dit voorbeeld wilt uitvoeren in een andere shell, zoals Windows PowerShell of opdrachtprompt, moet u mogelijk wijzigingen aanbrengen in uw script.

Voordat u dit voorbeeldscript gaat uitvoeren, moet u deze opdracht uitvoeren om verbinding te maken met Azure:

```azurecli-interactive

az login

```

Navigeer vervolgens naar de map waarin u de werkstroomdefinitie hebt gemaakt. Als u bijvoorbeeld het JSON-bestand van de werkstroomdefinitie op uw bureaublad hebt gemaakt:

```azurecli

cd ~/Desktop

```

Voer vervolgens dit script uit om een logische app te maken. 

```azurecli-interactive

#!/bin/bash

# Create a resource group

az group create --name testResourceGroup --location westus

# Create your logic app

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

### <a name="clean-up-deployment"></a>Opschonen van implementatie

Nadat u klaar bent met het voorbeeldscript, kunt u de volgende opdracht uitvoeren om uw resourcegroep en alle geneste resources te verwijderen, inclusief de logische app.

```azurecli-interactive

az group delete --name testResourceGroup --yes

```

## <a name="script-explanation"></a>Uitleg van het script

In dit voorbeeldscript worden de volgende opdrachten gebruikt om een nieuwe resourcegroep en logische app te maken.

| Opdracht | Opmerkingen |
| ------- | ----- |
| [`az group create`](/cli/azure/group#az_group_create) | Hiermee maakt u een resourcegroep waarin de resources van uw logische app worden opgeslagen. |
| [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create) | Hiermee maakt u een logische app op basis van de werkstroom die is gedefinieerd in de parameter `--definition` . |
| [`az group delete`](/cli/azure/vm/extension) | Hiermee verwijdert u een resourcegroep en alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure/) voor meer informatie over de Azure CLI.

Aanvullende Logic Apps CLI-voorbeeldscripts vindt u in [Microsoft's voorbeeldcodebrowser](/samples/browse/?products=azure-logic-apps).
