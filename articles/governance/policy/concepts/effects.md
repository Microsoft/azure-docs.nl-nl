---
title: Begrijpen hoe effecten werken
description: Azure Policy hebben verschillende effecten die bepalen hoe naleving wordt beheerd en gerapporteerd.
ms.date: 04/19/2021
ms.topic: conceptual
ms.openlocfilehash: e0d6eb5fb37ecf1b13edd945de52398b1e12f192
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861549"
---
# <a name="understand-azure-policy-effects"></a>Inzicht in Azure Policy effecten

Elke beleidsdefinitie in Azure Policy heeft één effect. Dit effect bepaalt wat er gebeurt wanneer de beleidsregel wordt geëvalueerd als overeenkomend. De effecten gedragen zich anders als ze voor een nieuwe resource, een bijgewerkte resource of een bestaande resource zijn.

Deze effecten worden momenteel ondersteund in een beleidsdefinitie:

- [Toevoegen](#append)
- [Controleren](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [Weigeren](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Uitgeschakeld](#disabled)
- [Wijzigen](#modify)

De volgende effecten _zijn afgeschaft:_

- [EnforceOPAConstraint](#enforceopaconstraint)
- [EnforceRegoPolicy](#enforceregopolicy)

> [!IMPORTANT]
> In plaats van de **effecten EnforceOPAConstraint** of **EnforceRegoPolicy** gebruikt u _controleren_ en _weigeren met_ de resourceprovidermodus `Microsoft.Kubernetes.Data` . De ingebouwde beleidsdefinities zijn bijgewerkt. Wanneer bestaande beleidstoewijzingen van deze ingebouwde beleidsdefinities worden gewijzigd, moet de _parameter effect_ worden gewijzigd in een waarde in de _bijgewerkte lijst allowedValues._

## <a name="order-of-evaluation"></a>Volgorde van evaluatie

Aanvragen voor het maken of bijwerken van een resource worden eerst Azure Policy geëvalueerd. Azure Policy maakt een lijst met alle toewijzingen die van toepassing zijn op de resource en evalueert vervolgens de resource op elke definitie. Voor een [Resource Manager worden](./definition-structure.md#resource-manager-modes)Azure Policy verschillende effecten verwerkt voordat de aanvraag wordt ingediend bij de juiste resourceprovider. Deze volgorde voorkomt onnodige verwerking door een resourceprovider wanneer een resource niet voldoet aan de ontworpen beheerselementen van Azure Policy. Met de [resourceprovidermodus beheert](./definition-structure.md#resource-provider-modes)de resourceprovider de evaluatie en het resultaat en rapporteert deze de resultaten Azure Policy.

- **Uitgeschakeld** wordt eerst gecontroleerd om te bepalen of de beleidsregel moet worden geëvalueerd.
- **Vervolgens worden Append** **en Modify** geëvalueerd. Aangezien een van beide de aanvraag kan wijzigen, kan een aangebrachte wijziging verhinderen dat een controle- of weigereffect wordt veroorzaakt. Deze effecten zijn alleen beschikbaar met een Resource Manager modus.
- **Weigeren** wordt vervolgens geëvalueerd. Door weigeren vóór controle te evalueren, wordt dubbele logboekregistratie van een ongewenste resource voorkomen.
- **Controle** wordt als laatste geëvalueerd.

Nadat de resourceprovider een succescode retourneert voor een Resource Manager-modusaanvraag, evalueren **AuditIfNotExists** en **DeployIfNotExists** om te bepalen of er aanvullende nalevingslogboekregistratie of -actie is vereist.

Bovendien beperken aanvragen die alleen gerelateerde velden wijzigen de beleidsevaluatie tot beleidsregels met `PATCH` voorwaarden die gerelateerde velden `tags` `tags` inspecteren.

## <a name="append"></a>Toevoegen

Toevoegen wordt gebruikt om extra velden toe te voegen aan de aangevraagde resource tijdens het maken of bijwerken. Een veelvoorkomende voorbeeld is het opgeven van toegestane IP's voor een opslagresource.

> [!IMPORTANT]
> Toevoegen is bedoeld voor gebruik met niet-tageigenschappen. Terwijl Toevoegen tags aan een resource kan toevoegen tijdens een aanvraag voor maken of bijwerken, is het raadzaam om in plaats daarvan het [effect](#modify) Wijzigen voor tags te gebruiken.

### <a name="append-evaluation"></a>Evaluatie van append

De app-waarde evalueert voordat de aanvraag wordt verwerkt door een resourceprovider tijdens het maken of bijwerken van een resource. Als aan de **if-voorwaarde** van de beleidsregel wordt voldaan, voegt u velden toe aan de resource. Als het toevoegen-effect een waarde in de oorspronkelijke aanvraag zou overschrijven met een andere waarde, fungeert dit als een weigeringseffect en wordt de aanvraag afgewezen. Als u een nieuwe waarde wilt toevoegen aan een bestaande matrix, gebruikt u **\[\*\]** de versie van de alias.

Wanneer een beleidsdefinitie die gebruik maakt van het append-effect wordt uitgevoerd als onderdeel van een evaluatiecyclus, worden er geen wijzigingen aangebracht in resources die al bestaan. In plaats daarvan wordt elke resource die voldoet aan de **if-voorwaarde** als niet-compatibel markeert.

### <a name="append-properties"></a>Eigenschappen van append

Een append-effect heeft alleen een **matrix met details.** Dit is vereist. Omdat **details** een matrix zijn, kan er één **veld/waarde-paar** of meerdere waarden nodig zijn. Raadpleeg [definitiestructuur voor](definition-structure.md#fields) de lijst met acceptabele velden.

### <a name="append-examples"></a>Voorbeelden van append

Voorbeeld 1: Eén **veld/waarde-paar met** een niet-alias met een **\[\*\]** 
 [matrixwaarde](definition-structure.md#aliases) om IP-regels in te stellen voor een opslagaccount.  Wanneer de niet-alias een matrix is, wordt de waarde door het effect als de **\[\*\]** hele matrix aan de waarde toevoegen.  Als de matrix al bestaat, treedt er een deny-gebeurtenis op vanuit het conflict.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
        "value": [{
            "action": "Allow",
            "value": "134.5.0.0/21"
        }]
    }]
}
```

Voorbeeld 2: Een **veld/waarde-paar met** een alias met een matrixwaarde om **\[\*\]** [](definition-structure.md#aliases) IP-regels in te stellen voor een opslagaccount.  Met behulp van de alias wordt de waarde door het effect aan een mogelijk bestaande matrix **\[\*\]** toevoegen.  Als de matrix nog niet bestaat, wordt deze gemaakt.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]",
        "value": {
            "value": "40.40.40.40",
            "action": "Allow"
        }
    }]
}
```

## <a name="audit"></a>Controleren

Controle wordt gebruikt om een waarschuwingsgebeurtenis in het activiteitenlogboek te maken bij het evalueren van een niet-compatibele resource, maar de aanvraag wordt niet gestopt.

### <a name="audit-evaluation"></a>Controle-evaluatie

Controle is het laatste effect dat wordt gecontroleerd door Azure Policy tijdens het maken of bijwerken van een resource. Voor een Resource Manager modus verzendt Azure Policy vervolgens de resource naar de resourceprovider. Controle werkt hetzelfde voor een resourceaanvraag en een evaluatiecyclus. Voor nieuwe en bijgewerkte resources voegt Azure Policy bewerking toe aan het activiteitenlogboek en markeert de `Microsoft.Authorization/policies/audit/action` resource als niet-compatibel.

### <a name="audit-properties"></a>Controle-eigenschappen

Voor een Resource Manager-modus heeft het controle-effect geen aanvullende eigenschappen voor gebruik in de **voorwaarde** van de beleidsdefinitie.

Voor de resourceprovidermodus `Microsoft.Kubernetes.Data` van heeft het controle-effect de volgende aanvullende subproperties met **details.**

- **constraintTemplate** (vereist)
  - De beperkingssjabloon CustomResourceDefinition (CRD) die nieuwe beperkingen definieert. De sjabloon definieert de Rego-logica, het beperkingsschema en de beperkingsparameters die worden doorgegeven via **waarden** van Azure Policy.
- **beperking** (vereist)
  - De CRD-implementatie van de sjabloon Beperking. Gebruikt parameters die worden doorgegeven via **-waarden** als `{{ .Values.<valuename> }}` . In voorbeeld 2 hieronder zijn deze waarden `{{ .Values.excludedNamespaces }}` en `{{ .Values.allowedContainerImagesRegex }}` .
- **waarden** (optioneel)
  - Definieert parameters en waarden die moeten worden door geven aan de Beperking. Elke waarde moet aanwezig zijn in de beperking sjabloon CRD.

### <a name="audit-example"></a>Voorbeeld van controle

Voorbeeld 1: Het controle-effect voor de Resource Manager gebruiken.

```json
"then": {
    "effect": "audit"
}
```

Voorbeeld 2: Het controle-effect gebruiken voor een resourceprovidermodus van `Microsoft.Kubernetes.Data` . De aanvullende informatie in details **definieert** de sjabloon Beperkingen en de CRD die moeten worden gebruikt in Kubernetes om de toegestane containerafbeeldingen te beperken.

```json
"then": {
    "effect": "audit",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/constraint.yaml",
        "values": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]",
            "excludedNamespaces": "[parameters('excludedNamespaces')]"
        }
    }
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists maakt het mogelijk om _resources_ te controleren die betrekking hebben op de resource die overeenkomt met de **if-voorwaarde,** maar die niet de eigenschappen hebben die zijn opgegeven in de **details** van de **voorwaarde.**

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists-evaluatie

AuditIfNotExists wordt uitgevoerd nadat een resourceprovider een resourceaanvraag voor maken of bijwerken heeft verwerkt en de statuscode geslaagd heeft geretourneerd. De controle vindt plaats als er geen gerelateerde resources zijn of als de resources die zijn gedefinieerd door **ExistenceCondition** niet worden geëvalueerd als waar. Voor nieuwe en bijgewerkte resources voegt Azure Policy bewerking toe aan het activiteitenlogboek en markeert `Microsoft.Authorization/policies/audit/action` de resource als niet-compatibel. Wanneer deze wordt geactiveerd, is de resource die voldoet aan **de if-voorwaarde** de resource die is gemarkeerd als niet-compatibel.

### <a name="auditifnotexists-properties"></a>AuditIfNotExists-eigenschappen

De **eigenschap details** van de effecten AuditIfNotExists heeft alle subeigen eigenschappen die de gerelateerde resources definiëren die moeten overeenkomen.

- **Type** (vereist)
  - Hiermee geeft u het type van de gerelateerde resource overeen te komen.
  - Als **details.type** een resourcetype is onder de if-voorwaarderesource, vraagt het beleid naar resources van dit  **type** binnen het bereik van de geëvalueerde resource. Anders worden beleidsquery's binnen dezelfde resourcegroep als de geëvalueerde resource opgevraagd.
- **Naam** (optioneel)
  - Hiermee geeft u de exacte naam van de resource overeen en zorgt ervoor dat het beleid één specifieke resource op te halen in plaats van alle resources van het opgegeven type.
  - Wanneer de voorwaardewaarden voor **if.field.type** en **then.details.type** overeenkomen, wordt **Naam** _vereist_ en moet `[field('name')]` dat zijn, of `[field('fullName')]` voor een onderliggende resource.
    In plaats daarvan [moet echter](#audit) een controle-effect worden overwogen.
- **ResourceGroupName** (optioneel)
  - Hiermee kan de overeenkomende resource afkomstig zijn uit een andere resourcegroep.
  - Is niet van toepassing als **type** een resource is die zich onder de **resource voor de if-voorwaarde** zou moeten houden.
  - De standaardwaarde is **de** resourcegroep van de if-voorwaarde.
- **ExistenceScope** (optioneel)
  - Toegestane waarden _zijn Subscription_ en _ResourceGroup._
  - Hiermee stelt u het bereik in van waar de gerelateerde resource moet worden opgehaald om vandaan te komen.
  - Is niet van toepassing als **type** een resource is die zich onder de **resource voor de if-voorwaarde** zou moeten houden.
  - Voor _ResourceGroup_ wordt beperkt tot de resourcegroep van de if-voorwaarderesource of de resourcegroep die is opgegeven in **ResourceGroupName.** 
  - Voor _Abonnement_ wordt het hele abonnement voor de gerelateerde resource opgevraagd.
  - De standaardwaarde is _ResourceGroup._
- **ExistenceCondition** (optioneel)
  - Als dit niet wordt opgegeven, voldoet een gerelateerde resource van **het type** aan het effect en wordt de controle niet getypt.
  - Gebruikt dezelfde taal als de beleidsregel voor de **if-voorwaarde,** maar wordt afzonderlijk geëvalueerd voor elke gerelateerde resource.
  - Als een overeenkomende gerelateerde resource wordt geëvalueerd als waar, wordt aan het effect voldaan en wordt de controle niet uitgevoerd.
  - Kan [field()] gebruiken om de equivalentie met waarden in de **if-voorwaarde te** controleren.
  - Kan bijvoorbeeld worden gebruikt om te valideren  dat de bovenliggende resource (in de if-voorwaarde) zich op dezelfde resourcelocatie bevindt als de overeenkomende gerelateerde resource.

### <a name="auditifnotexists-example"></a>Voorbeeld van AuditIfNotExists

Voorbeeld: evalueert Virtual Machines om te bepalen of de Antimalware-extensie bestaat en controleert wanneer deze ontbreekt.

```json
{
    "if": {
        "field": "type",
        "equals": "Microsoft.Compute/virtualMachines"
    },
    "then": {
        "effect": "auditIfNotExists",
        "details": {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "existenceCondition": {
                "allOf": [{
                        "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                        "equals": "Microsoft.Azure.Security"
                    },
                    {
                        "field": "Microsoft.Compute/virtualMachines/extensions/type",
                        "equals": "IaaSAntimalware"
                    }
                ]
            }
        }
    }
}
```

## <a name="deny"></a>Weigeren

Weigeren wordt gebruikt om een resourceaanvraag te voorkomen die niet voldoet aan gedefinieerde standaarden via een beleidsdefinitie en mislukt de aanvraag.

### <a name="deny-evaluation"></a>Evaluatie weigeren

Bij het maken of bijwerken van een overeenkomende resource in Resource Manager modus, voorkomt weigeren dat de aanvraag wordt verzonden naar de resourceprovider. De aanvraag wordt geretourneerd als een `403 (Forbidden)` . In de portal kan Verboden worden bekeken als een status voor de implementatie die is voorkomen door de beleidstoewijzing. Voor een resourceprovidermodus beheert de resourceprovider de evaluatie van de resource.

Tijdens de evaluatie van bestaande resources worden resources die overeenkomen met een beleidsdefinitie voor weigeren gemarkeerd als niet-compatibel.

### <a name="deny-properties"></a>Eigenschappen weigeren

Voor een Resource Manager-modus heeft het weigereffect geen aanvullende eigenschappen voor gebruik in de **voorwaarde** van de beleidsdefinitie.

Voor de resourceprovidermodus van heeft het weigereffect de volgende `Microsoft.Kubernetes.Data` aanvullende subproperties met **details.**

- **constraintTemplate** (vereist)
  - De beperkingssjabloon CustomResourceDefinition (CRD) die nieuwe beperkingen definieert. De sjabloon definieert de Rego-logica, het beperkingsschema en de beperkingsparameters die worden doorgegeven via **waarden** van Azure Policy.
- **beperking** (vereist)
  - De CRD-implementatie van de sjabloon Beperking. Gebruikt parameters die via waarden **worden doorgegeven** als `{{ .Values.<valuename> }}` . In voorbeeld 2 hieronder zijn deze waarden `{{ .Values.excludedNamespaces }}` en `{{ .Values.allowedContainerImagesRegex }}` .
- **waarden** (optioneel)
  - Definieert parameters en waarden die moeten worden doorgeven aan de beperking. Elke waarde moet bestaan in de beperkingssjabloon CRD.

### <a name="deny-example"></a>Voorbeeld weigeren

Voorbeeld 1: Het weigeren-effect gebruiken voor Resource Manager modi.

```json
"then": {
    "effect": "deny"
}
```

Voorbeeld 2: Het weigeren-effect gebruiken voor een resourceprovidermodus van `Microsoft.Kubernetes.Data` . De aanvullende  informatie in details definieert de sjabloon Beperkingen en de CRD die moeten worden gebruikt in Kubernetes om de toegestane containerafbeeldingen te beperken.

```json
"then": {
    "effect": "deny",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/constraint.yaml",
        "values": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]",
            "excludedNamespaces": "[parameters('excludedNamespaces')]"
        }
    }
}
```

## <a name="deployifnotexists"></a>DeployIfNotExists

Net als bij AuditIfNotExists voert een DeployIfNotExists-beleidsdefinitie een sjabloonimplementatie uit wanneer aan de voorwaarde wordt voldaan.

> [!NOTE]
> [Geneste sjablonen](../../../azure-resource-manager/templates/linked-templates.md#nested-template) worden ondersteund met **deployIfNotExists,** maar [gekoppelde sjablonen](../../../azure-resource-manager/templates/linked-templates.md#linked-template) worden momenteel niet ondersteund.

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists-evaluatie

DeployIfNotExists wordt ongeveer 15 minuten uitgevoerd nadat een resourceprovider een abonnement of resourceaanvraag voor maken of bijwerken heeft verwerkt en een statuscode voor succes heeft geretourneerd. Een sjabloonimplementatie vindt plaats als er geen gerelateerde resources zijn of als de resources die zijn gedefinieerd door **ExistenceCondition** niet worden geëvalueerd als waar. De duur van de implementatie is afhankelijk van de complexiteit van de resources die zijn opgenomen in de sjabloon.

Tijdens een evaluatiecyclus worden beleidsdefinities met een DeployIfNotExists-effect die overeenkomen met resources gemarkeerd als niet-compatibel, maar wordt er geen actie ondernomen op die resource. Bestaande niet-compatibele resources kunnen worden gesaneerd met een [hersteltaak](../how-to/remediate-resources.md).

### <a name="deployifnotexists-properties"></a>Eigenschappen deployIfNotExists

De **eigenschap details** van het effect DeployIfNotExists bevat alle subproperties die de gerelateerde resources definiëren die overeenkomen en de sjabloonimplementatie die moet worden uitgevoerd.

- **Type** (vereist)
  - Hiermee geeft u het type van de gerelateerde resource overeen te komen.
  - Begint met het ophalen van  een resource onder de if-voorwaarderesource en vervolgens query's binnen dezelfde resourcegroep als de if-voorwaarderesource. 
- **Naam** (optioneel)
  - Hiermee geeft u de exacte naam van de resource overeen en zorgt ervoor dat het beleid één specifieke resource op te halen in plaats van alle resources van het opgegeven type.
  - Wanneer de voorwaardewaarden voor **if.field.type** en **then.details.type** overeenkomen, wordt **Naam** _vereist_ en moet dat zijn, of `[field('name')]` voor een `[field('fullName')]` onderliggende resource.
- **ResourceGroupName** (optioneel)
  - Hiermee kan de overeenkomende resource afkomstig zijn uit een andere resourcegroep.
  - Is niet van toepassing als **type** een resource is die zich onder de **resource if-voorwaarde** zou houden.
  - De standaardwaarde is **de** resourcegroep if condition van de resource.
  - Als een sjabloonimplementatie wordt uitgevoerd, wordt deze geïmplementeerd in de resourcegroep van deze waarde.
- **ExistenceScope** (optioneel)
  - Toegestane waarden _zijn Subscription_ en _ResourceGroup._
  - Hiermee stelt u het bereik in van waar de gerelateerde resource moet worden opgehaald om vandaan te komen.
  - Is niet van toepassing als **type** een resource is die zich onder de **resource if-voorwaarde** zou houden.
  - Voor _ResourceGroup_ wordt beperkt tot de resourcegroep **if** condition of de resourcegroep die is opgegeven in **ResourceGroupName.**
  - Bij _Abonnement wordt_ het hele abonnement voor de gerelateerde resource opgevraagd.
  - De standaardwaarde is _ResourceGroup._
- **ExistenceCondition** (optioneel)
  - Als dit niet is opgegeven, voldoet een gerelateerde resource van **het type** aan het effect en wordt de implementatie niet getypt.
  - Gebruikt dezelfde taal als de beleidsregel voor de **if-voorwaarde,** maar wordt afzonderlijk geëvalueerd voor elke gerelateerde resource.
  - Als een overeenkomende gerelateerde resource wordt geëvalueerd als waar, wordt aan het effect voldaan en wordt de implementatie niet activeerd.
  - Kan [field()] gebruiken om de equivalentie met waarden in de **if-voorwaarde te** controleren.
  - Kan bijvoorbeeld worden gebruikt om te valideren  dat de bovenliggende resource (in de if-voorwaarde) zich op dezelfde resourcelocatie bevindt als de overeenkomende gerelateerde resource.
- **roleDefinitionIds** (vereist)
  - Deze eigenschap moet een matrix met tekenreeksen bevatten die overeenkomen met de rol-id voor op rollen gebaseerd toegangsbeheer die toegankelijk zijn voor het abonnement. Zie Herstel - beleidsdefinitie [configureren voor meer informatie.](../how-to/remediate-resources.md#configure-policy-definition)
- **DeploymentScope** (optioneel)
  - Toegestane waarden _zijn Subscription_ en _ResourceGroup._
  - Hiermee stelt u het type implementatie moet worden geactiveerd. _Abonnement_ geeft een implementatie [op abonnementsniveau aan,](../../../azure-resource-manager/templates/deploy-to-subscription.md) _ResourceGroup_ geeft een implementatie naar een resourcegroep aan.
  - Een _locatie-eigenschap_ moet worden opgegeven in de _implementatie bij het_ gebruik van implementaties op abonnementsniveau.
  - De standaardwaarde is _ResourceGroup._
- **Implementatie** (vereist)
  - Deze eigenschap moet de volledige sjabloonimplementatie bevatten zoals deze wordt doorgegeven aan de `Microsoft.Resources/deployments` PUT-API. Zie implementaties voor [meer informatie REST API](/rest/api/resources/deployments).

  > [!NOTE]
  > Alle functies in de **eigenschap Implementatie** worden geëvalueerd als onderdelen van de sjabloon, niet als het beleid. De uitzondering hierop is de **eigenschap parameters** die waarden van het beleid door geeft aan de sjabloon. De **waarde** in deze sectie onder de naam van een sjabloonparameter wordt gebruikt om deze waarde door te geven (zie _fullDbName_ in het deployIfNotExists-voorbeeld).

### <a name="deployifnotexists-example"></a>Voorbeeld van DeployIfNotExists

Voorbeeld: evalueert SQL Server om te bepalen of transparentDataEncryption is ingeschakeld. Zo niet, dan wordt een implementatie uitgevoerd die moet worden ingeschakeld.

```json
"if": {
    "field": "type",
    "equals": "Microsoft.Sql/servers/databases"
},
"then": {
    "effect": "DeployIfNotExists",
    "details": {
        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
        "name": "current",
        "roleDefinitionIds": [
            "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
            "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
        ],
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "fullDbName": {
                            "type": "string"
                        }
                    },
                    "resources": [{
                        "name": "[concat(parameters('fullDbName'), '/current')]",
                        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
                        "apiVersion": "2014-04-01",
                        "properties": {
                            "status": "Enabled"
                        }
                    }]
                },
                "parameters": {
                    "fullDbName": {
                        "value": "[field('fullName')]"
                    }
                }
            }
        }
    }
}
```

## <a name="disabled"></a>Uitgeschakeld

Dit effect is handig voor het testen van situaties of wanneer de beleidsdefinitie het effect heeft geparameteriseerd. Deze flexibiliteit maakt het mogelijk om één toewijzing uit te schakelen in plaats van alle toewijzingen van dat beleid uit te schakelen.

Een alternatief voor het effect Uitgeschakeld is **enforcementMode,** dat is ingesteld op de beleidstoewijzing.
Wanneer **enforcementMode** is _uitgeschakeld,_ worden resources nog steeds geëvalueerd. Logboekregistratie, zoals activiteitenlogboeken, en het beleidseffect worden niet uitgevoerd. Zie beleidstoewijzing [- afdwingingsmodus voor meer informatie.](./assignment-structure.md#enforcement-mode)

## <a name="enforceopaconstraint"></a>EnforceOPAConstraint

Dit effect wordt gebruikt met de _beleidsdefinitiemodus_ `Microsoft.Kubernetes.Data` . Het wordt gebruikt om Gatekeeper v3-toegangsbeheerregels die zijn gedefinieerd met [OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) door te geven aan [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) aan Kubernetes-clusters in Azure.

> [!IMPORTANT]
> De beperkte preview-beleidsdefinities met het effect **EnforceOPAConstraint** en de gerelateerde **categorie Kubernetes Service** _zijn afgeschaft._ Gebruik in plaats daarvan de _effectencontrole en_ _weigeren met_ de resourceprovidermodus `Microsoft.Kubernetes.Data` .

### <a name="enforceopaconstraint-evaluation"></a>EnforceOPAConstraint-evaluatie

De toegangscontroller van de Open Policy Agent evalueert elke nieuwe aanvraag op het cluster in realtime.
Om de 15 minuten wordt een volledige scan van het cluster uitgevoerd en worden de resultaten gerapporteerd aan Azure Policy.

### <a name="enforceopaconstraint-properties"></a>Eigenschappen van EnforceOPAConstraint

De **eigenschap details** van het effect EnforceOPAConstraint heeft de subproperties die de toegangsbeheerregel gatekeeper v3 beschrijven.

- **constraintTemplate** (vereist)
  - De sjabloon Voor beperkingen CustomResourceDefinition (CRD) die nieuwe beperkingen definieert. De sjabloon definieert de Rego-logica, het beperkingsschema en de beperkingsparameters die worden doorgegeven via **waarden** van Azure Policy.
- **beperking** (vereist)
  - De CRD-implementatie van de sjabloon Beperking. Gebruikt parameters die worden doorgegeven via **waarden** als `{{ .Values.<valuename> }}` . In het onderstaande voorbeeld zijn deze waarden `{{ .Values.cpuLimit }}` en `{{ .Values.memoryLimit }}` .
- **waarden** (optioneel)
  - Definieert parameters en waarden die moeten worden door geven aan de beperking. Elke waarde moet bestaan in de beperkingssjabloon CRD.

### <a name="enforceopaconstraint-example"></a>Voorbeeld EnforceOPAConstraint

Voorbeeld: Gatekeeper v3-toegangsbeheerregel om cpu- en geheugenresourcelimieten voor containers in Te stellen in Kubernetes.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "in": [
                "Microsoft.ContainerService/managedClusters",
                "AKS Engine"
            ]
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "enforceOPAConstraint",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/constraint.yaml",
        "values": {
            "cpuLimit": "[parameters('cpuLimit')]",
            "memoryLimit": "[parameters('memoryLimit')]"
        }
    }
}
```

