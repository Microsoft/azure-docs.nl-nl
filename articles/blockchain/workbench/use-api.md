---
title: REST-Api's van Azure Block Chain Workbench gebruiken
description: Scenario's voor het gebruik van de preview-versie van Azure Block Chain Workbench REST API
ms.date: 03/05/2020
ms.topic: how-to
ms.reviewer: brendal
ms.openlocfilehash: 696f1f2f96034f7a044f6a39182774c02804518f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004837"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>De preview-versie van Azure Block Chain Workbench gebruiken REST API

De preview-versie van Azure Block Chain Workbench REST API biedt ontwikkel aars en informatie werkers een manier om uitgebreide integraties te bouwen met block Chain-toepassingen. In dit artikel worden verschillende scenario's beschreven voor het gebruik van de workbench REST API. Stel dat u een aangepaste Block Chain-client wilt maken waarmee aangemelde gebruikers hun toegewezen Block Chain-toepassingen kunnen weer geven en gebruiken. De-client kan de Block Chain Workbench API gebruiken om contract instanties weer te geven en acties uit te voeren op slimme contracten.

## <a name="blockchain-workbench-api-endpoint"></a>Block Chain Workbench API-eind punt

Block Chain Workbench-Api's worden geopend via een eind punt voor uw implementatie. De URL van het API-eind punt ophalen voor uw implementatie:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **resource groepen** in het navigatie deel venster aan de linkerkant.
1. Kies de resource groep met de naam van uw geïmplementeerde Block Chain Workbench.
1. Selecteer de kolomkop **type** om de lijst alfabetisch te sorteren op type.
1. Er zijn twee resources met het type **app service**. Selecteer de resource van het type **app service** *met* het achtervoegsel-API.
1. Kopieer in het **overzicht** van app service de **URL** -waarde, die de URL van het API-eind punt vertegenwoordigt naar uw geïmplementeerde Block Chain Workbench.

    ![URL van app Service API-eind punt](media/use-api/app-service-api.png)

## <a name="authentication"></a>Verificatie

Aanvragen voor de Block Chain Workbench REST API worden beveiligd met Azure Active Directory (Azure AD).

Voor het maken van een geverifieerde aanvraag voor de REST-Api's, vereist de client code verificatie met geldige referenties voordat u de API kunt aanroepen. Verificatie wordt gecoördineerd tussen de verschillende actoren van Azure AD en biedt uw client een [toegangs token](../../active-directory/develop/developer-glossary.md#access-token) als bewijs van de verificatie. Het token wordt vervolgens verzonden in de HTTP-autorisatie-header van REST API-aanvragen. Zie [Azure Active Directory voor ontwikkel aars voor](../../active-directory/develop/index.yml)meer informatie over Azure AD-verificatie.

Raadpleeg [rest API](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples) voor beelden voor voor beelden van verificatie.

## <a name="using-postman"></a>Met Postman

Als u met Workbench Api's wilt testen of experimenteren, kunt u [postman](https://www.postman.com) gebruiken om API-aanroepen naar uw implementatie te maken. [Down load een voor beeld van een postman-verzameling van WORKBENCH API-aanvragen](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples/postman) van github. Raadpleeg het Leesmij-bestand voor meer informatie over het verifiëren en gebruiken van de voor beeld-API-aanvragen.

## <a name="create-an-application"></a>Een app maken

U gebruikt twee API-aanroepen om een Block Chain Workbench-toepassing te maken. Deze methode kan alleen worden uitgevoerd door gebruikers die Workbench beheerders zijn.

Gebruik de [API-berichten](/rest/api/azure-blockchain-workbench/applications/applicationspost) om het JSON-bestand van de toepassing te uploaden en een toepassings-id op te halen.

### <a name="applications-post-request"></a>Sollicitatie POST-aanvraag

Gebruik de para meter **appFile** om het configuratie bestand te verzenden als onderdeel van de hoofd tekst van de aanvraag.

``` http
POST /api/v1/applications
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="appFile"; filename="/C:/smart-contract-samples/HelloWorld.json"
Content-Type: application/json
```

### <a name="applications-post-response"></a>Toepassingen die het antwoord verzenden

