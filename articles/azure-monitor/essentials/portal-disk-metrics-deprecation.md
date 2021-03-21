---
title: Afschaffing van metrische schijf gegevens in de Azure Portal | Microsoft Docs
description: Meer informatie over de schijf metrieken die zijn afgeschaft en hoe u uw metrische waarschuwingen bijwerkt om nieuwe metrische gegevens te gebruiken.
services: azure-monitor
ms.topic: conceptual
author: albecker1
ms.author: albecker
ms.date: 03/12/2020
ms.openlocfilehash: 607ccacbfff86decaecaad0a869c8c0fe7500680
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033398"
---
# <a name="disk-metrics-deprecation-in-the-azure-portal"></a>Afschaffing van metrische schijf gegevens in de Azure Portal

Afgeschafte metrische gegevens over schijven worden binnenkort uit de Azure Portal verwijderd. Er is een nieuwe versie van elke gedeprecieerde metriek beschikbaar die u kunt gebruiken. Dit artikel laat u zien welke metrische gegevens nieuw zijn en hoe u uw metrische waarschuwingen bijwerkt om ze te gebruiken.

## <a name="list-of-new-metrics"></a>Lijst met nieuwe metrische gegevens

Deze tabel wijst elke afgeschafte metriek toe aan de bijbehorende nieuwe metriek. 

|Afgeschafte metriek|Nieuwe metrische gegevens (vervanging)|
|----|----|
|WACHTRIJ diepte van de gegevens schijf (afgeschaft)|Wachtrij diepte van gegevens schijf (preview-versie)|
|Gegevens schijf gelezen bytes per seconde (afgeschaft)|Gegevens schijf gelezen bytes per seconde (preview)|
|Lees bewerkingen op de gegevens schijf per seconde (afgeschaft)|Lees bewerkingen op de gegevens schijf per seconde (preview)|
|Gegevens schijf schrijf bewerkingen in bytes per seconde (afgeschaft)|Geschreven bytes per seconde (preview) gegevens schijf|
|Schrijf bewerkingen op de gegevens schijf per seconde (afgeschaft)|Schrijf bewerkingen op de gegevens schijf per seconde (preview)|
|WACHTRIJ diepte van besturings systeem (afgeschaft)|Wachtrij diepte van besturings systeem (preview-versie)|
|Door het systeem gelezen bytes per seconde (afgeschaft)|Door het besturings systeem gelezen bytes per seconde (preview)|
|Lees bewerkingen van het besturings systeem/SEC (afgeschaft)|Lees bewerkingen van het besturings systeem per seconde (preview)|
|Door het besturings systeem geschreven bytes per seconde (afgeschaft)|Door het besturings systeem geschreven bytes per seconde (preview)|
|Schrijf bewerkingen in het besturings systeem/SEC (afgeschaft)|Schrijf bewerkingen van het besturings systeem per seconde (preview-versie)|

<a id="update-metrics" />

## <a name="migrate-metrics-in-your-metric-alerts"></a>Metrische gegevens in uw metrische waarschuwingen migreren

Werk uw metrische waarschuwingen bij om nieuwe metrische gegevens te gebruiken.

1. Zoek in het Azure Portal naar **waarschuwingen**. Kies vervolgens in de sectie **Services** **waarschuwingen**.

   > [!div class="mx-imgBorder"]
   > ![Waarschuwings service](./media/portal-disk-metrics-deprecation/alert-service-azure-portal.png)

2. Kies op de pagina **waarschuwingen** de knop **waarschuwings regels beheren** . 

   > [!div class="mx-imgBorder"]
   > ![Waarschuwingsregels beheren](./media/portal-disk-metrics-deprecation/manage-alert-rules-button.png)

3. Schakel in de vervolg keuzelijst **resource groep** het selectie vakje **virtual machines** in en selecteer in de vervolg keuzelijst **type signaal** het selectie vakje **metrische gegevens** . 

   > [!div class="mx-imgBorder"]
   > ![Waarschuwingen filteren](./media/portal-disk-metrics-deprecation/filter-alerts.png)

4. In de lijst met metrische gegevens identificeert u de voor waarden die betrekking hebben op schijven. Klik op de naam van de regel. 

   De naam wordt weer gegeven als een Hyper link in de kolom **naam** van de tabel.

   > [!div class="mx-imgBorder"]
   > ![Schijf voorwaarden zoeken](./media/portal-disk-metrics-deprecation/find-disk-conditions.png)

5. Klik in de sectie **voor waarden** van de pagina **regels beheren** op de voor waarde van de waarschuwing. 

   De voor waarde wordt weer gegeven als een Hyper link.  

   > [!div class="mx-imgBorder"]
   > ![Voor waarden aanpassen](./media/portal-disk-metrics-deprecation/adjust-condition.png)

   De pagina **signaal logica configureren** wordt weer gegeven en de instellingen van de voor waarde worden weer gegeven in de sectie **waarschuwings logica** van die pagina.

6. Maak een record van deze instellingen, omdat deze verdwijnen wanneer u de afgeschafte metriek verwijdert.

   > [!div class="mx-imgBorder"]
   > ![Regels voor voor waarden](./media/portal-disk-metrics-deprecation/condition-rules.png)

   > [!TIP] 
   > U kunt deze instellingen vastleggen in een scherm opname of in een tekst bestand. 

7. Klik op de koppeling **terug naar signaal selectie** .

   > [!div class="mx-imgBorder"]
   > ![Terug naar signaal selectie](./media/portal-disk-metrics-deprecation/back-to-signal-selection.png)

8. Kies op de pagina **signaal logica configureren** de juiste vervangings metriek (nieuwe metrische gegevens). Gebruik de [tabel](#update-metrics) die eerder in dit artikel wordt weer gegeven om de naam van de nieuwe metrische gegevens te identificeren.

   > [!TIP] 
   > Begin met typen in de zoek balk om de lijst met metrische namen te beperken. 

   > [!div class="mx-imgBorder"]
   > ![Nieuwe metrische gegevens kiezen](./media/portal-disk-metrics-deprecation/choose-new-metric.png)

9. Klik op de knop **gereed** . 

   > [!div class="mx-imgBorder"]
   > ![Nieuwe metriek instellen](./media/portal-disk-metrics-deprecation/set-new-metric.png)

10. Sla uw wijzigingen op door de knop **Opslaan** te kiezen. 

    > [!div class="mx-imgBorder"]
    > ![Nieuwe metrische gegevens opslaan](./media/portal-disk-metrics-deprecation/save-new-metric.png)






