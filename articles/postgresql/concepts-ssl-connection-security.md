---
title: SSL/TLS-Azure Database for PostgreSQL-één server
description: Instructies en informatie over het configureren van TLS-connectiviteit voor Azure Database for PostgreSQL-één server.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: c98ee8f747975d4237c2906be2060eddbc7b9990
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000943"
---
# <a name="configure-tls-connectivity-in-azure-database-for-postgresql---single-server"></a>TLS-connectiviteit configureren in Azure Database for PostgreSQL-één server

Azure Database for PostgreSQL geeft de voor keur aan de verbinding tussen uw client toepassingen en de PostgreSQL-service met behulp van Transport Layer Security (TLS), voorheen bekend als Secure Sockets Layer (SSL). Het afdwingen van TLS-verbindingen tussen uw database server en uw client toepassingen helpt bij het beveiligen van ' man-in-the-middle '-aanvallen door de gegevens stroom tussen de server en uw toepassing te versleutelen.

De PostgreSQL-database service is standaard geconfigureerd voor het vereisen van een TLS-verbinding. U kunt ervoor kiezen om het vereisen van TLS uit te scha kelen als uw client toepassing geen TLS-connectiviteit ondersteunt.

>[!NOTE]
> Op basis van de feedback van klanten hebben we de afschaffing van het basis certificaat uitgebreid voor onze bestaande Baltimore basis certificerings instantie van 15 februari 2021 (02/15/2021).

> [!IMPORTANT] 
> Het SSL-basis certificaat is ingesteld op verlopen vanaf 15 februari 2021 (02/15/2021). Werk uw toepassing bij om het [nieuwe certificaat](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)te gebruiken. Zie [geplande certificaat updates](concepts-certificate-rotation.md) voor meer informatie.


## <a name="enforcing-tls-connections"></a>TLS-verbindingen afdwingen

Voor alle Azure Database for PostgreSQL servers die via de Azure Portal en CLI worden ingericht, wordt het afdwingen van TLS-verbindingen standaard ingeschakeld. 

De verbindings reeksen die vooraf zijn gedefinieerd in de instellingen voor verbindings reeksen onder uw server in de Azure Portal bevatten ook de vereiste para meters voor algemene talen om verbinding te maken met uw database server met behulp van TLS. De TLS-para meter varieert op basis van de connector, bijvoorbeeld "SSL = True" of "sslmode = vereist" of "sslmode = required" en andere variaties.

## <a name="configure-enforcement-of-tls"></a>Afdwinging van TLS configureren

U kunt eventueel het afdwingen van TLS-connectiviteit uitschakelen. Microsoft Azure wordt aanbevolen SSL- **verbindings instelling afdwingen** altijd in te scha kelen voor verbeterde beveiliging.

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Ga naar uw Azure Database for PostgreSQL-server en klik op **verbindings beveiliging**. Gebruik de wissel knop om de instelling SSL- **verbinding afdwingen** in of uit te scha kelen. Klik vervolgens op **Opslaan**.

:::image type="content" source="./media/concepts-ssl-connection-security/1-disable-ssl.png" alt-text="Verbindings beveiliging-TLS/SSL afdwingen uitschakelen":::

U kunt de instelling bevestigen door de pagina **overzicht** te bekijken om de **SSL-afdwingings status** indicator te bekijken.

### <a name="using-azure-cli"></a>Azure CLI gebruiken

U kunt de **SSL-afdwinging-** para meter in-of uitschakelen met `Enabled` `Disabled` respectievelijk waarden in azure cli.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Zorg ervoor dat uw toepassing of Framework TLS-verbindingen ondersteunt

Voor sommige toepassings raamwerken die gebruikmaken van PostgreSQL voor hun database services, wordt TLS niet standaard ingeschakeld tijdens de installatie. Als uw PostgreSQL-server TLS-verbindingen afdwingt, maar de toepassing niet is geconfigureerd voor TLS, kan de toepassing geen verbinding maken met de database server. Raadpleeg de documentatie van uw toepassing voor informatie over het inschakelen van TLS-verbindingen.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Toepassingen waarvoor certificaat verificatie voor TLS-connectiviteit is vereist

