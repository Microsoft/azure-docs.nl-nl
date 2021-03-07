---
title: Aangepaste beveiligings beleidsregels maken in Azure Security Center | Microsoft Docs
description: Aangepaste beleids definities van Azure worden bewaakt door Azure Security Center.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/25/2021
ms.author: memildin
zone_pivot_groups: manage-asc-initiatives
ms.openlocfilehash: a901e71da640f8413e5714ad59073324f582c1b9
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102441054"
---
# <a name="create-custom-security-initiatives-and-policies"></a>Aangepaste beveiligings initiatieven en-beleid maken

Azure Security Center beveiligings aanbevelingen worden gegenereerd om uw systemen en omgeving te beveiligen. Deze aanbevelingen zijn gebaseerd op de best practices van de branche, die zijn opgenomen in het algemene standaard beveiligings beleid dat aan alle klanten wordt verstrekt. Ze kunnen ook afkomstig zijn van de kennis van de branche-en regelgevings normen van Security Center.

Met deze functie kunt u uw eigen *aangepaste* initiatieven toevoegen. U ontvangt dan aanbevelingen als uw omgeving niet voldoet aan het beleid dat u maakt. Alle aangepaste initiatieven die u maakt, worden naast de ingebouwde initiatieven weer gegeven in het dash board voor nalevings vereisten, zoals beschreven in de zelf studie [uw naleving van regelgeving verbeteren](security-center-compliance-dashboard.md).

