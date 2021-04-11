---
title: Inleiding tot micro soft Spark-hulpprogram ma's
description: 'Zelf studie: MSSparkutils in azure Synapse Analytics-notebooks'
author: ruixinxu
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: spark
ms.date: 09/10/2020
ms.author: ruxu
ms.reviewer: ''
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: 8b3bc99d4391e2079d1b0ecc39011f1b2afc4440
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106096033"
---
# <a name="introduction-to-microsoft-spark-utilities"></a>Inleiding tot micro soft Spark-Hulpprogram Ma's

Micro soft Spark-Hulpprogram Ma's (MSSparkUtils) is een ingebouwd pakket waarmee u eenvoudig veelvoorkomende taken kunt uitvoeren. U kunt MSSparkUtils gebruiken om te werken met bestands systemen, omgevings variabelen op te halen en te werken met geheimen. MSSparkUtils zijn beschikbaar in `PySpark (Python)` , `Scala` , en `.NET Spark (C#)` notebooks en Synapse-pijp lijnen.

## <a name="pre-requisites"></a>Vereisten

### <a name="configure-access-to-azure-data-lake-storage-gen2"></a>Toegang tot Azure Data Lake Storage Gen2 configureren 

Synapse-notebooks gebruiken Azure Active Directory (Azure AD) pass-through om toegang te krijgen tot de ADLS Gen2 accounts. U moet een Inzender voor **opslag-blobs** hebben om toegang te krijgen tot het ADLS Gen2 account (of de map). 

Synapse-pipelines gebruiken werk ruimte-identiteit (MSI) voor toegang tot de opslag accounts. Als u MSSparkUtils in uw pijplijn activiteiten wilt gebruiken, moet de identiteit van de werk **ruimte worden gebruikt om toegang** te krijgen tot het ADLS Gen2 account (of de map).

