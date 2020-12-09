---
title: Problemen oplossen-Azure Monitor Application Insights java
description: Problemen met Azure Monitor Application Insights Java oplossen
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: cf27763f857cc1fd1aad5256d0c6cecf91251caf
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855618"
---
# <a name="troubleshooting-azure-monitor-application-insights-java"></a>Problemen met Azure Monitor Application Insights Java oplossen

In dit artikel hebben we enkele veelvoorkomende problemen besproken die een gebruiker kan belicht tijdens het instrumenteren van een Java-toepassing met behulp van de Java-Agent, samen met de stappen om deze problemen op te lossen.

## <a name="self-diagnostic-log-file"></a>Logboek bestand voor automatische diagnose

Application Insights Java 3,0 resulteert standaard in een logboek bestand `applicationinsights.log` met de naam in dezelfde map waarin het `applicationinsights-agent-3.0.0.jar` bestand zich bevindt.

Dit logboek bestand is de eerste plaats om te controleren of er hints zijn voor eventuele problemen die zich voordoen.

## <a name="upgrade-from-application-insights-java-2x-sdk"></a>Upgrade van Application Insights Java 2. x SDK

Zie [upgrade van 2. x SDK](./java-standalone-upgrade-from-2x.md).

## <a name="upgrade-from-30-preview"></a>Upgrade van 3,0 Preview

Als u een upgrade uitvoert van de preview-versie van 3,0, moet u alle [configuratie opties](./java-standalone-config.md) zorgvuldig controleren, omdat de JSON-structuur volledig is gewijzigd in de 3,0 ga-release.

Deze wijzigingen zijn onder andere:

1.  De naam van het configuratie bestand zelf is gewijzigd van `ApplicationInsights.json` in `applicationinsights.json` .
2.  Het `instrumentationSettings` knoop punt is niet meer aanwezig. Alle inhoud in `instrumentationSettings` wordt verplaatst naar het hoofd niveau. 
3.  Configuratie knooppunten zoals `sampling` , `jmxMetrics` , `instrumentation` en `heartbeat` worden verplaatst van `preview` naar het hoofd niveau.

## <a name="ssl-certificate-issues"></a>SSL-certificaat problemen

Als u de standaard Java-opslag locatie gebruikt, heeft deze al alle CA-basis certificaten en hoeft u geen verdere SSL-certificaten te importeren.

Als u een aangepaste Java-opslag groep gebruikt, moet u mogelijk de Application Insights endpoint SSL-certificaten importeren.

### <a name="some-key-terminology"></a>Een belang rijke terminologie:
Sleutel *Archief* is een opslag plaats van certificaten, open bare en persoonlijke sleutels. Doorgaans hebben JDK-distributies een uitvoerbaar bestand om ze te beheren `keytool` .

Het volgende voor beeld is een eenvoudige opdracht voor het importeren van een SSL-certificaat in de-opslag:

`keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name".cer -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-and-add-the-ssl-certificate"></a>Stappen om het SSL-certificaat te downloaden en toe te voegen:

1.  Open uw favoriete browser en ga naar de `IngestionEndpoint` URL die aanwezig is in de verbindings reeks die wordt gebruikt om uw toepassing te instrumenteren, zoals hieronder wordt weer gegeven

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-url.png" alt-text="Verbindings reeks Application Insights":::

2.  Klik op het pictogram site gegevens weer geven (vergren delen) op de browser en klik op certificaat optie, zoals hieronder weer gegeven

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="SSL-certificaat vastleggen":::

3.  Ga naar het tabblad Details en klik op kopiëren naar bestand.
4.  Klik op de knop Volgende en selecteer base-64 Encoded X. 509 (. CER) ' Format teren en selecteer volgende.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="SSL-certificaat ExportWizard":::

5.  Geef het bestand op waarin u het SSL-certificaat wilt opslaan. Klik ten slotte op volgende en volt ooien. Het bericht ' het exporteren is voltooid ' wordt weer gegeven.
6.  Zodra u het certificaat hebt, is het tijd om het certificaat te importeren in een Java-sleutel archief. Gebruik de bovenstaande [opdracht](#some-key-terminology) om certificaten te importeren.

> [!WARNING]
> U moet deze stappen herhalen om het nieuwe certificaat te krijgen voordat het huidige certificaat is verlopen. De informatie over de verval datum vindt u op het tabblad Details van de pop-up certificaat, zoals hieronder wordt weer gegeven

:::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="Details van SSL-certificaat":::
