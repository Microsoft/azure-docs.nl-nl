---
title: Integratie van werk items (preview)-Application Insights
description: Meer informatie over het maken van werk items in GitHub of Azure DevOps met Inge sloten Application Insights-gegevens.
ms.topic: conceptual
ms.date: 02/9/2021
ms.openlocfilehash: ba0a67bad3ba47191414d6b406ab6cb4e6b7da78
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101731915"
---
# <a name="work-item-integration-preview"></a>Integratie van werk items (preview-versie)

Met de functie voor integratie van werk items kunt u eenvoudig taken maken in GitHub of Azure DevOps die relevante Inge sloten Application Insights gegevens bevatten.

> [!IMPORTANT]
> Integratie van werk items is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="create-and-configure-a-work-item-template"></a>Een sjabloon voor werk items maken en configureren

1. Als u een sjabloon voor werk items wilt maken, gaat u naar uw Application Insights-resource en klikt u aan de linkerkant onder *Configure* **Working** Select. Selecteer vervolgens **een nieuwe sjabloon maken** .

    :::image type="content" source="./media/work-item-integration/create-work-item-template.png" alt-text=" Scherm afbeelding van het tabblad werk items waarop een nieuwe sjabloon maken is geselecteerd." lightbox="./media/work-item-integration/create-work-item-template.png":::

    U kunt ook een sjabloon voor werk items maken op het tabblad end-to-end-transactie Details als er momenteel geen sjabloon bestaat. Selecteer een gebeurtenis en selecteer aan de rechter kant **een werk item maken** en **begin met een werkmap sjabloon**.

    :::image type="content" source="./media/work-item-integration/create-template-from-transaction-details.png" alt-text=" Scherm opname van end-to-end-transactie Details tabblad met een werk item maken, starten met een geselecteerde werkmap sjabloon." lightbox="./media/work-item-integration/create-template-from-transaction-details.png":::

2. Nadat u **een nieuwe sjabloon maken** hebt geselecteerd, kunt u de Volg systemen kiezen, uw werkmap een naam en een koppeling naar het geselecteerde tracking systeem kiezen. vervolgens kiest u een regio om de sjabloon op te slaan (de standaard waarde is de regio waarin uw Application Insights-bron zich bevindt). De URL-para meters zijn de standaard-URL voor uw opslag plaats, bijvoorbeeld `https://github.com/myusername/reponame` of `https://mydevops.visualstudio.com/myproject` .

    :::image type="content" source="./media/work-item-integration/create-workbook.png" alt-text=" Scherm opname van een nieuwe werkmap sjabloon voor werk items maken.":::

## <a name="create-a-work-item"></a>Een werk item maken

 U hebt toegang tot uw nieuwe sjabloon vanuit een end-to-end transactie details die u kunt openen via prestaties, fouten, Beschik baarheid of andere tabbladen.

1. Als u een werk item wilt maken, gaat u naar end-to-end-transactie Details, selecteert u een gebeurtenis en selecteert u **werk item maken** en kiest u uw werk item sjabloon.

    :::image type="content" source="./media/work-item-integration/create-work-item.png" alt-text=" Scherm afbeelding van end-to-end trans actie-Details tabblad het maken van een werk item geselecteerd." lightbox="./media/work-item-integration/create-work-item.png":::

1. Er wordt een nieuw tabblad in de browser geopend met het Selecteer systeem voor het bijhouden van wijzigingen. In azure DevOps kunt u een bug of taak maken en in GitHub kunt u een nieuw probleem in uw opslag plaats maken. Er wordt automatisch een nieuw werk item gemaakt met contextuele informatie die wordt verstrekt door Application Insights.

    :::image type="content" source="./media/work-item-integration/github-work-item.png" alt-text=" Scherm opname van automatisch gemaakte GitHub-probleem" lightbox="./media/work-item-integration/github-work-item.png":::

    :::image type="content" source="./media/work-item-integration/azure-devops-work-item.png" alt-text=" Scherm opname van automatisch maken van een bug in azure DevOps." lightbox="./media/work-item-integration/azure-devops-work-item.png":::

## <a name="edit-a-template"></a>Een sjabloon bewerken

Als u uw sjabloon wilt bewerken, gaat u naar het tabblad **werk items** onder *configureren* en selecteert u het potlood pictogram naast de werkmap die u wilt bijwerken.

:::image type="content" source="./media/work-item-integration/edit-template.png" alt-text=" Scherm afbeelding van het tabblad werk item met het pictogram Potlood bewerken geselecteerd.":::

Selecteer bewerkings ![ pictogram bewerken ](./media/work-item-integration/edit-icon.png) bovenaan om de sjabloon te bewerken. Sjablonen voor werk items zijn gebaseerd op [Azure monitor werkmappen](../visualize/workbooks-overview.md). De gegevens van het werk item worden gegenereerd met de query taal van de tref woorden. U kunt de query's wijzigen om meer context essentiële informatie toe te voegen aan uw team. Wanneer u klaar bent met het bewerken, slaat u de werkmap op door ![ ](./media/work-item-integration/save-icon.png) in de bovenste werk balk het pictogram pictogram opslaan op te slaan.

:::image type="content" source="./media/work-item-integration/edit-workbook.png" alt-text=" Scherm afbeelding van de werkmap met werk item sjabloon in de bewerkings modus." lightbox="./media/work-item-integration/edit-workbook.png":::

U kunt meer dan één configuratie van een werk item maken en een aangepaste werkmap hebben om aan elk scenario te voldoen. De werkmappen kunnen ook worden geïmplementeerd door Azure Resource Manager de standaard implementaties in uw omgevingen te garanderen.