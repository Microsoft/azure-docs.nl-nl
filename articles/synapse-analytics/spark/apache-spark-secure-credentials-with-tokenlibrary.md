---
title: Toegangsreferenties beveiligen met gekoppelde services in Apache Spark voor Azure Synapse Analytics
description: Dit artikel biedt concepten over hoe u Apache Spark voor Azure Synapse Analytics veilig kunt integreren met andere services met behulp van gekoppelde services en een tokenbibliotheek
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 11/19/2020
ms.author: martinle
ms.reviewer: nirav
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: a588b37b270917524453419619fdad6f88f92338
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101693574"
---
# <a name="secure-credentials-with-linked-services-using-the-tokenlibrary"></a>Referenties beveiligen met gekoppelde services met behulp van TokenLibrary

Toegang tot gegevens uit externe bronnen verkrijgen is een veelvoorkomend patroon. Tenzij de externe gegevensbron anonieme toegang toestaat, is de kans groot dat u uw verbinding moet beveiligen met een referentie, geheim of verbindingsreeks.  

Synapse maakt standaard gebruik van AAD-passthrough (Azure Active Directory) voor verificatie tussen resources.  Als u verbinding wilt maken met een resource met behulp van andere referenties, moet u TokenLibrary rechtstreeks gebruiken.  TokenLibrary vereenvoudigt het proces van het ophalen van SAS-tokens, AAD-tokens, verbindingsreeksen en geheimen die zijn opgeslagen in een gekoppelde service, of vanuit Azure Key Vault.

Bij het ophalen van geheimen uit Azure Key Vault wordt u aangeraden een gekoppelde service te maken voor uw Azure Key Vault.  Zorg ervoor dat de MSI (beheerde service-identiteit) van de Synapse-werkruimte, Secret Get-bevoegdheden heeft voor uw Azure Key Vault.  Synapse verifieert zich bij Azure Key Vault met behulp van de MSI van de Synapse-werkruimte. Als u rechtstreeks verbinding maakt met Azure Key Vault zonder een gekoppelde service, voert u verificatie uit door middel van de Azure Active Directory-gebruikersreferenties.

Zie [gekoppelde services](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) voor meer informatie.

## <a name="usage"></a>Gebruik

### <a name="tokenlibraryhelp"></a>TokenLibrary.help()
Met deze functie wordt de Help-documentatie voor TokenLibrary weergegeven.

::: zone pivot = "programming-language-scala"

```scala
TokenLibrary.help()
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
TokenLibrary.help()
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
Console.WriteLine(TokenLibrary.help());
```

::: zone-end

## <a name="tokenlibrary-for-azure-data-lake-storage-gen2"></a>TokenLibrary voor Azure Data Lake Storage Gen2

#### <a name="adls-gen2-primary-storage"></a>Primaire opslag van ADLS Gen2

Voor het openen van bestanden vanuit de primaire Azure Data Lake Storage gebruikt u standaard Azure Active Directory-passthrough voor verificatie en hoeft u TokenLibrary niet expliciet te gebruiken.

::: zone pivot = "programming-language-scala"

```scala
val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")
display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')
display(df.limit(10))
```

::: zone-end

#### <a name="adls-gen2-storage-with-linked-services"></a>Opslag met gekoppelde services van ADLS Gen2

Synapse biedt een geïntegreerde ervaring met gekoppelde services bij het verbinding maken met Azure Data Lake Storage Gen2.  Gekoppelde services kunnen worden geconfigureerd om te worden geverifieerd met behulp van een **accountsleutel**, **service-principal**, **beheerde identiteit** of **referentie**.

Wanneer de verificatiemethode met de gekoppelde service is ingesteld op **accountsleutel**, wordt de gekoppelde service geverifieerd met behulp van de opgegeven sleutel voor het opslagaccount, wordt een SAS-sleutel aangevraagd en wordt deze automatisch toegepast op de opslagaanvraag met de **LinkedServiceBasedSASProvider**-provider.

::: zone pivot = "programming-language-scala"

```scala
val sc = spark.sparkContext
spark.conf.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# Python code
spark.conf.set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>')

df.show()
```

::: zone-end

Wanneer de verificatiemethode met de gekoppelde service is ingesteld op **beheerde identiteit** of **service-principal**, maakt de gekoppelde service gebruik van het token van de beheerde identiteit of van de service-principal met de **LinkedServiceBasedTokenProvider**-provider.  


::: zone pivot = "programming-language-scala"

```scala
val sc = spark.sparkContext
spark.conf.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.oauth.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedTokenProvider") 
val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# Python code
spark.conf.set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedTokenProvider")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>')

df.show()
```

::: zone-end

#### <a name="adls-gen2-storage-without-linked-services"></a>Opslag (zonder gekoppelde services) van ADLS Gen2

Maak rechtstreeks verbinding met ADLS Gen2 Storage met behulp van een SAS-sleutel, gebruik **ConfBasedSASProvider** en geef de SAS-sleutel op in de configuratie-instelling **spark.storage.synapse.sas**.

