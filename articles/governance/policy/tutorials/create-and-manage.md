---
title: 'Zelfstudie: Beleidsregels voor het afdwingen van naleving bouwen'
description: In deze zelfstudie gebruikt u beleidsregels voor het afdwingen van standaarden, het beheren van kosten, het onderhouden van de beveiliging en het afdwingen van ontwerpprincipes op ondernemingsniveau.
ms.date: 01/29/2021
ms.topic: tutorial
ms.openlocfilehash: a643e7ccede4966719972694ea29eeb77789595e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99221190"
---
# <a name="tutorial-create-and-manage-policies-to-enforce-compliance"></a>Zelfstudie: Beleidsregels voor het afdwingen van naleving maken en beheren

Als u aan uw bedrijfsnormen en serviceovereenkomsten wilt blijven voldoen, is het belangrijk dat u begrijpt hoe u beleidsregels kunt maken en beheren in Azure. In deze zelfstudie leert u hoe u Azure Policy gebruikt om enkele algemene taken voor het maken, toewijzen en beheren van beleid in uw organisatie uit te voeren, zoals:

> [!div class="checklist"]
> - Toewijzen van een beleid om een voorwaarde voor bronnen af te dwingen die u in de toekomst maakt
> - Maken en toewijzen van de definitie van een initiatief om naleving bij te houden voor meerdere bronnen
> - Problemen met een bron die niet voldoet of is geweigerd oplossen
> - Een nieuw beleid binnen een organisatie implementeren

Als u een beleid wilt toewijzen voor het identificeren van de huidige nalevingsstatus van uw bestaande bronnen, raadpleegt u de quickstart-artikelen over dit ontwerp.

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="assign-a-policy"></a>Een beleid toewijzen

De eerste stap bij het afdwingen van naleving met Azure Policy bestaat uit het toewijzen van een beleidsdefinitie. Een beleidsdefinitie definieert onder welke voorwaarde een beleid wordt afgedwongen en welke actie moet worden uitgevoerd. Wijs in dit voorbeeld de ingebouwde beleidsdefinitie met de naam _Een tag overnemen van de resourcegroep indien deze ontbreekt_ toe om de opgegeven tag met de bijbehorende waarde van de bovenliggende resourcegroep toe te voegen aan nieuwe of bijgewerkte resources zonder deze tag.

1. Ga naar Azure Portal om beleid toe te wijzen. Zoek en selecteer **Beleid**.

   :::image type="content" source="../media/create-and-manage/search-policy.png" alt-text="Schermopname van het zoeken naar Beleid in de zoekbalk." border="false":::

1. Selecteer **Toewijzingen** in het linkerdeelvenster van de Azure Policy-pagina. Een toewijzing is een beleid dat is toegewezen om te worden toegepast binnen een bepaald bereik.

   :::image type="content" source="../media/create-and-manage/select-assignments.png" alt-text="Schermopname van het selecteren van het knooppunt Toewijzingen op de pagina Overzicht van het beleid." border="false":::

1. Selecteer **Beleid toewijzen** boven in de pagina **Beleidstoewijzingen**.

   :::image type="content" source="../media/create-and-manage/select-assign-policy.png" alt-text="Schermopname van het selecteren van de knop Beleid toewijzen op de pagina Toewijzingen." border="false":::

1. Selecteer op het tabblad **Basisbeginselen** op de pagina **Beleid toewijzen** het **Bereik** door te klikken op het beletselteken en een beheergroep of abonnement te selecteren. U kunt ook een resourcegroep selecteren. Het bereik bepaalt op welke resources of groep resources de beleidstoewijzing wordt afgedwongen.
   Selecteer vervolgens **Selecteren** onderaan de pagina **Bereik**.

   In dit voorbeeld wordt het abonnement **Contoso** gebruikt. U hebt waarschijnlijk een ander abonnement.

1. Resources kunnen worden uitgesloten op basis van het **bereik**. **Uitsluitingen** starten op één niveau lager dan het niveau van het **bereik**. **Uitsluitingen** zijn optioneel, dus laat dit veld nu nog leeg.

1. Selecteer het weglatingsteken **Beleidsdefinitie** om de lijst van beschikbare definities te openen. U kunt het **Type** van de beleidsdefinitie filteren op _Ingebouwd_ om alles te bekijken en de beschrijvingen te lezen.

