---
title: Een aanbeveling Azure Security Center een resource, abonnement, beheergroep en beveiligde score uit te sluiten
description: Meer informatie over het maken van regels om beveiligingsaanbevelingen uit te sluiten van abonnementen of beheergroepen en om te voorkomen dat deze van invloed zijn op uw beveiligingsscore
author: memildin
ms.author: memildin
ms.date: 03/11/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 13abb35d0fa9ad3ee949b6edf5205de601a02956
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718553"
---
# <a name="exempting-resources-and-recommendations-from-your-secure-score"></a>Resources en aanbevelingen van uw beveiligde score vrijstellen 

Een kernprioriteit van elk beveiligingsteam is ervoor te zorgen dat analisten zich kunnen richten op de taken en incidenten die van belang zijn voor de organisatie. Security Center heeft veel functies voor het aanpassen van de ervaring en ervoor zorgen dat uw beveiligingsscore de beveiligingsprioriteiten van uw organisatie weerspiegelt. De **uitzonderingsoptie** is een van deze functies.

Wanneer u uw beveiligingsaanbevelingen in Azure Security Center onderzoeken, is de lijst met betrokken resources een van de eerste gegevens die u bekijkt.

Af en toe wordt er een resource weergegeven die u denkt dat deze niet moet worden opgenomen. Of een aanbeveling wordt in een bereik weer gegeven waar u denkt dat deze er niet bij hoort. De resource is mogelijk teruggemiddeld door een proces dat niet is bij te houden door Security Center. De aanbeveling is mogelijk niet geschikt voor een specifiek abonnement. Of misschien heeft uw organisatie besloten om de risico's met betrekking tot de specifieke resource of aanbeveling te accepteren.

In dergelijke gevallen kunt u een uitzondering maken voor een aanbeveling om:

- **Een resource vrijstellen** om ervoor te zorgen dat deze in de toekomst niet wordt vermeld met de resources die niet in orde zijn en geen invloed heeft op uw beveiligde score. De resource wordt weergegeven als niet van toepassing en de reden wordt weergegeven als 'uitgesloten' met de specifieke reden die u selecteert.

- **Een abonnement of beheergroep** vrijstellen om ervoor te zorgen dat de aanbeveling geen invloed heeft op uw beveiligde score en in de toekomst niet wordt weergegeven voor het abonnement of de beheergroep. Dit heeft betrekking op bestaande resources en alle resources die u in de toekomst maakt. De aanbeveling wordt gemarkeerd met de specifieke reden die u selecteert voor het bereik dat u hebt geselecteerd.

## <a name="availability"></a>Beschikbaarheid

| Aspect                          | Details                                                                                                                                                                                                                                                                                                                            |
|---------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Releasestatus:                  | Preview<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]                                                                                                                                                                                                                                             |
| Prijzen:                        | Dit is een premium Azure Policy die zonder extra kosten wordt aangeboden Azure Defender klanten. Voor andere gebruikers kunnen er in de toekomst kosten in rekening worden gebracht.                                                                                                                                                                 |
| Vereiste rollen en machtigingen: | **Inzender** voor **eigenaar of resourcebeleid** om een uitzondering te maken<br>Als u een regel wilt maken, hebt u machtigingen nodig om beleid te bewerken in Azure Policy.<br>Meer informatie in [Azure RBAC-machtigingen in Azure Policy](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy).                                            |
| Beperkingen:                    | Uitzonderingen kunnen alleen worden gemaakt voor aanbevelingen die zijn opgenomen in het standaardinitiatief van Security Center, [Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction)of een van de opgegeven standaardinitiatieven voor regelgeving. Aanbevelingen die worden gegenereerd op basis van aangepaste initiatieven, kunnen niet worden uitgesloten. Meer informatie over de relaties [tussen beleidsregels, initiatieven en aanbevelingen](security-policy-concept.md). |
| Clouds:                         | ![Ja](./media/icons/yes-icon.png) Commerciële clouds<br>![Nee](./media/icons/no-icon.png) Nationaal/onafhankelijk (overheid van de VS, China, andere overheden)                                                                                                                                                                                         |
|                                 |                                                                                                                                                                                                                                                                                                                                    |

## <a name="define-an-exemption"></a>Een uitzondering definiëren

Als u de beveiligingsaanbevelingen die Security Center doet voor uw abonnementen, beheergroep of resources wilt afstemmen, kunt u een uitzonderingsregel maken voor:

- Markeer een **specifieke aanbeveling** of als 'beperkt' of 'geaccepteerd risico'. U kunt aanbevelingsvrijstellingen maken voor een abonnement, meerdere abonnementen of een hele beheergroep.
- Markeer **een of meer resources** als 'beperkt' of 'geaccepteerd risico' voor een specifieke aanbeveling.

> [!NOTE]
> Uitzonderingen kunnen alleen worden gemaakt voor aanbevelingen die zijn opgenomen in het standaardinitiatief van Security Center, Azure Security Benchmark of een van de opgegeven standaardinitiatieven voor regelgeving. Aanbevelingen die worden gegenereerd op basis van aangepaste initiatieven die zijn toegewezen aan uw abonnementen, kunnen niet worden uitgesloten. Meer informatie over de relaties [tussen beleidsregels, initiatieven en aanbevelingen](security-policy-concept.md).

> [!TIP]
> U kunt ook uitzonderingen maken met behulp van de API. Zie een voorbeeld van een JSON-voorbeeld en een uitleg van de relevante structuren [Azure Policy uitzonderingsstructuur](../governance/policy/concepts/exemption-structure.md).

Een uitzonderingsregel maken:

1. Open de pagina met details van de aanbevelingen voor de specifieke aanbeveling.

1. Selecteer in de werkbalk boven aan de pagina de optie **Uitzondering.**

    :::image type="content" source="media/exempt-resource/exempting-recommendation.png" alt-text="Maak een uitzonderingsregel voor een aanbeveling die moet worden uitgesloten van een abonnement of beheergroep.":::

1. In het **deelvenster Uitgesloten:**
    1. Selecteer het bereik voor deze uitzonderingsregel:
        - Als u een beheergroep selecteert, wordt de aanbeveling uitgesloten van alle abonnementen binnen die groep
        - Als u deze regel maakt om een of meer resources uit te sluiten van de aanbeveling, kiest u Geselecteerde resources en selecteert u de relevante resources in de lijst

    1. Voer een naam in voor deze uitzonderingsregel.
    1. Stel eventueel een vervaldatum in.
    1. Selecteer de categorie voor de uitzondering:
        - **Opgelost via externe partij (beperkt)** : als u een service van derden gebruikt die Security Center niet is geïdentificeerd. 

            > [!NOTE]
            > Wanneer u een aanbeveling als beperkt hebt uitgesloten, krijgt u geen punten voor uw beveiligingsscore. Maar omdat punten niet worden *verwijderd voor* de resources die niet in orde zijn, is het resultaat dat uw score toeneemt.

        - **Geaccepteerd risico (wordt geaccepteerd)** : als u hebt besloten om het risico te accepteren dat deze aanbeveling niet wordt vereenigd
    1. Voer desgewenst een beschrijving in.
    1. Selecteer **Maken**.

    :::image type="content" source="media/exempt-resource/defining-recommendation-exemption.png" alt-text="Stappen voor het maken van een uitzonderingsregel om een aanbeveling uit te sluiten van uw abonnement of beheergroep":::

    Wanneer de uitzondering van kracht wordt (dit kan tot 30 minuten duren):
    - De aanbeveling of resources hebben geen invloed op uw beveiligde score.
    - Als u specifieke resources hebt uitgesloten, worden deze weergegeven op het **tabblad** Niet van toepassing op de pagina met aanbevelingsdetails.
    - Als u een aanbeveling hebt uitgesloten, wordt deze standaard verborgen op Security Center de pagina met aanbevelingen van de Security Center. Dit komt doordat de standaardopties van het **filter Aanbevelingsstatus** op die pagina niet van toepassing zijn op aanbevelingen **die van toepassing** zijn. Hetzelfde geldt als u alle aanbevelingen in een besturingselement voor beveiliging uitkeert.

        :::image type="content" source="media/exempt-resource/recommendations-filters-hiding-not-applicable.png" alt-text="Standaardfilters op Azure Security Center de pagina met aanbevelingen verbergen de niet-toepasselijke aanbevelingen en beveiligingsbesturingselementen":::

    - In de informatie strip boven aan de pagina met aanbevelingsdetails wordt het aantal uitgesloten resources bijgewerkt:
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="Aantal uitgesloten resources":::

1. Als u de uitgesloten resources wilt controleren, opent u **het tabblad Niet van toepassing:**

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="Een uitzondering wijzigen":::

    De reden voor elke uitzondering is opgenomen in de tabel (1).

    Als u een uitzondering wilt wijzigen of verwijderen, selecteert u het beletseltekenmenu ('...') zoals weergegeven (2).

