---
title: Verbinding maken met Azure App Service-Azure Database for MySQL
description: Instructies voor het op de juiste wijze koppelen van een bestaande Azure App Service aan Azure Database for MySQL
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 6355afe6ce5decbed029db4536b1b1b19f5a876c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94541501"
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>Een bestaande Azure App Service koppelen aan Azure Database for MySQL server
In dit onderwerp wordt uitgelegd hoe u een bestaande Azure App Service kunt verbinden met uw Azure Database for MySQL-server.

## <a name="before-you-begin"></a>Voordat u begint
Meld u aan bij [Azure Portal](https://portal.azure.com). Een Azure Database for MySQL-server maken. Raadpleeg voor meer informatie [hoe u Azure database for mysql server maakt vanuit de portal](quickstart-create-mysql-server-database-using-azure-portal.md) of [hoe u Azure database for mysql server maakt met behulp van CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

Er zijn momenteel twee oplossingen voor het inschakelen van toegang vanaf een Azure App Service naar een Azure Database for MySQL. Beide oplossingen moeten firewall regels op server niveau instellen.

## <a name="solution-1---allow-azure-services"></a>Oplossing 1: Azure-Services toestaan
Azure Database for MySQL biedt toegangs beveiliging met behulp van een firewall om uw gegevens te beveiligen. Wanneer u verbinding maakt vanaf een Azure App Service naar Azure Database for MySQL-server, moet u er wel voor zorgen dat de uitgaande IP-adressen van App Service dynamisch zijn. Als u de optie toegang tot Azure-Services toestaan kiest, kan de app service verbinding maken met de MySQL-server.

1. Klik op de Blade MySQL-server, onder de kop instellingen, op **verbindings beveiliging** om de Blade verbindings beveiliging te openen voor Azure database for MySQL.

   :::image type="content" source="./media/howto-connect-webapp/1-connection-security.png" alt-text="Azure Portal-Klik op verbindings beveiliging":::

2. Selecteer **aan** in **toegang tot Azure-Services toestaan** en **Sla** het bestand op.
   :::image type="content" source="./media/howto-connect-webapp/allow-azure.png" alt-text="Azure Portal-toegang tot Azure toestaan":::

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>Oplossing 2: een firewall regel maken om uitgaande IP-adressen expliciet toe te staan
U kunt alle uitgaande IP-adressen van uw Azure App Service expliciet toevoegen.

1. Bekijk op de Blade App Service eigenschappen uw **uitgaande IP-adres**.

   :::image type="content" source="./media/howto-connect-webapp/2_1-outbound-ip-address.png" alt-text="Azure Portal-uitgaand IP-adressen weer geven":::

2. Voeg op de Blade met de MySQL-verbinding de uitgaand IP-adressen één voor één toe.

   :::image type="content" source="./media/howto-connect-webapp/2_2-add-explicit-ips.png" alt-text="Azure Portal-expliciete IP-adressen toevoegen":::

3. Vergeet niet om uw firewall regels op te **slaan** .

Hoewel de Azure-app-service probeert IP-adressen constant gedurende een periode te blijven, zijn er gevallen waarin de IP-adressen kunnen worden gewijzigd. Dit kan bijvoorbeeld gebeuren wanneer de app wordt gerecycled of een schaal bewerking wordt uitgevoerd, of wanneer nieuwe computers worden toegevoegd in azure Regional Data Centers om de capaciteit te verg Roten. Wanneer de IP-adressen worden gewijzigd, kan de app downtime in de gebeurtenis ondervinden die niet meer kan worden verbonden met de MySQL-server. Houd rekening met het volgende bij het kiezen van een van de voor gaande oplossingen.

## <a name="ssl-configuration"></a>SSL-configuratie
Azure Database for MySQL SSL is standaard ingeschakeld. Als uw toepassing geen gebruikmaakt van SSL om verbinding te maken met de data base, moet u SSL op de MySQL-server uitschakelen. Zie [using SSL with Azure database for MySQL](howto-configure-ssl.md)voor meer informatie over het configureren van SSL.

### <a name="django-pymysql"></a>Django (PyMySQL)
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin@mydemoserver',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ssl-ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen
Raadpleeg [verbindings reeksen](howto-connection-string.md)voor meer informatie over verbindings reeksen.
