---
title: Gegevens toegangs beheer op afstand controleren-Azure | Microsoft Docs
description: In dit artikel vindt u informatie over het configureren van toegangs beheer voor de Time Series Insights-telemetrie-Verkenner in de oplossings versneller voor externe controle
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/06/2018
ms.topic: conceptual
ms.openlocfilehash: 9d5d572c3e32e3645e65ba8d6fc28b567b3c1e9a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005959"
---
# <a name="configure-access-controls-for-the-time-series-insights-telemetry-explorer"></a>Toegangs beheer voor de Time Series Insights telemetrie-Verkenner configureren

In dit artikel vindt u informatie over het configureren van toegangs beheer voor de Time Series Insights Explorer in de oplossing voor externe controle. Als u wilt dat gebruikers van de oplossings versneller toegang hebben tot de Time Series Insights Explorer, moet u elke gebruikers gegevens toegang verlenen.

Beleid voor gegevenstoegang verleent machtigingen voor het uitvoeren van gegevensquery's, het bewerken van referentiegegevens in de omgeving en het delen van opgeslagen query's en perspectieven die aan de omgeving zijn gekoppeld.

## <a name="grant-data-access"></a>Gegevenstoegang verlenen

Volg deze stappen om toegang tot gegevens toe te kennen voor een gebruikers-principal:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Zoek uw Time Series Insights omgeving. Typ **time reeks** in het **zoekvak** . Selecteer de **Time Series-omgeving** in de zoek resultaten. 

3. Selecteer uw Time Series Insights-omgeving in de lijst.

4. Selecteer **beleid voor gegevens toegang** en selecteer **+ toevoegen**.
    ![Beheren van de Time Series Insights-bron - omgeving](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access1.png)

5. Selecteer **gebruiker selecteren**.  Zoek naar de gebruikers naam of het e-mail adres om de gebruiker te zoeken die u wilt toevoegen. Klik op **selecteren** om de selectie te bevestigen. 

    ![Beheren van de Time Series Insights-bron - toevoegen](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access2.png)

6. Selecteer **rol selecteren**. Kies de juiste Access-rol voor de gebruiker:
   - Selecteer **Inzender** als u wilt toestaan dat de gebruiker referentie gegevens kan wijzigen en opgeslagen query's en perspectieven kan delen met andere gebruikers van de omgeving. 
   - Anders selecteert u **lezer** om gebruikers query gegevens in de omgeving toe te staan en persoonlijke (niet-gedeelde) query's in de omgeving op te slaan.

     Selecteer **OK** om de gewenste rol te bevestigen.

     ![Beheren van de Time Series Insights-bron - gebruiker selecteren](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access3.png)

7. Selecteer **OK** op de pagina **gebruikersrol selecteren** .

    ![Beheren van de Time Series Insights-bron - rol selecteren](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access4.png)

8. De pagina **Data Access policies** bevat de gebruikers en de rol (len) voor elke gebruiker.

    ![Beheren van de Time Series Insights-bron - resultaten](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u toegangs beheer voor de Time Series Insights Explorer kunt verlenen in de oplossing voor externe controle.

Zie [architectuur voor externe bewaking](iot-accelerators-remote-monitoring-sample-walkthrough.md) voor meer conceptuele informatie over de oplossings versneller voor externe controle

Zie [een micro service aanpassen en opnieuw implementeren](iot-accelerators-microservices-example.md) voor meer informatie over het aanpassen van de oplossing voor controle op afstand.
<!-- Next tutorials in the sequence -->