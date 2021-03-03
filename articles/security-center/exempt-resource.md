---
title: Een Azure Security Center aanbeveling uitsluiten van een resource, abonnement, beheer groep en beveiligde Score
description: Meer informatie over het maken van regels voor het uitsluiten van beveiligings aanbevelingen van abonnementen of beheer groepen en om te voor komen dat ze invloed hebben op uw beveiligde Score
author: memildin
ms.author: memildin
ms.date: 01/22/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 374ddaa088fba9ae7035f170562e06b7f07eae47
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709373"
---
# <a name="exempting-resources-and-recommendations-from-your-secure-score"></a>Resources en aanbevelingen uitsluiten van uw beveiligde Score 

Een belang rijke prioriteit van elk beveiligings team is om ervoor te zorgen dat analisten zich kunnen concentreren op de taken en incidenten die voor de organisatie van belang zijn. Security Center beschikt over een groot aantal functies voor het aanpassen van de ervaring en om ervoor te zorgen dat uw beveiligde Score overeenkomt met de beveiligings prioriteiten van uw organisatie. De optie **uitsluiting** is een van deze functies.

Wanneer u uw beveiligings aanbevelingen in Azure Security Center onderzoekt, is een van de eerste stukjes informatie die u controleert, de lijst met betrokken resources.

In sommige gevallen wordt een resource weer gegeven die u niet wilt opnemen. Het is ook mogelijk dat er een aanbeveling wordt weer gegeven in een bereik waarvan u denkt dat deze niet deel uitmaakt. De resource is mogelijk hersteld door een proces dat niet wordt bijgehouden door Security Center. De aanbeveling is mogelijk niet geschikt voor een specifiek abonnement. Of misschien heeft uw organisatie besloten om de Risico's te accepteren die zijn gerelateerd aan de specifieke resource of aanbeveling.

In dergelijke gevallen kunt u een uitzonde ring voor een aanbeveling maken voor het volgende:

- **Een resource vrij** te stellen om ervoor te zorgen dat deze niet in de toekomst wordt weer gegeven met de foutieve resources en niet van invloed is op uw beveiligde Score. De resource wordt weer gegeven als niet van toepassing en de reden wordt weer gegeven als ' uitgesloten ' met de specifieke reden die u selecteert.

- De **uitsluiting van een abonnement of beheer groep** om ervoor te zorgen dat de aanbeveling niet van invloed is op uw beveiligde Score en niet in de toekomst wordt weer gegeven voor het abonnement of de beheer groep. Dit heeft betrekking op bestaande resources en alle die u in de toekomst maakt. De aanbeveling wordt gemarkeerd met de specifieke reden die u selecteert voor het bereik dat u hebt geselecteerd.

## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Releasestatus:|Preview<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Prijzen:|Dit is een Premium Azure-beleids mogelijkheid die wordt aangeboden aan Azure Defender-klanten zonder extra kosten. Voor andere gebruikers kunnen kosten in de toekomst worden toegepast.|
|Vereiste rollen en machtigingen:|**Eigenaar van abonnement** of **beleids bijdrage** voor het maken van een uitzonde ring<br>Als u een regel wilt maken, hebt u machtigingen nodig voor het bewerken van beleid in Azure Policy.<br>Meer informatie vindt u in de [Azure RBAC-machtigingen in azure Policy](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy).|
|Clouds:|![Ja](./media/icons/yes-icon.png) Commerciële clouds<br>![Nee](./media/icons/no-icon.png) Nationaal/onafhankelijk (overheid van de VS, China, andere overheden)|
|||

## <a name="define-an-exemption"></a>Een uitzonde ring definiëren

Als u de aanbevelingen voor beveiliging die Security Center maakt voor uw abonnementen, beheer groep of resources, wilt afstemmen, kunt u een uitzonderings regel maken voor het volgende:

- Markeer een specifieke **aanbeveling** of als ' verminderd ' of ' risico geaccepteerd '. U kunt aanbevelings uitzonderingen maken voor een abonnement, meerdere abonnementen of een volledige beheer groep.
- Een **of meer resources** markeren als ' verminderd ' of ' risico geaccepteerd ' voor een specifieke aanbeveling.

> [!TIP]
> U kunt ook uitzonde ringen maken met behulp van de API. Zie [Azure Policy-uitsluitings structuur](../governance/policy/concepts/exemption-structure.md)voor een voor beeld-JSON en een uitleg van de relevante structuren.

Een uitzonderings regel maken:

1. Open de pagina met aanbevelingen voor de specifieke aanbeveling.

