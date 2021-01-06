---
title: IT Service Management-connector in Log Analytics
description: Dit artikel bevat een overzicht van IT Service Management-connector (ITSMC) en informatie over het gebruik ervan om ITSM-werk items in Log Analytics te controleren en te beheren en om snel problemen op te lossen.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: 072abffcc74b8b060d98c07d1f310413c7b323ef
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97954937"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-connector"></a>Verbinding maken tussen Azure en ITSM-hulpprogram ma's met behulp van IT Service Management-connector

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

In dit artikel vindt u informatie over het configureren van de IT Service Management-connector (ITSMC) in Log Analytics om uw werk items centraal te beheren.

## <a name="add-it-service-management-connector"></a>IT Service Management-connector toevoegen

Voordat u een verbinding kunt maken, moet u ITSMC toevoegen.

1. Selecteer in het Azure Portal **een resource maken**:

   ![Scherm opname van het menu-item een resource maken.](media/itsmc-overview/azure-add-new-resource.png)

2. Zoek naar **IT Service Management-connector** in azure Marketplace. Selecteer **maken**:

   ![Scherm opname waarin de knop maken wordt weer gegeven in azure Marketplace.](media/itsmc-overview/add-itsmc-solution.png)

3. Selecteer in de sectie **La Workspace** de Azure log Analytics-werk ruimte waar u ITSMC wilt installeren.
   >[!NOTE]
   >
   > * ITSMC kan alleen in Log Analytics-werk ruimten worden geïnstalleerd in de volgende regio's: VS-Oost, VS-West 2, Zuid-Centraal VS, West-Centraal VS, US Gov-Arizona, US Gov-Virginia, Canada-centraal, Europa-west, Zuid-Brittannië, Zuidoost-Azië, Japan-Oost, Centraal-India en Australië-zuidoost.

