---
title: Inleiding tot Microsoft Spark-hulpprogramma's
description: 'Zelfstudie: MSSparkutils in Azure Synapse Analytics notebooks'
author: ruixinxu
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: spark
ms.date: 09/10/2020
ms.author: ruxu
ms.reviewer: ''
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: cccfb71f485268f5eb5f9ea9b7275618e840737b
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107887540"
---
# <a name="introduction-to-microsoft-spark-utilities"></a>Inleiding tot Microsoft Spark-hulpprogramma's

Microsoft Spark Utilities (MSSparkUtils) is een ingebouwd pakket om u te helpen eenvoudig algemene taken uit te voeren. U kunt MSSparkUtils gebruiken om met bestandssystemen te werken, omgevingsvariabelen op te halen, notebooks aan elkaar te ketenen en met geheimen te werken. MSSparkUtils is beschikbaar in `PySpark (Python)` `Scala` , en `.NET Spark (C#)` notebooks en Synapse-pijplijnen.

## <a name="pre-requisites"></a>Vereisten

### <a name="configure-access-to-azure-data-lake-storage-gen2"></a>Toegang tot Azure Data Lake Storage Gen2 

Synapse-notebooks gebruiken pass-through van Azure Active Directory (Azure AD) om toegang te krijgen tot de ADLS Gen2 accounts. U moet een Bijdrager voor **opslagblobgegevens** zijn om toegang te krijgen tot ADLS Gen2 account (of map). 

Synapse-pijplijnen gebruiken werkruimte-id (MSI) voor toegang tot de opslagaccounts. Als u MSSparkUtils wilt gebruiken in uw pijplijnactiviteiten, moet uw werkruimte-id Bijdrager voor opslagblobgegevens zijn om **toegang** te krijgen tot ADLS Gen2 account (of map).

