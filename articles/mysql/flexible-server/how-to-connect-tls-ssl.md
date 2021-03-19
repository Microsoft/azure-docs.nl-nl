---
title: Versleutelde connectiviteit met TLS/SSL in Azure Database for MySQL-flexibele server
description: Instructies en informatie over hoe u verbinding maakt met behulp van TLS/SSL in Azure Database for MySQL-flexibele server.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 24a8dd4d21cb6ab6edeb985db4e6e6a1349a758d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90936650"
---
# <a name="encrypted-connectivity-using-transport-layer-security-tls-12-in-azure-database-for-mysql---flexible-server"></a>Versleutelde connectiviteit met Transport Layer Security (TLS 1,2) in Azure Database for MySQL-flexibele server

> [!IMPORTANT]
> Azure Database for MySQL Flexible Server is momenteel beschikbaar als openbare preview

Azure Database for MySQL flexibele server ondersteunt het verbinden van uw client toepassingen met de MySQL-service met behulp van Transport Layer Security (TLS), voorheen bekend als Secure Sockets Layer (SSL). TLS is een industrie standaard protocol dat versleutelde netwerk verbindingen tussen uw database server-en client toepassingen waarborgt, zodat u kunt voldoen aan de nalevings vereisten.

Azure Database for MySQL flexibele server ondersteunt alleen versleutelde verbindingen met behulp van Transport Layer Security (TLS 1,2) en alle binnenkomende verbindingen met TLS 1,0 en TLS 1,1 worden geweigerd. Voor alle flexibele servers is het afdwingen van TLS-verbindingen ingeschakeld en u kunt TLS/SSL niet uitschakelen om verbinding te maken met een flexibele server.

## <a name="applications-that-require-certificate-verification-for-tlsssl-connectivity"></a>Toepassingen waarvoor certificaat verificatie is vereist voor TLS/SSL-connectiviteit
In sommige gevallen vereist toepassingen een lokaal certificaat bestand dat is gegenereerd op basis van een certificaat bestand van een vertrouwde certificerings instantie (CA) om veilig verbinding te kunnen maken. Azure Database for MySQL flexibele server gebruikt *DigiCert globale basis certificerings instantie*. Down load dit certificaat dat nodig is om te communiceren via SSL van [DigiCert Global root CA](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) en sla het certificaat bestand op de gewenste locatie op. In deze zelfstudie wordt bijvoorbeeld `c:\ssl` gebruikt.

### <a name="connect-using-mysql-command-line-client-with-tlsssl"></a>Verbinding maken met behulp van de MySQL-opdracht regel client met TLS/SSL

Als u een flexibele server hebt gemaakt met *Persoonlijke toegang (VNet-integratie)* , moet u verbinding maken met uw server vanuit een resource binnen hetzelfde VNet als uw server. U kunt een virtuele machine maken en toevoegen aan het VNet dat is gemaakt met uw flexibele server.

Als u uw flexibele server met *Openbare toegang (toegestane IP-adressen)* hebt gemaakt, kunt u uw lokale IP-adres toevoegen aan de lijst met firewallregels op uw server.

U kunt kiezen [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) of [MySQL Workbench](./connect-workbench.md)--> om verbinding te maken met de server vanuit uw lokale omgeving. 

In het volgende voor beeld ziet u hoe u verbinding maakt met uw server met behulp van de MySQL-opdracht regel interface. Gebruik de `--ssl-mode=REQUIRED` instelling Connection String om verificatie van TLS/SSL-certificaten af te dwingen. Geef het pad van het lokale certificaat bestand door aan de `--ssl-ca` para meter. Vervang de waarden door uw werkelijke servernaam en wachtwoord. 

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\DigiCertGlobalRootCA.crt.pem
```
> [!Note]
> Controleer of de waarde die is door gegeven, `--ssl-ca` overeenkomt met het bestandspad voor het certificaat dat u hebt opgeslagen.

### <a name="verify-the-tlsssl-connection"></a>De TLS/SSL-verbinding controleren

Voer de MySQL- **status** opdracht uit om te controleren of u verbinding hebt met de mysql-server met behulp van TLS/SSL:

```dos
mysql> status
```
Controleer of de verbinding is versleuteld door de uitvoer te bekijken. deze moet er als volgt uitzien:  **SSL: code in gebruik is AES256-SHA**. Deze coderings Suite toont een voor beeld en op basis van de client, u kunt een andere coderings Suite zien.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Zorg ervoor dat uw toepassing of Framework TLS-verbindingen ondersteunt

Voor sommige toepassings raamwerken die gebruikmaken van MySQL voor hun database services, wordt TLS niet standaard ingeschakeld tijdens de installatie. De MySQL-server dwingt TLS-verbindingen af, maar als de toepassing niet is geconfigureerd voor TLS, kan de toepassing geen verbinding maken met de database server. Raadpleeg de documentatie van uw toepassing voor informatie over het inschakelen van TLS-verbindingen.

## <a name="sample-code"></a>Voorbeeldcode
Verbindings reeksen die vooraf zijn gedefinieerd op de pagina verbindings reeksen die beschikbaar zijn voor uw server in de Azure Portal bevatten de vereiste para meters voor algemene talen om verbinding te maken met uw database server met behulp van TLS/SSL. De TLS/SSL-para meter varieert op basis van de connector. Bijvoorbeeld "useSSL = True", "sslmode = required" of "ssl_verify_cert = True" en andere variaties.

Als u een versleutelde verbinding met uw flexibele server via TLS/SSL vanuit uw toepassing tot stand wilt brengen, raadpleegt u de volgende code voorbeelden:

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
