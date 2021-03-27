---
title: Database weergaven voor Azure Block Chain workbench
description: Overzicht van beschik bare SQL Data Base-weer gaven voor Azure Block Chain Workbench preview.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: 6c7fba590c9c8951e1862c28cb24dbde52c8da8d
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625531"
---
# <a name="azure-blockchain-workbench-database-views"></a>Database weergaven voor Azure Block Chain workbench

Azure Block Chain Workbench preview levert gegevens van gedistribueerde groot boeken aan een externe SQL DB *-* data base. De buiten-keten database maakt het mogelijk om SQL en bestaande hulpprogram ma's, zoals [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms), te gebruiken om te communiceren met block Chain-gegevens.

Azure Block Chain Workbench biedt een reeks database weergaven die toegang bieden tot gegevens die handig zijn bij het uitvoeren van uw query's. Deze weer gaven zijn sterk genormaliseerd, zodat het eenvoudig is om snel aan de slag te gaan met het maken van rapporten, analyses en andere Block Chain-gegevens met bestaande hulpprogram ma's en zonder dat u de data base-mede werkers hoeft te trainen.

Deze sectie bevat een overzicht van de database weergaven en de gegevens die ze bevatten.

> [!NOTE]
> Direct gebruik van database tabellen die in de data base buiten deze weer gaven worden gevonden, wordt mogelijk niet ondersteund.
>

## <a name="vwapplication"></a>vwApplication

Deze weer gave bevat details over **toepassingen** die zijn geüpload naar Azure Block Chain Workbench.

| Naam                             | Type          | Kan null zijn | Description                                                                                                                                                                                                                                                   |
|----------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationID                    | int           | No          | Een unieke id voor de toepassing |
| ApplicationName                  | nvarchar (50)  | No          | De naam van de toepassing |
| ApplicationDescription           | nvarchar (255) | Yes         | Een beschrijving van de toepassing |
| ApplicationDisplayName           | nvarchar (255) | No          | De naam die moet worden weer gegeven in een gebruikers interface |
| ApplicationEnabled               | bit           | No          | Hiermee wordt aangegeven of de toepassing op dit moment is ingeschakeld<br /> **Opmerking:** Hoewel een toepassing kan worden weer gegeven als uitgeschakeld in de data base, blijven gerelateerde contracten op de Block chain en gegevens over die contracten in de-data base. |
| UploadedDtTm                     | DATETIME2 (7)  | No          | De datum en tijd waarop een contract is geüpload |
| UploadedByUserId                 | int           | No          | De ID van de gebruiker die de toepassing heeft geüpload |
| UploadedByUserExternalId         | nvarchar (255) | No          | De externe ID voor de gebruiker die de toepassing heeft geüpload. Deze ID is standaard de gebruiker van de Azure Active Directory voor het consortium.                                                                                                |
| UploadedByUserProvisioningStatus | int           | No          | Hiermee wordt de huidige status van het inrichtings proces voor de gebruiker geïdentificeerd. Mogelijke waarden zijn: <br />0: gebruiker is gemaakt door de API<br />1: er is een sleutel aan de gebruiker in de data base gekoppeld<br />2: de gebruiker is volledig ingericht                         |
| UploadedByUserFirstName          | nvarchar (50)  | Yes         | De voor naam van de gebruiker die het contract heeft geüpload |
| UploadedByUserLastName           | nvarchar (50)  | Yes         | De achternaam van de gebruiker die het contract heeft geüpload |
| UploadedByUserEmailAddress       | nvarchar (255) | Yes         | Het e-mail adres van de gebruiker die het contract heeft geüpload |

## <a name="vwapplicationrole"></a>vwApplicationRole

Deze weer gave bevat details over de rollen die zijn gedefinieerd in azure Block Chain Workbench-toepassingen.

In een *Asset overboeking* -toepassing kunnen bijvoorbeeld rollen zoals de rollen *koper* en *verkoper* worden gedefinieerd.

| Naam                   | Type             | Kan null zijn | Description                                       |
|------------------------|------------------|-------------|---------------------------------------------------|
| ApplicationID          | int              | No          | Een unieke id voor de toepassing           |
| ApplicationName        | nvarchar (50)     | No          | De naam van de toepassing                       |
| ApplicationDescription | nvarchar (255)    | Yes         | Een beschrijving van de toepassing                  |
| ApplicationDisplayName | nvarchar (255)    | No          | De naam die moet worden weer gegeven in een gebruikers interface      |
| RoleId                 | int              | No          | Een unieke id voor een rol in de toepassing |
| RoleName               | nvarchar50)      | No          | De naam van de rol                              |
| RoleDescription        | Beschrijving (255) | Yes         | Een beschrijving van de rol                         |

## <a name="vwapplicationroleuser"></a>vwApplicationRoleUser

Deze weer gave bevat details over de rollen die zijn gedefinieerd in azure Block Chain Workbench-toepassingen en de gebruikers die eraan zijn gekoppeld.

In een *Asset overboeking* -toepassing kan bijvoorbeeld *John Smith* worden gekoppeld aan de rol van de *koper* .

