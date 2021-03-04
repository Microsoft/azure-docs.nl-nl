---
title: Galerie met werkmappen in Azure Security Center
description: Meer informatie over het maken van uitgebreide, interactieve rapporten van uw Azure Security Center gegevens met de geïntegreerde Azure Monitor werkmappen-galerie
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 198702f619e490e8000e4430aab23a7f6bfb6d85
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107710"
---
# <a name="create-rich-interactive-reports-of-security-center-data"></a>Uitgebreide, interactieve rapporten van Security Center gegevens maken

[Azure monitor werkmappen](../azure-monitor/visualize/workbooks-overview.md) bieden een flexibel canvas voor gegevens analyse en het maken van uitgebreide visuele rapporten in de Azure Portal. Ze stellen u in staat om meerdere gegevensbronnen uit Azure aan te boren en deze te combineren tot uniforme interactieve ervaringen.

Werkmappen bieden een uitgebreide set mogelijkheden voor het visualiseren van uw Azure-gegevens. Zie voor [beelden van visualisaties en documentatie](../azure-monitor/visualize/workbooks-text-visualizations.md)voor meer informatie over elk type visualisatie. 

In Azure Security Center kunt u de ingebouwde rapporten openen om de beveiligings postuur van uw organisatie bij te houden. U kunt ook aangepaste rapporten maken om een breed scala aan gegevens van Security Center of andere ondersteunde gegevens bronnen weer te geven.

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Rapport over beveiligde Score over tijd":::

## <a name="availability"></a>Beschikbaarheid

| Aspect                          | Details                                                                                                                                      |
|---------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------|
| Releasestatus:                  | Preview<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]                                                       |
| Prijzen:                        | Gratis                                                                                                                                         |
| Vereiste rollen en machtigingen: | Als u werkmappen wilt opslaan, moet u Mini maal Inzender machtigingen voor de werkmap hebben voor de doel resource groep                                      |
| Clouds:                         | ![Ja](./media/icons/yes-icon.png) Commerciële clouds<br>![Ja](./media/icons/yes-icon.png) Nationaal/onafhankelijk (Overheid van de VS, China, andere overheden) |
|                                 |                                                                                                                                              |

## <a name="workbooks-gallery-in-azure-security-center"></a>Galerie met werkmappen in Azure Security Center

Met de geïntegreerde functionaliteit van Azure Workbooks maakt Azure Security Center het eenvoudig om uw eigen aangepaste, interactieve rapporten te bouwen. Security Center ook een galerie met werkmappen bevat met de volgende rapporten die klaar zijn voor uw aanpassing:

- **Beveiligde Score over een bepaalde periode** : Houd de scores en wijzigingen van uw abonnementen bij in aanbevelingen voor uw resources
- **Systeem updates** : ontbrekende systeem updates weer geven op resources, besturings systeem, ernst en meer
- **Resultaten van evaluatie van beveiligings problemen** -Bekijk de resultaten van de scan van beveiligings problemen van uw Azure-resources

:::image type="content" source="media/custom-dashboards-azure-workbooks/workbooks-gallery-security-center.png" alt-text="Galerie met ingebouwde werkmappen in Azure Security Center":::

Kies een van de opgegeven rapporten of maak een eigen rapport.

> [!TIP]
> Gebruik de knop **bewerken** om de opgegeven rapporten aan uw tevredenheid aan te passen. Wanneer u klaar bent met bewerken, selecteert u **Opslaan** en worden uw wijzigingen opgeslagen in een nieuwe werkmap.
> 
> :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-supplied-workbooks.png" alt-text="De opgegeven werkmappen bewerken om ze aan te passen aan uw specifieke behoeften":::

### <a name="use-the-secure-score-over-time-report"></a>Het rapport beveiligde Score over tijd gebruiken

