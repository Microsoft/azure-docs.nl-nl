---
title: 'Quickstart: gegevens verzamelen van een hybride Linux-computer met Azure Monitor'
description: In deze snelstart vindt u informatie over het implementeren van de Log Analytics-agent voor Linux-computers die worden uitgevoerd buiten Azure en het inschakelen van gegevensverzameling met Azure Monitor-logboeken.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 12/24/2019
ms.author: bwren
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: ef6e31968f85407e8055315becdc1337b474ad70
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100628659"
---
# <a name="quickstart-collect-data-from-a-linux-computer-in-a-hybrid-environment-with-azure-monitor"></a>Quickstart: Gegevens verzamelen van een Linux-computer in een hybride omgeving met Azure Monitor

[Azure Monitor](../overview.md) kan gegevens rechtstreeks vanuit uw fysieke of virtuele Linux-computers in uw omgeving verzamelen en onderbrengen in een Log Analytics-werkruimte voor uitvoerige analyse en correlatie. Door de [Log Analytics-agent te installeren](../agents/log-analytics-agent.md) kan Azure Monitor gegevens verzamelen uit een datacentrum of een andere cloudomgeving. In deze snelstart wordt beschreven hoe u met een paar eenvoudige stappen gegevens van uw Linux-server configureert en verzamelt. Zie [Gegevens verzamelen over Azure Virtual Machines](../learn/quick-collect-azurevm.md) voor informatie over virtuele Linux-machines in Azure.  

