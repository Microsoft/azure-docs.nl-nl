---
title: De Azure Cosmos DB Emulator-certificaten exporteren
description: Meer informatie over het exporteren van het Azure Cosmos DB-emulator certificaat voor gebruik met Java-, python-en Node.js-apps. De certificaten moeten worden geëxporteerd en gebruikt voor talen en runtime omgevingen die niet gebruikmaken van het Windows-certificaat archief.
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/17/2020
author: deborahc
ms.author: dech
ms.custom: devx-track-python, devx-track-java, contperf-fy21q1
ms.openlocfilehash: 952be09662c2c74f883d63de72bba2b9cb58d0e0
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102554000"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs-apps"></a>De Azure Cosmos DB-emulator certificaten exporteren voor gebruik met Java-, python-en Node.js-apps
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

De Azure Cosmos DB Emulator is een lokale omgeving waarin de Azure Cosmos DB-service wordt geëmuleerd voor ontwikkelingsdoeleinden. Azure Cosmos DB-emulator ondersteunt alleen beveiligde communicatie via TLS-verbindingen.

Certificaten in de Azure Cosmos DB lokale emulator worden gegenereerd wanneer u de emulator voor het eerst uitvoert. Er zijn twee certificaten. Een van deze wordt gebruikt om verbinding te maken met de lokale emulator en de andere wordt gebruikt voor het beheren van de standaard versleuteling van de emulator gegevens binnen de emulator. Het certificaat dat u wilt exporteren, is het verbindingscertificaat met de beschrijvende naam DocumentDBEmulatorCertificate.

Wanneer u de emulator gebruikt voor het ontwikkelen van apps in verschillende talen, zoals Java, python of Node.js, moet u het Emulator certificaat exporteren en importeren in het vereiste certificaat archief.

