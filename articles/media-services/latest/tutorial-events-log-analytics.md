---
Titel: Azure Media Services gebeurtenissen opslaan in azure Log Analytics: Azure Media Services beschrijving: informatie over het opslaan van Azure Media Services gebeurtenissen in azure Log Analytics.
Services: Media Services documentationcenter: ' ' Auteur: IngridAtMicrosoft Manager: femila editor: ' MS. service: Media-Services MS. workload: MS. topic: zelf studie MS. date: 08/24/2020 MS. Author: inhenkel
---

# <a name="tutorial-store-azure-media-services-events-in-azure-log-analytics"></a>Zelfstudie: Gebeurtenissen van Azure Media Services opslaan in Azure Log Analytics

## <a name="azure-media-services-events"></a>Gebeurtenissen van Azure Media Services

Azure Media Services v3 verzendt gebeurtenissen naar [Azure Event Grid](media-services-event-schemas.md). U kunt zich op verschillende manieren abonneren op gebeurtenissen en deze opslaan in gegevensarchieven. In deze zelfstudie abonneert u zich abonneren op Media Services-gebeurtenissen met behulp van een [logische app](https://azure.microsoft.com/services/logic-apps/). De logische app wordt voor elke gebeurtenis geactiveerd en slaat de hoofdtekst van de gebeurtenis op in Azure Log Analytics. Zodra de gebeurtenissen zich in Azure Log Analytics bevinden, kunt u andere Azure-services gebruiken om een dashboard te maken, en deze gebeurtenissen te controleren en er waarschuwingen voor versturen. Dit gaan we overigens niet bespreken in deze zelfstudie.

> [!NOTE]
> Het is handig als u al bekend bent met het gebruik van FFmpeg als uw on-premises coderingsprogramma.  Het is geen probleem als u dit programma niet eerder hebt gebruikt. Hieronder vindt u de opdrachtregel en instructies voor het streamen van een video.

U leert het volgende:

> [!div class="checklist"]
> * Een stroom van een logische app maken zonder code
> * Abonneren op gebeurtenisonderwerpen van Azure Media Services
> * Gebeurtenissen parseren en opslaan in Azure Log Analytics
> * Query's uitvoeren op gebeurtenissen vanuit Azure Log Analytics

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

> * Een [Azure-abonnement](how-to-set-azure-subscription.md)
> * Een [Media Services](create-account-howto.md)-account en een resourcegroep.
> * Een installatie van [FFmpeg](https://ffmpeg.org/download.html) voor uw besturingssysteem.
> * Een [Log Analytics-werkruimte](../../azure-monitor/learn/quick-create-workspace.md)

## <a name="subscribe-to-a-media-services-event-with-logic-app"></a>Abonneren op een Media Services-gebeurtenis met een logische app

1. Als u dat nog niet hebt gedaan, maakt u in de Azure-portal een [Log Analytics](../../azure-monitor/learn/quick-create-workspace.md)-werkruimte. U hebt de werkruimte-id en een van de sleutels nodig, dus laat het browservenster geopend. Open de portal vervolgens in een ander tabblad of venster.

1. Ga naar uw Azure Media Services-account en selecteer **Gebeurtenissen**. U ziet nu alle methoden voor het abonneren op Azure Media Services-gebeurtenissen.
    > [!div class="mx-imgBorder"]
    > ![Portal van Azure Media Services](media/tutorial-events-log-analytics/select-events-01a.png)

1. Selecteer het pictogram **Logic Apps** om een logische app te maken. Hiermee opent u de ontwerpfunctie voor logische apps, waar u de stroom kunt maken om de gebeurtenissen vast te leggen en deze naar Log Analytics te pushen. 
    > [!div class="mx-imgBorder"]
    > ![Logische app maken](media/tutorial-events-log-analytics/select-logic-app-02.png)

1. Selecteer het **pictogram +** , selecteer de tenant die u wilt gebruiken en selecteer vervolgens Aanmelden. U ziet een aanmeldingsprompt voor Microsoft.
    > [!div class="mx-imgBorder"]
    > ![Verbinding maken met Azure Event Grid](media/tutorial-events-log-analytics/select-event-add-grid-03.png)
![De tenant selecteren](media/tutorial-events-log-analytics/select-tenant-03a.png)

1. Selecteer **Doorgaan** om u te abonneren op de Media Services-gebeurtenissen.
    > [!div class="mx-imgBorder"]
    > ![Verbonden met Azure Event Grid](media/tutorial-events-log-analytics/select-continue-04.png)

1. Zoek Microsoft.Media.MediaServices in de lijst **Resourcetype**.
    > [!div class="mx-imgBorder"]
    >![Resourcetypen voor Azure Media Services-gebeurtenissen](media/tutorial-events-log-analytics/locate-azure-media-services-events-05.png)

1. Selecteer een waarde voor **Item van gebeurtenistype**. Er wordt een lijst weergegeven met alle gebeurtenissen die door Azure Media Services worden verzonden. U kunt hier de gebeurtenissen selecteren die u wilt bijhouden. U kunt meerdere gebeurtenistypen toevoegen. (Later brengt u een kleine wijziging aan in de stroom van de logische app om elk gebeurtenistype op te slaan in een afzonderlijk Log Analytics-logboek en de naam van het gebeurtenistype dynamisch door te geven aan de naam van het Log Analytics-logboek.)
    > [!div class="mx-imgBorder"]
    > ![Gebeurtenistype van Azure Media Services](media/tutorial-events-log-analytics/select-azure-media-services-event-type-06.png)

1. Selecteer **Save As**.

1. Geef een naam op voor uw logische app.  Er wordt standaard een waarde voor Resourcegroep geselecteerd. Laat de andere instellingen ongewijzigd en selecteer **Maken**.  U keert terug naar het beginscherm van Azure.
    > [!div class="mx-imgBorder"]
    > ![Naamgevingsinterface voor logische app](media/tutorial-events-log-analytics/give-logic-app-name-06a.png)

## <a name="create-an-action"></a>Maak een actie

Nu u bent geabonneerd op een of meer gebeurtenissen kunt u een actie maken.

1. Ga naar de logische app die u zojuist hebt gemaakt door in Alle resources te zoeken naar de naam van de app.

1. Selecteer uw app en selecteer vervolgens **Ontwerpfunctie van logische app**. U ziet het deelvenster van de ontwerpfunctie.

1. Selecteer **+ Nieuwe stap**.

1. Omdat u de gebeurtenissen naar de Azure Log Analytics-service wilt pushen, zoekt u naar 'Azure Log Analytics' en selecteert u Azure Log Analytics Data Collector.
    > [!div class="mx-imgBorder"]
    > ![Azure Log Analytics-gegevensverzamelaar](media/tutorial-events-log-analytics/select-azure-log-analytics-data-collector-07.png)

1. Als u verbinding wilt maken met de Log Analytics-werkruimte, hebt u de werkruimte-id en een agentsleutel nodig. Open de Azure-portal in een nieuw tabblad of venster en ga naar de Log Analytics-werkruimte die u hebt gemaakt voorafgaand aan deze zelfstudie.
    > [!div class="mx-imgBorder"]
    > ![Id van Azure Log Analytics-werkruimte](media/tutorial-events-log-analytics/log-analytics-workspace-id-08.png)

1. Zoek in het menu aan de linkerkant **Agentbeheer** en selecteer deze optie. U ziet nu de agentsleutels die zijn gegenereerd.
    > [!div class="mx-imgBorder"]
    > ![Agentbeheer van Azure Log Analytics](media/tutorial-events-log-analytics/select-agents-management-09.png)

1. Kopieer de waarde voor *Werkruimte-id*.
    > [!div class="mx-imgBorder"]
    > ![Werkruimte-id kopiëren](media/tutorial-events-log-analytics/copy-workspace-id.png)

1. Selecteer in het andere tabblad of-venster van de browser, onder Azure Log Analytics-gegevensverzamelaar,**Gegevens verzenden**, geef uw verbinding een naam en plak de *werkruimte-id* in het veld **Werkruimte-id**.

1. Ga terug naar het tabblad of het venster van de werkruimtebrowser en kopieer de *werkruimtesleutel*.
    > [!div class="mx-imgBorder"]
    > ![Primaire sleutel voor agentbeheer](media/tutorial-events-log-analytics/agents-management-primary-key-10.png)

1. Plak in het andere tabblad of venster van de browser de *werkruimtesleutel* in het veld **Werkruimtesleutel** .

1. Selecteer **Maken**. Nu gaat u de JSON-aanvraagtekst en de naam van het aangepaste logboek maken.

1. Selecteer het veld **JSON-aanvraagtekst**.  Er wordt een link naar **Dynamische inhoud toevoegen** weergegeven.

1. Selecteer **Dynamische inhoud toevoegen** en selecteer vervolgens **Onderwerp**.

1. Doe hetzelfde voor **Aangepaste logboeknaam**.
    > [!div class="mx-imgBorder"]
    > ![Onderwerp geselecteerd](media/tutorial-events-log-analytics/topic-selected.png)

1. Selecteer **Codeweergave** voor de logische app. Zoek naar de regels Inputs en Log-Type.
    > [!div class="mx-imgBorder"]
    > ![Codeweergave van twee regels](media/tutorial-events-log-analytics/code-view-two-lines.png)

1. Wijzig de waarde voor `body` van `"@triggerBody()?['topic']"` in `"@{triggerBody()}"`. Dit is voor het parseren van het hele bericht naar Log Analytics.

1. Wijzig het `Log-Type` van `"@triggerBody()?['topic']"` in `"@replace(triggerBody()?['eventType'],'.','')"`. (Hierdoor wordt "." vervangen omdat deze niet zijn toegestaan in logboeknamen van Log Analytics.)
    > [!div class="mx-imgBorder"]
    > ![JSON van logische app na wijziging](media/tutorial-events-log-analytics/changed-lines.png)

1. Selecteer **Opslaan**.

1. Als u wilt controleren of alles in orde is, selecteert u **Ontwerpfunctie van logische app**.
    > [!div class="mx-imgBorder"]
    > ![Body en functiestappen controleren](media/tutorial-events-log-analytics/verify-changes-to-json.png)

1. Wanneer u alle resources in de resourcegroep onderzoekt, ziet u een logische app en twee API-connectors voor logische apps, één voor de gebeurtenissen en één voor Log Analytics. Lees [Event Grid-systeemonderwerpen](../../event-grid/system-topics.md)voor meer informatie over Event Grid-systeemonderwerpen.
    > [!div class="mx-imgBorder"]
    > ![Alle nieuwe resources in de resourcegroep bekijken](media/tutorial-events-log-analytics/contoso-rg-listing.png)

## <a name="test"></a>Testen

Als u wilt testen hoe dit werkt, maakt u een livegebeurtenis in Azure Media Services. Maak een live RTMP-gebeurtenis en gebruik ffmpeg om een 'live'-stream op basis van een MP4-voorbeeldbestand te pushen. Als de gebeurtenis is gemaakt, haalt u de RTMP-URL voor opnemen op.

1. Selecteer **Live streamen** in uw Media Services-account.
    > [!div class="mx-imgBorder"]
    > ![Een livegebeurtenis maken in Azure Media Services](media/tutorial-events-log-analytics/live-event.png)

1. Selecteer **Livegebeurtenis** toevoegen.

1. Voer in het veld **Naam van de livegebeurtenis** een naam in. (Het veld **Beschrijving** is optioneel.)

1. Selecteer **Standaard** als codering voor de cloud.

1. Selecteer **Standaard 720p** voor de voorinstelling voor Encoding.

1. Selecteer **RTMP** als het invoerprotocol.

1. Selecteer **Ja** voor de URL voor permanente invoer.

1. Selecteer **Ja** om de gebeurtenis te starten wanneer deze wordt gemaakt.

    > [!WARNING]
    > Facturering wordt gestart als u Ja selecteert.  Als u wilt wachten met het starten van de stroom tot het moment *net* voordat u begint met het streamen met FFmpeg, selecteert u **Nee**. U moet dan niet vergeten om zelf uw livegebeurtenis te starten.

    > [!div class="mx-imgBorder"]
    > ![Instellingen voor livegebeurtenis](media/tutorial-events-log-analytics/live-event-settings.png)

1. Schakel het selectievakje dat bevestigt dat u over de rechten beschikt om de inhoud of het bestand te gebruiken.

1. Selecteer **Controleren + maken**.

1. Controleer uw instellingen en selecteer vervolgens **Maken**.  De vermelding van de livegebeurtenis wordt weergegeven, evenals de URL voor het opnemen van livegebeurtenis.

1. Kopieer de **URL voor opnemen** naar het klembord.

1. Selecteer de **livegebeurtenis** in de lijst om de producentweergave te bekijken.

### <a name="stream-with-ffmpeg-cli"></a>Streamen met FFmpeg CLI

1. Gebruik de volgende opdrachtregel.

    ```AzureCLI
    ffmpeg -i <localpathtovideo> -map 0 -c:v libx264 -c:a copy -f flv <ingestURL>/mystream
    ```

1. Vervang `<ingestURL>` door de URL voor opnemen die u hebt naar het klembord hebt gekopieerd.
1. Vervang `<localpathtovideo>` door het lokale pad van het bestand dat u wilt streamen vanuit FFmpeg.
1. Voeg aan het einde een unieke naam toe, bijvoorbeeld `mystream`.
1. Pas de opdrachtregel aan om uw testbronbestand te gebruiken. Pas ook eventuele andere systeemvariabelen aan.
1. Voer de opdracht uit. Na een paar seconden moet de speler Producentweergave gaan streamen. (Vernieuw de speler als de video niet automatisch wordt weergegeven.)

    > [!div class="mx-imgBorder"]
    > ![In Producerweergave controleren of juiste video wordt afgespeeld](media/tutorial-events-log-analytics/live-event-producer-view.png)

## <a name="verify-the-events"></a>De gebeurtenissen controleren

Met de livestream worden door Azure Media Services verschillende gebeurtenissen verzonden die de stroom van de logische app activeren. Als u dit wilt controleren, gaat u naar de logische app en kijkt u of er triggers worden geactiveerd door de gebeurtenissen van Media Services.

1. Ga naar de overzichtspagina van de logische app. U ziet daar een uitvoeringsgeschiedenis met de taken die zijn voltooid.
    > [!div class="mx-imgBorder"]
    > ![Controleren of taken goed zijn uitgevoerd in de logische app](media/tutorial-events-log-analytics/run-history.png)

1. Selecteer een voltooide taak. De details van de taak tijdens runtime worden weergegeven.
1. Selecteer **Gegevens verzenden** om meer details weer te geven. In dit geval ziet u voor de gebeurtenis `MicrosoftMediaLiveEventEncoderConnected` dat deze is vastgelegd, naast de geparseerde body. Dit is wat er naar de Azure Log Analytics-werkruimte wordt gepusht.
    > [!div class="mx-imgBorder"]
    > ![Gegevens verzenden](media/tutorial-events-log-analytics/verify-send-data.png)

## <a name="verify-the-logs"></a>De logboeken controleren

1. Ga naar de Log Analytics-werkruimte die u eerder hebt gemaakt.

1. Selecteer **Logboeken**.
1. Sluit de pop-up met voorbeeldquery's.
1. U ziet een lijst met aangepaste logboeken. Selecteer de pijl-omlaag om deze uit te vouwen. Hier ziet u de gebeurtenisnaam `MicrosoftMediaLiveEventEncoderConnected`.
1. Selecteer de naam van de gebeurtenis om deze uit te vouwen.
1. Wanneer u het 'oogpictogram' selecteert, wordt er een voorbeeld van het queryresultaat weergegeven.
1. Selecteer **Weergeven in query-editor** en selecteer het item vervolgens onder **TimeGenerated UTC** om het uit te vouwen en de onbewerkte gegevens weer te geven.

![Gedetailleerde uitvoer van gebeurtenis bekijken in Log Analytics](media/tutorial-events-log-analytics/raw-data.png)

## <a name="delete-resources"></a>Resources verwijderen

Als u de resources die u tijdens deze zelfstudie hebt gemaakt niet meer nodig hebt, moet u alle resources in de resourcegroep verwijderen om te voorkomen dat er nog kosten in rekening worden gebracht.

## <a name="next-steps"></a>Volgende stappen

U kunt verschillende query's maken en deze opslaan. Deze kunnen worden toegevoegd aan het [Azure-dashboard](../../azure-monitor/learn/tutorial-logs-dashboards.md).