---
title: Problemen met Azure FarmBeats oplossen
description: In dit artikel wordt beschreven hoe u problemen met Azure FarmBeats oplost.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: dd0ed78c56e4d656a2ecee6395d831ed093e85b5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001606"
---
# <a name="troubleshoot-azure-farmbeats"></a>Problemen met Azure FarmBeats oplossen

Dit artikel bevat oplossingen voor veelvoorkomende problemen met Azure FarmBeats. Neem contact op met onze [Q&een ondersteunings forum](/answers/topics/azure-farmbeats.html) of e-mail ons op voor meer hulp farmbeatssupport@microsoft.com .

> [!NOTE]
  > Als u FarmBeats hebt geïnstalleerd tijdens april en uw taken mislukken met een leeg fout bericht, heeft uw installatie mogelijk geen batch quotum toegewezen om de prioriteit van de ondersteuning voor kritieke en veiligheids organisaties te bepalen. Zie [hier](https://azure.microsoft.com/blog/update-2-on-microsoft-cloud-services-continuity/) voor meer informatie. U moet Vm's aanvragen om te worden toegewezen aan het batch-account om taken te kunnen uitvoeren.

## <a name="install-issues"></a>Installatie problemen

  > [!NOTE]
  > Als u de installatie opnieuw wilt starten vanwege een fout, moet u ervoor zorgen dat u de **resource groep** verwijdert of alle resources verwijdert uit de resource groep voordat u de installatie opnieuw start.

### <a name="invalid-sentinel-credentials"></a>Ongeldige verklikker referenties

De Sentinel-referenties die tijdens de installatie zijn gegeven, zijn onjuist. Start de installatie opnieuw met de juiste referenties.

### <a name="the-regional-account-quota-of-batch-accounts-for-the-specified-subscription-has-been-reached"></a>Het regionale account quotum van de batch-accounts voor het opgegeven abonnement is bereikt

Verhoog het quotum of verwijder de niet-gebruikte batch-accounts en start de installatie opnieuw.

### <a name="invalid-resource-group-location"></a>Ongeldige locatie van resource groep

Zorg ervoor dat de **resource groep** zich op dezelfde locatie bevindt als de **regio** die tijdens de installatie is opgegeven.

### <a name="other-install-issues"></a>Overige installatie problemen

Neem contact met ons op met de volgende details:

- Uw abonnements-ID
- Naam resourcegroep
- Volg de onderstaande stappen om het logboek bestand voor de implementatie fout te koppelen:

    1. Ga naar de **resource groep** in het Azure Portal.

    2. Selecteer **implementaties** onder **instellingen** in het gedeelte aan de linkerkant.

    3. Voor elke implementatie die **niet kan** worden weer gegeven, selecteert u de details en downloadt u de details van de implementatie. Voeg dit bestand toe aan de e-mail.

## <a name="sensor-telemetry"></a>Sensor-telemetrie

### <a name="cant-view-telemetry-data"></a>Kan geen telemetriegegevens weer geven

**Symptoom**: er zijn apparaten of Sens oren geïmplementeerd en u hebt FarmBeats gekoppeld aan de partner van uw apparaat, maar u kunt geen telemetriegegevens ophalen of weer geven op FarmBeats.

**Corrigerende actie**

1. Ga naar uw FarmBeats-resource groep.
2. Selecteer de **Event hub** -naam ruimte (' sensor-partner-eh-naam ruimte-XXXX '), klik op Event hubs en controleer vervolgens het aantal inkomende berichten in de Event hub dat is toegewezen aan de partner
3. Gebruik een van de volgende methoden:

   - Als er *geen inkomende berichten* zijn, neemt u contact op met de partner van uw apparaat.  
   - Als er *inkomende berichten* zijn, kunt u contact met ons opnemen met uw Datahub-en Accelerator logboeken en vastgelegde telemetrie.

Als u wilt weten hoe u Logboeken kunt downloaden, gaat u naar de sectie [Logboeken hand matig verzamelen](#collect-logs-manually) .  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Er kunnen geen telemetriegegevens worden weer gegeven na het opnemen van historische/streaming-gegevens van uw Sens oren

**Symptoom**: er zijn apparaten of Sens oren geïmplementeerd en u hebt de apparaten/Sens oren op FarmBeats en opgenomen telemetrie naar de EventHub gemaakt, maar u kunt geen telemetriegegevens ophalen of weer geven op FarmBeats.

**Corrigerende actie**

1. Zorg ervoor dat u de partner registratie op de juiste manier hebt uitgevoerd. u kunt dit controleren door naar uw datahub Swagger te gaan, naar/partner API te gaan, een Get-en check-taak uit te voeren als de partner is geregistreerd. Als dat niet het geval is, volgt u deze [stappen](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) om een partner toe te voegen.

2. Zorg ervoor dat u de juiste indeling voor telemetrie-berichten hebt gebruikt:

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>":<value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>De Azure-Event Hubs niet hebben connection string

**Corrigerende actie**

1. Ga in Datahub Swagger naar de partner-API.
2. Selecteer **Get**  >  **Try it out**  >  **uitvoeren** uitproberen.

> [!NOTE]
> De partner-ID van de sensor partner die u wilt.

3. Ga terug naar de partner-API en selecteer **ophalen/ \<ID>**.
4. Geef de partner-ID op uit stap 3 en selecteer vervolgens **uitvoeren**.

   De API-reactie moet de Event Hubs connection string hebben.

### <a name="device-appears-offline"></a>Apparaat wordt offline weer gegeven

**Symptomen**: er zijn apparaten geïnstalleerd en u hebt FarmBeats gekoppeld aan de partner van uw apparaat. De apparaten zijn online en verzenden telemetriegegevens, maar ze worden offline weer gegeven.

**Corrigerende actie** Het rapportage-interval is niet geconfigureerd voor dit apparaat. Neem contact op met de fabrikant van uw apparaat om het rapportage-interval in te stellen. 

### <a name="error-deleting-a-device"></a>Fout bij het verwijderen van een apparaat

Tijdens het verwijderen van een apparaat kan een van de volgende veelvoorkomende fout scenario's optreden:  

**Bericht**: er wordt naar het apparaat verwezen in Sens oren: er zijn een of meer Sens oren gekoppeld aan het apparaat. Verwijder de Sens oren en verwijder vervolgens het apparaat.  

Dat **wil zeggen**: het apparaat is gekoppeld aan meerdere Sens oren die in de farm zijn geïmplementeerd.

**Corrigerende actie**  

1. Verwijder de Sens oren die zijn gekoppeld aan het apparaat via Accelerator.  
2. Als u de Sens oren wilt koppelen aan een ander apparaat, vraagt u de partner van het apparaat hetzelfde te doen.  
3. Verwijder het apparaat met behulp van een `DELETE API` aanroep en stel de Force-para meter in op *True*.  

**Bericht**: in apparaten wordt naar het apparaat verwezen als ParentDeviceId: er zijn een of meer apparaten die zijn gekoppeld aan dit apparaat als onderliggende apparaten. Verwijder ze en verwijder dit apparaat. "  

Dat **wil zeggen**: er zijn andere apparaten aan het apparaat gekoppeld.  

**Corrigerende actie**

1. De apparaten verwijderen die zijn gekoppeld aan dit specifieke apparaat.  
2. Het specifieke apparaat verwijderen.  

    > [!NOTE]
    > U kunt een apparaat niet verwijderen als er Sens oren aan zijn gekoppeld. Voor meer informatie over het verwijderen van gekoppelde Sens oren, zie de sectie **sensor verwijderen** in [sensor gegevens ophalen van sensor partners](get-sensor-data-from-sensor-partner.md).
    > Partners hebben geen machtiging voor het verwijderen van een apparaat of sensor. Alleen beheerders hebben toestemming om te verwijderen.

## <a name="issues-with-jobs"></a>Problemen met taken

### <a name="farmbeats-internal-error"></a>Interne FarmBeats-fout

**Bericht**: ' FarmBeats interne fout ' Zie probleemoplossings gids voor meer informatie. '

**Corrigerende actie** Dit probleem kan het gevolg zijn van een tijdelijke fout in de gegevens pijplijn. Maak de taak opnieuw. Als de fout zich blijft voordoen, kunt u contact met ons opnemen met het fout bericht/de logboeken.

## <a name="accelerator-troubleshooting"></a>Problemen met accelerators oplossen

### <a name="access-control"></a>Toegangsbeheer

**Probleem**: er wordt een fout bericht weer gegeven tijdens het toevoegen van een roltoewijzing.

**Bericht**: er zijn geen overeenkomende gebruikers gevonden.

**Corrigerende actie** Controleer de e-mail-ID waarvoor u een roltoewijzing wilt toevoegen. De e-mail-ID moet exact overeenkomen met de ID, die is geregistreerd voor die gebruiker in de Active Directory. Als de fout zich blijft voordoen, kunt u contact met ons opnemen met het fout bericht/de logboeken.

### <a name="unable-to-log-in-to-accelerator"></a>Kan niet aanmelden bij de Accelerator

**Bericht**: ' fout: u bent niet gemachtigd om de service aan te roepen. Neem contact op met de beheerder voor autorisatie. "

**Corrigerende actie** Vraag de beheerder u toestemming te geven voor toegang tot de FarmBeats-implementatie. U kunt dit doen door een bericht te plaatsen van de RoleAssignment-Api's of via de Access Control in het deel venster **instellingen** in Accelerator.  

Als u al toegang hebt gekregen en deze fout hebt geconstateerd, probeert u het opnieuw door de pagina te vernieuwen. Als de fout zich blijft voordoen, kunt u contact met ons opnemen met het fout bericht/de logboeken.

![Scherm opname waarin de autorisatie fout wordt weer gegeven.](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Problemen met de Accelerator  

**Probleem**: u hebt een verwerkings fout van een onbepaalde oorzaak ontvangen.

**Bericht**: ' fout: er is een onbekende fout opgetreden. '

**Corrigerende actie** Deze fout treedt op als u de pagina te lang niet-actief blijft. Vernieuw de pagina. Als de fout zich blijft voordoen, kunt u contact met ons opnemen met het fout bericht/de logboeken.

**Probleem**: FarmBeats Accelerator geeft niet de nieuwste versie weer, zelfs nadat u FarmBeatsDeployment hebt bijgewerkt.

**Corrigerende actie** Deze fout treedt op vanwege persistentie van de service medewerker in de browser. Ga als volgt te werk:

1. Sluit alle browser tabbladen met Accelerator open en sluit het browser venster.
2. Start een nieuw exemplaar van de browser en laad de Accelerator-URI opnieuw. Met deze actie wordt de nieuwe versie van Accelerator geladen.

## <a name="sentinel-imagery-related-issues"></a>Sentinel: problemen met betrekking tot installatie kopieën

### <a name="wrong-username-or-password"></a>Onjuiste gebruikers naam of wacht woord

**Taak fout bericht**: volledige verificatie is vereist voor toegang tot deze resource.

**Corrigerende actie**: Voer een van de volgende handelingen uit:

- Werk FarmBeats met de juiste gebruikers naam/wacht woord bij met behulp van de onderstaande stappen en probeer de taak opnieuw uit te voeren.

  **Sentinel-gebruikers naam bijwerken**

    1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
    2. Zoek in **het zoekvak naar de resource** groep FarmBeats Datahub.
    3. Opslag account opslag selecteren * * * * * > **containers**  >  **batch-prep-files**  >  **to_vm**  >  **config.ini**
    4. Selecteer **bewerken**
    5. De gebruikers naam in de sectie sentinel_account bijwerken

  **Verklikker wachtwoord bijwerken**

    1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
    2. Zoek in **het zoekvak naar de resource** groep FarmBeats Datahub.
    3. Selecteer de sleutel kluis-* * * * *
    4. Selecteer toegangs beleid onder instellingen
    5. Selecteer **Toegangsbeleid toevoegen**
    6. Het gebruik van een **geheim beheer** voor configureren vanaf sjabloon en uzelf toevoegen aan de principal
    7. Selecteer **toevoegen** en selecteer vervolgens **Opslaan** op de pagina **toegangs beleid**
    8. **Geheimen** selecteren onder **instellingen**
    9. **Verklikker wachtwoord** selecteren
    10. Maak een nieuwe versie van de waarde en schakel deze in.

- Voer de mislukte taak opnieuw uit of voer een satelliet-indexen taak uit voor een datum bereik van 5 tot 7 dagen en controleer vervolgens of de taak is geslaagd.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Sentinel hub: onjuiste URL of site niet toegankelijk

**Taak fout bericht**: er is iets fout gegaan. De pagina die u probeert te openen, is (tijdelijk) niet beschikbaar. "

**Corrigerende actie**:

1. Open [Sentinel](https://scihub.copernicus.eu/dhus/) in uw browser om te zien of de website toegankelijk is.
2. Als de website niet toegankelijk is, controleert u of alle firewalls, bedrijfs netwerken of andere blokkerende software toegang tot de website verhinderen en voert u vervolgens de benodigde stappen uit om de verklikker-URL toe te staan. 
3. Voer de mislukte taak opnieuw uit of voer een satelliet-indexen taak uit voor een datum bereik van 5 tot 7 dagen en controleer vervolgens of de taak is voltooid.  

### <a name="sentinel-server-down-for-maintenance"></a>Verklikker server: omlaag voor onderhoud

**Taak fout bericht**: de Open Access-hub Copernicus wordt binnenkort weer gegeven. Onze excuses voor het ongemak. we voeren nu een aantal onderhoud uit. We zijn binnenkort weer online. " 

**Corrigerende actie**:

Dit probleem kan zich voordoen als er onderhouds activiteiten op de Sentinel-server worden uitgevoerd.

1. Als een taak of pijp lijn mislukt omdat er onderhoud wordt uitgevoerd, dient u de taak na enige tijd opnieuw in te dienen. 

   Voor informatie over geplande of niet-geplande verklikker onderhouds activiteiten gaat u naar de [nieuws site Copernicus Open Access hub](https://scihub.copernicus.eu/news/) .  

2. Voer de mislukte taak opnieuw uit of voer een satelliet-indexen taak uit voor een datum bereik van 5 tot 7 dagen en controleer vervolgens of de taak is voltooid.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinel: maximum aantal verbindingen bereikt

**Taak fout bericht**: het maximum aantal gelijktijdige stromen dat door de gebruiker wordt bereikt \<username> .

Dat **wil zeggen**: als een taak mislukt omdat het maximum aantal verbindingen is bereikt, wordt hetzelfde verklikker account in meerdere taken gebruikt.

**Corrigerende actie**: Voer een van de volgende handelingen uit:

* Wacht totdat de andere taken zijn voltooid voordat u de mislukte taak opnieuw uitvoert.
* Maak een nieuw verklikker account en werk vervolgens de Sentinel-gebruikers naam en het wacht woord bij in FarmBeats.

### <a name="sentinel-server-refused-connection"></a>Verklikker server: verbinding geweigerd

**Taak fout bericht**: de server heeft de verbinding geweigerd bij: http://172.30.175.69:8983/solr/dhus .

**Corrigerende actie**: dit probleem kan zich voordoen als er onderhouds activiteiten worden uitgevoerd op de Sentinel-server.

1. Als een taak of pijp lijn mislukt omdat er onderhoud wordt uitgevoerd, dient u de taak na enige tijd opnieuw in te dienen.

   Voor informatie over geplande of niet-geplande verklikker onderhouds activiteiten gaat u naar de [nieuws site Copernicus Open Access hub](https://scihub.copernicus.eu/news/) .  

2. Voer de mislukte taak opnieuw uit of voer een satelliet-indexen taak uit voor een datum bereik van 5 tot 7 dagen en controleer vervolgens of de taak is voltooid.

### <a name="soil-moisture-map-has-white-areas"></a>Kaart voor bodem vocht bevat witte gebieden

**Probleem**: de **kaart voor bodem vocht** is gegenereerd, maar de kaart heeft grotendeels witte gebieden.

**Corrigerende actie**: dit probleem kan optreden als de satelliet indexen die zijn gegenereerd voor de tijd waarvoor de kaart is aangevraagd, NDVI waarden hebben die kleiner zijn dan 0,3. Ga voor meer informatie naar [technische hand leiding van Sentinel](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm).

1. Voer de taak opnieuw uit voor een ander datum bereik en controleer of de NDVI-waarden in de satelliet-indexen meer dan 0,3 zijn.

## <a name="collect-logs-manually"></a>Logboeken hand matig verzamelen

[Installeer en implementeer Azure Storage Explorer]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-in-datahub"></a>Azure Data Factory-taak Logboeken of App Service-logboeken verzamelen in Datahub

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Zoek in **het zoekvak naar de resource** groep FarmBeats Datahub.
3. Zoek in het dash board van de **resource groep** naar het *datahublogs \** -opslag account. Bijvoorbeeld *datahublogsmvxmq*.  
4. Selecteer in de kolom **naam** het opslag account om het dash board van het **opslag account** weer te geven.
5. Selecteer in **het \* *deel venster datahubblogs _ de optie _* openen in verkenner** om de **geopende Azure Storage Explorer** -toepassing weer te geven.
6. Selecteer **BLOB-containers** in het linkerdeel venster en selecteer vervolgens **taak-logboeken** voor Azure Data Factory Logboeken of **appinsights-logboeken** voor app service-Logboeken.
7. Selecteer **downloaden** en down load de logboeken naar een lokale map op uw computer.

    ![Scherm opname van de gedownloade logboek bestanden.](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-for-accelerator"></a>Azure Data Factory-taak Logboeken of App Service logboeken voor de Accelerator verzamelen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Zoek in **het zoekvak naar de resource** groep FarmBeats Accelerator.
3. Zoek het *\* opslag* account op in het dash board van de **resource groep** . Bijvoorbeeld *storagedop4k \**.
4. Selecteer het opslag account in de kolom **naam** om het dash board van het **opslag account** weer te geven.
5. Selecteer in **het \* *deel venster opslag _ de optie _* openen in Verkenner** om de Azure Storage Explorer-toepassing te openen.
6. Selecteer **BLOB-containers** in het linkerdeel venster en selecteer vervolgens **taak-logboeken** voor Azure Data Factory Logboeken of **appinsights-logboeken** voor app service-Logboeken.
7. Selecteer **downloaden** en down load de logboeken naar een lokale map op uw computer.

## <a name="high-cpu-usage"></a>Hoog CPU-gebruik

**Fout**: er wordt een e-mail waarschuwing weer geven die verwijst naar een **hoge waarschuwing voor CPU-gebruik**.

**Corrigerende actie**:

1. Ga naar de resource groep FarmBeats Datahub.
2. Selecteer **app service**.  
3. Ga naar de pagina met [prijs](https://azure.microsoft.com/pricing/details/app-service/windows/)opschalen app service en selecteer een geschikte prijs categorie.

## <a name="weather-data-job-failures"></a>Fouten met weer gegevens taken

**Fout**: u kunt taken uitvoeren om weer gegevens op te halen, maar de taak mislukt

### <a name="collect-logs-to-troubleshoot-weather-data-job-failures"></a>Logboeken verzamelen om problemen met weer gegeven gegevens taken op te lossen

1. Ga naar de resource groep FarmBeats in de Azure Portal.
2. Klik op de Data Factory-service die deel uitmaakt van de resource groep. De service heeft een tag ' SKU: Datahub '

> [!NOTE]
> Als u de tags van de services binnen de resource groep wilt weer geven, klikt u op ' kolommen bewerken ' en voegt u ' Tags ' toe aan de weer gave van de resource groep

:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-1.png" alt-text="Scherm afbeelding die de code SKU: Datahub markeert.":::

3. Klik op de pagina overzicht van de Data Factory op **auteur en monitor**. Er wordt een nieuw tabblad geopend in uw browser. Klik op **monitor**

:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-2.png" alt-text="Scherm opname van de menu optie monitor.":::

4. Er wordt een lijst met pijplijn uitvoeringen weer geven die deel uitmaken van de weers-taak wordt uitgevoerd. Klik op de taak waarvoor u logboeken wilt verzamelen
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-3.png" alt-text="Scherm afbeelding die de menu optie pijplijn uitvoeringen en de geselecteerde taak markeert.":::

5. Op de overzichts pagina pijplijn ziet u de lijst met uitvoeringen van de activiteit. Noteer de run-Id's van de activiteiten waarvoor u logboeken wilt verzamelen
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-4.png" alt-text="Scherm afbeelding met de lijst met uitvoeringen van de activiteit.":::

6. Ga terug naar de resource groep FarmBeats in Azure Portal en klik op het opslag account met de naam **datahublogs-xxxx**
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-5.png" alt-text="Scherm afbeelding die het opslag account markeert met de naam datahublogs-XXXX.":::

7. Klik op **containers**  ->  **adfjobs**. Voer in het zoekvak de taak run-ID in die u in stap 5 hierboven hebt genoteerd.
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-6.png" alt-text="Project FarmBeats":::

8. Het Zoek resultaat bevat de map met de logboeken die betrekking hebben op de taak. Down load de logboeken en verzend het naar farmbeatssupport@microsoft.com voor hulp bij fout opsporing in het probleem.