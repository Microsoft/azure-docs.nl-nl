---
title: Aangepast beveiligingsbeleid maken in Azure Security Center | Microsoft Docs
description: Aangepaste Azure-beleidsdefinities die worden bewaakt door Azure Security Center.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/25/2021
ms.author: memildin
zone_pivot_groups: manage-asc-initiatives
ms.openlocfilehash: a41696ba92757550f9cbaa08ccf78d9a5da528d2
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718895"
---
# <a name="create-custom-security-initiatives-and-policies"></a>Aangepaste beveiligingsinitiatieven en -beleid maken

Om uw systemen en omgeving te beveiligen, Azure Security Center beveiligingsaanbevelingen gegenereerd. Deze aanbevelingen zijn gebaseerd op best practices in de branche, die zijn opgenomen in het algemene, standaardbeveiligingsbeleid dat aan alle klanten wordt geleverd. Ze kunnen ook afkomstig zijn Security Center kennis van industrie- en regelgevingsstandaarden.

Met deze functie kunt u uw eigen *aangepaste* initiatieven toevoegen. Vervolgens ontvangt u aanbevelingen als uw omgeving niet het beleid volgt dat u maakt. Alle aangepaste initiatieven die u maakt, worden weergegeven naast de ingebouwde initiatieven in het dashboard voor naleving van regelgeving, zoals beschreven in de zelfstudie Uw naleving van [regelgeving verbeteren.](security-center-compliance-dashboard.md)