## <a name="enforceregopolicy"></a>EnforceRegoPolicy

Dit effect wordt gebruikt met een _beleidsdefinitiemodus_ van `Microsoft.ContainerService.Data` . Deze wordt gebruikt om Gatekeeper v2-toegangsbeheerregels die zijn gedefinieerd met [Rego,](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) door te geven aan [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) op [Azure Kubernetes Service](../../../aks/intro-kubernetes.md).

> [!IMPORTANT]
> De beperkte preview-beleidsdefinities met het effect **EnforceRegoPolicy** en de gerelateerde **Kubernetes** _Service-categorie zijn afgeschaft._ Gebruik in plaats daarvan de _effectencontrole en_ _weigeren met_ de resourceprovidermodus `Microsoft.Kubernetes.Data` .

### <a name="enforceregopolicy-evaluation"></a>EnforceRegoPolicy-evaluatie

De toegangscontroller Open Policy Agent evalueert elke nieuwe aanvraag op het cluster in realtime.
Om de 15 minuten wordt een volledige scan van het cluster uitgevoerd en worden de resultaten gerapporteerd aan Azure Policy.

### <a name="enforceregopolicy-properties"></a>EnforceRegoPolicy-eigenschappen

De **eigenschap details** van het effect EnforceRegoPolicy heeft de subeigenschapen die de toegangsbeheerregel Gatekeeper v2 beschrijven.