| Naam                       | Type          | Kan null zijn | Description                                                                                                                                                                                                                           |
|----------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationID              | int           | No          | Een unieke id voor de toepassing                                                                                                                                                                                               |
| ApplicationName            | nvarchar (50)  | No          | De naam van de toepassing                                                                                                                                                                                                           |
| ApplicationDescription     | nvarchar (255) | Yes         | Een beschrijving van de toepassing                                                                                                                                                                                                      |
| ApplicationDisplayName     | nvarchar (255) | No          | De naam die moet worden weer gegeven in een gebruikers interface                                                                                                                                                                                          |
| ApplicationRoleId          | int           | No          | Een unieke id voor een rol in de toepassing                                                                                                                                                                                     |
| ApplicationRoleName        | nvarchar50)   | No          | De naam van de rol                                                                                                                                                                                                                  |
| ApplicationRoleDescription | nvarchar (255) | Yes         | Een beschrijving van de rol                                                                                                                                                                                                             |
| UserId                     | int           | No          | De ID van de gebruiker die is gekoppeld aan de rol |
| UserExternalId             | nvarchar (255) | No          | De externe ID voor de gebruiker die aan de rol is gekoppeld. Deze ID is standaard de gebruiker van de Azure Active Directory voor het consortium.                                                                     |
| UserProvisioningStatus     | int           | No          | Hiermee wordt de huidige status van het inrichtings proces voor de gebruiker geïdentificeerd. Mogelijke waarden zijn: <br />0: gebruiker is gemaakt door de API<br />1: er is een sleutel aan de gebruiker in de data base gekoppeld<br />2: de gebruiker is volledig ingericht |
| UserFirstName              | nvarchar (50)  | Yes         | De voor naam van de gebruiker die aan de rol is gekoppeld |
| UserLastName               | nvarchar (255) | Yes         | De achternaam van de gebruiker die aan de rol is gekoppeld |
| UserEmailAddress           | nvarchar (255) | Yes         | Het e-mail adres van de gebruiker die aan de rol is gekoppeld |

## <a name="vwconnectionuser"></a>vwConnectionUser

Deze weer gave bevat details over de verbindingen die zijn gedefinieerd in azure Block Chain Workbench en de gebruikers die aan hen zijn gekoppeld. Voor elke verbinding bevat deze weer gave de volgende gegevens:

-   Gekoppelde grootboek gegevens
-   Bijbehorende gebruikers gegevens

| Naam                     | Type          | Kan null zijn | Description                                                                                                                                                                                                                           |
|--------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId             | int           | No          | De unieke id voor een verbinding in azure Block Chain workbench |
| ConnectionEndpointUrl    | nvarchar (50)  | No          | De eind punt-URL voor een verbinding |
| ConnectionFundingAccount | nvarchar (255) | Yes         | Het financierings account dat is gekoppeld aan een verbinding, indien van toepassing |
| LedgerId                 | int           | No          | De unieke id voor een groot boek |
| Grootboeknaam               | nvarchar (50)  | No          | De naam van het groot boek |
| LedgerDisplayName        | nvarchar (255) | No          | De naam van het groot boek dat moet worden weer gegeven in de gebruikers interface |
| UserId                   | int           | No          | De ID van de gebruiker die is gekoppeld aan de verbinding |
| UserExternalId           | nvarchar (255) | No          | De externe ID voor de gebruiker die is gekoppeld aan de verbinding. Deze ID is standaard de gebruiker van de Azure Active Directory voor het consortium. |
| UserProvisioningStatus   | int           | No          |Hiermee wordt de huidige status van het inrichtings proces voor de gebruiker geïdentificeerd. Mogelijke waarden zijn: <br />0: gebruiker is gemaakt door de API<br />1: er is een sleutel aan de gebruiker in de data base gekoppeld<br />2: de gebruiker is volledig ingericht |
| UserFirstName            | nvarchar (50)  | Yes         | De voor naam van de gebruiker die is gekoppeld aan de verbinding |
| UserLastName             | nvarchar (255) | Yes         | De achternaam van de gebruiker die is gekoppeld aan de verbinding |
| UserEmailAddress         | nvarchar (255) | Yes         | Het e-mail adres van de gebruiker die is gekoppeld aan de verbinding |

## <a name="vwcontract"></a>vwContract

Deze weer gave bevat details over geïmplementeerde contracten. Voor elk contract bevat deze weer gave de volgende gegevens:

-   Definitie van gekoppelde toepassing
-   Gekoppelde werk stroom definitie
-   Gekoppelde grootboek implementatie voor de functie
-   Details van de gebruiker die de actie heeft gestart
-   Details met betrekking tot het block Chain-blok en de trans actie