De gemaakte toepassings-ID wordt in het antwoord geretourneerd. U hebt de toepassings-ID nodig om het configuratie bestand te koppelen aan het code bestand wanneer u de volgende API aanroept.

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
1
```

### <a name="contract-code-post-request"></a>POST-aanvraag contract code

Gebruik de [contract code](/rest/api/azure-blockchain-workbench/applications/contractcodepost) van de toepassing post-API door de toepassings-id door te geven voor het uploaden van het bestand met de code ring van de toepassing. De payload kan één bestand met een volheid zijn of een zip-bestand met volheid-bestanden.

Vervang de volgende waarden:

| Parameter | Waarde |
|-----------|-------|
| ApplicationId | Retour waarde van de API-berichten na de toepassing. |
| {ledgerId} | Index van het groot boek. De waarde is doorgaans 1. U kunt ook de [grootboek tabel](data-sql-management-studio.md) voor de waarde controleren. |

``` http
POST /api/v1/applications/{applicationId}/contractCode?ledgerId={ledgerId}
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="contractFile"; filename="/C:/smart-contract-samples/HelloWorld.sol"
```

### <a name="contract-code-post-response"></a>Contract code POST-antwoord

Als dit is gelukt, bevat het antwoord de gemaakte contract code-ID uit de [tabel ContractCode](data-sql-management-studio.md).

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
2
```

## <a name="assign-roles-to-users"></a>Rollen toewijzen aan gebruikers

Gebruik de [functie toewijzingen van toepassingen post-API](/rest/api/azure-blockchain-workbench/applications/roleassignmentspost) door de toepassings-id, gebruikers-id en toepassingsrol-id door te geven om een gebruiker-to-Role-toewijzing in de opgegeven Block Chain-toepassing te maken. Deze methode kan alleen worden uitgevoerd door gebruikers die Workbench beheerders zijn.

### <a name="role-assignments-post-request"></a>Roltoewijzingen POST-aanvraag

Vervang de volgende waarden:

| Parameter | Waarde |
|-----------|-------|
| ApplicationId | Retour waarde van de API-berichten na de toepassing. |
| Naam | De waarde van de gebruikers-ID uit de [gebruikers tabel](data-sql-management-studio.md). |
| {applicationRoleId} | De waarde van de toepassingsrol-ID die is gekoppeld aan de toepassings-ID uit de [tabel ApplicationRole](data-sql-management-studio.md). |

``` http
POST /api/v1/applications/{applicationId}/roleAssignments
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "userId": {userId},
  "applicationRoleId": {applicationRoleId}
}
```

### <a name="role-assignments-post-response"></a>Roltoewijzingen POST-antwoord

Als dit is gelukt, bevat het antwoord de gemaakte roltoewijzings-ID uit de [tabel RoleAssignment](data-sql-management-studio.md).

``` http
HTTP/1.1 200
1
```

## <a name="list-applications"></a>Lijst met toepassingen weergeven

Gebruik de [apps Get API](/rest/api/azure-blockchain-workbench/applications/applicationsget) om alle Block Chain Workbench-toepassingen voor de gebruiker op te halen. In dit voor beeld heeft de aangemelde gebruiker toegang tot twee toepassingen:

- [Asset overboeking](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
- [Koel transport](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

### <a name="applications-get-request"></a>Aanvraag ophalen aanvragen

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

### <a name="applications-get-response"></a>Reactie van toepassingen ophalen

In het antwoord wordt een lijst weer gegeven met alle Block Chain-toepassingen waartoe een gebruiker toegang heeft in Block Chain Workbench. Block Chain Workbench-beheerders krijgen elke Block Chain-toepassing. Beheerders van niet-Workbench krijgen alle Block Chain-toepassingen waarvoor ze ten minste één gekoppelde toepassingsrol of een gekoppelde rol van een slimme-contract instantie hebben.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications?skip=2",
    "applications": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
            "displayName": "Asset Transfer",
            "createdByUserId": 1,
            "createdDtTm": "2018-04-28T05:59:14.4733333",
            "enabled": true,
            "applicationRoles": null
        },
        {
            "id": 2,
            "name": "RefrigeratedTransportation",
            "description": "Application to track end-to-end transportation of perishable goods.",
            "displayName": "Refrigerated Transportation",
            "createdByUserId": 7,
            "createdDtTm": "2018-04-28T18:25:38.71",
            "enabled": true,
            "applicationRoles": null
        }
    ]
}
```

## <a name="list-workflows-for-an-application"></a>Lijst met werkstromen voor een toepassing weergeven

Gebruik [toepassingen werk stromen API ophalen](/rest/api/azure-blockchain-workbench/applications/workflowsget) om een lijst weer te geven met alle werk stromen van een opgegeven Block Chain-toepassing waartoe een gebruiker toegang heeft in Block Chain Workbench. Elke blockchain-toepassing heeft een of meer werkstromen en elke werkstroom heeft nul of meer exemplaren van slimme contracten. Voor een Block Chain-client toepassing die slechts één werk stroom heeft, wordt u aangeraden de stroom voor gebruikers ervaring over te slaan waarmee gebruikers de juiste werk stroom kunnen selecteren.

### <a name="application-workflows-request"></a>Aanvraag voor toepassings werk stromen

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

### <a name="application-workflows-response"></a>Reactie op toepassings werk stromen

Block Chain Workbench-beheerders krijgen elke Block Chain-werk stroom. Beheerders van niet-Workbench krijgen alle werk stromen waarvoor ze ten minste één gekoppelde toepassingsrol hebben of die is gekoppeld aan een rol van een intelligent contract exemplaar.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications/1/workflows?skip=1",
    "workflows": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Handles the business logic for the asset transfer scenario",
            "displayName": "Asset Transfer",
            "applicationId": 1,
            "constructorId": 1,
            "startStateId": 1
        }
    ]
}
```

