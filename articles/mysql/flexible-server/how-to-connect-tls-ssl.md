---
title: Versleutelde connectiviteit met TLS/SSL in Azure Database for MySQL-flexibele server
description: Instructies en informatie over hoe u verbinding maakt met behulp van TLS/SSL in Azure Database for MySQL-flexibele server.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 6dbb1b46aef40986fc2d601aee152aed02591ac0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312600"
---
# <a name="connect-to-azure-database-for-mysql---flexible-server-with-encrypted-connections"></a>Verbinding maken met Azure Database for MySQL-flexibele server met versleutelde verbindingen

> [!IMPORTANT]
> Azure Database for MySQL Flexible Server is momenteel beschikbaar als openbare preview

Azure Database for MySQL flexibele server ondersteunt het verbinden van uw client toepassingen met de MySQL-server met behulp van Secure Sockets Layer (SSL) met TLS-code ring (trans port Layer Security). TLS is een industrie standaard protocol dat versleutelde netwerk verbindingen tussen uw database server-en client toepassingen waarborgt, zodat u kunt voldoen aan de nalevings vereisten.

Azure Database for MySQL flexibele server ondersteunt standaard versleutelde verbindingen met behulp van Transport Layer Security (TLS 1,2) en alle binnenkomende verbindingen met TLS 1,0 en TLS 1,1 worden standaard geweigerd. De versleutelde configuratie voor het afdwingen van verbinding of TLS-versie op de flexibele server kan worden gewijzigd zoals beschreven in dit artikel. 

Hieronder vindt u de verschillende configuratie van SSL-en TLS-instellingen die u kunt hebben voor uw flexibele server:

| Scenario   | Instellingen voor server parameters      | Description                                    |
|------------|--------------------------------|------------------------------------------------|
|SSL-afdwinging uitschakelen | require_secure_transport = uit |Als uw oudere toepassing geen versleutelde verbindingen met de MySQL-server ondersteunt, kunt u het afdwingen van versleutelde verbindingen met uw flexibele server uitschakelen door require_secure_transport = uit te stellen.|
|SSL afdwingen met TLS-versie < 1,2 | require_secure_transport = ON en tls_version = TLSV1 of TLSV 1.1| Als uw oudere toepassing versleutelde verbindingen ondersteunt, maar TLS-versie < 1,2, kunt u versleutelde verbindingen inschakelen, maar uw flexibele server zo configureren dat verbindingen met de TLS-versie (v 1.0 of v 1.1) die door uw toepassing worden ondersteund, worden toegestaan|
|SSL afdwingen met TLS-versie = 1.2 (standaard configuratie)|require_secure_transport = ON en tls_version = TLSV 1.2| Dit is de aanbevolen en standaard configuratie voor een flexibele server.|
|SSL afdwingen met TLS-versie = 1.3 (ondersteund met MySQL v 8.0 en hoger)| require_secure_transport = op en tls_version = TLSV 1.3| Dit is handig en aanbevolen voor het ontwikkelen van nieuwe toepassingen|

> [!Note]
> Wijzigingen in SSL-code ring op een flexibele server worden niet ondersteund. FIPS-coderings suites worden standaard afgedwongen wanneer tls_version is ingesteld op TLS-versie 1,2. Voor TLS-versies met uitzonde ring van versie 1,2 wordt SSL-code ring ingesteld op standaard instellingen die bij de installatie van MySQL-community's worden geleverd.

In dit artikel leert u het volgende:
* Uw flexibele server configureren 
  * Met SSL uitgeschakeld 
  * Met SSL afgedwongen met TLS-versie < 1,2
* Verbinding maken met uw flexibele server via de opdracht regel mysql 
  * Met uitgeschakelde versleutelde verbindingen
  * Met ingeschakelde versleutelde verbindingen
* De versleutelings status voor de verbinding controleren
* Verbinding maken met uw flexibele server met versleutelde verbindingen met behulp van verschillende toepassings raamwerken

## <a name="disable-ssl-enforcement-on-your-flexible-server"></a>SSL-afdwinging op uw flexibele server uitschakelen
Als uw client toepassing geen versleutelde verbindingen ondersteunt, moet u het afdwingen van versleutelde verbindingen op uw flexibele server uitschakelen. Als u het afdwingen van versleutelde verbindingen wilt uitschakelen, moet u de para meter require_secure_transport server instellen op uitgeschakeld, zoals wordt weer gegeven in de scherm opname en de configuratie van de server parameter zodanig opslaan dat deze van kracht wordt. require_secure_transport is een **para meter voor dynamische server** die onmiddellijk van kracht wordt en niet opnieuw moet worden opgestart om de server van kracht te laten worden.