| Naam                                     | Type           | Kan null zijn | Description                                                                                                                                                                                                                                                   |
|------------------------------------------|----------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId                             | int            | No          | De unieke id voor een verbinding in azure Block Chain Workbench.                                                                                                                                                                                         |
| ConnectionEndpointUrl                    | nvarchar (50)   | No          | De eind punt-URL voor een verbinding |
| ConnectionFundingAccount                 | nvarchar (255)  | Yes         | Het financierings account dat is gekoppeld aan een verbinding, indien van toepassing |
| LedgerId                                 | int            | No          | De unieke id voor een groot boek |
| Grootboeknaam                               | nvarchar (50)   | No          | De naam van het groot boek |
| LedgerDisplayName                        | nvarchar (255)  | No          | De naam van het groot boek dat moet worden weer gegeven in de gebruikers interface |
| ApplicationID                            | int            | No          | Een unieke id voor de toepassing |
| ApplicationName                          | nvarchar (50)  | No          | De naam van de toepassing |
| ApplicationDisplayName                   | nvarchar (255) | No          | De naam die moet worden weer gegeven in een gebruikers interface |
| ApplicationEnabled                       | bit            | No          | Hiermee wordt aangegeven of de toepassing op dit moment is ingeschakeld.<br /> **Opmerking:** Hoewel een toepassing kan worden weer gegeven als uitgeschakeld in de data base, blijven gerelateerde contracten op de Block chain en gegevens over die contracten in de-data base.  |
| WorkflowId                               | int            | No          | Een unieke id voor de werk stroom die aan een contract is gekoppeld |
| WorkflowName                             | nvarchar (50)   | No          | De naam van de werk stroom die aan een contract is gekoppeld |
| WorkflowDisplayName                      | nvarchar (255)  | No          | De naam van de werk stroom die is gekoppeld aan het contract dat wordt weer gegeven in de gebruikers interface |
| WorkflowDescription                      | nvarchar (255)  | Yes         | De beschrijving van de werk stroom die aan een contract is gekoppeld |
| ContractCodeId                           | int            | No          | Een unieke id voor de contract code die is gekoppeld aan het contract |
| ContractFileName                         | int            | No          | De naam van het bestand met de code van het slimme contract voor deze werk stroom. |
| ContractUploadedDtTm                     | int            | No          | De datum en tijd waarop de contract code is geüpload |
| ContractId                               | int            | No          | De unieke id voor het contract |
| ContractProvisioningStatus               | int            | No          | Hiermee wordt de huidige status van het inrichtings proces voor het contract aangeduid. Mogelijke waarden zijn: <br />0: het contract is gemaakt door de API in de data base<br />1: het contract is naar het groot boek verzonden<br />2: het contract is geïmplementeerd in het groot boek<br />3 of 4: het contract kan niet in het groot boek worden geïmplementeerd<br />5-het contract is in het groot boek geïmplementeerd <br /><br />Met ingang van versie 1,5 worden de waarden 0 t/m 5 ondersteund. Voor achterwaartse compatibiliteit in de huidige versie is de weer gave **vwContractV0** beschikbaar die alleen waarden 0 t/m 2 ondersteunt. |
| ContractLedgerIdentifier                 | nvarchar (255) |             | Het e-mail adres van de gebruiker die het contract heeft geïmplementeerd |
| ContractDeployedByUserId                 | int            | No          | Een externe ID voor de gebruiker die het contract heeft geïmplementeerd. Deze ID is standaard de GUID die de Azure Active Directory-ID voor de gebruiker vertegenwoordigt.                                                                                                          |
| ContractDeployedByUserExternalId         | nvarchar (255)  | No          | Een externe ID voor de gebruiker die het contract heeft geïmplementeerd. Deze ID is standaard de GUID die de Azure Active Directory-ID voor de gebruiker vertegenwoordigt.                                                                                                         |
| ContractDeployedByUserProvisioningStatus | int            | No          | Hiermee wordt de huidige status van het inrichtings proces voor de gebruiker geïdentificeerd. Mogelijke waarden zijn: <br />0: gebruiker is gemaakt door de API<br />1: er is een sleutel aan de gebruiker in de data base gekoppeld <br />2: de gebruiker is volledig ingericht                     |
| ContractDeployedByUserFirstName          | nvarchar (50)   | Yes         | De voor naam van de gebruiker die het contract heeft geïmplementeerd |
| ContractDeployedByUserLastName           | nvarchar (255)  | Yes         | De achternaam van de gebruiker die het contract heeft geïmplementeerd |
| ContractDeployedByUserEmailAddress       | nvarchar (255)  | Yes         | Het e-mail adres van de gebruiker die het contract heeft geïmplementeerd |

## <a name="vwcontractaction"></a>vwContractAction

Deze weer gave vertegenwoordigt het meren deel van informatie met betrekking tot de acties die zijn uitgevoerd op contracten en is ontworpen om veelvoorkomende rapportage scenario's te vergemakkelijken. Voor elke uitgevoerde actie bevat deze weer gave de volgende gegevens:

-   Definitie van gekoppelde toepassing
-   Gekoppelde werk stroom definitie
-   Gekoppelde functie voor slimme contracten en parameter definitie
-   Gekoppelde grootboek implementatie voor de functie
-   Specifieke instantie waarden die zijn opgegeven voor para meters
-   Details van de gebruiker die de actie heeft gestart
-   Details met betrekking tot het block Chain-blok en de trans actie

