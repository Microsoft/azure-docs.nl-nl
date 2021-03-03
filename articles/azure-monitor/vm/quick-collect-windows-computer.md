---
title: Gegevens verzamelen van een hybride Windows-computer met Azure Monitor
description: In deze snelstart vindt u informatie over het implementeren van de Log Analytics-agent voor Windows-computers die worden uitgevoerd buiten Azure en het inschakelen van gegevensverzameling met Azure Monitor-logboeken.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/22/2019
ms.author: bwren
ms.custom: mvc, seo-javascript-october2019
ms.openlocfilehash: 65d60c77280b569d1bff5e4d3acbdf97ead96270
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733785"
---
# <a name="collect-data-from-a-windows-computer-in-a-hybrid-environment-with-azure-monitor"></a>Gegevens verzamelen van een Windows-computer in een hybride omgeving met Azure Monitor

[Azure Monitor](../overview.md) kan gegevens rechtstreeks vanuit uw fysieke of virtuele Windows-computers in uw omgeving verzamelen en onderbrengen in een Log Analytics-werkruimte voor uitvoerige analyse en correlatie. Door de [Log Analytics-agent te installeren](../agents/log-analytics-agent.md) kan Azure Monitor gegevens verzamelen uit een datacentrum of een andere cloudomgeving. In deze quickstart wordt beschreven hoe u met een paar eenvoudige stappen gegevens van uw Windows-computer configureert en verzamelt. Zie [Gegevens verzamelen over Azure Virtual Machines](./quick-collect-azurevm.md) voor informatie over virtuele Windows-machines in Azure.  

