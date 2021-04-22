---
title: Beheer en werk Azure HPC Cache
description: Informatie over het beheren en bijwerken Azure HPC Cache de Azure Portal of Azure CLI
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/08/2021
ms.author: v-erkel
ms.openlocfilehash: a831aa7b2f3b0d438d9db8fefa3d26428fea3680
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862593"
---
# <a name="manage-your-cache"></a>Uw cache beheren

De overzichtspagina van de cache in Azure Portal projectdetails, cachestatus en basisstatistieken voor uw cache. Het bevat ook besturingselementen voor het stoppen of starten van de cache, het verwijderen van de cache, het leegmaken van gegevens naar langetermijnopslag en het bijwerken van software.

In dit artikel wordt ook uitgelegd hoe u deze basistaken kunt uitvoeren met de Azure CLI.

Als u de overzichtspagina wilt openen, selecteert u uw cacheresource in Azure Portal. Laad bijvoorbeeld de pagina **Alle resources** en klik op de naam van de cache.

![schermopname van Azure HPC Cache overzichtspagina van een exemplaar](media/hpc-cache-overview.png)

Met de knoppen bovenaan de pagina kunt u de cache beheren:

* **Starten** en [**stoppen:**](#stop-the-cache) hervat of onderhoudt de cachebewerking
* [**Leeg maken:**](#flush-cached-data) schrijft gewijzigde gegevens naar opslagdoelen
* [**Upgrade**](#upgrade-cache-software) uitvoeren: werkt de cachesoftware bij
* [**Diagnostische gegevens verzamelen**](#collect-diagnostics) - Uploadt informatie over debuggen
* **Vernieuwen:** de overzichtspagina wordt opnieuw geladen
* [**Verwijderen:**](#delete-the-cache) de cache permanent vernietigen

Meer informatie over deze opties vindt u hieronder.

Klik op de onderstaande afbeelding om een [video te bekijken](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) waarin cachebeheertaken worden gedemonstreerd.

[![videominiature: Azure HPC Cache: Beheren (klik om naar de videopagina te gaan)](media/video-5-manage.png)](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)

## <a name="stop-the-cache"></a>De cache stoppen

U kunt de cache stoppen om de kosten te verlagen tijdens een inactieve periode. Er worden geen kosten in rekening gebracht voor de uptime terwijl de cache is gestopt, maar er worden wel kosten in rekening gebracht voor de toegewezen schijfopslag van de cache. (Zie de [pagina met prijzen](https://aka.ms/hpc-cache-pricing) voor meer informatie.)

Een gestopte cache reageert niet op clientaanvragen. U moet clients ontkoppelen voordat u de cache stopt.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Met **de knop Stoppen** wordt een actieve cache opgeschort. De **knop Stoppen** is beschikbaar wanneer de status van een cache In **orde** of **Gedegradeerd is.**

![schermopname van de bovenste knoppen met Stoppen gemarkeerd en een pop-upbericht met een beschrijving van de stopactie en de vraag 'Wilt u doorgaan?' met de knoppen Ja (standaard) en Geen](media/stop-cache.png)

Nadat u op Ja hebt geklikt om te bevestigen dat de cache wordt gestopt, wordt de inhoud van de cache automatisch leeg naar de opslagdoelen. Dit proces kan enige tijd duren, maar zorgt voor gegevensconsistentie. Ten slotte wordt de cachestatus gewijzigd in **Gestopt.**

Als u een gestopte cache opnieuw wilt activeren, klikt u op de **knop** Start. Er is geen bevestiging nodig.

![schermopname van de bovenste knoppen met Start gemarkeerd](media/start-cache.png)

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

[Azure CLI instellen voor Azure HPC Cache.](./az-cli-prerequisites.md)

Een cache tijdelijk opschorten met de [opdracht az hpc-cache stop.](/cli/azure/hpc-cache#az_hpc_cache_stop) Deze actie is alleen geldig wanneer de status van een cache **In orde** of **Gedegradeerd is.**

De inhoud van de cache wordt automatisch naar de opslagdoelen leeggetrokken voordat deze wordt gestopt. Dit proces kan enige tijd duren, maar zorgt voor gegevensconsistentie.

Wanneer de actie is voltooid, verandert de cachestatus in **Gestopt.**

Een gestopte cache opnieuw activeren met [az hpc-cache start](/cli/azure/hpc-cache#az_hpc_cache_start).

Wanneer u de opdracht start of stop geeft, wordt op de opdrachtregel het statusbericht 'Wordt uitgevoerd' weergegeven totdat de bewerking is voltooid.

```azurecli
$ az hpc-cache start --name doc-cache0629
 - Running ..
```

Na voltooiing wordt het bericht bijgewerkt naar Voltooid en worden retourcodes en andere informatie weergegeven.

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

## <a name="flush-cached-data"></a>Gegevens in cache leegmaken

De **knop Leegmaken** op de overzichtspagina laat de cache weten dat alle gewijzigde gegevens die in de cache zijn opgeslagen, direct naar de back-endopslagdoelen moeten worden geschreven. De cache slaat regelmatig gegevens op in de opslagdoelen, dus het is niet nodig om dit handmatig te doen, tenzij u ervoor wilt zorgen dat het back-endopslagsysteem up-to-date is. U kunt bijvoorbeeld Flush gebruiken voordat **u een** opslagmomentopname maakt of de grootte van de gegevensset controleert.

> [!NOTE]
> Tijdens het leegmaken kan de cache geen clientaanvragen verwerken. De toegang tot de cache wordt tijdelijk opgeschort en hervat nadat de bewerking is uitgevoerd.

Wanneer u de bewerking voor het leegmaken van de cache start, stopt de cache met het accepteren van clientaanvragen en wordt de cachestatus op de overzichtspagina gewijzigd in **Leegmaken.**

Gegevens in de cache worden opgeslagen in de juiste opslagdoelen. Afhankelijk van hoeveel gegevens er moeten worden leeggepokt, kan het proces enkele minuten of langer dan een uur duren.

Nadat alle gegevens zijn opgeslagen in opslagdoelen, begint de cache automatisch opnieuw clientaanvragen te verwerken. De cachestatus wordt weer In **orde.**

### <a name="portal"></a>[Portal](#tab/azure-portal)

Als u de cache wilt leegmaken, klikt u op **de knop Leegmaken** en klikt u vervolgens op **Ja** om de actie te bevestigen.

![schermopname van de bovenste knoppen met Leeg maken gemarkeerd en een pop-upbericht met een beschrijving van de actie leeg maken en de vraag 'wilt u doorgaan?' met de knoppen Ja (standaard) en Nee](media/hpc-cache-flush.png)

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

[Azure CLI instellen voor Azure HPC Cache.](./az-cli-prerequisites.md)

Gebruik [az hpc-cache flush om](/cli/azure/hpc-cache#az_hpc_cache_flush) af te dwingen dat de cache alle gewijzigde gegevens naar de opslagdoelen schrijft.

Voorbeeld:

```azurecli
$ az hpc-cache flush --name doc-cache0629 --resource-group doc-rg
 - Running ..
```

Wanneer het leeg maken is, wordt een succesbericht geretourneerd.

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

## <a name="upgrade-cache-software"></a>Cachesoftware upgraden

Als er een nieuwe softwareversie beschikbaar is, wordt **de knop Upgrade** actief. Boven aan de pagina ziet u ook een bericht over het bijwerken van software.

![schermopname van de bovenste rij met knoppen met de knop Upgrade ingeschakeld](media/hpc-cache-upgrade-button.png)

Clienttoegang wordt niet onderbroken tijdens een software-upgrade, maar de cacheprestaties nemen af. Plan de upgrade van software tijdens niet-piekuren of tijdens een geplande onderhoudsperiode.

De software-update kan enkele uren duren. Het duurt langer om caches te upgraden die zijn geconfigureerd met een hogere doorvoer dan caches met lagere piekdoorvoerwaarden.

Wanneer een software-upgrade beschikbaar is, hebt u een week of zo om deze handmatig toe te passen. De einddatum wordt vermeld in het upgradebericht. Als u gedurende die tijd geen upgrade hebt uitgevoerd, wordt de update automatisch toegepast op uw cache. De timing van de automatische upgrade kan niet worden geconfigureerd. Als u zich zorgen maakt over de invloed op de prestaties van de cache, moet u de software zelf bijwerken voordat de periode verloopt.

Als uw cache wordt gestopt wanneer de einddatum is voorbij, wordt de software automatisch bijgewerkt wanneer deze de volgende keer wordt gestart. (De update start mogelijk niet onmiddellijk, maar begint wel in het eerste uur.)

### <a name="portal"></a>[Portal](#tab/azure-portal)

Klik op **de knop Upgrade** om de software-update te starten. De cachestatus wordt gewijzigd in **Upgraden** totdat de bewerking is voltooid.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

[Azure CLI instellen voor Azure HPC Cache.](./az-cli-prerequisites.md)

In de Azure CLI wordt nieuwe software-informatie opgenomen aan het einde van het rapport over de cachestatus. (Gebruik [az hpc-cache show om dit](/cli/azure/hpc-cache#az_hpc_cache_show) te controleren.) Zoek de tekenreeks upgradeStatus in het bericht.

Gebruik [az hpc-cache upgrade-firmware om](/cli/azure/hpc-cache#az_hpc_cache_upgrade-firmware) de update toe te passen, indien aanwezig.

Als er geen update beschikbaar is, heeft deze bewerking geen effect.

In dit voorbeeld ziet u de cachestatus (er is geen update beschikbaar) en de resultaten van de opdracht upgrade-firmware.

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

Met **de knop Diagnostische gegevens** verzamelen wordt het proces voor het verzamelen van systeemgegevens handmatig gestart en geüpload naar microsoft-service en -ondersteuning voor probleemoplossing. In uw cache worden automatisch dezelfde diagnostische gegevens verzameld en geüpload als er een ernstig cacheprobleem optreedt.

Gebruik dit besturingselement als De service en ondersteuning van Microsoft hier om vragen.

Nadat u op de knop hebt geklikt, **klikt u op Ja** om het uploaden te bevestigen.

![schermopname van het pop-upbevestigingsbericht 'Diagnostische verzameling starten'. De standaardknop Ja is gemarkeerd.](media/diagnostics-confirm.png)

## <a name="delete-the-cache"></a>De cache verwijderen

Met **de knop** Verwijderen wordt de cache vernietigd. Wanneer u een cache verwijdert, worden alle resources vernietigd en worden er geen accountkosten meer in rekening gebracht.

De back-endopslagvolumes die als opslagdoelen worden gebruikt, worden niet beïnvloed wanneer u de cache verwijdert. U kunt ze later toevoegen aan een toekomstige cache of ze afzonderlijk buiten gebruik stellen.

> [!NOTE]
> Azure HPC Cache schrijft niet automatisch gewijzigde gegevens van de cache naar de back-endopslagsystemen voordat de cache wordt verwijderd.
>
> Om ervoor te zorgen dat alle gegevens in de cache naar langetermijnopslag zijn geschreven, moet u de [cache](#stop-the-cache) stoppen voordat u deze verwijdert. Zorg ervoor dat de status Gestopt **vóór** het verwijderen wordt weergeven.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Nadat u de cache hebt gestopt, klikt u **op de knop Verwijderen** om de cache permanent te verwijderen.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

[Azure CLI instellen voor Azure HPC Cache.](./az-cli-prerequisites.md)

Gebruik de Azure CLI-opdracht [az hpc-cache delete om](/cli/azure/hpc-cache#az_hpc_cache_delete) de cache permanent te verwijderen.

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

## <a name="cache-metrics-and-monitoring"></a>Metrische gegevens en bewaking in cache

Op de overzichtspagina ziet u grafieken voor enkele eenvoudige cachestatistieken: cachedoorvoer, bewerkingen per seconde en latentie.

![schermopname van drie lijndiagrammen met de hierboven genoemde statistieken voor een voorbeeldcache](media/hpc-cache-overview-stats.png)

Deze grafieken maken deel uit van de ingebouwde hulpprogramma's voor bewaking en analyse van Azure. Aanvullende hulpprogramma's en waarschuwingen zijn beschikbaar op de pagina's onder de **kop Bewaking** in de zijbalk van de portal. Meer informatie in de portalsectie van de [Azure Monitoring-documentatie](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-in-the-azure-portal).

## <a name="view-warnings"></a>Waarschuwingen weergeven

Als de cache een slechte status heeft, controleert u de **pagina** Waarschuwingen. Op deze pagina worden meldingen van de cachesoftware weergegeven die u kunnen helpen de status ervan te begrijpen.

Deze meldingen worden niet weergegeven in het activiteitenlogboek omdat ze niet worden beheerd door Azure Portal. Ze worden vaak gekoppeld aan aangepaste instellingen die u mogelijk hebt gemaakt.

Mogelijke soorten waarschuwingen die u hier ziet, zijn:

* De cache kan de NTP-server niet bereiken
* De cache kan gebruikersnaamgegevens van uitgebreide groepen niet downloaden
* Aangepaste DNS-instellingen zijn gewijzigd op een opslagdoel

![schermopname van de pagina Waarschuwingen > bewakingsgroep met een bericht dat gebruikersnamen voor uitgebreide groepen niet kunnen worden gedownload](media/warnings-page.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [hulpprogramma's voor metrische gegevens en statistieken van Azure](../azure-monitor/index.yml)
* Hulp [krijgen bij uw Azure HPC Cache](hpc-cache-support-ticket.md)