| Naam                                     | Type          | Kan null zijn | Description                                                                                                                                                                                                                                                                                                    |
|------------------------------------------|---------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationID                            | int           | No          | Een unieke id voor de toepassing |
| ApplicationName                          | nvarchar (50)  | No          | De naam van de toepassing |
| ApplicationDisplayName                   | nvarchar (255) | No          | De naam die moet worden weer gegeven in een gebruikers interface |
| ApplicationEnabled                       | bit           | No          | Dit veld geeft aan of de toepassing momenteel is ingeschakeld. Opmerking: Hoewel een toepassing kan worden weer gegeven als uitgeschakeld in de data base, blijven gerelateerde contracten op de Block chain en gegevens over die contracten in de-data base.                                                  |
| WorkflowId                               | int           | No          | Een unieke id voor een werk stroom |
| WorkflowName                             | nvarchar (50)  | No          | De naam van de werk stroom |
| WorkflowDisplayName                      | nvarchar (255) | No          | De naam van de werk stroom die moet worden weer gegeven in een gebruikers interface |
| WorkflowDescription                      | nvarchar (255) | Yes         | De beschrijving van de werk stroom |
| ContractId                               | int           | No          | Een unieke id voor het contract |
| ContractProvisioningStatus               | int           | No          | Hiermee wordt de huidige status van het inrichtings proces voor het contract aangeduid. Mogelijke waarden zijn: <br />0: het contract is gemaakt door de API in de data base<br />1: het contract is naar het groot boek verzonden<br />2: het contract is geïmplementeerd in het groot boek<br />3 of 4: het contract kan niet in het groot boek worden geïmplementeerd<br />5-het contract is in het groot boek geïmplementeerd <br /><br />Met ingang van versie 1,5 worden de waarden 0 t/m 5 ondersteund. Voor achterwaartse compatibiliteit in de huidige versie is de weer gave **vwContractActionV0** beschikbaar die alleen waarden 0 t/m 2 ondersteunt. |
| ContractCodeId                           | int           | No          | Een unieke id voor de code-implementatie van het contract |
| ContractLedgerIdentifier                 | nvarchar (255) | Yes         | Een unieke id die is gekoppeld aan de geïmplementeerde versie van een slim contract voor een specifiek gedistribueerd groot boek. Bijvoorbeeld Ethereum. |
| ContractDeployedByUserId                 | int           | No          | De unieke id van de gebruiker die het contract heeft geïmplementeerd |
| ContractDeployedByUserFirstName          | nvarchar (50)  | Yes         | De voor naam van de gebruiker die het contract heeft geïmplementeerd |
| ContractDeployedByUserLastName           | nvarchar (255) | Yes         | De achternaam van de gebruiker die het contract heeft geïmplementeerd |
| ContractDeployedByUserExternalId         | nvarchar (255) | No          | De externe ID van de gebruiker die het contract heeft geïmplementeerd. Deze ID is standaard de GUID die de identiteit vertegenwoordigt in het consortium Azure Active Directory.                                                                                                                                                |
| ContractDeployedByUserEmailAddress       | nvarchar (255) | Yes         | Het e-mail adres van de gebruiker die het contract heeft geïmplementeerd |
| WorkflowFunctionId                       | int           | No          | Een unieke id voor een werk stroom functie |
| WorkflowFunctionName                     | nvarchar (50)  | No          | De naam van de functie |
| WorkflowFunctionDisplayName              | nvarchar (255) | No          | De naam van een functie die moet worden weer gegeven in de gebruikers interface |
| WorkflowFunctionDescription              | nvarchar (255) | No          | De beschrijving van de functie |
| ContractActionId                         | int           | No          | De unieke id voor een contract actie |
| ContractActionProvisioningStatus         | int           | No          | Hiermee wordt de huidige status van het inrichtings proces voor de contract actie aangegeven. Mogelijke waarden zijn: <br />0: de contract actie is gemaakt door de API in de data base<br />1: de contract actie is naar het groot boek verzonden<br />2: de contract actie is geïmplementeerd in het groot boek<br />3 of 4: het contract kan niet in het groot boek worden geïmplementeerd<br />5-het contract is in het groot boek geïmplementeerd <br /><br />Met ingang van versie 1,5 worden de waarden 0 t/m 5 ondersteund. Voor achterwaartse compatibiliteit in de huidige versie is de weer gave **vwContractActionV0** beschikbaar die alleen waarden 0 t/m 2 ondersteunt. |
| ContractActionTimestamp                  | datetime (2, 7) | No          | De tijds tempel van de contract actie |
| ContractActionExecutedByUserId           | int           | No          | De unieke id van de gebruiker die de contract actie heeft uitgevoerd |
| ContractActionExecutedByUserFirstName    | int           | Ja         | De voor naam van de gebruiker die de contract actie heeft uitgevoerd |
| ContractActionExecutedByUserLastName     | nvarchar (50)  | Yes         | De achternaam van de gebruiker die de contract actie heeft uitgevoerd |
| ContractActionExecutedByUserExternalId   | nvarchar (255) | Yes         | De externe ID van de gebruiker die de contract actie heeft uitgevoerd. Deze ID is standaard de GUID die de identiteit vertegenwoordigt in het consortium Azure Active Directory. |
| ContractActionExecutedByUserEmailAddress | nvarchar (255) | Yes         | Het e-mail adres van de gebruiker die de contract actie heeft uitgevoerd |
| WorkflowFunctionParameterId              | int           | No          | Een unieke id voor een para meter van de functie |
| WorkflowFunctionParameterName            | nvarchar (50)  | No          | De naam van een para meter van de functie |
| WorkflowFunctionParameterDisplayName     | nvarchar (255) | No          | De naam van een functie parameter die moet worden weer gegeven in de gebruikers interface |
| WorkflowFunctionParameterDataTypeId      | int           | No          | De unieke id voor het gegevens type dat is gekoppeld aan een werk stroom functie parameter |
| WorkflowParameterDataTypeName            | nvarchar (50)  | No          | De naam van een gegevens type dat is gekoppeld aan een werk stroom functie parameter |
| ContractActionParameterValue             | nvarchar (255) | No          | De waarde voor de para meter die is opgeslagen in het slimme contract |
| BlockHash                                | nvarchar (255) | Yes         | De hash van het blok |
| BlockNumber                              | int           | Ja         | Het nummer van het blok op het groot boek |
| BlockTimestamp                           | datetime (2, 7) | Yes         | Het tijds tempel van het blok |
| TransactionId                            | int           | No          | Een unieke id voor de trans actie |
| TransactionFrom                          | nvarchar (255) | Yes         | De partij die de trans actie heeft gestart |
| TransactionTo                            | nvarchar (255) | Yes         | De partij die heeft gehandeld met |
| TransactionHash                          | nvarchar (255) | Yes         | De hash van een trans actie |
| TransactionIsWorkbenchTransaction        | bit           | Yes         | Een bit waarmee wordt aangegeven of de trans actie een Azure Block Chain Workbench-trans actie is |
| TransactionProvisioningStatus            | int           | Ja         | Identificeert de huidige status van het inrichtings proces voor de trans actie. Mogelijke waarden zijn: <br />0: de trans actie is gemaakt door de API in de data base<br />1: de trans actie is naar het groot boek verzonden<br />2: de trans actie is in het groot boek geïmplementeerd                 |
| TransactionValue                         | decimaal (32, 2) | Yes         | De waarde van de trans actie |

