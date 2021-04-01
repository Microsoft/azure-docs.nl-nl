---
title: Versleutelde connectiviteit met TLS/SSL in Azure Database for PostgreSQL-flexibele server
description: Instructies en informatie over hoe u verbinding maakt met behulp van TLS/SSL in Azure Database for PostgreSQL-flexibele server.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 0ba04ff14e62cd411515a765dc95ef7a3e72b709
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90935929"
---
# <a name="encrypted-connectivity-using-transport-layer-security-in-azure-database-for-postgresql---flexible-server"></a>Versleutelde connectiviteit met Transport Layer Security in Azure Database for PostgreSQL-flexibele server

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is als preview-versie beschikbaar

Azure Database for PostgreSQL-flexibele server ondersteunt de verbinding tussen uw client toepassingen en de PostgreSQL-service met behulp van Transport Layer Security (TLS), voorheen bekend als Secure Sockets Layer (SSL). TLS is een industrie standaard protocol dat versleutelde netwerk verbindingen tussen uw database server-en client toepassingen waarborgt, zodat u kunt voldoen aan de nalevings vereisten.

Azure Database for PostgreSQL-flexibele server ondersteunt versleutelde verbindingen met behulp van Transport Layer Security (TLS 1.2 +) en alle binnenkomende verbindingen met TLS 1,0 en TLS 1,1 worden geweigerd. Voor alle flexibele servers is het afdwingen van TLS-verbindingen ingeschakeld en u kunt TLS/SSL niet uitschakelen om verbinding te maken met een flexibele server.

## <a name="applications-that-require-certificate-verification-for-tlsssl-connectivity"></a>Toepassingen waarvoor certificaat verificatie is vereist voor TLS/SSL-connectiviteit
In sommige gevallen vereist toepassingen een lokaal certificaat bestand dat is gegenereerd op basis van een certificaat bestand van een vertrouwde certificerings instantie (CA) om veilig verbinding te kunnen maken. Azure Database for PostgreSQL-flexibele server gebruikt *DigiCert globale basis certificerings instantie*. Down load dit certificaat dat nodig is om te communiceren via SSL van [DigiCert Global root CA](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) en sla het certificaat bestand op de gewenste locatie op. In deze zelfstudie wordt bijvoorbeeld `c:\ssl` gebruikt.


### <a name="connect-using-psql"></a>Verbinding maken met psql
Als u een flexibele server hebt gemaakt met *Persoonlijke toegang (VNet-integratie)* , moet u verbinding maken met uw server vanuit een resource binnen hetzelfde VNet als uw server. U kunt een virtuele machine maken en toevoegen aan het VNet dat is gemaakt met uw flexibele server.

Als u uw flexibele server met *Openbare toegang (toegestane IP-adressen)* hebt gemaakt, kunt u uw lokale IP-adres toevoegen aan de lijst met firewallregels op uw server.

In het volgende voor beeld ziet u hoe u verbinding maakt met uw server met behulp van de psql-opdracht regel interface. Gebruik de `sslmode=verify-full` instelling Connection String om verificatie van TLS/SSL-certificaten af te dwingen. Geef het pad van het lokale certificaat bestand door aan de `sslrootcert` para meter.

```bash
 psql "sslmode=verify-full sslrootcert=c:\ssl\DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=postgres user=myadmin"
```
> [!Note]
> Controleer of de waarde die is door gegeven, `sslrootcert` overeenkomt met het bestandspad voor het certificaat dat u hebt opgeslagen.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Zorg ervoor dat uw toepassing of Framework TLS-verbindingen ondersteunt

Voor sommige toepassings raamwerken die gebruikmaken van PostgreSQL voor hun database services, wordt TLS niet standaard ingeschakeld tijdens de installatie. Uw PostgreSQL-server dwingt TLS-verbindingen af, maar als de toepassing niet is geconfigureerd voor TLS, kan de toepassing geen verbinding maken met de database server. Raadpleeg de documentatie van uw toepassing voor informatie over het inschakelen van TLS-verbindingen.

## <a name="next-steps"></a>Volgende stappen
- [Maak en beheer Azure database for PostgreSQL flexibel Virtueel Server netwerk met behulp van Azure cli](./how-to-manage-virtual-network-cli.md).
- Meer informatie over [netwerken in azure database for PostgreSQL-flexibele server](./concepts-networking.md)
- Meer informatie over [Azure database for PostgreSQL-flexibele server firewall regels](./concepts-networking.md#public-access-allowed-ip-addresses)
