---
title: Meta gegevens van de configuratie van Azure Block Chain workbench
description: Overzicht van de meta gegevens van Azure Block Chain Workbench Preview voor toepassings configuratie.
ms.date: 12/09/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: f0ba19bf1d7fdf05014ac199fae9392b5c3249d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87073076"
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Configuratie referentie voor Azure Block Chain workbench

Azure Block Chain Workbench-toepassingen zijn multi-party werk stromen die zijn gedefinieerd door de meta gegevens van de configuratie en de code van een Smart contract. Configuratiemetagegevens definiëren de werkstromen van hoog niveau en het interactiemodel van de blockchain-toepassing. Met slimme contracten wordt de bedrijfs logica van de Block Chain-toepassing gedefinieerd. Workbench maakt gebruik van configuratie-en slimme contract code voor het genereren van gebruikers ervaringen voor Block Chain toepassingen.

Met de meta gegevens van de configuratie wordt de volgende informatie opgegeven voor elke Block Chain-toepassing:

* Naam en beschrijving van de Block Chain-toepassing
* Unieke rollen voor gebruikers die kunnen reageren op of deel nemen aan de Block Chain-toepassing
* Een of meer werk stromen. Elke werk stroom fungeert als een status computer om de stroom van de bedrijfs logica te beheren. Werk stromen kunnen onafhankelijk zijn of onderling met elkaar communiceren.

Met elke gedefinieerde werk stroom geeft u het volgende op:

* Naam en beschrijving van de werk stroom
* Statussen van de werk stroom.  Elke status is een fase in de controle stroom van de bedrijfs logica. 
* Acties voor de overgang naar de volgende status
* Gebruikers rollen die zijn toegestaan om elke actie te initiëren
* Slimme contracten die bedrijfs logica vertegenwoordigen in code bestanden

## <a name="application"></a>Toepassing

Een Block Chain-toepassing bevat meta gegevens van de configuratie, werk stromen en gebruikers rollen die kunnen reageren op of deel nemen aan de toepassing.