## <a name="vwcontractproperty"></a>vwContractProperty

Deze weer gave vertegenwoordigt het meren deel van informatie met betrekking tot eigenschappen die zijn gekoppeld aan een contract en is ontworpen om veelvoorkomende rapportage scenario's te vergemakkelijken. Voor elke ondernomen eigenschap bevat deze weer gave de volgende gegevens:

-   Definitie van gekoppelde toepassing
-   Gekoppelde werk stroom definitie
-   Details van de gebruiker die de werk stroom heeft geïmplementeerd
-   Definitie van gekoppelde Smart-contract eigenschap
-   Specifieke instantie waarden voor eigenschappen
-   Details voor de eigenschap State van het contract

| Naam                               | Type          | Kan null zijn | Description                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationID                      | int           | No          | Een unieke id voor de toepassing |
| ApplicationName                    | nvarchar (50)  | No          | De naam van de toepassing |
| ApplicationDisplayName             | nvarchar (255) | No          | De naam die moet worden weer gegeven in een gebruikers interface |
| ApplicationEnabled                 | bit           | No          | Hiermee wordt aangegeven of de toepassing op dit moment is ingeschakeld.<br />**Opmerking:** Hoewel een toepassing kan worden weer gegeven als uitgeschakeld in de data base, blijven gerelateerde contracten op de Block chain en gegevens over die contracten in de-data base.                      |
| WorkflowId                         | int           | No          | De unieke id voor de werk stroom |
| WorkflowName                       | nvarchar (50)  | No          | De naam van de werk stroom |
| WorkflowDisplayName                | nvarchar (255) | No          | De naam van de werk stroom die wordt weer gegeven in de gebruikers interface |
| WorkflowDescription                | nvarchar (255) | Yes         | De beschrijving van de werk stroom |
| ContractId                         | int           | No          | De unieke id voor het contract |
| ContractProvisioningStatus         | int           | No          | Hiermee wordt de huidige status van het inrichtings proces voor het contract aangeduid. Mogelijke waarden zijn: <br />0: het contract is gemaakt door de API in de data base<br />1: het contract is naar het groot boek verzonden<br />2: het contract is geïmplementeerd in het groot boek<br />3 of 4: het contract kan niet in het groot boek worden geïmplementeerd<br />5-het contract is in het groot boek geïmplementeerd <br /><br />Met ingang van versie 1,5 worden de waarden 0 t/m 5 ondersteund. Voor achterwaartse compatibiliteit in de huidige versie is de weer gave **vwContractPropertyV0** beschikbaar die alleen waarden 0 t/m 2 ondersteunt. |
| ContractCodeId                     | int           | No          | Een unieke id voor de code-implementatie van het contract |
| ContractLedgerIdentifier           | nvarchar (255) | Yes         | Een unieke id die is gekoppeld aan de geïmplementeerde versie van een slim contract voor een specifiek gedistribueerd groot boek. Bijvoorbeeld Ethereum. |
| ContractDeployedByUserId           | int           | No          | De unieke id van de gebruiker die het contract heeft geïmplementeerd |
| ContractDeployedByUserFirstName    | nvarchar (50)  | Yes         | De voor naam van de gebruiker die het contract heeft geïmplementeerd |
| ContractDeployedByUserLastName     | nvarchar (255) | Yes         | De achternaam van de gebruiker die het contract heeft geïmplementeerd |
| ContractDeployedByUserExternalId   | nvarchar (255) | No          | De externe ID van de gebruiker die het contract heeft geïmplementeerd. Deze ID is standaard de GUID die de identiteit vertegenwoordigt in het consortium Azure Active Directory |
| ContractDeployedByUserEmailAddress | nvarchar (255) | Yes         | Het e-mail adres van de gebruiker die het contract heeft geïmplementeerd |
| WorkflowPropertyId                 | int           |             | Een unieke id voor een eigenschap van een werk stroom |
| WorkflowPropertyDataTypeId         | int           | No          | De ID van het gegevens type van de eigenschap |
| WorkflowPropertyDataTypeName       | nvarchar (50)  | No          | De naam van het gegevens type van de eigenschap |
| WorkflowPropertyName               | nvarchar (50)  | No          | De naam van de werk stroom eigenschap |
| WorkflowPropertyDisplayName        | nvarchar (255) | No          | De weergave naam van de werk stroom eigenschap |
| WorkflowPropertyDescription        | nvarchar (255) | Yes         | Een beschrijving van de eigenschap |
| ContractPropertyValue              | nvarchar (255) | No          | De waarde voor een eigenschap in het contract |
| Stateful                          | nvarchar (50)  | Yes         | Als deze eigenschap de status van het contract bevat, is dit de weergave naam voor de status. Als deze niet is gekoppeld aan de status, is de waarde Null. |
| StateDisplayName                   | nvarchar (255) | No          | Als deze eigenschap de status bevat, is dit de weergave naam voor de status. Als deze niet is gekoppeld aan de status, is de waarde Null. |
| StateValue                         | nvarchar (255) | Yes         | Als deze eigenschap de status bevat, is het de status waarde. Als deze niet is gekoppeld aan de status, is de waarde Null. |