> :::image type="content" source="./media/how-to-connect-tls-ssl/disable-ssl.png" alt-text="Scherm opname waarin wordt getoond hoe SSL met Azure Database for MySQL flexibele server moet worden uitgeschakeld.":::

### <a name="connect-using-mysql-command-line-client-with-ssl-disabled"></a>Verbinding maken met behulp van de MySQL-opdracht regel client met SSL uitgeschakeld

In het volgende voor beeld ziet u hoe u verbinding maakt met uw server met behulp van de MySQL-opdracht regel interface. Gebruik de `--ssl-mode=DISABLED` instelling Connection String om de TLS/SSL-verbinding van de mysql-client uit te scha kelen. Vervang de waarden door uw werkelijke servernaam en wachtwoord. 

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=DISABLED 
```
Het is belang rijk te weten dat de instelling require_secure_transport op uit niet betekent dat versleutelde verbindingen niet worden ondersteund aan de server zijde. Als u require_secure_transport instelt op de flexibele server, maar als de client verbinding maakt met een versleutelde verbinding, wordt deze nog steeds geaccepteerd. De volgende verbinding die gebruikmaakt van mysql-client naar een flexibele server die is geconfigureerd met require_secure_transport = uitgeschakeld, werkt ook zoals hieronder wordt weer gegeven.

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=REQUIRED
```
```output
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 17
Server version: 5.7.29-log MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show global variables like '%require_secure_transport%';
+--------------------------+-------+
| Variable_name            | Value |
+--------------------------+-------+
| require_secure_transport | OFF   |
+--------------------------+-------+
1 row in set (0.02 sec)
```

In samen vatting vertoont require_secure_transport = uit instelling het afdwingen van versleutelde verbindingen op een flexibele server en maakt niet-versleutelde verbindingen met de server van client mogelijk naast de versleutelde verbindingen.

## <a name="enforce-ssl-with-tls-version--12"></a>SSL afdwingen met TLS-versie < 1,2

Als uw toepassing verbindingen met een MySQL-server met SSL ondersteunt, maar TLS-versie < 1,2, moet u de para meter TLS-server parameters op de flexibele server instellen. Als u TLS-versies wilt instellen die door de flexibele server moeten worden ondersteund, moet u tls_version server para meter instellen op TLSV1, TLSV 1.1 of TLSV1 en TLSV 1.1, zoals weer gegeven in de scherm opname en de configuratie van de server parameter zodanig opslaan dat deze van kracht wordt. tls_version is een **statische server parameter** waarvoor de server opnieuw moet worden opgestart om de para meter van kracht te laten worden.

> :::image type="content" source="./media/how-to-connect-tls-ssl/tls-version.png" alt-text="Scherm afbeelding die laat zien hoe u TLS-versie kunt instellen voor een Azure Database for MySQL flexibele server.":::

## <a name="connect-using-mysql-command-line-client-with-tlsssl"></a>Verbinding maken met behulp van de MySQL-opdracht regel client met TLS/SSL

### <a name="download-the-public-ssl-certificate"></a>Het open bare SSL-certificaat downloaden
Als u versleutelde verbindingen met uw client toepassingen wilt gebruiken, moet u het [open bare SSL-certificaat](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) downloaden dat ook beschikbaar is op Azure portal netwerk-Blade, zoals wordt weer gegeven in de onderstaande scherm afbeelding.

> :::image type="content" source="./media/how-to-connect-tls-ssl/download-ssl.png" alt-text="Scherm afbeelding die laat zien hoe u het open bare SSL-certificaat van Azure Portal downloadt.":::

Sla het certificaat bestand op de gewenste locatie op. Deze zelf studie maakt bijvoorbeeld gebruik `c:\ssl` `\var\www\html\bin` van of in uw lokale omgeving of de client omgeving waar uw toepassing wordt gehost. Hierdoor kunnen toepassingen veilig verbinding maken met de data base via SSL.

Als u een flexibele server hebt gemaakt met *Persoonlijke toegang (VNet-integratie)* , moet u verbinding maken met uw server vanuit een resource binnen hetzelfde VNet als uw server. U kunt een virtuele machine maken en toevoegen aan het VNet dat is gemaakt met uw flexibele server.

Als u uw flexibele server met *Openbare toegang (toegestane IP-adressen)* hebt gemaakt, kunt u uw lokale IP-adres toevoegen aan de lijst met firewallregels op uw server.

U kunt kiezen [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) of [MySQL Workbench](./connect-workbench.md)--> om verbinding te maken met de server vanuit uw lokale omgeving. 

