---
title: Transport Layer Security (TLS)-grootschalige (Citus)-Azure Database for PostgreSQL
description: Instructies en informatie voor het configureren van Azure Database for PostgreSQL-grootschalige (Citus) en bijbehorende toepassingen voor het goed gebruiken van TLS-verbindingen.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: 659f86a107e4b08db4ec5195635ea32d2260d677
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87071459"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>TLS configureren in Azure Database for PostgreSQL-grootschalige (Citus)
Het grootschalige (Citus)-coördinator knooppunt vereist dat client toepassingen verbinding maken met Transport Layer Security (TLS). Het afdwingen van TLS tussen de database server en client toepassingen zorgt ervoor dat gegevens vertrouwelijk blijven. Extra verificatie-instellingen die hieronder worden beschreven, worden ook beschermd tegen ' man-in-the-middle ' aanvallen.

## <a name="enforcing-tls-connections"></a>TLS-verbindingen afdwingen
Toepassingen gebruiken een ' connection string ' om de doel database en-instellingen voor een verbinding te identificeren. Verschillende clients vereisen verschillende instellingen. Raadpleeg de sectie **verbindings reeksen** voor uw server groep in de Azure portal voor een overzicht van de verbindings reeksen die worden gebruikt door algemene clients.

De TLS-para meters `ssl` en `sslmode` variëren op basis van de mogelijkheden van de connector, bijvoorbeeld `ssl=true` of `sslmode=require` `sslmode=required` .

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Zorg ervoor dat uw toepassing of Framework TLS-verbindingen ondersteunt
Voor sommige toepassings raamwerken is TLS niet standaard ingeschakeld voor PostgreSQL-verbindingen. Zonder een beveiligde verbinding kan een toepassing echter geen verbinding maken met een grootschalige (Citus)-coördinator knooppunt. Raadpleeg de documentatie van uw toepassing voor informatie over het inschakelen van TLS-verbindingen.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Toepassingen waarvoor certificaat verificatie voor TLS-connectiviteit is vereist
In sommige gevallen vereist toepassingen een lokaal certificaat bestand dat is gegenereerd op basis van een certificaat bestand van een vertrouwde certificerings instantie (. CER) om veilig verbinding te kunnen maken. Het certificaat om verbinding te maken met een Azure Database for PostgreSQL-grootschalige (Citus) bevindt zich op https://cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem . Down load het certificaat bestand en sla het op naar uw voorkeurs locatie.

> [!NOTE]
>
> Als u de authenticiteit van het certificaat wilt controleren, kunt u de SHA-256-vinger afdruk controleren met behulp van het opdracht regel programma OpenSSL:
>
> ```sh
> openssl x509 -in DigiCertGlobalRootCA.crt.pem -noout -sha256 -fingerprint
>
> # should output:
> # 43:48:A0:E9:44:4C:78:CB:26:5E:05:8D:5E:89:44:B4:D8:4F:96:62:BD:26:DB:25:7F:89:34:A4:43:C7:01:61
> ```

### <a name="connect-using-psql"></a>Verbinding maken met psql
In het volgende voor beeld ziet u hoe u verbinding maakt met uw grootschalige (Citus)-coördinator knooppunt met behulp van het psql-opdracht regel programma. Gebruik de `sslmode=verify-full` instelling Connection String om TLS-certificaat verificatie af te dwingen. Geef het pad van het lokale certificaat bestand door aan de `sslrootcert` para meter.

Hieronder ziet u een voor beeld van de psql-connection string:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Controleer of de waarde die is door gegeven, `sslrootcert` overeenkomt met het bestandspad voor het certificaat dat u hebt opgeslagen.

## <a name="next-steps"></a>Volgende stappen
Verbeter de beveiliging verder met de [firewall regels in azure database for PostgreSQL-grootschalige (Citus)](concepts-hyperscale-firewall-rules.md).