1. Selecteer in de werk balk boven aan de pagina **uitzonde** ring selecteren.

    :::image type="content" source="media/exempt-resource/exempting-recommendation.png" alt-text="Maak een uitzonderings regel voor een aanbeveling die moet worden uitgesloten van een abonnement of beheer groep.":::

1. In het deel venster **uitzonde** ring:
    1. Selecteer het bereik voor deze uitzonderings regel:
        - Als u een beheer groep selecteert, wordt de aanbeveling uitgesloten van alle abonnementen in die groep
        - Als u deze regel maakt om een of meer resources uit te sluiten van de aanbeveling, kiest u geselecteerde resources en selecteert u de relevante items in de lijst

    1. Voer een naam in voor deze uitzonderings regel.
    1. U kunt desgewenst een verval datum instellen.
    1. Selecteer de categorie voor de uitzonde ring:
        - **Opgelost door derden (beperkt)** : als u gebruikmaakt van een service van derden die Security Center niet is geïdentificeerd. 

            > [!NOTE]
            > Wanneer u een aanbeveling vrijmaakt als een oplossing, krijgt u geen punten voor uw beveiligde Score. Maar omdat punten niet worden *verwijderd* voor de beschadigde resources, is het resultaat dat uw score toeneemt.

        - **Geaccepteerd risico (ontheffing)** : als u hebt besloten om het risico van het niet beperken van deze aanbeveling te accepteren
    1. Voer desgewenst een beschrijving in.
    1. Selecteer **Maken**.

    :::image type="content" source="media/exempt-resource/defining-recommendation-exemption.png" alt-text="Stappen voor het maken van een uitzonderings regel voor het uitsluiten van een aanbeveling van uw abonnement of beheer groep":::

    Wanneer de uitzonde ring wordt doorgevoerd (dit kan tot 30 minuten duren):
    - De aanbeveling of resources hebben geen invloed op uw beveiligde Score.
    - Als u specifieke resources hebt uitgesloten, worden deze weer gegeven op het tabblad **niet van toepassing** op de pagina aanbevelings Details.
    - Als u een aanbeveling hebt uitgesloten, wordt deze standaard verborgen op de pagina aanbevelingen van Security Center. Dit komt doordat de standaard opties van het filter voor **aanbevelings status** op die pagina **niet van toepassing** zijnde aanbevelingen uit te sluiten. Dit geldt ook voor alle aanbevelingen in een beveiligings controle.

        :::image type="content" source="media/exempt-resource/recommendations-filters-hiding-not-applicable.png" alt-text="Standaard filters op de pagina aanbevelingen van Azure Security Center Verberg de aanbevelingen en beveiligings controles die niet van toepassing zijn":::

    - In de informatie strook boven aan de pagina met aanbevelings Details wordt het aantal uitgesloten resources bijgewerkt:
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="Aantal vrijgestelde resources":::

1. Als u de vrijgestelde resources wilt bekijken, opent u het tabblad **niet van toepassing** :

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="Een uitzonde ring wijzigen":::

    De reden voor elke uitzonde ring is opgenomen in de tabel (1).

    Als u een uitzonde ring wilt wijzigen of verwijderen, selecteert u het menu met het weglatings teken ("..."), zoals weer gegeven (2).

