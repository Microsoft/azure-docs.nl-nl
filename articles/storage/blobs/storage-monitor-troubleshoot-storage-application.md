---
title: Een toepassing voor cloudopslag bewaken en problemen oplossen in Azure | Microsoft Docs
description: Gebruik diagnostische hulpprogramma's, metrische gegevens en waarschuwingen voor het oplossen van problemen met een cloudtoepassing en deze te bewaken.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 07/20/2018
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 6c1d9b813db15900521968865c1036d53ad53f41
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95544782"
---
# <a name="monitor-and-troubleshoot-a-cloud-storage-application"></a>Een toepassing voor cloudopslag bewaken en problemen oplossen

Deze zelfstudie is deel vier en het laatste deel van een serie. U leert hoe u een toepassing voor cloudopslag kunt bewaken en problemen ermee kunt oplossen.

In deel vier van de serie leert u het volgende:

> [!div class="checklist"]
> * Logboekregistratie en metrische gegevens inschakelen
> * Waarschuwingen voor autorisatiefouten inschakelen
> * Testverkeer met onjuiste SAS-tokens uitvoeren
> * Logboeken downloaden en analyseren

[Azure Storage Analytics](../common/storage-analytics.md) biedt logboekregistratie en metrische gegevens voor een opslagaccount. Deze gegevens bieden inzicht in de status van uw opslagaccount. U kunt logboekregistratie, metrische gegevens en waarschuwingen configureren om gegevens uit Azure-opslaganalyse te verzamelen. Dit proces omvat het inschakelen van logboekregistratie, het configureren van metrische gegevens en het inschakelen van waarschuwingen.

Logboekregistratie en metrische gegevens van opslagaccounts worden ingeschakeld op het tabblad **Diagnostische gegevens** in Azure-portal. Met logboekregistratie voor opslag kunt u details vastleggen voor zowel geslaagde als mislukte aanvragen in uw opslagaccount. Deze logboeken stellen u in staat details te zien over lees-, schrijf en verwijderbewerkingen met betrekking tot uw Azure-tabellen, -wachtrijen en -blobs. Ze maken het ook mogelijk om te zien wat de oorzaken zijn van mislukte aanvragen zoals time-outs, aanvraagbeperkingen en autorisatiefouten.

## <a name="log-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com).

## <a name="turn-on-logging-and-metrics"></a>Logboekregistratie en metrische gegevens inschakelen

Selecteer in het menu links **Resourcegroepen**, selecteer **myResourceGroup** en selecteer vervolgens uw opslagaccount in de lijst met resources.

Stel onder **Diagnostische instellingen (klassiek)** **Status** in op **Aan**. Zorg ervoor dat alle opties onder **Blob-eigenschappen** zijn ingeschakeld.

Na het voltooien klikt u op **Opslaan**.

![Schermopname waarin de sectie is gemarkeerd die de configuratie-instellingen bevat voor het inschakelen van logboekregistratie en metrische gegevens.](media/storage-monitor-troubleshoot-storage-application/enable-diagnostics.png)

## <a name="enable-alerts"></a>Waarschuwingen inschakelen

Waarschuwingen bieden een manier waarbij beheerders e-mails ontvangen of dat er een webhook wordt geactiveerd als een metrisch gegeven een drempelwaarde overschrijdt. In dit voorbeeld, gaat u een waarschuwing voor het metrische gegeven `SASClientOtherError` inschakelen.

### <a name="navigate-to-the-storage-account-in-the-azure-portal"></a>Navigeren naar het opslagaccount in Azure Portal

Selecteer onder het gedeelte **Bewaking** de optie **Waarschuwingen (klassiek)** .

Selecteer **Waarschuwing voor metrische gegevens toevoegen (klassiek)** en voltooi het formulier **Regel toevoegen** door de vereiste gegevens in te vullen. In de vervolgkeuzelijst **Metrisch** selecteert u `SASClientOtherError`. Als u wilt toestaan dat de waarschuwing bij de eerste fout wordt geactiveerd, selecteert u in de vervolgkeuzelijst **Voorwaarde** **Groter dan of gelijk aan**.

![Het deelvenster Diagnostische gegevens](media/storage-monitor-troubleshoot-storage-application/add-alert-rule.png)

## <a name="simulate-an-error"></a>Een fout simuleren