::: zone pivot = "programming-language-scala"

```scala
%%spark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.ConfBasedSASProvider")
spark.conf.set("spark.storage.synapse.sas", "<SAS KEY>")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark

spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.ConfBasedSASProvider")
spark.conf.set("spark.storage.synapse.sas", "<SAS KEY>")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')

display(df.limit(10))
```

::: zone-end

#### <a name="adls-gen2-storage-with-azure-key-vault"></a>Opslag van ADLS Gen2 met Azure Key Vault

Maak verbinding met ADLS Gen2-opslag met behulp van een SAS-token dat is opgeslagen in een Azure Key Vault-geheim.  

::: zone pivot = "programming-language-scala"

```scala
%%spark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.AkvBasedSASProvider")
spark.conf.set("spark.storage.synapse.akv", "<AZURE KEY VAULT NAME>")
spark.conf.set("spark.storage.akv.secret", "<SECRET KEY>")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.AkvBasedSASProvider")
spark.conf.set("spark.storage.synapse.akv", "<AZURE KEY VAULT NAME>")
spark.conf.set("spark.storage.akv.secret", "<SECRET KEY>")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')

display(df.limit(10))
```

::: zone-end

## <a name="tokenlibrary-for-other-linked-services"></a>TokenLibrary voor andere gekoppelde services

Om verbinding te maken met andere gekoppelde services, kunt u de tokenbibliotheek rechtstreeks aanroepen.

#### <a name="getconnectionstring"></a>getConnectionString()

 Om de verbindingsreeks op te halen, gebruikt u de functie **getConnectionString** en geeft u de **naam van de gekoppelde service** door.

::: zone pivot = "programming-language-scala"

```scala
%%spark
// retrieve connectionstring from TokenLibrary

import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getConnectionString("<LINKED SERVICE NAME>")
println(connectionString)
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# retrieve connectionstring from TokenLibrary

from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
connection_string = token_library.getConnectionString("<LINKED SERVICE NAME>")
print(connection_string)
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
%%csharp
// retrieve connectionstring from TokenLibrary

using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.GetConnectionString(<LINKED SERVICE NAME>);
Console.WriteLine(connectionString);
```

::: zone-end

#### <a name="getconnectionstringasmap"></a>getConnectionStringAsMap()

getConnectionStringAsMap is een helperfunctie die beschikbaar is in Scala en Python om specifieke waarden te parseren van een _key=value_-paar in de verbindingsreeks, zoals

_`DefaultEndpointsProtocol=https;AccountName=<ACCOUNT NAME>;AccountKey=<ACCOUNT KEY>`_

gebruikt u de functie **getConnectionStringAsMap** en geeft u de sleutel door om de waarde te retourneren.  In het bovenstaande voorbeeld met de verbindingsreeks: 

_**TokenLibrary.getConnectionStringAsMap("DefaultEndpointsProtocol")**_

retourneert

**_"https"_**

::: zone pivot = "programming-language-scala"

```scala
// Linked services can be used for storing and retrieving credentials (e.g, account key)
// Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val accountKey: String = TokenLibrary.getConnectionStringAsMap("<LINKED SERVICE NAME">).get("<KEY NAME>")
println(accountKey)
```
::: zone-end

::: zone pivot = "programming-language-python"

```python
# Linked services can be used for storing and retrieving credentials (e.g, account key)
# Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
accountKey = token_library.getConnectionStringAsMap("<LINKED SERVICE NAME>").get("<KEY NAME>")
print(accountKey)
```

::: zone-end

#### <a name="getsecret"></a>getSecret()

Als u een geheim wilt ophalen dat is opgeslagen in Azure Key Vault, kunt u het beste een gekoppelde service naar Azure Key Vault maken in de Synapse-werkruimte. De beheerde service-identiteit van de Synapse-werkruimte moet de machtiging **GET** Secret worden verleend voor Azure Key Vault.  De gekoppelde service maakt gebruik van de beheerde service-identiteit om verbinding te maken met de Azure Key Vault-service om het geheim op te halen.  Als rechtstreeks verbinding wordt gemaakt met Azure Key Vault moet daarentegen de AAD-gebruikersreferentie (Azure Active Directory) worden gebruikt.  In dit geval moet de gebruiker de GET Secret-machtigingen in Azure Key Vault worden verleend.

`TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>" [, <LINKED SERVICE NAME>])`

Als u een geheim wilt ophalen uit Azure Key Vault, gebruikt u de functie **TokenLibrary.getSecret()** .

::: zone pivot = "programming-language-scala"

```scala
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>")
println(connectionString)
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
import sys
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

connection_string = token_library.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>")
print(connection_string)
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>");
Console.WriteLine(connectionString);
```

::: zone-end

## <a name="next-steps"></a>Volgende stappen

- [Schrijven naar toegewezen SQL-pool](./synapse-spark-sql-pool-import-export.md)
