---
title: SSL-Azure Database for MySQL configureren
description: Instructies voor het correct configureren van Azure Database for MySQL en gekoppelde toepassingen om SSL-verbindingen correct te gebruiken
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 07/08/2020
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 1c3e5a44e01f3fa43b82644103066f5a03684ad2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94591577"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Configureer SSL-connectiviteit in de toepassing zo dat er op een veilige manier verbinding wordt gemaakt met Azure Database for MySQL.

Azure Database for MySQL biedt ondersteuning voor het gebruik van Secure Sockets Layer (SSL) om uw Azure Database for MySQL-server te verbinden met clienttoepassingen. Het afdwingen van SSL-verbindingen tussen uw databaseserver en clienttoepassingen zorgt dat u bent beschermt tegen 'man in the middle'-aanvallen omdat de gegevensstroom tussen de server en uw toepassing wordt versleuteld.

## <a name="step-1-obtain-ssl-certificate"></a>Stap 1: een SSL-certificaat verkrijgen

Down load het certificaat dat nodig is om via SSL met uw Azure Database for MySQL-server te communiceren [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) en sla het certificaat bestand op uw lokale station op (in deze zelf studie wordt c:\ssl bijvoorbeeld gebruikt).
**Voor micro soft Internet Explorer en micro soft Edge:** Wanneer het downloaden is voltooid, wijzigt u de naam van het certificaat in BaltimoreCyberTrustRoot. CRT. pem.

>[!NOTE]
> Op basis van de feedback van klanten hebben we de afschaffing van het basis certificaat uitgebreid voor onze bestaande Baltimore basis certificerings instantie van 15 februari 2021 (02/15/2021).

> [!IMPORTANT] 
> Het SSL-basis certificaat is ingesteld op verlopen vanaf 15 februari 2021 (02/15/2021). Werk uw toepassing bij om het [nieuwe certificaat](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)te gebruiken. Zie [geplande certificaat updates](concepts-certificate-rotation.md) voor meer informatie.

Raadpleeg de volgende koppelingen voor certificaten voor servers in soevereine Clouds: [Azure Government](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem), [Azure China](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)en [Azure Duitsland](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt).

## <a name="step-2-bind-ssl"></a>Stap 2: SSL binden

Raadpleeg de onderstaande [voorbeeld code](howto-configure-ssl.md#sample-code) voor specifieke verbindings reeksen voor programmeer talen.

### <a name="connecting-to-server-using-mysql-workbench-over-ssl"></a>Verbinding maken met de server via MySQL Workbench via SSL

MySQL Workbench configureren om veilig te verbinden via SSL.

1. Ga in het dialoog venster nieuwe verbinding instellen naar het tabblad **SSL** .

1. Werk het veld **SSL gebruiken** bij naar vereist.

1. Voer in het veld **SSL-ca-bestand:** de bestands locatie in van de **BaltimoreCyberTrustRoot. CRT. pem**.

   :::image type="content" source="./media/howto-configure-ssl/mysql-workbench-ssl.png" alt-text="SSL-configuratie opslaan":::

Voor bestaande verbindingen kunt u SSL koppelen door met de rechter muisknop op het verbindings pictogram te klikken en bewerken te kiezen. Ga vervolgens naar het tabblad **SSL** en bind het certificaat bestand.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Verbinding maken met de server via de MySQL CLI via SSL

Een andere manier om het SSL-certificaat te koppelen, is door de volgende opdrachten uit te voeren via de MySQL-opdracht regel interface.

```bash
mysql.exe -h mydemoserver.mysql.database.azure.com -u Username@mydemoserver -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

> [!NOTE]
> Wanneer u de MySQL-opdracht regel interface in Windows gebruikt, wordt er mogelijk een fout bericht weer gegeven `SSL connection error: Certificate signature check failed` . Als dit het geval is, vervangt u de `--ssl-mode=REQUIRED --ssl-ca={filepath}` para meters door `--ssl` .

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>Stap 3: SSL-verbindingen in azure afdwingen

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Ga met behulp van de Azure Portal naar uw Azure Database for MySQL-server en klik vervolgens op **verbindings beveiliging**. Gebruik de wissel knop om de instelling SSL- **verbinding afdwingen** in of uit te scha kelen en klik vervolgens op **Opslaan**. Micro soft adviseert altijd de instelling **SSL-verbinding afdwingen** in te scha kelen voor verbeterde beveiliging.

:::image type="content" source="./media/howto-configure-ssl/enable-ssl.png" alt-text="Scherm opname van Azure Portal voor het afdwingen van SSL-verbindingen in Azure Database for MySQL":::

### <a name="using-azure-cli"></a>Azure CLI gebruiken

U kunt de **SSL-afdwinging-** para meter in-of uitschakelen door respectievelijk ingeschakelde of uitgeschakelde waarden in azure CLI te gebruiken.

```azurecli-interactive
az mysql server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>Stap 4: de SSL-verbinding controleren

Voer de MySQL- **status** opdracht uit om te controleren of u verbinding hebt met de mysql-server met behulp van SSL:

```dos
mysql> status
```

Controleer of de verbinding is versleuteld door de uitvoer te bekijken. deze moet er als volgt uitzien:  **SSL: code in gebruik is AES256-SHA**

## <a name="sample-code"></a>Voorbeeldcode

Raadpleeg de volgende code voorbeelden om een beveiligde verbinding tot stand te brengen met Azure Database for MySQL via SSL van uw toepassing:

Raadpleeg de lijst met [compatibele Stuur Programma's](concepts-compatibility.md) die worden ondersteund door de Azure database for MySQL-service.

### <a name="php"></a>PHP

```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

### <a name="php-using-pdo"></a>PHP (met Bob)

```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'username@mydemoserver', 'yourpassword', $options);
```

### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector python)

```python
try:
    conn = mysql.connector.connect(user='myadmin@mydemoserver',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mysql.database.azure.com',
                                   ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```

### <a name="python-pymysql"></a>Python (PyMySQL)

```python
conn = pymysql.connect(user='myadmin@mydemoserver',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mysql.database.azure.com',
                       ssl={'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'})
```

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
            'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}
        }
    }
}
```

### <a name="ruby"></a>Ruby

```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com',
        :username => 'myadmin@mydemoserver',
        :password => 'yourpassword',
        :database => 'quickstartdb',
        :sslca => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
    )
```

### <a name="golang"></a>Golang

```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/BaltimoreCyberTrustRoot.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@mydemoserver' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')
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
properties.setProperty("user", 'myadmin@mydemoserver');
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
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)

```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin@mydemoserver",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    SslCa = "BaltimoreCyberTrustRoot.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

## <a name="next-steps"></a>Volgende stappen

Verschillende connectiviteits opties voor toepassingen bekijken volgende [verbindings bibliotheken voor Azure database for MySQL](concepts-connection-libraries.md)