- **policyId** (vereist)
  - Een unieke naam die als parameter wordt doorgegeven aan de rego-toegangsbeheerregel.
- **beleid** (vereist)
  - Hiermee geeft u de URI van de Rego-toegangsbeheerregel op.
- **policyParameters** (optioneel)
  - Definieert parameters en waarden die moeten worden doorgeven aan het beleid voor opnieuw instellen.

### <a name="enforceregopolicy-example"></a>Voorbeeld van EnforceRegoPolicy

Voorbeeld: Gatekeeper v2-toegangsbeheerregel om alleen de opgegeven containerafbeeldingen in AKS toe te staan.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.ContainerService/managedClusters"
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "EnforceRegoPolicy",
    "details": {
        "policyId": "ContainerAllowedImages",
        "policy": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/KubernetesService/container-allowed-images/limited-preview/gatekeeperpolicy.rego",
        "policyParameters": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]"
        }
    }
}
```

## <a name="modify"></a>Wijzigen

Wijzigen wordt gebruikt om eigenschappen of tags aan een abonnement of resource toe te voegen, bij te werken of te verwijderen tijdens het maken of bijwerken. Een veelvoorkomende voorbeeld is het bijwerken van tags voor resources zoals costCenter. Bestaande niet-compatibele resources kunnen worden herstelt met een [hersteltaak](../how-to/remediate-resources.md). Eén regel wijzigen kan een groot aantal bewerkingen hebben.

De volgende bewerkingen worden ondersteund door Modify:

- Resourcetags toevoegen, vervangen of verwijderen. Voor tags moet het beleid Wijzigen zijn `mode` ingesteld op _Geïndexeerd,_ tenzij de doelresource een resourcegroep is.
- De waarde van het type beheerde identiteit () van virtuele machines en `identity.type` virtuele-machineschaalsets toevoegen of vervangen.
- Voeg de waarden van bepaalde aliassen toe of vervang deze (preview).
  - `Get-AzPolicyAlias | Select-Object -ExpandProperty 'Aliases' | Where-Object { $_.DefaultMetadata.Attributes -eq 'Modifiable' }` gebruiken
    in Azure PowerShell **4.6.0** of hoger om een lijst op te halen met aliassen die kunnen worden gebruikt met Modify.

> [!IMPORTANT]
> Als u tags beheert, is het raadzaam om Wijzigen te gebruiken in plaats van Toevoegen, omdat Wijzigen aanvullende bewerkingstypen biedt en de mogelijkheid om bestaande resources te herstellen. Toevoegen wordt echter aanbevolen als u geen beheerde identiteit kunt maken of als Wijzigen nog geen ondersteuning biedt voor de alias voor de resource-eigenschap.

### <a name="modify-evaluation"></a>Evaluatie wijzigen

Wijzigen evalueert voordat de aanvraag door een resourceprovider wordt verwerkt tijdens het maken of bijwerken van een resource. De bewerkingen Modify worden toegepast op de aanvraaginhoud wanneer aan de **if-voorwaarde** van de beleidsregel wordt voldaan. Elke modify-bewerking kan een voorwaarde opgeven die bepaalt wanneer deze wordt toegepast. Bewerkingen met voorwaarden die worden geëvalueerd als _onwaar worden_ overgeslagen.

Wanneer een alias is opgegeven, worden de volgende extra controles uitgevoerd om ervoor te zorgen dat de bewerking Modify de inhoud van de aanvraag niet zo wijzigt dat de resourceprovider deze weigert:

- De eigenschap die de alias toekent aan is gemarkeerd als 'Wijzigbaar' in de API-versie van de aanvraag.
- Het tokentype in de bewerking Modify komt overeen met het verwachte tokentype voor de eigenschap in de API-versie van de aanvraag.

Als een van deze controles mislukt, valt de beleidsevaluatie terug op het opgegeven **conflictEffect**.

> [!IMPORTANT]
> Het wordt aanbevolen om definities met aliassen   te wijzigen die het effect van controleconflicten gebruiken om te voorkomen dat aanvragen mislukken met behulp van API-versies waarbij de eigenschap 'Wijzigbaar' is. Als dezelfde alias zich anders gedraagt tussen API-versies, kunnen voorwaardelijke wijzigingsbewerkingen worden gebruikt om de wijzigingsbewerking te bepalen die wordt gebruikt voor elke API-versie.

Wanneer een beleidsdefinitie die het effect Wijzigen gebruikt, wordt uitgevoerd als onderdeel van een evaluatiecyclus, worden er geen wijzigingen aangebracht in bestaande resources. In plaats daarvan markeert elke resource die voldoet aan de **if-voorwaarde** als niet-compatibel.

### <a name="modify-properties"></a>Eigenschappen wijzigen

De **eigenschap details** van het effect Wijzigen heeft alle subproperties die de  machtigingen definiëren die nodig zijn voor herstel en de bewerkingen die worden gebruikt om tagwaarden toe te voegen, bij te werken of te verwijderen.

- **roleDefinitionIds** (vereist)
  - Deze eigenschap moet een matrix met tekenreeksen bevatten die overeenkomen met de rol-id van op rollen gebaseerd toegangsbeheer die toegankelijk zijn voor het abonnement. Zie Herstel - [beleidsdefinitie configureren voor meer informatie.](../how-to/remediate-resources.md#configure-policy-definition)
  - De gedefinieerde rol moet alle bewerkingen bevatten die worden verleend aan de [rol Inzender.](../../../role-based-access-control/built-in-roles.md#contributor)
- **conflictEffect** (optioneel)
  - Bepaalt welke beleidsdefinitie 'wint' als meer dan één beleidsdefinitie dezelfde eigenschap wijzigt of wanneer de bewerking Wijzigen niet werkt op de opgegeven alias.
    - Voor nieuwe of bijgewerkte resources heeft de beleidsdefinitie _met weigeren_ prioriteit. Beleidsdefinities _met controle slaan_ alle bewerkingen **over.** Als meer dan één beleidsdefinitie _weigert,_ wordt de aanvraag geweigerd als een conflict. Als alle beleidsdefinities _controle hebben,_ worden geen van **de** bewerkingen van de conflicterende beleidsdefinities verwerkt.
    - Als voor bestaande resources meer dan één beleidsdefinitie _weigert,_ is de nalevingsstatus _Conflict_. Als een of minder beleidsdefinities _weigeren,_ retourneert elke toewijzing de nalevingsstatus _Niet-compatibel._
  - Beschikbare waarden: _controleren,_ _weigeren,_ _uitgeschakeld._
  - De standaardwaarde is _weigeren._
- **bewerkingen** (vereist)
  - Een matrix met alle tagbewerkingen die moeten worden uitgevoerd op overeenkomende resources.
  - Eigenschappen:
    - **bewerking** (vereist)
      - Hiermee definieert u welke actie moet worden ondernomen voor een overeenkomende resource. Opties zijn: _addOrReplace,_ _Add_, _Remove._ _Toevoegen_ gedraagt zich vergelijkbaar met [het](#append) toevoegeffect.
    - **veld** (vereist)
      - De tag die moet worden toevoegen, vervangen of verwijderen. Tagnamen moeten voldoen aan dezelfde naamconventie voor andere [velden.](./definition-structure.md#fields)
    - **waarde** (optioneel)
      - De waarde waar de tag op moet worden ingesteld.
      - Deze eigenschap is vereist als **de bewerking** _addOrReplace_ of _Add is._
    - **voorwaarde** (optioneel)
      - Een tekenreeks met een Azure Policy taalexpressie met [Beleidsfuncties](./definition-structure.md#policy-functions) die als waar of _onwaar_ _worden geëvalueerd._
      - Biedt geen ondersteuning voor de volgende beleidsfuncties: `field()` `resourceGroup()` , , `subscription()` .

### <a name="modify-operations"></a>Bewerkingen wijzigen

De **matrix met** eigenschappen van bewerkingen maakt het mogelijk om verschillende tags op verschillende manieren te wijzigen van één beleidsdefinitie. Elke bewerking bestaat uit **bewerkings-,** **veld-** en **waardeeigenschappen.** De bewerking bepaalt wat de hersteltaak met de tags doet, het veld bepaalt welke tag wordt gewijzigd en de waarde definieert de nieuwe instelling voor die tag. In het onderstaande voorbeeld worden de volgende tagwijzigingen aangebracht:

- Hiermee stelt `environment` u de tag in op 'Testen', zelfs als deze al bestaat met een andere waarde.
- Hiermee verwijdert u de tag `TempResource` .
- Hiermee stelt `Dept` u de tag in op de _beleidsparameter DeptName_ die is geconfigureerd voor de beleidstoewijzing.

```json
"details": {
    ...
    "operations": [
        {
            "operation": "addOrReplace",
            "field": "tags['environment']",
            "value": "Test"
        },
        {
            "operation": "Remove",
            "field": "tags['TempResource']",
        },
        {
            "operation": "addOrReplace",
            "field": "tags['Dept']",
            "value": "[parameters('DeptName')]"
        }
    ]
}
```

De **eigenschap bewerking** heeft de volgende opties:

|Bewerking |Beschrijving |
|-|-|
|addOrReplace |Voegt de gedefinieerde eigenschap of tag en waarde toe aan de resource, zelfs als de eigenschap of tag al met een andere waarde bestaat. |
|Toevoegen |Voegt de gedefinieerde eigenschap of tag en waarde toe aan de resource. |
|Verwijderen |Hiermee verwijdert u de gedefinieerde eigenschap of tag uit de resource. |

### <a name="modify-examples"></a>Voorbeelden wijzigen

Voorbeeld 1: voeg de `environment` tag toe en vervang bestaande tags door `environment` 'Test':

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "Test"
            }
        ]
    }
}
```