Zoals besproken in [de Azure Policy documentatie](../governance/policy/concepts/definition-structure.md#definition-location), moet het een beheergroep of een abonnement zijn wanneer u een locatie voor uw aangepaste initiatief opgeeft. 

> [!TIP]
> Zie Wat zijn beveiligingsbeleid, initiatieven en aanbevelingen? voor een overzicht van de belangrijkste concepten [op deze pagina.](security-policy-concept.md)

::: zone pivot="azure-portal"

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Een aangepast initiatief toevoegen aan uw abonnement 

1. Open Security Center de zijbalk van de pagina **Beveiligingsbeleid.**

1. Selecteer een abonnement of beheergroep waaraan u een aangepast initiatief wilt toevoegen.

    [![Een abonnement selecteren waarvoor u uw aangepaste beleid gaat maken](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > U moet aangepaste standaarden toevoegen op abonnementsniveau (of hoger) om ze te evalueren en weer te geven in Security Center. 
    >
    > Wanneer u een aangepaste standaard toevoegt, wordt er een *initiatief aan dat* bereik toegewezen. Daarom raden we u aan het breedste bereik te selecteren dat is vereist voor die toewijzing.

1. Klik op de pagina Beveiligingsbeleid onder Uw aangepaste initiatieven op **Een aangepast initiatief toevoegen.**

    [![Klik op Een aangepast initiatief toevoegen](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    De volgende pagina wordt geopend:

    ![Een beleid maken of toevoegen](media/custom-security-policies/create-or-add-custom-policy.png)

1. Bekijk op de pagina Aangepaste initiatieven toevoegen de lijst met aangepaste beleidsregels die al in uw organisatie zijn gemaakt. Als u een abonnement ziet dat u wilt toewijzen aan uw abonnement, klikt u op **Toevoegen.** Als er geen initiatief in de lijst staat dat aan uw behoeften voldoet, kunt u deze stap overslaan.

1. Een nieuw aangepast initiatief maken:

    1. Klik **op Nieuwe maken.**
    1. Voer de locatie en naam van de definitie in.
    1. Selecteer de beleidsregels die u wilt opnemen en klik op **Toevoegen.**
    1. Voer de gewenste parameters in.
    1. Klik op **Opslaan**.
    1. Klik op de pagina Aangepaste initiatieven toevoegen op Vernieuwen. Uw nieuwe initiatief wordt weergegeven als beschikbaar.
    1. Klik **op Toevoegen** en wijs het toe aan uw abonnement.

    > [!NOTE]
    > Voor het maken van nieuwe initiatieven zijn referenties van abonnementseigenaars vereist. Zie Machtigingen in Azure Security Center voor [meer informatie over Azure-Azure Security Center.](security-center-permissions.md)

    Uw nieuwe initiatief wordt van kracht en u kunt de impact op de volgende twee manieren zien:

    * Selecteer in Security Center zijbalk onder & naleving de optie **Naleving van regelgeving.** Het nalevingsdashboard wordt geopend om uw nieuwe aangepaste initiatief weer te geven naast de ingebouwde initiatieven.
    
    * U krijgt nu aanbevelingen als uw omgeving niet het beleid volgt dat u hebt gedefinieerd.

1. Als u de resulterende aanbevelingen voor uw beleid wilt zien, klikt u op **Aanbevelingen** in de zijbalk om de pagina met aanbevelingen te openen. De aanbevelingen worden weergegeven met het label Aangepast en zijn binnen ongeveer een uur beschikbaar.

    [![Aangepaste aanbevelingen](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)

::: zone-end

::: zone pivot="rest-api"

## <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>Een beveiligingsbeleid configureren in Azure Policy met behulp van de REST API

Als onderdeel van de systeemeigen integratie met Azure Policy, Azure Security Center u profiteren van de Azure Policy van REST API om beleidstoewijzingen te maken. De volgende instructies helpen u bij het maken van beleidstoewijzingen en het aanpassen van bestaande toewijzingen. 

Belangrijke concepten in Azure Policy: 

- Een **beleidsdefinitie** is een regel 

- Een **initiatief** is een verzameling beleidsdefinities (regels) 

- Een **toewijzing** is een toepassing van een initiatief of een beleid voor een specifiek bereik (beheergroep, abonnement, enzovoort) 

Security Center heeft een ingebouwd initiatief, [Azure Security Benchmark,](https://docs.microsoft.com/security/benchmark/azure/introduction)dat al het beveiligingsbeleid bevat. Als u Security Center van uw Azure-resources wilt beoordelen, moet u een toewijzing maken voor de beheergroep of het abonnement dat u wilt beoordelen.

Voor het ingebouwde initiatief zijn Security Center standaard ingeschakeld. U kunt ervoor kiezen om bepaalde beleidsregels uit te schakelen vanuit het ingebouwde initiatief. Als u bijvoorbeeld alle beleidsregels van Security Center web **application firewall** wilt toepassen, wijzigt u de waarde van de effectparameter van het beleid in **Uitgeschakeld.**

## <a name="api-examples"></a>API-voorbeelden

Vervang in de volgende voorbeelden deze variabelen:

- **{scope}** voer de naam in van de beheergroep of het abonnement waarop u het beleid wilt toepassen
- **{policyAssignmentName}** voer de naam van de relevante beleidstoewijzing in
- **{name} voer** uw naam in of de naam van de beheerder die de beleidswijziging heeft goedgekeurd

In dit voorbeeld ziet u hoe u het ingebouwde initiatief Security Center toewijzen aan een abonnement of beheergroep
 
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

In dit voorbeeld ziet u hoe u het ingebouwde Security Center toewijst aan een abonnement, met het volgende beleid uitgeschakeld: 

- Systeemupdates ('systemUpdatesMonitoringEffect') 

- Beveiligingsconfiguraties ('systemConfigurationsMonitoringEffect') 

- Endpoint Protection ('endpointProtectionMonitoringEffect') 

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
In dit voorbeeld ziet u hoe u een toewijzing verwijdert:
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

::: zone-end


## <a name="enhance-your-custom-recommendations-with-detailed-information"></a>Uw aangepaste aanbevelingen verbeteren met gedetailleerde informatie

De ingebouwde aanbevelingen die worden geleverd met Azure Security Center bevatten details zoals ernstniveaus en herstelinstructies. Als u dit type informatie wilt toevoegen aan uw aangepaste aanbevelingen, zodat deze wordt weergegeven in de Azure Portal of waar u uw aanbevelingen ook kunt openen, moet u de REST API. 

De twee typen informatie die u kunt toevoegen zijn:

- **RemediationDescription** – String
- **Ernst :** enum [laag, gemiddeld, hoog]

De metagegevens moeten worden toegevoegd aan de beleidsdefinitie voor een beleid dat deel uitmaakt van het aangepaste initiatief. Dit moet de eigenschap securityCenter zijn, zoals wordt weergegeven:

```json
 "metadata": {
    "securityCenter": {
        "RemediationDescription": "Custom description goes here",
        "Severity": "High"
    },
```

Hieronder vindt u een voorbeeld van een aangepast beleid met inbegrip van de eigenschap metadata/securityCenter:

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

Zie voor een ander voorbeeld van het gebruik van de eigenschap securityCenter deze sectie [van de REST API documentatie](/rest/api/securitycenter/assessmentsmetadata/createinsubscription#examples).


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u aangepaste beveiligingsbeleidsregels maakt. 

Zie de volgende artikelen voor ander gerelateerd materiaal: 

- [Het overzicht van beveiligingsbeleid](tutorial-security-policy.md)
- [Een lijst met de ingebouwde beveiligingsbeleidsregels](./policy-reference.md)