De .NET-taal en runtime maakt gebruik van het Windows-certificaat archief om veilig verbinding te maken met de Azure Cosmos DB lokale emulator wanneer de toepassing wordt uitgevoerd op een Windows-hostbesturingssysteem. Andere talen hebben hun eigen methode voor het beheren en gebruiken van certificaten. Java maakt bijvoorbeeld gebruik van een eigen [certificaat archief](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), python gebruikt [socket wrappers](https://docs.python.org/2/library/ssl.html)en Node.js gebruikt [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

In dit artikel wordt beschreven hoe u de TLS/SSL-certificaten exporteert voor gebruik in verschillende talen en runtime omgevingen die niet worden geïntegreerd met het Windows-certificaat archief. Meer informatie over de emulator vindt u in [Azure Cosmos DB Emulator gebruiken voor ontwikkeling en tests](./local-emulator.md).

## <a name="export-the-azure-cosmos-db-tlsssl-certificate"></a><a id="export-emulator-certificate"></a>Het Azure Cosmos DB TLS/SSL-certificaat exporteren

U moet het Emulator-certificaat exporteren om het Emulator-eind punt te kunnen gebruiken vanuit talen en runtime omgevingen die niet worden geïntegreerd met het Windows-certificaat archief. U kunt het certificaat exporteren met behulp van Windows Certificate Manager. Gebruik de volgende stapsgewijze instructies voor het exporteren van het certificaat ' DocumentDBEmulatorCertificate ' als een BASE-64 Encoded X. 509-bestand (. CER):

1. Start het Windows-certificaatbeheer door certlm.msc uit te voeren en ga naar de map Persoonlijk->Certificaten om het certificaat met de beschrijvende naam **DocumentDbEmulatorCertificate** te openen.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png" alt-text="Stap 1 van exporteren met lokale Azure DB Cosmos-emulator":::

1. Klik op **Details** en klik op **OK**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png" alt-text="Stap 2 van exporteren met lokale Azure DB Cosmos-emulator":::

1. Klik op **Kopiëren naar bestand...**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png" alt-text="Stap 3 van exporteren met lokale Azure DB Cosmos-emulator":::

1. Klik op **Volgende**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png" alt-text="Stap 4 van exporteren met lokale Azure DB Cosmos-emulator":::

1. Klik op **Nee, persoonlijke sleutel niet exporteren** en klik op **Volgende**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png" alt-text="Stap 5 van exporteren met lokale Azure DB Cosmos-emulator":::

1. Klik op **Base-64 encoded X.509 (.CER)** en klik op **Volgende**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png" alt-text="Stap 6 van exporteren met lokale Azure DB Cosmos-emulator":::

1. Geef het certificaat een naam, in dit geval **documentdbemulatorcert**, en klik op **Volgende**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png" alt-text="Stap 7 van exporteren met lokale Azure DB Cosmos-emulator":::

1. Klik op **Voltooien**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png" alt-text="Stap 8 van exporteren met lokale Azure DB Cosmos-emulator":::

## <a name="use-the-certificate-with-java-apps"></a>Het certificaat gebruiken met Java-apps

Bij het uitvoeren van Java-toepassingen of MongoDB-toepassingen die gebruikmaken van een Java-client, is het eenvoudiger om het certificaat te installeren in het standaard certificaat archief van Java dan door de vlaggen door te geven `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"` . De opgenomen Java-demonstratie toepassing ( `https://localhost:8081/_explorer/index.html` ) is bijvoorbeeld afhankelijk van het standaard certificaat archief.

Volg de instructies in het gedeelte [een certificaat aan het Archief voor Java-certificaten toevoegen](https://docs.oracle.com/cd/E54932_01/doc.705/e54936/cssg_create_ssl_cert.htm) om het X. 509-certificaat te importeren in het standaard-Java-certificaat archief. Houd er rekening mee dat u werkt in de map *% JAVA_HOME%* wanneer u het hulp programma voor het uitvoeren van de functie uitvoert. Nadat het certificaat in het certificaat archief is geïmporteerd, kunnen clients voor SQL-en Azure Cosmos DB-API voor MongoDB verbinding maken met de Azure Cosmos DB-emulator.

U kunt ook het volgende bash-script uitvoeren om het certificaat te importeren:

```bash
#!/bin/bash

# If emulator was started with /AllowNetworkAccess, replace the below with the actual IP address of it:
EMULATOR_HOST=localhost
EMULATOR_PORT=8081
EMULATOR_CERT_PATH=/tmp/cosmos_emulator.cert
openssl s_client -connect ${EMULATOR_HOST}:${EMULATOR_PORT} </dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > $EMULATOR_CERT_PATH
# delete the cert if already exists
sudo $JAVA_HOME/bin/keytool -cacerts -delete -alias cosmos_emulator
# import the cert
sudo $JAVA_HOME/bin/keytool -cacerts -importcert -alias cosmos_emulator -file $EMULATOR_CERT_PATH
```

Zodra het ' CosmosDBEmulatorCertificate ' TLS/SSL-certificaat is geïnstalleerd, moet uw toepassing verbinding kunnen maken en de lokale Azure Cosmos DB-emulator gebruiken. Als u problemen ondervindt, kunt u het artikel [fouten opsporen SSL/TLS-verbindingen](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) volgen. In de meeste gevallen kan het certificaat niet worden geïnstalleerd in het archief *% JAVA_HOME%/jre/lib/security/cacerts* . Als u bijvoorbeeld meerdere geïnstalleerde versies van Java hebt, gebruikt uw toepassing mogelijk een andere cacerts Store dan de versie die u hebt bijgewerkt.

## <a name="use-the-certificate-with-python-apps"></a>Het certificaat gebruiken met python-apps

Wanneer u verbinding maakt met de emulator vanuit Python-Apps, wordt TLS-verificatie uitgeschakeld. Standaard kan de [python-SDK (versie 2.0.0 of hoger)](sql-api-sdk-python.md) voor de SQL-API niet proberen het TLS/SSL-certificaat te gebruiken wanneer u verbinding maakt met de lokale emulator. Als u echter TLS-validatie wilt gebruiken, kunt u de voor beelden in de documentatie van [python-socket-wrappers](https://docs.python.org/2/library/ssl.html) volgen.

## <a name="how-to-use-the-certificate-in-nodejs"></a>Het certificaat gebruiken in Node.js

Wanneer u verbinding maakt met de emulator vanuit Node.js Sdk's, wordt TLS-verificatie uitgeschakeld. Standaard zal de [Node.js SDK (versie 1.10.1 of hoger)](sql-api-sdk-node.md) voor de SQL-API niet proberen om het TLS/SSL-certificaat te gebruiken wanneer verbinding wordt gemaakt met de lokale emulator. Als u echter TLS-validatie wilt gebruiken, kunt u de voor beelden in de [Node.js documentatie](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)volgen.

## <a name="rotate-emulator-certificates"></a>Emulator-certificaten draaien

U kunt forceren om de emulator certificaten opnieuw te genereren door **gegevens opnieuw instellen** te selecteren in de Azure Cosmos DB-emulator die wordt uitgevoerd in de Windows-lade. Houd er rekening mee dat met deze actie ook alle gegevens die lokaal zijn opgeslagen door de emulator, worden gewist.

:::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png" alt-text="Gegevens herstellen met de lokale emulator van Azure Cosmos DB":::

Als u het certificaat in het Java-certificaat archief hebt geïnstalleerd of elders hebt gebruikt, moet u ze opnieuw importeren met de huidige certificaten. Uw toepassing kan pas verbinding maken met de lokale emulator als u de certificaten bijwerkt.

## <a name="next-steps"></a>Volgende stappen

* [Opdracht regel parameters en Power shell-opdrachten gebruiken om de emulator te beheren](emulator-command-line-parameters.md)
* [Problemen met de emulator oplossen](troubleshoot-local-emulator.md)