## <a name="create-a-contract-instance"></a>Een contract exemplaar maken

Gebruik [contracten v2 post-API](/rest/api/azure-blockchain-workbench/contractsv2/contractpost) om een nieuw exemplaar van het slimme contract te maken voor een werk stroom. Gebruikers kunnen alleen een nieuw intelligent contract exemplaar maken als de gebruiker is gekoppeld aan een toepassingsrol, waarmee een info contract exemplaar voor de werk stroom kan worden gestart.

> [!NOTE]
> In dit voor beeld wordt versie 2 van de API gebruikt. Versie 2-contract-Api's bieden meer granulatie voor de gekoppelde ProvisioningStatus velden.

### <a name="contracts-post-request"></a>Contracten POST-aanvraag

Vervang de volgende waarden:

| Parameter | Waarde |
|-----------|-------|
| WorkflowId | De waarde van de werk stroom-ID is de ConstructorID van het contract uit de [werk stroom tabel](data-sql-management-studio.md). |
| {contractCodeId} | De waarde van de contract code-ID in de [ContractCode-tabel](data-sql-management-studio.md). Correleer de toepassings-ID en de grootboek-ID voor het contract exemplaar dat u wilt maken. |
| ConnectionId | De waarde van de verbindings-ID uit de [verbindings tabel](data-sql-management-studio.md). |

Stel in de hoofd tekst van de aanvraag waarden in met behulp van de volgende gegevens:

| Parameter | Waarde |
|-----------|-------|
| workflowFunctionID | ID uit de [tabel WorkflowFunction](data-sql-management-studio.md). |
| workflowActionParameters | Naam waarde-paren van para meters die zijn door gegeven aan de constructor. Gebruik voor elke para meter de waarde workflowFunctionParameterID uit de tabel [WorkflowFunctionParameter](data-sql-management-studio.md) . |

``` http
POST /api/v2/contracts?workflowId={workflowId}&contractCodeId={contractCodeId}&connectionId={connectionId}
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "workflowFunctionID": 2,
  "workflowActionParameters": [
    {
      "name": "message",
      "value": "Hello, world!",
      "workflowFunctionParameterId": 3
    }
  ]
}
```

### <a name="contracts-post-response"></a>Contracten POST-antwoord

Als deze is geslaagd, retourneert functie toewijzings-API de ContractActionID uit de [tabel ContractActionParameter](data-sql-management-studio.md).

``` http
HTTP/1.1 200 OK
4
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>Lijst met exemplaren van slimme contracten voor een werkstroom weergeven

Gebruik [contracten Get API](/rest/api/azure-blockchain-workbench/contractsv2/contractsget) om alle slimme contract instanties voor een werk stroom weer te geven. U kunt gebruikers ook in staat stellen om een van de weer gegeven slimme contract instanties te bekijken.

### <a name="contracts-request"></a>Aanvraag voor contracten

Stel dat een gebruiker in dit voorbeeld interactie wil hebben met een van de exemplaren van een slim contract om er vervolgens een bepaalde actie op uit te voeren.

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

### <a name="contracts-response"></a>Antwoord op contracten

In het antwoord wordt een lijst weer gegeven met alle slimme contract instanties van de opgegeven werk stroom. Workbench beheerders krijgen alle slimme contract instanties. Beheerders van niet-Workbench krijgen elke slimme contract instantie waarvoor ze ten minste één gekoppelde toepassingsrol hebben of die is gekoppeld aan een rol van een slimme-contract instantie.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts?skip=3&workflowId=1",
    "contracts": [
        {
            "id": 1,
            "provisioningStatus": 2,
            "connectionID": 1,
            "ledgerIdentifier": "0xbcb6127be062acd37818af290c0e43479a153a1c",
            "deployedByUserId": 1,
            "workflowId": 1,
            "contractCodeId": 1,
            "contractProperties": [
                {
                    "workflowPropertyId": 1,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 2,
                    "value": "My first car"
                },
                {
                    "workflowPropertyId": 3,
                    "value": "54321"
                },
                {
                    "workflowPropertyId": 4,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 5,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 6,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 7,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 8,
                    "value": "0xd882530eb3d6395e697508287900c7679dbe02d7"
                }
            ],
            "transactions": [
                {
                    "id": 1,
                    "connectionId": 1,
                    "transactionHash": "0xf3abb829884dc396e03ae9e115a770b230fcf41bb03d39457201449e077080f4",
                    "blockID": 241,
                    "from": "0xd882530eb3d6395e697508287900c7679dbe02d7",
                    "to": null,
                    "value": 0,
                    "isAppBuilderTx": true
                }
            ],
            "contractActions": [
                {
                    "id": 1,
                    "userId": 1,
                    "provisioningStatus": 2,
                    "timestamp": "2018-04-29T23:41:14.9333333",
                    "parameters": [
                        {
                            "name": "Description",
                            "value": "My first car"
                        },
                        {
                            "name": "Price",
                            "value": "54321"
                        }
                    ],
                    "workflowFunctionId": 1,
                    "transactionId": 1,
                    "workflowStateId": 1
                }
            ]
        }
    ]
}
```

