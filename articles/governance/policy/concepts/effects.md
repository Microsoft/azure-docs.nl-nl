---
title: Inzicht krijgen in de werking van effecten
description: Azure Policy definities hebben verschillende effecten die bepalen hoe de naleving wordt beheerd en gerapporteerd.
ms.date: 10/05/2020
ms.topic: conceptual
ms.openlocfilehash: e72e94766dce2660409e729bc43eb107fb9ab39a
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97883075"
---
# <a name="understand-azure-policy-effects"></a>Azure Policy effecten begrijpen

Elke beleidsdefinitie in Azure Policy heeft één effect. Dit effect bepaalt wat er gebeurt wanneer de beleids regel wordt geëvalueerd voor overeenstemming. De effecten gedragen zich anders als ze voor een nieuwe resource, een bijgewerkte resource of een bestaande resource zijn.

Deze effecten worden momenteel ondersteund in een beleids definitie:

- [Toevoegen](#append)
- [Controleren](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [Weigeren](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Uitgeschakeld](#disabled)
- [Wijzigen](#modify)

De volgende effecten zijn _afgeschaft_:

- [EnforceOPAConstraint](#enforceopaconstraint)
- [EnforceRegoPolicy](#enforceregopolicy)

> [!IMPORTANT]
> In plaats van de **EnforceOPAConstraint** -of **EnforceRegoPolicy** -effecten gebruikt u _controleren_ en _weigeren_ met de resource provider modus `Microsoft.Kubernetes.Data` . De ingebouwde beleids definities zijn bijgewerkt. Wanneer bestaande beleids toewijzingen van deze ingebouwde beleids definities worden gewijzigd, moet de para meter _effect_ worden gewijzigd in een waarde in de lijst met bijgewerkte _allowedValues_ .

## <a name="order-of-evaluation"></a>Volgorde van evaluatie

Aanvragen om een resource te maken of bij te werken worden eerst geëvalueerd door Azure Policy. Azure Policy maakt een lijst met alle toewijzingen die van toepassing zijn op de resource en evalueert vervolgens de resource op basis van elke definitie. Voor een [Resource Manager-modus](./definition-structure.md#resource-manager-modes)worden door Azure Policy verschillende effecten verwerkt voordat de aanvraag aan de juiste resource provider wordt door gegeven. Deze volg orde voor komt onnodige verwerking door een resource provider wanneer een resource niet voldoet aan de ontworpen governance-besturings elementen van Azure Policy. Met een [resource provider modus](./definition-structure.md#resource-provider-modes)beheert de resource provider de evaluatie en het resultaat en rapporteert de resultaten terug naar Azure Policy.

- **Uitgeschakeld** wordt eerst gecontroleerd om te bepalen of de beleids regel moet worden geëvalueerd.
- **Toevoegen** en **wijzigen** worden vervolgens geëvalueerd. Omdat de aanvraag kan worden gewijzigd, is het mogelijk dat er een wijziging is aangebracht waardoor een controle of weigering van een trigger kan worden verhinderd. Deze effecten zijn alleen beschikbaar in de modus Resource Manager.
- De **weigering** wordt vervolgens geëvalueerd. Door weigeren voor controle te evalueren, wordt de dubbele logboek registratie van een ongewenste resource voor komen.
- De **controle** wordt als laatste geëvalueerd.

Nadat de resource provider een succes code heeft geretourneerd in een aanvraag voor de Resource Manager-modus, **AuditIfNotExists** en **DeployIfNotExists** bepalen of aanvullende nalevings logboek registratie of actie vereist is.

Daarnaast wordt `PATCH` met aanvragen die gerelateerde velden alleen wijzigen, de `tags` beleids evaluatie beperkt tot beleids regels met voor waarden die `tags` gerelateerde velden controleren.

## <a name="append"></a>Toevoegen

Append wordt gebruikt om extra velden toe te voegen aan de aangevraagde resource tijdens het maken of bijwerken. Een voor beeld hiervan is het opgeven van toegestane IP-adressen voor een opslag resource.

> [!IMPORTANT]
> Append is bedoeld voor gebruik met niet-label eigenschappen. Bij toevoegen kunnen Tags aan een resource worden toegevoegd tijdens het maken of bijwerken van een aanvraag, maar het is raadzaam om in plaats daarvan het [wijzigings](#modify) effect voor Tags te gebruiken.

### <a name="append-evaluation"></a>Evaluatie toevoegen

Toevoegen evalueert voordat de aanvraag wordt verwerkt door een resource provider tijdens het maken of bijwerken van een resource. Append voegt velden toe aan de resource wanneer aan de **voor waarde van de beleids** regel wordt voldaan. Als het toevoeg effect een waarde in de oorspronkelijke aanvraag overschrijft met een andere waarde, fungeert deze als een weigerings effect en weigert de aanvraag. Gebruik de **\[\*\]** versie van de alias om een nieuwe waarde toe te voegen aan een bestaande matrix.

Wanneer een beleids definitie die gebruikmaakt van het toevoeg effect wordt uitgevoerd als onderdeel van een evaluatie cyclus, worden er geen wijzigingen aangebracht in resources die al bestaan. In plaats daarvan wordt er een resource gemarkeerd die voldoet aan de **if** -voor waarde als niet-compatibel.

### <a name="append-properties"></a>Eigenschappen toevoegen

Een toevoeg effect heeft alleen een **detail** matrix, wat vereist is. Omdat **Details** een matrix is, kan het één **veld/waarde-** paar of meerdere waarden hebben. Raadpleeg de [definitie structuur](definition-structure.md#fields) voor de lijst met geaccepteerde velden.

### <a name="append-examples"></a>Voor beelden toevoegen

Voor beeld 1: een combi natie van één **veld/waarde** met een niet- **\[\*\]** [alias](definition-structure.md#aliases) met een matrix **waarde** om IP-regels in te stellen op een opslag account. Wanneer de niet- **\[\*\]** alias een matrix is, voegt het effect de **waarde** toe als de volledige matrix. Als de matrix al bestaat, treedt er een gebeurtenis deny op van het conflict.

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

Voor beeld 2: een combi natie van één **veld/waarde** met een **\[\*\]** [alias](definition-structure.md#aliases) met een matrix **waarde** om IP-regels in te stellen op een opslag account. Met behulp van de **\[\*\]** alias voegt het effect de **waarde** toe aan een mogelijk vooraf bestaande matrix. Als de matrix nog niet bestaat, wordt deze gemaakt.

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

Controle wordt gebruikt om een waarschuwings gebeurtenis in het activiteiten logboek te maken bij het evalueren van een niet-compatibele resource, maar de aanvraag wordt niet gestopt.

### <a name="audit-evaluation"></a>Controle-evaluatie

Controle is het laatste effect dat door Azure Policy is gecontroleerd tijdens het maken of bijwerken van een resource. Voor een resource manager-modus wordt Azure Policy de resource vervolgens naar de resource provider verzonden. Audit werkt hetzelfde voor een resource aanvraag en een evaluatie cyclus. Voor nieuwe en bijgewerkte resources voegt Azure Policy een `Microsoft.Authorization/policies/audit/action` bewerking aan het activiteiten logboek toe en markeert de resource als niet-compatibel.

### <a name="audit-properties"></a>Controle-eigenschappen

Voor een resource manager-modus bevat het controle-effect geen aanvullende eigenschappen voor gebruik in de **voor waarde voor de beleids** definitie.

Voor de modus van de resource provider van `Microsoft.Kubernetes.Data` heeft het controle-effect de volgende aanvullende subeigenschappen van **Details**.

- **constraintTemplate** (vereist)
  - De beperkings sjabloon CustomResourceDefinition (CRD) waarmee nieuwe beperkingen worden gedefinieerd. De sjabloon definieert de Rego Logic, het beperkings schema en de beperkings parameters die worden door gegeven via **waarden** van Azure Policy.
- **beperking** (vereist)
  - De CRD-implementatie van de beperkings sjabloon. Maakt gebruik van para meters die worden door gegeven via **waarden** als `{{ .Values.<valuename> }}` . In voor beeld 2 hieronder staan deze waarden `{{ .Values.excludedNamespaces }}` en `{{ .Values.allowedContainerImagesRegex }}` .
- **waarden** (optioneel)
  - Hiermee definieert u de para meters en waarden die moeten worden door gegeven aan de beperking. Elke waarde moet bestaan in de beperkings sjabloon CRD.

### <a name="audit-example"></a>Controle voorbeeld

Voor beeld 1: het controle-effect voor de Resource Manager-modus gebruiken.

```json
"then": {
    "effect": "audit"
}
```

Voor beeld 2: het controle-effect voor een resource provider modus van gebruiken `Microsoft.Kubernetes.Data` . De aanvullende informatie in **Details** definieert de beperkings sjabloon en CRD voor gebruik in Kubernetes om de toegestane container installatie kopieën te beperken.

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

AuditIfNotExists maakt het mogelijk om de resources te controleren die zijn _gerelateerd_ aan de resource die overeenkomt met de **if** -voor waarde, maar waarvoor **u** de eigenschappen niet hebt opgegeven in de **Details** van de voor waarde.

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists-evaluatie

AuditIfNotExists wordt uitgevoerd nadat een resource provider een aanvraag voor het maken of bijwerken van een resource heeft verwerkt en een status code voor geslaagd heeft geretourneerd. De controle treedt op als er geen gerelateerde resources zijn of als de resources die zijn gedefinieerd door **ExistenceCondition** niet naar waar worden geëvalueerd. Voor nieuwe en bijgewerkte resources voegt Azure Policy een `Microsoft.Authorization/policies/audit/action` bewerking aan het activiteiten logboek toe en markeert de resource als niet-compatibel. Als deze wordt geactiveerd, is de resource die voldoet aan de **if** -voor waarde de resource die is gemarkeerd als niet-compatibel.

### <a name="auditifnotexists-properties"></a>AuditIfNotExists-eigenschappen

De eigenschap **Details** van de AuditIfNotExists-effecten heeft alle subeigenschappen waarmee de gerelateerde resources worden gedefinieerd.

- **Type** (vereist)
  - Hiermee geeft u het type gerelateerde resource op dat moet worden gezocht.
  - Als **Details. type** een resource type onder de **if** -voor waarde-resource is, wordt in het beleid query's voor bronnen van dit **type** binnen het bereik van de geëvalueerde resource beschreven. Anders worden er beleids query's uitgevoerd binnen dezelfde resource groep als de geëvalueerde resource.
- **Naam** (optioneel)
  - Hiermee geeft u de exacte naam op van de resource die moet worden vergeleken. het beleid zorgt ervoor dat er één specifieke resource wordt opgehaald in plaats van alle resources van het opgegeven type.
  - Wanneer de voorwaarde waarden voor **if. Field. type** en **then. Details. type** overeenkomen, wordt de **naam** _vereist_ en moet deze `[field('name')]` of `[field('fullName')]` voor een onderliggende resource zijn.
    In plaats daarvan moet echter een [controle](#audit) -effect worden overwogen.
- **ResourceGroupName** (optioneel)
  - Hiermee kan het vergelijken van de gerelateerde resource afkomstig zijn uit een andere resource groep.
  - Is niet van toepassing als **type** een resource is die onder de **if** -voor waarde-resource zou vallen.
  - De standaard waarde is de resource groep voor de **indienings** voorwaarde resource.
- **ExistenceScope** (optioneel)
  - Toegestane waarden zijn _abonnements_ -en _ResourceGroup_.
  - Hiermee stelt u het bereik van waar de gerelateerde resource moet worden opgehaald om overeen te komen met.
  - Is niet van toepassing als **type** een resource is die onder de **if** -voor waarde-resource zou vallen.
  - Voor _ResourceGroup_ zou de resource groep van de **if** -voor waarde worden beperkt of de resource groep die is opgegeven in **ResourceGroupName**.
  - Voor het _abonnement_ voert u een query uit op het hele abonnement voor de gerelateerde resource.
  - De standaard waarde is _ResourceGroup_.
- **ExistenceCondition** (optioneel)
  - Als u niets opgeeft, wordt een gerelateerde bron van het **type** voldoet aan het effect en wordt de controle niet geactiveerd.
  - Maakt gebruik van dezelfde taal als de beleids regel voor de **if** -voor waarde, maar wordt voor elke gerelateerde resource afzonderlijk geëvalueerd.
  - Als een overeenkomende gerelateerde resource wordt geëvalueerd als waar, wordt het effect ervan afgehandeld en wordt de controle niet geactiveerd.
  - Kan [Field ()] gebruiken om de gelijkwaardigheid te controleren met waarden in de **if** -voor waarde.
  - Kan bijvoorbeeld worden gebruikt om te controleren of de bovenliggende resource (in de **if** -voor waarde) zich op dezelfde resource locatie bevindt als de overeenkomende gerelateerde resource.

### <a name="auditifnotexists-example"></a>AuditIfNotExists-voor beeld

Voor beeld: Hiermee wordt Virtual Machines geëvalueerd om te bepalen of de uitbrei ding van de uitschakeling van de anti-malware bestaat en wordt gecontroleerd

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

Weigeren wordt gebruikt om te voor komen dat een resource aanvraag die voldoet aan gedefinieerde normen via een beleids definitie, de aanvraag mislukt.

### <a name="deny-evaluation"></a>Evaluatie weigeren

Wanneer u een overeenkomende resource in een resource manager-modus maakt of bijwerkt, voor komt u dat de aanvraag wordt verzonden naar de resource provider. De aanvraag wordt geretourneerd als een `403 (Forbidden)` . In de portal kan de verboden worden weer gegeven als een status voor de implementatie die door de beleids toewijzing is voor komen. De resource provider beheert de evaluatie van de resource voor de modus van de resource provider.

Tijdens de evaluatie van bestaande resources zijn bronnen die overeenkomen met een beleids definitie voor weigeren als niet-compatibel gemarkeerd.

### <a name="deny-properties"></a>Eigenschappen weigeren

Voor een resource manager-modus heeft het deny-effect geen aanvullende eigenschappen voor gebruik in de **voor waarde voor de beleids** definitie.

Voor de modus van de resource provider van `Microsoft.Kubernetes.Data` heeft het deny-effect de volgende aanvullende subeigenschappen van **Details**.

- **constraintTemplate** (vereist)
  - De beperkings sjabloon CustomResourceDefinition (CRD) waarmee nieuwe beperkingen worden gedefinieerd. De sjabloon definieert de Rego Logic, het beperkings schema en de beperkings parameters die worden door gegeven via **waarden** van Azure Policy.
- **beperking** (vereist)
  - De CRD-implementatie van de beperkings sjabloon. Maakt gebruik van para meters die worden door gegeven via **waarden** als `{{ .Values.<valuename> }}` . In voor beeld 2 hieronder staan deze waarden `{{ .Values.excludedNamespaces }}` en `{{ .Values.allowedContainerImagesRegex }}` .
- **waarden** (optioneel)
  - Hiermee definieert u de para meters en waarden die moeten worden door gegeven aan de beperking. Elke waarde moet bestaan in de beperkings sjabloon CRD.

### <a name="deny-example"></a>Voor beeld weigeren

Voor beeld 1: het afwijzen van de Resource Manager-modus gebruiken.

```json
"then": {
    "effect": "deny"
}
```

Voor beeld 2: het weigeren van het effect van een resource provider modus van `Microsoft.Kubernetes.Data` . De aanvullende informatie in **Details** definieert de beperkings sjabloon en CRD voor gebruik in Kubernetes om de toegestane container installatie kopieën te beperken.

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

Net als bij AuditIfNotExists voert een DeployIfNotExists-beleids definitie een sjabloon implementatie uit wanneer aan de voor waarde wordt voldaan.

> [!NOTE]
> [Geneste sjablonen](../../../azure-resource-manager/templates/linked-templates.md#nested-template) worden ondersteund met **deployIfNotExists**, maar [gekoppelde sjablonen](../../../azure-resource-manager/templates/linked-templates.md#linked-template) worden momenteel niet ondersteund.

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists-evaluatie

DeployIfNotExists wordt ongeveer 15 minuten uitgevoerd nadat een resource provider een aanvraag voor het maken of bijwerken van een resource heeft afgehandeld en een status code voor geslaagd heeft geretourneerd. Een sjabloon implementatie treedt op als er geen gerelateerde resources zijn of als de resources die zijn gedefinieerd door **ExistenceCondition** niet naar waar worden geëvalueerd.
De duur van de implementatie is afhankelijk van de complexiteit van de resources die in de sjabloon zijn opgenomen.

Tijdens een evaluatie cyclus worden beleids definities met een DeployIfNotExists-effect dat overeenkomt met resources, gemarkeerd als niet-compatibel, maar wordt er geen actie ondernomen voor die bron. Bestaande niet-compatibele resources kunnen worden hersteld met een [herstel taak](../how-to/remediate-resources.md).

### <a name="deployifnotexists-properties"></a>DeployIfNotExists-eigenschappen

De eigenschap **Details** van het effect DeployIfNotExists heeft alle subeigenschappen waarmee de gerelateerde resources worden gedefinieerd en de sjabloon implementatie moet worden uitgevoerd.

- **Type** (vereist)
  - Hiermee geeft u het type gerelateerde resource op dat moet worden gezocht.
  - U begint met het ophalen van een resource onder de **if** -voor waarde resource en vervolgens query's in dezelfde resource groep als de **indienings** voorwaarde resource.
- **Naam** (optioneel)
  - Hiermee geeft u de exacte naam op van de resource die moet worden vergeleken. het beleid zorgt ervoor dat er één specifieke resource wordt opgehaald in plaats van alle resources van het opgegeven type.
  - Wanneer de voorwaarde waarden voor **if. Field. type** en **then. Details. type** overeenkomen, wordt de **naam** _vereist_ en moet deze `[field('name')]` of `[field('fullName')]` voor een onderliggende resource zijn.
- **ResourceGroupName** (optioneel)
  - Hiermee kan het vergelijken van de gerelateerde resource afkomstig zijn uit een andere resource groep.
  - Is niet van toepassing als **type** een resource is die onder de **if** -voor waarde-resource zou vallen.
  - De standaard waarde is de resource groep voor de **indienings** voorwaarde resource.
  - Als er een sjabloon implementatie wordt uitgevoerd, wordt deze geïmplementeerd in de resource groep van deze waarde.
- **ExistenceScope** (optioneel)
  - Toegestane waarden zijn _abonnements_ -en _ResourceGroup_.
  - Hiermee stelt u het bereik van waar de gerelateerde resource moet worden opgehaald om overeen te komen met.
  - Is niet van toepassing als **type** een resource is die onder de **if** -voor waarde-resource zou vallen.
  - Voor _ResourceGroup_ zou de resource groep van de **if** -voor waarde worden beperkt of de resource groep die is opgegeven in **ResourceGroupName**.
  - Voor het _abonnement_ voert u een query uit op het hele abonnement voor de gerelateerde resource.
  - De standaard waarde is _ResourceGroup_.
- **ExistenceCondition** (optioneel)
  - Als u niets opgeeft, wordt een gerelateerde bron van het **type** voldoet aan het effect en wordt de implementatie niet geactiveerd.
  - Maakt gebruik van dezelfde taal als de beleids regel voor de **if** -voor waarde, maar wordt voor elke gerelateerde resource afzonderlijk geëvalueerd.
  - Als een overeenkomende gerelateerde resource wordt geëvalueerd als waar, wordt het effect voldaan en wordt de implementatie niet geactiveerd.
  - Kan [Field ()] gebruiken om de gelijkwaardigheid te controleren met waarden in de **if** -voor waarde.
  - Kan bijvoorbeeld worden gebruikt om te controleren of de bovenliggende resource (in de **if** -voor waarde) zich op dezelfde resource locatie bevindt als de overeenkomende gerelateerde resource.
- **roleDefinitionIds** (vereist)
  - Deze eigenschap moet een matrix van teken reeksen bevatten die overeenkomen met de op rollen gebaseerde toegangs beheer functie-ID die toegankelijk is voor het abonnement. Zie voor meer informatie [herstel-beleids definitie configureren](../how-to/remediate-resources.md#configure-policy-definition).
- **DeploymentScope** (optioneel)
  - Toegestane waarden zijn _abonnements_ -en _ResourceGroup_.
  - Hiermee stelt u het type implementatie in dat moet worden geactiveerd. Met het _abonnement_ wordt een [implementatie op abonnements niveau](../../../azure-resource-manager/templates/deploy-to-subscription.md)aangegeven. _ResourceGroup_ wijst op een implementatie naar een resource groep.
  - Een _locatie_ -eigenschap moet worden opgegeven in de _implementatie_ bij het gebruik van implementaties op abonnements niveau.
  - De standaard waarde is _ResourceGroup_.
- **Implementatie** (vereist)
  - Deze eigenschap moet de volledige sjabloon implementatie bevatten, aangezien deze wordt door gegeven aan de `Microsoft.Resources/deployments` put-API. Zie [implementaties rest API](/rest/api/resources/deployments)voor meer informatie.

  > [!NOTE]
  > Alle functies in de **implementatie** -eigenschap worden geëvalueerd als onderdelen van de sjabloon, niet het beleid. De uitzonde ring is de eigenschap **para meters** waarmee waarden van het beleid worden door gegeven aan de sjabloon. De **waarde** in deze sectie onder een sjabloon parameter naam wordt gebruikt om deze waarde door te geven (Zie _FullDbName_ in het DeployIfNotExists-voor beeld).

### <a name="deployifnotexists-example"></a>DeployIfNotExists-voor beeld

Voor beeld: Hiermee worden SQL Server data bases geëvalueerd om te bepalen of transparentDataEncryption is ingeschakeld. Als dat niet het geval is, wordt er een implementatie uitgevoerd om in te scha kelen.

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

Dit effect is handig voor het testen van situaties of voor wanneer de beleids definitie van invloed is op de para meter. Deze flexibiliteit maakt het mogelijk een enkele toewijzing uit te scha kelen in plaats van alle toewijzingen van dat beleid uit te scha kelen.

Een alternatief voor het uitgeschakelde effect is **enforcementMode**, dat is ingesteld op de beleids toewijzing.
Wanneer **enforcementMode** is _uitgeschakeld_, worden er nog steeds resources geëvalueerd. Logboek registratie, zoals activiteiten logboeken, en het beleids effect vindt niet plaats. Zie [beleids toewijzing-afdwingings modus](./assignment-structure.md#enforcement-mode)voor meer informatie.

## <a name="enforceopaconstraint"></a>EnforceOPAConstraint

Dit effect wordt gebruikt in combi natie met de beleids definitie _modus_ van `Microsoft.Kubernetes.Data` . Het wordt gebruikt voor het door geven van Gate Keeping-regels voor toegangs beheer die zijn gedefinieerd met [opa CONSTRAINT Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) voor het [openen van beleids agent](https://www.openpolicyagent.org/) (opa) voor Kubernetes-clusters in Azure.

> [!IMPORTANT]
> De beperkte definities van de preview-beleids regels met **EnforceOPAConstraint** -effect en de gerelateerde **Kubernetes-service** categorie zijn _afgeschaft_. Gebruik in plaats daarvan de effecten _controleren_ en _weigeren_ met de resource provider modus `Microsoft.Kubernetes.Data` .

### <a name="enforceopaconstraint-evaluation"></a>EnforceOPAConstraint-evaluatie

De open Policy Agent Admission controller evalueert elke nieuwe aanvraag op het cluster in realtime.
Elke 15 minuten wordt een volledige scan van het cluster voltooid en worden de resultaten gerapporteerd aan Azure Policy.

### <a name="enforceopaconstraint-properties"></a>EnforceOPAConstraint-eigenschappen

De eigenschap **Details** van het effect EnforceOPAConstraint heeft de subeigenschappen waarmee de gate keeper v3 Admission Control-regel wordt beschreven.

- **constraintTemplate** (vereist)
  - De beperkings sjabloon CustomResourceDefinition (CRD) waarmee nieuwe beperkingen worden gedefinieerd. De sjabloon definieert de Rego Logic, het beperkings schema en de beperkings parameters die worden door gegeven via **waarden** van Azure Policy.
- **beperking** (vereist)
  - De CRD-implementatie van de beperkings sjabloon. Maakt gebruik van para meters die worden door gegeven via **waarden** als `{{ .Values.<valuename> }}` . In het onderstaande voor beeld zijn deze waarden `{{ .Values.cpuLimit }}` en `{{ .Values.memoryLimit }}` .
- **waarden** (optioneel)
  - Hiermee definieert u de para meters en waarden die moeten worden door gegeven aan de beperking. Elke waarde moet bestaan in de beperkings sjabloon CRD.

### <a name="enforceopaconstraint-example"></a>EnforceOPAConstraint-voor beeld

Voor beeld: gate keeper v3 Admission Control regel voor het instellen van limieten voor container-CPU en geheugen bronnen in Kubernetes.

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

Dit effect wordt gebruikt in combi natie met de beleids definitie _modus_ van `Microsoft.ContainerService.Data` . Het wordt gebruikt voor het door geven van de regels voor toegangs beheer van gate keeper v2 die zijn gedefinieerd met [Rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) voor het [openen van beleids agent](https://www.openpolicyagent.org/) (opa) op de [Azure Kubernetes-service](../../../aks/intro-kubernetes.md).

> [!IMPORTANT]
> De beperkte definities van de preview-beleids regels met **EnforceRegoPolicy** -effect en de gerelateerde **Kubernetes-service** categorie zijn _afgeschaft_. Gebruik in plaats daarvan de effecten _controleren_ en _weigeren_ met de resource provider modus `Microsoft.Kubernetes.Data` .

### <a name="enforceregopolicy-evaluation"></a>EnforceRegoPolicy-evaluatie

De open Policy Agent Admission controller evalueert elke nieuwe aanvraag op het cluster in realtime.
Elke 15 minuten wordt een volledige scan van het cluster voltooid en worden de resultaten gerapporteerd aan Azure Policy.

### <a name="enforceregopolicy-properties"></a>EnforceRegoPolicy-eigenschappen

De eigenschap **Details** van het effect EnforceRegoPolicy heeft de subeigenschappen waarmee de gate keeper v2 Admission Control-regel wordt beschreven.

- **policyId** (vereist)
  - Een unieke naam die als een para meter wordt door gegeven aan de Rego Admission Control-regel.
- **beleid** (vereist)
  - Hiermee geeft u de URI op van de Rego Admission Control-regel.
- **policyParameters** (optioneel)
  - Hiermee worden alle para meters en waarden gedefinieerd die moeten worden door gegeven aan het Rego-beleid.

### <a name="enforceregopolicy-example"></a>EnforceRegoPolicy-voor beeld

Voor beeld: gate keeper v2 Admission Control regel om alleen de opgegeven container installatie kopieën in AKS toe te staan.

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

Modify wordt gebruikt om eigenschappen of tags toe te voegen, bij te werken of te verwijderen tijdens het maken of bijwerken van een resource.
Een voor beeld hiervan is het bijwerken van tags op resources, zoals costCenter. Bestaande niet-compatibele resources kunnen worden hersteld met een [herstel taak](../how-to/remediate-resources.md). Eén wijzigings regel kan elk wille keurig aantal bewerkingen hebben.

De volgende bewerkingen worden ondersteund door modify:

- Resource Tags toevoegen, vervangen of verwijderen. Voor tags moet een wijzigings beleid zijn `mode` ingesteld op _geïndexeerd_ , tenzij de doel resource een resource groep is.
- De waarde van het beheerde identiteits type ( `identity.type` ) van virtuele machines en virtuele-machine schaal sets toevoegen of vervangen.
- De waarden van bepaalde aliassen (preview-versie) toevoegen of vervangen.
  - `Get-AzPolicyAlias | Select-Object -ExpandProperty 'Aliases' | Where-Object { $_.DefaultMetadata.Attributes -eq 'Modifiable' }` gebruiken
    in Azure PowerShell **4.6.0** of hoger om een lijst met aliassen op te halen die kunnen worden gebruikt met wijzigen.

> [!IMPORTANT]
> Als u labels beheert, is het raadzaam om wijzigen te gebruiken in plaats van toevoegen als wijzigen biedt extra bewerkings typen en de mogelijkheid om bestaande resources te herstellen. Toevoegen wordt echter aanbevolen als u geen beheerde identiteit kunt maken of de alias voor de bron eigenschap niet wilt wijzigen.

### <a name="modify-evaluation"></a>Evaluatie wijzigen

Met modify worden geëvalueerd voordat de aanvraag wordt verwerkt door een resource provider tijdens het maken of bijwerken van een resource. De wijzigings bewerkingen worden toegepast op de aanvraag inhoud wanneer aan de **voor waarde van de beleids** regel wordt voldaan. Elke wijzigings bewerking kan een voor waarde opgeven die bepaalt wanneer deze wordt toegepast. Bewerkingen met voor waarden die zijn geëvalueerd als _Onwaar_ , worden overgeslagen.

Wanneer een alias is opgegeven, worden de volgende aanvullende controles uitgevoerd om ervoor te zorgen dat de wijzigings bewerking de inhoud van de aanvraag niet wijzigt op een manier die ervoor zorgt dat de resource provider deze weigert:

- De eigenschap waarnaar de alias wordt toegewezen, wordt in de API-versie van de aanvraag gemarkeerd als gewijzigd.
- Het token type in de wijzigings bewerking komt overeen met het verwachte token type voor de eigenschap in de API-versie van de aanvraag.

Als een van deze controles mislukt, wordt de evaluatie van het beleid terugvallen op de opgegeven **conflictEffect**.

> [!IMPORTANT]
> Het is met dat definities die aliassen bevatten, gebruikmaken van het _controle_ **conflict effect** om mislukte aanvragen te voor komen met behulp van API-versies waarbij de toegewezen eigenschap niet kan worden gewijzigd. Als dezelfde alias zich anders bevindt dan de API-versies, kunnen voorwaardelijke wijzigings bewerkingen worden gebruikt om te bepalen welke wijzigings bewerking voor elke API-versie wordt gebruikt.

Wanneer een beleids definitie die gebruikmaakt van het Modify-effect, wordt uitgevoerd als onderdeel van een evaluatie cyclus, worden er geen wijzigingen aangebracht in resources die al bestaan. In plaats daarvan wordt er een resource gemarkeerd die voldoet aan de **if** -voor waarde als niet-compatibel.

### <a name="modify-properties"></a>Eigenschappen wijzigen

De eigenschap **Details** van het effect Modify heeft alle subeigenschappen die de machtigingen definiëren die nodig zijn voor herstel en de **bewerkingen** die worden gebruikt om label waarden toe te voegen, bij te werken of te verwijderen.

- **roleDefinitionIds** (vereist)
  - Deze eigenschap moet een matrix van teken reeksen bevatten die overeenkomen met de op rollen gebaseerde toegangs beheer functie-ID die toegankelijk is voor het abonnement. Zie voor meer informatie [herstel-beleids definitie configureren](../how-to/remediate-resources.md#configure-policy-definition).
  - De gedefinieerde rol moet alle bewerkingen bevatten die zijn toegewezen aan de rol [Inzender](../../../role-based-access-control/built-in-roles.md#contributor) .
- **conflictEffect** (optioneel)
  - Hiermee wordt bepaald welke beleids definitie ' WINS ' in het geval van meer dan één beleids definitie dezelfde eigenschap wijzigt of wanneer de wijzigings bewerking niet werkt voor de opgegeven alias.
    - Voor nieuwe of bijgewerkte bronnen heeft de beleids definitie met _weigeren_ prioriteit. Beleids definities met _controle_ alle **bewerkingen** overs Laan. Als er meer dan één beleids definitie is _geweigerd, wordt_ de aanvraag geweigerd als een conflict. Als alle beleids definities _controle_ hebben, worden geen van de **bewerkingen** van de conflicterende beleids definities verwerkt.
    - Voor bestaande bronnen geldt dat als er meerdere beleids regels zijn _geweigerd_, de nalevings status _conflicterend_ is. Als een of minder beleids definities zijn _geweigerd_, retourneert elke toewijzing een nalevings status van _niet-compatibel_.
  - Beschik bare waarden: _controleren_, _weigeren_, _uitgeschakeld_.
  - De standaard waarde is _weigeren_.
- **bewerkingen** (vereist)
  - Een matrix met alle label bewerkingen die moeten worden voltooid voor overeenkomende resources.
  - Eigenschappen:
    - **bewerking** (vereist)
      - Definieert welke actie moet worden uitgevoerd op een overeenkomende resource. Opties zijn: _addOrReplace_, _toevoegen_, _verwijderen_. Een gezichte lijkt op [](#append) het _toevoegen_ van het effect.
    - **veld** (vereist)
      - Het label dat moet worden toegevoegd, vervangen of verwijderd. Label namen moeten voldoen aan dezelfde naam Conventie voor andere [velden](./definition-structure.md#fields).
    - **waarde** (optioneel)
      - De waarde waarop de tag moet worden ingesteld.
      - Deze eigenschap is vereist als  de bewerking _addOrReplace_ of _add_ is.
    - **voor waarde** (optioneel)
      - Een teken reeks met een Azure Policy taal expressie met [beleids functies](./definition-structure.md#policy-functions) die worden geëvalueerd als _waar_ of _Onwaar_.
      - Biedt geen ondersteuning voor de volgende beleids functies: `field()` , `resourceGroup()` , `subscription()` .

### <a name="modify-operations"></a>Bewerkingen wijzigen

De eigenschap array **Operations** maakt het mogelijk verschillende labels op verschillende manieren te wijzigen vanuit één beleids definitie. Elke bewerking bestaat uit **bewerking**-, **veld**-en **waarde** -eigenschappen. Bewerking bepaalt wat de herstel taak doet op de tags, het veld bepaalt welke tag wordt gewijzigd en waarde definieert de nieuwe instelling voor die tag. In het onderstaande voor beeld wordt de volgende tag gewijzigd:

- Hiermee stelt `environment` u de tag in op test, zelfs als deze al met een andere waarde bestaat.
- Hiermee verwijdert u de tag `TempResource` .
- Hiermee stelt `Dept` u het label in op de beleids parameter voor de beleids toewijzing geconfigureerde _afdeling_ .

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

De eigenschap **Operation** heeft de volgende opties:

|Bewerking |Beschrijving |
|-|-|
|addOrReplace |Voegt de gedefinieerde eigenschap of tag en waarde toe aan de resource, zelfs als de eigenschap of tag al bestaat met een andere waarde. |
|Toevoegen |Voegt de gedefinieerde eigenschap of tag en waarde toe aan de resource. |
|Verwijderen |Hiermee verwijdert u de gedefinieerde eigenschap of tag uit de resource. |

### <a name="modify-examples"></a>Voor beelden wijzigen

Voor beeld 1: Voeg de `environment` tag toe en vervang bestaande `environment` Tags door ' test ':

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

Voor beeld 2: de `env` tag verwijderen en de `environment` tag toevoegen of bestaande `environment` Tags vervangen door een waarde met para meters:

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

Voor beeld 3: controleren of een opslag account open bare BLOB-toegang niet toestaat, wordt de wijzigings bewerking alleen toegepast bij het evalueren van aanvragen met een API-versie die groter is dan of gelijk is aan 2019-04-01:

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

## <a name="layering-policy-definitions"></a>Beleids definities voor lagen

Een resource kan worden beïnvloed door meerdere toewijzingen. Deze toewijzingen kunnen zich in hetzelfde bereik bevinden of in verschillende bereiken. Voor elk van deze toewijzingen is waarschijnlijk ook een ander effect gedefinieerd. De voor waarde en het effect van elk beleid worden onafhankelijk geëvalueerd. Bijvoorbeeld:

- Beleid 1
  - Hiermee wordt de resource locatie beperkt tot ' westelijke '
  - Toegewezen aan abonnement A
  - Effect weigeren
- Beleid 2
  - Hiermee wordt de resource locatie beperkt tot oostus
  - Toegewezen aan resource groep B in abonnement A
  - Controle-effect
  
Deze instelling zou resulteren in het volgende resultaat:

- Resources die al deel uitmaken van resource groep B in Oost-out, voldoen aan beleid 2 en niet-compatibel met beleid 1
- Resources die zich al in resource groep B bevindt, worden niet conform ' Oost-', niet-compatibel met beleid 2 en voldoen niet aan beleid 1 als dit niet in ' westelijke ' is
- Nieuwe resources in abonnement die niet in ' westus ' voor komen, worden geweigerd door beleid 1
- Nieuwe resources in abonnement A en resource groep B in westus worden gemaakt en voldoen niet aan het beleid 2

Als zowel beleid 1 als beleid 2 gevolgen heeft voor de weigering, verandert de situatie in:

- Een resource die al deel uitmaakt van resource groep B is niet compatibel met het beleid 2
- Resources die zich al in resource groep B bevindt, zijn niet compatibel met beleid 1
- Nieuwe resources in abonnement die niet in ' westus ' voor komen, worden geweigerd door beleid 1
- Nieuwe resources in resource groep B van abonnement A worden geweigerd

Elke toewijzing wordt afzonderlijk geëvalueerd. Daarom is er geen kans dat een resource door een hiaat wordt geslipd van verschillen in het bereik. Het resultaat van het beperken van beleids definities voor lagen wordt als **cumulatief het meest beperkend** beschouwd. Als voor het beleid 1 en 2 bijvoorbeeld een weigering is ingesteld, wordt een bron geblokkeerd door de beleids definities die elkaar overlappen en conflicteren. Als u de resource nog steeds moet maken in het doel bereik, kunt u de uitsluitingen voor elke toewijzing controleren om te controleren of de juiste beleids toewijzingen van invloed zijn op de juiste bereiken.

## <a name="next-steps"></a>Volgende stappen

- Bekijk voor beelden op [Azure Policy voor beelden](../samples/index.md).
- Lees over de [structuur van Azure Policy-definities](definition-structure.md).
- Meer informatie over het [programmatisch maken van beleids regels](../how-to/programmatically-create.md).
- Meer informatie over het [ophalen van compatibiliteits gegevens](../how-to/get-compliance-data.md).
- Ontdek hoe u [niet-compatibele resources kunt herstellen](../how-to/remediate-resources.md).
- Bekijk wat een beheer groep is met [het organiseren van uw resources met Azure-beheer groepen](../../management-groups/overview.md).