In het volgende voor beeld ziet u hoe u verbinding maakt met uw server met behulp van de MySQL-opdracht regel interface. Gebruik de `--ssl-mode=REQUIRED` instelling Connection String om verificatie van TLS/SSL-certificaten af te dwingen. Geef het pad van het lokale certificaat bestand door aan de `--ssl-ca` para meter. Vervang de waarden door uw werkelijke servernaam en wachtwoord. 

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```
> [!Note]
> Controleer of de waarde die is door gegeven, `--ssl-ca` overeenkomt met het bestandspad voor het certificaat dat u hebt opgeslagen.

Als u probeert verbinding te maken met uw server met niet-versleutelde verbindingen, ziet u dat er een fout bericht wordt weer gegeven met behulp van onbeveiligde Trans Port, zoals hieronder is toegestaan:

```output
ERROR 3159 (HY000): Connections using insecure transport are prohibited while --require_secure_transport=ON.
```

## <a name="verify-the-tlsssl-connection"></a>De TLS/SSL-verbinding controleren

Voer de MySQL- **status** opdracht uit om te controleren of u verbinding hebt met de mysql-server met behulp van TLS/SSL:

```dos
mysql> status
```
Controleer of de verbinding is versleuteld door de uitvoer te bekijken. deze moet er als volgt uitzien: * * SSL: code in gebruik is * *. Deze coderings Suite toont een voor beeld en op basis van de client, u kunt een andere coderings Suite zien.

## <a name="connect-to-your-flexible-server-with-encrypted-connections-using-various-application-frameworks"></a>Verbinding maken met uw flexibele server met versleutelde verbindingen met behulp van verschillende toepassings raamwerken

Verbindings reeksen die vooraf zijn gedefinieerd op de pagina verbindings reeksen die beschikbaar zijn voor uw server in de Azure Portal bevatten de vereiste para meters voor algemene talen om verbinding te maken met uw database server met behulp van TLS/SSL. De TLS/SSL-para meter varieert op basis van de connector. Bijvoorbeeld "useSSL = True", "sslmode = required" of "ssl_verify_cert = True" en andere variaties.

Als u een versleutelde verbinding met uw flexibele server via TLS/SSL vanuit uw toepassing tot stand wilt brengen, raadpleegt u de volgende code voorbeelden:

### <a name="wordpress"></a>WordPress
Down load het [open bare SSL-certificaat](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) en voeg de volgende regels toe in wp-config. php na de regel ```// ** MySQL settings - You can get this info from your web host ** //``` .

```php
//** Connect with SSL** //
define('MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL);
//** SSL CERT **//
define('MYSQL_SSL_CERT','/FULLPATH/on-client/to/DigiCertGlobalRootCA.crt.pem');
```

### <a name="php"></a>PHP

```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootCA.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

### <a name="php-using-pdo"></a>PHP (met Bob)

```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'myadmin', 'yourpassword', $options);
```

### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector python)

```python
try:
    conn = mysql.connector.connect(user='myadmin',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mysql.database.azure.com',
                                   ssl_ca='/var/www/html/DigiCertGlobalRootCA.crt.pem')
except mysql.connector.Error as err:
    print(err)
```

### <a name="python-pymysql"></a>Python (PyMySQL)

```python
conn = pymysql.connect(user='myadmin',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mysql.database.azure.com',
                       ssl={'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'})
```

### <a name="django-pymysql"></a>Django (PyMySQL)

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'}
        }
    }
}
```

### <a name="ruby"></a>Ruby

```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com',
        :username => 'myadmin',
        :password => 'yourpassword',
        :database => 'quickstartdb',
        :sslca => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
    )
```

### <a name="golang"></a>Golang

```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/DigiCertGlobalRootCA.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')
db, _ := sql.Open("mysql", connectionString)
```

### <a name="java-mysql-connector-for-java"></a>Java (MySQL-Connector voor Java)

```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " +
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " +
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="java-mariadb-connector-for-java"></a>Java (MariaDB-connector voor Java)

```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " +
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " +
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)

```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    SslCa = "DigiCertGlobalRootCA.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

## <a name="next-steps"></a>Volgende stappen
- [MySQL Workbench gebruiken om verbinding te maken en gegevens op te vragen in Azure Database for MySQL flexibele server](./connect-workbench.md)
- [PHP gebruiken om verbinding te maken en gegevens op te vragen in Azure Database for MySQL flexibele server](./connect-php.md)
- [Maak en beheer Azure database for MySQL flexibele virtuele server netwerk met behulp van Azure cli](./how-to-manage-virtual-network-cli.md).
- Meer informatie over [netwerken in azure database for MySQL flexibele server](./concepts-networking.md)
- Meer informatie over [Azure database for MySQL flexibele server firewall regels](./concepts-networking.md#public-access-allowed-ip-addresses)
