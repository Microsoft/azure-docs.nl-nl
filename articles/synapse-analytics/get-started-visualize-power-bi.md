---
title: 'Zelfstudie: Aan de slag met Azure Synapse Analytics: werkruimtegegevens visualiseren met Power BI'
description: In deze zelfstudie leert u hoe u een Power BI-werkruimte maakt, hoe u uw Azure Synapse-werkruimte koppelt en hoe u een Power BI-gegevensset maakt die gebruikmaakt van gegevens in de Azure Synapse-werkruimte.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: business-intelligence
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 952d69cccff86d1a0119391c400a40908c62ed69
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208961"
---
# <a name="visualize-data-with-power-bi"></a>Gegevens visualiseren met Power BI

In deze zelfstudie leert u hoe u een Power BI-werkruimte maakt, hoe u uw Azure Synapse-werkruimte koppelt en hoe u een Power BI-gegevensset maakt die gebruikmaakt van gegevens in uw Azure Synapse-werkruimte. 

> [!NOTE]
> [Installeer Power bi Desktop](https://aka.ms/pbidesktopstore)om deze zelf studie te volt ooien.

## <a name="overview"></a>Overzicht

Aan de hand van de NYC-taxigegevens hebben we geaggregeerde gegevenssets gemaakt in twee tabellen:
- **nyctaxi.passengercountstats**
- **SQLDB1.dbo.PassengerCountStats**

U kunt een Power BI-werkruimte koppelen aan uw Azure Synapse-werkruimte. Zo kunt u eenvoudig gegevens in uw Power BI-werkruimte ophalen. U kunt uw Power BI-rapporten rechtstreeks in uw Azure Synapse-werkruimte bewerken. 

### <a name="create-a-power-bi-workspace"></a>Een Power BI-werkruimte maken

1. Meld u aan bij [powerbi.microsoft.com](https://powerbi.microsoft.com/).
1. Klik op **werk ruimten** en selecteer vervolgens **een werk ruimte maken**. Maak een nieuwe Power BI-werk ruimte met de naam **NYCTaxiWorkspace1** of vergelijk bare, aangezien deze naam uniek moet zijn.

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>Uw Azure Synapse-werkruimte koppelen aan uw nieuwe Power BI-werkruimte

1. Ga in Synapse Studio naar **Beheren** > **Gekoppelde services**.
1. Selecteer **nieuwe**  >  **verbinding maken met Power bi**.
1. Stel **name** in op **NYCTaxiWorkspace1**.
1. Stel de naam van de **werk ruimte** in op de Power bi werk ruimte die u hierboven hebt gemaakt, vergelijkbaar met **NYCTaxiWorkspace1**.
1. Selecteer **Maken**.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>Een Power BI-gegevensset maken die gebruikmaakt van gegevens in uw Azure Synapse-werkruimte

1. Ga in Synapse Studio naar **Ontwikkelen** > **Power BI**.
1. Ga naar **NYCTaxiWorkspace1** > **Power BI-gegevenssets** en selecteer **Nieuwe Power BI-gegevensset**. Klik op **Start**.
1. Selecteer de gegevens bron **SQLPOOL1** en klik op **door gaan**.
1. Klik op **downloaden** om het. pbids-bestand voor uw **NYCTaxiWorkspace1SQLPOOL1. pbids** -bestand te downloaden. Klik op **Continue**.
1. Open het gedownloade **.pbids**-bestand. Power BI Desktop wordt geopend en er wordt automatisch verbinding gemaakt met **SQLDB1** in uw Azure Synapse-werk ruimte.
1. Als er een dialoog venster wordt weer gegeven met de naam **SQL Server Data Base**:
    1. Selecteer **Microsoft-account**.
    1. Selecteer **Aanmelden** en meld u aan bij uw account.
    1. Selecteer **Verbinden**.
1. Wanneer het dialoogvenster **Navigator** is geopend, schakelt u de tabel **PassengerCountStats** in en selecteert u **Laden**.
1. Wanneer het dialoogvenster **Verbindingsinstellingen** wordt weergegeven, selecteert u **DirectQuery** > **OK**.
1. Selecteer de knop **Rapport** aan de linkerkant.
1. Klik onder **Visualisaties** op het pictogram lijn diagram om een **lijn diagram** aan uw rapport toe te voegen.
    1. Sleep onder **velden** de kolom **PassengerCount** naar **Visualisaties**-  >  **as**.
    1. Sleep de kolommen **SumTripDistance** en **AvgTripDistance** naar **Visualisaties** > **Waarden**.
1. Selecteer op het tabblad **Start** de optie **Publiceren**.
1. Selecteer **Opslaan** om uw wijzigingen op te slaan.
1. Kies de bestandsnaam **PassengerAnalysis.pbix** en selecteer **Opslaan**.
1. Kies in het venster **publiceren naar Power bi** onder **Selecteer een bestemming** uw **NYCTaxiWorkspace1** en klik vervolgens op **selecteren**.
1. Wacht tot het publiceren is voltooid. 

### <a name="configure-authentication-for-your-dataset"></a>Verificatie configureren voor uw gegevensset

1. Open [powerbi.microsoft.com](https://powerbi.microsoft.com/) en kies **Aanmelden**.
1. Selecteer aan de linkerkant onder **Werkruimten** de werkruimte **NYCTaxiWorkspace1**.
1. Zoek in die werkruimte een gegevensset met de naam **Passenger Analysis** en een rapport met de naam **Passenger Analysis**.
1. Beweeg de muisaanwijzer over de gegevensset **PassengerAnalysis**, selecteer de knop met het weglatingsteken (...) en selecteer vervolgens **Instellingen**.
1. In **gegevens bron referenties**, klikt u op **bewerken**, stelt u de **verificatie methode** in op **OAuth2** en selecteert u **Aanmelden**.

### <a name="edit-a-report-in-synapse-studio"></a>Een rapport bewerken in Synapse Studio

1. Ga terug naar Synapse Studio en selecteer **Sluiten en vernieuwen**.
1. Ga naar de hub **Ontwikkelen**.
1. Rechts van de knop **Power bi** laag, beletsel teken (...) en klik op **vernieuwen** om het knoop punt **Power bi rapporten** te vernieuwen.
1. Onder **Power BI** zou het volgende moeten staan:
    * In **NYCTaxiWorkspace1**  >  **Power BI gegevens sets**, een nieuwe gegevensset met de naam **PassengerAnalysis**.
    * Onder **NYCTaxiWorkspace1Power** > **Power BI-rapporten**, een nieuw rapport met de naam **PassengerAnalysis**.
1. Selecteer het rapport **PassengerAnalysis**. Het rapport wordt geopend en u kunt het rechtstreeks in Synapse Studio bewerken.



## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Controle](get-started-monitor.md)
                                 