1. Als u alle uitzonderingsregels voor uw abonnement wilt bekijken, selecteert **u Uitzonderingen in de** informatie strip weergeven:

    > [!IMPORTANT]
    > Als u de specifieke uitzonderingen wilt zien die relevant zijn voor één aanbeveling, filtert u de lijst op basis van het relevante bereik en de naam van de aanbeveling.

    :::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Azure Policy uitzonderingspagina van de Azure Policy":::

    > [!TIP]
    > U kunt ook [Azure Resource Graph om aanbevelingen met uitzonderingen te vinden.](#find-recommendations-with-exemptions-using-azure-resource-graph)

## <a name="monitor-exemptions-created-in-your-subscriptions"></a>Uitzonderingen bewaken die in uw abonnementen zijn gemaakt

Zoals eerder op deze pagina is uitgelegd, zijn uitzonderingsregels een krachtig hulpmiddel dat gedetailleerde controle biedt over de aanbevelingen die van invloed zijn op resources in uw abonnementen en beheergroepen. 

Om bij te houden hoe uw gebruikers deze mogelijkheid oefenen, hebben we een ARM-sjabloon (Azure Resource Manager) gemaakt waarmee een Playbook voor logische apps en alle benodigde API-verbindingen worden geïmplementeerd om u te waarschuwen wanneer er een uitzondering is gemaakt.

- Zie voor meer informatie over het playbook de blogpost van de tech-community [Resource-uitzonderingen bijhouden in Azure Security Center](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-keep-track-of-resource-exemptions-in-azure-security/ba-p/1770580)
- U vindt de ARM-sjabloon in de [Azure Security Center GitHub-opslagplaats](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation/Notify-ResourceExemption)
- Als u alle benodigde onderdelen wilt implementeren, [gebruikt u dit geautomatiseerde proces](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Security-Center%2Fmaster%2FWorkflow%2520automation%2FNotify-ResourceExemption%2Fazuredeploy.json)


## <a name="find-recommendations-with-exemptions-using-azure-resource-graph"></a>Aanbevelingen zoeken met uitzonderingen die gebruikmaken van Azure Resource Graph

Azure Resource Graph (ARG) biedt directe toegang tot resourcegegevens in uw cloudomgevingen met robuuste filter-, groeperings- en sorteermogelijkheden. Het is een snelle en efficiënte manier om via een programma of vanuit de Azure Portal.

Alle aanbevelingen met uitzonderingsregels weergeven:

1. Open **Azure Resource Graph Explorer.**

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Start Azure Resource Graph Explorer** aanbevelingspagina" :::

1. Voer de volgende query in en selecteer **Query uitvoeren.**

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


Meer informatie op de volgende pagina's:
- [Meer informatie over Azure Resource Graph](../governance/resource-graph/index.yml).
- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)
- [Kusto-querytaal (KQL)](/azure/data-explorer/kusto/query/)





## <a name="faq---exemption-rules"></a>Veelgestelde vragen - Uitzonderingsregels

### <a name="what-happens-when-one-recommendation-is-in-multiple-policy-initiatives"></a>Wat gebeurt er wanneer één aanbeveling in meerdere beleidsinitiatieven valt?

Soms wordt een beveiligingsaanbeveling in meer dan één beleidsinitiatief weergegeven. Als u meerdere exemplaren van dezelfde aanbeveling hebt toegewezen aan hetzelfde abonnement en u een uitzondering voor de aanbeveling maakt, is dit van invloed op alle initiatieven die u mag bewerken. 

De aanbeveling **** maakt bijvoorbeeld deel uit van het standaardbeleidsinitiatief dat is toegewezen aan alle Azure-abonnementen door Azure Security Center. Het is ook in XXXXX.

Als u probeert een uitzondering voor deze aanbeveling te maken, ziet u een van de volgende twee berichten:

- Als u de benodigde machtigingen hebt om beide initiatieven te bewerken, ziet u het volgende:

    *Deze aanbeveling is opgenomen in verschillende beleidsinitiatieven: [initiatiefnamen gescheiden door komma's]. Op al deze uitzonderingen worden uitzonderingen gemaakt.*  

- Als u niet voldoende machtigingen voor beide initiatieven hebt, ziet u in plaats daarvan dit bericht:

    *U hebt beperkte machtigingen om de uitzondering toe te passen op alle beleidsinitiatieven. De uitzonderingen worden alleen gemaakt voor de initiatieven met voldoende machtigingen.*


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een resource kunt vrijstellen van een aanbeveling, zodat deze geen invloed heeft op uw beveiligde score. Zie voor meer informatie over de beveiligde score:

- [Beveiligingsscore in Azure Security Center](secure-score-security-controls.md)
