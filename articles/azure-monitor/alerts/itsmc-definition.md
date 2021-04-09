---
title: IT Service Management-connector in Log Analytics
description: Dit artikel bevat een overzicht van IT Service Management-connector (ITSMC) en informatie over het gebruik ervan om ITSM-werk items in Log Analytics te controleren en te beheren en om snel problemen op te lossen.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: 98f53ec1b6506a6d47146377e837576254f445e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103601063"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-solution"></a>Verbinding maken tussen Azure en ITSM-hulpprogram ma's met behulp van IT Service Management-oplossing

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

In dit artikel vindt u informatie over het configureren van IT Service Management-connector (ITSMC) in Log Analytics voor het centraal beheren van uw ITSM-werk items (IT Service Management).

## <a name="add-it-service-management-connector"></a>IT Service Management-connector toevoegen

Voordat u een verbinding kunt maken, moet u ITSMC installeren.

1. Selecteer in het Azure Portal **een resource maken**:

   ![Scherm opname van het menu-item voor het maken van een resource.](media/itsmc-overview/azure-add-new-resource.png)

2. Zoek naar **IT Service Management-connector** in azure Marketplace. Selecteer vervolgens **Maken**:

   ![Scherm opname waarin de knop maken wordt weer gegeven in azure Marketplace.](media/itsmc-overview/add-itsmc-solution.png)

3. Selecteer in de sectie **La Workspace** de log Analytics-werk ruimte waar u ITSMC wilt installeren.
   > [!NOTE]
   > U kunt ITSMC in Log Analytics-werk ruimten alleen installeren in de volgende regio's: VS-Oost, VS-West 2, Zuid-Centraal VS, West-Centraal VS, US Gov-Arizona, US Gov-Virginia, Canada-centraal, Europa-west, Zuid-Brittannië, Zuidoost-Azië, Japan-Oost, Centraal-India en Australië-zuidoost.

4. Selecteer in de sectie **log Analytics werk ruimte** de resource groep waar u de ITSMC-resource wilt maken:

   ![Scherm afbeelding van de sectie Log Analytics werk ruimte.](media/itsmc-overview/itsmc-solution-workspace.png)
   
   > [!NOTE]
   > Als onderdeel van de lopende overgang van Microsoft Operations Management Suite (OMS) naar Azure Monitor worden OMS-werk ruimten nu *log Analytics-werk ruimten* genoemd.

5. Selecteer **OK**.

Wanneer de ITSMC-resource wordt geïmplementeerd, wordt er een melding weer gegeven in de rechter bovenhoek van het venster.

## <a name="create-an-itsm-connection"></a>Een ITSM-verbinding maken

Nadat u ITSMC hebt geïnstalleerd, moet u het ITSM-hulp programma voorbereiden om de verbinding van ITSMC toe te staan. Op basis van het ITSM-product waarmee u verbinding maakt, selecteert u een van de volgende koppelingen voor instructies:

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

Nadat u het ITSM-hulp programma hebt bereid, voert u de volgende stappen uit om een verbinding te maken:

1. Zoek in **alle resources** naar **Service Desk (*de naam van uw werk ruimte*)**:

   ![Scherm opname van recente resources in het Azure Portal.](media/itsmc-definition/create-new-connection-from-resource.png)

1. Selecteer onder **gegevens bronnen voor werk ruimte** in het linkerdeel venster **ITSM-verbindingen**:

   ![Scherm opname van het menu-item ITSM-verbindingen.](media/itsmc-overview/add-new-itsm-connection.png)

1. Selecteer **verbinding toevoegen**.