Zie [Ondersteunde besturingssystemen](../agents/agents-overview.md#supported-operating-systems) en [Netwerkconfiguratie voor de firewall](../agents/log-analytics-agent.md#network-requirements) voor meer informatie over de ondersteunde configuratie.
 
Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-azure-portal"></a>Meld u aan bij Azure Portal

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Een werkruimte maken

1. Selecteer in de Azure-portal de optie **Alle services**. Typ in de lijst met resources **Log Analytics**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Log Analytics-werkruimten**.

    ![Azure Portal](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. Selecteer **Maken** en geef uw keuze aan voor de volgende items:

   * Geef een naam op voor de nieuwe **Log Analytics-werkruimte**, bijvoorbeeld *StandaardLAWerkruimte*.  
   * Selecteer een **abonnement** om te koppelen door een selectie in de vervolgkeuzelijst te maken als de geselecteerde standaardwaarde niet juist is.
   * Selecteer bij **Resourcegroep** een bestaande resourcegroep die een of meer virtuele Azure-machines bevat.  
   * Selecteer de **Locatie** waarop uw virtuele machines zijn geïmplementeerd.  Bekijk [in welke regio's Log Analytics beschikbaar is](https://azure.microsoft.com/regions/services/) voor aanvullende informatie.
   * Als u een werkruimte maakt in een nieuw abonnement dat na 2 april 2018 is gemaakt, gebruikt deze automatisch de prijscategorie *Per GB*. De optie voor het selecteren van een prijscategorie is dan niet beschikbaar.  Als u een werkruimte maakt voor een bestaand abonnement dat is gemaakt vóór 2 april, of voor een abonnement dat is gekoppeld aan een bestaande EA-inschrijving, kunt u hier uw gewenste prijscategorie selecteren.  Zie [Prijzen voor Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) voor aanvullende informatie over de afzonderlijke lagen.
  
        ![Log Analytics-resourceblade maken](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. Nadat u de vereiste gegevens hebt opgegeven in het deelvenster **Log Analytics-werkruimte** selecteert u **OK**.  

Terwijl de gegevens worden geverifieerd en de werkruimte wordt gemaakt, kunt u de voortgang bijhouden onder **Meldingen** in het menu. 


## <a name="get-the-workspace-id-and-key"></a>De werkruimte-id en -sleutel ophalen

Voordat u de Log Analytics-werkruimte voor Windows (ook wel de MMA (Microsoft Monitoring Agent) genoemd) installeert, hebt u eerst de werkruimte-id en -sleutel voor uw Log Analytics-werkruimte nodig. De setup-wizard heeft deze informatie nodig om de agent correct te configureren en ervoor te zorgen dat deze kan communiceren met Azure Monitor.  

1. Selecteer **Alle services** in de linkerbovenhoek van de Azure-portal. Voer **Log Analytics** in het zoekvak in. Terwijl u typt, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Log Analytics-werkruimten**.

2. Selecteer in de lijst met Log Analytics-werkruimten de werkruimte die u eerder hebt gemaakt. (Mogelijk hebt u deze de naam **DefaultLAWorkspace** gegeven.)

3. Selecteer **Geavanceerde instellingen**:

    ![Geavanceerde instellingen van Log Analytics](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)
  
4. Selecteer **Verbonden bronnen** en selecteer vervolgens **Windows-servers**.

5. Kopieer de waarden rechts van **Werkruimte-id** en **Primaire sleutel**. Plak deze in uw favoriete editor.

## <a name="install-the-agent-for-windows"></a>De agent voor Windows installeren

Met de volgende stappen wordt de agent voor Log Analytics in Azure en de Azure Government geïnstalleerd en geconfigureerd. U gebruikt het installatieprogramma Microsoft Monitoring Agent Setup om de agent op uw computer te installeren.

1. Nadat u de vorige stappen hebt uitgevoerd, selecteert u op de pagina **Windows-servers** de versie van **Download Windows Agent** die u wilt downloaden. Selecteer de juiste versie voor de processorarchitectuur van uw Windows-besturingssysteem.

2. Voer Setup uit om de agent op de computer te installeren.

3. Op de pagina **Welkom** selecteert u **Volgende**.

4. Lees de licentie op de pagina **Licentievoorwaarden** en selecteer **Akkoord**.

5. Op de pagina **Doelmap** wijzigt u desgewenst de standaardinstallatiemap en selecteert u **Volgende**.

6. Op de pagina **Installatieopties voor Agent** laat u de agent verbinding maken met Azure Log Analytics en selecteert u **Volgende**.

7. Voer op de pagina **Azure Log Analytics** deze stappen uit:

   1. Plak de **Werkruimte-id** en **Werkruimtesleutel (primaire sleutel)** die u eerder hebt gekopieerd. Als u de computer wilt laten rapporteren bij een Log Analytics-werkruimte in Azure Government, selecteert u **Azure US Government** in de lijst **Azure Cloud**.  
   2. Als de computer met de Log Analytics-service moet communiceren via een proxyserver, selecteert u **Geavanceerd** en geeft u de URL en het poortnummer van de proxyserver op. Als er voor uw proxyserver verificatie is vereist, voert u de gebruikersnaam en het wachtwoord in voor verificatie bij de proxyserver en selecteert u **Volgende**.  

8. Selecteer **Volgende** nadat u de configuratie-instellingen hebt toegevoegd:

    ![Microsoft Monitoring Agent Setup](media/quick-collect-windows-computer/log-analytics-mma-setup-laworkspace.png)

9. Controleer op de pagina **Gereed om te installeren** uw keuzes en selecteer **Installeren**.

10. Selecteer op de pagina **Configuratie voltooid** de optie **Voltooien**.

Wanneer de installatie en het instellen zijn voltooid, wordt Microsoft Monitoring Agent weergegeven in het Configuratiescherm. U kunt de configuratie controleren en verifiëren of de agent is verbonden met de Log Analytics-werkruimte. Wanneer de verbinding is gemaakt, geeft de agent op het tabblad **Azure Log Analytics** dit bericht weer: **De Microsoft Monitoring Agent heeft verbinding gemaakt met de service Microsoft Log Analytics.**<br><br> ![MMA-verbindingsstatus](media/quick-collect-windows-computer/log-analytics-mma-laworkspace-status.png)

## <a name="collect-event-and-performance-data"></a>Gebeurtenis- en prestatiegegevens verzamelen

Azure Monitor kan gebeurtenissen uit het Windows-gebeurtenislogboek en prestatiemeteritems verzamelen voor langetermijnanalyses en -rapportages. Het kan ook actie ondernemen wanneer een bepaalde voorwaarde wordt gedetecteerd. Volg deze stappen om eerst het verzamelen van gebeurtenissen uit de Windows-gebeurtenislogboeken en diverse algemene prestatiemeters te configureren.  

1. Selecteer **Meer services** in de linkerbenedenhoek van de Azure-portal. Voer **Log Analytics** in het zoekvak in. Terwijl u typt, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Log Analytics-werkruimten**.

2. Selecteer **Geavanceerde instellingen**:

    ![Geavanceerde instellingen van Log Analytics](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)
 
3. Selecteer **Gegevens** en selecteer vervolgens **Windows-gebeurtenislogboeken**.  

4. U kunt een gebeurtenislogboek toevoegen door de naam van het logboek in te voeren. Voer **System** in en selecteer het plusteken ( **+** ).  

5. Selecteer in de tabel de ernstcategorieën **Fout** en **Waarschuwing**.

6. Selecteer **Opslaan** bovenaan de pagina.

7. Selecteer **Windows-prestatiemeteritems** om het verzamelen van prestatiemeteritems op een Windows-computer in te schakelen.

8. Wanneer u voor het eerst Windows-prestatiemeteritems voor een nieuwe Log Analytics-werkruimte configureert, krijgt u de optie om snel verschillende algemene prestatiemeteritems te maken. Elke optie wordt weergegeven, met een selectievakje ernaast:

    ![Windows-prestatiemeteritems](media/quick-collect-windows-computer/windows-perfcounters-default.png).
    
    Selecteer **De geselecteerde prestatiemeteritems toevoegen**. De tellers worden toegevoegd en vooraf ingesteld met een sample-interval van tien seconden.

9. Selecteer **Opslaan** bovenaan de pagina.

## <a name="view-collected-data"></a>Verzamelde gegevens weergeven

Nu u gegevensverzameling hebt ingeschakeld, voeren we een eenvoudige zoekopdracht uit in de logboeken om enkele gegevens van de doelcomputer weer te geven.  

1. Selecteer de optie **Logboeken** in de geselecteerde werkruimte in het linkerdeelvenster.

2. Typ op de logboekquerypagina `Perf` in de query-editor en selecteer **Uitvoeren**.
 
    ![Log Analytics-logboeken zoeken](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    Zo heeft de query in de deze afbeelding 10.000 prestatierecords geretourneerd. Uw resultaat zal aanzienlijk kleiner zijn.

    ![Zoekresultaat uit Log Analytics-logboeken](media/quick-collect-windows-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de agent en de Log Analytics-werkruimte van uw computer verwijderen als u ze niet meer nodig hebt.  

Voer de volgende stappen uit om de agent te verwijderen:

1. Open het Configuratiescherm.

2. Open **Programma's en onderdelen**.

3. Selecteer in **Programma's en onderdelen** de optie **MMA** en selecteer **Verwijderen**.

Om de Log Analytics-werkruimte te verwijderen die u eerder hebt gemaakt, selecteert u deze en selecteert u vervolgens **Verwijderen** op de resourcepagina.

![Log Analytics-werkruimte verwijderen](media/quick-collect-windows-computer/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Volgende stappen

Nu u bezig bent met het verzamelen van operationele gegevens en prestatiegegevens van uw Windows-computer, kunt u beginnen met het *gratis* verkennen, analyseren en het ondernemen van actie op gegevens die u verzamelt.  

Voor informatie over het weergeven en analyseren van de gegevens gaat u verder met de zelfstudie:

> [!div class="nextstepaction"]
> [Gegevens weergeven of analyseren in Log Analytics](../logs/log-analytics-tutorial.md)