1. Als u alle uitzonderings regels voor uw abonnement wilt bekijken, selecteert u **uitzonde ringen weer geven** in de gegevens strook:

    > [!IMPORTANT]
    > Als u de specifieke uitzonde ringen wilt zien die relevant zijn voor één aanbeveling, filtert u de lijst op basis van het relevante bereik en de naam van de aanbeveling.

    :::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Pagina uitzonde ringen van Azure Policy":::

    > [!TIP]
    > U kunt ook [Azure resource Graph gebruiken om aanbevelingen te vinden met uitzonde ringen](#find-recommendations-with-exemptions-using-azure-resource-graph).

## <a name="monitor-exemptions-created-in-your-subscriptions"></a>Bewaken van uitzonde ringen die zijn gemaakt in uw abonnementen

Zoals eerder op deze pagina is uitgelegd, zijn de uitzonderings regels een krachtig hulp programma waarmee u de aanbevelingen die van invloed zijn op resources in uw abonnementen en beheer groepen nauw keuriger kunt beheren. 

We hebben een Azure Resource Manager ARM-sjabloon gemaakt waarmee een logische app-Playbook en alle benodigde API-verbindingen worden geïmplementeerd om u te waarschuwen wanneer er een uitzonde ring is gemaakt om bij te houden hoe uw gebruikers deze functie uitoefenen.

- Voor meer informatie over de Playbook raadpleegt u het tech Community-Blog bericht [over het bijhouden van resource-uitzonde ringen in azure Security Center](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-keep-track-of-resource-exemptions-in-azure-security/ba-p/1770580)
- U vindt de ARM-sjabloon in de [github-opslag plaats van Azure Security Center](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation/Notify-ResourceExemption)
- Als u alle benodigde onderdelen wilt implementeren, [gebruikt u dit geautomatiseerde proces](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Security-Center%2Fmaster%2FWorkflow%2520automation%2FNotify-ResourceExemption%2Fazuredeploy.json)


## <a name="find-recommendations-with-exemptions-using-azure-resource-graph"></a>Aanbevelingen met uitzonde ringen zoeken met Azure resource Graph

Azure resource Graph (ARG) biedt directe toegang tot resource gegevens in uw Cloud omgevingen met krachtige filters, groeperingen en sorteer mogelijkheden. Het is een snelle en efficiënte manier om via programma code of vanuit de Azure Portal informatie op te vragen over Azure-abonnementen.

Alle aanbevelingen met uitzonderings regels weer geven:

1. Open **Azure resource Graph Explorer**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="De pagina aanbeveling van Azure resource Graph Explorer * * wordt gestart" :::

1. Voer de volgende query in en selecteer **query uitvoeren**.

    ```kusto
    securityresources
    | where type == "microsoft.security/assessments"
    // Get recommendations in useful format
    | project
    ['TenantID'] = tenantId,
    ['SubscriptionID'] = subscriptionId,
    ['AssessmentID'] = name,
    ['DisplayName'] = properties.displayName,
    ['ResourceType'] = tolower(split(properties.resourceDetails.Id,"/").[7]),
    ['ResourceName'] = tolower(split(properties.resourceDetails.Id,"/").[8]),
    ['ResourceGroup'] = resourceGroup,
    ['ContainsNestedRecom'] = tostring(properties.additionalData.subAssessmentsLink),
    ['StatusCode'] = properties.status.code,
    ['StatusDescription'] = properties.status.description,
    ['PolicyDefID'] = properties.metadata.policyDefinitionId,
    ['Description'] = properties.metadata.description,
    ['RecomType'] = properties.metadata.assessmentType,
    ['Remediation'] = properties.metadata.remediationDescription,
    ['Severity'] = properties.metadata.severity,
    ['Link'] = properties.links.azurePortal
    | where StatusDescription contains "Exempt"    
    ```


Meer informatie vindt u op de volgende pagina's:
- [Meer informatie over Azure Resource Graph](../governance/resource-graph/index.yml).
- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)
- [Kusto-querytaal (KQL)](/azure/data-explorer/kusto/query/)





## <a name="exemption-rule-faq"></a>Veelgestelde vragen over de uitsluitings regel

### <a name="what-happens-when-one-recommendation-is-in-multiple-policy-initiatives"></a>Wat gebeurt er wanneer een aanbeveling zich in meerdere beleids initiatieven voordoet?

Soms wordt in meer dan één beleids initiatief een beveiligings aanbeveling weer gegeven. Als u meerdere exemplaren van dezelfde aanbeveling hebt die aan hetzelfde abonnement zijn toegewezen, en u een uitzonde ring voor de aanbeveling maakt, heeft dit invloed op alle initiatieven waarvoor u toestemming hebt om te bewerken. 

De aanbeveling * * * * maakt bijvoorbeeld deel uit van het standaard beleid dat is toegewezen aan alle Azure-abonnementen door Azure Security Center. Ook in XXXXX.

Als u een uitzonde ring voor deze aanbeveling probeert te maken, ziet u een van de volgende twee berichten:

- Als u over de benodigde machtigingen beschikt om beide initiatieven te bewerken, ziet u het volgende:

    *Deze aanbeveling is opgenomen in verschillende beleids initiatieven: [initiatief namen gescheiden door komma's]. Er worden op al deze uitzonde ringen gemaakt.*  

- Als u niet over de juiste machtigingen beschikt voor beide initiatieven, ziet u dit bericht in plaats daarvan:

    *U hebt beperkte machtigingen om de uitzonde ring op alle beleids initiatieven toe te passen. de uitzonde ringen worden alleen gemaakt voor de initiatieven met voldoende machtigingen.*


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een resource kunt uitsluiten van een aanbeveling zodat deze geen invloed heeft op uw beveiligde Score. Zie voor meer informatie over beveiligde scores:

- [Beveiligingsscore in Azure Security Center](secure-score-security-controls.md)