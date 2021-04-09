---
title: 'Quickstart: Aan de slag met Azure Sentinel'
description: Gebruik deze quickstart om te leren u hoe u Azure Sentinel kunt gebruiken om snel te zien wat er in uw omgeving gebeurt.
services: sentinel
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.custom: mvc, fasttrack-edit
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: 384c6df86b4aadb117bb31b4604fa4c2ffcda09a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570583"
---
# <a name="quickstart-get-started-with-azure-sentinel"></a>Quickstart: Aan de slag met Azure Sentinel




In deze quickstart leert u hoe u Azure Sentinel kunt gebruiken om snel te zien wat er in uw omgeving gebeurt. Nadat u uw gegevensbronnen hebt verbonden met Azure Sentinel, krijgt u direct toegang tot een visualisatie en analyse van de gegevens, zodat u kunt weten wat er gebeurt in al uw verbonden gegevensbronnen. Met Azure Sentinel beschikt u over werkmappen die de volledige kracht van Azure-hulpprogramma's combineren met ingebouwde tabellen en grafieken om u te voorzien van analyses voor uw logboeken en query's. U kunt de ingebouwde werkmappen gebruiken of eenvoudig een nieuwe werkmap maken, helemaal zelf of op basis van een bestaande werkmappen. 

## <a name="get-visualization"></a>Visualisatie van gegevens

Om te visualiseren en analyseren wat er gebeurt in uw omgeving, is het goed om eerst het overzichtsdashboard te bestuderen. U kunt zich zo een duidelijk beeld vormen van de beveiligingspostuur van uw organisatie. U kunt op elk element van deze tegels klikken om in te zoomen op de onbewerkte gegevens die eraan ten grondslag liggen. Om de ruis te verminderen en het aantal waarschuwingen te beperken dat u moet beoordelen en onderzoeken, gebruikt Azure Sentinel een samenvoegingstechniek om waarschuwingen te correleren in incidenten. **Incidenten** zijn groepen gerelateerde waarschuwingen die samen een incident vormen dat kan worden onderzocht en opgelost en waarvoor een actie kan worden uitgevoerd.

- Selecteer in de Azure-portal de service Azure Sentinel en selecteer vervolgens de werkruimte die u wilt controleren.

  ![Overzichtsdashboard van Azure Sentinel](./media/qs-get-visibility/overview.png)

- De werkbalk aan de bovenzijde van het dashboard geeft aan hoeveel gebeurtenissen er zijn opgetreden tijdens de geselecteerde periode en vergelijkt deze met de voorgaande 24 uur. De werkbalk bevat verder informatie over de waarschuwingen die zijn geactiveerd op basis van deze gebeurtenissen (het kleine getal vertegenwoordigt een wijziging in de afgelopen 24 uur). Vervolgens wordt er voor het totale aantal gebeurtenissen aangegeven hoeveel er openstaan, in behandeling zijn en zijn gesloten. Kijk of het aantal gebeurtenissen niet aanzienlijk is gestegen of gedaald. Als er sprake is van een daling, kan het zijn dat een verbinding geen meldingen meer verstuurt naar Azure Sentinel. In het geval van een stijging, is er mogelijk iets dat onderzocht moet worden. Controleer of er nieuwe waarschuwingen zijn.

   ![Azure Sentinel-tellers](./media/qs-get-visibility/funnel.png)

De hoofdtekst van de overzichtspagina geeft in een oogopslag inzicht in de beveiligingsstatus van uw werkruimte:

- **Gebeurtenissen en waarschuwingen in de loop van de tijd**: Hier wordt het aantal gebeurtenissen weergegeven en hoeveel waarschuwingen er zijn gemaakt op basis van deze gebeurtenissen. Als er sprake is van een ongebruikelijke piek, ziet u daar waarschuwingen voor. Als er iets ongebruikelijk is met als gevolg een piek en u geen waarschuwingen ziet, kan dit reden voor ongerustheid zijn.

- **Gebeurtenissen die schadelijk kunnen zijn**: Wanneer er verkeer wordt gedetecteerd van bronnen waarvan bekend is dat ze schadelijk zijn, waarschuwt Azure Sentinel u hiervoor op de kaart. Als u oranje cirkels ziet, gaat het om inkomend verkeer: iemand probeert toegang te krijgen tot uw organisatie vanaf een bekend schadelijk IP-adres. Als de activiteit uitgaand is (rood), betekent dit dat er gegevens van uw netwerk worden gestreamd vanuit uw organisatie naar een bekend schadelijk IP-adres.

   ![Kaart van schadelijk verkeer](./media/qs-get-visibility/map.png)

- **Recente incidenten**: Om uw recente incidenten te bekijken, evenals hun ernst en het aantal waarschuwingen dat is gekoppeld aan het incident. Als u een onverwachte piek ziet voor een bepaald type waarschuwing, kan dit betekenen dat er een aanval wordt uitgevoerd. Als u bijvoorbeeld een plotselinge piek ziet van 20 Pass-the-hash-gebeurtenissen vanuit Microsoft Defender for Identity (voorheen Azure ATP), is het mogelijk dat iemand op dit moment een aanval uitvoert.

