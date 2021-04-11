---
title: Problemen met Azure Monitor Application Insights voor Java oplossen
description: Meer informatie over het oplossen van problemen met de Java-Agent voor Azure Monitor Application Insights
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: 9bcd0ead2516b040a5a5aee4a7fae042a5f678a2
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449984"
---
# <a name="troubleshooting-guide-azure-monitor-application-insights-for-java"></a>Gids voor probleem oplossing: Azure Monitor Application Insights voor Java

In dit artikel worden enkele veelvoorkomende problemen besproken die u kunt tegen komen tijdens het instrumenteren van een Java-toepassing met behulp van de Java-Agent voor Application Insights. We behandelen ook de stappen om deze problemen op te lossen. Application Insights is een functie van de Azure Monitor platform-service.

## <a name="check-the-self-diagnostic-log-file"></a>Het logboek bestand voor automatische diagnose controleren

De Java 3,0-agent voor Application Insights produceert standaard een logboek bestand met `applicationinsights.log` de naam in dezelfde map waarin het bestand zich bevindt `applicationinsights-agent-3.0.3.jar` .

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

Logboek registratie wordt alleen vastgelegd als het eerst voldoet aan het niveau dat is geconfigureerd voor het logboek registratie raamwerk en de tweede, ook voldoet aan het niveau dat is geconfigureerd voor Application Insights.

Als uw Framework voor logboek registratie bijvoorbeeld is ingesteld op logboek `WARN` (en hoger) van het pakket `com.example` , en Application Insights is geconfigureerd voor het vastleggen `INFO` van (en hoger), wordt door Application Insights alleen het `WARN` pakket vastgelegd (en hierboven) `com.example` .

De beste manier om te bepalen of een bepaalde logboek registratie-instructie voldoet aan de geconfigureerde drempel waarde voor registratie raamwerken is om te bevestigen dat deze wordt weer gegeven in uw normale toepassings logboek (bijvoorbeeld bestand of console).

Houd er ook rekening mee dat als een uitzonderings object wordt door gegeven aan de logboek registratie, het logboek bericht (en Details van uitzonderings object) wordt weer gegeven in de Azure Portal onder de `exceptions` tabel in plaats van de `traces` tabel.

