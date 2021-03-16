---
title: HPC-cache van Azure beheren en bijwerken
description: De Azure HPC-cache beheren en bijwerken met behulp van de Azure Portal of Azure CLI
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/08/2021
ms.author: v-erkel
ms.openlocfilehash: b34beb65bb8c4136887651d8365c937b17718572
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103471904"
---
# <a name="manage-your-cache"></a>Uw cache beheren

Op de pagina cache-overzicht in de Azure Portal worden project details, de cache status en de basis statistieken voor uw cache weer gegeven. Het bevat ook besturings elementen voor het stoppen of starten van de cache, het verwijderen van de cache, het leegmaken van gegevens naar lange termijn opslag en het bijwerken van software.

In dit artikel wordt ook uitgelegd hoe u deze basis taken kunt uitvoeren met de Azure CLI.

Als u de overzichts pagina wilt openen, selecteert u uw cache resource in het Azure Portal. Laad bijvoorbeeld de pagina **alle resources** en klik op de naam van de cache.

![scherm afbeelding van de overzichts pagina van een Azure HPC-cache-exemplaar](media/hpc-cache-overview.png)

Met de knoppen boven aan de pagina kunt u de cache beheren:

* **Starten** en [**stoppen**](#stop-the-cache) -de cache bewerking hervatten of onderbreken
* [**Flush**](#flush-cached-data) -schrijft gewijzigde gegevens naar opslag doelen
* [**Upgrade**](#upgrade-cache-software) -de cache software bijwerken
* [**Diagnostische gegevens verzamelen**](#collect-diagnostics) : uploads van fout opsporingsgegevens
* **Vernieuwen** : Hiermee wordt de pagina Overzicht opnieuw geladen
* [**Verwijderen**](#delete-the-cache) : de cache wordt definitief vernietigd

Meer informatie over deze opties vindt u hieronder.

Klik op de onderstaande afbeelding om een [video](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) te bekijken waarin cache beheer taken worden getoond.

[![Video miniatuur: Azure HPC-cache: beheren (Klik hier om naar de video pagina te bezoeken)](media/video-5-manage.png)](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)

## <a name="stop-the-cache"></a>De cache stoppen

U kunt de cache stoppen om de kosten te verlagen tijdens een inactieve periode. Er worden geen kosten in rekening gebracht voor de actieve tijds duur tijdens het stoppen van de cache, maar er worden kosten in rekening gebracht voor de toegewezen schijf ruimte in de cache. (Zie de pagina met [prijzen](https://aka.ms/hpc-cache-pricing) voor meer informatie.)

Een gestopte cache reageert niet op client aanvragen. U moet clients ontkoppelen voordat u de cache stopt.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Met de knop **stoppen** wordt een actieve cache onderbroken. De knop **stoppen** is beschikbaar wanneer de status van een cache **in orde** is of **gedegradeerd** is.

![scherm afbeelding van de bovenste knoppen met een stop markering en een pop-upbericht waarin de actie stoppen wordt beschreven en u wordt gevraagd om door te gaan? met Ja (standaard) en geen knoppen](media/stop-cache.png)

Nadat u op Ja hebt geklikt om het stoppen van de cache te bevestigen, wordt de inhoud van de cache automatisch leeg gemaakt naar de opslag doelen. Dit proces kan enige tijd duren, maar Hiermee wordt de consistentie van gegevens gegarandeerd. Ten slotte wordt de cache status gewijzigd in **gestopt**.

Als u een gestopt cache opnieuw wilt activeren, klikt u op de knop **starten** . Er is geen bevestiging nodig.

![scherm afbeelding van de bovenste knoppen met de markering begin gemarkeerd](media/start-cache.png)

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

[Azure cli instellen voor Azure HPC-cache](./az-cli-prerequisites.md).

Een cache tijdelijk onderbreken met de opdracht [AZ HPC-cache stop](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-stop) . Deze actie is alleen geldig wanneer de status van een cache **in orde** is of **gedegradeerd** is.

De inhoud van de cache wordt automatisch naar de opslag doelen gewist voordat deze wordt gestopt. Dit proces kan enige tijd duren, maar Hiermee wordt de consistentie van gegevens gegarandeerd.

Wanneer de actie is voltooid, wordt de cache status gewijzigd in **gestopt**.

Activeer een gestopt cache opnieuw met [AZ HPC-cache start](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-start).

Wanneer u de opdracht start of stop geeft, wordt de opdracht regel weer gegeven met het status bericht ' running ' totdat de bewerking is voltooid.

```azurecli
$ az hpc-cache start --name doc-cache0629
 - Running ..
```

Bij voltooiing wordt het bericht bijgewerkt naar voltooid en worden retour codes en andere informatie weer gegeven.

```azurecli
$ az hpc-cache start --name doc-cache0629
{- Finished ..
  "endTime": "2020-07-01T18:46:43.6862478+00:00",
  "name": "c48d320f-f5f5-40ab-8b25-0ac065984f62",
  "properties": {
    "output": "success"
  },
  "startTime": "2020-07-01T18:40:28.5468983+00:00",
  "status": "Succeeded"
}
```

---

## <a name="flush-cached-data"></a>In cache opgeslagen gegevens wissen

Met de knop **leegmaken** op de overzichts pagina wordt aan de cache meegedeeld dat alle gewijzigde gegevens die in de cache zijn opgeslagen, direct naar de back-end-opslag doelen worden geschreven. De cache slaat gegevens op in de opslag doelen, dus is het niet nodig om dit hand matig te doen, tenzij u ervoor wilt zorgen dat het back-end-opslag systeem up-to-date is. U kunt bijvoorbeeld **Flush** gebruiken voordat u een moment opname van de opslag maakt of de grootte van de gegevensset controleert.

> [!NOTE]
> Tijdens het leegmaak proces kan de cache geen client aanvragen verwerken. De toegang tot de cache wordt onderbroken en hervat nadat de bewerking is voltooid.

Wanneer u de cache-leegmaak bewerking start, wordt het accepteren van client aanvragen door de cache gestopt en wordt de cache status op de overzichts pagina gewijzigd in **leegmaken**.

De gegevens in de cache worden opgeslagen in de juiste opslag doelen. Afhankelijk van de hoeveelheid gegevens die moet worden leeg gemaakt, kan het proces enkele minuten of meer tijd in beslag nemen.

Nadat alle gegevens zijn opgeslagen in opslag doelen, begint de cache automatisch met het uitvoeren van client aanvragen. De cache status wordt teruggezet naar **in orde**.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Als u de cache wilt leegmaken, klikt u op de knop **leegmaken** en klikt u vervolgens op **Ja** om de actie te bevestigen.

![scherm afbeelding van de bovenste knoppen met flush gemarkeerd en een pop-upbericht waarin de actie Flush wordt beschreven en u wordt gevraagd om door te gaan? met Ja (standaard) en geen knoppen](media/hpc-cache-flush.png)

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

[Azure cli instellen voor Azure HPC-cache](./az-cli-prerequisites.md).

Gebruik [AZ HPC-cache flush](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-flush) om ervoor te zorgen dat de cache alle gewijzigde gegevens naar de opslag doelen schrijft.

Voorbeeld:

```azurecli
$ az hpc-cache flush --name doc-cache0629 --resource-group doc-rg
 - Running ..
```

Wanneer het leegmaken is voltooid, wordt een bericht met succes geretourneerd.

```azurecli
{- Finished ..
  "endTime": "2020-07-09T17:26:13.9371983+00:00",
  "name": "c22f8e12-fcf0-49e5-b897-6a6e579b6489",
  "properties": {
    "output": "success"
  },
  "startTime": "2020-07-09T17:25:21.4278297+00:00",
  "status": "Succeeded"
}
$
```

---

## <a name="upgrade-cache-software"></a>Cache software bijwerken

Als er een nieuwe software versie beschikbaar is, wordt de knop **bijwerken** actief. Boven aan de pagina wordt ook een bericht weer gegeven over het bijwerken van software.

![scherm afbeelding van de bovenste rij met knoppen waarop de knop bijwerken is ingeschakeld](media/hpc-cache-upgrade-button.png)

Client toegang wordt niet onderbroken tijdens een software-upgrade, maar de prestaties van de cache worden trager. Upgrade van software plannen tijdens niet-piek uren of in een geplande onderhouds periode.

Het kan enkele uren duren voordat de software-update is uitgevoerd. Caches die zijn geconfigureerd met een hogere door Voer, nemen meer tijd in beslag dan caches met kleinere waarden voor piek doorvoer.

Wanneer een software-upgrade beschikbaar is, hebt u een week of zodat deze hand matig kan worden toegepast. De eind datum wordt vermeld in het upgrade bericht. Als u tijdens die tijd niet bijwerkt, past Azure automatisch de update toe op uw cache. De timing van de automatische upgrade kan niet worden geconfigureerd. Als u zich zorgen maakt over de invloed op de cache prestaties, moet u de software zelf upgraden voordat de periode verloopt.

Als uw cache wordt gestopt wanneer de eind datum wordt door gegeven, wordt de software bij de volgende keer dat deze wordt gestart automatisch bijgewerkt met de cache. (De update wordt mogelijk niet onmiddellijk gestart, maar wordt in het eerste uur gestart.)

### <a name="portal"></a>[Portal](#tab/azure-portal)

Klik op de knop **bijwerken** om de software-update te starten. De cache status verandert in **een upgrade** totdat de bewerking is voltooid.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

[Azure cli instellen voor Azure HPC-cache](./az-cli-prerequisites.md).

In azure CLI worden nieuwe software-informatie aan het einde van het cache status rapport opgenomen. (Gebruik [AZ HPC-cache show](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-show) om te controleren.) Zoek naar de teken reeks ' upgradeStatus ' in het bericht.

Gebruik [AZ HPC-cache upgrade-firmware](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-upgrade-firmware) om de update toe te passen, indien aanwezig.

Als er geen update beschikbaar is, heeft deze bewerking geen effect.

In dit voor beeld ziet u de status van de cache (er is geen update beschikbaar) en de resultaten van de opdracht upgrade-firmware.

```azurecli
$ az hpc-cache show --name doc-cache0629
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },

<...>

  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.61",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-06-29T22:18:32.004822+00:00",
    "pendingFirmwareVersion": null
  }
}
$ az hpc-cache upgrade-firmware --name doc-cache0629
$
```

---

## <a name="collect-diagnostics"></a>Diagnostische gegevens verzamelen

Met de knop **Diagnostische gegevens verzamelen** wordt het proces hand matig gestart om systeem gegevens te verzamelen en te uploaden naar de micro soft-service en ondersteuning voor probleem oplossing. Uw cache verzamelt en uploadt automatisch dezelfde diagnostische gegevens als er een ernstig probleem met de cache optreedt.

Gebruik dit besturings element als deze door micro soft service en support wordt aangevraagd.

Nadat u op de knop hebt geklikt, klikt u op **Ja** om de upload te bevestigen.

![scherm afbeelding van het pop-upbericht ' Diagnostische verzameling starten ' wordt bevestigd. De standaard knop Ja is gemarkeerd.](media/diagnostics-confirm.png)

## <a name="delete-the-cache"></a>De cache verwijderen

De knop **verwijderen** vernietigt de cache. Wanneer u een cache verwijdert, worden alle bijbehorende bronnen vernietigd en worden er geen account kosten meer in rekening gebracht.

De back-end-opslag volumes die als opslag doelen worden gebruikt, worden niet beïnvloed wanneer u de cache verwijdert. U kunt ze later toevoegen aan een toekomstige cache of ze afzonderlijk uit bedrijf nemen.

> [!NOTE]
> Azure HPC cache schrijft niet automatisch gewijzigde gegevens van de cache naar de back-end-opslag systemen voordat de cache wordt verwijderd.
>
> Om ervoor te zorgen dat alle gegevens in de cache naar lange termijn opslag zijn geschreven, moet u [de cache stoppen](#stop-the-cache) voordat u deze verwijdert. Zorg ervoor dat de status wordt weer **gegeven voordat deze** wordt verwijderd.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Nadat u de cache hebt gestopt, klikt u op de knop **verwijderen** om de cache permanent te verwijderen.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

[Azure cli instellen voor Azure HPC-cache](./az-cli-prerequisites.md).

Gebruik de Azure CLI [-opdracht AZ HPC-cache delete](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-delete) om de cache permanent te verwijderen.

Voorbeeld:
```azurecli
$ az hpc-cache delete --name doc-cache0629
 - Running ..

<...>

{- Finished ..
  "endTime": "2020-07-09T22:24:35.1605019+00:00",
  "name": "7d3cd0ba-11b3-4180-8298-d9cafc9f22c1",
  "startTime": "2020-07-09T22:13:32.0732892+00:00",
  "status": "Succeeded"
}
$
```

---

## <a name="cache-metrics-and-monitoring"></a>Metrische gegevens over cache en bewaking

Op de overzichts pagina worden grafieken weer gegeven voor een aantal basis cache-statistieken, bewerkingen per seconde en latentie.

![scherm afbeelding van drie lijn grafieken met de statistieken die hierboven worden vermeld voor een voorbeeld cache](media/hpc-cache-overview-stats.png)

Deze grafieken maken deel uit van de ingebouwde hulpprogram ma's voor bewaking en analyse van Azure. Aanvullende hulp middelen en waarschuwingen zijn beschikbaar op de pagina's onder de kop **bewaking** in de zijbalk van de portal. Meer informatie vindt u in de sectie Portal van de [Azure monitoring-documentatie](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-in-the-azure-portal).

## <a name="view-warnings"></a>Waarschuwingen weer geven

Als de cache een slechte status heeft, controleert u de pagina **waarschuwingen** . Op deze pagina worden meldingen van de cache software weer gegeven die u kunnen helpen de status ervan te begrijpen.

Deze meldingen worden niet weer gegeven in het activiteiten logboek, omdat ze niet worden beheerd door Azure Portal. Ze zijn vaak gekoppeld aan aangepaste instellingen die u mogelijk hebt gemaakt.

Soorten waarschuwingen die u hier kunt zien, zijn onder andere:

* De cache kan de NTP-server niet bereiken
* De cache kan de gebruikers naam van de uitgebreide groepen niet downloaden
* Aangepaste DNS-instellingen zijn gewijzigd op een opslag doel

![scherm afbeelding van de pagina bewakings > waarschuwingen met een bericht dat de gebruikers namen van uitgebreide groepen niet kunnen worden gedownload](media/warnings-page.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [hulpprogram ma's voor metrische gegevens en statistieken van Azure](../azure-monitor/index.yml)
* [Hulp krijgen bij uw Azure HPC-cache](hpc-cache-support-ticket.md)