Als u een geldige waarschuwing wilt simuleren, kunt u proberen om een niet-bestaande blob uit uw opslagaccount aan te vragen. De volgende opdracht vereist een naam voor de opslagcontainer. U kunt de naam van een bestaande container gebruiken of een nieuwe maken voor dit voorbeeld.

Vervang de tijdelijke aanduidingen door echte waarden (zorg ervoor dat `<INCORRECT_BLOB_NAME>` is ingesteld op een waarde die niet bestaat) en voer de opdracht uit.

```azurecli-interactive
sasToken=$(az storage blob generate-sas \
    --account-name <STORAGE_ACCOUNT_NAME> \
    --account-key <STORAGE_ACCOUNT_KEY> \
    --container-name <CONTAINER_NAME> \
    --name <INCORRECT_BLOB_NAME> \
    --permissions r \
    --expiry `date --date="next day" +%Y-%m-%d`)

curl https://<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/<CONTAINER_NAME>/<INCORRECT_BLOB_NAME>?$sasToken
```

In de volgende afbeelding ziet u een voorbeeldwaarschuwing die is gebaseerd op de gesimuleerde mislukte aanvraag die in het vorige voorbeeld is uitgevoerd.

 ![Voorbeeldwaarschuwing](media/storage-monitor-troubleshoot-storage-application/email-alert.png)

## <a name="download-and-view-logs"></a>Logboeken downloaden en weergeven

In opslaglogboeken worden gegevens vastgelegd in een reeks blobs in een blobcontainer met de naam **$logs** in uw opslagaccount. Deze container wordt niet weergegeven als u alle blobcontainers in uw account weergeeft, maar u kunt de inhoud ervan zien als u de container rechtstreeks opent.

In dit scenario gaat u [Microsoft Message Analyzer](/message-analyzer/microsoft-message-analyzer-operating-guide) gebruiken om te communiceren met uw Azure-opslagaccount.

### <a name="download-microsoft-message-analyzer"></a>Microsoft Message Analyzer downloaden

Download [Microsoft Message Analyzer](/message-analyzer/installing-and-upgrading-message-analyzer) en installeer de toepassing.

Start de toepassing en kies **Bestand** > **Openen** > **Vanuit andere bestandsbronnen**.

Selecteer in het dialoogvenster **Bestandsselector** de optie **+ Azure-verbinding toevoegen**. Voer uw **opslagaccountnaam** en **accountsleutel** in en klik op **OK**.

![Microsoft Message Analyzer - dialoogvenster Azure-opslagverbinding toevoegen](media/storage-monitor-troubleshoot-storage-application/figure3.png)

Nadat u verbonden bent, vouwt u de containers in de structuurweergave van de opslag uit om de blobs in de logboeken te kunnen zien. Selecteer het meest recente logboek en klik op **OK**.

![Schermopname van Microsoft Message Analyzer, waarin het geselecteerde logboekbestand is geselecteerd.](media/storage-monitor-troubleshoot-storage-application/figure4.png)

In het dialoogvenster **Nieuwe sessie** klikt u op **Start** om het logboek weer te geven.

Zodra het logboek is geopend, kunt u de opslaggebeurtenissen bekijken. Zoals u in de volgende afbeelding kunt zien, is er een `SASClientOtherError` geactiveerd op het opslagaccount. Voor meer informatie over logboekregistratie van opslag, gaat u naar [Opslaganalyse](../common/storage-analytics.md).

![Microsoft Message Analyzer - gebeurtenissen bekijken](media/storage-monitor-troubleshoot-storage-application/figure5.png)

[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) is een ander hulpmiddel dat kan worden gebruikt om te communiceren met uw opslagaccount, met inbegrip van de container **$logs** en de logboeken die er zich in bevinden.

## <a name="next-steps"></a>Volgende stappen

In het vierde en laatste deel van de serie, hebt u geleerd hoe u uw opslagaccount kunt controleren en hoe u problemen ermee kunt oplossen, zodat u het volgende kunt doen:

> [!div class="checklist"]
> * Logboekregistratie en metrische gegevens inschakelen
> * Waarschuwingen voor autorisatiefouten inschakelen
> * Testverkeer met onjuiste SAS-tokens uitvoeren
> * Logboeken downloaden en analyseren

Volg deze link om vooraf samengestelde opslagvoorbeelden te bekijken.

> [!div class="nextstepaction"]
> [Voorbeelden van Azure Storage-scripts](storage-samples-blobs-cli.md)