Zie de [configuratie voor automatisch verzamelde logboek registratie](./java-standalone-config.md#auto-collected-logging) voor meer informatie.

## <a name="import-ssl-certificates"></a>SSL-certificaten importeren

Deze sectie helpt u bij het oplossen van problemen met de uitzonde ringen met betrekking tot SSL-certificaten bij gebruik van de Java-Agent.

Er zijn twee verschillende onderstaande paden voor het oplossen van dit probleem:
* Als u een standaard Java-opslag archief gebruikt
* Als u een aangepast Java-opslag archief gebruikt

Als u niet zeker weet welk pad u moet volgen, controleert u of u een JVM ARG hebt `-Djavax.net.ssl.trustStore=...` .
Als u _geen_ dergelijk JVMe ARG hebt, gebruikt u waarschijnlijk de standaard Java-opslag.
Als u een dergelijk JVMe _ARG hebt, gebruikt u waarschijnlijk_ een aangepaste opslag voor de JVM en gaat u naar uw aangepaste opslag locatie.

### <a name="if-using-the-default-java-keystore"></a>Als u het standaard Java-opslag archief gebruikt:

Normaal gesp roken bevat het standaard Java-opslag archief al alle CA-basis certificaten. Er kunnen echter enkele uitzonde ringen zijn, zoals het inslikken van het eind punt certificaat kan worden ondertekend door een ander basis certificaat. We raden u daarom aan de volgende drie stappen uit te voeren om dit probleem op te lossen:

1.  Controleer of het basis certificaat dat is gebruikt voor het ondertekenen van het Application Insights-eind punt al aanwezig is in de standaard-opslag. De vertrouwde CA-certificaten worden standaard opgeslagen in `$JAVA_HOME/jre/lib/security/cacerts` . Als u certificaten in een Java-opslag archief wilt weer geven, gebruikt u de volgende opdracht:
    > `keytool -list -v -keystore $PATH_TO_KEYSTORE_FILE`
 
    U kunt de uitvoer omleiden naar een tijdelijk bestand, zoals dit (eenvoudig te doorzoeken)
    > `keytool -list -v -keystore $JAVA_HOME/jre/lib/security/cacerts > temp.txt`

2. Wanneer u de lijst met certificaten hebt, volgt u deze [stappen](#steps-to-download-ssl-certificate) om het basis certificaat te downloaden dat is gebruikt voor het ondertekenen van het Application Insights-eind punt.

    Nadat u het certificaat hebt gedownload, genereert u een SHA-1-hash op het certificaat met behulp van de onderstaande opdracht:
    > `keytool -printcert -v -file "your_downloaded_root_certificate.cer"`
 
    Kopieer de SHA-1-waarde en controleer of deze waarde aanwezig is in het bestand ' temp.txt ' dat u eerder hebt opgeslagen.  Als u de SHA-1-waarde in het tijdelijke bestand niet kunt vinden, geeft dit aan dat het gedownloade basis certificaat ontbreekt in de standaard Java-opslag.


3. Importeer het basis certificaat naar het standaard-Java-opslag archief met behulp van de volgende opdracht:
    >   `keytool -import -file "the cert file" -alias "some meaningful name" -keystore "path to cacerts file"`
 
    In dit geval wordt het
 
    > `keytool -import -file "your downloaded root cert file" -alias "some meaningful name" $JAVA_HOME/jre/lib/security/cacerts`


### <a name="if-using-a-custom-java-keystore"></a>Als u een aangepast Java-opslag archief gebruikt:

Als u een aangepaste Java-opslag groep gebruikt, moet u mogelijk de Application Insights een of meer basis-SSL-certificaten importeren.
We raden u aan de volgende twee stappen uit te voeren om dit probleem op te lossen:
1. Volg deze [stappen](#steps-to-download-ssl-certificate) om het basis certificaat van het Application Insights-eind punt te downloaden.
2. Gebruik de volgende opdracht om het basis-SSL-certificaat te importeren in het aangepaste Java-hoofd archief:
    > `keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name.cer" -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-ssl-certificate"></a>Stappen voor het downloaden van het SSL-certificaat

1.  Open uw favoriete browser en ga naar de `IngestionEndpoint` URL die wordt weer gegeven in de Connection String die wordt gebruikt om uw toepassing te instrumenteren.

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-snippet.png" alt-text="Scherm opname waarin een Application Insights connection string wordt weer gegeven." lightbox="media/java-ipa/troubleshooting/ingestion-endpoint-snippet.png":::

2.  Selecteer het pictogram **site-informatie weer geven** in de browser en selecteer vervolgens de optie **certificaat** .

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="Scherm afbeelding van de optie certificaat in site gegevens." lightbox="media/java-ipa/troubleshooting/certificate-icon-capture.png":::

3.  In plaats van het Leaf-certificaat te downloaden, moet u het basis certificaat downloaden, zoals hieronder wordt weer gegeven. Later moet u op het certificeringspad klikken-> Selecteer het basis certificaat-> Klik op certificaat weer geven. Hiermee wordt een nieuw certificaat menu weer gegeven en kunt u het certificaat downloaden in het nieuwe menu.

    :::image type="content" source="media/java-ipa/troubleshooting/root-certificate-selection.png" alt-text="Scherm opname van het selecteren van het basis certificaat." lightbox="media/java-ipa/troubleshooting/root-certificate-selection.png":::

4.  Ga naar het tabblad **Details** en selecteer **kopiëren naar bestand**.
5.  Selecteer de knop **volgende** , selecteer **Base-64 Encoded X. 509 (. CER)** en selecteer vervolgens opnieuw **volgende** .

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="Scherm opname van de wizard Certificaat exporteren, met een geselecteerde indeling." lightbox="media/java-ipa/troubleshooting/certificate-export-wizard.png":::

6.  Geef het bestand op waarin u het SSL-certificaat wilt opslaan. Selecteer vervolgens **volgende**  >  **volt ooien**. Het bericht ' het exporteren is voltooid ' wordt weer gegeven.

> [!WARNING]
> U moet deze stappen herhalen om het nieuwe certificaat op te halen voordat het huidige certificaat verloopt. Op het tabblad **Details** van het dialoog venster **certificaat** vindt u informatie over de verval datum.
>
> :::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="Scherm opname van de details van het SSL-certificaat." lightbox="media/java-ipa/troubleshooting/certificate-details.png":::