Voorbeeld 2: verwijder de tag en voeg de tag toe of `env` vervang bestaande tags door een `environment` `environment` geparameteriseerde waarde:

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "conflictEffect": "deny",
        "operations": [
            {
                "operation": "Remove",
                "field": "tags['env']"
            },
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "[parameters('tagValue')]"
            }
        ]
    }
}
```

Voorbeeld 3: Zorg ervoor dat een opslagaccount geen openbare blobtoegang toestaat. De bewerking Wijzigen wordt alleen toegepast bij het evalueren van aanvragen met API-versie hoger of gelijk aan '2019-04-01':

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/microsoft.authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab"
        ],
        "conflictEffect": "audit",
        "operations": [
            {
                "condition": "[greaterOrEquals(requestContext().apiVersion, '2019-04-01')]",
                "operation": "addOrReplace",
                "field": "Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
                "value": false
            }
        ]
    }
}
```

## <a name="layering-policy-definitions"></a>Beleidsdefinities voor lagen

Een resource kan worden beïnvloed door verschillende toewijzingen. Deze toewijzingen kunnen zich in hetzelfde bereik of op verschillende scopes. Voor elk van deze toewijzingen is waarschijnlijk ook een ander effect gedefinieerd. De voorwaarde en het effect voor elk beleid worden onafhankelijk geëvalueerd. Bijvoorbeeld:

- Beleid 1
  - Beperkt de resourcelocatie tot 'westus'
  - Toegewezen aan abonnement A
  - Effect weigeren
- Beleid 2
  - Beperkt resourcelocatie tot 'eastus'
  - Toegewezen aan resourcegroep B in abonnement A
  - Controle-effect
  
Deze installatie zou het volgende resultaat hebben:

- Alle resources in resourcegroep B in eastus voldoen aan beleid 2 en voldoen niet aan beleid 1
- Een resource die zich al in resourcegroep B niet in 'eastus' heeft, voldoet niet aan beleid 2 en voldoet niet aan beleid 1 als dat niet het geval is in 'westus'
- Elke nieuwe resource in abonnement A die zich niet in 'westus' belandt, wordt geweigerd door beleid 1
- Nieuwe resources in abonnement A en resourcegroep B in 'westus' worden gemaakt en voldoen niet aan beleid 2

Als zowel beleid 1 als beleid 2 het effect van weigeren had, verandert de situatie in:

- Alle resources in resourcegroep B die niet in eastus zijn, voldoen niet aan beleid 2
- Alle resources in resourcegroep B die zich niet in 'westus' hebben, voldoen niet aan beleid 1
- Elke nieuwe resource in abonnement A die zich niet in 'westus' belandt, wordt geweigerd door beleid 1
- Nieuwe resources in resourcegroep B van abonnement A worden geweigerd