## <a name="vwcontractstate"></a>vwContractState

Deze weer gave vertegenwoordigt het meren deel van informatie met betrekking tot de status van een specifiek contract en is ontworpen om veelvoorkomende rapportage scenario's te vergemakkelijken. Elke record in deze weer gave bevat de volgende gegevens:

-   Definitie van gekoppelde toepassing
-   Gekoppelde werk stroom definitie
-   Details van de gebruiker die de werk stroom heeft geïmplementeerd
-   Definitie van gekoppelde Smart-contract eigenschap
-   Details voor de eigenschap State van het contract

| Naam                               | Type          | Kan null zijn | Description                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationID                      | int           | No          | Een unieke id voor de toepassing |
| ApplicationName                    | nvarchar (50)  | No          | De naam van de toepassing |
| ApplicationDisplayName             | nvarchar (255) | No          | De naam die moet worden weer gegeven in een gebruikers interface |
| ApplicationEnabled                 | bit           | No          | Hiermee wordt aangegeven of de toepassing op dit moment is ingeschakeld.<br />**Opmerking:** Hoewel een toepassing kan worden weer gegeven als uitgeschakeld in de data base, blijven gerelateerde contracten op de Block chain en gegevens over die contracten in de-data base. |
| WorkflowId                         | int           | No          | Een unieke id voor de werk stroom |
| WorkflowName                       | nvarchar (50)  | No          | De naam van de werk stroom |
| WorkflowDisplayName                | nvarchar (255) | No          | De naam die wordt weer gegeven in de gebruikers interface |
| WorkflowDescription                | nvarchar (255) | Yes         | De beschrijving van de werk stroom |
| ContractLedgerImplementationId     | nvarchar (255) | Yes         | Een unieke id die is gekoppeld aan de geïmplementeerde versie van een slim contract voor een specifiek gedistribueerd groot boek. Bijvoorbeeld Ethereum. |
| ContractId                         | int           | No          | Een unieke id voor het contract |
| ContractProvisioningStatus         | int           | No          |Hiermee wordt de huidige status van het inrichtings proces voor het contract aangeduid. Mogelijke waarden zijn: <br />0: het contract is gemaakt door de API in de data base<br />1: het contract is naar het groot boek verzonden<br />2: het contract is geïmplementeerd in het groot boek<br />3 of 4: het contract kan niet in het groot boek worden geïmplementeerd<br />5-het contract is in het groot boek geïmplementeerd <br /><br />Met ingang van versie 1,5 worden de waarden 0 t/m 5 ondersteund. Voor achterwaartse compatibiliteit in de huidige versie is de weer gave **vwContractStateV0** beschikbaar die alleen waarden 0 t/m 2 ondersteunt. |
| ConnectionId                       | int           | No          | Een unieke id voor het block Chain-exemplaar waarop de werk stroom wordt geïmplementeerd |
| ContractCodeId                     | int           | No          | Een unieke id voor de code-implementatie van het contract |
| ContractDeployedByUserId           | int           | No          | De unieke id van de gebruiker die het contract heeft geïmplementeerd |
| ContractDeployedByUserExternalId   | nvarchar (255) | No          | De externe ID van de gebruiker die het contract heeft geïmplementeerd. Deze ID is standaard de GUID die de identiteit vertegenwoordigt in het consortium Azure Active Directory. |
| ContractDeployedByUserFirstName    | nvarchar (50)  | Yes         | De voor naam van de gebruiker die het contract heeft geïmplementeerd |
| ContractDeployedByUserLastName     | nvarchar (255) | Yes         | De achternaam van de gebruiker die het contract heeft geïmplementeerd |
| ContractDeployedByUserEmailAddress | nvarchar (255) | Yes         | Het e-mail adres van de gebruiker die het contract heeft geïmplementeerd |
| WorkflowPropertyId                 | int           | No          | Een unieke id voor een werk stroom eigenschap |
| WorkflowPropertyDataTypeId         | int           | No          | De ID van het gegevens type van de werk stroom eigenschap |
| WorkflowPropertyDataTypeName       | nvarchar (50)  | No          | De naam van het gegevens type van de werk stroom eigenschap |
| WorkflowPropertyName               | nvarchar (50)  | No          | De naam van de werk stroom eigenschap |
| WorkflowPropertyDisplayName        | nvarchar (255) | No          | De weergave naam van de eigenschap die moet worden weer gegeven in een gebruikers interface |
| WorkflowPropertyDescription        | nvarchar (255) | Yes         | De beschrijving van de eigenschap |
| ContractPropertyValue              | nvarchar (255) | No          | De waarde voor een eigenschap die in het contract is opgeslagen |
| Stateful                          | nvarchar (50)  | Yes         | Als deze eigenschap de status bevat, wordt deze weer gegeven als de weergave naam van de status. Als deze niet is gekoppeld aan de status, is de waarde Null. |
| StateDisplayName                   | nvarchar (255) | No          | Als deze eigenschap de status bevat, is dit de weergave naam voor de status. Als deze niet is gekoppeld aan de status, is de waarde Null. |
| StateValue                         | nvarchar (255) | Yes         | Als deze eigenschap de status bevat, is het de status waarde. Als deze niet is gekoppeld aan de status, is de waarde Null. |

## <a name="vwuser"></a>vwUser

Deze weer gave bevat details over de consortium leden die zijn ingericht voor het gebruik van Azure Block Chain Workbench. Standaard worden gegevens gevuld via de eerste inrichting van de gebruiker.