1. Geef de verbindings instellingen op volgens het ITSM-product dat u gebruikt:

    - [ServiceNow](./itsmc-connections-servicenow.md)
    - [System Center Service Manager](./itsmc-connections-scsm.md)
    - [Cherwell](./itsmc-connections-cherwell.md)
    - [Provance](./itsmc-connections-provance.md)

   > [!NOTE]
   > De configuratie gegevens van de verbinding worden standaard elke 24 uur vernieuwd door ITSMC. Als u de gegevens van uw verbinding onmiddellijk wilt vernieuwen om te zien welke wijzigingen of sjabloon updates u aanbrengt, selecteert u de knop **synchroniseren** in het deel venster van de verbinding:
   >
   > ![Scherm afbeelding met de knop synchroniseren in het deel venster van de verbinding.](media/itsmc-overview/itsmc-connections-refresh.png)

## <a name="create-itsm-work-items-from-azure-alerts"></a>ITSM-werk items maken op basis van Azure-waarschuwingen

Nadat u uw ITSM-verbinding hebt gemaakt, kunt u ITMC gebruiken om werk items te maken in uw ITSM-hulp programma op basis van Azure-waarschuwingen. Als u de werk items wilt maken, gebruikt u de actie ITSM in actie groepen.

Actie groepen bieden een modulaire en herbruikbare manier om acties voor uw Azure-waarschuwingen te activeren. U kunt actie groepen gebruiken met metrische waarschuwingen, waarschuwingen voor activiteiten logboeken en Log Analytics waarschuwingen in de Azure Portal.

> [!NOTE]
> Nadat u de ITSM-verbinding hebt gemaakt, moet u 30 minuten wachten totdat het synchronisatie proces is voltooid.

## <a name="define-a-template"></a>Een sjabloon definiëren