Elke toewijzing wordt afzonderlijk geëvalueerd. Daarom is er geen mogelijkheid voor een resource om een hiaat te dichten bij verschillen in bereik. Het nettoresultaat van beleidsdefinities voor lagen wordt beschouwd als **cumulatief meest beperkende**. Als beleid 1 en 2 bijvoorbeeld een weigereffect hebben, wordt een resource geblokkeerd door de overlappende en conflicterende beleidsdefinities. Als u de resource nog steeds moet maken in het doelbereik, controleert u de uitsluitingen voor elke toewijzing om te controleren of de juiste beleidstoewijzingen van invloed zijn op de juiste bereiken.

## <a name="next-steps"></a>Volgende stappen

- Bekijk voorbeelden op [Azure Policy voorbeelden.](../samples/index.md)
- Lees over de [structuur van Azure Policy-definities](definition-structure.md).
- Begrijpen hoe u [programmatisch beleid kunt maken.](../how-to/programmatically-create.md)
- Meer informatie over het [op halen van nalevingsgegevens.](../how-to/get-compliance-data.md)
- Ontdek hoe u [niet-compatibele resources kunt herstellen](../how-to/remediate-resources.md).
- Bekijk wat een beheergroep is met [Uw resources organiseren met Azure-beheergroepen.](../../management-groups/overview.md)
