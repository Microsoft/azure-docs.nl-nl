---
title: Verbindingen blok keren voor specifieke API-Connect oren
description: Het maken en gebruiken van API-verbindingen in Azure Logic Apps beperken
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 02d9852f6615c3926a02294e0e7eca50f2fbe9a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92310039"
---
# <a name="block-connections-created-by-connectors-in-azure-logic-apps"></a>Verbindingen die zijn gemaakt door connectors in Azure Logic Apps blok keren

Als uw organisatie geen verbinding kan maken met beperkte of niet-goedgekeurde bronnen met behulp van hun connectors in Azure Logic Apps, kunt u de mogelijkheid blok keren om deze verbindingen in werk stromen voor logische apps te creëren en te gebruiken. Met [Azure Policy](../governance/policy/overview.md)kunt u [beleids regels](../governance/policy/overview.md#policy-definition) definiëren en afdwingen waarmee wordt voor komen dat verbindingen worden gemaakt of gebruikt voor connectors die u wilt blok keren. Om veiligheids redenen kunt u bijvoorbeeld verbindingen met specifieke systemen voor sociale media of andere services en systemen blok keren.

In dit onderwerp wordt beschreven hoe u een beleid instelt waarmee specifieke verbindingen worden geblokkeerd met behulp van de Azure Portal, maar u kunt beleids definities op andere manieren maken, bijvoorbeeld via de Azure-REST API, Azure PowerShell, Azure CLI en Azure Resource Manager-sjablonen. Zie [zelf studie: beleid maken en beheren om naleving af te dwingen](../governance/policy/tutorials/create-and-manage.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, [maakt u een gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.

* De verwijzings-ID voor de connector die u wilt blok keren. Zie [de referentie-id van de connector zoeken](#connector-reference-ID)voor meer informatie.

<a name="connector-reference-ID"></a>

## <a name="find-connector-reference-id"></a>Referentie-ID van connector zoeken

Als u al een logische app hebt met de verbinding die u wilt blok keren, volgt u de [stappen voor de Azure Portal](#connector-ID-portal). Anders doet u het volgende:

1. Ga naar de [lijst met Logic apps connectors](/connectors/connector-reference/connector-reference-logicapps-connectors).

1. Zoek de referentie pagina voor de connector die u wilt blok keren.

   Als u bijvoorbeeld de Insta gram-connector wilt blok keren, die wordt afgeschaft, gaat u naar deze pagina:

   `https://docs.microsoft.com/connectors/instagram/`

1. Kopieer de referentie-ID van de connector aan het einde van de URL van de pagina en sla deze vervolgens op met bijvoorbeeld een slash ( `/` ) `instagram` .

   Wanneer u later de beleids definitie maakt, gebruikt u deze ID in de voor waarde-instructie van de definitie, bijvoorbeeld:

   `"like": "*managedApis/instagram"`

<a name="connector-ID-portal"></a>

### <a name="azure-portal"></a>Azure Portal

1. Zoek en open uw logische app in de [Azure Portal](https://portal.azure.com).

1. Selecteer in het menu van de logische app de **code weergave Logic app** zodat u de JSON-definitie van uw logische app kunt weer geven.

   ![Open de weer gave Logic app-code om de connector-ID te vinden](./media/block-connections-connectors/code-view-connector-id.png)

1. Zoek het `parameters` object dat het `$connections` object bevat. Dit bevat een `{connection-name}` object voor elke verbinding in uw logische app en geeft informatie over die verbinding:

   ```json
   {
      "parameters": {
         "$connections": {
            "value" : {
               "{connection-name}": {
                  "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group-name}/providers/Microsoft.Web/connections/{connection-name}",
                  "connectionName": "{connection-name}",
                  "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{connection-name}"
               }
            }
         }
      }
   }
   ```

   Zoek bijvoorbeeld voor de Insta gram-connector het `instagram` object, waarmee een Insta gram-verbinding wordt geïdentificeerd:

   ```json
   {
      "parameters": {
         "$connections": {
            "value" : {
               "instagram": {
                  "connectionId": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/resourceGroups/MyLogicApp-RG/providers/Microsoft.Web/connections/instagram",
                  "connectionName": "instagram",
                  "id": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Web/locations/westus/managedApis/instagram"
               }
            }
         }
      }
   }
   ```

1. Voor de verbinding die u wilt blok keren, zoekt u de `id` eigenschap en de waarde, die volgt op deze notatie: 

   `"id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{connection-name}"`

   Hier volgt een voor beeld `id` van de eigenschap en waarde voor een Insta gram-verbinding:

   `"id": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Web/locations/westus/managedApis/instagram"`

1. `id`Kopieer en sla de referentie-id van de connector aan het einde van de waarde van de eigenschap, bijvoorbeeld `instagram` .

   Wanneer u later de beleids definitie maakt, gebruikt u deze ID in de voor waarde-instructie van de definitie, bijvoorbeeld:

   `"like": "*managedApis/instagram"`

<a name="create-policy-connections"></a>

## <a name="create-policy-to-block-creating-connections"></a>Beleid maken om het maken van verbindingen te blok keren

Voer de volgende stappen uit om het maken van een verbinding in een logische app te blok keren:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Voer in het zoekvak van de portal in `policy` en selecteer **beleid**.

   ![Zoek en selecteer ' beleid ' in Azure Portal](./media/block-connections-connectors/find-select-azure-policy.png)

1. Selecteer in het menu **beleid** onder **ontwerpen** de optie **definities**  >  **+ beleids definitie**.

   ![Selecteer definities > + beleids definitie](./media/block-connections-connectors/add-new-policy-definition.png)

1. Onder **beleids definitie** geeft u de informatie voor de beleids definitie op, op basis van de eigenschappen die in het voor beeld worden beschreven:

   ![Scherm opname waarin de eigenschappen van de beleids definitie worden weer gegeven.](./media/block-connections-connectors/policy-definition-create-connections-1.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Locatie van definitie** | Ja | <*Azure-subscription-name*> | Het Azure-abonnement dat moet worden gebruikt voor de beleids definitie <p><p>1. Als u uw abonnement wilt vinden, selecteert u de knop met weglatings tekens (**...**). <br>2. Zoek en selecteer uw abonnement in de lijst met **abonnementen** . <br>3. Als u klaar bent, selecteert u **selecteren**. |
   | **Naam** | Ja | <*beleids definitie: naam*> | De naam die moet worden gebruikt voor de beleids definitie |
   | **Beschrijving** | Nee | <*beleids definitie: naam*> | Een beschrijving van de beleids definitie |
   | **Categorie** | Ja | **Logic apps** | De naam van een bestaande categorie of nieuwe categorie voor de beleids definitie |
   | **Beleidsafdwinging** | Ja | **Ingeschakeld** | Met deze instelling bepaalt u of de beleids definitie moet worden in-of uitgeschakeld wanneer u uw werk opslaat. |
   ||||

1. Onder **BELEIDS regel** is het JSON-invoervak vooraf ingevuld met een beleids definitie sjabloon. Vervang deze sjabloon door de [beleids definitie](../governance/policy/concepts/definition-structure.md) op basis van de eigenschappen die worden beschreven in de onderstaande tabel en met de volgende syntaxis:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "field": "Microsoft.Web/connections/api.id",
            "like": "*managedApis/{connector-name}"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | `mode` | `All` | De modus die de resource typen bepaalt die door het beleid worden geëvalueerd. <p><p>Met dit scenario stelt `mode` u in dat `All` het beleid wordt toegepast op Azure-resource groepen,-abonnementen en alle resource typen. <p><p>Zie [structuur modus van beleids definitie](../governance/policy/concepts/definition-structure.md#mode)voor meer informatie. |
   | `if` | `{condition-to-evaluate}` | De voor waarde waarmee wordt bepaald wanneer de beleids regel moet worden afgedwongen <p><p>In dit scenario bepaalt de `{condition-to-evaluate}` of de `api.id` waarde in `Microsoft.Web/connections/api.id` overeenkomt met `*managedApis/{connector-name}` , waarmee een Joker teken waarde (*) wordt opgegeven. <p><p>Zie [structuur beleids definitie-beleids regel](../governance/policy/concepts/definition-structure.md#policy-rule)voor meer informatie. |
   | `field` | `Microsoft.Web/connections/api.id` | De `field` waarde die moet worden vergeleken met de voor waarde <p><p>In dit scenario gebruikt de `field` [*alias*](../governance/policy/concepts/definition-structure.md#aliases), `Microsoft.Web/connections/api.id` om toegang te krijgen tot de waarde in de eigenschap Connector `api.id` . |
   | `like` | `*managedApis/{connector-name}` | De logische operator en de waarde die moeten worden gebruikt voor het vergelijken van de `field` waarde <p><p>In dit scenario gebruiken de `like` operator en het Joker teken (*) beide om ervoor te zorgen dat de regel zich bevindt, ongeacht de regio, en de teken reeks, `*managedApis/{connector-name}` , de waarde die overeenkomt met de `{connector-name}` id van de connector die u wilt blok keren. <p><p>Stel bijvoorbeeld dat u het maken van verbindingen met sociale media-platforms of-data bases wilt blok keren: <p><p>Twitter `twitter` <br>Insta gram `instagram` <br>Facebook `facebook` <br>Pinterest `pinterest` <br>-SQL Server of Azure SQL: `sql` <p><p>Zie Naslag informatie voor [connectors vinden](#connector-reference-ID) eerder in dit onderwerp om deze connector-id's te vinden. |
   | `then` | `{effect-to-apply}` | Het effect dat moet worden toegepast wanneer `if` aan de voor waarde wordt voldaan <p><p>In dit scenario `{effect-to-apply}` wordt een aanvraag of bewerking die niet voldoet aan het beleid, geblokkeerd en mislukt. <p><p>Zie [structuur beleids definitie-beleids regel](../governance/policy/concepts/definition-structure.md#policy-rule)voor meer informatie. |
   | `effect` | `deny` | De `effect` is om de aanvraag te blok keren. de opgegeven verbinding moet worden gemaakt <p><p>Zie [Azure Policy-effecten weigeren](../governance/policy/concepts/effects.md#deny)voor meer informatie. |
   ||||

   Stel bijvoorbeeld dat u het maken van verbindingen met de Insta gram-connector wilt blok keren. Dit is de beleids definitie die u kunt gebruiken:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "field": "Microsoft.Web/connections/api.id",
            "like": "*managedApis/instagram"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
   }
   ```

   Dit is de manier waarop het selectie vakje **BELEIDS regel** wordt weer gegeven:

   ![Scherm opname van het vak BELEIDS regel met een voor beeld van een beleids regel.](./media/block-connections-connectors/policy-definition-create-connections-2.png)

   Voor meerdere connectors kunt u meer voor waarden toevoegen, bijvoorbeeld:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "anyOf": [
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/instagram"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/twitter"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/facebook"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/pinterest"
               }
            ]
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

1. Selecteer **Opslaan** als u klaar bent. Nadat u de beleids definitie hebt opgeslagen, worden door Azure Policy gegenereerd en worden meer eigenschaps waarden toegevoegd aan de beleids definitie.

1. [Maak vervolgens een beleids toewijzing](#create-policy-assignment)om de beleids definitie toe te wijzen waarop u het beleid wilt afdwingen.

Zie de volgende onderwerpen voor meer informatie over Azure-beleids definities:

* [Definitie van beleids structuur](../governance/policy/concepts/definition-structure.md)
* [Zelfstudie: Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)
* [Ingebouwde Azure Policy-beleidsdefinities voor Azure Logic Apps](./policy-reference.md)

<a name="create-policy-connector-usage"></a>

## <a name="create-policy-to-block-using-connections"></a>Beleid maken om te blok keren met verbindingen

Wanneer u een verbinding maakt in een logische app, bestaat die verbinding als afzonderlijke Azure-resource. Als u alleen de logische app verwijdert, wordt de verbinding niet automatisch verwijderd en blijft deze bestaan tot deze wordt verwijderd. Mogelijk hebt u een scenario waarin de verbinding al bestaat of waar u de verbinding moet maken voor gebruik buiten een logische app. U kunt nog steeds de mogelijkheid voor het gebruik van een bestaande verbinding in een logische app blok keren door een beleid te maken waarmee wordt voor komen dat logische apps met de beperkte of niet-goedgekeurde verbinding worden opgeslagen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Voer in het zoekvak van de portal in `policy` en selecteer **beleid**.

   ![Zoek en selecteer ' beleid ' in Azure Portal](./media/block-connections-connectors/find-select-azure-policy.png)

1. Selecteer in het menu **beleid** onder **ontwerpen** de optie **definities**  >  **+ beleids definitie**.

   ![Selecteer definities > + beleids definitie](./media/block-connections-connectors/add-new-policy-definition.png)

1. Onder **beleids definitie** geeft u de informatie voor de beleids definitie op, op basis van de eigenschappen die in het voor beeld worden beschreven en gaat u door met behulp van Insta gram als voor beeld:

   ![Eigenschappen van beleids definitie](./media/block-connections-connectors/policy-definition-using-connections-1.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Locatie van definitie** | Ja | <*Azure-subscription-name*> | Het Azure-abonnement dat moet worden gebruikt voor de beleids definitie <p><p>1. Als u uw abonnement wilt vinden, selecteert u de knop met weglatings tekens (**...**). <br>2. Zoek en selecteer uw abonnement in de lijst met **abonnementen** . <br>3. Als u klaar bent, selecteert u **selecteren**. |
   | **Naam** | Ja | <*beleids definitie: naam*> | De naam die moet worden gebruikt voor de beleids definitie |
   | **Beschrijving** | Nee | <*beleids definitie: naam*> | Een beschrijving van de beleids definitie |
   | **Categorie** | Ja | **Logic apps** | De naam van een bestaande categorie of nieuwe categorie voor de beleids definitie |
   | **Beleidsafdwinging** | Ja | **Ingeschakeld** | Met deze instelling bepaalt u of de beleids definitie moet worden in-of uitgeschakeld wanneer u uw werk opslaat. |
   ||||

1. Onder **BELEIDS regel** is het JSON-invoervak vooraf ingevuld met een beleids definitie sjabloon. Vervang deze sjabloon door de [beleids definitie](../governance/policy/concepts/definition-structure.md) op basis van de eigenschappen die worden beschreven in de onderstaande tabel en met de volgende syntaxis:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "value": "[string(field('Microsoft.Logic/workflows/parameters'))]",
            "contains": "{connector-name}"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | `mode` | `All` | De modus die de resource typen bepaalt die door het beleid worden geëvalueerd. <p><p>Met dit scenario stelt `mode` u in dat `All` het beleid wordt toegepast op Azure-resource groepen,-abonnementen en alle resource typen. <p><p>Zie [structuur modus van beleids definitie](../governance/policy/concepts/definition-structure.md#mode)voor meer informatie. |
   | `if` | `{condition-to-evaluate}` | De voor waarde waarmee wordt bepaald wanneer de beleids regel moet worden afgedwongen <p><p>In dit scenario bepaalt de `{condition-to-evaluate}` of de teken reeks uitvoer van de `[string(field('Microsoft.Logic/workflows/parameters'))]` teken reeks bevat, `{connector-name}` . <p><p>Zie [structuur beleids definitie-beleids regel](../governance/policy/concepts/definition-structure.md#policy-rule)voor meer informatie. |
   | `value` | `[string(field('Microsoft.Logic/workflows/parameters'))]` | De waarde die moet worden vergeleken met de voor waarde <p><p>In dit scenario is de `value` teken reeks uitvoer van `[string(field('Microsoft.Logic/workflows/parameters'))]` , waarmee het `$connectors` object in het object wordt geconverteerd `Microsoft.Logic/workflows/parameters` naar een teken reeks. |
   | `contains` | `{connector-name}` | De logische operator en de waarde die moeten worden gebruikt om te vergelijken met de `value` eigenschap <p><p>In dit scenario zorgt de `contains` operator ervoor dat de regel werkt ongeacht waar `{connector-name}` wordt weer gegeven, waarbij de teken reeks, `{connector-name}` , de id is van de connector die u wilt beperken of blok keren. <p><p>Stel dat u wilt blok keren met verbindingen met sociale media platforms of data bases: <p><p>Twitter `twitter` <br>Insta gram `instagram` <br>Facebook `facebook` <br>Pinterest `pinterest` <br>-SQL Server of Azure SQL: `sql` <p><p>Zie Naslag informatie voor [connectors vinden](#connector-reference-ID) eerder in dit onderwerp om deze connector-id's te vinden. |
   | `then` | `{effect-to-apply}` | Het effect dat moet worden toegepast wanneer `if` aan de voor waarde wordt voldaan <p><p>In dit scenario `{effect-to-apply}` wordt een aanvraag of bewerking die niet voldoet aan het beleid wordt geblokkeerd en mislukt. <p><p>Zie [structuur beleids definitie-beleids regel](../governance/policy/concepts/definition-structure.md#policy-rule)voor meer informatie. |
   | `effect` | `deny` | De `effect` is om `deny` de aanvraag te blok keren om een logische app op te slaan die gebruikmaakt van de opgegeven verbinding <p><p>Zie [Azure Policy-effecten weigeren](../governance/policy/concepts/effects.md#deny)voor meer informatie. |
   ||||

   Stel bijvoorbeeld dat u het opslaan van logische apps die gebruikmaken van Insta gram-verbindingen wilt blok keren. Dit is de beleids definitie die u kunt gebruiken:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "value": "[string(field('Microsoft.Logic/workflows/parameters'))]",
            "contains": "instagram"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   Dit is de manier waarop het selectie vakje **BELEIDS regel** wordt weer gegeven:

   ![Regel voor beleids definitie](./media/block-connections-connectors/policy-definition-using-connections-2.png)

1. Selecteer **Opslaan** als u klaar bent. Nadat u de beleids definitie hebt opgeslagen, worden door Azure Policy gegenereerd en worden meer eigenschaps waarden toegevoegd aan de beleids definitie.

1. [Maak vervolgens een beleids toewijzing](#create-policy-assignment)om de beleids definitie toe te wijzen waarop u het beleid wilt afdwingen.

Zie de volgende onderwerpen voor meer informatie over Azure-beleids definities:

* [Definitie van beleids structuur](../governance/policy/concepts/definition-structure.md)
* [Zelfstudie: Beleidsregels voor het afdwingen van naleving maken en beheren](../governance/policy/tutorials/create-and-manage.md)
* [Ingebouwde Azure Policy-beleidsdefinities voor Azure Logic Apps](./policy-reference.md)

<a name="create-policy-assignment"></a>

## <a name="create-policy-assignment"></a>Beleids toewijzing maken

Vervolgens moet u de beleids definitie toewijzen waarvoor u het beleid wilt afdwingen, bijvoorbeeld aan één resource groep, meerdere resource groepen, Azure Active Directory (Azure AD)-Tenant of Azure-abonnement. Voer voor deze taak de volgende stappen uit om een beleids toewijzing te maken:

1. Als u zich hebt afgemeld, meldt u zich weer aan bij de [Azure Portal](https://portal.azure.com). Voer in het zoekvak van de portal in `policy` en selecteer **beleid**.

   ![Zoek en selecteer ' beleid ' in Azure Portal](./media/block-connections-connectors/find-select-azure-policy.png)

1. Selecteer in het menu **beleid** onder **ontwerpen** de optie **toewijzingen**  >  **beleid toewijzen**.

   ![Selecteer toewijzingen > toewijzen](./media/block-connections-connectors/add-new-policy-assignment.png)

1. Geef onder **basis beginselen** deze informatie voor de beleids toewijzing op:

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Bereik** | Ja | De resources waar u de beleids toewijzing wilt afdwingen. <p><p>1. Selecteer de knop met weglatings tekens (**...**) naast het vak **bereik** . <br>2. Selecteer in de lijst **abonnement** het Azure-abonnement. <br>3. Selecteer desgewenst de resource groep in de lijst **resource groep** . <br>4. Als u klaar bent, selecteert u **selecteren**. |
   | **Uitsluitingen** | Nee | Alle Azure-resources die moeten worden uitgesloten van de beleids toewijzing. <p><p>1. Selecteer de knop met weglatings tekens (**...**) naast het vak **uitsluitingen** . <br>2. Selecteer in de lijst met **resources** de resource die > **toevoegen aan de geselecteerde scope**. <br>3. Als u klaar bent, selecteert u **Opslaan**. |
   | **Beleidsdefinitie** | Ja | De naam voor de beleids definitie die u wilt toewijzen en afdwingen. In dit voor beeld wordt het Insta gram-beleid ' Insta gram-verbindingen blok keren ' voortgezet. <p><p>1. Selecteer de knop met weglatings tekens (**...**) naast het vak **beleids definitie** . <br>2. Zoek de beleids definitie en selecteer deze met behulp van het **type** filter of het **zoekvak** . <br>3. Als u klaar bent, selecteert u **selecteren**. |
   | **Naam van toewijzing** | Ja | De naam die moet worden gebruikt voor de beleids toewijzing als deze afwijkt van de beleids definitie |
   | **Toewijzings-ID** | Ja | De automatisch gegenereerde ID voor de beleids toewijzing |
   | **Beschrijving** | Nee | Een beschrijving voor de beleids toewijzing |
   | **Beleidsafdwinging** | Ja | De instelling waarmee de beleids toewijzing wordt in-of uitgeschakeld |
   | **Toegewezen door** | Nee | De naam van de persoon die de beleids toewijzing heeft gemaakt en toegepast |
   ||||

   Bijvoorbeeld, om het beleid toe te wijzen aan een Azure-resource groep met behulp van het Insta gram-voor beeld:

   ![Eigenschappen van beleids toewijzing](./media/block-connections-connectors/policy-assignment-basics.png)

1. Selecteer als u klaar bent de optie **Beoordelen en maken**.

   Nadat u een beleid hebt gemaakt, moet u mogelijk tot wel 15 minuten wachten voordat het beleid van kracht wordt. Wijzigingen kunnen ook vergelijk bare effecten hebben.

1. Nadat het beleid van kracht is, kunt u [het beleid testen](#test-policy).

Zie [Quick Start: een beleids toewijzing maken om niet-compatibele resources te identificeren](../governance/policy/assign-policy-portal.md)voor meer informatie.

<a name="test-policy"></a>

## <a name="test-the-policy"></a>Het beleid testen

U kunt het beleid proberen door een verbinding te maken met behulp van de nu beperkte connector in de ontwerp functie voor logische apps. Als u doorgaat met het Insta gram-voor beeld, kunt u, wanneer u zich aanmeldt bij Insta gram, deze fout melding ontvangen dat uw logische app de verbinding niet tot stand kan brengen:

![De verbindings fout is opgetreden vanwege het toegepaste beleid](./media/block-connections-connectors/connection-failure-message.png)

Het bericht bevat de volgende informatie:

| Beschrijving | Content |
|-------------|---------|
| Reden voor de fout | `"Resource 'instagram' was disallowed by policy."` |
| Naam van toewijzing | `"Block Instagram connections"` |
| Toewijzings-ID | `"/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/resourceGroups/MyLogicApp-RG/providers/Microsoft.Authorization/policyAssignments/4231890fc3bd4352acb0b673"` |
| Beleids definitie-ID | `"/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Authorization/policyDefinitions/b5ddcfec-1b24-4cac-a353-360846a59f24"` |
|||

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure Policy](../governance/policy/overview.md)