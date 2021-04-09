---
title: Gebruik para meters voor het maken van dynamische blauw drukken
description: Meer informatie over statische en dynamische para meters en hoe u deze kunt gebruiken om veilig en dynamisch blauw drukken te maken.
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: 5dbf7ec02e89eac791ec3e17202a5ab13a04b81d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98918531"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>Dynamische blauw drukken via para meters maken

Een volledig gedefinieerde blauw druk met verschillende artefacten, zoals resource groepen, Azure Resource Manager sjablonen (ARM-sjablonen), beleids regels of roltoewijzingen, biedt het snel maken en maken van consistente objecten in Azure. Azure-blauw drukken ondersteunt para meters om flexibel gebruik van deze herbruikbare ontwerp patronen en containers mogelijk te maken. De para meter maakt flexibiliteit, zowel tijdens de definitie als toewijzing, om eigenschappen te wijzigen van de artefacten die door de blauw druk worden geïmplementeerd.

Een eenvoudig voor beeld is het artefact van een resource groep. Wanneer een resource groep wordt gemaakt, heeft deze twee vereiste waarden die moeten worden opgegeven: naam en locatie. Als er geen para meters bestaan, definieert u bij het toevoegen van een resource groep aan uw blauw druk de naam en locatie voor elk gebruik van de blauw druk. Deze herhaling zou elk gebruik van de blauw druk kunnen veroorzaken om artefacten in dezelfde resource groep te maken. Resources in die resource groep worden gedupliceerd en veroorzaken een conflict.

> [!NOTE]
> Het is geen probleem voor twee verschillende blauw drukken om een resource groep met dezelfde naam te gebruiken.
> Als er al een resource groep bestaat die is opgenomen in een blauw druk, blijft de blauw druk de gerelateerde artefacten in die resource groep maken. Dit kan een conflict veroorzaken omdat twee resources met dezelfde naam en hetzelfde bron type niet in een abonnement kunnen bestaan.

De oplossing voor dit probleem is para meters. Met Azure-blauw drukken kunt u de waarde voor elke eigenschap van het artefact definiëren tijdens de toewijzing aan een abonnement. Met de para meter kunt u een blauw druk gebruiken om een resource groep en andere resources binnen één abonnement te maken zonder dat er sprake is van een conflict.

## <a name="blueprint-parameters"></a>Blauwdrukparameters