4. Selecteer in de sectie **log Analytics werk ruimte** de resource groep waar u de ITSMC-resource wilt maken:

   ![Scherm afbeelding van de sectie Log Analytics werk ruimte.](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >Als onderdeel van de doorlopende overgang van Microsoft Operations Management Suite (OMS) naar Azure Monitor worden OMS-werk ruimten nu aangeduid als *log Analytics*.

5. Selecteer **OK**.

Wanneer de ITSMC-resource wordt geïmplementeerd, wordt er een melding weer gegeven in de rechter bovenhoek van het venster.

## <a name="create-an-itsm-connection"></a>Een ITSM-verbinding maken

Nadat u ITSMC hebt geïnstalleerd, kunt u een verbinding maken.

Als u een verbinding wilt maken, moet u uw ITSM-hulp programma voorbereiden om de verbinding van ITSMC toe te staan.  

Op basis van het ITSM-product waarmee u verbinding maakt, selecteert u een van de volgende koppelingen voor instructies:

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

Nadat u uw ITSM-hulpprogram ma's hebt bereid, voert u de volgende stappen uit om een verbinding te maken:

1. Zoek in **alle resources** naar **Service Desk (*de naam van uw werk ruimte*)**:

   ![Scherm opname van recente resources in het Azure Portal.](media/itsmc-overview/itsm-connections.png)

1. Selecteer onder **gegevens bronnen voor werk ruimte** in het linkerdeel venster **ITSM-verbindingen**:

   ![Scherm opname van het menu-item ITSM-verbindingen.](media/itsmc-overview/add-new-itsm-connection.png)
1. Selecteer **verbinding toevoegen**.

1. Geef de verbindings instellingen op, zoals wordt beschreven op basis van ITSM-producten/-services:

    - [ServiceNow](./itsmc-connections-servicenow.md)
    - [System Center Service Manager](./itsmc-connections-scsm.md)
    - [Cherwell](./itsmc-connections-cherwell.md)
    - [Provance](./itsmc-connections-provance.md)

   > [!NOTE]
   >
   > De configuratie gegevens van de verbinding worden standaard elke 24 uur vernieuwd door ITSMC. Als u de gegevens van uw verbinding onmiddellijk wilt vernieuwen om te zien welke wijzigingen of sjabloon updates u aanbrengt, selecteert u de knop **synchroniseren** op de Blade van de verbinding:
   >
   > ![Scherm afbeelding met de knop synchroniseren op de Blade verbinding.](media/itsmc-overview/itsmc-connections-refresh.png)

## <a name="use-itsmc"></a>ITSMC gebruiken

   U kunt ITSMC gebruiken om waarschuwingen te maken van Azure Monitor waarschuwingen in het ITSM-hulp programma.

## <a name="create-itsm-work-items-from-azure-alerts"></a>ITSM-werk items maken op basis van Azure-waarschuwingen

Nadat u uw ITSM-verbinding hebt gemaakt, kunt u in uw ITSM-hulp programma werk items maken op basis van Azure-waarschuwingen. Als u de werk items wilt maken, gebruikt u de actie ITSM in actie groepen.

Actie groepen bieden een modulaire en herbruikbare manier om acties voor uw Azure-waarschuwingen te activeren. U kunt actie groepen met metrische waarschuwingen, waarschuwingen voor activiteiten logboeken en waarschuwingen voor Azure Log Analytics gebruiken in de Azure Portal.

> [!NOTE]
> Nadat u de ITSM-verbinding hebt gemaakt, moet u 30 minuten wachten totdat het synchronisatie proces is voltooid.

### <a name="template-definitions"></a>Sjabloon definities

   Er zijn typen werk items die sjablonen kunnen gebruiken die zijn gedefinieerd door het ITSM-hulp programma.
Met behulp van sjablonen kunt u velden definiëren die automatisch worden ingevuld op basis van vaste waarden die zijn gedefinieerd als onderdeel van de actie groep. U definieert sjablonen in het hulp programma ITSM.
U kunt definiëren welke sjabloon u wilt gebruiken als onderdeel van de definitie van de actie groep.

Gebruik de volgende procedure om actie groepen te maken:

1. Selecteer in de Azure Portal  **waarschuwingen**.
2. Selecteer in het menu boven aan het scherm **acties beheren**:

    ![Scherm afbeelding met de menu opdracht acties beheren.](media/itsmc-overview/action-groups-selection-big.png)

   Het venster **actie groep maken** wordt weer gegeven.

3. Selecteer het **abonnement** en de **resource groep** waar u uw actie groep wilt maken. Geef een naam op voor de **actie groep** en de **weergave naam** voor uw actie groep. Selecteer **volgende: meldingen**.

    ![Scherm opname van het venster actie groep maken.](media/itsmc-overview/action-groups-details.png)

4. Selecteer in de lijst met meldingen **volgende: acties**.
5. Selecteer in de lijst acties de optie **ITSM** in de lijst **actie type** . Geef een **naam** op voor de actie. Selecteer de knop pen die de **Details** van de bewerking weergeeft.

    ![Scherm afbeelding waarin de definitie van de actie groep wordt weer gegeven.](media/itsmc-definition/action-group-pen.png)

6. Selecteer in de lijst **abonnement** het abonnement waarin uw log Analytics-werk ruimte zich bevindt. Selecteer in de lijst **verbinding** de naam van uw ITSM-connector. Deze wordt gevolgd door de naam van uw werk ruimte. Bijvoorbeeld MyITSMConnector (MyWorkspace).

7. Selecteer een type **werk item** .

8. Als u velden met vaste waarden wilt invullen, selecteert u **aangepaste sjabloon gebruiken**. Als dat niet het geval is, kiest u een bestaande [sjabloon](#template-definitions) in de lijst **sjabloon** en voert u de vaste waarden in de sjabloon velden in.

9. In de laatste sectie van de groeps definitie van de actie ITSM kunt u definiëren hoeveel werk items worden gemaakt voor elke waarschuwing.

    >[!NOTE]
    >
    > * Deze sectie is alleen relevant voor het vastleggen van zoek waarschuwingen in een logboek.
    > * Met metrische waarschuwingen en waarschuwingen voor activiteiten Logboeken wordt altijd één werk item per waarschuwing gemaakt.

    * In het geval dat u selecteert in de vervolg keuzelijst voor werk items "incident" of "waarschuwing":
        * Als u het selectie vakje **' afzonderlijke werk items voor elk configuratie-item maken '** inschakelt, wordt in elk configuratie-item in elke waarschuwing een nieuw werk item gemaakt. Er kan meer dan één werk item per configuratie-item in het ITSM-systeem zijn.

            Bijvoorbeeld:
            1) Waarschuwing 1 met 3 configuratie-items: A, B, C-worden drie werk items gemaakt.
            2) Waarschuwing 2 met 1 configuratie-item: A-maakt 1 werk item.

        * Als u het selectie vakje **' afzonderlijke werk items voor elk configuratie-item maken '** uitschakelt, wordt in ITSM-connector één werk item voor elke waarschuwings regel gemaakt en toegevoegd aan alle beïnvloede configuratie-items. Er wordt een nieuw werk item gemaakt als de vorige taak is gesloten.
        >[!NOTE]
        > In dit geval worden met een deel van de geactiveerde waarschuwing geen nieuwe werk items gegenereerd in het ITSM-hulp programma.

            For example:
            1) Alert 1 with 3 Configuration Items: A, B, C - will create 1 work item.
            2) Alert 2 for the same alert rule as phase 1 with 1 Configuration Item: D - will be merged to the work item in phase 1.
            3) Alert 3 for a different alert rule with 1 Configuration Item: E - will create 1 work item.

       ![Scherm opname van het ITSM-incident venster.](media/itsmc-overview/itsm-action-configuration.png)

    * In het geval dat u selecteert in de vervolg keuzelijst werk item:
        * Als u **' afzonderlijke werk items maken voor elke logboek vermelding (configuratie-item) selecteert, is dit veld niet ingevuld. Kan resulteren in grote hoeveel heden werk items.) "** in de keuze rondjes selectie wordt een waarschuwing gemaakt per rij in de zoek resultaten van de waarschuwing zoek opdracht in Logboeken. In de payload van de waarschuwing krijgt de eigenschap Description de rij uit de zoek resultaten.
        * Als u in de selectie keuze rondjes **' afzonderlijke werk items maken voor elk configuratie-item '** selecteert, wordt voor elk configuratie-item in elke waarschuwing een nieuw werk item gemaakt. Er kan meer dan één werk item per configuratie-item in het ITSM-systeem zijn. Dit komt overeen met het selectie vakje inschakelen in de sectie incident/waarschuwing.
    ![Scherm opname van het ITSM-gebeurtenis venster.](media/itsmc-overview/itsm-action-configuration-event.png)

10. Selecteer **OK**.

Wanneer u een Azure-waarschuwings regel maakt of bewerkt, gebruikt u een actie groep met een ITSM-actie. Wanneer de waarschuwing wordt geactiveerd, wordt het werk item gemaakt of bijgewerkt in het ITSM-hulp programma.

> [!NOTE]
>
>- Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/monitor/) voor actie groepen voor meer informatie over de prijzen van de actie ITSM.
>
>
>- Het veld korte beschrijving in de definitie van de waarschuwings regel is beperkt tot 40 tekens wanneer u het verzendt met behulp van de actie ITSM.

## <a name="next-steps"></a>Volgende stappen

* [Problemen oplossen in ITSM-connector](./itsmc-resync-servicenow.md)
