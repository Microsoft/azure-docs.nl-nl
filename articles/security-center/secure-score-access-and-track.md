---
title: Uw beveiligde Score bijhouden in Azure Security Center
description: Meer informatie over de verschillende manieren om uw beveiligde Score in Azure Security Center te benaderen en bij te houden.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/25/2021
ms.openlocfilehash: 5efc48d348e9cfceab590bcfba8c621e7721376f
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107699"
---
# <a name="access-and-track-your-secure-score"></a>Uw beveiligde Score openen en bijhouden

U kunt uw algemene beveiligde Score vinden, evenals uw score per abonnement, via de Azure Portal of via het programma, zoals beschreven in de volgende secties:

> [!TIP]
> Zie [berekeningen-Under standing your score](secure-score-security-controls.md#calculations---understanding-your-score)voor een gedetailleerde uitleg over hoe uw scores worden berekend.

## <a name="get-your-secure-score-from-the-portal"></a>Uw beveiligde Score ophalen uit de portal

Security Center wordt uw score prominent weer gegeven in de portal: het is de eerste hoofd tegel van de Security Center overzichts pagina. Als u deze tegel selecteert, gaat u naar de pagina speciale beveiligde Score, waar u de score kunt zien die is opgesplitst per abonnement. Selecteer één abonnement voor een overzicht van de gedetailleerde lijst met aanbevelingen met prioriteit en de mogelijke gevolgen voor het oplossen van deze voor de Score van het abonnement. 

Voor samen vatting wordt uw beveiligde Score op de volgende locaties weer gegeven in de portal pagina's van Security Center.

- In een tegel op het **overzicht** van Security Center (hoofd dashboard):

    :::image type="content" source="./media/secure-score-security-controls/score-on-main-dashboard.png" alt-text="De beveiligde Score op het dash board van de Security Center":::

- Op de pagina speciale **beveiligde Score** ziet u de beveiligde score voor uw abonnement en uw beheer groepen:

    :::image type="content" source="./media/secure-score-security-controls/score-on-dedicated-dashboard.png" alt-text="De beveiligde score voor abonnementen op de pagina beveiligde Score van Security Center":::

    :::image type="content" source="./media/secure-score-security-controls/secure-score-management-groups.png" alt-text="De beveiligde score voor beheer groepen op de pagina beveiligde Score van Security Center":::

    > [!NOTE]
    > Alle beheer groepen waarvoor u onvoldoende machtigingen hebt, worden weer gegeven als ' beperkt '. 

- Boven aan de pagina **aanbevelingen** :

    :::image type="content" source="./media/secure-score-security-controls/score-on-recommendations-page.png" alt-text="De pagina met aanbevelingen voor beveiligde scores op Security Center":::

## <a name="get-your-secure-score-from-the-rest-api"></a>Uw beveiligde Score ophalen uit de REST API

U krijgt toegang tot uw score via de API voor beveiligde scores. De API-methoden bieden de flexibiliteit om query's uit te voeren op de gegevens en uw eigen rapportagemechanisme te bouwen van uw beveiligingsscores in de loop van de tijd. U kunt bijvoorbeeld de [API beveiligde scores](/rest/api/securitycenter/securescores) gebruiken om de score voor een specifiek abonnement op te halen. Daarnaast kunt u de [API besturings elementen voor beveiligde scores](/rest/api/securitycenter/securescorecontrols) gebruiken om de beveiligings controles en de huidige Score van uw abonnementen weer te geven.

![Het ophalen van een enkele beveiligde Score via de API](media/secure-score-security-controls/single-secure-score-via-api.png)

Zie voor voor beelden van hulpprogram ma's die zijn gebouwd boven op de API voor beveiligde scores [het beveiligde Score gebied van onze github-Community](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score). 

## <a name="get-your-secure-score-from-azure-resource-graph"></a>Uw beveiligde Score ophalen uit de Azure-resource grafiek

Azure resource Graph biedt directe toegang tot resource gegevens in uw Cloud omgevingen met krachtige filters, groeperingen en sorteer mogelijkheden. Het is een snelle en efficiënte manier om via programma code of vanuit de Azure Portal informatie op te vragen over Azure-abonnementen. [Meer informatie over Azure Resource Graph](../governance/resource-graph/index.yml).

Voor toegang tot de beveiligde score voor meerdere abonnementen met Azure resource Graph:

1. Open in de Azure Portal **Azure resource Graph Explorer**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="De pagina aanbeveling van Azure resource Graph Explorer * * wordt gestart" :::

1. Voer uw Kusto-query in (met behulp van de voor beelden hieronder voor hulp).

    - Met deze query worden de abonnements-ID, de huidige score in punten en als een percentage en de maximale score voor het abonnement geretourneerd. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores' 
        | extend current = properties.score.current, max = todouble(properties.score.max)
        | project subscriptionId, current, max, percentage = ((current / max)*100)
        ```

    - Met deze query wordt de status van alle beveiligings controles geretourneerd. Voor elk besturings element krijgt u het aantal slechte resources, de huidige score en de maximale score. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores/securescorecontrols'
        | extend SecureControl = properties.displayName, unhealthy = properties.unhealthyResourceCount, currentscore = properties.score.current, maxscore = properties.score.max
        | project SecureControl , unhealthy, currentscore, maxscore
        ```

1. Selecteer **query uitvoeren**.


## <a name="tracking-your-secure-score-over-time"></a>Uw beveiligde Score na verloop van tijd bijhouden

### <a name="secure-score-over-time-report-in-workbooks-page"></a>Beveiligde score voor een tijd rapport op de pagina werkmappen

De pagina werkmappen van Security Center bevat een kant-en-klare rapport voor het visueel bijhouden van de scores van uw abonnementen, beveiligings controles en meer. Meer informatie over het [maken van uitgebreide, interactieve rapporten van Security Center gegevens](custom-dashboards-azure-workbooks.md).

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Een gedeelte van het rapport beveiligde Score over tijd van de galerie met werkmappen van Azure Security Center":::

### <a name="power-bi-pro-dashboards"></a>Power BI Pro Dash boards

Als u een Power BI gebruiker bent met een Pro-account, kunt u de **beveiligde Score gedurende een periode** Power bi dash board gebruiken om uw beveiligde Score na verloop van tijd bij te houden en eventuele wijzigingen te onderzoeken.

> [!TIP]
> U kunt dit dash board vinden, evenals andere hulp middelen voor het werken met een beveiligde Score, in het toegewezen gebied van de Azure Security Center Community op GitHub: https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score

Het dash board bevat de volgende twee rapporten die u helpen bij het analyseren van de beveiligings status:

- **Overzicht van resources** : bevat samengevatte gegevens met betrekking tot de status van uw resources.
- **Overzicht van beveiligde scores** : bevat samengevatte gegevens over de voortgang van uw score. Gebruik de grafiek beveiligde Score over tijd per abonnement om de wijzigingen in de Score weer te geven. Als u een aanzienlijke wijziging in uw score ziet, raadpleegt u de tabel met gedetecteerde wijzigingen die van invloed kunnen zijn op uw beveiligde score voor mogelijke wijzigingen die de wijziging zouden hebben veroorzaakt. Deze tabel bevat verwijderde resources, nieuw geïmplementeerde resources of bronnen waarvan de beveiligings status is gewijzigd voor een van de aanbevelingen.

:::image type="content" source="./media/secure-score-security-controls/power-bi-secure-score-dashboard.png" alt-text="De optionele beveiligde Score in de loop van de tijd Power BI dash board voor het bijhouden van uw beveiligde Score over tijd en het onderzoeken van wijzigingen":::


## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt beschreven hoe u uw beveiligde Score opent en volgt. Raadpleeg de volgende artikelen voor gerelateerd materiaal:

- [Meer informatie over de verschillende elementen van een aanbeveling](security-center-recommendations.md)
- [Meer informatie over het oplossen van aanbevelingen](security-center-remediate-recommendations.md)
- [De GitHub-hulpprogram ma's voor het werken met een beveiligde Score weer geven](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)