In sommige gevallen vereist toepassingen een lokaal certificaat bestand dat is gegenereerd op basis van een certificaat bestand van een vertrouwde certificerings instantie (CA) om veilig verbinding te kunnen maken. Het certificaat om verbinding te maken met een Azure Database for PostgreSQL server bevindt zich op https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem . Down load het certificaat bestand en sla het op naar uw voorkeurs locatie. 

Raadpleeg de volgende koppelingen voor certificaten voor servers in soevereine Clouds: [Azure Government](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem), [Azure China](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)en [Azure Duitsland](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt).

### <a name="connect-using-psql"></a>Verbinding maken met psql

In het volgende voor beeld ziet u hoe u verbinding maakt met uw PostgreSQL-server met behulp van het opdracht regel programma psql. Gebruik de `sslmode=verify-full` instelling Connection String om verificatie van TLS/SSL-certificaten af te dwingen. Geef het pad van het lokale certificaat bestand door aan de `sslrootcert` para meter.

De volgende opdracht is een voor beeld van de psql-connection string:

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Controleer of de waarde die is door gegeven, `sslrootcert` overeenkomt met het bestandspad voor het certificaat dat u hebt opgeslagen.

## <a name="tls-enforcement-in-azure-database-for-postgresql-single-server"></a>TLS-afdwinging in Azure Database for PostgreSQL één server

Azure Database for PostgreSQL-één server ondersteunt versleuteling voor clients die verbinding maken met uw database server met behulp van Transport Layer Security (TLS). TLS is een industrie standaard protocol dat beveiligde netwerk verbindingen tussen uw database server-en client toepassingen waarborgt, zodat u kunt voldoen aan de nalevings vereisten.

### <a name="tls-settings"></a>TLS-instellingen

Azure Database for PostgreSQL één server biedt de mogelijkheid om de TLS-versie voor de client verbindingen af te dwingen. Als u de TLS-versie wilt afdwingen, gebruikt u de instelling **minimale TLS-versie** optie. De volgende waarden zijn toegestaan voor deze optie-instelling:

|  Minimale TLS-instelling             | TLS-versie van client wordt ondersteund                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (standaard) | Geen TLS vereist                      |
| TLS1_0                           | TLS 1,0, TLS 1,1, TLS 1,2 en hoger |
| TLS1_1                           | TLS 1,1, TLS 1,2 en hoger          |
| TLS1_2                           | TLS-versie 1,2 en hoger           |


Als u bijvoorbeeld deze versie van een TLS-instelling instelt op TLS 1,0, betekent dit dat uw server verbindingen met clients toestaat die gebruikmaken van TLS 1,0, 1,1 en 1.2 +. U kunt dit ook instellen op 1,2 zodat u alleen verbindingen van clients met TLS 1.2 + kunt toestaan en alle verbindingen met TLS 1,0 en TLS 1,1 worden geweigerd.

> [!Note] 
> Azure Database for PostgreSQL wordt standaard geen TLS-versie (de instelling) afgedwongen `TLSEnforcementDisabled` .
>
> Wanneer u een minimum versie van TLS afdwingt, kunt u de minimale afdwinging van de versie niet later uitschakelen.

Zie [TLS-instelling configureren](howto-tls-configurations.md)voor meer informatie over het instellen van de TLS-instelling voor uw Azure database for PostgreSQL één server.

## <a name="cipher-support-by-azure-database-for-postgresql-single-server"></a>Ondersteuning voor code ring door Azure Database for PostgreSQL één server

Als onderdeel van de SSL/TLS-communicatie worden de coderings suites gevalideerd en worden alleen coderings pakken toegestaan om te communiceren met de data base-serer. De coderings Suite validatie wordt beheerd in de laag van de [Gateway](concepts-connectivity-architecture.md#connectivity-architecture) en niet expliciet op het knoop punt zelf. Als de coderings suites niet overeenkomen met een van de hieronder vermelde suites, worden binnenkomende client verbindingen afgewezen.

### <a name="cipher-suite-supported"></a>Coderings Suite ondersteund

*   TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
*   TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="next-steps"></a>Volgende stappen

Bekijk verschillende opties voor toepassings connectiviteit in [verbindings bibliotheken voor Azure database for PostgreSQL](concepts-connection-libraries.md).

- Meer informatie over het [configureren van TLS](howto-tls-configurations.md)