Volg deze stappen om ervoor te zorgen dat uw Azure AD- en werkruimte-MSI toegang hebben tot het ADLS Gen2 account:
1. Open het [Azure Portal](https://portal.azure.com/) en het opslagaccount dat u wilt openen. U kunt naar de specifieke container navigeren die u wilt openen.
2. Selecteer **Toegangsbeheer (IAM) in** het linkerpaneel.
3. Wijs **uw Azure AD-account** en uw werkruimte-id  **(dezelfde** als de naam van uw werkruimte) toe aan de rol Bijdrager voor opslagblobgegevens in het opslagaccount als deze nog niet is toegewezen. 
4. Selecteer **Opslaan**.

U hebt toegang tot gegevens op ADLS Gen2 synapse Spark via de volgende URL:

<code>abfss://<container_name>@<storage_account_name>.dfs.core.windows.net/<path></code>

### <a name="configure-access-to-azure-blob-storage"></a>Toegang tot Azure Blob Storage  

Synapse gebruikt [**Shared Access Signature (SAS) voor**](https://docs.microsoft.com/azure/storage/common/storage-sas-overview) toegang tot Azure Blob Storage. Om te voorkomen dat SAS-sleutels in de code beschikbaar worden, raden we u aan een nieuwe gekoppelde service in de Synapse-werkruimte te maken voor het Azure Blob Storage-account dat u wilt openen.

Volg deze stappen om een nieuwe gekoppelde service toe te voegen voor een Azure Blob Storage account:

1. Open Azure Synapse [Studio.](https://web.azuresynapse.net/)
2. Selecteer **Beheren** in het linkerpaneel en selecteer **Gekoppelde services** onder Externe **verbindingen.**
3. Zoek **Azure Blob Storage** in het **deelvenster Nieuwe gekoppelde service** aan de rechterkant.
4. Selecteer **Doorgaan**.
5. Selecteer het Azure Blob Storage account om de naam van de gekoppelde service te openen en te configureren. Stel het gebruik **van accountsleutel** voor de **verificatiemethode voor.**
6. Selecteer **Verbinding testen om** te controleren of de instellingen juist zijn.
7. Selecteer **eerst Maken** en klik op Alles publiceren **om** uw wijzigingen op te slaan. 

U kunt toegang krijgen tot gegevens Azure Blob Storage synapse Spark via de volgende URL:

<code>wasb[s]://<container_name>@<storage_account_name>.blob.core.windows.net/<path></code>

Hier is een codevoorbeeld:

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
 
###  <a name="configure-access-to-azure-key-vault"></a>Toegang tot Azure Key Vault

U kunt een Azure Key Vault als gekoppelde service toevoegen om uw referenties in Synapse te beheren. Volg deze stappen om een Azure Key Vault als een gekoppelde Synapse-service toe te voegen:
1. Open Azure Synapse [Studio.](https://web.azuresynapse.net/)
2. Selecteer **Beheren** in het linkerpaneel en selecteer **Gekoppelde services** onder Externe **verbindingen.**
3. Zoek **Azure Key Vault** in het **deelvenster Nieuwe gekoppelde service** aan de rechterkant.
4. Selecteer het Azure Key Vault account om de naam van de gekoppelde service te openen en te configureren.
5. Selecteer **Verbinding testen om** te controleren of de instellingen juist zijn.
6. Selecteer **eerst Maken** en klik op Alles publiceren **om** uw wijziging op te slaan. 

Synapse-notebooks gebruiken pass-through van Azure Active Directory (Azure AD) om toegang te krijgen tot Azure Key Vault. Synapse-pijplijnen gebruiken werkruimte-id (MSI) voor toegang tot Azure Key Vault. Om ervoor te zorgen dat uw code zowel in notebook als in Synapse-pijplijn werkt, raden we u aan om geheime toegang te verlenen voor zowel uw Azure AD-account als werkruimte-identiteit.

Volg deze stappen om geheime toegang te verlenen tot uw werkruimte-identiteit:
1. Open de [Azure Portal](https://portal.azure.com/) en de Azure Key Vault u wilt openen. 
2. Selecteer **toegangsbeleid in** het linkerpaneel.
3. Selecteer **Toegangsbeleid toevoegen:** 
    - Kies **Sleutel, Geheim en & Certificaatbeheer als** configuratiesjabloon.
    - Selecteer **uw Azure AD-account** en uw werkruimte-id (dezelfde als de naam van uw werkruimte) in de principal selecteren of zorg ervoor dat deze al is toegewezen.  
4. Selecteer **Selecteren** en **Toevoegen.**
5. Selecteer de **knop Opslaan om** wijzigingen door te voeren.  

## <a name="file-system-utilities"></a>Hulpprogramma's voor bestandssysteem

`mssparkutils.fs` biedt hulpprogramma's voor het werken met verschillende bestandssystemen, Azure Data Lake Storage Gen2 (ADLS Gen2) en Azure Blob Storage. Zorg ervoor dat u de toegang tot [Azure Data Lake Storage Gen2](#configure-access-to-azure-data-lake-storage-gen2) en [Azure Blob Storage](#configure-access-to-azure-blob-storage) configureert.

Voer de volgende opdrachten uit voor een overzicht van de beschikbare methoden:

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
Vermeld de inhoud van een map.


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


### <a name="view-file-properties"></a>Bestandseigenschappen weergeven
Retourneert bestandseigenschappen, waaronder de bestandsnaam, het bestandspad, de bestandsgrootte en of het een map en een bestand is.

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

Hiermee maakt u de opgegeven map als deze niet bestaat en eventuele benodigde bovenliggende mappen.

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

Kopieert een bestand of map. Ondersteunt kopiëren tussen bestandssystemen.

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

### <a name="preview-file-content"></a>Voorbeeld van bestandsinhoud bekijken

Retourneert tot de eerste 'maxBytes'-bytes van het opgegeven bestand als een tekenreeks die is gecodeerd in UTF-8.

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

Hiermee verplaatst u een bestand of map. Ondersteunt het verplaatsen tussen bestandssystemen.

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

Schrijft de opgegeven tekenreeks naar een bestand, gecodeerd in UTF-8.

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

De opgegeven tekenreeks wordt toegevoegd aan een bestand, gecodeerd in UTF-8.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.append("file path", "content to append", True) # Set the last parameter as True to create the file if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.append("file path","content to append",true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Append("file path", "content to append", true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

### <a name="delete-file-or-directory"></a>Bestand of map verwijderen

Hiermee verwijdert u een bestand of een map.

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

:::zone pivot = "programming-language-python"

## <a name="notebook-utilities"></a>Notebook-hulpprogramma's 

U kunt de HULPPROGRAMMA's voor MSSparkUtils Notebook gebruiken om een notebook uit te voeren of een notebook af te sluiten met een waarde. Voer de volgende opdracht uit voor een overzicht van de beschikbare methoden:

```python
mssparkutils.notebook.help()
```

Resultaten krijgen:
```
The notebook module.

exit(value: String): void -> This method lets you exit a notebook with a value.
run(path: String, timeoutSeconds: int, arguments: Map): String -> This method runs a notebook and returns its exit value.

```

### <a name="run-a-notebook"></a>Een notebook uitvoeren
Voert een notebook uit en retourneert de afsluitende waarde. U kunt geneste functie-aanroepen interactief of in een pijplijn uitvoeren in een notebook. Het notebook waarnaar wordt verwezen, wordt uitgevoerd in de Spark-pool waarvan het notebook deze functie aanroept.  

```python

mssparkutils.notebook.run("notebook path", <timeoutSeconds>, <parameterMap>)

```

Bijvoorbeeld:

```python
mssparkutils.notebook.run("folder/Sample1", 90, {"input": 20 })
```

### <a name="exit-a-notebook"></a>Een notebook afsluiten
Sluit een notebook met een waarde af. U kunt aanroepen van geneste functies interactief of in een pijplijn uitvoeren in een notebook. 

- Wanneer u een functie interactief een notebook aanroept, Azure Synapse een uitzondering, slaat u het uitvoeren van subsequentiecellen over en houdt u `exit()` de Spark-sessie actief.

- Wanneer u een notebook in orchestrate dat een functie in een Synapse-pijplijn aanroept, retourneert Azure Synapse een exit-waarde, voltooit u de pijplijnuitleiding en stopt u de `exit()` Spark-sessie.  

- Wanneer u een functie aanroept in een notebook waarnaar wordt verwezen, stopt Azure Synapse de verdere uitvoering in het notebook waarnaar wordt verwezen en blijft u volgende cellen uitvoeren in het notebook die de functie `exit()` `run()` aanroepen. Bijvoorbeeld: Notebook1 heeft drie cellen en roept een functie `exit()` aan in de tweede cel. Notebook2 heeft vijf cellen en roept `run(notebook1)` aan in de derde cel. Wanneer u Notebook2 gebruikt, wordt Notebook1 bij de tweede cel gestopt wanneer de functie wordt `exit()` gebruikt. Notebook2 blijft de vierde cel en vijfde cel uitvoeren. 


```python
mssparkutils.notebook.exit("value string")
```

Bijvoorbeeld:

**Sample1** notebook zoekt onder **map/** met de volgende twee cellen: 
- cel 1 definieert **een invoerparameter** met de standaardwaarde ingesteld op 10.
- cel 2 sluit het notebook af met **invoer** als exit-waarde. 

![Schermopname van een voorbeeldnotenotenote](./media/microsoft-spark-utilities/spark-utilities-run-notebook-sample.png)

U kunt **Sample1 uitvoeren** in een ander notebook met standaardwaarden:

```python

exitVal = mssparkutils.notebook.run("folder/Sample1")
print (exitVal)

```
Resulteert in:

```
Sample1 run success with input is 10
```

U kunt **Sample1 uitvoeren** in een ander notebook en de **invoerwaarde** instellen op 20:

```python
exitVal = mssparkutils.notebook.run("mssparkutils/folder/Sample1", 90, {"input": 20 })
print (exitVal)
```

Resulteert in:

```
Sample1 run success with input is 20
```
::: zone-end


:::zone pivot = "programming-language-scala"

## <a name="notebook-utilities"></a>Notebook-hulpprogramma's 

U kunt de MSSparkUtils Notebook Utilities gebruiken om een notebook uit te voeren of een notebook met een waarde af te sluiten. Voer de volgende opdracht uit om een overzicht te krijgen van de beschikbare methoden:

```scala
mssparkutils.notebook.help()
```

Resultaten krijgen:
```
The notebook module.

exit(value: String): void -> This method lets you exit a notebook with a value.
run(path: String, timeoutSeconds: int, arguments: Map): String -> This method runs a notebook and returns its exit value.

```

### <a name="run-a-notebook"></a>Een notebook uitvoeren
Voert een notebook uit en retourneert de afsluitende waarde. U kunt geneste functie-aanroepen interactief of in een pijplijn uitvoeren in een notebook. Het notebook waarnaar wordt verwezen, wordt uitgevoerd in de Spark-pool waarvan de notebook deze functie aanroept.  

```scala

mssparkutils.notebook.run("notebook path", <timeoutSeconds>, <parameterMap>)

```

Bijvoorbeeld:

```scala
mssparkutils.notebook.run("folder/Sample1", 90, {"input": 20 })
```

### <a name="exit-a-notebook"></a>Een notebook afsluiten
Sluit een notebook met een waarde af. U kunt geneste functie-aanroepen interactief of in een pijplijn uitvoeren in een notebook. 

- Wanneer u een functie interactief aanroept, Azure Synapse een uitzondering, slaat u het uitvoeren van subsequentiecellen over en houdt u `exit()` de Spark-sessie actief.

- Wanneer u een notebook in orchestrat dat een functie in een Synapse-pijplijn aanroept, retourneert Azure Synapse een exit-waarde, voltooit u de pijplijnuit voeren en stopt u de `exit()` Spark-sessie.  

- Wanneer u een functie aanroept in een notebook waarnaar wordt verwezen, stopt Azure Synapse de verdere uitvoering in het notebook waarnaar wordt verwezen en blijven volgende cellen in het notebook worden uitgevoerd die de functie `exit()` `run()` aanroepen. Bijvoorbeeld: Notebook1 heeft drie cellen en roept een functie `exit()` aan in de tweede cel. Notebook2 heeft vijf cellen en aanroepen `run(notebook1)` in de derde cel. Wanneer u Notebook2 gebruikt, wordt Notebook1 bij de tweede cel gestopt wanneer de functie wordt `exit()` gebruikt. Notebook2 blijft de vierde cel en vijfde cel uitvoeren. 


```python
mssparkutils.notebook.exit("value string")
```

Bijvoorbeeld:

**Sample1** notebook zoekt onder **mssparkutils/folder/** met de volgende twee cellen: 
- cel 1 definieert **een invoerparameter** met de standaardwaarde ingesteld op 10.
- cel 2 sluit het notebook af met **invoer als** exitwaarde. 

![Schermopname van een voorbeeldnotenote](./media/microsoft-spark-utilities/spark-utilities-run-notebook-sample.png)

U kunt **Sample1 uitvoeren** in een ander notebook met standaardwaarden:

```scala

val exitVal = mssparkutils.notebook.run("mssparkutils/folder/Sample1")
print(exitVal)

```
Resulteert in:

```
exitVal: String = Sample1 run success with input is 10
Sample1 run success with input is 10
```


U kunt **Sample1 uitvoeren** in een ander notebook en de **invoerwaarde** instellen op 20:

```scala
val exitVal = mssparkutils.notebook.run("mssparkutils/folder/Sample1", 90, {"input": 20 })
print(exitVal)
```

Resulteert in:

```
exitVal: String = Sample1 run success with input is 20
Sample1 run success with input is 20
```
::: zone-end


## <a name="credentials-utilities"></a>Hulpprogramma's voor referenties

U kunt de HULPPROGRAMMA's voor MSSparkUtils-referenties gebruiken om de toegangstokens van gekoppelde services op te halen en geheimen te beheren in Azure Key Vault. 

Voer de volgende opdracht uit om een overzicht te krijgen van de beschikbare methoden:

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

Resultaat krijgen:

```
getToken(audience, name): returns AAD token for a given audience, name (optional)
isValidToken(token): returns true if token hasn't expired
getConnectionStringOrCreds(linkedService): returns connection string or credentials for linked service
getSecret(akvName, secret, linkedService): returns AKV secret for a given AKV linked service, akvName, secret key
getSecret(akvName, secret): returns AKV secret for a given akvName, secret key
putSecret(akvName, secretName, secretValue, linkedService): puts AKV secret for a given akvName, secretName
putSecret(akvName, secretName, secretValue): puts AKV secret for a given akvName, secretName
```

### <a name="get-token"></a>Token op halen

Retourneert een Azure AD-token voor een bepaalde doelgroep, naam (optioneel). In de onderstaande tabel worden alle beschikbare doelgroeptypen weergegeven: 

|Doelgroeptype|Doelgroepsleutel|
|--|--|
|Type doelgroep oplossen|'Doelgroep'|
|Storage-doelgroepresource|'Opslag'|
|Data Warehouse-doelgroepresource|DW|
|Data Lake-doelgroepresource|'AzureManagement'|
|Vault-doelgroepresource|DataLakeStore|
|Azure OSSDB-doelgroepresource|'AzureOSSDB'|
|Azure Synapse Resource|'Synapse'|
|Azure Data Factory Resource|'ADF'|

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

Retourneert true als het token niet is verlopen.

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


### <a name="get-connection-string-or-credentials-for-linked-service"></a>Uw connection string of referenties voor de gekoppelde service op te halen

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


### <a name="get-secret-using-workspace-identity"></a>Geheim maken met behulp van werkruimte-id

Retourneert Azure Key Vault geheim voor een bepaalde Azure Key Vault, geheime naam en naam van de gekoppelde service met behulp van werkruimte-identiteit. Zorg ervoor dat u de toegang tot [Azure Key Vault](#configure-access-to-azure-key-vault) configureert.

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


### <a name="get-secret-using-user-credentials"></a>Geheim maken met behulp van gebruikersreferenties

Retourneert Azure Key Vault voor een bepaalde Azure Key Vault, geheime naam en naam van de gekoppelde service met behulp van gebruikersreferenties. 

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

### <a name="put-secret-using-workspace-identity"></a>Geheim maken met behulp van werkruimte-id

Maakt Azure Key Vault geheim voor een bepaalde Azure Key Vault naam, geheime naam en naam van de gekoppelde service met behulp van werkruimte-id. Zorg ervoor dat u de toegang tot [Azure Key Vault](#configure-access-to-azure-key-vault) configureert.

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

### <a name="put-secret-using-workspace-identity"></a>Geheim maken met behulp van werkruimte-id

Maakt Azure Key Vault geheim voor een bepaalde Azure Key Vault naam, geheime naam en naam van de gekoppelde service met behulp van werkruimte-id. Zorg ervoor dat u de toegang tot [Azure Key Vault](#configure-access-to-azure-key-vault) configureert.

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

### <a name="put-secret-using-user-credentials"></a>Geheim maken met behulp van gebruikersreferenties

Maakt Azure Key Vault geheim voor een bepaalde Azure Key Vault naam, geheime naam en gekoppelde servicenaam met behulp van gebruikersreferenties. 

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value')
```
::: zone-end

:::zone pivot = "programming-language-scala"

### <a name="put-secret-using-user-credentials"></a>Geheim maken met behulp van gebruikersreferenties

Maakt Azure Key Vault geheim voor een bepaalde Azure Key Vault naam, geheime naam en gekoppelde servicenaam met behulp van gebruikersreferenties. 

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value")
```

::: zone-end

<!-- :::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end -->


## <a name="environment-utilities"></a>Omgevingsvoorzieningen 

Voer de volgende opdrachten uit om een overzicht te krijgen van de beschikbare methoden:

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

Resultaat krijgen:
```
GetUserName(): returns user name
GetUserId(): returns unique user id
GetJobId(): returns job id
GetWorkspaceName(): returns workspace name
GetPoolName(): returns Spark pool name
GetClusterId(): returns cluster id
```

### <a name="get-user-name"></a>Gebruikersnaam op halen

Retourneert de huidige gebruikersnaam.

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

### <a name="get-user-id"></a>Gebruikers-id op halen

Retourneert de huidige gebruikers-id.

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

### <a name="get-job-id"></a>Taak-id op halen

Retourneert de taak-id.

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

### <a name="get-workspace-name"></a>Naam van werkruimte op halen

Retourneert de naam van de werkruimte.

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

### <a name="get-pool-name"></a>Naam van pool op halen

Retourneert de naam van de Spark-pool.

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

### <a name="get-cluster-id"></a>Cluster-id op halen

Retourneert de huidige cluster-id.

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

- [Bekijk Synapse-voorbeeldnotenotenotes](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Quickstart: Een Apache Spark-pool maken in Azure Synapse Analytics met behulp van webhulpprogramma's](../quickstart-apache-spark-notebook.md)
- [Wat is Apache Spark in Azure Synapse Analytics?](apache-spark-overview.md)
- [Azure Synapse Analytics](../index.yml)