1. Selecteer **Een tag overnemen van de resourcegroep indien deze ontbreekt**. Als u deze optie niet meteen kunt vinden, kunt u **een tag overnemen** in het zoekvak typen en op Enter drukken of buiten het zoekvak klikken.
   Selecteer **Selecteren** onderaan de pagina **Beschikbare definities** zodra u de beleidsdefinitie hebt gevonden en geselecteerd.

   :::image type="content" source="../media/create-and-manage/select-available-definition.png" alt-text="Schermopname van het zoekfilter tijdens het selecteren van een beleidsdefinitie.":::

1. De **Toewijzingsnaam** wordt automatisch ingevuld met de naam van het beleid dat u hebt geselecteerd, maar u kunt dit wijzigen. Laat voor dit voorbeeld _Een tag overnemen van de resourcegroep indien deze ontbreekt_ ongewijzigd. U kunt ook een optionele **Beschrijving** opgeven. De beschrijving bevat details over deze beleidstoewijzing.

1. Laat **Beleidsafdwinging** op _Ingeschakeld_ staan. Als deze instelling op _Uitgeschakeld_ staat, kunt u het resultaat van het beleid testen zonder het effect te activeren. Zie [Afdwingingsmodus](../concepts/assignment-structure.md#enforcement-mode) voor meer informatie.

1. **Toegewezen door** wordt automatisch ingevuld op basis van degene die is aangemeld. Dit veld is optioneel, dus u kunt aangepaste waarden invoeren.

1. Selecteer het tabblad **Parameters** bovenaan de wizard.

1. Voer voor **Tagnaam** _Omgeving_ in.

1. Selecteer het tabblad **Herstel** bovenaan de wizard.

1. Laat **Een hersteltaak maken** uitgeschakeld. Met dit vak kunt u een taak maken voor het wijzigen van bestaande resources naast nieuwe of bijgewerkte resources. Zie [Resources herstellen](../how-to/remediate-resources.md) voor meer informatie.

1. **Een beheerde identiteit maken** wordt automatisch ingeschakeld omdat deze beleidsdefinitie het effect [Wijzigen](../concepts/effects.md#modify) gebruikt. **Machtigingen** wordt automatisch ingesteld op _Inzender_ op basis van de beleidsdefinitie. Zie [Beheerde identiteiten](../../../active-directory/managed-identities-azure-resources/overview.md) en [Hoe herstelbeveiliging werkt](../how-to/remediate-resources.md#how-remediation-security-works) voor meer informatie.

1. Klik boven aan de wizard op het tabblad **niet-nalevings berichten** .

1. Stel het **bericht niet-naleving** in op _deze resource heeft niet het vereiste label_. Dit aangepaste bericht wordt weer gegeven wanneer een resource wordt geweigerd of voor niet-compatibele resources tijdens een reguliere evaluatie.

1. Selecteer het tabblad **Controleren + maken** bovenaan de wizard.

1. Controleer uw selecties en selecteer vervolgens **Maken** onderaan de pagina.

## <a name="implement-a-new-custom-policy"></a>Een nieuw aangepast beleid implementeren

Nu u een ingebouwde beleidsdefinitie hebt toegewezen, kunt u meer kunt doen met Azure Policy. Maak vervolgens een nieuw aangepast beleid om kosten te besparen door te controleren of virtuele machines die in uw omgeving worden gemaakt niet van de G-serie zijn. Hiermee wordt steeds wanneer een gebruiker in uw organisatie de virtuele machine in de G-serie wil maken, de aanvraag geweigerd.

1. Selecteer **Definities** onder **Ontwerpen** aan de linkerkant van de pagina Azure Policy.

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="Schermopname van de pagina Definities onder de groep Ontwerpen." border="false":::

1. Selecteer **+ Beleidsdefinitie** bovenaan de pagina. Met deze knop opent u de pagina **Beleidsdefinitie**.

1. Voer de volgende informatie in:

   - De beheergroep of het abonnement waarin de beleidsdefinitie is opgeslagen. Selecteer met behulp van het weglatingsteken **Definitielocatie**.

     > [!NOTE]
     > Als u van plan bent deze toe te passen op meerdere abonnementen, moet de locatie een beheergroep zijn met de abonnementen waaraan u het beleid toewijst. Hetzelfde geldt voor de definitie van een initiatief.

   - De naam van de beleidsdefinitie - _Vereisen dat VM-SKU's niet tot de G-serie behoren_
   - De beschrijving van het doel van de beleidsdefinitie: _Deze beleidsdefinitie dwingt af dat alle virtuele machines die zijn gemaakt in dit bereik, andere SKU's hebben dan de G-serie om kosten te beperken._
   - Kies een van de bestaande opties (zoals _Compute_) of maak een nieuwe categorie voor deze beleidsdefinitie.
   - Kopieer de volgende JSON-code en werk deze vervolgens naar wens bij:
      - De beleidsparameters.
      - De beleidsregels/-voorwaarden, in dit geval – VM SKU-grootte gelijk aan G-serie
      - Het effect van dit beleid, in dit geval: **Weigeren**.

   Zo moet de JSON eruitzien. Plak uw gewijzigde code in Azure Portal.

   ```json
   {
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Compute/virtualMachines"
                   },
                   {
                       "field": "Microsoft.Compute/virtualMachines/sku.name",
                       "like": "Standard_G*"
                   }
               ]
           },
           "then": {
               "effect": "deny"
           }
       }
   }
   ```

   De eigenschap _Veld_ in de beleidsregel moet een ondersteunde waarde hebben. Een volledige lijst met waarden is te vinden in de [structuurvelden voor de beleidsdefinitie](../concepts/definition-structure.md#fields). Een voorbeeld van een alias is mogelijk `"Microsoft.Compute/VirtualMachines/Size"`.

   Meer Azure Policy-voorbeelden vindt u in [Voorbeelden van Azure Policy](../samples/index.md).

1. Selecteer **Opslaan**.

## <a name="create-a-policy-definition-with-rest-api"></a>Een beleidsdefinitie maken met de REST API

U kunt een beleid maken met de REST API voor Azure-beleidsdefinities. Met de REST API kunt u beleidsdefinities maken en verwijderen en informatie over bestaande definities ophalen. Gebruik het volgende voorbeeld om een beleidsdefinitie te maken:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

Voeg aanvraagtekst toe die soortgelijk is aan het volgende voorbeeld:

```json
{
    "properties": {
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                }
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

## <a name="create-a-policy-definition-with-powershell"></a>Een beleidsdefinitie maken met PowerShell

Controleer voordat u doorgaat met het PowerShell-voorbeeld of u de nieuwste versie van de Azure PowerShell Az-module hebt geïnstalleerd.

U kunt een beleidsdefinitie maken met de `New-AzPolicyDefinition`-cmdlet.

Als u een beleidsdefinitie wilt maken op basis van een bestand, geeft u het pad naar het bestand op. Gebruik het volgende voorbeeld voor een extern bestand:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -DisplayName 'Deny cool access tiering for storage' `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

Gebruik het volgende voorbeeld voor een lokaal bestand:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -Description 'Deny cool access tiering for storage' `
    -Policy 'c:\policies\coolAccessTier.json'
```

Gebruik het volgende voorbeeld om een beleidsdefinitie met een inline regel te maken:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name 'denyCoolTiering' -Description 'Deny cool access tiering for storage' -Policy '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

De uitvoer wordt opgeslagen in een `$definition`-object, dat wordt gebruikt tijdens de toewijzing van configuratiebeleid. In het volgende voorbeeld wordt een beleidsdefinitie met parameters gemaakt:

```azurepowershell-interactive
$policy = '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of locations that can be specified when deploying storage accounts.",
            "strongType": "location",
            "displayName": "Allowed locations"
        }
    }
}'

$definition = New-AzPolicyDefinition -Name 'storageLocations' -Description 'Policy to specify locations for storage accounts.' -Policy $policy -Parameter $parameters
```

### <a name="view-policy-definitions-with-powershell"></a>Beleidsdefinities met PowerShell bekijken

Als u alle beleidsdefinities in uw abonnement wilt weergeven, gebruikt u de volgende opdracht:

```azurepowershell-interactive
Get-AzPolicyDefinition
```

Hiermee worden alle beschikbare beleidsdefinities geretourneerd, inclusief ingebouwd beleid. Elk beleid wordt geretourneerd in de volgende indeling:

```output
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

## <a name="create-a-policy-definition-with-azure-cli"></a>Een beleidsdefinitie maken met Azure CLI

U kunt een beleidsdefinitie maken met Azure CLI met behulp van de opdracht `az policy definition`. Gebruik het volgende voorbeeld om een beleidsdefinitie met een inline regel te maken:

```azurecli-interactive
az policy definition create --name 'denyCoolTiering' --description 'Deny cool access tiering for storage' --rules '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

### <a name="view-policy-definitions-with-azure-cli"></a>Beleidsdefinities met Azure CLI bekijken

Als u alle beleidsdefinities in uw abonnement wilt weergeven, gebruikt u de volgende opdracht:

```azurecli-interactive
az policy definition list
```

Hiermee worden alle beschikbare beleidsdefinities geretourneerd, inclusief ingebouwd beleid. Elk beleid wordt geretourneerd in de volgende indeling:

```json
{
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",
    "displayName": "Allowed locations",
    "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "policyRule": {
        "if": {
            "not": {
                "field": "location",
                "in": "[parameters('listOfAllowedLocations')]"
            }
        },
        "then": {
            "effect": "Deny"
        }
    },
    "policyType": "BuiltIn"
}
```

## <a name="create-and-assign-an-initiative-definition"></a>Een initiatiefdefinitie maken en toewijzen

Met een initiatiefdefinitie kunt u verschillende beleidsdefinities groeperen om één overkoepelend doel te bereiken. Een initiatief evalueert resources binnen het bereik van de toewijzing op naleving van de opgenomen beleidsregels. Zie het [Overzicht van Azure Policy](../overview.md) voor meer informatie over initiatiefdefinities.

### <a name="create-an-initiative-definition"></a>Een initiatiefdefinitie maken

1. Selecteer **Definities** onder **Ontwerpen** aan de linkerkant van de pagina Azure Policy.

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="Schermopname van de pagina Definities onder de groep Ontwerpen.":::

1. Selecteer **+ Initiatiefdefinitie** bovenaan de pagina. U gaat dan naar de wizard **Initiatiefdefinitie**.

   :::image type="content" source="../media/create-and-manage/initiative-definition.png" alt-text="Schermopname van de pagina Initiatiefdefinitie en eigenschappen die moeten worden ingesteld.":::

1. Gebruik het beletselteken bij **Initiatieflocatie** om een beheergroep of abonnement te selecteren voor het opslaan van de definitie. Als het bereik van de vorige pagina was beperkt tot een enkele beheergroep of enkel abonnement, wordt **Initiatieflocatie** automatisch ingevuld.

1. Voer de **naam** en **beschrijving** van het initiatief in.

   Dit voorbeeld zorgt ervoor worden dat bronnen voldoen aan beleidsdefinities over beveiliging. Noem het initiatief **Beveiligen** en stel de volgende beschrijving in: **Dit initiatief is gemaakt voor het afhandelen van alle beleidsregels die zijn gekoppeld aan het beveiligen van resources**.

1. Kies voor **Categorie** uit bestaande opties of maak een nieuwe categorie.

1. Stel een **Versie** in voor het initiatief, zoals _1.0_.

   > [!NOTE]
   > De versiewaarde is strikt een metagegeven, en wordt niet gebruikt voor updates of andere processen van de Azure Policy-service.

1. Selecteer **Volgende** onderaan de pagina of het tabblad **Beleid** bovenaan de wizard.

1. Selecteer de knop **Beleidsdefinitie (s) toevoegen** en blader door de lijst. Selecteer de beleidsdefinitie (s) die u aan dit initiatief wilt toevoegen. Voeg de volgende ingebouwde beleidsdefinities toe aan het initiatief **Beveiligen** door het selectievakje naast de beleidsdefinities te selecteren:

   - Toegestane locaties
   - Ontbrekende Endpoint Protection bewaken in Azure Security Center
   - Niet-internetgerichte virtuele machines moeten worden beveiligd met netwerkbeveiligingsgroepen
   - Azure Backup moet zijn ingeschakeld voor virtuele machines
   - Schijfversleuteling moet worden toegepast op virtuele machines
   - Een tag toevoegen aan of vervangen in resources (voeg deze beleidsdefinitie twee keer toe)

   Nadat u elke beleidsdefinitie hebt geselecteerd in de lijst, selecteert u **Toevoegen** onderaan de lijst.
   Omdat de definitie _Een tag toevoegen aan of vervangen in resources_ twee keer is toegevoegd, krijgt elk van de twee definities een andere _referentie-id_.

   :::image type="content" source="../media/create-and-manage/initiative-definition-2.png" alt-text="Schermopname van de geselecteerde beleidsdefinities met hun referentie-id en -groep op de initiatiefdefinitiepagina.":::

   > [!NOTE]
   > De geselecteerde beleidsdefinities kunnen worden toegevoegd aan groepen door een of meer toegevoegde definities te selecteren en **Geselecteerde beleidsregels toevoegen aan een groep** te selecteren. De groep moet eerst bestaan, en kan worden gemaakt op het tabblad **Groepen**.

1. Selecteer **Volgende** onderaan de pagina of het tabblad **Groepen** bovenaan de wizard. Vanaf dit tabblad kunnen nieuwe groepen worden toegevoegd. In deze zelfstudie voegen we geen groepen toe.

1. Selecteer **Volgende** onderaan de pagina of het tabblad **Initiatiefparameters** bovenaan de wizard. Als we in het initiatief een parameter willen hebben om door te geven aan een of meer opgenomen beleidsdefinities, wordt de parameter hier gedefinieerd en vervolgens gebruikt op het tabblad **Beleidsparameters**. In deze zelfstudie voegen we geen initiatiefparameters toe.

   > [!NOTE]
   > Wanneer ze eenmaal zijn opgeslagen in een initiatiefdefinitie, kunnen initiatiefparameters niet uit het initiatief worden verwijderd. Als een initiatiefparameter niet meer nodig is, stelt u deze buiten gebruik in eventuele beleidsdefinitieparameters.

1. Selecteer **Volgende** onderaan de pagina of het tabblad **Beleidsparameters** bovenaan de wizard.

1. Aan het initiatief toegevoegde beleidsdefinities die parameters hebben, worden weergegeven in een raster. Het _Waardetype_ kan 'Standaardwaarde', 'Waarde instellen' of 'Initiatiefparameter gebruiken' zijn. Als 'Waarde instellen' is geselecteerd, wordt de betreffende waarde ingevoerd onder _Waarde(n)_ . Als de parameter in de beleidsdefinitie een lijst met toegestane waarden bevat, is het invoervak een vervolgkeuzelijst. Als 'Initiatiefparameter gebruiken' is geselecteerd, wordt er een vervolgkeuzelijst aangeboden met de namen van de initiatiefparameters die op het tabblad **Initiatiefparameters** zijn gemaakt.

   :::image type="content" source="../media/create-and-manage/initiative-definition-3.png" alt-text="Schermopname van de opties voor toegestane waarden voor de parameter voor de definitie van toegestane locaties op het tabblad met beleidsparameters van de initiatiefdefinitiepagina.":::

   > [!NOTE]
   > Voor sommige `strongType`-parameters kan de lijst met waarden niet automatisch worden bepaald. In deze gevallen wordt een beletselteken rechts van de parameterrij weergegeven. Als u deze optie selecteert, wordt de pagina Parameterbereik (&lt;parameternaam&gt;) geopend. Op deze pagina selecteert u het abonnement dat u wilt gebruiken om de waardeopties op te geven. Dit parameterbereik wordt alleen gebruikt tijdens het maken van de initiatiefdefinitie en heeft geen invloed op de evaluatie van het beleid of het bereik van het initiatief na het toewijzen.

   Stel het _waardetype_ 'Toegestane locaties' in op 'Waarde instellen' en selecteer 'US - oost 2' in de vervolgkeuzelijst. Voor de twee exemplaren van de beleidsdefinitie _Een tag toevoegen aan of vervangen in resources_ stelt u de parameter **Tagnaam** in op 'Env' en 'CostCenter', en de parameter **Tagwaarde** op 'Test' en 'Lab', zoals hieronder getoond. Laat bij de overige 'Standaardwaarde' staan. Door dezelfde definitie twee keer in het initiatief te gebruiken, maar met verschillende parameters, wordt in deze configuratie de tag 'Env' vervangen door de waarde 'Test' en de tag 'CostCenter'door de waarde 'Lab' in het bereik van de toewijzing.

   :::image type="content" source="../media/create-and-manage/initiative-definition-4.png" alt-text="Schermopname van de ingevoerde opties voor toegestane waarden voor de parameter voor de definitie van toegestane locaties en waarden voor beide parametersets op het tabblad met beleidsparameters van de initiatiefdefinitiepagina.":::

1. Selecteer **Controleren en maken** onderaan de pagina of bovenaan de wizard.

1. Controleer de instellingen en selecteer **Maken**.

#### <a name="create-a-policy-initiative-definition-with-azure-cli"></a>Een initiatiefdefinitie voor beleid maken met Azure CLI

U kunt een initiatiefdefinitie voor beleid maken met Azure CLI met behulp van de opdracht `az policy set-definition`. Gebruik het volgende voorbeeld om een initiatiefdefinitie voor beleid te maken met een bestaande beleidsdefinitie:

```azurecli-interactive
az policy set-definition create -n readOnlyStorage --definitions '[
        {
            "policyDefinitionId": "/subscriptions/mySubId/providers/Microsoft.Authorization/policyDefinitions/storagePolicy",
            "parameters": { "storageSku": { "value": "[parameters(\"requiredSku\")]" } }
        }
    ]' \
    --params '{ "requiredSku": { "type": "String" } }'
```

#### <a name="create-a-policy-initiative-definition-with-azure-powershell"></a>Een initiatiefdefinitie voor beleid maken met Azure PowerShell

U kunt een initiatiefdefinitie voor beleid maken in Azure PowerShell met behulp van de cmdlet `New-AzPolicySetDefinition`. Gebruik het volgende beleidsinitiatiefdefinitiebestand als `VMPolicySet.json` om een initiatiefdefinitie voor beleid te maken met een bestaande beleidsdefinitie:

```json
[
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "parameters": {
            "tagName": {
                "value": "Business Unit"
            },
            "tagValue": {
                "value": "Finance"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/464dbb85-3d5f-4a1d-bb09-95a9b5dd19cf"
    }
]
```

```azurepowershell-interactive
New-AzPolicySetDefinition -Name 'VMPolicySetDefinition' -Metadata '{"category":"Virtual Machine"}' -PolicyDefinition C:\VMPolicySet.json
```

### <a name="assign-an-initiative-definition"></a>Een initiatiefdefinitie maken

1. Selecteer **Definities** onder **Ontwerpen** aan de linkerkant van de pagina Azure Policy.

1. Zoek de initiatiefdefinitie **Veilig worden** die u eerder hebt gemaakt en selecteer deze. Selecteer **Toewijzen** bovenaan de pagina om de pagina **Beveiligen: Initiatief toewijzen** te openen.

   :::image type="content" source="../media/create-and-manage/assign-definition.png" alt-text="Schermopname van de knop Toewijzen op de pagina Initiatiefdefinitie." border="false":::

   U kunt ook met de rechtermuisknop op de geselecteerde rij klikken of het beletselteken aan het einde van de rij selecteren voor een contextmenu. Selecteer daarna **Toewijzen**.

   :::image type="content" source="../media/create-and-manage/select-right-click.png" alt-text="Schermopname van het contextmenu voor een initiatief om de toewijsfunctionaliteit te selecteren." border="false":::

1. Vul de pagina **Beveiligen: Initiatief toewijzen** in door de volgende voorbeeldinformatie in te voeren. U kunt uw eigen gegevens gebruiken.

   - Bereik: de beheergroep of het abonnement waarin u het initiatief hebt opgeslagen, wordt de standaardwaarde.
     U kunt het bereik wijzigen om het initiatief toe te wijzen aan een abonnement of resourcegroep in de opslaglocatie.
   - Uitsluitingen: configureer alle resources binnen het bereik om te voorkomen dat de initiatieftoewijzing erop wordt toegepast.
   - Definitie van het initiatief en naam van de toewijzing: Beveiligen (vooraf ingevuld als de naam van het initiatief dat wordt toegewezen).
   - Beschrijving: deze initiatieftoewijzing heeft als doel deze groep beleidsdefinities af te dwingen.
   - Beleidsafdwinging: Laat de standaardwaarde op _Ingeschakeld_ staan.
   - Toegewezen door: automatisch gevuld op basis van degene die is aangemeld. Dit veld is optioneel, dus u kunt aangepaste waarden invoeren.

1. Selecteer het tabblad **Parameters** bovenaan de wizard. Als u in de vorige stappen een initiatiefparameter hebt geconfigureerd, moet u hier een waarde instellen.

1. Selecteer het tabblad **Herstel** bovenaan de wizard. Laat **Beheerde identiteit maken** uitgeschakeld. Dit vak _moet_ worden ingeschakeld wanneer het beleid of initiatief dat wordt toegewezen beleid bevat met de effecten [deployIfNotExists](../concepts/effects.md#deployifnotexists) of [modify](../concepts/effects.md#modify). Omdat het beleid dat voor deze zelfstudie wordt gebruikt deze regel niet bevat, laat u deze optie uitgeschakeld. Zie [Beheerde identiteiten](../../../active-directory/managed-identities-azure-resources/overview.md) en [Hoe herstelbeveiliging werkt](../how-to/remediate-resources.md#how-remediation-security-works) voor meer informatie.

1. Selecteer het tabblad **Controleren + maken** bovenaan de wizard.

1. Controleer uw selecties en selecteer vervolgens **Maken** onderaan de pagina.

## <a name="check-initial-compliance"></a>Eerste naleving controleren

1. Selecteer **Naleving** links op de Azure Policy-pagina.

1. Zoek het initiatief **Veilig verkrijgen**. De _Nalevingsstatus_ hiervan is waarschijnlijk nog **Niet gestart**.
   Selecteer het initiatief voor alle details van de toewijzing.

   :::image type="content" source="../media/create-and-manage/compliance-status-not-started.png" alt-text="Schermopname van de nalevingspagina van het initiatief, waarin de toewijzingsevaluaties worden weergegeven met de status Niet gestart." border="false":::

1. Wanneer de initiatieftoewijzing is voltooid, wordt de pagina voor naleving bijgewerkt met de _Nalevingsstatus_**Compatibel**.

   :::image type="content" source="../media/create-and-manage/compliance-status-compliant.png" alt-text="Schermopname van de nalevingspagina van het initiatief, waarin de toewijzingsevaluaties als voltooid worden weergegeven met de status Compatibel." border="false":::

1. Als u een beleid op de pagina voor initiatiefnaleving selecteert, wordt de pagina met nalevingsdetails voor dat beleid geopend. Op deze pagina staan de nalevingsdetails op resourceniveau.

## <a name="remove-a-non-compliant-or-denied-resource-from-the-scope-with-an-exclusion"></a>Een niet-compatibele of afgewezen resource uit het bereik met een uitsluiting

Na het toewijzen van een beleidsinitiatief om een specifieke locatie te vereisen, wordt elke resource geweigerd die op een andere locatie is gemaakt. In deze sectie doorloopt u het oplossen van een geweigerde poging om een resource te maken door een uitsluiting te maken voor een specifieke groep resources. Uitsluiting voorkomt afdwingen van het beleid (of het initiatief) op de resourcegroep. In het volgende voorbeeld is elke willekeurige locatie toegestaan in de uitgesloten resourcegroep. Een uitsluiting kan worden toegepast op een abonnement, een resourcegroep of afzonderlijke resources.

> [!NOTE]
> Er kan ook een [beleidsuitzondering](../concepts/exemption-structure.md) worden gebruikt om de evaluatie van een resource over te slaan. Zie [Bereik in Azure Policy](../concepts/scope.md) voor meer informatie.

Implementaties die worden voorkomen door een toegewezen beleid of initiatief, kunnen worden weergegeven op de resourcegroep waarop de implementatie is gericht: Selecteer **Implementaties** aan de linkerkant van de pagina en selecteer de **implementatienaam** van de mislukte implementatie. De resource die is geweigerd, wordt weergegeven met de status _Verboden_. Om het beleid of het initiatief en de toewijzing te bepalen die de resource heeft geweigerd, selecteert u **Mislukt. Klik hier voor meer informatie ->** op de overzichtspagina van de implementatie. Aan de rechterkant van de pagina wordt een venster geopend met de informatie over de fout. Onder **Foutdetails** staan de GUID's van de bijbehorende beleidstoewijzingsobjecten.

:::image type="content" source="../media/create-and-manage/rg-deployment-denied.png" alt-text="Schermopname van een mislukte implementatie die is geweigerd door een beleidstoewijzing." border="false":::

Op de Azure Policy-pagina: Selecteer **Naleving** aan de linkerkant van de pagina en selecteer het beleidsinitiatief **Veilig ophalen**. Op deze pagina ziet u een toename van het aantal **weigeringen** voor geblokkeerde resources. Op het tabblad **Gebeurtenissen** vindt u informatie over wie heeft geprobeerd de resource te maken of te implementeren die door de beleidsdefinitie is geweigerd.

:::image type="content" source="../media/create-and-manage/compliance-overview.png" alt-text="Schermopname van het tabblad Gebeurtenissen en details van de beleidsgebeurtenis op de nalevingspagina van het initiatief." border="false":::

In dit voorbeeld deed een van de senior virtualisatie-experts van Contoso vereist werk. We moeten aan Trent een ruimte toekennen voor een uitzondering. Maak een nieuwe resourcegroep, **LocationsExcluded**, en ken hieraan vervolgens een uitzondering op deze beleidstoewijzing toe.

### <a name="update-assignment-with-exclusion"></a>Toewijzing met uitsluiting bijwerken

1. Selecteer **Toewijzingen** onder **Ontwerpen** aan de linkerkant van de pagina Azure Policy.

1. Blader door alle beleidstoewijzingen en open de toewijzing _Veilig ophalen_.

1. Stel de **Uitsluiting** in door het beletselteken te selecteren en de resourcegroep te selecteren die u wilt uitsluiten (in dit voorbeeld _LocationsExcluded_). Selecteer **Toevoegen aan geselecteerde bereik** en selecteer **Opslaan**.

   :::image type="content" source="../media/create-and-manage/request-exclusion.png" alt-text="Schermopname van de optie Uitsluitingen op de pagina Initiatieftoewijzing waarmee een uitgesloten resourcegroep kan worden toegevoegd aan de beleidstoewijzing." border="false":::

   > [!NOTE]
   > Afhankelijk van de beleidsdefinitie en de invloed ervan, kan de uitsluiting ook worden toegekend aan specifieke resources binnen een resourcegroep binnen het bereik van de toewijzing. Als een **Weigeren**-effect is gebruikt in deze zelfstudie, zou het niet verstandig zijn om de uitsluiting in te stellen op een specifieke resource die al bestaat.

1. Selecteer **Beoordelen en opslaan** en selecteer vervolgens **Opslaan**.

In deze sectie hebt u de geweigerde aanvraag toch uitgevoerd door een uitsluiting te maken in één resourcegroep.

## <a name="clean-up-resources"></a>Resources opschonen

Als u niet wilt doorgaan, gebruikt u de volgende stappen om alle bovenstaande beleidstoewijzingen of -definities te verwijderen:

1. Selecteer **Definities** (of **Toewijzingen** als u een toewijzing wilt verwijderen) onder **Ontwerpen** in het linkerdeelvenster van de Azure Policy-pagina.

1. Zoek naar de nieuwe initiatief- of beleidsdefinitie (of toewijzing) die u zojuist hebt gemaakt.

1. Klik met de rechtermuisknop op de rij of selecteer de weglatingstekens aan het einde van de definitie (of de toewijzing) en selecteer **Definitie verwijderen** (of **Toewijzing verwijderen**).

## <a name="review"></a>Beoordelen

In deze zelfstudie hebt u de volgende taken uitgevoerd:

> [!div class="checklist"]
> - Een beleid toegewezen voor het afdwingen van een voorwaarde voor bronnen die u in de toekomst maakt
> - Een initiatiefdefinitie gemaakt en toegewezen om naleving bij te houden voor meerdere bronnen
> - Problemen met een bron die niet voldoet of is geweigerd, opgelost
> - Een nieuw beleid binnen een organisatie geïmplementeerd

## <a name="next-steps"></a>Volgende stappen

Lees het volgende artikel voor meer informatie over de structuur van beleidsdefinities:

> [!div class="nextstepaction"]
> [Structuur van Azure Policy-definities](../concepts/definition-structure.md)