Bepaalde typen werk items kunnen sjablonen gebruiken die u in het ITSM-hulp programma definieert. Met behulp van sjablonen kunt u velden definiëren die automatisch worden ingevuld op basis van vaste waarden voor een actie groep. U kunt definiëren welke sjabloon u wilt gebruiken als onderdeel van de definitie van een actie groep. U vindt in ServiceNow docs-informatie over het maken van sjablonen (hier) [ https://docs.servicenow.com/bundle/paris-platform-administration/page/administer/form-administration/task/t_CreateATemplateUsingTheTmplForm.html ].

Een actie groep maken:

1. Selecteer in de Azure Portal  **waarschuwingen**.
2. Selecteer in het menu boven aan het scherm **acties beheren**:

    ![Scherm afbeelding met de menu opdracht acties beheren.](media/itsmc-overview/action-groups-selection-big.png)

   Het venster **actie groep maken** wordt weer gegeven.

3. Selecteer het **abonnement** en de **resource groep** waar u uw actie groep wilt maken. Geef waarden op in de naam van de **actie groep** en de **weergave naam** voor uw actie groep. Selecteer **volgende: meldingen**.

    ![Scherm opname van het venster actie groep maken.](media/itsmc-overview/action-groups-details.png)

4. Op het tabblad **meldingen** selecteert u **volgende: acties**.
5. Selecteer **ITSM** in de lijst **actie type** op het tabblad **acties** . Geef bij **naam** een naam op voor de actie. Selecteer vervolgens de knop pen die de **Details** van de bewerking weergeeft.

    ![Scherm opname van selecties voor het maken van een actie groep.](media/itsmc-definition/action-group-pen.png)

6. Selecteer in de lijst **abonnement** het abonnement met uw log Analytics-werk ruimte. Selecteer in de lijst **verbinding** de naam van uw ITSM-connector. Deze wordt gevolgd door de naam van uw werk ruimte. Een voor beeld is *MyITSMConnector (MyWorkspace)*.

7. Selecteer een type **werk item** .

8. Als u velden met vaste waarden wilt invullen, selecteert u **aangepaste sjabloon gebruiken**. Als dat niet het geval is, kiest u een bestaande [sjabloon](#define-a-template) in de lijst **sjabloon** en voert u de vaste waarden in de sjabloon velden in.

9. In de laatste sectie van de interface voor het maken van een ITSM-actie groep kunt u definiëren hoeveel werk items worden gemaakt voor elke waarschuwing.

   > [!NOTE]
   > Deze sectie is alleen relevant voor waarschuwingen voor logboek registratie. Voor alle andere waarschuwings typen maakt u per waarschuwing één werk item.

   * Als u **incident** of **waarschuwing** hebt geselecteerd in de vervolg keuzelijst **werk item** , hebt u de mogelijkheid om afzonderlijke werk items te maken voor elk configuratie-item.
    
     ![Scherm afbeelding van het I T n M-ticket gebied met het incident geselecteerd als een werk item.](media/itsmc-overview/itsm-action-configuration.png)
    
     * Als u het selectie vakje **afzonderlijke werk items voor elk configuratie-item maken** inschakelt, wordt in elk configuratie-item in elke waarschuwing een nieuw werk item gemaakt. Omdat er verschillende waarschuwingen voor dezelfde betrokken configuratie-items worden uitgevoerd, is er meer dan één werk item voor elk configuratie-item.

       Een waarschuwing die drie configuratie-items heeft, maakt bijvoorbeeld drie werk items. Bij een waarschuwing met één configuratie-item wordt één werk item gemaakt.
        
     * Als u het selectie vakje **afzonderlijke werk items voor elk configuratie-item maken** uitschakelt, wordt in ITSMC één werk item voor elke waarschuwings regel gemaakt en worden alle betrokken configuratie-items toegevoegd. Er wordt een nieuw werk item gemaakt als de vorige taak is gesloten.

       >[!NOTE]
       > In dit geval genereren enkele van de geactiveerde waarschuwingen geen nieuwe werk items in het ITSM-hulp programma.

       Een waarschuwing die drie configuratie-items heeft, maakt bijvoorbeeld één werk item. Als een waarschuwing voor dezelfde waarschuwings regel als het vorige voor beeld een configuratie-item heeft, wordt dat configuratie-item gekoppeld aan de lijst met betrokken configuratie-items in het gemaakte werk item. Met een waarschuwing voor een andere waarschuwings regel met één configuratie-item wordt één werk item gemaakt.

   * Als u **gebeurtenis** hebt geselecteerd in de vervolg keuzelijst **werk item** , kunt u ervoor kiezen om afzonderlijke werk items te maken voor elk logboek vermelding of voor elk configuratie-item.
    
     ![Scherm afbeelding van het I T M-ticket gebied met de gebeurtenis geselecteerd als een werk item.](media/itsmc-overview/itsm-action-configuration-event.png)

     * Als u **afzonderlijke werk items maken selecteert voor elk logboek item (configuratie-item veld wordt niet ingevuld. Kan resulteren in grote aantallen werk items.)**, er wordt een werk item gemaakt voor elke rij in de zoek resultaten van de waarschuwing zoeken in Logboeken. De eigenschap Description in de payload van het werk item bevat de rij uit de zoek resultaten.
      
     * Als u **afzonderlijke werk items maken voor elk configuratie-item** selecteert, wordt voor elk configuratie-item in elke waarschuwing een nieuw werk item gemaakt. Elk configuratie-item kan meer dan één werk item bevatten in het ITSM-systeem. Deze optie is hetzelfde als het selectie vakje dat wordt weer gegeven wanneer u **incident** als het type werk item selecteert.

10. Selecteer **OK**.

Wanneer u een Azure-waarschuwings regel maakt of bewerkt, gebruikt u een actie groep met een ITSM-actie. Wanneer de waarschuwing wordt geactiveerd, wordt het werk item gemaakt of bijgewerkt in het ITSM-hulp programma.

> [!NOTE]
> Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/monitor/) voor actie groepen voor meer informatie over de prijzen van de actie ITSM.
>
> Het veld korte beschrijving in de definitie van de waarschuwings regel is beperkt tot 40 tekens wanneer u het verzendt met behulp van de actie ITSM.

## <a name="next-steps"></a>Volgende stappen

* [Problemen oplossen in ITSMC](./itsmc-resync-servicenow.md)
