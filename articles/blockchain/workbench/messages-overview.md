---
title: Berichten gebruiken om te integreren met Azure Block Chain workbench
description: Overzicht van het gebruik van berichten om de preview-versie van Azure Block Chain Workbench te integreren met andere systemen.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 14bd0f84bc9490d95d3dbe0b9f122882f0d2059d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74324512"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Integratie van Azure Block Chain Workbench-berichten

Naast het leveren van een REST API biedt Azure Block Chain Workbench ook integratie op basis van berichten. Workbench publiceert grootboek gerichte gebeurtenissen via Azure Event Grid, waardoor downstream-gebruikers gegevens opnemen of actie ondernemen op basis van deze gebeurtenissen. Voor die clients die betrouw bare berichten vereisen, levert Azure Block Chain Workbench ook berichten aan een Azure Service Bus eind punt.

## <a name="input-apis"></a>Invoer-Api's

Als u trans acties van externe systemen wilt initiëren om gebruikers te maken, contracten te maken en contracten bij te werken, kunt u invoer-Api's voor berichten gebruiken om trans acties op een groot boek uit te voeren. Zie voor [beelden van berichten integratie](https://aka.ms/blockchain-workbench-integration-sample) voor een voor beeld van invoer-api's.

Hieronder vindt u de momenteel beschik bare invoer-Api's.

### <a name="create-user"></a>Gebruiker maken

Hiermee maakt u een nieuwe gebruiker.

De aanvraag vereist de volgende velden:

| **Naam**             | **Beschrijving**                                      |
|----------------------|------------------------------------------------------|
| requestId            | Door client opgegeven GUID                                |
| voornaam            | De voor naam van de gebruiker                              |
| achternaam             | Achternaam van de gebruiker                               |
| emailAddress         | Het e-mailadres van de gebruiker                           |
| externalId           | Azure AD-object-ID van de gebruiker                      |
| connectionId         | De unieke id voor de Block Chain-verbinding |
| messageSchemaVersion | Schema versie berichten                            |
| berichtnaam          | **CreateUserRequest**                               |

Voorbeeld:

``` json
{
    "requestId": "e2264523-6147-41fc-bbbb-edba8e44562d",
    "firstName": "Ali",
    "lastName": "Alio",
    "emailAddress": "aa@contoso.com",
    "externalId": "6a9b7f65-ffff-442f-b3b8-58a35abd1bcd",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateUserRequest"
}
```

Block Chain Workbench retourneert een antwoord met de volgende velden:

| **Naam**              | **Beschrijving**                                                                                                             |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------|
| requestId             | Door client opgegeven GUID |
| userId                | ID van de gebruiker die is gemaakt |
| userChainIdentifier   | Het adres van de gebruiker die is gemaakt in het block chain-netwerk. In Ethereum is het adres het adres van de gebruiker **op de keten** . |
| connectionId          | De unieke id voor de Block Chain-verbinding|
| messageSchemaVersion  | Schema versie berichten |
| berichtnaam           | **CreateUserUpdate** |
| status                | Status van de aanvraag voor het maken van een gebruiker.  Als de bewerking is geslaagd, is de waarde **geslaagd**. Als de fout is opgetreden, is de waarde **mislukt**.     |
| additionalInformation | Aanvullende informatie die wordt verstrekt op basis van de status |

Voor beeld van succes volle **gebruikers** reactie van Block Chain Workbench:

``` json
{ 
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Success", 
    "additionalInformation": { } 
} 
```

Als de aanvraag is mislukt, vindt u meer informatie over de fout.

``` json
{
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": null, 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Failure", 
    "additionalInformation": { 
        "errorCode": 4000, 
        "errorMessage": "User cannot be provisioned on connection." 
    }
}
```

### <a name="create-contract"></a>Contract maken

Hiermee maakt u een nieuw contract.

De aanvraag vereist de volgende velden:

| **Naam**             | **Beschrijving**                                                                                                           |
|----------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId            | Door client opgegeven GUID |
| userChainIdentifier  | Het adres van de gebruiker die is gemaakt in het block chain-netwerk. In Ethereum is dit adres het adres van de gebruiker **op de keten** . |
| applicationName      | Naam van de toepassing |
| versie              | Versie van de toepassing. Vereist als er meerdere versies van de toepassing zijn ingeschakeld. Anders is versie optioneel. Zie voor meer informatie over toepassings versie beheer [Azure Block Chain Workbench toepassings versie beheer](version-app.md). |
| workflowName         | Naam van de werk stroom |
| parameters           | Invoer parameters voor het maken van contracten |
| connectionId         | De unieke id voor de Block Chain-verbinding |
| messageSchemaVersion | Schema versie berichten |
| berichtnaam          | **CreateContractRequest** |

Voorbeeld:

``` json
{ 
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211", 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "applicationName": "AssetTransfer",
    "version": "1.0",
    "workflowName": "AssetTransfer", 
    "parameters": [ 
        { 
            "name": "description", 
            "value": "a 1969 dodge charger" 
        }, 
        { 
            "name": "price", 
            "value": "12345" 
        } 
    ], 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateContractRequest" 
}
```

Block Chain Workbench retourneert een antwoord met de volgende velden:

| **Naam**                 | **Beschrijving**                                                                   |
|--------------------------|-----------------------------------------------------------------------------------|
| requestId                | Door client opgegeven GUID                                                             |
| contractId               | De unieke id voor het contract in azure Block Chain workbench |
| contractLedgerIdentifier | Adres van het contract in het groot boek                                            |
| connectionId             | De unieke id voor de Block Chain-verbinding                               |
| messageSchemaVersion     | Schema versie berichten                                                         |
| berichtnaam              | **CreateContractUpdate**                                                      |
| status                   | Status van de aanvraag voor het maken van het contract.  Mogelijke waarden: **verzonden**, **doorgevoerd**, **fout**.  |
| additionalInformation    | Aanvullende informatie die wordt verstrekt op basis van de status                              |

Voor beeld van een ingediend verzoek om een **contract te maken** vanuit Block Chain Workbench:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Voor beeld van een vastgelegd antwoord op een contract voor het **maken** van Block Chain-Workbench:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

Als de aanvraag is mislukt, vindt u meer informatie over de fout.

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": null,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Failure",
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract cannot be provisioned on connection."
    }
}
```

### <a name="create-contract-action"></a>Contract actie maken

Hiermee maakt u een nieuwe contract actie.

De aanvraag vereist de volgende velden:

| **Naam**                 | **Beschrijving**                                                                                                           |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId                | Door client opgegeven GUID |
| userChainIdentifier      | Het adres van de gebruiker die is gemaakt in het block chain-netwerk. In Ethereum is dit adres het adres van de gebruiker **op de keten** . |
| contractLedgerIdentifier | Adres van het contract in het groot boek |
| versie                  | Versie van de toepassing. Vereist als er meerdere versies van de toepassing zijn ingeschakeld. Anders is versie optioneel. Zie voor meer informatie over toepassings versie beheer [Azure Block Chain Workbench toepassings versie beheer](version-app.md). |
| workflowFunctionName     | De naam van de werk stroom functie |
| parameters               | Invoer parameters voor het maken van contracten |
| connectionId             | De unieke id voor de Block Chain-verbinding |
| messageSchemaVersion     | Schema versie berichten |
| berichtnaam              | **CreateContractActionRequest** |

Voorbeeld:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398",
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "version": "1.0",
    "workflowFunctionName": "modify",
    "parameters": [
        {
            "name": "description",
            "value": "a 1969 dodge charger"
        },
        {
            "name": "price",
            "value": "12345"
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionRequest"
}
```

Block Chain Workbench retourneert een antwoord met de volgende velden:

| **Naam**              | **Beschrijving**                                                                   |
|-----------------------|-----------------------------------------------------------------------------------|
| requestId             | Door client opgegeven GUID|
| contractId            | De unieke id voor het contract in azure Block Chain workbench |
| connectionId          | De unieke id voor de Block Chain-verbinding |
| messageSchemaVersion  | Schema versie berichten |
| berichtnaam           | **CreateContractActionUpdate** |
| status                | Status van de actie aanvraag van het contract. Mogelijke waarden: **verzonden**, **doorgevoerd**, **fout**.                         |
| additionalInformation | Aanvullende informatie die wordt verstrekt op basis van de status |

