---
title: Problemen met Azure Monitor Application Insights voor Java oplossen
description: Meer informatie over het oplossen van problemen met de Java-Agent voor Azure Monitor Application Insights
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: 2876abd3749c9e56cef462e41b8268135f82cd12
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98232213"
---
# <a name="troubleshooting-guide-azure-monitor-application-insights-for-java"></a>Gids voor probleem oplossing: Azure Monitor Application Insights voor Java

In dit artikel worden enkele veelvoorkomende problemen besproken die u kunt tegen komen tijdens het instrumenteren van een Java-toepassing met behulp van de Java-Agent voor Application Insights. We behandelen ook de stappen om deze problemen op te lossen. Application Insights is een functie van de Azure Monitor platform-service.

## <a name="check-the-self-diagnostic-log-file"></a>Het logboek bestand voor automatische diagnose controleren

De Java 3,0-agent voor Application Insights produceert standaard een logboek bestand met `applicationinsights.log` de naam in dezelfde map waarin het bestand zich bevindt `applicationinsights-agent-3.0.1.jar` .

Dit logboek bestand is de eerste plaats om te controleren of er hints zijn voor eventuele problemen die zich voordoen.

## <a name="jvm-fails-to-start"></a>JVM kan niet worden gestart

Als de JVM niet kan worden gestart met ' fout bij openen van zip-bestand of JAR-manifest ontbreekt ', moet u het jar-bestand van de agent opnieuw downloaden omdat het mogelijk tijdens de bestands overdracht is beschadigd.

## <a name="upgrade-from-the-application-insights-java-2x-sdk"></a>Upgrade van de Application Insights Java 2. x SDK

Als u de Application Insights Java 2. x-SDK al gebruikt in uw toepassing, kunt u deze blijven gebruiken. De Java 3,0-agent detecteert deze. Zie [upgrade uitvoeren van de Java 2. x SDK](./java-standalone-upgrade-from-2x.md)voor meer informatie.

## <a name="upgrade-from-application-insights-java-30-preview"></a>Upgrade van Application Insights Java 3,0 Preview

Als u een upgrade uitvoert van de Java 3,0 Preview-agent, controleert u alle [configuratie opties](./java-standalone-config.md) zorgvuldig. De JSON-structuur is volledig gewijzigd in de 3,0-release voor algemene Beschik baarheid (GA).

Deze wijzigingen zijn onder andere:

-  De naam van het configuratie bestand is gewijzigd van `ApplicationInsights.json` in `applicationinsights.json` .
-  Het `instrumentationSettings` knoop punt is niet meer aanwezig. Alle inhoud in `instrumentationSettings` wordt verplaatst naar het hoofd niveau. 
-  Configuratie knooppunten zoals `sampling` , `jmxMetrics` , `instrumentation` en `heartbeat` worden `preview` naar het hoofd niveau verplaatst.

## <a name="some-logging-is-not-auto-collected"></a>Een deel van de logboek registratie wordt niet automatisch verzameld

Logboek registratie wordt alleen vastgelegd als het eerst voldoet aan de geconfigureerde drempel waarde voor logboek registratie raamwerken en de tweede ook voldoet aan de Application Insights geconfigureerde drempel waarde.

De beste manier om te bepalen of een bepaalde logboek registratie-instructie voldoet aan de geconfigureerde drempel waarde voor registratie raamwerken is om te bevestigen dat deze wordt weer gegeven in uw normale toepassings logboek (bijvoorbeeld bestand of console).

Zie de [configuratie voor automatisch verzamelde logboek registratie](./java-standalone-config.md#auto-collected-logging) voor meer informatie.

## <a name="import-ssl-certificates"></a>SSL-certificaten importeren

Als u de standaard Java-opslag groep gebruikt, heeft deze al alle CA-basis certificaten. U hoeft niet meer SSL-certificaten te importeren.

Als u een aangepaste Java-opslag groep gebruikt, moet u mogelijk de Application Insights endpoint SSL-certificaten importeren.

### <a name="key-terminology"></a>Belangrijkste terminologie
Een sleutel *Archief* is een opslag plaats van certificaten, open bare sleutels en persoonlijke sleutels. Doorgaans hebben distributies van Java Development Kit een uitvoerbaar bestand om ze te beheren: `keytool` .

Het volgende voor beeld is een eenvoudige opdracht voor het importeren van een SSL-certificaat in de-opslag:

`keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name".cer -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-and-add-an-ssl-certificate"></a>Stappen om een SSL-certificaat te downloaden en toe te voegen

1.  Open uw favoriete browser en ga naar de `IngestionEndpoint` URL die wordt weer gegeven in de Connection String die wordt gebruikt om uw toepassing te instrumenteren.

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-url.png" alt-text="Scherm opname waarin een Application Insights connection string wordt weer gegeven.":::

2.  Selecteer het pictogram **site-informatie weer geven** in de browser en selecteer vervolgens de optie **certificaat** .

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="Scherm afbeelding van de optie certificaat in site gegevens.":::

3.  Ga naar het tabblad **Details** en selecteer **kopiëren naar bestand**.
4.  Selecteer de knop **volgende** , selecteer **Base-64 Encoded X. 509 (. CER)** en selecteer vervolgens opnieuw **volgende** .

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="Scherm opname van de wizard Certificaat exporteren, met een geselecteerde indeling.":::

5.  Geef het bestand op waarin u het SSL-certificaat wilt opslaan. Selecteer vervolgens **volgende**  >  **volt ooien**. Het bericht ' het exporteren is voltooid ' wordt weer gegeven.
6.  Nadat u het certificaat hebt, is het tijd om het certificaat te importeren in een Java-archief. Gebruik de [voor gaande opdracht](#key-terminology) om certificaten te importeren.

> [!WARNING]
> U moet deze stappen herhalen om het nieuwe certificaat op te halen voordat het huidige certificaat verloopt. Op het tabblad **Details** van het dialoog venster **certificaat** vindt u informatie over de verval datum.
>
> :::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="Scherm opname van de details van het SSL-certificaat.":::
