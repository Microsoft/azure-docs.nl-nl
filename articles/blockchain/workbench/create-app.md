---
title: Een blockchain-toepassing maken - Azure Blockchain Workbench
description: Zelfstudie over het maken van een blockchain-toepassing voor Azure Blockchain Workbench (preview-versie).
ms.date: 08/24/2020
ms.topic: tutorial
ms.reviewer: ravastra
ms.openlocfilehash: add790a069d2e0ea66d84bbd632825cf9331fd38
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88784025"
---
# <a name="tutorial-create-a-blockchain-application-for-azure-blockchain-workbench"></a>Zelfstudie: Een blockchain-toepassing maken voor Azure Blockchain Workbench

U kunt Azure Blockchain Workbench gebruiken om een blockchain-toepassing te maken waarmee werkstromen voor meerdere partijen worden gerepresenteerd die door configuratie en slimme-contractcode worden gedefinieerd.

U leert het volgende:

> [!div class="checklist"]
> * Een blockchain-toepassing configureren
> * Een bestand voor slimme-contractcode maken
> * Een blockchain-toepassing toevoegen aan Blockchain Workbench
> * Leden toevoegen aan de blockchain-toepassing

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* Een Blockchain Workbench-implementatie. Zie [Azure Blockchain Workbench implementeren](deploy.md) voor meer informatie over implementatie.
* Gebruikers van Azure Active Directory in de tenant die aan Blockchain Workbench is gekoppeld. Zie [Azure AD-gebruikers toevoegen in Azure Blockchain Workbench](manage-users.md#add-azure-ad-users) voor meer informatie.
* Een Blockchain Workbench-beheerdersaccount. Zie [Blockchain Workbench-beheerders in Azure Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).

## <a name="hello-blockchain"></a>Hallo Blockchain.

Laten we een eenvoudige toepassing bouwen waarin een aanvrager een aanvraag verzendt en een beantwoorder een antwoord naar de aanvrager verzendt.
Een aanvraag kan bijvoorbeeld zijn: "Hallo, hoe gaat het met u?", en het antwoord kan zijn: "Het gaat goed". Zowel de aanvraag als het antwoord worden opgenomen in de onderliggende blockchain.

Volg de stappen om de toepassingsbestanden te maken. U kunt [het voorbeeld ook downloaden op GitHub](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/hello-blockchain).

## <a name="configuration-file"></a>Configuratiebestand

Configuratiemetagegevens definiëren de werkstromen van hoog niveau en het interactiemodel van de blockchain-toepassing. Configuratiemetagegevens definiëren de werkstroomfasen en het interactiemodel van de blockchain-toepassing. Zie [Naslaginformatie over Azure Blockchain Workbench-configuratie](configuration.md) voor meer informatie over de inhoud van configuratiebestanden.

1. Maak in uw favoriete editor een bestand met de naam `HelloBlockchain.json`.
2. Voeg de volgende JSON toe om de configuratie van de blockchain-toepassing te definiëren.

    ``` json
    {
      "ApplicationName": "HelloBlockchain",
      "DisplayName": "Hello, Blockchain!",
      "Description": "A simple application to send request and get response",
      "ApplicationRoles": [
        {
          "Name": "Requestor",
          "Description": "A person sending a request."
        },
        {
          "Name": "Responder",
          "Description": "A person responding to a request"
        }
      ],
      "Workflows": [
        {
          "Name": "HelloBlockchain",
          "DisplayName": "Request Response",
          "Description": "A simple workflow to send a request and receive a response.",
          "Initiators": [ "Requestor" ],
          "StartState": "Request",
          "Properties": [
            {
              "Name": "State",
              "DisplayName": "State",
              "Description": "Holds the state of the contract.",
              "Type": {
                "Name": "state"
              }
            },
            {
              "Name": "Requestor",
              "DisplayName": "Requestor",
              "Description": "A person sending a request.",
              "Type": {
                "Name": "Requestor"
              }
            },
            {
              "Name": "Responder",
              "DisplayName": "Responder",
              "Description": "A person sending a response.",
              "Type": {
                "Name": "Responder"
              }
            },
            {
              "Name": "RequestMessage",
              "DisplayName": "Request Message",
              "Description": "A request message.",
              "Type": {
                "Name": "string"
              }
            },
            {
              "Name": "ResponseMessage",
              "DisplayName": "Response Message",
              "Description": "A response message.",
              "Type": {
                "Name": "string"
              }
            }
          ],
          "Constructor": {
            "Parameters": [
              {
                "Name": "message",
                "Description": "...",
                "DisplayName": "Request Message",
                "Type": {
                  "Name": "string"
                }
              }
            ]
          },
          "Functions": [
            {
              "Name": "SendRequest",
              "DisplayName": "Request",
              "Description": "...",
              "Parameters": [
                {
                  "Name": "requestMessage",
                  "Description": "...",
                  "DisplayName": "Request Message",
                  "Type": {
                    "Name": "string"
                  }
                }
              ]
            },
            {
              "Name": "SendResponse",
              "DisplayName": "Response",
              "Description": "...",
              "Parameters": [
                {
                  "Name": "responseMessage",
                  "Description": "...",
                  "DisplayName": "Response Message",
                  "Type": {
                    "Name": "string"
                  }
                }
              ]
            }
          ],
          "States": [
            {
              "Name": "Request",
              "DisplayName": "Request",
              "Description": "...",
              "PercentComplete": 50,
              "Value": 0,
              "Style": "Success",
              "Transitions": [
                {
                  "AllowedRoles": ["Responder"],
                  "AllowedInstanceRoles": [],
                  "Description": "...",
                  "Function": "SendResponse",
                  "NextStates": [ "Respond" ],
                  "DisplayName": "Send Response"
                }
              ]
            },
            {
              "Name": "Respond",
              "DisplayName": "Respond",
              "Description": "...",
              "PercentComplete": 90,
              "Value": 1,
              "Style": "Success",
              "Transitions": [
                {
                  "AllowedRoles": [],
                  "AllowedInstanceRoles": ["Requestor"],
                  "Description": "...",
                  "Function": "SendRequest",
                  "NextStates": [ "Request" ],
                  "DisplayName": "Send Request"
                }
              ]
            }
          ]
        }
      ]
    }
    ```

3. Sla het bestand `HelloBlockchain.json` op.

Het configuratiebestand heeft meerdere secties. De details over de secties zijn als volgt:

### <a name="application-metadata"></a>Toepassingsmetagegevens

het begin van het configuratiebestand bevat informatie over de toepassing, waaronder de naam en een beschrijving.

### <a name="application-roles"></a>Toepassingsrollen

In de sectie over toepassingsrollen worden de gebruikersrollen gedefinieerd met betrekking tot wie kan fungeren als of deelnemen binnen de blockchain-toepassing. U definieert een verzameling afzonderlijke rollen over functionaliteit. In het aanvraag-antwoordscenario wordt onderscheid gemaakt tussen de functionaliteit van een aanvrager als een entiteit die aanvragen produceert en een beantwoorder als een entiteit die antwoorden produceert.

### <a name="workflows"></a>Werkstromen

Met werkstromen worden een of meer fasen en acties van het contract gedefinieerd. In het aanvraag-antwoordscenario bestaat de eerste fase (status) van de werkstroom uit een aanvrager (rol) die een actie (overgang) onderneemt om een aanvraag (functie) te verzenden. In de volgende fase (status) voert een beantwoorder (rol) een actie (overgang) uit om een antwoord (functie) te verzenden. Bij de werkstroom van een toepassing kunnen eigenschappen, functies en statussen betrokken zijn die zijn vereist om de stroom van een contract te beschrijven.

## <a name="smart-contract-code-file"></a>Een bestand voor slimme-contractcode

Slimme contracten representeren de bedrijfslogica van de blockchain-toepassing. Momenteel biedt Blockchain Workbench ondersteuning voor Ethereum voor het blockchain-grootboek. Ethereum maakt gebruik van [Solidity](https://solidity.readthedocs.io) als programmeertaal voor het schrijven van zelf-afdwingende bedrijfslogica voor slimme contracten.

Slimme contracten in Solidity zijn soortgelijk aan klassen in object-georiënteerde talen. Elk contract bevat statussen en functies voor het implementeren van fasen en acties van het slimme contract.

Maak in uw favoriete editor een bestand met de naam `HelloBlockchain.sol`.

### <a name="version-pragma"></a>Versiepragma

Als best practice geeft u de versie van Solidity aan die u voor ogen hebt. Door de versie op te geven, vermijdt u compatibiliteitsproblemen met toekomstige versies van Solidity.

Voeg de volgende versiepragma toe aan de bovenkant van bestand `HelloBlockchain.sol` met de slimme-contractcode.

``` solidity
pragma solidity >=0.4.25 <0.6.0;
```

### <a name="configuration-and-smart-contract-code-relationship"></a>Relatie tussen configuratie en slimme-contractcode

Blockchain Workbench maakt gebruik van het configuratiebestand en het slimme-contractcodebestand om een blockchain-toepassing te maken. Er bestaat een relatie tussen wat er in de configuratie en de code in het slimme contract is gedefinieerd. Contractgegevens, functies, parameters en typen moeten met elkaar overeenkomen voordat de toepassing kan worden gemaakt. Blockchain Workbench verifieert de bestanden voordat de toepassing wordt gemaakt.

### <a name="contract"></a>Contract

Voeg de kop van het **contract** toe aan bestand `HelloBlockchain.sol` met de slimme-contractcode.

``` solidity
contract HelloBlockchain {
```

### <a name="state-variables"></a>Statusvariabelen

In de statusvariabelen worden de waarden van de status voor elk contractexemplaar opgeslagen. De statusvariabelen in uw contract moeten overeenkomen met de werkstroomeigenschappen die in het configuratiebestand zijn gedefinieerd.

Voeg de statusvariabelen toe aan uw contract in bestand `HelloBlockchain.sol` voor de slimme-contractcode.

``` solidity
    //Set of States
    enum StateType { Request, Respond}

    //List of properties
    StateType public  State;
    address public  Requestor;
    address public  Responder;

    string public RequestMessage;
    string public ResponseMessage;
```

### <a name="constructor"></a>Constructor

De constructor definieert invoerparameters voor een nieuw exemplaar van het slimme contract van een werkstroom. Vereiste parameters voor de constructor worden in het configuratiebestand gedefinieerd als constructorparameters. Het aantal parameters, de volgorde van de parameters en het type parameter moeten in beide bestanden met elkaar overeenkomen.

Schrijf, voordat u het contract gaat maken, in de constructorfunctie de bedrijfslogica die u wilt uitvoeren. Initialiseer bijvoorbeeld de statusvariabelen met beginwaarden.

Voeg de constructorfunctie toe aan uw contract in bestand `HelloBlockchain.sol` voor de slimme-contractcode.

``` solidity
    // constructor function
    constructor(string memory message) public
    {
        Requestor = msg.sender;
        RequestMessage = message;
        State = StateType.Request;
    }
```

### <a name="functions"></a>Functions

Functies zijn de uitvoerbare eenheden van de bedrijfslogica in een contract. Vereiste parameters voor de functie worden in het configuratiebestand gedefinieerd als functieparameters. Het aantal parameters, de volgorde van de parameters en het type parameter moeten in beide bestanden met elkaar overeenkomen. Functies zijn in het configuratiebestand gekoppeld aan overgangen in een Blockchain Workbench-werkstroom. Een overgang is een actie die wordt uitgevoerd om naar de volgende fase van de werkstroom van een toepassing over te gaan, zoals bepaald door het contract.

Schrijf de bedrijfslogica die u in de functie wilt uitvoeren. Bijvoorbeeld het wijzigen van de waarde van een variabele.

1. Voeg de volgende functies toe aan uw contract in bestand `HelloBlockchain.sol` voor de slimme-contractcode.

    ``` solidity
        // call this function to send a request
        function SendRequest(string memory requestMessage) public
        {
            if (Requestor != msg.sender)
            {
                revert();
            }
    
            RequestMessage = requestMessage;
            State = StateType.Request;
        }
    
        // call this function to send a response
        function SendResponse(string memory responseMessage) public
        {
            Responder = msg.sender;
    
            ResponseMessage = responseMessage;
            State = StateType.Respond;
        }
    }
    ```

2. Sla bestand `HelloBlockchain.sol` voor slimme-contractcode op.

## <a name="add-blockchain-application-to-blockchain-workbench"></a>Blockchain-toepassing toevoegen aan Blockchain Workbench

Als u een blockchain-toepassing aan Blockchain Workbench wilt toevoegen, uploadt u de bestanden voor de configuratie en het slimme contract voor het definiëren van de toepassing.

1. Ga in een webbrowser naar het webadres van Blockchain Workbench, bijvoorbeeld `https://{workbench URL}.azurewebsites.net/`. De webtoepassing wordt gemaakt als u Blockchain Workbench implementeert. Zie [Web-URL van Blockchain Workbench](deploy.md#blockchain-workbench-web-url) voor informatie over waar u het webadres van Blockchain Workbench kunt vinden
2. Meld u aan als een [Blockchain Workbench-beheerder](manage-users.md#manage-blockchain-workbench-administrators).
3. Selecteer **Toepassingen** > **Nieuw**. Het deelvenster **Nieuwe toepassing** wordt weergegeven.
4. Selecteer **Upload the contract configuration** > **Bladeren** om configuratiebestand **HalloBlockchain.json** te vinden dat u hebt gemaakt. Het configuratiebestand wordt automatisch gevalideerd. Selecteer de koppeling **Weergeven** om validatiefouten weer te geven. Corrigeer validatiefouten voordat u de toepassing implementeer.
5. Selecteer **Upload the contract code** > **Bladeren** om bestand **HalloBlockchain.sol** met de slimme-contractcode te vinden. Het codebestand wordt automatisch gevalideerd. Selecteer de koppeling **Weergeven** om validatiefouten weer te geven. Corrigeer validatiefouten voordat u de toepassing implementeer.
6. Selecteer **Implementeren** om de blockchain-toepassing te maken op basis van de configuratie- en slimme-contractbestanden.

Het implementeren van de blockchain-toepassing neemt enkele minuten in beslag. Als de implementatie is voltooid, wordt de nieuwe toepassing weergegeven in **Toepassingen**. 

> [!NOTE]
> U kunt blockchain-toepassingen ook maken met behulp van de [REST API voor Azure Blockchain Workbench](/rest/api/azure-blockchain-workbench).

## <a name="add-blockchain-application-members"></a>Leden voor blockchain-toepassing toevoegen

Voegen leden voor de toepassing aan uw toepassing toe om acties op contracten te initiëren en uit te voeren. Als u leden wilt toevoegen, moet u een [Blockchain-toepassingbeheerder](manage-users.md#manage-blockchain-workbench-administrators) zijn.

1. Selecteer **Toepassingen** > **Hallo Blockchain.** .
2. Het aantal leden dat is gekoppeld aan de toepassing wordt weergegeven in de rechterbovenhoek van de pagina. Voor nieuwe toepassingen is het aantal leden nul.
3. Selecteer de koppeling **leden** in de rechterbovenhoek van de pagina. De huidige ledenlijst voor de toepassing wordt weergegeven.
4. Selecteer **Leden toevoegen** in de ledenlijst.
5. Selecteer de naam van het lid dat u wilt toevoegen of voer deze in. Alleen Azure AD-gebruikers die voorkomen in de Blockchain Workbench-tenant staan vermeld. Als de gebruiker niet wordt gevonden, dient u [Azure AD-gebruikers toe te voegen](manage-users.md#add-azure-ad-users).
6. Selecteer de **Rol** voor het lid. Voor het eerste lid selecteert u **Aanvrager** als de rol.
7. Selecteer **Toevoegen** om het lid met de gekoppelde rol aan de toepassing toe te voegen.
8. Voeg een ander lid aan de toepassing toe met de rol **Beantwoorder**.

Zie [Gebruikers beheren in Azure Blockchain Workbench](manage-users.md) voor meer informatie over het beheren van gebruikers in Blockchain Workbench

## <a name="next-steps"></a>Volgende stappen

In dit instructieartikel hebt u een eenvoudige toepassing voor aanvragen en beantwoorden gemaakt. Als u wilt weten hoe u de toepassing kunt gebruiken, gaat u door naar het volgende instructieartikel.

> [!div class="nextstepaction"]
> [Een blockchain-toepassing gebruiken](use.md)
