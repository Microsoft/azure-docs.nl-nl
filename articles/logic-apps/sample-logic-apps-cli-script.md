---
title: Voor beeld van Azure CLI-script-een logische app maken
description: Voorbeeld script voor het maken van een logische app via de extensie Logic Apps in de Azure CLI.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc, devx-track-azurecli
ms.date: 07/30/2020
ms.openlocfilehash: a4553ceee482fb232e9ab56deca650be93f9dc6b
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218040"
---
# <a name="azure-cli-script-sample---create-a-logic-app"></a>Voor beeld van Azure CLI-script-een logische app maken

Met dit script maakt u een voor beeld van een logische app via de [Azure CLI Logic apps extension](/cli/azure/ext/logic/logic), ( `az logic` ). Voor een gedetailleerde hand leiding voor het maken en beheren van logische apps via de Azure CLI raadpleegt u de [Logic apps Snelstartgids voor de Azure cli](quickstart-logic-apps-azure-cli.md).

> [!WARNING]
> De Azure CLI Logic Apps-extensie is momenteel *experimenteel* en *niet gedekt door klantenondersteuning*. Wees voorzichtig wanneer u deze CLI-extensie gebruikt, vooral als u ervoor kiest de extensie in productieomgevingen te gebruiken.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. Als u nog geen abonnement op Azure hebt, [maak dan een gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Azure CLI](/cli/azure/install-azure-cli), geïnstalleerd op uw lokale computer.
* De [Azure CLI Logic Apps-extensie](/cli/azure/azure-cli-extensions-list), geïnstalleerd op uw computer. Gebruik de volgende opdracht om deze extensie te installeren: `az extension add --name logic`
* Een [werk stroom definitie](quickstart-logic-apps-azure-cli.md#workflow-definition) voor uw logische app. Dit JSON-bestand moet voldoen aan het [taal schema voor de werk stroom definitie](logic-apps-workflow-definition-language.md).
* Een API-verbinding met een e-mail account via een ondersteunde [Logic Apps-Connector](../connectors/apis-list.md) in dezelfde resource groep als uw logische app. In dit voor beeld wordt de [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md) -connector gebruikt, maar u kunt ook andere connectors gebruiken, zoals [Outlook.com](../connectors/connectors-create-api-outlook.md).

### <a name="prerequisite-check"></a>Controle van vereisten

Valideer uw omgeving voordat u begint:

* Meld u aan bij de Azure-portal en controleer of uw abonnement actief is door `az login` uit te voeren.

* Controleer uw Azure CLI-versie in een terminal- of opdrachtvenster door `az --version` uit te voeren. Bekijk de [meest recente releaseopmerkingen](/cli/azure/release-notes-azure-cli) voor de nieuwste versie.

  * Als u de nieuwste versie niet hebt, werkt u uw installatie bij door de [installatiehandleiding voor uw besturingssysteem of platform](/cli/azure/install-azure-cli) te volgen.

### <a name="sample-workflow-explanation"></a>Uitleg van de voorbeeld werk stroom

In dit voor beeld van een werk stroom definitie bestand wordt dezelfde Basic Logic app gemaakt als de [Logic apps Snelstartgids voor de Azure Portal](quickstart-create-first-logic-app-workflow.md). 

Deze voorbeeld werk stroom: 

1. Hiermee geeft u een schema op, `$schema` voor de logische app.

1. Hiermee definieert u een trigger voor de logische app in de lijst met triggers, `triggers` . De trigger wordt `recurrence` elke drie uur herhaald (). De acties worden geactiveerd wanneer een nieuw feed-item wordt gepubliceerd ( `When_a_feed_item_is_published` ) voor de opgegeven RSS-feed ( `feedUrl` ).

1. Hiermee definieert u een actie voor de logische app in de lijst met acties, `actions` . De actie verzendt een e-mail ( `Send_an_email_(V2)` ) via Microsoft 365 met details van de RSS-feed-items zoals opgegeven in de sectie Body ( `body` ) van de invoer van de actie ( `inputs` ).

## <a name="sample-workflow-definition"></a>Definitie van de voorbeeld werk stroom

Voordat u het voorbeeld script uitvoert, moet u eerst een definitie van een voorbeeld [werk stroom](#prerequisites)maken.

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

    1. Vervang de tijdelijke aanduiding voor het e-mail adres ( `"To": "test@example.com"` ). U moet een e-mail adres gebruiken dat compatibel is met Logic Apps connectors. Zie de [vereisten](#prerequisites)voor meer informatie.

    1. Aanvullende details van de connector vervangen als u een andere e-mail Connector gebruikt dan de Office 365 Outlook-Connector.

    1. Vervang de waarden van de tijdelijke aanduiding ( `00000000-0000-0000-0000-000000000000` ) voor uw verbindings-id's ( `connectionId` en `id` ) onder de para meter Connections ( `$connections` ) door uw eigen abonnements waarden.

1. Sla uw wijzigingen op.

## <a name="sample-script"></a>Voorbeeldscript

> [!NOTE]
> Dit voor beeld is geschreven voor de `bash` shell. Als u dit voor beeld wilt uitvoeren in een andere shell, zoals Windows Power shell of opdracht prompt, moet u mogelijk wijzigingen aanbrengen in uw script.

Voer de volgende opdracht uit om verbinding te maken met Azure voordat u dit voorbeeld script uitvoert:

```azurecli-interactive

az login

```

Ga vervolgens naar de map waarin u de werk stroom definitie hebt gemaakt. Als u bijvoorbeeld het JSON-bestand van de werk stroom definitie hebt gemaakt op uw bureau blad:

```azurecli

cd ~/Desktop

```

Voer dit script uit om een logische app te maken. 

```azurecli-interactive

#!/bin/bash

# Create a resource group

az group create --name testResourceGroup --location westus

# Create your logic app

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

### <a name="clean-up-deployment"></a>Opschonen van implementatie

Wanneer u klaar bent met het gebruik van het voorbeeld script, voert u de volgende opdracht uit om de resource groep en alle geneste resources te verwijderen, met inbegrip van de logische app.

```azurecli-interactive

az group delete --name testResourceGroup --yes

```

## <a name="script-explanation"></a>Uitleg van het script

In dit voorbeeld script worden de volgende opdrachten gebruikt om een nieuwe resource groep en logische app te maken.

| Opdracht | Opmerkingen |
| ------- | ----- |
| [`az group create`](/cli/azure/group#az-group-create) | Hiermee maakt u een resource groep waarin de resources van de logische app zijn opgeslagen. |
| [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create) | Hiermee maakt u een logische app op basis van de werk stroom die is gedefinieerd in de para meter `--definition` . |
| [`az group delete`](/cli/azure/vm/extension) | Hiermee verwijdert u een resource groep en alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [documentatie van Azure CLI](/cli/azure/) voor meer informatie over de Azure CLI.

Aanvullende Logic Apps CLI-voorbeeldscripts vindt u in [Microsoft's voorbeeldcodebrowser](/samples/browse/?products=azure-logic-apps).