Zie [Ondersteunde besturingssystemen](../agents/agents-overview.md#supported-operating-systems) en [Netwerkconfiguratie voor de firewall](../agents/log-analytics-agent.md#network-requirements) voor meer informatie over de ondersteunde configuratie.
 
Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Een werkruimte maken

1. Selecteer in de Azure-portal de optie **Alle services**. Typ in de lijst met resources **Log Analytics**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Log Analytics-werkruimten**.

    ![Zoeken naar Log Analytics-werkruimten in de Azure-portal](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. Selecteer **Maken** en geef uw keuze aan voor de volgende items:

   * Geef een naam op voor de nieuwe **Log Analytics-werkruimte**, bijvoorbeeld *StandaardLAWerkruimte*.  
   * Selecteer een **abonnement** om te koppelen door een selectie in de vervolgkeuzelijst te maken als de geselecteerde standaardwaarde niet juist is.
   * Selecteer bij **Resourcegroep** een bestaande resourcegroep die een of meer virtuele Azure-machines bevat.  
   * Selecteer de **Locatie** waarop uw virtuele machines zijn geïmplementeerd.  Bekijk [in welke regio's Log Analytics beschikbaar is](https://azure.microsoft.com/regions/services/) voor aanvullende informatie.
   * Als u een werkruimte maakt in een nieuw abonnement dat na 2 april 2018 is gemaakt, gebruikt deze automatisch de prijscategorie *Per GB*. De optie voor het selecteren van een prijscategorie is dan niet beschikbaar.  Als u een werkruimte maakt voor een bestaand abonnement dat is gemaakt vóór 2 april, of voor een abonnement dat is gekoppeld aan een bestaande EA-inschrijving, kunt u hier uw gewenste prijscategorie selecteren.  Zie [Prijzen voor Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) voor aanvullende informatie over de afzonderlijke lagen.
  
        ![Een Log Analytics-werkruimte maken in de Azure-portal](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. Nadat u de vereiste gegevens hebt opgegeven in het deelvenster **Log Analytics-werkruimte** selecteert u **OK**.  

Terwijl de gegevens worden geverifieerd en de werkruimte wordt gemaakt, kunt u de voortgang bijhouden onder **Meldingen** in het menu. 

## <a name="obtain-workspace-id-and-key"></a>Werkruimte-ID en -sleutel ophalen

Voordat u de Log Analytics-agent voor Linux installeert, hebt u eerst de werkruimte-id en -sleutel voor de Log Analytics-werkruimte nodig. Het script voor de agentwrapper heeft deze informatie nodig om de agent correct te configureren en ervoor te zorgen dat deze kan communiceren met Azure Monitor.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. Selecteer **Alle services** in de linkerbovenhoek van de Azure-portal. Voer **Log Analytics** in het zoekvak in. Terwijl u typt, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Log Analytics-werkruimten**.

2. Selecteer in de lijst met Log Analytics-werkruimten de werkruimte die u eerder hebt gemaakt. (Mogelijk hebt u deze de naam **DefaultLAWorkspace** gegeven.)

3. Selecteer **Agents beheren**:
 
4. Selecteer vervolgens **Linux-servers**.

5. De waarde rechts van **Werkruimte-id** en **Primaire sleutel**. Kopieer en plak beide in uw favoriete editor.

## <a name="install-the-agent-for-linux"></a>De agent voor Linux installeren

Met de volgende stappen wordt de installatie van de agent voor Log Analytics in Azure en de Azure Government-cloud geconfigureerd.  

>[!NOTE]
>De Log Analytics-agent voor Linux kan niet worden geconfigureerd om aan meer dan één Log Analytics-werkruimte te rapporteren.  

Als uw Linux-computer via een proxyserver moet communiceren met Log Analytics, kan de proxyconfiguratie op de opdrachtregel worden opgegeven door `-p [protocol://][user:password@]proxyhost[:port]` op te nemen.  De eigenschap *proxyhost* accepteert een volledig gekwalificeerde domeinnaam of het IP-adres van de proxyserver. 

Bijvoorbeeld: `https://user01:password@proxy01.contoso.com:30443`

1. Als u de Linux-computer wilt configureren om verbinding te maken met een Log Analytics-werkruimte, voert u de volgende opdracht uit om de eerder gekopieerde werkruimte-id en primaire sleutel op te geven. Met de volgende opdracht wordt de agent gedownload, de bijbehorende controlesom gevalideerd en de agent geïnstalleerd. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    De volgende opdracht bevat de proxyparameter `-p` en een voorbeeld van de syntaxis wanneer verificatie vereist is voor de proxyserver:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Als u de Linux-computer wilt configureren om verbinding te maken met een Log Analytics-werkruimte in de Azure Government-cloud, voert u de volgende opdracht uit met de eerder gekopieerde werkruimte-id en primaire sleutel. Met de volgende opdracht wordt de agent gedownload, de bijbehorende controlesom gevalideerd en de agent geïnstalleerd. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    De volgende opdracht bevat de proxyparameter `-p` en een voorbeeld van de syntaxis wanneer verificatie vereist is voor de proxyserver:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```

3. Start de agent opnieuw door de volgende opdracht uit te voeren: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Gebeurtenis- en prestatiegegevens verzamelen

Azure Monitor kan gebeurtenissen uit de Linux Syslog en prestatiemeteritems verzamelen die u opgeeft voor langetermijnanalyses en -rapportages. Het kan ook actie ondernemen wanneer een bepaalde voorwaarde wordt gedetecteerd. Volg deze stappen om eerst het verzamelen van gebeurtenissen uit de Linux Syslog en diverse algemene prestatiemeters te configureren.  

1. Selecteer in de Azure-portal de optie **Alle services**. Typ Log Analytics in de lijst met resources. Terwijl u typt, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Log Analytics-werkruimten** en selecteer in de lijst met Log Analytics-werkruimten de werkruimte die u zoekt. Selecteer vervolgens **Geavanceerde instellingen** van de **Log Analytics**-werkruimte.

2. Selecteer **Gegevens** en selecteer vervolgens **Syslog**.  

3. U kunt syslog toevoegen door de naam van het logboek te typen. Voer **syslog** in en selecteer het plusteken **+** .  

4. Schakel in de tabel de ernstcategorieën **Info**, **Kennisgeving** en **Fouten opsporen** uit. 

5. Selecteer boven aan de pagina **Opslaan** om de configuratie op te slaan.

6. Selecteer **Linux-prestatiegegevens** om het verzamelen van prestatiemeteritems op een Linux-computer in te schakelen. 

7. Wanneer u Linux-prestatiemeteritems voor een nieuwe Log Analytics-werkruimte voor het eerst configureert, krijgt u de optie om snel verschillende algemene prestatiemeteritems te maken. Ze worden weergegeven met een selectievakje ernaast.

    ![Standaardprestatiemeteritems voor Linux geselecteerd in Azure Monitor](media/quick-collect-azurevm/linux-perfcounters-azure-monitor.png)

    Selecteer **Onderstaande configuratie toepassen op mijn machines** en selecteer vervolgens **De geselecteerde prestatiemeteritems toevoegen**. Ze worden toegevoegd en vooraf ingesteld met een sample-interval van tien seconden.  

8. Selecteer boven aan de pagina **Opslaan** om de configuratie op te slaan.

## <a name="view-data-collected"></a>Verzamelde gegevens weergeven

Nu u gegevensverzameling hebt ingeschakeld, geven we een voorbeeld van een eenvoudige zoekopdracht in logboeken om enkele gegevens afkomstig van de doelcomputer weer te geven.  

1. Selecteer de optie **Logboeken** in de geselecteerde werkruimte in het linkerdeelvenster.

2. Typ op de logboekquerypagina `Perf` in de query-editor en selecteer **Uitvoeren**.
 
    ![Log Analytics-logboeken zoeken](media/quick-collect-linux-computer/log-analytics-portal-queryexample.png)

    Zo heeft de query op de volgende afbeelding 10.000 prestatierecords als resultaat. Uw resultaat zal aanzienlijk kleiner zijn.

    ![Zoekresultaat uit Log Analytics-logboeken](media/quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer deze niet langer nodig is, kunt u de agent verwijderen van de Linux-computer en de Log Analytics-werkruimte verwijderen.  

Voer de volgende opdracht op de Linux-computer uit om de agent te verwijderen. Met het argument *--purge* worden de agent en de configuratie ervan volledig verwijderd.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

U verwijdert de werkruimte door de eerder gemaakte Log Analytics-werkruimte te selecteren en op de resourcepagina **Verwijderen** te selecteren.

![Log Analytics-resource verwijderen](media/quick-collect-linux-computer/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Volgende stappen

Nu u bezig bent met het verzamelen van operationele gegevens en prestatiegegevens van uw on-premises Windows-computer, kunt u beginnen met het verkennen, analyseren en het ondernemen van actie op gegevens die u *gratis* verzamelt.  

Voor informatie over het weergeven en analyseren van de gegevens gaat u verder met de zelfstudie.

> [!div class="nextstepaction"]
> [Gegevens weergeven of analyseren in Log Analytics](../log-query/log-analytics-tutorial.md)