| Veld | Beschrijving | Vereist |
|-------|-------------|:--------:|
| ApplicationName | Unieke toepassings naam. Het bijbehorende slimme contract moet dezelfde **ApplicationName** gebruiken voor de toepasselijke contract klasse.  | Ja |
| DisplayName | Beschrijvende weergave naam van de toepassing. | Ja |
| Beschrijving | Beschrijving van de toepassing. | Nee |
| ApplicationRoles | Verzameling van [ApplicationRoles](#application-roles). Gebruikers rollen die kunnen reageren op of deel nemen aan de toepassing.  | Ja |
| Werkstromen | Verzameling  [werk stromen](#workflows). Elke werk stroom fungeert als een status computer om de stroom van de bedrijfs logica te beheren. | Ja |

Zie het voor beeld van een [configuratie bestand](#configuration-file-example)voor een voor beeld.

## <a name="workflows"></a>Werkstromen

De bedrijfs logica van een toepassing kan worden gemodelleerd als een status machine waarbij het uitvoeren van een actie de stroom van de bedrijfs logica overneemt van de ene status naar de andere. Een werk stroom is een verzameling van dergelijke statussen en acties. Elke werk stroom bestaat uit een of meer slimme contracten die de bedrijfs logica in code bestanden vertegenwoordigen. Een uitvoerbaar contract is een exemplaar van een werk stroom.

| Veld | Beschrijving | Vereist | Max. lengte |
|-------|-------------|:--------:|-----------:|
| Name | Unieke werk stroom naam. Het bijbehorende slimme contract moet dezelfde **naam** gebruiken voor de toepasselijke contract klasse. | Ja | 50 |
| DisplayName | Beschrijvende weergave naam van de werk stroom. | Ja | 255 |
| Beschrijving | Beschrijving van de werk stroom. | Nee | 255 |
| Initia tors | Verzameling van [ApplicationRoles](#application-roles). Rollen die zijn toegewezen aan gebruikers die zijn gemachtigd om contracten te maken in de werk stroom. | Ja | |
| StartState | De naam van de begin status van de werk stroom. | Ja | |
| Eigenschappen | Verzameling [id's](#identifiers). Hiermee worden gegevens weer gegeven die kunnen worden gelezen uit een keten of visualiseren in een hulp programma voor gebruikers ervaring. | Ja | |
| Constructor | Definieert de invoer parameters voor het maken van een exemplaar van de werk stroom. | Ja | |
| Functions | Een verzameling [functies](#functions) die in de werk stroom kunnen worden uitgevoerd. | Ja | |
| Staten | Een verzameling werk stroom [statussen](#states). | Ja | |

Zie het voor beeld van een [configuratie bestand](#configuration-file-example)voor een voor beeld.

## <a name="type"></a>Type

Ondersteunde gegevens typen.

| Type | Beschrijving |
|-------|-------------|
| adres  | Block Chain adres type, zoals *contracten* of *gebruikers*. |
| matrix    | Eén niveau matrix van het type geheel getal, BOOL, Money of time. Matrices kunnen statisch of dynamisch zijn. Gebruik **element type** om het gegevens type van de elementen in de matrix op te geven. Zie [voorbeeld configuratie](#example-configuration-of-type-array). |
| booleaans     | Boolean-gegevens type. |
| aanbesteding | Adres van het type contract. |
| vaste     | Geïnventariseerde set met benoemde waarden. Wanneer u het Enum-type gebruikt, geeft u ook een lijst met EnumValues op. Elke waarde is beperkt tot 255 tekens. Geldige tekens zijn onder andere hoofd letters (A-Z, a-z) en cijfers (0-9). Zie [voorbeeld configuratie en gebruik in volheid](#example-configuration-of-type-enum). |
| int      | Gegevens type integer. |
| money    | Money-gegevens type. |
| staat    | Werk stroom status. |
| tekenreeks  | Tekenreeksgegevenstype. Maxi maal 4000 tekens. Zie [voorbeeld configuratie](#example-configuration-of-type-string). |
| gebruiker     | Adres van het type gebruiker. |
| tijd     | Tijd gegevens type. |
|`[ Application Role Name ]`| Een naam die is opgegeven in de toepassingsrol. Hiermee worden gebruikers beperkt tot het type rol. |

### <a name="example-configuration-of-type-array"></a>Voorbeeld configuratie van het type matrix

```json
{
  "Name": "Quotes",
  "Description": "Market quotes",
  "DisplayName": "Quotes",
  "Type": {
    "Name": "array",
    "ElementType": {
        "Name": "int"
    }
  }
}
```

#### <a name="using-a-property-of-type-array"></a>Een eigenschap van het type matrix gebruiken

Als u een eigenschap definieert als type matrix in de configuratie, moet u een expliciete Get-functie gebruiken om de open bare eigenschap van het matrix type in volheid te retour neren. Bijvoorbeeld:

```
function GetQuotes() public constant returns (int[]) {
     return Quotes;
}
```

### <a name="example-configuration-of-type-string"></a>Voorbeeld configuratie van het type teken reeks

``` json
{
  "Name": "description",
  "Description": "Descriptive text",
  "DisplayName": "Description",
  "Type": {
    "Name": "string"
  }
}
```

### <a name="example-configuration-of-type-enum"></a>Voorbeeld configuratie van het type Enum

``` json
{
  "Name": "PropertyType",
  "DisplayName": "Property Type",
  "Description": "The type of the property",
  "Type": {
    "Name": "enum",
    "EnumValues": ["House", "Townhouse", "Condo", "Land"]
  }
}
```

#### <a name="using-enumeration-type-in-solidity"></a>Opsommings type gebruiken in volheid

Zodra een Enum is gedefinieerd in de configuratie, kunt u opsommings typen gebruiken in volheid. U kunt bijvoorbeeld een enum met de naam PropertyTypeEnum definiëren.

```
enum PropertyTypeEnum {House, Townhouse, Condo, Land} PropertyTypeEnum public PropertyType; 
```

De lijst met teken reeksen moet overeenkomen tussen de configuratie en het slimme contract om geldige en consistente declaraties in Block Chain Workbench te zijn.

Voor beeld van toewijzing:

```
PropertyType = PropertyTypeEnum.Townhouse;
```

Voor beeld van functie parameter: 

``` 
function AssetTransfer(string description, uint256 price, PropertyTypeEnum propertyType) public
{
    InstanceOwner = msg.sender;
    AskingPrice = price;
    Description = description;
    PropertyType = propertyType;
    State = StateType.Active;
    ContractCreated();
}

```

## <a name="constructor"></a>Constructor

Definieert de invoer parameters voor een exemplaar van een werk stroom.

| Veld | Beschrijving | Vereist |
|-------|-------------|:--------:|
| Parameters | Verzameling van [id's](#identifiers) die vereist zijn om een slim contract te initiëren. | Ja |

### <a name="constructor-example"></a>Voor beeld van constructor

``` json
{
  "Parameters": [
    {
      "Name": "description",
      "Description": "The description of this asset",
      "DisplayName": "Description",
      "Type": {
        "Name": "string"
      }
    },
    {
      "Name": "price",
      "Description": "The price of this asset",
      "DisplayName": "Price",
      "Type": {
        "Name": "money"
      }
    }
  ]
}
```

## <a name="functions"></a>Functions

Definieert functies die kunnen worden uitgevoerd in de werk stroom.

| Veld | Beschrijving | Vereist | Max. lengte |
|-------|-------------|:--------:|-----------:|
| Name | De unieke naam van de functie. Het bijbehorende slimme contract moet dezelfde **naam** gebruiken voor de desbetreffende functie. | Ja | 50 |
| DisplayName | Beschrijvende weergave naam van de functie. | Ja | 255 |
| Beschrijving | Beschrijving van de functie | Nee | 255 |
| Parameters | Verzameling van [id's](#identifiers) die overeenkomen met de para meters van de functie. | Ja | |

### <a name="functions-example"></a>Functions-voor beeld

``` json
"Functions": [
  {
    "Name": "Modify",
    "DisplayName": "Modify",
    "Description": "Modify the description/price attributes of this asset transfer instance",
    "Parameters": [
      {
        "Name": "description",
        "Description": "The new description of the asset",
        "DisplayName": "Description",
        "Type": {
          "Name": "string"
        }
      },
      {
        "Name": "price",
        "Description": "The new price of the asset",
        "DisplayName": "Price",
        "Type": {
          "Name": "money"
        }
      }
    ]
  },
  {
    "Name": "Terminate",
    "DisplayName": "Terminate",
    "Description": "Used to cancel this particular instance of asset transfer",
    "Parameters": []
  }
]

```

## <a name="states"></a>Staten

Een verzameling unieke statussen binnen een werk stroom. Elke status legt een stap vast in de controle stroom van de bedrijfs logica. 

| Veld | Beschrijving | Vereist | Max. lengte |
|-------|-------------|:--------:|-----------:|
| Name | De unieke naam van de status. Het bijbehorende slimme contract moet dezelfde **naam** gebruiken voor de betreffende status. | Ja | 50 |
| DisplayName | Beschrijvende weergave naam van de status. | Ja | 255 |
| Beschrijving | De beschrijving van de status. | Nee | 255 |
| PercentComplete | Een integer-waarde die wordt weer gegeven in de Block Chain Workbench-gebruikers interface om de voortgang in de bedrijfs logica controle stroom weer te geven. | Ja | |
| Stijl | Visuele hint die aangeeft of de status de status geslaagd of mislukt aangeeft. Er zijn twee geldige waarden: `Success` of `Failure` . | Ja | |
| Overgangen | Verzameling van beschik bare [overgangen](#transitions) van de huidige status naar de volgende set statussen. | Nee | |

### <a name="states-example"></a>Voor beeld van statussen

``` json
"States": [
    {
      "Name": "Active",
      "DisplayName": "Active",
      "Description": "The initial state of the asset transfer workflow",
      "PercentComplete": 20,
      "Style": "Success",
      "Transitions": [
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Cancels this instance of asset transfer",
          "Function": "Terminate",
          "NextStates": [ "Terminated" ],
          "DisplayName": "Terminate Offer"
        },
        {
          "AllowedRoles": [ "Buyer" ],
          "AllowedInstanceRoles": [],
          "Description": "Make an offer for this asset",
          "Function": "MakeOffer",
          "NextStates": [ "OfferPlaced" ],
          "DisplayName": "Make Offer"
        },
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Modify attributes of this asset transfer instance",
          "Function": "Modify",
          "NextStates": [ "Active" ],
          "DisplayName": "Modify"
        }
      ]
    },
    {
      "Name": "Accepted",
      "DisplayName": "Accepted",
      "Description": "Asset transfer process is complete",
      "PercentComplete": 100,
      "Style": "Success",
      "Transitions": []
    },
    {
      "Name": "Terminated",
      "DisplayName": "Terminated",
      "Description": "Asset transfer has been canceled",
      "PercentComplete": 100,
      "Style": "Failure",
      "Transitions": []
    }
  ]
```

## <a name="transitions"></a>Overgangen

Beschik bare acties voor de volgende status. Een of meer gebruikers rollen kunnen een actie uitvoeren op elke status, waarbij een actie een status kan overzetten naar een andere status in de werk stroom. 

| Veld | Beschrijving | Vereist |
|-------|-------------|:--------:|
| AllowedRoles | Lijst met toepassings rollen die de overgang kunnen initiëren. Alle gebruikers van de opgegeven rol kunnen de actie mogelijk uitvoeren. | Nee |
| AllowedInstanceRoles | Een lijst met gebruikers rollen die in het slimme contract zijn gedeeld of opgegeven om de overgang te initiëren. Exemplaar rollen worden gedefinieerd in **Eigenschappen** binnen werk stromen. AllowedInstanceRoles vertegenwoordigen een gebruiker die deelneemt aan een exemplaar van een slim contract. AllowedInstanceRoles biedt u de mogelijkheid om een actie te beperken voor een gebruikersrol in een contract exemplaar.  Zo wilt u misschien alleen toestaan dat de gebruiker die het contract heeft gemaakt (InstanceOwner), in plaats van alle gebruikers in het type rol (eigenaar) kunnen beëindigen als u de rol hebt opgegeven in AllowedRoles. | Nee |
| DisplayName | Beschrijvende weergave naam van de overgang. | Ja |
| Beschrijving | Beschrijving van de overgang. | Nee |
| Functie | De naam van de functie om de overgang te initiëren. | Ja |
| NextStates | Een verzameling van mogelijke volgende statussen na een geslaagde overgang. | Ja |

### <a name="transitions-example"></a>Overgangen-voor beeld

``` json
"Transitions": [
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Cancels this instance of asset transfer",
    "Function": "Terminate",
    "NextStates": [ "Terminated" ],
    "DisplayName": "Terminate Offer"
  },
  {
    "AllowedRoles": [ "Buyer" ],
    "AllowedInstanceRoles": [],
    "Description": "Make an offer for this asset",
    "Function": "MakeOffer",
    "NextStates": [ "OfferPlaced" ],
    "DisplayName": "Make Offer"
  },
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Modify attributes of this asset transfer instance",
    "Function": "Modify",
    "NextStates": [ "Active" ],
    "DisplayName": "Modify"
  }
]

```

## <a name="application-roles"></a>Toepassingsrollen

Met toepassings rollen wordt een set rollen gedefinieerd die kan worden toegewezen aan gebruikers die binnen de toepassing willen handelen of deel nemen. Toepassings rollen kunnen worden gebruikt om acties en deelname binnen de Block Chain-toepassing en de bijbehorende werk stromen te beperken. 

| Veld | Beschrijving | Vereist | Max. lengte |
|-------|-------------|:--------:|-----------:|
| Name | De unieke naam van de toepassingsrol. Het bijbehorende slimme contract moet dezelfde **naam** gebruiken voor de toepasselijke rol. Namen van basis typen zijn gereserveerd. U kunt geen toepassingsrol een naam met dezelfde naam als het [type](#type)| Ja | 50 |
| Beschrijving | Beschrijving van de toepassingsrol. | Nee | 255 |

### <a name="application-roles-example"></a>Voor beeld van toepassings rollen

``` json
"ApplicationRoles": [
  {
    "Name": "Appraiser",
    "Description": "User that signs off on the asset price"
  },
  {
    "Name": "Buyer",
    "Description": "User that places an offer on an asset"
  }
]
```
## <a name="identifiers"></a>Id's

Id's vertegenwoordigen een verzameling gegevens die wordt gebruikt voor het beschrijven van werk stroom eigenschappen, constructors en Function-para meters. 

| Veld | Beschrijving | Vereist | Max. lengte |
|-------|-------------|:--------:|-----------:|
| Name | De unieke naam van de eigenschap of para meter. Het bijbehorende slimme contract moet dezelfde **naam** gebruiken voor de toepasselijke eigenschap of para meter. | Ja | 50 |
| DisplayName | Beschrijvende weergave naam voor de eigenschap of para meter. | Ja | 255 |
| Beschrijving | Beschrijving van de eigenschap of para meter. | Nee | 255 |
| Type | [Gegevens type](#type)van eigenschap. | Ja |

### <a name="identifiers-example"></a>Voor beelden van id's

``` json
"Properties": [
  {
    "Name": "State",
    "DisplayName": "State",
    "Description": "Holds the state of the contract",
    "Type": {
      "Name": "state"
    }
  },
  {
    "Name": "Description",
    "DisplayName": "Description",
    "Description": "Describes the asset being sold",
    "Type": {
      "Name": "string"
    }
  }
]
```

## <a name="configuration-file-example"></a>Voor beeld van configuratie bestand

Asset overboeking is een slim contract scenario voor het kopen en verkopen van hoogwaardige assets, waarvoor een inspecteur en beoordeling nodig zijn. Verkopers kunnen hun activa vermelden door een Smart-contract voor Asset-overdracht te instantiëren. Kopers kunnen aanbiedingen maken door een actie uit te voeren op het slimme contract en andere partijen kunnen acties ondernemen om de activa te controleren of te beoordelen. Zodra de Asset is gemarkeerd en geoordeeld, wordt de verkoop door de koper en de verkoper opnieuw bevestigd voordat het contract is ingesteld op voltooid. Op elk moment in het proces hebben alle deel nemers inzicht in de status van het contract wanneer het wordt bijgewerkt. 

Zie voor [beeld van Asset-overdracht voor Azure Block Chain Workbench](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer) voor meer informatie, waaronder de code bestanden

Het volgende configuratie bestand is voor het voor beeld van een Asset-overdracht:

``` json
{
  "ApplicationName": "AssetTransfer",
  "DisplayName": "Asset Transfer",
  "Description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
  "ApplicationRoles": [
    {
      "Name": "Appraiser",
      "Description": "User that signs off on the asset price"
    },
    {
      "Name": "Buyer",
      "Description": "User that places an offer on an asset"
    },
    {
      "Name": "Inspector",
      "Description": "User that inspects the asset and signs off on inspection"
    },
    {
      "Name": "Owner",
      "Description": "User that signs off on the asset price"
    }
  ],
  "Workflows": [
    {
      "Name": "AssetTransfer",
      "DisplayName": "Asset Transfer",
      "Description": "Handles the business logic for the asset transfer scenario",
      "Initiators": [ "Owner" ],
      "StartState":  "Active",
      "Properties": [
        {
          "Name": "State",
          "DisplayName": "State",
          "Description": "Holds the state of the contract",
          "Type": {
            "Name": "state"
          }
        },
        {
          "Name": "Description",
          "DisplayName": "Description",
          "Description": "Describes the asset being sold",
          "Type": {
            "Name": "string"
          }
        },
        {
          "Name": "AskingPrice",
          "DisplayName": "Asking Price",
          "Description": "The asking price for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "OfferPrice",
          "DisplayName": "Offer Price",
          "Description": "The price being offered for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "InstanceAppraiser",
          "DisplayName": "Instance Appraiser",
          "Description": "The user that appraises the asset",
          "Type": {
            "Name": "Appraiser"
          }
        },
        {
          "Name": "InstanceBuyer",
          "DisplayName": "Instance Buyer",
          "Description": "The user that places an offer for this asset",
          "Type": {
            "Name": "Buyer"
          }
        },
        {
          "Name": "InstanceInspector",
          "DisplayName": "Instance Inspector",
          "Description": "The user that inspects this asset",
          "Type": {
            "Name": "Inspector"
          }
        },
        {
          "Name": "InstanceOwner",
          "DisplayName": "Instance Owner",
          "Description": "The seller of this particular asset",
          "Type": {
            "Name": "Owner"
          }
        }
      ],
      "Constructor": {
        "Parameters": [
          {
            "Name": "description",
            "Description": "The description of this asset",
            "DisplayName": "Description",
            "Type": {
              "Name": "string"
            }
          },
          {
            "Name": "price",
            "Description": "The price of this asset",
            "DisplayName": "Price",
            "Type": {
              "Name": "money"
            }
          }
        ]
      },
      "Functions": [
        {
          "Name": "Modify",
          "DisplayName": "Modify",
          "Description": "Modify the description/price attributes of this asset transfer instance",
          "Parameters": [
            {
              "Name": "description",
              "Description": "The new description of the asset",
              "DisplayName": "Description",
              "Type": {
                "Name": "string"
              }
            },
            {
              "Name": "price",
              "Description": "The new price of the asset",
              "DisplayName": "Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Terminate",
          "DisplayName": "Terminate",
          "Description": "Used to cancel this particular instance of asset transfer",
          "Parameters": []
        },
        {
          "Name": "MakeOffer",
          "DisplayName": "Make Offer",
          "Description": "Place an offer for this asset",
          "Parameters": [
            {
              "Name": "inspector",
              "Description": "Specify a user to inspect this asset",
              "DisplayName": "Inspector",
              "Type": {
                "Name": "Inspector"
              }
            },
            {
              "Name": "appraiser",
              "Description": "Specify a user to appraise this asset",
              "DisplayName": "Appraiser",
              "Type": {
                "Name": "Appraiser"
              }
            },
            {
              "Name": "offerPrice",
              "Description": "Specify your offer price for this asset",
              "DisplayName": "Offer Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Reject",
          "DisplayName": "Reject",
          "Description": "Reject the user's offer",
          "Parameters": []
        },
        {
          "Name": "AcceptOffer",
          "DisplayName": "Accept Offer",
          "Description": "Accept the user's offer",
          "Parameters": []
        },
        {
          "Name": "RescindOffer",
          "DisplayName": "Rescind Offer",
          "Description": "Rescind your placed offer",
          "Parameters": []
        },
        {
          "Name": "ModifyOffer",
          "DisplayName": "Modify Offer",
          "Description": "Modify the price of your placed offer",
          "Parameters": [
            {
              "Name": "offerPrice",
              "DisplayName": "Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Accept",
          "DisplayName": "Accept",
          "Description": "Accept the inspection/appraisal results",
          "Parameters": []
        },
        {
          "Name": "MarkInspected",
          "DisplayName": "Mark Inspected",
          "Description": "Mark the asset as inspected",
          "Parameters": []
        },
        {
          "Name": "MarkAppraised",
          "DisplayName": "Mark Appraised",
          "Description": "Mark the asset as appraised",
          "Parameters": []
        }
      ],
      "States": [
        {
          "Name": "Active",
          "DisplayName": "Active",
          "Description": "The initial state of the asset transfer workflow",
          "PercentComplete": 20,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancels this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate Offer"
            },
            {
              "AllowedRoles": [ "Buyer" ],
              "AllowedInstanceRoles": [],
              "Description": "Make an offer for this asset",
              "Function": "MakeOffer",
              "NextStates": [ "OfferPlaced" ],
              "DisplayName": "Make Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Modify attributes of this asset transfer instance",
              "Function": "Modify",
              "NextStates": [ "Active" ],
              "DisplayName": "Modify"
            }
          ]
        },
        {
          "Name": "OfferPlaced",
          "DisplayName": "Offer Placed",
          "Description": "Offer has been placed for the asset",
          "PercentComplete": 30,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Accept the proposed offer for the asset",
              "Function": "AcceptOffer",
              "NextStates": [ "PendingInspection" ],
              "DisplayName": "Accept Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you previously placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Modify the price that you specified for your offer",
              "Function": "ModifyOffer",
              "NextStates": [ "OfferPlaced" ],
              "DisplayName": "Modify Offer"
            }
          ]
        },
        {
          "Name": "PendingInspection",
          "DisplayName": "Pending Inspection",
          "Description": "Asset is pending inspection",
          "PercentComplete": 40,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark this asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "Inspected" ],
              "DisplayName": "Mark Inspected"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "Appraised" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Inspected",
          "DisplayName": "Inspected",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Appraised",
          "DisplayName": "Appraised",
          "Description": "Asset has been appraised, now awaiting inspection",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark the asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Inspected"
            }
          ]
        },
        {
          "Name": "NotionalAcceptance",
          "DisplayName": "Notional Acceptance",
          "Description": "Asset has been inspected and appraised, awaiting final sign-off from buyer and seller",
          "PercentComplete": 50,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "SellerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "BuyerAccepted" ],
              "DisplayName": "BuyerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            }
          ]
        },
        {
          "Name": "BuyerAccepted",
          "DisplayName": "Buyer Accepted",
          "Description": "Buyer has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            }
          ]
        },
        {
          "Name": "SellerAccepted",
          "DisplayName": "Seller Accepted",
          "Description": "Seller has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "Accepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            }
          ]
        },
        {
          "Name": "Accepted",
          "DisplayName": "Accepted",
          "Description": "Asset transfer process is complete",
          "PercentComplete": 100,
          "Style": "Success",
          "Transitions": []
        },
        {
          "Name": "Terminated",
          "DisplayName": "Terminated",
          "Description": "Asset transfer has been canceled",
          "PercentComplete": 100,
          "Style": "Failure",
          "Transitions": []
        }
      ]
    }
  ]
}
```
## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naslaginformatie over de REST-API van Azure Blockchain Workbench](/rest/api/azure-blockchain-workbench)