- **Gegevensbronanomalieën**: De gegevensanalisten van Microsoft hebben modellen gemaakt die de gegevens uit uw gegevensbronnen voortdurend doorzoeken op afwijkingen. Als er geen afwijkingen zijn, wordt er niets weergegeven. Als er afwijkingen worden gedetecteerd, moet u deze in detail bekijken om te zien wat er is gebeurd. Klik bijvoorbeeld op de piek in Azure-activiteiten. Klik op **Grafiek** om te zien wanneer de piek zich heeft voorgedaan en filter vervolgens op activiteiten die zijn opgetreden in die periode om te zien wat de oorzaak van de piek is.

   ![Afwijkende gegevensbronnen](./media/qs-get-visibility/anomolies.png)

## <a name="use-built-in-workbooks"></a>Ingebouwde werkmappen gebruiken<a name="dashboards"></a>

Ingebouwde werkmappen bieden geïntegreerde gegevens uit uw verbonden gegevensbronnen om u de kans te geven in te zoomen op de gebeurtenissen die in deze services zijn gegenereerd. De ingebouwde werkmappen hebben betrekking op Azure AD, gebeurtenissen voor Azure-activiteiten en on-premises, wat gegevens kunnen zijn van Windows-gebeurtenissen van servers, uit Microsoft-bronnen, uit bronnen van derden, waaronder logboeken van firewallverkeer, Office 365 en onveilige protocollen op basis van Windows-gebeurtenissen. De werkmappen zijn gebaseerd op Azure Monitor-werkmappen, zodat u bij het ontwerpen van uw eigen werkmap de beschikking hebt over verbeterde mogelijkheden voor aanpassing en flexibiliteit. Zie [Werkmappen](../azure-monitor/visualize/workbooks-overview.md) voor meer informatie.

1. Selecteer onder **Instellingen** de optie **Werkmappen**. Onder **Geïnstalleerd** worden alle geïnstalleerde werkmappen weergegeven. Onder **Alle** ziet u de complete galerie met ingebouwde werkmappen die beschikbaar zijn voor installatie. 
2. Zoek naar een specifieke werkmap om de hele lijst te zien met beschrijvingen van de mappen. 
3. Als we ervan uitgaan dat u Azure AD gebruikt, raden we u aan om ten minste de volgende werkmappen te installeren om aan de slag te gaan met Azure Sentinel:
   - **Azure AD**: Gebruik een van de volgende werkmappen, of beide:
       - **Azure AD-aanmeldingen**: hierin worden aanmeldingen in de loop van de tijd geanalyseerd om te zien of er afwijkingen zijn. Deze werkmap vermeldt mislukte aanmeldingen op toepassing, apparaat en locatie, zodat u in een oogopslag kunt zien of er iets vreemds aan de hand is. Let met name op meerdere mislukte aanmeldingen. 
       - **Azure AD-auditlogboeken**: hierin worden beheeractiviteiten geanalyseerd, zoals wijzigingen van gebruikers (toevoegen, verwijderen, enz.), het maken van groepen en aanpassingen.  

   - Voeg een werkmap toe voor uw firewall, zoals de werkmap Palo Alto. De werkmap analyseert het verkeer op uw firewall en biedt correlaties tussen uw firewallgegevens en bedreigingsgebeurtenissen, en accentueert verdachte gebeurtenissen binnen entiteiten. Werkmappen bieden informatie over trends in uw verkeer en u kunt inzoomen op resultaten en deze filteren. 

      ![Palo Alto-dashboard](./media/qs-get-visibility/palo-alto-week-query.png)


U kunt de werkmappen aanpassen door de hoofdquery te bewerken ![knop query bewerken](./media/qs-get-visibility/edit-query-button.png). Klik op de knop ![Log Analytics](./media/qs-get-visibility/go-to-la-button.png) om naar [Log Analytics te gaan om de query daar te bewerken](../azure-monitor/logs/log-analytics-tutorial.md). Selecteer het beletselteken (...) en vervolgens **Tegelgegevens aanpassen** om het hoofdtijdfilter te bewerken of de specifieke tegels uit de werkmap te verwijderen.

Ga voor meer informatie over het werken met query's naar [Tutorial: Visual data in Log Analytics](../azure-monitor/visualize/tutorial-logs-dashboards.md) (Zelfstudie: Gegevens visualiseren in Log Analytics).

### <a name="add-a-new-tile"></a>Een nieuwe tegel toevoegen

Als u een nieuwe tegel wilt toevoegen, kunt u deze toevoegen aan een bestaande werkmap. Dit kan een werkmap zijn die u zelf maakt of een ingebouwde werkmap van Azure Sentinel. 
1. Maak in Log Analytics een tegel met behulp van de instructies in [Tutorial: Visual data in Log Analytics](../azure-monitor/visualize/tutorial-logs-dashboards.md) (Zelfstudie: Gegevens visualiseren in Log Analytics). 
2. Nadat de tegel is gemaakt, selecteert u onder **Vastmaken** de werkmap waarin u de tegel wilt weergeven.