In dit rapport worden beveiligde Score gegevens van uw Log Analytics-werk ruimte gebruikt. Deze gegevens moeten worden geëxporteerd uit het hulp programma voor doorlopend exporteren, zoals wordt beschreven in [continue export configureren vanaf de Security Center pagina's in azure Portal](continuous-export.md?tabs=azure-portal).

Wanneer u de continue export instelt, stelt u de export frequentie in op zowel **streaming-updates** als **moment opnamen**.

:::image type="content" source="media/custom-dashboards-azure-workbooks/export-frequency-both.png" alt-text="Voor de beveiligde Score van de werkmap moet u beide opties selecteren in de instellingen voor de export frequentie in de configuratie voor continue export":::

> [!NOTE]
> Moment opnamen worden wekelijks geëxporteerd, dus u moet ten minste één week wachten voordat de eerste moment opname wordt geëxporteerd voordat u gegevens in dit rapport kunt weer geven.

> [!TIP]
> Gebruik het meegeleverde Azure Policy ' DeployIfNotExist-beleid dat wordt beschreven in [continue export op schaal configureren](continuous-export.md?tabs=azure-policy)voor het configureren van continue export in uw organisatie.

Het rapport beveiligde scores in de loop van de tijd heeft vijf grafieken voor de abonnementen die worden gerapporteerd aan de geselecteerde werk ruimten:


|Graph  |Voorbeeld  |
|---------|---------|
|**Score trends voor de afgelopen week en maand**<br>In deze sectie kunt u de huidige score en algemene trends van de scores voor uw abonnementen bewaken.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-1.png" alt-text="Trends voor beveiligde scores op het ingebouwde rapport":::|
|**Geaggregeerde score voor alle geselecteerde abonnementen**<br>Beweeg de muis aanwijzer over een wille keurig punt in de trend lijn om de geaggregeerde Score op een wille keurige datum in het geselecteerde tijds bereik weer te geven.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-2.png" alt-text="Geaggregeerde score voor alle geselecteerde abonnementen":::|
|**Aanbevelingen met de meeste slechte bronnen**<br>Deze tabel helpt u bij het sorteren van de aanbevelingen waarvan de meeste resources zijn gewijzigd in een slechte status voor de geselecteerde periode.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-3.png" alt-text="Aanbevelingen met de meeste slechte bronnen":::|
|**Scores voor specifieke beveiligings controles**<br>De beveiligings controles van Security Center zijn logische groeperingen van aanbevelingen. In dit diagram ziet u in één oogopslag de week cijfers voor al uw Besturings elementen.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-4.png" alt-text="Scores voor uw beveiligings besturings elementen gedurende de geselecteerde tijds periode":::|
|**Wijzigingen in resources**<br>Hier vindt u aanbevelingen met de meeste resources waarvan de status is gewijzigd (in orde, beschadigd of niet van toepassing) tijdens de geselecteerde periode. Selecteer een aanbeveling uit de lijst om een nieuwe tabel te openen met daarin de specifieke resources.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-5.png" alt-text="Aanbevelingen met de meeste resources waarvan de status is gewijzigd":::|

### <a name="use-the-system-updates-report"></a>Het rapport systeem updates gebruiken

Dit rapport is gebaseerd op de beveiligings aanbeveling ' systeem updates moeten op uw computers worden geïnstalleerd '.

Het rapport helpt u bij het identificeren van computers met openstaande updates.

U kunt de situatie voor de geselecteerde abonnementen weer geven op basis van:

- De lijst met resources met openstaande updates
- De lijst met updates die ontbreken in uw resources

:::image type="content" source="media/custom-dashboards-azure-workbooks/system-updates-report.png" alt-text="Het rapport systeem updates van Security Center op basis van de beveiligings aanbeveling ontbrekende updates":::

### <a name="use-the-vulnerability-assessment-findings-report"></a>Het rapport voor evaluatie van beveiligings problemen gebruiken

Security Center bevat beveiligings problemen voor uw machines, containers in container registers en SQL-servers.

Meer informatie over het gebruik van deze scanners:

- [Uw computers scannen met de geïntegreerde VA-scanner](deploy-vulnerability-assessment-vm.md)
- [Scan uw registerafbeeldingen op kwetsbaarheden](defender-for-container-registries-usage.md)
- [Uw SQL-resources scannen op beveiligings problemen](defender-for-sql-on-machines-vulnerability-assessment.md)

De bevindingen voor elk van deze scanners worden vermeld in afzonderlijke aanbevelingen:

- Beveiligingsproblemen op uw virtuele machines moeten worden hersteld
- Beveiligingsproblemen met installatiekopieën in Azure Container Registry moeten worden hersteld (mogelijk gemaakt door Qualys)
- Resultaten voor evaluaties van beveiligingsproblemen in uw SQL-databases moeten worden hersteld
- Resultaten voor evaluaties van beveiligingsproblemen op uw SQL-servers moeten worden hersteld

In dit rapport worden deze bevindingen verzameld en geordend op Ernst, resource type en categorie.

:::image type="content" source="media/custom-dashboards-azure-workbooks/vulnerability-assessment-findings-report.png" alt-text="Rapport met resultaten van evaluatie van beveiligings problemen van Security Center":::


## <a name="import-workbooks-from-other-workbook-galleries"></a>Werkmappen uit andere werkmap galerieën importeren

Als u werkmappen hebt gemaakt in andere Azure-Services en u deze wilt verplaatsen naar de galerie met Azure Security Center werkmappen:

1. Open de doel werkmap.

1. Selecteer **bewerken** op de werk balk.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks.png" alt-text="Een Azure Monitor werkmap bewerken":::

1. Selecteer op de werk balk **</>** om de geavanceerde editor in te voeren.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-advanced-editor.png" alt-text="De geavanceerde editor starten om de JSON-code van de galerie sjabloon op te halen":::

1. Kopieer de JSON van de galerie sjabloon van de werkmap.

1. Open de galerie met werkmappen in Security Center en selecteer in de menu balk de optie **Nieuw**.
1. Selecteer de **</>** om de geavanceerde editor in te voeren.
1. Plak de volledige JSON van de galerie sjabloon.
1. Selecteer **Toepassen**.
1. Selecteer in de werk balk de optie **Opslaan als**.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-save-as.png" alt-text="De werkmap opslaan in de galerie in Security Center":::

1. Voer de vereiste gegevens in voor het opslaan van de werkmap:
   1. Een naam voor de werkmap
   2. De gewenste regio
   3. Abonnement, resource groep en delen, indien van toepassing.

U vindt de opgeslagen werkmap in de categorie **recent gewijzigde werkmappen** .


## <a name="next-steps"></a>Volgende stappen

Dit artikel wordt beschreven op de pagina geïntegreerde Azure Monitor werkmappen van Security Center met ingebouwde rapporten en de optie om uw eigen aangepaste, interactieve rapporten te bouwen.

- Meer informatie over [Azure monitor werkmappen](../azure-monitor/visualize/workbooks-overview.md)
- De ingebouwde rapporten halen hun gegevens uit de aanbevelingen van Security Center. Meer informatie over de vele beveiligings aanbevelingen in [beveiligings aanbevelingen: een referentie gids](recommendations-reference.md)