| Naam               | Type          | Kan null zijn | Beschrijving                                                                                                                                                                                                                               |
|--------------------|---------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Id                 | int           | No          | Een unieke id voor een gebruiker |
| ExternalID         | nvarchar (255) | No          | Een externe ID voor een gebruiker. Deze ID is standaard de GUID die de Azure Active Directory-ID voor de gebruiker vertegenwoordigt. |
| ProvisioningStatus | int           | No          |Hiermee wordt de huidige status van het inrichtings proces voor de gebruiker geïdentificeerd. Mogelijke waarden zijn: <br />0: gebruiker is gemaakt door de API<br />1: er is een sleutel aan de gebruiker in de data base gekoppeld<br />2: de gebruiker is volledig ingericht |
| FirstName          | nvarchar (50)  | Yes         | De voor naam van de gebruiker |
| LastName           | nvarchar (50)  | Yes         | De achternaam van de gebruiker |
| EmailAddress       | nvarchar (255) | Yes         | Het e-mail adres van de gebruiker |

## <a name="vwworkflow"></a>vwWorkflow

Deze weer gave bevat de meta gegevens van de werk stroom en de functies en para meters van de werk stroom. Het is ontworpen voor rapportage en bevat ook meta gegevens over de toepassing die is gekoppeld aan de werk stroom. Deze weer gave bevat gegevens uit meerdere onderliggende tabellen om de rapportage van werk stromen mogelijk te maken. Voor elke werk stroom bevat deze weer gave de volgende gegevens:

-   Definitie van gekoppelde toepassing
-   Gekoppelde werk stroom definitie
-   Informatie over de begin status van de werk stroom

| Naam                              | Type          | Kan null zijn | Description                                                                                                                                |
|-----------------------------------|---------------|-------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationID                     | int           | No          | Een unieke id voor de toepassing |
| ApplicationName                   | nvarchar (50)  | No          | De naam van de toepassing |
| ApplicationDisplayName            | nvarchar (255) | No          | De naam die moet worden weer gegeven in een gebruikers interface |
| ApplicationEnabled                | bit           | No          | Hiermee wordt aangegeven of de toepassing is ingeschakeld |
| WorkflowId                        | int           | Ja         | Een unieke id voor een werk stroom |
| WorkflowName                      | nvarchar (50)  | No          | De naam van de werk stroom |
| WorkflowDisplayName               | nvarchar (255) | No          | De naam die wordt weer gegeven in de gebruikers interface |
| WorkflowDescription               | nvarchar (255) | Yes         | De beschrijving van de werk stroom. |
| WorkflowConstructorFunctionId     | int           | No          | De id van de werk stroom functie die fungeert als constructor voor de werk stroom |
| WorkflowStartStateId              | int           | No          | Een unieke id voor de status |
| WorkflowStartStateName            | nvarchar (50)  | No          | De naam van de status |
| WorkflowStartStateDisplayName     | nvarchar (255) | No          | De naam die moet worden weer gegeven in de gebruikers interface voor de status |
| WorkflowStartStateDescription     | nvarchar (255) | Yes         | Een beschrijving van de werk stroom status |
| WorkflowStartStateStyle           | nvarchar (50)  | Yes         | Deze waarde geeft het percentage aan dat de werk stroom in deze status is voltooid |
| WorkflowStartStateValue           | int           | No          | De waarde van de status |
| WorkflowStartStatePercentComplete | int           | No          | Een beschrijving van een tekst die een hint biedt aan clients om deze status in de gebruikers interface weer te geven. Ondersteunde statussen zijn *geslaagd* en *mislukt* |

## <a name="vwworkflowfunction"></a>vwWorkflowFunction

Deze weer gave bevat de meta gegevens van de werk stroom en de functies en para meters van de werk stroom. Het is ontworpen voor rapportage en bevat ook meta gegevens over de toepassing die is gekoppeld aan de werk stroom. Deze weer gave bevat gegevens uit meerdere onderliggende tabellen om de rapportage van werk stromen mogelijk te maken. Voor elke werk stroom functie bevat deze weer gave de volgende gegevens:

-   Definitie van gekoppelde toepassing
-   Gekoppelde werk stroom definitie
-   Details van werk stroom functie

| Naam                                 | Type          | Kan null zijn | Description                                                                          |
|--------------------------------------|---------------|-------------|--------------------------------------------------------------------------------------|
| ApplicationID                        | int           | No          | Een unieke id voor de toepassing |
| ApplicationName                      | nvarchar (50)  | No          | De naam van de toepassing |
| ApplicationDisplayName               | nvarchar (255) | No          | De naam die moet worden weer gegeven in een gebruikers interface |
| ApplicationEnabled                   | bit           | No          | Hiermee wordt aangegeven of de toepassing is ingeschakeld |
| WorkflowId                           | int           | No          | Een unieke id voor een werk stroom |
| WorkflowName                         | nvarchar (50)  | No          | De naam van de werk stroom |
| WorkflowDisplayName                  | nvarchar (255) | No          | De naam van de werk stroom die wordt weer gegeven in de gebruikers interface |
| WorkflowDescription                  | nvarchar (255) | Yes         | De beschrijving van de werk stroom |
| WorkflowFunctionId                   | int           | No          | Een unieke id voor een functie |
| WorkflowFunctionName                 | nvarchar (50)  | Yes         | De naam van de functie |
| WorkflowFunctionDisplayName          | nvarchar (255) | No          | De naam van een functie die moet worden weer gegeven in de gebruikers interface |
| WorkflowFunctionDescription          | nvarchar (255) | Yes         | De beschrijving van de werk stroom functie |
| WorkflowFunctionIsConstructor        | bit           | No          | Hiermee wordt aangegeven of de werk stroom functie de constructor voor de werk stroom is |
| WorkflowFunctionParameterId          | int           | No          | Een unieke id voor een para meter van een functie |
| WorkflowFunctionParameterName        | nvarchar (50)  | No          | De naam van een para meter van de functie |
| WorkflowFunctionParameterDisplayName | nvarchar (255) | No          | De naam van een functie parameter die moet worden weer gegeven in de gebruikers interface |
| WorkflowFunctionParameterDataTypeId  | int           | No          | Een unieke id voor het gegevens type dat is gekoppeld aan een werk stroom functie parameter |
| WorkflowParameterDataTypeName        | nvarchar (50)  | No          | De naam van een gegevens type dat is gekoppeld aan een werk stroom functie parameter |