Zoals beschreven in [de Azure Policy-documentatie](../governance/policy/concepts/definition-structure.md#definition-location), moet u, wanneer u een locatie opgeeft voor uw aangepaste initiatief, een beheer groep of een abonnement zijn. 

> [!TIP]
> Zie [Wat zijn beveiligings beleid, initiatieven en aanbevelingen?](security-policy-concept.md)voor een overzicht van de belangrijkste concepten op deze pagina.

::: zone pivot="azure-portal"

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Een aangepast initiatief toevoegen aan uw abonnement 

1. Open de pagina **beveiligings beleid** vanuit de zijbalk van Security Center.

1. Selecteer een abonnement of beheer groep waaraan u een aangepast initiatief wilt toevoegen.

    [![Een abonnement selecteren waarvoor u uw aangepaste beleid wilt maken](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > U moet aangepaste standaarden toevoegen op het abonnements niveau (of hoger) om ze te evalueren en weer te geven in Security Center. 
    >
    > Wanneer u een aangepaste standaard toevoegt, wijst deze een *initiatief* toe aan dat bereik. Daarom raden we u aan om het breedste bereik te selecteren dat vereist is voor de toewijzing.

1. Klik op de pagina beveiligings beleid onder uw aangepaste initiatieven op **een aangepast initiatief toevoegen**.

    [![Klik op een aangepast initiatief toevoegen](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    De volgende pagina wordt geopend:

    ![Een beleid maken of toevoegen](media/custom-security-policies/create-or-add-custom-policy.png)

1. Bekijk op de pagina aangepaste initiatieven toevoegen de lijst met aangepaste beleids regels die al zijn gemaakt in uw organisatie. Als u een account wilt toewijzen aan uw abonnement, klikt u op **toevoegen**. Als er geen initiatief in de lijst is die aan uw behoeften voldoet, kunt u deze stap overs Laan.

1. Een nieuw aangepast initiatief maken:

    1. Klik op **nieuwe maken**.
    1. Voer de locatie en naam van de definitie in.
    1. Selecteer het beleid dat u wilt insluiten en klik op **toevoegen**.
    1. Voer de gewenste para meters in.
    1. Klik op **Opslaan**.
    1. Klik op de pagina aangepaste initiatieven toevoegen op vernieuwen. Uw nieuwe initiatief wordt weer gegeven als beschikbaar.
    1. Klik op **toevoegen** en wijs deze toe aan uw abonnement.

    > [!NOTE]
    > Voor het maken van nieuwe initiatieven zijn referenties van abonnements eigenaren vereist. Zie [machtigingen in azure Security Center](security-center-permissions.md)voor meer informatie over Azure-rollen.

    Het nieuwe initiatief gaat in op de volgende twee manieren:

    * Selecteer op de Security Center zijbalk onder beleids & naleving de optie naleving van **regelgeving**. Het compatibiliteits dashboard wordt geopend om uw nieuwe aangepaste initiatief naast de ingebouwde initiatieven weer te geven.
    
    * U ontvangt aanbevelingen als uw omgeving niet voldoet aan het beleid dat u hebt gedefinieerd.

1. Als u de resulterende aanbevelingen voor uw beleid wilt zien, klikt u op de zijbalk op **aanbevelingen** om de pagina aanbevelingen te openen. De aanbevelingen worden weer gegeven met een aangepast label en zijn binnen ongeveer een uur beschikbaar.

    [![Aangepaste aanbevelingen](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)

::: zone-end

::: zone pivot="rest-api"

## <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>Een beveiligings beleid configureren in Azure Policy met behulp van de REST API

Als onderdeel van de systeem eigen integratie met Azure Policy, Azure Security Center kunt u de REST API van Azure Policy benutten om beleids toewijzingen te maken. De volgende instructies begeleiden u bij het maken van beleids toewijzingen, evenals het aanpassen van bestaande toewijzingen. 

Belang rijke concepten in Azure Policy: 

- Een **beleids definitie** is een regel 

- Een **initiatief** is een verzameling beleids definities (regels) 

- Een **toewijzing** is een toepassing van een initiatief of een beleid voor een specifiek bereik (beheer groep, abonnement, enz.) 

Security Center heeft een ingebouwd initiatief, Azure Security Bench Mark, dat alle beveiligings beleidsregels omvat. Als u het beleid van Security Center op uw Azure-resources wilt beoordelen, moet u een toewijzing maken voor de beheer groep of het abonnement dat u wilt beoordelen.

Het ingebouwde initiatief heeft standaard alle beleids regels van Security Center ingeschakeld. U kunt ervoor kiezen om bepaalde beleids regels uit het ingebouwde initiatief uit te scha kelen. Als u bijvoorbeeld alle beleids regels van Security Center wilt Toep assen, met uitzonde ring van **Web Application firewall**, wijzigt u de waarde van de effect parameter van het beleid in **uitgeschakeld**.

## <a name="api-examples"></a>API-voorbeelden

Vervang deze variabelen in de volgende voor beelden:

- **{Scope}** Voer de naam in van de beheer groep of het abonnement waarop u het beleid toepast
- **{policyAssignmentName}** Voer de naam van de relevante beleids toewijzing in
- **{name}** Voer uw naam in of de naam van de beheerder die de beleids wijziging heeft goedgekeurd

Dit voor beeld laat zien hoe u het ingebouwde Security Center initiatief toewijst aan een abonnement of beheer groep
 
 ```
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Azure Security Center", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 
 ```

Dit voor beeld laat zien hoe u het ingebouwde Security Center initiatief toewijst aan een abonnement, waarbij het volgende beleid is uitgeschakeld: 

- Systeem updates ("systemUpdatesMonitoringEffect") 

- Beveiligings configuraties ("systemConfigurationsMonitoringEffect") 

- Endpoint Protection ("endpointProtectionMonitoringEffect") 

 ```
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    Request Body (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName":"Enable Monitoring in Azure Security Center", 
    
    "metadata":{ 
    
    "assignedBy":"{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters":{ 
    
    "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 
    
    }, 
    
     } 
    
    } 
 ```
In dit voor beeld ziet u hoe u een toewijzing verwijdert:
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

::: zone-end


## <a name="enhance-your-custom-recommendations-with-detailed-information"></a>Verbeter uw aangepaste aanbevelingen met gedetailleerde informatie

De ingebouwde aanbevelingen die bij Azure Security Center worden geleverd, bevatten details zoals Ernst niveaus en herstel instructies. Als u dit type informatie wilt toevoegen aan uw aangepaste aanbevelingen zodat deze wordt weer gegeven in de Azure Portal of waar u toegang tot uw aanbevelingen hebt, moet u de REST API gebruiken. 

U kunt de volgende twee soorten informatie toevoegen:

- **RemediationDescription** : teken reeks
- **Ernst** – Enum [laag, gemiddeld, hoog]

De meta gegevens moeten worden toegevoegd aan de beleids definitie voor een beleid dat deel uitmaakt van het aangepaste initiatief. Deze moet in de eigenschap ' Security Center ' staan, zoals wordt weer gegeven:

```json
 "metadata": {
    "securityCenter": {
        "RemediationDescription": "Custom description goes here",
        "Severity": "High"
    },
```

Hieronder ziet u een voor beeld van een aangepast beleid met de eigenschap meta gegevens/Security Center:

  ```json
  {
"properties": {
    "displayName": "Security - ERvNet - AuditRGLock",
    "policyType": "Custom",
    "mode": "All",
    "description": "Audit required resource groups lock",
    "metadata": {
        "securityCenter": {
            "RemediationDescription": "Resource Group locks can be set via Azure Portal -> Resource Group -> Locks",
            "Severity": "High"
        }
    },
    "parameters": {
        "expressRouteLockLevel": {
            "type": "String",
            "metadata": {
                "displayName": "Lock level",
                "description": "Required lock level for ExpressRoute resource groups."
            },
            "allowedValues": [
                "CanNotDelete",
                "ReadOnly"
            ]
        }
    },
    "policyRule": {
        "if": {
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        "then": {
            "effect": "auditIfNotExists",
            "details": {
                "type": "Microsoft.Authorization/locks",
                "existenceCondition": {
                    "field": "Microsoft.Authorization/locks/level",
                    "equals": "[parameters('expressRouteLockLevel')]"
                }
            }
        }
    }
}
}
  ```

Voor een ander voor beeld van het gebruik van de eigenschap Security Center raadpleegt u [deze sectie van de rest API-documentatie](/rest/api/securitycenter/assessmentsmetadata/createinsubscription#examples).


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een aangepast beveiligings beleid maakt. 

Raadpleeg de volgende artikelen voor meer gerelateerde materialen: 

- [Het overzicht van beveiligings beleid](tutorial-security-policy.md)
- [Een lijst met ingebouwde beveiligings beleidsregels](./policy-reference.md)