## <a name="list-available-actions-for-a-contract"></a>Lijst met beschikbare acties voor een contract weergeven

Gebruik [contract actie Get API](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget) om de beschik bare gebruikers acties weer te geven op basis van de status van het contract. 

### <a name="contract-action-request"></a>Actie aanvraag contract

In dit voor beeld bekijkt de gebruiker alle beschik bare acties voor een nieuw, door hen gemaakt slim contract.

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

### <a name="contract-action-response"></a>Antwoord op contract actie

Het antwoord bestaat uit een lijst met alle acties die een gebruiker kan uitvoeren op basis van de huidige status van het opgegeven exemplaar van het slimme contract.

* Modify: hiermee kan de gebruiker de beschrijving en prijs van een asset aanpassen.
* Beëindigen: Hiermee kan de gebruiker het contract van de Asset beëindigen.

Gebruikers zien alle toepasselijke acties als de gebruiker een bijbehorende toepassingsrol heeft of een rol voor het exemplaar van het slimme contract voor de huidige status van het opgegeven exemplaar van het slimme contract.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts/1/actions?skip=2",
    "workflowFunctions": [
        {
            "id": 2,
            "name": "Modify",
            "description": "Modify the description/price attributes of this asset transfer instance",
            "displayName": "Modify",
            "parameters": [
                {
                    "id": 1,
                    "name": "description",
                    "description": "The new description of the asset",
                    "displayName": "Description",
                    "type": {
                        "id": 2,
                        "name": "string",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                },
                {
                    "id": 2,
                    "name": "price",
                    "description": "The new price of the asset",
                    "displayName": "Price",
                    "type": {
                        "id": 3,
                        "name": "money",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                }
            ],
            "workflowId": 1
        },
        {
            "id": 3,
            "name": "Terminate",
            "description": "Used to cancel this particular instance of asset transfer",
            "displayName": "Terminate",
            "parameters": [],
            "workflowId": 1
        }
    ]
}
```

## <a name="execute-an-action-for-a-contract"></a>Een actie voor een contract uitvoeren

Gebruik de [contract actie post-API](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost) om actie te ondernemen voor het opgegeven exemplaar van de Smart-contract.

### <a name="contract-action-post-request"></a>Contract actie POST-aanvraag

In dit geval moet u rekening houden met het scenario waarin een gebruiker de beschrijving en prijs van een activum wil wijzigen.

``` http
POST /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
actionInformation: {
    "workflowFunctionId": 2,
    "workflowActionParameters": [
        {
            "name": "description",
            "value": "My updated car"
        },
        {
            "name": "price",
            "value": "54321"
        }
    ]
}
```

Gebruikers kunnen alleen de actie uitvoeren die mogelijk is op basis van de huidige status van het opgegeven exemplaar van het slimme contract en de bijbehorende toepassingsrol van de gebruiker of de rol van het exemplaar van het slimme contract.

### <a name="contract-action-post-response"></a>Actie POST-antwoord contract

Als het posten is gelukt, wordt dit aangegeven met een HTTP 200 OK-antwoord zonder verdere toelichting.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>Volgende stappen

Zie voor naslag informatie over Block Chain Workbench-Api's de [Naslag Gids voor Azure Block Chain Workbench rest API](/rest/api/azure-blockchain-workbench).