## <a name="vwworkflowproperty"></a>vwWorkflowProperty

Deze weer gave bevat de eigenschappen die voor een werk stroom zijn gedefinieerd. Deze weer gave bevat voor elke eigenschap de volgende gegevens:

-   Definitie van gekoppelde toepassing
-   Gekoppelde werk stroom definitie
-   Details van werk stroom eigenschap

| Naam                         | Type          | Kan null zijn | Description                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationID                | int           | No          | Een unieke id voor de toepassing |
| ApplicationName              | nvarchar (50)  | No          | De naam van de toepassing |
| ApplicationDisplayName       | nvarchar (255) | No          | De naam die moet worden weer gegeven in een gebruikers interface |
| ApplicationEnabled           | bit           | No          | Hiermee wordt aangegeven of de toepassing op dit moment is ingeschakeld.<br />**Opmerking:** Hoewel een toepassing kan worden weer gegeven als uitgeschakeld in de data base, blijven gerelateerde contracten op de Block chain en gegevens over die contracten in de-data base. |
| WorkflowId                   | int           | No          | Een unieke id voor de werk stroom |
| WorkflowName                 | nvarchar (50)  | No          | De naam van de werk stroom |
| WorkflowDisplayName          | nvarchar (255) | No          | De naam die moet worden weer gegeven voor de werk stroom in een gebruikers interface |
| WorkflowDescription          | nvarchar (255) | Yes         | Een beschrijving van de werk stroom |
| WorkflowPropertyID           | int           | No          | Een unieke id voor een eigenschap van een werk stroom |
| WorkflowPropertyName         | nvarchar (50)  | No          | De naam van de eigenschap |
| WorkflowPropertyDescription  | nvarchar (255) | Yes         | Een beschrijving van de eigenschap |
| WorkflowPropertyDisplayName  | nvarchar (255) | No          | De naam die moet worden weer gegeven in een gebruikers interface |
| WorkflowPropertyWorkflowId   | int           | No          | De ID van de werk stroom waaraan deze eigenschap is gekoppeld |
| WorkflowPropertyDataTypeId   | int           | No          | De ID van het gegevens type dat voor de eigenschap is gedefinieerd |
| WorkflowPropertyDataTypeName | nvarchar (50)  | No          | De naam van het gegevens type dat voor de eigenschap is gedefinieerd |
| WorkflowPropertyIsState      | bit           | No          | Dit veld geeft aan of deze werk stroom eigenschap de status van de werk stroom bevat |

## <a name="vwworkflowstate"></a>vwWorkflowState

Deze weer gave bevat de eigenschappen die aan een werk stroom zijn gekoppeld. Voor elk contract bevat deze weer gave de volgende gegevens:

-   Definitie van gekoppelde toepassing
-   Gekoppelde werk stroom definitie
-   Status informatie werk stroom

| Naam                         | Type          | Kan null zijn | Description                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationID                | int           | No          | Een unieke id voor de toepassing |
| ApplicationName              | nvarchar (50)  | No          | De naam van de toepassing |
| ApplicationDisplayName       | nvarchar (255) | No          | Een beschrijving van de toepassing |
| ApplicationEnabled           | bit           | No          | Hiermee wordt aangegeven of de toepassing op dit moment is ingeschakeld.<br />**Opmerking:** Hoewel een toepassing kan worden weer gegeven als uitgeschakeld in de data base, blijven gerelateerde contracten op de Block chain en gegevens over die contracten in de-data base. |
| WorkflowId                   | int           | No          | De unieke id voor de werk stroom |
| WorkflowName                 | nvarchar (50)  | No          | De naam van de werk stroom |
| WorkflowDisplayName          | nvarchar (255) | No          | De naam die wordt weer gegeven in de gebruikers interface voor de werk stroom |
| WorkflowDescription          | nvarchar (255) | Yes         | De beschrijving van de werk stroom |
| WorkflowStateID              | int           | No          | De unieke id voor de status |
| WorkflowStateName            | nvarchar (50)  | No          | De naam van de status |
| WorkflowStateDisplayName     | nvarchar (255) | No          | De naam die moet worden weer gegeven in de gebruikers interface voor de status |
| WorkflowStateDescription     | nvarchar (255) | Yes         | Een beschrijving van de werk stroom status |
| WorkflowStatePercentComplete | int           | No          | Deze waarde geeft het percentage aan dat de werk stroom in deze status is voltooid |
| WorkflowStateValue           | nvarchar (50)  | No          | Waarde van de status |
| WorkflowStateStyle           | nvarchar (50)  | No          | Een beschrijving van een tekst die een hint biedt aan clients om deze status in de gebruikers interface weer te geven. Ondersteunde statussen zijn *geslaagd* en *mislukt* |