Via de REST API kunnen para meters op de blauw druk zelf worden gemaakt. Deze para meters verschillen van de para meters voor elk van de ondersteunde artefacten. Wanneer een para meter op de blauw druk wordt gemaakt, kan deze worden gebruikt door de artefacten in die blauw druk. Een voor beeld hiervan is het voor voegsel voor de naam van de resource groep. Het artefact kan de para meter blauw drukken gebruiken om een ' grotendeels dynamische ' para meter te maken. Aangezien de para meter ook kan worden gedefinieerd tijdens de toewijzing, biedt dit patroon een consistentie die kan voldoen aan de naamgevings regels. Zie voor stappen [statische para meters instellen-para meter op blauw niveau](#blueprint-level-parameter).

### <a name="using-securestring-and-secureobject-parameters"></a>SecureString-en secureObject-para meters gebruiken

Hoewel een ARM-sjabloon _artefact_ para meters van de typen **secureString** en **SecureObject** ondersteunt, moeten voor Azure-blauw drukken worden verbonden met een Azure Key Vault. Deze veiligheids maatregel voor komt de onveilige prak tijken van het opslaan van geheimen samen met de blauw druk en moedigt werk gelegenheid van beveiligde patronen aan. Azure-blauw drukken ondersteunt deze beveiligings maatregel en detecteert de opname van een veilige para meter in een ARM-sjabloon _artefact_. De service vraagt vervolgens tijdens de toewijzing naar de volgende Key Vault eigenschappen per gedetecteerde beveiligde para meter:

- Resource-ID Key Vault
- Key Vault geheime naam
- Key Vault geheime versie

Als de blauw druk toewijzing gebruikmaakt van een door het **systeem toegewezen beheerde identiteit**, _moet_ de Key Vault waarnaar wordt verwezen in hetzelfde abonnement bestaan, de definitie van de blauw druk wordt toegewezen aan.

Als de blauw druk toewijzing gebruikmaakt van een door de **gebruiker toegewezen beheerde identiteit**, _kan_ de Key Vault waarnaar wordt verwezen, bestaan in een gecentraliseerd abonnement. Voor de beheerde identiteit moeten de juiste rechten worden verleend op de Key Vault voorafgaande aan de toewijzing van de blauw druk.

> [!IMPORTANT]
> In beide gevallen moet de Key Vault **toegang inschakelen tot Azure Resource Manager voor sjabloon implementatie die** is geconfigureerd op de pagina **toegangs beleid** . Zie [Key Vault-sjabloon implementatie inschakelen](../../../azure-resource-manager/managed-applications/key-vault-access.md#enable-template-deployment)voor instructies over het inschakelen van deze functie.

Zie [Key Vault Overview](../../../key-vault/general/overview.md)voor meer informatie over Azure Key Vault.

## <a name="parameter-types"></a>Parametertypen

### <a name="static-parameters"></a>Statische para meters

Een parameter waarde die in de definitie van een blauw druk is gedefinieerd, wordt een **statische para meter** genoemd, omdat bij elk gebruik van de blauw druk het artefact wordt geïmplementeerd met die statische waarde. In het voor beeld van de resource groep is het niet logisch voor de naam van de resource groep. Dit kan zinvol zijn voor de locatie. Vervolgens wordt bij elke toewijzing van de blauw druk de resource groep gemaakt, ongeacht hoe deze wordt aangeroepen tijdens de toewijzing, op dezelfde locatie. Met deze flexibiliteit kunt u selectief selecteren in wat u definieert als vereist en wat kan worden gewijzigd tijdens de toewijzing.

#### <a name="setting-static-parameters-in-the-portal"></a>Statische para meters instellen in de portal

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer **Blauwdrukdefinities** op de pagina aan de linkerkant.

1. Selecteer een bestaande blauw druk en selecteer vervolgens **blauw** drukken of selecteer **+ blauw druk maken** en vul de informatie in op het tabblad **basis beginselen** .

1. Selecteer **volgende: artefacten** of selecteer het tabblad **artefacten** .

1. Artefacten die zijn toegevoegd aan de blauw druk met parameter opties, worden **X van Y-para meters** weer gegeven in de kolom **para meters** . Selecteer de rij artefacten om de artefact parameters te bewerken.

   :::image type="content" source="../media/parameters/parameter-column.png" alt-text="Scherm afbeelding van de definitie van een blauw druk en de ' X van Y-para meters ingevuld ' gemarkeerd." border="false":::

1. Op de pagina **artefact bewerken** worden de opties weer gegeven die geschikt zijn voor het geselecteerde artefact. Elke para meter op het artefact heeft een titel, een waarde box en een selectie vakje. Stel het selectie vakje in op uitgeschakeld om er een **statische para meter** van te maken. In het onderstaande voor beeld is alleen _locatie_ een **statische para meter** als deze is uitgeschakeld en de _naam van de resource groep_ is ingeschakeld.

   :::image type="content" source="../media/parameters/static-parameter.png" alt-text="Scherm opname van statische para meters op een blauw druk artefact." border="false":::

#### <a name="setting-static-parameters-from-rest-api"></a>Statische para meters instellen van REST API

In elke REST API-URI zijn er verschillende variabelen die worden gebruikt en die u moet vervangen door uw eigen waarden:

- Vervang `{YourMG}` door de naam van uw beheergroep
- Vervang `{subscriptionId}` door uw abonnements-ID

##### <a name="blueprint-level-parameter"></a>Para meter op blauw niveau

Wanneer u een blauw druk via REST API maakt, is het mogelijk om blauw drukken- [para meters](#blueprint-parameters)te maken. Gebruik hiervoor de volgende REST API URI en de indeling van de hoofd tekst:

- REST API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

- Aanvraagtekst

  ```json
  {
      "properties": {
          "description": "This blueprint has blueprint level parameters.",
          "targetScope": "subscription",
          "parameters": {
              "owners": {
                  "type": "array",
                  "metadata": {
                      "description": "List of AAD object IDs that is assigned Owner role at the resource group"
                  }
              }
          },
          "resourceGroups": {
              "storageRG": {
                  "description": "Contains the resource template deployment and a role assignment."
              }
          }
      }
  }
  ```

Zodra de para meter voor het niveau van de blauw druk is gemaakt, kan deze worden gebruikt voor artefacten die aan die blauw drukken worden toegevoegd.
In het volgende REST API voor beeld wordt een functie toewijzings artefact gemaakt op de blauw druk en wordt de para meter blauw niveau gebruikt.

- REST API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
  ```

- Aanvraagtekst

  ```json
  {
      "kind": "roleAssignment",
      "properties": {
          "resourceGroup": "storageRG",
          "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
          "principalIds": "[parameters('owners')]"
      }
  }
  ```

In dit voor beeld maakt de eigenschap **principalIds** gebruik van de para meter **eigen aren** blauw niveau met behulp van een waarde van `[parameters('owners')]` . Het instellen van een para meter voor een artefact met behulp van een para meter op blauw niveau is nog steeds een voor beeld van een **statische para meter**. De para meter op het niveau van de blauw druk kan niet worden ingesteld tijdens de toewijzing van blauw drukken en is dezelfde waarde als elke toewijzing.

##### <a name="artifact-level-parameter"></a>Para meter voor artefact niveau

Het maken van **statische para meters** voor een artefact is vergelijkbaar, maar heeft een rechte waarde in plaats van de functie te gebruiken `parameters()` . In het volgende voor beeld worden twee statische para meters, **tagName** en **tagValue** gemaakt. De waarde op elk wordt rechtstreeks gegeven en gebruikt geen functie aanroep.

- REST API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
  ```

- Aanvraagtekst

  ```json
  {
      "kind": "policyAssignment",
      "properties": {
          "description": "Apply storage tag and the parameter also used by the template to resource groups",
          "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
          "parameters": {
              "tagName": {
                  "value": "StorageType"
              },
              "tagValue": {
                  "value": "Premium_LRS"
              }
          }
      }
  }
  ```

### <a name="dynamic-parameters"></a>Dynamische parameters

Het tegenovergestelde van een statische para meter is een **dynamische para meter**. Deze para meter is niet gedefinieerd op de blauw druk, maar wordt in plaats daarvan gedefinieerd tijdens elke toewijzing van de blauw druk. In het voor beeld van een resource groep is het gebruik van een **dynamische para meter** zinvol voor de naam van de resource groep. Het biedt een andere naam voor elke toewijzing van de blauw druk. Zie de naslag informatie voor [blauw](../reference/blueprint-functions.md) drukken voor een lijst met functies van blauw drukken.

#### <a name="setting-dynamic-parameters-in-the-portal"></a>Dynamische para meters instellen in de portal

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer **Blauwdrukdefinities** op de pagina aan de linkerkant.

1. Klik met de rechter muisknop op de blauw druk die u wilt toewijzen. Selecteer **blauw druk toewijzen** of selecteer de blauw druk die u wilt toewijzen en gebruik vervolgens de knop **blauw** drukken.

1. Zoek de sectie **artefact parameters** op de pagina **blauw** drukken. Elk artefact met ten minste één **dynamische para meter** geeft het artefact en de configuratie opties weer. Geef de vereiste waarden voor de para meters op voordat u de blauw druk toewijst. In het onderstaande voor beeld is _name_ een **dynamische para meter** die moet worden gedefinieerd voor het volt ooien van de blauw druk-toewijzing.

   :::image type="content" source="../media/parameters/dynamic-parameter.png" alt-text="Scherm afbeelding van het instellen van dynamische para meters tijdens de toewijzing van blauw drukken." border="false":::

#### <a name="setting-dynamic-parameters-from-rest-api"></a>Dynamische para meters instellen van REST API

Het instellen van **dynamische para meters** tijdens de toewijzing wordt uitgevoerd door de waarde rechtstreeks in te voeren. In plaats van een functie te gebruiken, zoals [para meters ()](../reference/blueprint-functions.md#parameters), is de opgegeven waarde een juiste teken reeks. Artefacten voor een resource groep worden gedefinieerd met een sjabloon naam, **naam** en **locatie** -eigenschappen. Alle andere para meters voor opgenomen artefacten worden gedefinieerd onder **para meters** met een **\<name\>** sleutel paar en een **waarde** . Als de blauw druk is geconfigureerd voor een dynamische para meter die niet wordt opgegeven tijdens de toewijzing, mislukt de toewijzing.

- REST API-URI

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

- Aanvraagtekst

  ```json
  {
      "properties": {
          "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}  /providers/Microsoft.Blueprint/blueprints/MyBlueprint",
          "resourceGroups": {
              "storageRG": {
                  "name": "StorageAccount",
                  "location": "eastus2"
              }
          },
          "parameters": {
              "storageAccountType": {
                  "value": "Standard_GRS"
              },
              "tagName": {
                  "value": "CostCenter"
              },
              "tagValue": {
                  "value": "ContosoIT"
              },
              "contributors": {
                  "value": [
                      "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                      "38833b56-194d-420b-90ce-cff578296714"
                  ]
                },
              "owners": {
                  "value": [
                      "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                      "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                  ]
              }
          }
      },
      "identity": {
          "type": "systemAssigned"
      },
      "location": "westus"
  }
  ```

## <a name="next-steps"></a>Volgende stappen

- Bekijk de lijst met [blauw](../reference/blueprint-functions.md)drukken-functies.
- Meer informatie over de [levenscyclus van een blauwdruk](./lifecycle.md).
- Meer informatie over hoe u de [blauwdrukvolgorde](./sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](./resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../how-to/update-existing-assignments.md).
- Problemen oplossen tijdens de toewijzing van een blauwdruk met [algemene probleemoplossing](../troubleshoot/general.md).