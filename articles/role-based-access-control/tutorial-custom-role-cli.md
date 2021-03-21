---
title: 'Zelfstudie: Een aangepaste Azure-rol maken met Azure CLI - Azure RBAC'
description: Lees hoe u een aangepaste Azure-rol maakt met behulp van Azure CLI en op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/20/2019
ms.author: rolyon
ms.custom: devx-track-azurecli
ms.openlocfilehash: 319bca74c8e781e5dc5022e9fb901b2edca24a80
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87485640"
---
# <a name="tutorial-create-an-azure-custom-role-using-azure-cli"></a>Zelfstudie: Een aangepaste Azure-rol maken met Azure CLI

Als de [ingebouwde rollen van Azure](built-in-roles.md) niet voldoen aan de specifieke behoeften van uw organisatie, kunt u uw eigen aangepaste rollen maken. Voor deze zelfstudie gaat u met behulp van Azure CLI een aangepaste rol maken met de naam Reader Support Tickets. De aangepaste rol stelt de gebruiker in staat om alles in het beheersvenster van een abonnement te zien en ook om ondersteuningstickets te openen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een aangepaste rol maken
> * Aangepaste rollen opvragen
> * Een aangepaste rol bijwerken
> * Een aangepaste rol verwijderen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

- Machtigingen voor het maken van aangepaste rollen, zoals [Eigenaar](built-in-roles.md#owner) of [Administrator voor gebruikerstoegang](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) of [Azure CLI](/cli/azure/install-azure-cli)

## <a name="sign-in-to-azure-cli"></a>Aanmelden bij Azure CLI

Meld u aan bij [Azure CLI](/cli/azure/authenticate-azure-cli).

## <a name="create-a-custom-role"></a>Een aangepaste rol maken

De eenvoudigste manier om een aangepaste rol te maken, is door een JSON-sjabloon als uitgangspunt te nemen, deze te bewerken en zo een nieuwe rol te maken.

1. Bekijk de lijst met bewerkingen voor de [resourceprovider Microsoft.Support](resource-provider-operations.md#microsoftsupport). Het is handig om te weten welke bewerkingen er beschikbaar zijn voor het maken van machtigingen.

    | Bewerking | Beschrijving |
    | --- | --- |
    | Microsoft.Support/register/action | Hiermee wordt het item geregistreerd bij de resourceprovider voor ondersteuning |
    | Microsoft.Support/supportTickets/read | Hiermee worden gegevens over het ondersteuningsticket opgehaald (inclusief status, ernst, contactgegevens en communicatie) of wordt de lijst met ondersteuningstickets voor de verschillende abonnementen opgehaald. |
    | Microsoft.Support/supportTickets/write | Hiermee wordt een ondersteuningsticket gemaakt of bijgewerkt. U kunt een ondersteuningsticket maken voor problemen van technische aard of op het gebied van facturering, quota's of beheer van abonnementen. U kunt voor bestaande ondersteuningstickets de ernst, de contactgegevens en de communicatiemethoden aanpassen. |
    
1. Maak een nieuw bestand met de naam **ReaderSupportRole.json**.

1. Open ReaderSupportRole.json in een editor en voeg de volgende JSON-code toe.

    Zie [Aangepaste Azure-rollen](custom-roles.md) voor informatie over de verschillende eigenschappen.

    ```json
    {
      "Name": "",
      "IsCustom": true,
      "Description": "",
      "Actions": [],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/{subscriptionId1}"
      ]
    }
    ```
    
1. Voeg de volgende bewerkingen toe aan de eigenschap `Actions`. Deze acties stellen de gebruiker in staat om alles in het abonnement te zien en ook om ondersteuningstickets te maken.

    ```
    "*/read",
    "Microsoft.Support/*"
    ```

1. De id van uw abonnement kunt u opvragen met de opdracht [az account list](/cli/azure/account#az-account-list).

    ```azurecli
    az account list --output table
    ```

1. Vervang in `AssignableScopes``{subscriptionId1}` door de id van uw abonnement.

    U moet expliciete abonnement-id's toevoegen, anders is het niet mogelijk om de rol in uw abonnement te importeren.

1. Wijzig de eigenschappen `Name` en `Description` in 'Reader Support Tickets' en 'View everything in the subscription and also open support tickets'.

    Uw JSON-bestand moet er nu zo uitzien:

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    
1. Gebruik voor het maken van de nieuwe aangepaste rol de opdracht [az role definition create](/cli/azure/role/definition#az-role-definition-create) en geef het JSON-definitiebestand voor de rol op.

    ```azurecli
    az role definition create --role-definition "~/CustomRoles/ReaderSupportRole.json"
    ```

    ```Output
    {
      "additionalProperties": {},
      "assignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ],
      "description": "View everything in the subscription and also open support tickets.",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
      "name": "22222222-2222-2222-2222-222222222222",
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Support/*"
          ],
          "additionalProperties": {},
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": "Reader Support Tickets",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```

    De nieuwe aangepaste rol is nu beschikbaar en kan worden toegewezen aan gebruikers, groepen of service-principals, net als ingebouwde rollen.

## <a name="list-custom-roles"></a>Aangepaste rollen opvragen

- Als u een overzicht wilt zien van alle aangepaste rollen, gebruikt u de opdracht [az role definition list](/cli/azure/role/definition#az-role-definition-list) met de parameter `--custom-role-only`.

    ```azurecli
    az role definition list --custom-role-only true
    ```
    
    ```Output
    [
      {
        "additionalProperties": {},
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ],
        "description": "View everything in the subscription and also open support tickets.",
        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
        "name": "22222222-2222-2222-2222-222222222222",
        "permissions": [
          {
            "actions": [
              "*/read",
              "Microsoft.Support/*",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Insights/diagnosticSettings/*/read"
            ],
            "additionalProperties": {},
            "dataActions": [],
            "notActions": [],
            "notDataActions": []
          }
        ],
        "roleName": "Reader Support Tickets",
        "roleType": "CustomRole",
        "type": "Microsoft.Authorization/roleDefinitions"
      }
    ]
    ```
    
    U kunt de aangepaste rol ook zien in Azure Portal.

    ![schermafbeelding van aangepaste rol geïmporteerd in Azure Portal](./media/tutorial-custom-role-cli/custom-role-reader-support-tickets.png)

## <a name="update-a-custom-role"></a>Een aangepaste rol bijwerken

Als u de aangepaste rol wilt bijwerken, moet u eerst het JSON-bestand bijwerken en vervolgens de aangepaste rol.

1. Open het bestand ReaderSupportRole.json.

1. Voeg in `Actions` de bewerking toe voor het maken en beheren van implementaties van resourcegroepen`"Microsoft.Resources/deployments/*"`. Vergeet niet om een komma toe te voegen na de vorige bewerking.

    Het bijgewerkte JSON-bestand moet er nu zo uitzien:

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*",
        "Microsoft.Resources/deployments/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
        
1. Als u de aangepaste rol wilt bijwerken, gebruikt u de opdracht [az role definition update](/cli/azure/role/definition#az-role-definition-update) en geeft u het bijgewerkte JSON-bestand op.

    ```azurecli
    az role definition update --role-definition "~/CustomRoles/ReaderSupportRole.json"
    ```

    ```Output
    {
      "additionalProperties": {},
      "assignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ],
      "description": "View everything in the subscription and also open support tickets.",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
      "name": "22222222-2222-2222-2222-222222222222",
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Support/*",
            "Microsoft.Resources/deployments/*"
          ],
          "additionalProperties": {},
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": "Reader Support Tickets",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```
    
## <a name="delete-a-custom-role"></a>Een aangepaste rol verwijderen

- Gebruik de opdracht [az role definition delete](/cli/azure/role/definition#az-role-definition-delete) en geef de naam of id van de rol op om de aangepaste rol te verwijderen.

    ```azurecli
    az role definition delete --name "Reader Support Tickets"
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aangepaste Azure-rollen maken of bijwerken met behulp van Azure CLI](custom-roles-cli.md)