Volg deze stappen om ervoor te zorgen dat uw Azure AD-en Workspace MSI toegang hebben tot het ADLS Gen2-account:
1. Open de [Azure Portal](https://portal.azure.com/) en het opslag account dat u wilt openen. U kunt naar de specifieke container navigeren die u wilt openen.
2. Selecteer **toegangs beheer (IAM)** in het linkerdeel venster.
3. Wijs **uw Azure ad-account** en **de identiteit van uw werk ruimte** (hetzelfde als de naam van uw werk ruimte) toe aan de rol voor het overdragen van **blobgegevens** op het opslag account als deze nog niet is toegewezen. 
4. Selecteer **Opslaan**.

U kunt toegang krijgen tot gegevens op ADLS Gen2 met Synapse Spark via de volgende URL:

<code>abfss://<container_name>@<storage_account_name>.dfs.core.windows.net/<path></code>

### <a name="configure-access-to-azure-blob-storage"></a>Toegang tot Azure Blob Storage configureren  

Synapse maakt gebruik van **Shared Access Signature (SAS)** voor toegang tot Azure Blob Storage. Om te voor komen dat SAS-sleutels in de code worden weer gegeven, kunt u het beste een nieuwe gekoppelde service in de Synapse-werk ruimte maken voor het Azure Blob Storage-account dat u wilt openen.

Volg deze stappen om een nieuwe gekoppelde service voor een Azure Blob Storage-account toe te voegen:

1. Open [Azure Synapse Studio](https://web.azuresynapse.net/).
2. Selecteer **beheren** in het linkerdeel venster en selecteer **gekoppelde services** onder de **externe verbindingen**.
3. Zoek in **Azure Blob Storage** in het deel venster **nieuwe gekoppelde service** aan de rechter kant.
4. Selecteer **Doorgaan**.
5. Selecteer de Azure Blob Storage-account om de naam van de gekoppelde service te openen en te configureren. Suggesties voor het gebruik van de **account sleutel** voor de **verificatie methode**.
6. Selecteer **verbinding testen** om te controleren of de instellingen juist zijn.
7. Selecteer eerste **maken** en klik op **Alles publiceren** om uw wijzigingen op te slaan. 

U kunt toegang krijgen tot gegevens op Azure Blob Storage met Synapse Spark via de volgende URL:

<code>wasb[s]://<container_name>@<storage_account_name>.blob.core.windows.net/<path></code>

Hier volgt een code voorbeeld:

:::zone pivot = "programming-language-python"

```python
from pyspark.sql import SparkSession

# Azure storage access info
blob_account_name = 'Your account name' # replace with your blob name
blob_container_name = 'Your container name' # replace with your container name
blob_relative_path = 'Your path' # replace with your relative folder path
linked_service_name = 'Your linked service name' # replace with your linked service name

blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

# Allow SPARK to access from Blob remotely

wasb_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)

spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
print('Remote blob path: ' + wasb_path)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val blob_account_name = "" // replace with your blob name
val blob_container_name = "" //replace with your container name
val blob_relative_path = "/" //replace with your relative folder path
val linked_service_name = "" //replace with your linked service name


val blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

val wasbs_path = f"wasbs://$blob_container_name@$blob_account_name.blob.core.windows.net/$blob_relative_path"
spark.conf.set(f"fs.azure.sas.$blob_container_name.$blob_account_name.blob.core.windows.net",blob_sas_token)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var blob_account_name = "";  // replace with your blob name
var blob_container_name = "";     // replace with your container name
var blob_relative_path = "";  // replace with your relative folder path
var linked_service_name = "";    // replace with your linked service name
var blob_sas_token = Credentials.GetConnectionStringOrCreds(linked_service_name);

spark.SparkContext.GetConf().Set($"fs.azure.sas.{blob_container_name}.{blob_account_name}.blob.core.windows.net", blob_sas_token);

var wasbs_path = $"wasbs://{blob_container_name}@{blob_account_name}.blob.core.windows.net/{blob_relative_path}";

Console.WriteLine(wasbs_path);

```

::: zone-end 
 
###  <a name="configure-access-to-azure-key-vault"></a>Toegang tot Azure Key Vault configureren

U kunt een Azure Key Vault toevoegen als een gekoppelde service om uw referenties in Synapse te beheren. Volg deze stappen om een Azure Key Vault toe te voegen als een Synapse-gekoppelde service:
1. Open [Azure Synapse Studio](https://web.azuresynapse.net/).
2. Selecteer **beheren** in het linkerdeel venster en selecteer **gekoppelde services** onder de **externe verbindingen**.
3. Zoek **Azure Key Vault** in het deel venster **nieuwe gekoppelde service** aan de rechter kant.
4. Selecteer het Azure Key Vault-account om de naam van de gekoppelde service te openen en te configureren.
5. Selecteer **verbinding testen** om te controleren of de instellingen juist zijn.
6. Selecteer eerste **maken** en klik op **Alles publiceren** om de wijziging op te slaan. 

Synapse-notebooks gebruiken Azure Active Directory (Azure AD) pass-through om toegang te krijgen tot Azure Key Vault. Synapse-pipelines gebruiken werk ruimte-identiteit (MSI) voor toegang tot Azure Key Vault. Om ervoor te zorgen dat uw code zowel in notebook als in Synapse-pijp lijn werkt, kunt u het beste een geheime toegangs machtiging verlenen voor zowel uw Azure AD-account als uw werk ruimte-id.

Voer de volgende stappen uit om de identiteit van uw werk ruimte geheim te geven:
1. Open de [Azure Portal](https://portal.azure.com/) en de Azure Key Vault die u wilt openen. 
2. Selecteer het **toegangs beleid** in het linkerdeel venster.
3. Selecteer **toegangs beleid toevoegen**: 
    - Kies **sleutel, geheim, & certificaat beheer** als configuratie sjabloon.
    - Selecteer **uw Azure ad-account** en **uw werk ruimte-id** (hetzelfde als de naam van uw werk ruimte) in de principal selecteren of zorg ervoor dat deze al is toegewezen. 
4. Selecteer **selecteren** en **toevoegen**.
5. Selecteer de knop **Opslaan** om wijzigingen door te voeren.  

## <a name="file-system-utilities"></a>Hulpprogram ma's voor bestands systeem

`mssparkutils.fs` voorziet in hulpprogram ma's voor het werken met verschillende bestands systemen, waaronder Azure Data Lake Storage Gen2 (ADLS Gen2) en Azure Blob Storage. Zorg ervoor dat u de toegang tot [Azure data Lake Storage Gen2](#configure-access-to-azure-data-lake-storage-gen2) en [Azure-Blob Storage](#configure-access-to-azure-blob-storage) op de juiste manier configureert.

Voer de volgende opdrachten uit voor een overzicht van de beschik bare methoden:

:::zone pivot = "programming-language-python"

```python
from notebookutils import mssparkutils
mssparkutils.fs.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Notebook.MSSparkUtils;
FS.Help()
```

::: zone-end

Resulteert in:
```
mssparkutils.fs provides utilities for working with various FileSystems.

Below is overview about the available methods:

cp(from: String, to: String, recurse: Boolean = false): Boolean -> Copies a file or directory, possibly across FileSystems
mv(from: String, to: String, recurse: Boolean = false): Boolean -> Moves a file or directory, possibly across FileSystems
ls(dir: String): Array -> Lists the contents of a directory
mkdirs(dir: String): Boolean -> Creates the given directory if it does not exist, also creating any necessary parent directories
put(file: String, contents: String, overwrite: Boolean = false): Boolean -> Writes the given String out to a file, encoded in UTF-8
head(file: String, maxBytes: int = 1024 * 100): String -> Returns up to the first 'maxBytes' bytes of the given file as a String encoded in UTF-8
append(file: String, content: String, createFileIfNotExists: Boolean): Boolean -> Append the content to a file
rm(dir: String, recurse: Boolean = false): Boolean -> Removes a file or directory

Use mssparkutils.fs.help("methodName") for more info about a method.

```

### <a name="list-files"></a>Bestanden in een lijst weergeven
De inhoud van een map weer geven.


:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.ls('Your directory path')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.ls("Your directory path")
```
::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Ls("Your directory path")
```

::: zone-end


### <a name="view-file-properties"></a>Bestands eigenschappen weer geven
Retourneert bestands eigenschappen, inclusief de bestands naam, het bestandspad, de bestands grootte en of het een map en een bestand is.

:::zone pivot = "programming-language-python"

```python
files = mssparkutils.fs.ls('Your directory path')
for file in files:
    print(file.name, file.isDir, file.isFile, file.path, file.size)
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val files = mssparkutils.fs.ls("/")
files.foreach{
    file => println(file.name,file.isDir,file.isFile,file.size)
}
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var Files = FS.Ls("/");
foreach(var File in Files) {
    Console.WriteLine(File.Name+" "+File.IsDir+" "+File.IsFile+" "+File.Size);
}
```

::: zone-end

### <a name="create-new-directory"></a>Nieuwe map maken

Maakt de gegeven map als deze nog niet bestaat en alle benodigde bovenliggende mappen bevat.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.mkdirs('new directory name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.mkdirs("new directory name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Mkdirs("new directory name")
```

::: zone-end

### <a name="copy-file"></a>Bestand kopiëren

Hiermee wordt een bestand of map gekopieerd. Ondersteunt kopiëren over bestands systemen.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.cp('source file or directory', 'destination file or directory', True)# Set the third parameter as True to copy all files and directories recursively
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.cp("source file or directory", "destination file or directory", true) // Set the third parameter as True to copy all files and directories recursively
```
::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Cp("source file or directory", "destination file or directory", true) // Set the third parameter as True to copy all files and directories recursively
```

::: zone-end

### <a name="preview-file-content"></a>Voor beeld van bestands inhoud

Retourneert de eerste ' maxBytes ' bytes van het opgegeven bestand als een teken reeks die is gecodeerd in UTF-8.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.head('file path', maxBytes to read)
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.head("file path", maxBytes to read)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Head("file path", maxBytes to read)
```

::: zone-end

### <a name="move-file"></a>Bestand verplaatsen

Hiermee verplaatst u een bestand of map. Ondersteunt verplaatsen tussen bestands systemen.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.mv('source file or directory', 'destination directory', True) # Set the last parameter as True to firstly create the parent directory if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.mv("source file or directory", "destination directory", true) // Set the last parameter as True to firstly create the parent directory if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Mv("source file or directory", "destination directory", true)
```

::: zone-end

### <a name="write-file"></a>Bestand schrijven

Schrijft de gegeven teken reeks naar een bestand dat is gecodeerd in UTF-8.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.put("file path", "content to write", True) # Set the last parameter as True to overwrite the file if it existed already
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.put("file path", "content to write", true) // Set the last parameter as True to overwrite the file if it existed already
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Put("file path", "content to write", true) // Set the last parameter as True to overwrite the file if it existed already
```

::: zone-end

### <a name="append-content-to-a-file"></a>Inhoud toevoegen aan een bestand

Hiermee wordt de opgegeven teken reeks toegevoegd aan een bestand, gecodeerd in UTF-8.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.append('file path','content to append',True) # Set the last parameter as True to create the file if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.append("file path","content to append",true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Append("file path","content to append",true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

### <a name="delete-file-or-directory"></a>Bestand of map verwijderen

Hiermee verwijdert u een bestand of map.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.rm('file path', True) # Set the last parameter as True to remove all files and directories recursively 
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.rm("file path", true) // Set the last parameter as True to remove all files and directories recursively 
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Rm("file path", true) // Set the last parameter as True to remove all files and directories recursively 
```

::: zone-end


## <a name="credentials-utilities"></a>Hulpprogram ma's voor referenties

U kunt de MSSparkUtils-Hulpprogram Ma's voor referenties gebruiken voor het ophalen van de toegangs tokens van gekoppelde services en het beheren van geheimen in Azure Key Vault. 

Voer de volgende opdracht uit om een overzicht te krijgen van de beschik bare methoden:

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.Help()
```

::: zone-end

Resultaat ophalen:

```
getToken(audience, name): returns AAD token for a given audience, name (optional)
isValidToken(token): returns true if token hasn't expired
getConnectionStringOrCreds(linkedService): returns connection string or credentials for linked service
getSecret(akvName, secret, linkedService): returns AKV secret for a given AKV linked service, akvName, secret key
getSecret(akvName, secret): returns AKV secret for a given akvName, secret key
putSecret(akvName, secretName, secretValue, linkedService): puts AKV secret for a given akvName, secretName
putSecret(akvName, secretName, secretValue): puts AKV secret for a given akvName, secretName
```

### <a name="get-token"></a>Token ophalen

Retourneert een Azure AD-token voor een bepaalde doel groep, naam (optioneel). In de volgende tabel worden alle beschik bare typen doel groepen weer gegeven: 

|Type doel groep|Sleutel van doel groep|
|--|--|
|Type doel groep oplossen|Gericht|
|Bron van opslag publiek|Opslagpad|
|Bron van de doel groep van het Data Warehouse|DW|
|Bron van Data Lake doel groep|Beheer|
|Resource van kluis doelgroep|Data Lake Store|
|Azure OSSDB-doel resource|'AzureOSSDB'|
|Azure Synapse-resource|'Synapse'|
|Azure Data Factory resource|ADF|

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getToken('audience Key')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getToken("audience Key")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetToken("audience Key")
```

::: zone-end


### <a name="validate-token"></a>Token valideren

Retourneert waar als het token niet is verlopen.

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.isValidToken('your token')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.isValidToken("your token")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.IsValidToken("your token")
```

::: zone-end


### <a name="get-connection-string-or-credentials-for-linked-service"></a>connection string of referenties voor de gekoppelde service ophalen

Retourneert connection string of referenties voor de gekoppelde service. 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getConnectionStringOrCreds('linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getConnectionStringOrCreds("linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetConnectionStringOrCreds("linked service name")
```

::: zone-end


### <a name="get-secret-using-workspace-identity"></a>Geheim ophalen met werkruimte identiteit

Retourneert Azure Key Vault geheim voor een opgegeven Azure Key Vault naam, geheime naam en gekoppelde service naam met behulp van werk ruimte-identiteit. Zorg ervoor dat u de toegang tot [Azure Key Vault](#configure-access-to-azure-key-vault) op de juiste manier configureert.

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getSecret('azure key vault name','secret name','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getSecret("azure key vault name","secret name","linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetSecret("azure key vault name","secret name","linked service name")
```

::: zone-end


### <a name="get-secret-using-user-credentials"></a>Geheim ophalen met gebruikers referenties

Retourneert Azure Key Vault geheim voor een opgegeven Azure Key Vault naam, geheime naam en gekoppelde service naam met behulp van gebruikers referenties. 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getSecret('azure key vault name','secret name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getSecret("azure key vault name","secret name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetSecret("azure key vault name","secret name")
```

::: zone-end

<!-- ### Put secret using workspace identity

Puts Azure Key Vault secret for a given Azure Key Vault name, secret name, and linked service name using workspace identity. Make sure you configure the access to [Azure Key Vault](#configure-access-to-azure-key-vault) appropriately. -->

:::zone pivot = "programming-language-python"

### <a name="put-secret-using-workspace-identity"></a>Geheim zetten met werkruimte identiteit

Hiermee wordt Azure Key Vault geheim geplaatst voor een bepaalde Azure Key Vault naam, geheime naam en gekoppelde service naam met behulp van werk ruimte-identiteit. Zorg ervoor dat u de toegang tot [Azure Key Vault](#configure-access-to-azure-key-vault) op de juiste manier configureert.

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

### <a name="put-secret-using-workspace-identity"></a>Geheim zetten met werkruimte identiteit

Hiermee wordt Azure Key Vault geheim geplaatst voor een bepaalde Azure Key Vault naam, geheime naam en gekoppelde service naam met behulp van werk ruimte-identiteit. Zorg ervoor dat u de toegang tot [Azure Key Vault](#configure-access-to-azure-key-vault) op de juiste manier configureert.

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value","linked service name")
```

::: zone-end

<!-- :::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end -->


<!-- ### Put secret using user credentials

Puts Azure Key Vault secret for a given Azure Key Vault name, secret name, and linked service name using user credentials.  -->

:::zone pivot = "programming-language-python"

### <a name="put-secret-using-user-credentials"></a>Geheim zetten met gebruikers referenties

Plaatst Azure Key Vault geheim voor een bepaalde Azure Key Vault naam, geheime naam en gekoppelde service naam met behulp van gebruikers referenties. 

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value')
```
::: zone-end

:::zone pivot = "programming-language-scala"

### <a name="put-secret-using-user-credentials"></a>Geheim zetten met gebruikers referenties

Plaatst Azure Key Vault geheim voor een bepaalde Azure Key Vault naam, geheime naam en gekoppelde service naam met behulp van gebruikers referenties. 

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value")
```

::: zone-end

<!-- :::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end -->


## <a name="environment-utilities"></a>Omgevings hulpprogramma's 

Voer de volgende opdrachten uit om een overzicht te krijgen van de beschik bare methoden:

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.Help()
```

::: zone-end

Resultaat ophalen:
```
GetUserName(): returns user name
GetUserId(): returns unique user id
GetJobId(): returns job id
GetWorkspaceName(): returns workspace name
GetPoolName(): returns Spark pool name
GetClusterId(): returns cluster id
```

### <a name="get-user-name"></a>Gebruikers naam ophalen

Hiermee wordt de huidige gebruikers naam geretourneerd.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getUserName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getUserName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetUserName()
```

::: zone-end

### <a name="get-user-id"></a>Gebruikers-ID ophalen

Retourneert de huidige gebruikers-ID.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getUserId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getUserId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetUserId()
```

::: zone-end

### <a name="get-job-id"></a>Taak-ID ophalen

Retourneert de taak-ID.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getJobId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getJobId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetJobId()
```

::: zone-end

### <a name="get-workspace-name"></a>Werkruimte naam ophalen

Retourneert de naam van een werk ruimte.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getWorkspaceName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getWorkspaceName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetWorkspaceName()
```

::: zone-end

### <a name="get-pool-name"></a>Groeps naam ophalen

Retourneert de naam van de Spark-groep.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getPoolName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getPoolName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetPoolName()
```

::: zone-end

### <a name="get-cluster-id"></a>Cluster-ID ophalen

Hiermee wordt de huidige cluster-ID geretourneerd.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getClusterId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getClusterId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetClusterId()
```

::: zone-end

## <a name="next-steps"></a>Volgende stappen

- [Voor beelden van Synapse-voorbeeld notitieblokken bekijken](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Quickstart: Een Apache Spark-pool maken in Azure Synapse Analytics met behulp van webhulpprogramma's](../quickstart-apache-spark-notebook.md)
- [Wat is Apache Spark in Azure Synapse Analytics?](apache-spark-overview.md)
- [Azure Synapse Analytics](../index.yml)
