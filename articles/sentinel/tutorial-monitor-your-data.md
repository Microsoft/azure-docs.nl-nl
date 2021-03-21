---
title: Uw gegevens visualiseren met behulp van Azure Monitor werkmappen in azure Sentinel | Microsoft Docs
description: Gebruik deze zelf studie om te leren hoe u uw gegevens kunt visualiseren met behulp van werkmappen in azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2020
ms.author: yelevin
ms.openlocfilehash: 048a089209ef7c5f20c96f77593e2cf39590147e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600518"
---
# <a name="tutorial-visualize-and-monitor-your-data"></a>Zelfstudie: Uw gegevens visualiseren en bewaken



Zodra u [uw gegevens bronnen](quickstart-onboard.md) aan Azure Sentinel hebt gekoppeld, kunt u de gegevens visualiseren en bewaken met behulp van de Azure Sentinel-acceptatie van Azure monitor werkmappen, die veelzijdigheid biedt bij het maken van aangepaste Dash boards. Hoewel de werkmappen anders worden weer gegeven in de Azure-Sentinel, kan het nuttig zijn om te zien hoe u [interactieve rapporten met Azure monitor werkmappen maakt](../azure-monitor/visualize/workbooks-overview.md). Met Azure Sentinel kunt u aangepaste werkmappen maken voor uw gegevens. Daarnaast bevat dit ingebouwde werkmapsjablonen, zodat u snel inzicht kunt krijgen in uw gegevens zodra u verbinding maakt met een gegevensbron.


Deze zelf studie helpt u bij het visualiseren van uw gegevens in azure Sentinel.
> [!div class="checklist"]
> * Ingebouwde werkmappen gebruiken
> * Nieuwe werkmappen maken

## <a name="prerequisites"></a>Vereisten

- U moet mini maal over machtigingen voor de werkmap lezer of werkmap beschikken voor de resource groep van de Azure Sentinel-werk ruimte.

> [!NOTE]
> De werkmappen die u in azure Sentinel kunt zien, worden opgeslagen in de resource groep van de Azure-Sentinel-werk ruimte en worden gelabeld door de werk ruimte waarin ze zijn gemaakt.

## <a name="use-built-in-workbooks"></a>Ingebouwde werkmappen gebruiken

1. Ga naar **werkmappen** en selecteer vervolgens **sjablonen** om de volledige lijst met ingebouwde Azure-Sentinel-werkmappen weer te geven. Om te zien welke relevant zijn voor de gegevens typen die u hebt verbonden, wordt in het veld **vereiste gegevens typen** in elke werkmap het gegevens type weer gegeven naast een groen vinkje als u al relevante gegevens streamt naar Azure Sentinel.
  ![Ga naar werkmappen](./media/tutorial-monitor-data/access-workbooks.png)
1. Klik op **sjabloon weer geven** om de sjabloon te bekijken die is ingevuld met uw gegevens.
  
1. Als u de werkmap wilt bewerken, selecteert u **Opslaan** en selecteert u vervolgens de locatie waar u het JSON-bestand voor de sjabloon wilt opslaan. 

   > [!NOTE]
   > Hiermee maakt u een Azure-resource op basis van de relevante sjabloon en slaat u het JSON-bestand van de werkmap en niet de gegevens op.


1. Selecteer **opgeslagen werkmap weer geven**. Klik vervolgens bovenaan op de knop **bewerken** . U kunt de werkmap nu bewerken en aanpassen op basis van uw behoeften. Zie [interactieve rapporten maken met Azure monitor werkmappen](../azure-monitor/visualize/workbooks-overview.md)voor meer informatie over het aanpassen van de werkmap.
![werkmappen weer geven](./media/tutorial-monitor-data/workbook-graph.png)
1. Nadat u de wijzigingen hebt aangebracht, kunt u de werkmap opslaan. 

1. U kunt de werkmap ook klonen: Selecteer **bewerken** en vervolgens **Opslaan als**, en zorg ervoor dat u deze opslaat onder een andere naam, onder hetzelfde abonnement en dezelfde resource groep. Deze gekloonde werkmappen worden weer gegeven op het tabblad **mijn werkmappen** .


## <a name="create-new-workbook"></a>Nieuwe werkmap maken

1. Ga naar **werkmappen** en selecteer vervolgens **werkmap toevoegen** om een nieuwe werkmap helemaal opnieuw te maken.
  ![Scherm opname van het nieuwe werk blad.](./media/tutorial-monitor-data/create-workbook.png)

1. Als u de werkmap wilt bewerken, selecteert u **bewerken** en voegt u indien nodig tekst, query's en para meters toe. Zie [interactieve rapporten maken met Azure monitor werkmappen](../azure-monitor/visualize/workbooks-overview.md)voor meer informatie over het aanpassen van de werkmap. 

1. Wanneer u een query bouwt, moet u ervoor zorgen dat de **gegevens bron** is ingesteld op **Logboeken** en dat het **Resource type** is ingesteld op **log Analytics**, en kies vervolgens de relevante werk ruimte (n). 

1. Nadat u de werkmap hebt gemaakt, slaat u de werkmap op en zorgt u ervoor dat u deze opslaat onder het abonnement en de resource groep van uw Azure Sentinel-werk ruimte.

1. Als u wilt dat anderen in uw organisatie de werkmap kunnen gebruiken, klikt u onder **Opslaan om** **gedeelde rapporten** te selecteren. Als u wilt dat deze werkmap alleen voor u beschikbaar is, selecteert u **mijn rapporten**.

1. Als u wilt scha kelen tussen werkmappen in uw werk ruimte, kunt u pictogram **openen** selecteren ![ om een werkmap te openen. ](./media/tutorial-monitor-data/switch.png) in het bovenste deel venster van een werkmap. In het venster dat aan de rechter kant wordt geopend, schakelt u tussen werkmappen.

   ![Scha kelen tussen werkmappen](./media/tutorial-monitor-data/switch-workbooks.png)


## <a name="print-a-workbook-or-save-as-pdf"></a>Een werkmap afdrukken of opslaan als PDF-bestand

Als u een werkmap wilt afdrukken of opslaan als PDF-bestand, gebruikt u het menu opties rechts van de werkmap titel.

1. Selecteer opties > :::image type="icon" source="media/whats-new/print-icon.png" border="false"::: **inhoud afdrukken**. 
2. Pas in het scherm afdrukken de afdruk instellingen aan de gewenste wijzigingen aan of selecteer **Opslaan als PDF-bestand** om het lokaal op te slaan.

Bijvoorbeeld:

:::image type="content" source="media/whats-new/print-workbook.png" alt-text="Uw werkmap afdrukken of opslaan als PDF-bestand.":::

## <a name="how-to-delete-workbooks"></a>Werkmappen verwijderen

Als u een opgeslagen werkmap (een opgeslagen sjabloon of een aangepaste werkmap) wilt verwijderen, selecteert u op de pagina werkmappen de opgeslagen werkmap die u wilt verwijderen en selecteert u **verwijderen**. Hiermee wordt de opgeslagen werkmap verwijderd.

> [!NOTE]
> Hiermee verwijdert u de werkmap resource en alle wijzigingen die u hebt aangebracht in de sjabloon. De oorspronkelijke sjabloon blijft beschikbaar.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u uw gegevens in azure Sentinel kunt visualiseren met behulp van Azure-werkmappen.

Zie voor meer informatie over het automatiseren van uw reacties op bedreigingen [automatische bedreigings reacties instellen in azure Sentinel](tutorial-respond-threats-playbook.md).