Voor beeld van een verzonden actie respons voor het maken van een **contract** vanuit Block Chain Workbench:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Voor beeld van een vastgelegd actie respons voor het maken van een **contract** vanuit Block Chain Workbench:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

Als de aanvraag is mislukt, vindt u meer informatie over de fout.

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Failure",
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract action cannot be provisioned on connection."
    }
}
```

### <a name="input-api-error-codes-and-messages"></a>Invoer-API-fout codes en-berichten

**Fout code 4000: onjuiste aanvraag fout**
- Ongeldige connectionId
- CreateUserRequest-deserialisatie is mislukt
- CreateContractRequest-deserialisatie is mislukt
- CreateContractActionRequest-deserialisatie is mislukt
- De toepassing {geïdentificeerd door de toepassings naam} bestaat niet
- De toepassing {geïdentificeerd door de toepassings naam} heeft geen werk stroom
- UserChainIdentifier bestaat niet
- Contract {geïdentificeerd door boekhoud-id} bestaat niet
- Contract {geïdentificeerd door boekhoud-id} heeft geen functie {naam werk stroom functie}
- UserChainIdentifier bestaat niet

**Fout code 4090: conflict fout**
- Gebruiker bestaat al
- Contract bestaat al
- De contract actie bestaat al

**Fout code 5000: interne server fout**
- Uitzonderings berichten

## <a name="event-notifications"></a>Gebeurtenismeldingen

Gebeurtenis meldingen kunnen worden gebruikt voor het melden van gebruikers en downstream-systemen van gebeurtenissen die plaatsvinden in Block Chain Workbench en het block chain-netwerk waarmee het is verbonden. Gebeurtenis meldingen kunnen rechtstreeks in code worden verbruikt of worden gebruikt met hulpprogram ma's als Logic Apps en flow om gegevens stroom naar downstream-systemen te activeren.

Zie [Naslag informatie voor meldings berichten](#notification-message-reference) voor details over verschillende berichten die kunnen worden ontvangen.

### <a name="consuming-event-grid-events-with-azure-functions"></a>Event Grid gebeurtenissen gebruiken met Azure Functions

Als een gebruiker Event Grid wil gebruiken om op de hoogte te worden gesteld van gebeurtenissen die zich in Block Chain Workbench voordoen, kunt u gebeurtenissen uit Event Grid gebruiken met behulp van Azure Functions.

1. Maak een **Azure-functie-app** in het Azure Portal.
2. Maak een nieuwe functie.
3. Zoek de sjabloon voor de Event Grid. De Basic-sjabloon code voor het lezen van het bericht wordt weer gegeven. Wijzig de code naar wens.
4. Sla de functie op. 
5. Selecteer de Event Grid van de resource groep van Block Chain Workbench.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Event Grid gebeurtenissen gebruiken met Logic Apps

1. Maak een nieuwe **Azure Logic-app** in de Azure Portal.
2. Wanneer u de Azure Logic-app in de portal opent, wordt u gevraagd een trigger te selecteren. Selecteer **Azure Event grid--als er een resource gebeurtenis optreedt**.
3. Wanneer de werk stroom ontwerper wordt weer gegeven, wordt u gevraagd u aan te melden.
4. Selecteer het abonnement. Resource als **micro soft. EventGrid. topics**. Selecteer de **resource naam** uit de naam van de resource uit de resource groep Azure Block Chain Workbench.
5. Selecteer de Event Grid van de resource groep van Block Chain Workbench.

## <a name="using-service-bus-topics-for-notifications"></a>Service Bus onderwerpen gebruiken voor meldingen

Service Bus-onderwerpen kunnen worden gebruikt om gebruikers op de hoogte te stellen van gebeurtenissen die zich in Block Chain Workbench voordoen. 

1. Blader naar de Service Bus in de resource groep van de Workbench.
2. Selecteer **Onderwerpen**.
3. Selecteer **uitgangs-onderwerp**.
4. Maak een nieuw abonnement op dit onderwerp. Schaf hiervoor een sleutel aan.
5. Een programma maken dat zich abonneert op gebeurtenissen van dit abonnement.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Service Bus berichten gebruiken met Logic Apps

1. Maak een nieuwe **Azure Logic-app** in de Azure Portal.
2. Wanneer u de Azure Logic-app in de portal opent, wordt u gevraagd een trigger te selecteren. Typ **Service Bus** in het zoekvak en selecteer de trigger die geschikt is voor het type interactie dat u wilt hebben met de service bus. Bijvoorbeeld **Service Bus: wanneer een bericht wordt ontvangen in een onderwerps abonnement (automatisch volt ooien)**.
3. Wanneer de werk stroom ontwerper wordt weer gegeven, geeft u de verbindings gegevens voor de Service Bus op.
4. Selecteer uw abonnement en geef het onderwerp van **Workbench-extern** op.
5. Ontwikkel de logica voor uw toepassing die gebruikmaakt van het bericht van deze trigger.

## <a name="notification-message-reference"></a>Naslag informatie voor meldings berichten

De meldings berichten bevatten afhankelijk van de **bericht** naam een van de volgende bericht typen.

### <a name="block-message"></a>Bericht blok keren

Bevat informatie over afzonderlijke blokken. De *BlockMessage* bevat een sectie met informatie over het blok niveau en een sectie met transactie gegevens.

| Naam | Beschrijving |
|------|-------------|
| blokkeren | Bevat [blok gegevens](#block-information) |
| transacties | Bevat [informatie over](#transaction-information) de verzamelings transactie voor het blok |
| connectionId | De unieke id voor de verbinding |
| messageSchemaVersion | Schema versie berichten |
| berichtnaam | **BlockMessage** |
| additionalInformation | Aanvullende informatie |

#### <a name="block-information"></a>Gegevens blok keren

| Naam              | Beschrijving |
|-------------------|-------------|
| blockId           | De unieke id voor de blok kering in azure Block Chain workbench |
| blockNumber       | Unieke id voor een blok in het groot boek |
| blockHash         | De hash van het blok |
| previousBlockHash | De hash van het vorige blok |
| blockTimestamp    | De tijds tempel van het blok |

#### <a name="transaction-information"></a>Trans actie-informatie

| Naam               | Beschrijving |
|--------------------|-------------|
| transactionId      | De unieke id voor de trans actie in azure Block Chain workbench |
| transactionHash    | De hash van de trans actie in het groot boek |
| from               | Unieke id in het groot boek voor de oorsprong van de trans actie |
| tot                 | Unieke id in het groot boek voor het doel van de trans actie |
| provisioningStatus | Identificeert de huidige status van het inrichtings proces voor de trans actie. Mogelijke waarden zijn: </br>0: de trans actie is gemaakt door de API in de data base</br>1: de trans actie is naar het groot boek verzonden</br>2: de trans actie is doorgevoerd voor het groot boek</br>3 of 4: de trans actie kan niet worden doorgevoerd in het groot boek</br>5: de trans actie is doorgevoerd naar het groot boek |

Voor beeld van een *BlockMessage* van Block Chain Workbench:

``` json
{
    "block": {
        "blockId": 123,
        "blockNumber": 1738312,
        "blockHash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
        "previousBlockHash": null,
        "blockTimestamp": "2018-10-09T23:35:58Z",
    },
    "transactions": [
        {
            "transactionId": 234,
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": null,
            "provisioningStatus": 1
        },
        {
            "transactionId": 235,
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xadd97e1e595916e29ea94fda894941574000ffff",
            "to": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff",
            "provisioningStatus": 2
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "BlockMessage",
    "additionalInformation": {}
}
```

### <a name="contract-message"></a>Contract bericht

Bevat informatie over een contract. Het bericht bevat een sectie met contract eigenschappen en een sectie met transactie gegevens. Alle trans acties die het contract voor het desbetreffende blok hebben gewijzigd, worden opgenomen in de sectie trans actie.

| Naam | Beschrijving |
|------|-------------|
| blockId | De unieke id voor de blok kering in azure Block Chain workbench |
| blockHash | Hash van het blok |
| modifyingTransactions | [Trans acties waarmee](#modifying-transaction-information) het contract is gewijzigd |
| contractId | De unieke id voor het contract in azure Block Chain workbench |
| contractLedgerIdentifier | De unieke id voor het contract in het groot boek |
| contractProperties | [Eigenschappen van het contract](#contract-properties) |
| isNewContract | Hiermee wordt aangegeven of dit contract nieuw is gemaakt. Mogelijke waarden zijn: True: voor dit contract is een nieuw contract gemaakt. onwaar: dit contract is een update van het contract. |
| connectionId | De unieke id voor de verbinding |
| messageSchemaVersion | Schema versie berichten |
| berichtnaam | **ContractMessage** |
| additionalInformation | Aanvullende informatie |

#### <a name="modifying-transaction-information"></a>Transactie gegevens wijzigen

| Naam               | Beschrijving |
|--------------------|-------------|
| transactionId | De unieke id voor de trans actie in azure Block Chain workbench |
| transactionHash | De hash van de trans actie in het groot boek |
| from | Unieke id in het groot boek voor de oorsprong van de trans actie |
| tot | Unieke id in het groot boek voor het doel van de trans actie |

#### <a name="contract-properties"></a>Eigenschappen van contract

| Naam               | Beschrijving |
|--------------------|-------------|
| workflowPropertyId | Unieke id voor de werk stroom eigenschap in azure Block Chain workbench |
| naam | Naam van de werk stroom eigenschap |
| waarde | Waarde van de werk stroom eigenschap |

Voor beeld van een *ContractMessage* van Block Chain Workbench:

``` json
{
    "blockId": 123,
    "blockhash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
    "modifyingTransactions": [
        {
            "transactionId": 234,
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07ffff"
        },
        {
            "transactionId": 235,
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
        }
    ],
    "contractId": 111,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "contractProperties": [
        {
            "workflowPropertyId": 1,
            "name": "State",
            "value": "0"
        },
        {
            "workflowPropertyId": 2,
            "name": "Description",
            "value": "1969 Dodge Charger"
        },
        {
            "workflowPropertyId": 3,
            "name": "AskingPrice",
            "value": "30000"
        },
        {
            "workflowPropertyId": 4,
            "name": "OfferPrice",
            "value": "0"
        },
        {
            "workflowPropertyId": 5,
            "name": "InstanceAppraiser",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 6,
            "name": "InstanceBuyer",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 7,
            "name": "InstanceInspector",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 8,
            "name": "InstanceOwner",
            "value": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
        },
        {
            "workflowPropertyId": 9,
            "name": "ClosingDayOptions",
            "value": "[21,48,69]"
        }
    ],
    "isNewContract": false,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "ContractMessage",
    "additionalInformation": {}
}
```

### <a name="event-message-contract-function-invocation"></a>Gebeurtenis bericht: aanroep van contract functie

Bevat informatie over het aanroepen van een contract functie, zoals de functie naam, de invoer van para meters en de aanroeper van de functie.

| Naam | Beschrijving |
|------|-------------|
| eventName                   | **ContractFunctionInvocation** |
| aanroeper                      | [Gegevens van oproepende functie](#caller-information) |
| contractId                  | De unieke id voor het contract in azure Block Chain workbench |
| contractLedgerIdentifier    | De unieke id voor het contract in het groot boek |
| functionName                | De naam van de functie |
| parameters                  | [Parametergegevens](#parameter-information) |
| trans actie                 | Trans actie-informatie |
| inTransactionSequenceNumber | Het Volg nummer van de trans actie in het blok |
| connectionId                | De unieke id voor de verbinding |
| messageSchemaVersion        | Schema versie berichten |
| berichtnaam                 | **EventMessage** |
| additionalInformation       | Aanvullende informatie |

#### <a name="caller-information"></a>Gegevens van oproepende functie

| Naam | Beschrijving |
|------|-------------|
| type | Type oproepende functie, zoals een gebruiker of een contract |
| id | De unieke id voor de aanroeper in azure Block Chain workbench |
| ledgerIdentifier | De unieke id voor de oproepende functie in het groot boek |

#### <a name="parameter-information"></a>Parametergegevens

| Naam | Beschrijving |
|------|-------------|
| naam | Parameternaam |
| waarde | Parameterwaarde |

#### <a name="event-message-transaction-information"></a>Trans actie-informatie voor gebeurtenis bericht

| Naam               | Beschrijving |
|--------------------|-------------|
| transactionId      | De unieke id voor de trans actie in azure Block Chain workbench |
| transactionHash    | De hash van de trans actie in het groot boek |
| from               | Unieke id in het groot boek voor de oorsprong van de trans actie |
| tot                 | Unieke id in het groot boek voor het doel van de trans actie |

Voor beeld van een *EventMessage-ContractFunctionInvocation* van Block Chain Workbench:

``` json
{
    "eventName": "ContractFunctionInvocation",
    "caller": {
        "type": "User",
        "id": 21,
        "ledgerIdentifier": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60ffff"
    },
    "contractId": 34,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "functionName": "Modify",
    "parameters": [
        {
            "name": "description",
            "value": "a new description"
        },
        {
            "name": "price",
            "value": "4567"
        }
    ],
    "transaction": {
        "transactionId": 234,
        "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
        "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
        "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
    },
    "inTransactionSequenceNumber": 1,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

### <a name="event-message-application-ingestion"></a>Gebeurtenis bericht: opname van toepassing

Bevat informatie over het uploaden van een toepassing naar Workbench, zoals de naam en de versie van de geüploade toepassing.

| Naam | Beschrijving |
|------|-------------|
| eventName | **ApplicationIngestion** |
| applicationId | De unieke id voor de toepassing in azure Block Chain workbench |
| applicationName | De naam van de toepassing |
| applicationDisplayName | Weergave naam van toepassing |
| applicationVersion | Toepassings versie |
| applicationDefinitionLocation | URL waar het configuratie bestand van de toepassing zich bevindt |
| contractCodes | Verzameling van [contract codes](#contract-code-information) voor de toepassing |
| applicationRoles | Verzameling [toepassings rollen](#application-role-information) voor de toepassing |
| applicationWorkflows | Verzameling van [toepassings werk stromen](#application-workflow-information) voor de toepassing |
| connectionId | De unieke id voor de verbinding |
| messageSchemaVersion | Schema versie berichten |
| berichtnaam | **EventMessage** |
| additionalInformation | Hier vindt u aanvullende informatie over de status van de werk stroom van de toepassing en over de overgangs informatie. |

#### <a name="contract-code-information"></a>Informatie over contract code

| Naam | Beschrijving |
|------|-------------|
| id | De unieke id voor het contract code bestand in azure Block Chain workbench |
| ledgerId | Unieke id voor het groot boek in azure Block Chain workbench |
| location | URL waar het contract code bestand zich bevindt |

#### <a name="application-role-information"></a>Informatie over toepassingsrol

| Naam | Beschrijving |
|------|-------------|
| id | De unieke id voor de toepassingsrol binnen Azure Block Chain workbench |
| naam | Naam van de toepassingsrol |

#### <a name="application-workflow-information"></a>Informatie over de werk stroom van de toepassing

| Naam | Beschrijving |
|------|-------------|
| id | Unieke id voor de toepassings werk stroom in azure Block Chain workbench |
| naam | Naam van toepassings werk stroom |
| displayName | Weergave naam van de toepassings werk stroom |
| vervullen | Verzameling [functies voor de werk stroom van de toepassing](#workflow-function-information)|
| toestand | Verzameling van [statussen voor de werk stroom van de toepassing](#workflow-state-information) |
| properties | [Informatie over eigenschappen van toepassings werk stroom](#workflow-property-information) |

##### <a name="workflow-function-information"></a>Informatie over werk stroom functies

| Naam | Beschrijving |
|------|-------------|
| id | Unieke id voor de functie van de werk stroom van de toepassing in azure Block Chain workbench |
| naam | Functienaam |
| parameters | Para meters voor de functie |

##### <a name="workflow-state-information"></a>Status informatie werk stroom

| Naam | Beschrijving |
|------|-------------|
| naam | Status naam |
| displayName | Weergave naam status |
| stijl | Status stijl (geslaagd of mislukt) |

##### <a name="workflow-property-information"></a>Informatie over werk stroom-eigenschappen

| Naam | Beschrijving |
|------|-------------|
| id | Unieke id voor de eigenschap van de toepassings werk stroom in azure Block Chain workbench |
| naam | Naam van eigenschap |
| type | Type eigenschap |

Voor beeld van een *EventMessage-ApplicationIngestion* van Block Chain Workbench:

``` json
{
    "eventName": "ApplicationIngestion",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": "1.0",
    "applicationDefinitionLocation": "http://url",
    "contractCodes": [
        {
            "id": 23,
            "ledgerId": 1,
            "location": "http://url"
        }
    ],
    "applicationRoles": [
            {
                "id": 134,
                "name": "Buyer"
            },
            {
                "id": 135,
                "name": "Seller"
            }
       ],
    "applicationWorkflows": [
        {
            "id": 89,
            "name": "AssetTransfer",
            "displayName": "Asset Transfer",
            "functions": [
                {
                    "id": 912,
                    "name": "",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                },
                {
                    "id": 913,
                    "name": "modify",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                }
            ],
            "states": [ 
                 {
                      "name": "Created",
                      "displayName": "Created",
                      "style" : "Success"
                 },
                 {
                      "name": "Terminated",
                      "displayName": "Terminated",
                      "style" : "Failure"
                 }
            ],
            "properties": [
                {
                    "id": 879,
                    "name": "Description",
                    "type": {
                        "name": "string"
                     }
                },
                {
                    "id": 880,
                    "name": "Price",
                    "type": {
                        "name": "int"
                     }
                }
            ]
        }
    ],
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation":
        {
            "states" :
            [
                {
                    "Name": "BuyerAccepted",
                    "Transitions": [
                        {
                            "DisplayName": "Accept",
                            "AllowedRoles": [ ],
                            "AllowedInstanceRoles": [ "InstanceOwner" ],
                            "Function": "Accept",
                            "NextStates": [ "SellerAccepted" ]
                        }
                    ]
                }
            ]
        }
}
```

### <a name="event-message-role-assignment"></a>Gebeurtenis bericht: roltoewijzing

Bevat informatie wanneer aan een gebruiker een rol is toegewezen in Workbench, zoals wie de roltoewijzing en de naam van de rol en de bijbehorende toepassing heeft uitgevoerd.

| Naam | Beschrijving |
|------|-------------|
| eventName | **RoleAssignment** |
| applicationId | De unieke id voor de toepassing in azure Block Chain workbench |
| applicationName | De naam van de toepassing |
| applicationDisplayName | Weergave naam van toepassing |
| applicationVersion | Toepassings versie |
| applicationRole        | Informatie [over de toepassingsrol](#roleassignment-application-role) |
| Toewijzer               | Informatie over de [Toewijzer](#roleassignment-assigner) |
| toegewezen gebruiker               | Informatie over de [toegewezen persoon](#roleassignment-assignee) |
| connectionId           | De unieke id voor de verbinding |
| messageSchemaVersion   | Schema versie berichten |
| berichtnaam            | **EventMessage** |
| additionalInformation  | Aanvullende informatie |

#### <a name="roleassignment-application-role"></a>Toepassingsrol RoleAssignment

| Naam | Beschrijving |
|------|-------------|
| id | De unieke id voor de toepassingsrol binnen Azure Block Chain workbench |
| naam | Naam van de toepassingsrol |

#### <a name="roleassignment-assigner"></a>RoleAssignment-Toewijzer

| Naam | Beschrijving |
|------|-------------|
| id | De unieke id van de gebruiker in azure Block Chain workbench |
| type | Type van de Toewijzer |
| chainIdentifier | De unieke id van de gebruiker in het groot boek |

#### <a name="roleassignment-assignee"></a>Toegewezen RoleAssignment

| Naam | Beschrijving |
|------|-------------|
| id | De unieke id van de gebruiker in azure Block Chain workbench |
| type | Het type van de toegewezen gebruiker |
| chainIdentifier | De unieke id van de gebruiker in het groot boek |

Voor beeld van een *EventMessage-RoleAssignment* van Block Chain Workbench:

``` json
{
    "eventName": "RoleAssignment",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": "1.0",
    "applicationRole": {
        "id": 134,
        "name": "Buyer"
    },
    "assigner": {
        "id": 1,
        "type": null,
        "chainIdentifier": "0xeFFC7766d38aC862d79706c3C5CEEf089564ffff"
    },
    "assignee": {
        "id": 3,
        "type": null,
        "chainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
    },
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

## <a name="next-steps"></a>Volgende stappen

- [Integratiepatronen voor slimme contracten](integration-patterns.md)
