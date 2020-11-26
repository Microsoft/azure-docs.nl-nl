---
title: IT Service Management-connector in Log Analytics
description: Dit artikel bevat een overzicht van IT Service Management-connector (ITSMC) en informatie over het gebruik ervan om ITSM-werk items in Log Analytics te controleren en te beheren en om snel problemen op te lossen.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: b4e35296a999070a6f536f4a52cfb7c3a1d4f42a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186487"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-connector"></a>Verbinding maken tussen Azure en ITSM-hulpprogram ma's met behulp van IT Service Management-connector

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

Met IT Service Management-connector (ITSMC) kunt u Azure verbinden met een ondersteund ITSM-product of-service (IT Service Management).

Azure-Services zoals Azure Log Analytics en Azure Monitor bieden hulp middelen voor het detecteren, analyseren en oplossen van problemen met uw Azure-en niet-Azure-resources. Maar de werk items die betrekking hebben op een probleem bevinden zich doorgaans in een ITSM-product of-service. ITSMC biedt een bidirectionele verbinding tussen Azure-en ITSM-hulpprogram ma's die u helpen problemen sneller op te lossen.

ITSMC ondersteunt verbindingen met de volgende ITSM-hulpprogram ma's:

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

   >[!NOTE]
> Met ingang van 1-okt-2020 Cher well-en Provance ITSM-integratie met Azure-waarschuwing wordt niet langer ingeschakeld voor nieuwe klanten. Nieuwe ITSM-verbindingen worden niet ondersteund. Bestaande ITSM-verbindingen worden ondersteund.

Met ITSMC kunt u het volgende doen:

-  Maak werk items in uw ITSM-hulp programma, op basis van uw Azure-waarschuwingen (metrische waarschuwingen, waarschuwingen voor activiteiten logboeken en Log Analytics waarschuwingen).
-  U kunt eventueel uw incident synchroniseren en gegevens van uw ITSM-hulp programma wijzigen in een Azure Log Analytics-werk ruimte.

Zie de [privacyverklaring van micro soft](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9)voor informatie over juridische voor waarden en het privacybeleid.

U kunt ITSMC gaan gebruiken door de volgende stappen uit te voeren:

1.  [Voeg ITSMC toe.](#add-it-service-management-connector)
2.  [Maak een ITSM-verbinding.](#create-an-itsm-connection)
3.  [Gebruik de verbinding.](#use-itsmc)


##  <a name="add-it-service-management-connector"></a>IT Service Management-connector toevoegen

Voordat u een verbinding kunt maken, moet u ITSMC toevoegen.

1. Selecteer in het Azure Portal **een resource maken**:

   ![Scherm opname van het menu-item een resource maken.](media/itsmc-overview/azure-add-new-resource.png)

2. Zoek naar **IT Service Management-connector** in azure Marketplace. Selecteer **maken**:

   ![Scherm opname waarin de knop maken wordt weer gegeven in azure Marketplace.](media/itsmc-overview/add-itsmc-solution.png)

3. Selecteer in de sectie **OMS-werk ruimte** de Azure log Analytics-werk ruimte waar u ITSMC wilt installeren.
   >[!NOTE]
   > * Als onderdeel van de doorlopende overgang van Microsoft Operations Management Suite (OMS) naar Azure Monitor worden OMS-werk ruimten nu aangeduid als *log Analytics*.
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

- [System Center Service Manager](./itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](./itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](./itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](./itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Nadat u uw ITSM-hulpprogram ma's hebt bereid, voert u de volgende stappen uit om een verbinding te maken:

1. Zoek in **alle resources** naar **Service Desk (*de naam van uw werk ruimte*)**:

   ![Scherm opname van recente resources in het Azure Portal.](media/itsmc-overview/itsm-connections.png)

1. Selecteer onder **gegevens bronnen voor werk ruimte** in het linkerdeel venster **ITSM-verbindingen**:

   ![Scherm opname van het menu-item ITSM-verbindingen.](media/itsmc-overview/add-new-itsm-connection.png)
   Op deze pagina wordt de lijst met verbindingen weer gegeven.
1. Selecteer **verbinding toevoegen**.

4. Geef de verbindings instellingen op, zoals beschreven in [de ITSMC-verbinding configureren met uw ITSM-producten of-services](./itsmc-connections.md).

   > [!NOTE]
   >
   > De configuratie gegevens van de verbinding worden standaard elke 24 uur vernieuwd door ITSMC. Als u de gegevens van uw verbinding onmiddellijk wilt vernieuwen om te zien welke wijzigingen of sjabloon updates u aanbrengt, selecteert u de knop **synchroniseren** op de Blade van de verbinding:
   >
   > ![Scherm afbeelding met de knop synchroniseren op de Blade verbinding.](media/itsmc-overview/itsmc-connections-refresh.png)


## <a name="use-itsmc"></a>ITSMC gebruiken
   U kunt ITSMC gebruiken om werk items te maken op basis van Azure-waarschuwingen, Log Analytics waarschuwingen en Log Analytics logboek records.

## <a name="template-definitions"></a>Sjabloon definities
   Er zijn typen werk items die sjablonen kunnen gebruiken die zijn gedefinieerd door het ITSM-hulp programma.
Met behulp van sjablonen kunt u velden definiëren die automatisch worden ingevuld op basis van vaste waarden die zijn gedefinieerd als onderdeel van de actie groep. U definieert sjablonen in het hulp programma ITSM. U kunt definiëren in welke sjabloon u wilt gebruiken als onderdeel van de definitie van de actie groep.
      
## <a name="create-itsm-work-items-from-azure-alerts"></a>ITSM-werk items maken op basis van Azure-waarschuwingen

Nadat u uw ITSM-verbinding hebt gemaakt, kunt u in uw ITSM-hulp programma werk items maken op basis van Azure-waarschuwingen. Als u de werk items wilt maken, gebruikt u de actie ITSM in actie groepen.

Actie groepen bieden een modulaire en herbruikbare manier om acties voor uw Azure-waarschuwingen te activeren. U kunt actie groepen met metrische waarschuwingen, waarschuwingen voor activiteiten logboeken en waarschuwingen voor Azure Log Analytics gebruiken in de Azure Portal.

> [!NOTE]
> Nadat u de ITSM-verbinding hebt gemaakt, moet u 30 minuten wachten totdat het synchronisatie proces is voltooid.
> 

Gebruik de volgende procedure om werk items te maken:

1. Selecteer in de Azure Portal  **waarschuwingen**.
2. Selecteer in het menu boven aan het scherm **acties beheren**:

    ![Scherm afbeelding met de menu opdracht acties beheren.](media/itsmc-overview/action-groups-selection-big.png)

   Het venster **actie groep maken** wordt weer gegeven.

3. Selecteer het **abonnement** en de **resource groep** waar u uw actie groep wilt maken. Geef een naam op voor de **actie groep** en de **weergave naam** voor uw actie groep. Selecteer **volgende: meldingen**.

    ![Scherm opname van het venster actie groep maken.](media/itsmc-overview/action-groups-details.png)

4. Selecteer in de lijst met meldingen **volgende: acties**.
5. Selecteer in de lijst acties de optie **ITSM** in de lijst **actie type** . Geef een **naam** op voor de actie. Selecteer de knop pen die de **Details** van de bewerking weergeeft.
6. Selecteer in de lijst **abonnement** het abonnement waarin uw log Analytics-werk ruimte zich bevindt. Selecteer in de lijst **verbinding** de naam van uw ITSM-connector. Deze wordt gevolgd door de naam van uw werk ruimte. Bijvoorbeeld MyITSMConnector (MyWorkspace).

7. Selecteer een type **werk item** .

8. Als u velden met vaste waarden wilt invullen, selecteert u **aangepaste sjabloon gebruiken**. Als dat niet het geval is, kiest u een bestaande [sjabloon](#template-definitions) in de lijst **sjabloon** en voert u de vaste waarden in de sjabloon velden in.

9. Als u **afzonderlijke werk items maken voor elk configuratie-item** selecteert, heeft elk configuratie-item een eigen werk item. Er wordt één werk item per configuratie-item weer. Het wordt bijgewerkt op basis van de waarschuwingen die worden gemaakt.

   * In het geval dat u in de vervolg keuzelijst werk item of waarschuwing klikt: als u het selectie vakje **afzonderlijke werk items voor elk configuratie-item maken** uitschakelt, wordt er door elke waarschuwing een nieuw werk item gemaakt. Er kan meer dan één waarschuwing per configuratie-item zijn.

   ![Scherm opname van het ITSM-ticket venster.](media/itsmc-overview/itsm-action-configuration.png)
   
   * In het geval dat u in de vervolg keuzelijst voor werk items selecteert: als u **afzonderlijke werk items maken voor elke logboek vermelding** in de keuze rondjes selecteert, wordt er met elke waarschuwing een nieuw werk item gemaakt. Als u **afzonderlijke werk items maken selecteert voor elk configuratie-item** in de keuze rondjes selectie, heeft elk configuratie-item een eigen werk item.
   ![Scherm opname van het ITSM-ticket venster.](media/itsmc-overview/itsm-action-configuration-event.png)

10. Selecteer **OK**.

Wanneer u een Azure-waarschuwings regel maakt of bewerkt, gebruikt u een actie groep met een ITSM-actie. Wanneer de waarschuwing wordt geactiveerd, wordt het werk item gemaakt of bijgewerkt in het ITSM-hulp programma.

> [!NOTE]
>
>- Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/monitor/) voor actie groepen voor meer informatie over de prijzen van de actie ITSM.
>
>
>- Het veld korte beschrijving in de definitie van de waarschuwings regel is beperkt tot 40 tekens wanneer u het verzendt met behulp van de actie ITSM.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Het incident en de gegevens van de wijzigings aanvraag visualiseren en analyseren

Afhankelijk van uw configuratie wanneer u een verbinding instelt, kan ITSMC tot 120 dagen aan incidenten en wijzigings aanvraag gegevens worden gesynchroniseerd. Het logboek record schema voor deze gegevens vindt u in de [volgende sectie](#additional-information) van dit artikel.

U kunt het incident visualiseren en gegevens wijzigen met behulp van het ITSMC-dash board:

![Scherm opname van het ITSMC-dash board.](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Het dash board bevat ook informatie over de status van de connector, die u als uitgangs punt kunt gebruiken om problemen met de verbindingen te analyseren.

U kunt ook visualiseren van de incidenten die zijn gesynchroniseerd met de betrokken computers in Servicetoewijzing.

Servicetoewijzing detecteert automatisch de toepassings onderdelen op Windows-en Linux-systemen en wijst de communicatie tussen services toe. Zo kunt u uw servers bekijken zoals u dat wilt: als onderling verbonden systemen die essentiële services leveren. Servicetoewijzing toont verbindingen tussen servers, processen en poorten via elke met TCP verbonden architectuur. Met uitzonde ring van de installatie van een agent is geen configuratie vereist. Zie [servicetoewijzing gebruiken](../insights/service-map.md)voor meer informatie.

Als u Servicetoewijzing gebruikt, kunt u de Service Desk-items weer geven die zijn gemaakt in ITSM-oplossingen, zoals hier wordt weer gegeven:

![Scherm opname van de Log Analytics scherm.](media/itsmc-overview/itsmc-overview-integrated-solutions.png)


## <a name="additional-information"></a>Aanvullende informatie

### <a name="data-synced-from-your-itsm-product"></a>Gegevens die zijn gesynchroniseerd met uw ITSM-product
Incidenten en wijzigings aanvragen worden gesynchroniseerd vanuit uw ITSM-product naar uw Log Analytics-werk ruimte, op basis van de configuratie van de verbinding.

In deze sectie vindt u enkele voor beelden van gegevens die zijn verzameld door ITSMC.

De velden in **ServiceDesk_CL** variëren, afhankelijk van het type werk item dat u in log Analytics importeert. Hier volgt een lijst met velden voor twee typen werk items:

**Werk item:** **incidenten**  
ServiceDeskWorkItemType_s = "incident"

**Fields**

- ServiceDeskConnectionName
- Service Desk-ID
- Staat
- Urgentie
- Impact
- Prioriteit
- Escalatie
- Created By
- Opgelost door
- Gesloten door
- Bron
- Toegewezen aan
- Categorie
- Titel
- Beschrijving
- Gemaakt op
- Datum gesloten
- Datum opgelost
- Datum laatst gewijzigd
- Computer


**Werk item:** **wijzigings aanvragen**

ServiceDeskWorkItemType_s = "ChangeRequest"

**Fields**
- ServiceDeskConnectionName
- Service Desk-ID
- Created By
- Gesloten door
- Bron
- Toegewezen aan
- Titel
- Type
- Categorie
- Staat
- Escalatie
- Conflict status
- Urgentie
- Prioriteit
- Risico
- Impact
- Toegewezen aan
- Gemaakt op
- Datum gesloten
- Datum laatst gewijzigd
- Aangevraagde datum
- Geplande begin datum
- Geplande eind datum
- Begin datum van werk
- Eind datum van werk
- Description
- Computer

## <a name="output-data-for-a-servicenow-incident"></a>Uitvoer gegevens voor een ServiceNow-incident

| Log Analytics veld | Het veld ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Getal |
| IncidentState_s | Staat |
| Urgency_s |Urgentie |
| Impact_s |Impact|
| Priority_s | Prioriteit |
| CreatedBy_s | Geopend door |
| ResolvedBy_s | Opgelost door|
| ClosedBy_s  | Gesloten door |
| Source_s| Type contact |
| AssignedTo_s | Toegewezen aan  |
| Category_s | Categorie |
| Title_s|  Korte beschrijving |
| Description_s|  Notities |
| CreatedDate_t|  Had |
| ClosedDate_t| gesloten|
| ResolvedDate_t|Opgelost|
| Computer  | Configuratie-item |

## <a name="output-data-for-a-servicenow-change-request"></a>Uitvoer gegevens voor een wijzigings aanvraag voor een ServiceNow

| Log Analytics | Het veld ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Getal |
| CreatedBy_s | Aangevraagd door |
| ClosedBy_s | Gesloten door |
| AssignedTo_s | Toegewezen aan  |
| Title_s|  Korte beschrijving |
| Type_s|  Type |
| Category_s|  Categorie |
| CRState_s|  Staat|
| Urgency_s|  Urgentie |
| Priority_s| Prioriteit|
| Risk_s| Risico|
| Impact_s| Impact|
| RequestedDate_t  | Aangevraagd door datum |
| ClosedDate_t | Gesloten datum |
| PlannedStartDate_t  |     Geplande begin datum |
| PlannedEndDate_t  |   Geplande eind datum |
| WorkStartDate_t  | Werkelijke begin datum |
| WorkEndDate_t | Werkelijke eind datum|
| Description_s | Description |
| Computer  | Configuratie-item |


## <a name="troubleshoot-itsm-connections"></a>Problemen met ITSM-verbindingen oplossen
- Voer de volgende stappen uit als er een **fout optreedt bij het opslaan** van een verbindings bericht in de gebruikers interface van de verbonden Bron:
   - Voor ServiceNow-, Cher well-en Provance-verbindingen:  
     - Zorg ervoor dat u de gebruikers naam, het wacht woord, de client-ID en het client geheim juist hebt ingevoerd voor elk van de verbindingen.  
     - Zorg ervoor dat u voldoende bevoegdheden hebt in het bijbehorende ITSM-product om de verbinding tot stand te brengen.  
   - Voor Service Manager verbindingen:  
     - Zorg ervoor dat de web-app is geïmplementeerd en dat de hybride verbinding is gemaakt. Als u wilt controleren of de verbinding tot stand is gebracht met de on-premises Service Manager computer, gaat u naar de URL van de web-app zoals beschreven in de documentatie voor het maken van de [hybride verbinding](./itsmc-connections.md#configure-the-hybrid-connection).  

- Als gegevens van ServiceNow niet worden gesynchroniseerd met Log Analytics, moet u ervoor zorgen dat het ServiceNow-exemplaar niet in de slaap stand staat. ServiceNow dev-instanties gaan soms naar de slaap stand wanneer ze gedurende een lange periode niet actief zijn. Als dat niet het geval is, meldt u het probleem.
- Als Log Analytics waarschuwingen geactiveerd, maar er worden geen werk items gemaakt in het ITSM-product, als configuratie-items niet zijn gemaakt/gekoppeld aan werk items, of als u andere informatie wilt, raadpleegt u deze bronnen:
   -  ITSMC: de oplossing toont een samen vatting van verbindingen, werk items, computers en meer. Selecteer de tegel met het label **status** van de connector. Hiermee gaat u zoeken naar **Logboeken** met de relevante query. Bekijk logboek records met een `LogType_S` van `ERROR` voor meer informatie.
   - **Zoek pagina voor logboeken** : de fouten en gerelateerde informatie rechtstreeks weer geven met behulp van de query `*ServiceDeskLog_CL*` .

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Problemen met de implementatie van Service Manager web-app oplossen
-   Als u problemen ondervindt met het implementeren van web-apps, moet u ervoor zorgen dat u gemachtigd bent om resources in het abonnement te maken of te implementeren.
-   Als u een **object verwijzing krijgt die niet is ingesteld op een object** fout wanneer u het [script](itsmc-service-manager-script.md)uitvoert, moet u ervoor zorgen dat u geldige waarden hebt opgegeven in de sectie **gebruikers configuratie** .
-   Als u de service bus relay-naam ruimte niet hebt gemaakt, moet u ervoor zorgen dat de vereiste resource provider is geregistreerd in het abonnement. Als het niet is geregistreerd, maakt u de service bus relay-naam ruimte hand matig via de Azure Portal. U kunt deze ook maken wanneer u [de hybride verbinding maakt](./itsmc-connections.md#configure-the-hybrid-connection) in de Azure Portal.


## <a name="contact-us"></a>Contact opnemen

Neem contact met ons op als u query's of feedback over de IT Service Management-connector hebt [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com) .

## <a name="next-steps"></a>Volgende stappen
[ITSM-producten/-services toevoegen aan IT Service Management-connector](./itsmc-connections.md)