## <a name="create-new-workbooks"></a>Nieuwe werkmappen maken
U kunt een volledig nieuwe werkmap maken of een ingebouwde werkmap gebruiken als basis voor de nieuwe werkmap.

1. Als u een volledig nieuwe werkmap wilt maken, selecteert u **Werkmappen** en vervolgens **+ Nieuwe werkmap**.
2. Selecteer het abonnement waarin u de werkmap wilt maken en geef de map een beschrijvende naam. Elke werkmap is net als alle andere elementen een Azure-resource, en u kunt er rollen (Azure RBAC) aan toewijzen om te bepalen wie toegang heeft. 
3. Als u wilt dat de werkmap kan worden gekozen om er visualisaties aan vast te maken, moet u de map delen. Klik hiervoor op **Delen** en vervolgens op **Gebruikers beheren**. 
 
1. Gebruik de opties **Toegang controleren** en **Roltoewijzingen** zoals u dat zou doen voor andere Azure-resources. Zie [Azure-werkmappen delen met behulp van Azure RBAC](../azure-portal/azure-portal-dashboard-share-access.md) voor meer informatie.


## <a name="new-workbook-examples"></a>Voorbeelden van nieuwe werkmappen

Met de volgende voorbeeldquery kunt u trends vergelijken van verkeer voor verschillende weken. U kunt eenvoudig aanpassen voor welke apparaatleverancier en gegevensbron u de query wilt uitvoeren. In dit voorbeeld wordt SecurityEvent van Windows gebruikt. U kunt de query aanpassen voor uitvoering op AzureActivity of CommonSecurityLog op elke andere firewall.

```console
// week over week query
SecurityEvent
| where TimeGenerated > ago(14d)
| summarize count() by bin(TimeGenerated, 1d)
| extend Week = iff(TimeGenerated>ago(7d), "This Week", "Last Week"), TimeGenerated = iff(TimeGenerated>ago(7d), TimeGenerated, TimeGenerated + 7d)
```

Mogelijk wilt u een query maken die gegevens uit meerdere bronnen combineert. U kunt een query maken om te controleren of Azure Active Directory-auditlogboeken informatie bevatten over nieuwe gebruikers die zojuist zijn gemaakt. Daarna kunt u de logboeken van Azure controleren om na te gaan of deze gebruikers binnen 24 uur nadat ze zijn toegevoegd, hun roltoewijzing hebben gewijzigd. Deze verdachte activiteit zou in dit dashboard worden weergegeven:

```console
AuditLogs
| where OperationName == "Add user"
| project AddedTime = TimeGenerated, user = tostring(TargetResources[0].userPrincipalName)
| join (AzureActivity
| where OperationName == "Create role assignment"
| project OperationName, RoleAssignmentTime = TimeGenerated, user = Caller) on user
| project-away user1
```

U kunt verschillende werkmappen maken op basis van de rol van de persoon die de gegevens bekijkt en waarnaar ze op zoek zijn. U kunt bijvoorbeeld een werkmap maken voor uw netwerkbeheerder die de firewallgegevens bevat. U kunt ook werkmappen maken op basis van hoe vaak u deze wilt bekijken, of u nu bepaalde zaken dagelijks wilt controleren en andere items eenmaal per uur. Het laatste is bijvoorbeeld zinvol om adequaat te kunnen reageren bij verdachte Azure AD-aanmeldingen. 

## <a name="create-new-detections"></a>Nieuwe detecties maken

Genereer detecties voor de [gegevensbronnen die u hebt verbonden met Azure Sentinel](connect-data-sources.md) om bedreigingen in uw organisatie te onderzoeken.

Wanneer u een nieuwe detectie maakt, kunt u gebruikmaken van de ingebouwde detecties die zijn gemaakt door beveiligingsonderzoekers van Microsoft en die zijn afgestemd op de gegevensbronnen waarmee u bent verbonden.

Als u alle vooraf gedefinieerde detecties wilt weergeven, gaat u naar **Analyse** en vervolgens naar **Regelsjablonen**. Dit tabblad bevat alle ingebouwde regels van Azure Sentinel.

   ![Ingebouwde detecties gebruiken om bedreigingen te vinden met Azure Sentinel](media/tutorial-detect-built-in/view-oob-detections.png)

Meer informatie over vooraf gedefinieerde detecties vindt u in [Tutorial: Detect threats out-of-the-box](tutorial-detect-threats-built-in.md) (Zelfstudie: Kant-en-klare detectie van bedreigingen).
 
## <a name="next-steps"></a>Volgende stappen
In deze quickstart hebt u geleerd hoe u aan de slag gaat met Azure Sentinel. Ga verder met de zelfstudie voor het [detecteren van bedreigingen](tutorial-detect-threats-built-in.md).
> [!div class="nextstepaction"]
> [Definieer aangepaste regels voor de detectie van bedreigingen](tutorial-detect-threats-custom.md) om uw reacties op bedreigingen te automatiseren.
