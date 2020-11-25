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
ms.date: 07/20/2020
ms.openlocfilehash: b72d083216b7cd0ae7a588bfd721d8981829bfc7
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844364"
---
# <a name="visualize-data-with-power-bi"></a>Gegevens visualiseren met Power BI

In deze zelfstudie leert u hoe u een Power BI-werkruimte maakt, hoe u uw Azure Synapse-werkruimte koppelt en hoe u een Power BI-gegevensset maakt die gebruikmaakt van gegevens in uw Azure Synapse-werkruimte. 

## <a name="overview"></a>Overzicht

Aan de hand van de NYC-taxigegevens hebben we geaggregeerde gegevenssets gemaakt in twee tabellen:
- **nyctaxi.passengercountstats**
- **SQLDB1.dbo.PassengerCountStats**

U kunt een Power BI-werkruimte koppelen aan uw Azure Synapse-werkruimte. Zo kunt u eenvoudig gegevens in uw Power BI-werkruimte ophalen. U kunt uw Power BI-rapporten rechtstreeks in uw Azure Synapse-werkruimte bewerken.

### <a name="create-a-power-bi-workspace"></a>Een Power BI-werkruimte maken

1. Meld u aan bij [powerbi.microsoft.com](https://powerbi.microsoft.com/).
1. Maak een nieuwe Power BI-werkruimte met de naam **NYCTaxiWorkspace1**.

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>Uw Azure Synapse-werkruimte koppelen aan uw nieuwe Power BI-werkruimte

1. Ga in Synapse Studio naar **Beheren** > **Gekoppelde services**.
1. Selecteer **Nieuw** > **Verbinding maken met Power BI**
1. Stel **Naam** in op **NYCTaxiWorkspace1**
1. Stel **Werkruimtenaam** in op **NYCTaxiWorkspace1**
1. Selecteer **Maken**.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>Een Power BI-gegevensset maken die gebruikmaakt van gegevens in uw Azure Synapse-werkruimte

1. Ga in Synapse Studio naar **Ontwikkelen** > **Power BI**.
1. Ga naar **NYCTaxiWorkspace1** > **Power BI-gegevenssets** en selecteer **Nieuwe Power BI-gegevensset**.
1. Beweeg de muisaanwijzer over de database **SQLDB1** en selecteer **.pbids-bestand downloaden**.
1. Open het gedownloade **.pbids**-bestand. Power BI Desktop opent en maakt automatisch verbinding met **SQLDB1** in uw Azure Synapse-werkruimte.
1. Als er een dialoogvenster wordt weergegeven met de naam **SQL Server-database**:
    1. Selecteer **Microsoft-account**.
    1. Selecteer **Aanmelden** en meld u aan bij uw account.
    1. Selecteer **Verbinden**.
1. Wanneer het dialoogvenster **Navigator** is geopend, schakelt u de tabel **PassengerCountStats** in en selecteert u **Laden**.
1. Wanneer het dialoogvenster **Verbindingsinstellingen** wordt weergegeven, selecteert u **DirectQuery** > **OK**.
1. Selecteer de knop **Rapport** aan de linkerkant.
1. Voeg **Lijndiagram** toe aan uw rapport.
    1. Sleep de kolom **PasssengerCount** naar **Visualisaties** > **As**.
    1. Sleep de kolommen **SumTripDistance** en **AvgTripDistance** naar **Visualisaties** > **Waarden**.
1. Selecteer op het tabblad **Start** de optie **Publiceren**.
1. Selecteer **Opslaan** om uw wijzigingen op te slaan.
1. Kies de bestandsnaam **PassengerAnalysis.pbix** en selecteer **Opslaan**.
1. Kies bij **Een doel selecteren** de optie **NYCTaxiWorkspace1** en klik vervolgens op **Selecteren**.
1. Wacht tot het publiceren is voltooid.

### <a name="configure-authentication-for-your-dataset"></a>Verificatie configureren voor uw gegevensset

1. Open [powerbi.microsoft.com](https://powerbi.microsoft.com/) en kies **Aanmelden**.
1. Selecteer aan de linkerkant onder **Werkruimten** de werkruimte **NYCTaxiWorkspace1**.
1. Zoek in die werkruimte een gegevensset met de naam **Passenger Analysis** en een rapport met de naam **Passenger Analysis**.
1. Beweeg de muisaanwijzer over de gegevensset **PassengerAnalysis**, selecteer de knop met het weglatingsteken (...) en selecteer vervolgens **Instellingen**.
1. Stel in **Referenties voor de gegevensbron** de **Verificatiemethode** in op **OAuth2** en selecteer **Aanmelden**.

### <a name="edit-a-report-in-synapse-studio"></a>Een rapport bewerken in Synapse Studio

1. Ga terug naar Synapse Studio en selecteer **Sluiten en vernieuwen**.
1. Ga naar de hub **Ontwikkelen**.
1. Beweeg de muisaanwijzer over **Power BI** en selecteer het knooppunt voor **Power BI-rapporten** vernieuwen.
1. Onder **Power BI** zou het volgende moeten staan:
    * Onder **NYCTaxiWorkspace1** > **Power BI-gegevenssets**, een nieuwe gegevensset met de naam **PassengerAnalysis**.
    * Onder **NYCTaxiWorkspace1Power** > **Power BI-rapporten**, een nieuw rapport met de naam **PassengerAnalysis**.
1. Selecteer het rapport **PassengerAnalysis**. Het rapport wordt geopend en u kunt het rechtstreeks in Synapse Studio bewerken.



## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Controle](get-started-monitor.md)
                                 

