---
title: Azure-Services en-toepassingen bewaken met Grafana
description: Azure Monitor-en Application Insights gegevens routeren zodat u ze kunt weer geven in Grafana.
ms.topic: conceptual
ms.date: 11/06/2017
ms.openlocfilehash: decd0353bcb2cf048fd5591ea8c895cf22944809
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048655"
---
# <a name="monitor-your-azure-services-in-grafana"></a>Uw Azure-Services controleren in Grafana
U kunt nu Azure-Services en-toepassingen bewaken vanuit [Grafana](https://grafana.com/) met behulp van de [invoeg toepassing voor Azure monitor gegevens bron](https://grafana.com/plugins/grafana-azure-monitor-datasource). De invoeg toepassing verzamelt de prestatie gegevens voor toepassingen die worden verzameld door Azure Monitor, met inbegrip van verschillende logboeken en metrieken. U kunt deze gegevens vervolgens weer geven op uw Grafana-dash board.

Gebruik de volgende stappen om een Grafana-server in te stellen en dash boards te maken voor metrische gegevens en logboeken van Azure Monitor.

## <a name="set-up-a-grafana-server"></a>Een Grafana-server instellen

### <a name="set-up-grafana-locally"></a>Grafana lokaal instellen
Als u een lokale Grafana-server wilt instellen, [downloadt en installeert u Grafana in uw lokale omgeving](https://grafana.com/grafana/download). Installeer Grafana versie 5,3 of hoger om de Azure Monitor integratie van de invoeg toepassing te gebruiken.

### <a name="set-up-grafana-on-azure-through-the-azure-marketplace"></a>Grafana op Azure instellen via de Azure Marketplace
1. Ga naar Azure Marketplace en kies Grafana door Grafana Labs.

2. Vul de namen en gegevens in. Een nieuwe resourcegroep maken. Houd de waarden bij die u kiest voor de VM-gebruikers naam, het VM-wacht woord en het wacht woord voor de Grafana-Server beheerder.  

3. Kies de grootte van de virtuele machine en een opslag account.

4. Configureer de netwerk configuratie-instellingen.

5. Bekijk de samen vatting en selecteer **maken** nadat u de gebruiks voorwaarden hebt geaccepteerd.

6. Nadat de implementatie is voltooid, selecteert **u Ga naar resource groep**. U ziet een lijst met nieuw gemaakte resources.

    ![Grafana-resource groeps objecten](media/grafana-plugin/grafana1.png)

    Als u de netwerk beveiligings groep (*grafana-NSG* in dit geval) selecteert, kunt u zien dat poort 3000 wordt gebruikt voor toegang tot grafana-server.

7. Het open bare IP-adres van uw Grafana-server ophalen: Ga terug naar de lijst met resources en selecteer **openbaar IP-adres**.

## <a name="sign-in-to-grafana"></a>Aanmelden bij Grafana

1. Gebruik het IP-adres van uw server om de aanmeldings pagina te openen op *http:// \<IP address\> : 3000* of *\<DNSName> \: 3000* in uw browser. Hoewel 3000 de standaard poort is, is het mogelijk dat u tijdens de installatie een andere poort hebt geselecteerd. Er wordt een aanmeldings pagina weer geven voor de Grafana-server die u hebt gemaakt.

    ![Aanmeldings scherm Grafana](./media/grafana-plugin/grafana-login-screen.png)

2. Meld u aan met de gebruikers naam *beheerder* en het Grafana-Server beheerders wachtwoord dat u eerder hebt gemaakt. Als u een lokale installatie gebruikt, is het standaard wachtwoord *beheerder* en wordt u gevraagd dit in te stellen op uw eerste aanmelding.

## <a name="configure-data-source-plugin"></a>Gegevens bron-invoeg toepassing configureren

Nadat u bent aangemeld, ziet u dat de Azure Monitor-invoeg toepassing voor gegevens bronnen al is opgenomen.

![Grafana bevat Azure Monitor-invoeg toepassing](./media/grafana-plugin/grafana-includes-azure-monitor-plugin-dark.png)

1. Selecteer **gegevens bron toevoegen** om de Azure monitor gegevens bron toe te voegen en te configureren.

2. Kies een naam voor de gegevens bron en selecteer **Azure monitor** als het type in de vervolg keuzelijst.

3. Een service-principal maken-Grafana maakt gebruik van een Azure Active Directory Service-Principal om verbinding te maken met Azure Monitor Api's en gegevens te verzamelen. U moet een bestaande service-principal maken of gebruiken om de toegang tot uw Azure-resources te beheren.
    * Zie [deze instructies](../../active-directory/develop/howto-create-service-principal-portal.md) voor het maken van een service-principal. Kopieer en sla uw Tenant-ID (Directory-id), client-ID (toepassings-ID) en client geheim op (waarde van toepassing Key).
    * Zie [toepassing toewijzen aan rol](../../active-directory/develop/howto-create-service-principal-portal.md) om de rol van lezer toe te wijzen aan de Azure Active Directory toepassing op het abonnement, de resource groep of de resource die u wilt bewaken. 
    De Log Analytics-API vereist de [rol van log Analytics lezer](../../role-based-access-control/built-in-roles.md#log-analytics-reader), die de machtigingen van de rol lezer bevat en er een toevoegt.

4. Geef de verbindings gegevens op voor de Api's die u wilt gebruiken. U kunt verbinding maken met alle of een aantal ervan. 
    * Als u verbinding maakt met zowel de metrische gegevens als de logboeken in Azure Monitor, kunt u dezelfde referenties opnieuw gebruiken door **dezelfde gegevens te selecteren als Azure monitor-API**.
    * Wanneer u de invoeg toepassing configureert, kunt u aangeven welke Azure-Cloud u wilt bewaken met de invoeg toepassing (openbaar, Azure US Government, Azure Duitsland of Azure China).
    * Als u Application Insights gebruikt, kunt u ook uw Application Insights-API en toepassings-ID opnemen voor het verzamelen van Application Insights op basis van metrische gegevens. Zie [uw API-sleutel en toepassings-id ophalen](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID)voor meer informatie.

        > [!NOTE]
        > Sommige gegevens bron velden hebben een andere naam dan de gecorreleerde Azure-instellingen:
        > * Tenant-ID is de Azure-Directory-ID
        > * Client-ID is de Azure Active Directory toepassings-ID
        > * Client geheim is de Azure Active Directory sleutel waarde van de toepassing

5. Als u Application Insights gebruikt, kunt u ook uw Application Insights-API en toepassings-ID opnemen voor het verzamelen van Application Insights op basis van metrische gegevens. Zie [uw API-sleutel en toepassings-id ophalen](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID)voor meer informatie.

6. Selecteer **Opslaan** en Grafana worden de referenties voor elke API getest. Er wordt een bericht weer gegeven dat vergelijkbaar is met het volgende.  
    ![Configuratie van Grafana-gegevens bron goedgekeurd](./media/grafana-plugin/grafana-data-source-config-approved-dark.png)

## <a name="build-a-grafana-dashboard"></a>Een Grafana-dash board bouwen

1. Ga naar de start pagina van Grafana en selecteer **Nieuw dash board**.

2. Selecteer in het nieuwe dash board de **grafiek**. U kunt andere grafiek opties proberen, maar in dit artikel wordt *Graph* als voor beeld gebruikt.

3. Een lege grafiek wordt weer gegeven op het dash board. Klik op de paneel titel en selecteer **bewerken** om de details van de gegevens die u in dit grafiek diagram wilt uitzetten in te voeren.
    ![Grafana nieuwe grafiek](./media/grafana-plugin/grafana-new-graph-dark.png)

4. Selecteer de Azure Monitor gegevens bron die u hebt geconfigureerd.
   * Azure Monitor metrieken verzamelen: Selecteer **Azure monitor** in de vervolg keuzelijst service. Een lijst met selecters wordt weer gegeven, waar u de resources en metriek kunt selecteren die u in deze grafiek wilt bewaken. Als u metrische gegevens wilt verzamelen van een virtuele machine, gebruikt u de naam ruimte **micro soft. Compute/informatie**. Zodra u Vm's en metrische gegevens hebt geselecteerd, kunt u beginnen met het weer geven van zijn of haar data in het dash board.
     ![Grafana Graph-configuratie voor Azure Monitor](./media/grafana-plugin/grafana-graph-config-for-azure-monitor-dark.png)
   * Verzamelen van Azure Monitor-logboek gegevens: Selecteer **Azure log Analytics** in de vervolg keuzelijst voor services. Selecteer de werk ruimte die u wilt opvragen en stel de query tekst in. U kunt hier alle logboek query's kopiëren die u al hebt of een nieuwe maken. Terwijl u typt in uw query, worden de opties voor automatisch aanvullen weer gegeven en voorgesteld door IntelliSense. Selecteer het type visualisatie, de **tabel** **Time Series** en voer de query uit.
    
     > [!NOTE]
     >
     > De standaard query die wordt meegeleverd met de invoeg toepassing, gebruikt twee macro's: "$ __timeFilter () en $ __interval. 
     > Met deze macro's kan Grafana dynamisch het tijds bereik en de tijd berekenen, wanneer u inzoomt op een deel van een grafiek. U kunt deze macro's verwijderen en een standaard tijd filter gebruiken, zoals *TimeGenerated > geleden (1U)*, maar dat betekent dat de grafiek de functie inzoomen niet ondersteunt.
    
     ![Grafana Graph-configuratie voor Azure Log Analytics](./media/grafana-plugin/grafana-graph-config-for-azure-log-analytics-dark.png)

5. Hieronder vindt u een eenvoudig dash board met twee grafieken. Het één aan de linkerkant toont het CPU-percentage van twee Vm's. In de grafiek aan de rechter kant worden de trans acties in een Azure Storage account weer gegeven, onderverdeeld op basis van het API-type van de trans actie.
    ![Voor beeld van twee grafieken Grafana](media/grafana-plugin/grafana6.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Optioneel: uw aangepaste metrische gegevens in dezelfde Grafana-server bewaken

U kunt ook telegrafie en InfluxDB installeren om zowel aangepaste als op agents gebaseerde metrieken te verzamelen en af te zetten. Er zijn veel invoeg toepassingen voor gegevens bronnen die u kunt gebruiken om deze metrieken samen te brengen in een dash board.

U kunt deze instelling ook opnieuw gebruiken om metrische gegevens van uw Prometheus-server op te zetten. Gebruik de Prometheus-invoeg toepassing voor gegevens bronnen in de galerie met invoeg toepassingen van Grafana.

Hier vindt u een goede verwijzing naar artikelen over het gebruik van telegrafie, InfluxDB, Prometheus en docker
 - [Systeem metrieken bewaken met de TICK-stack op Ubuntu 16,04](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [Een bewakings oplossing voor docker-hosts, containers en container Services](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Hier volgt een afbeelding van een volledig Grafana-dash board met metrische gegevens van Azure Monitor en Application Insights.
![Metrische voorbeeld waarden voor Grafana](media/grafana-plugin/grafana8.png)

## <a name="advanced-grafana-features"></a>Geavanceerde Grafana-functies

### <a name="variables"></a>Variabelen
Sommige query waarden kunnen worden geselecteerd via de vervolg keuzelijst van de gebruikers interface en worden bijgewerkt in de query. Bekijk de volgende query als voor beeld:
```
Usage 
| where $__timeFilter(TimeGenerated) 
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```

U kunt een variabele configureren waarin alle beschik bare **oplossings** waarden worden weer geven en vervolgens uw query bijwerken om deze te gebruiken.
Als u een nieuwe variabele wilt maken, klikt u in het bovenste gedeelte rechtsboven op de knop instellingen van het dash board, selecteert u **variabelen** en vervolgens **Nieuw**.
Definieer op de pagina variabele de gegevens bron en query die moet worden uitgevoerd om de lijst met waarden te verkrijgen.
![Variabele voor Grafana configureren](./media/grafana-plugin/grafana-configure-variable-dark.png)

Nadat u de query hebt gemaakt, kunt u de geselecteerde waarde (n) gebruiken en uw grafieken op de juiste manier reageren:
```
Usage 
| where $__timeFilter(TimeGenerated) and Solution in ($Solutions)
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```
    
![Variabelen voor Grafana gebruiken](./media/grafana-plugin/grafana-use-variables-dark.png)

### <a name="create-dashboard-playlists"></a>Afspeel lijsten met Dash boards maken

Een van de vele handige functies van Grafana is de afspeel lijst met Dash boards. U kunt meerdere Dash boards maken en toevoegen aan een afspeel lijst een interval configureren voor elk dash board dat moet worden weer gegeven. Selecteer **afspelen** om de Dash boards-cyclus te bekijken. Mogelijk wilt u ze weer geven op een grote wand monitor om een status bord voor uw groep te bieden.

![Voor beeld van Grafana-afspeel lijst](./media/grafana-plugin/grafana7.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Grafana-omgeving hebt ingesteld op Azure, worden er kosten in rekening gebracht wanneer Vm's worden uitgevoerd, ongeacht of u deze gebruikt. Opschonen van de resource groep die in dit artikel is gemaakt om te voor komen dat er extra kosten in rekening worden gebracht.

1. Klik in het menu aan de linkerkant in het Azure Portal op **resource groepen** en klik vervolgens op **Grafana**.
2. Klik op de pagina van uw resource groep op **verwijderen**, typ **Grafana** in het tekstvak en klik vervolgens op **verwijderen**.

## <a name="next-steps"></a>Volgende stappen
* [Overzicht van Azure Monitor metrische gegevens](../data-platform.md)
