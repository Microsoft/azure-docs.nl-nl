---
title: Waarschuwings instanties in Azure Monitor beheren
description: Waarschuwings instanties in azure beheren
ms.subservice: alerts
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 82905cba5f391365ada13f4e5df5ad139f4c121e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734805"
---
# <a name="manage-alert-instances-with-unified-alerts"></a>Waarschuwings instanties met Unified Alerts beheren

Met de [geïntegreerde ervaring voor waarschuwingen](./alerts-overview.md) in azure monitor kunt u al uw verschillende soorten waarschuwingen in azure zien. Dit omvat meerdere abonnementen, in één deel venster. In dit artikel wordt beschreven hoe u uw waarschuwings instanties kunt weer geven en hoe u specifieke waarschuwings instanties vindt voor het oplossen van problemen.

> [!NOTE]
> U hebt alleen toegang tot waarschuwingen die in de afgelopen 30 dagen zijn gegenereerd.

## <a name="go-to-the-alerts-page"></a>Ga naar de pagina waarschuwingen

U kunt op de volgende manieren naar de pagina waarschuwingen gaan:

- Selecteer in de [Azure Portal](https://portal.azure.com/) **controle**  >  **waarschuwingen**.  

     ![Scherm afbeelding van monitor waarschuwingen](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
- De context van een specifieke resource gebruiken. Open een resource, ga naar de sectie **bewaking** en kies **waarschuwingen**. De landings pagina wordt vooraf gefilterd op waarschuwingen voor die specifieke resource.

     ![Scherm opname van waarschuwingen voor het controleren van resources](media/alerts-managing-alert-instances/alert-resource.JPG)

- De context van een specifieke resource groep gebruiken. Open een resource groep, ga naar de sectie **bewaking** en kies **waarschuwingen**. De landings pagina wordt vooraf gefilterd op waarschuwingen voor die specifieke resource groep.    

     ![Scherm opname van bewakings waarschuwingen van resource groep](media/alerts-managing-alert-instances/alert-rg.JPG)

## <a name="find-alert-instances"></a>Waarschuwings instanties zoeken

Op de pagina **overzicht van waarschuwingen** vindt u een overzicht van alle waarschuwings exemplaren in Azure. U kunt de samenvattings weergave wijzigen door **meerdere abonnementen** te selecteren (Maxi maal 5) of door te filteren op **resource groepen**, specifieke **resources** of **Peri Oden**. Selecteer **Totaal aantal waarschuwingen** of een van de ernst banden om naar de lijst weergave voor uw waarschuwingen te gaan.     

![Scherm afbeelding van de pagina overzicht van waarschuwingen](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
Op de pagina **alle waarschuwingen** worden alle waarschuwings exemplaren in azure weer gegeven. Als u vanuit een waarschuwings melding naar de portal komt, kunt u de beschik bare filters gebruiken om te beperken op dat specifieke waarschuwings exemplaar.

> [!NOTE]
> Als u op de pagina bent gekomen door een van de ernst banden te selecteren, wordt de lijst vooraf gefilterd op basis van de ernst.

Naast de filters die beschikbaar zijn op de vorige pagina, kunt u ook filteren op basis van de bewakende service (bijvoorbeeld platform voor metrische gegevens), de bewaken van de voor waarde (geactiveerd of opgelost), Ernst, waarschuwings status (nieuw/bevestigd/gesloten) of de ID van de Smart-groep.

![Scherm afbeelding van alle pagina waarschuwingen](media/alerts-managing-alert-instances/all-alerts.jpg)

> [!NOTE]
> Als u op de pagina bent gekomen door een van de ernst banden te selecteren, wordt de lijst vooraf gefilterd op basis van de ernst.

Als u een waarschuwings instantie selecteert, wordt de pagina **waarschuwings Details** geopend, zodat u meer informatie over dat specifieke waarschuwings exemplaar kunt zien.   

![Scherm afbeelding van de pagina met waarschuwings Details](media/alerts-managing-alert-instances/alert-details.jpg)