---
title: Azurite-emulator gebruiken voor het ontwikkelen van lokale Azure Storage
description: De Azurite open-source-emulator biedt een gratis lokale omgeving voor het testen van uw Azure Storage-toepassingen.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/15/2020
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: b5051a5e29f9e34c43963a9a264ee47e2ad7b04a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92490778"
---
# <a name="use-the-azurite-emulator-for-local-azure-storage-development"></a>De Azurite-emulator gebruiken voor het ontwikkelen van lokale Azure Storage

De Azurite open-source-emulator biedt een gratis lokale omgeving voor het testen van uw Azure Blob-en Queue Storage-toepassingen. Wanneer u tevreden bent over de manier waarop uw toepassing lokaal werkt, schakelt u over naar het gebruik van een Azure Storage-account in de Cloud. De emulator biedt ondersteuning voor meerdere platforms voor Windows, Linux en macOS.

Azurite is het toekomstige opslag emulator-platform. Azurite vervangt de [Azure Storage-emulator](storage-use-emulator.md). Azurite zal nog steeds worden bijgewerkt ter ondersteuning van de nieuwste versies van Azure Storage-Api's.

Er zijn verschillende manieren om Azurite te installeren en uit te voeren op uw lokale systeem:

  1. [De Azurite Visual Studio code-extensie installeren en uitvoeren](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [Azurite installeren en uitvoeren met behulp van NPM](#install-and-run-azurite-by-using-npm)
  1. [De Azurite docker-installatie kopie installeren en uitvoeren](#install-and-run-the-azurite-docker-image)
  1. [Azurite klonen, bouwen en uitvoeren vanuit de GitHub-opslag plaats](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>De Azurite Visual Studio code-extensie installeren en uitvoeren

Selecteer in Visual Studio code het deel venster **uitbrei dingen** en zoek naar *Azurite* in de **modules extensies: marketplace**.

![Marketplace voor Visual Studio-code uitbreidingen](media/storage-use-azurite/azurite-vs-code-extension.png)

U kunt ook naar de [markt voor Visual Studio-code uitbreiding](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) navigeren in uw browser. Selecteer de knop **installeren** om Visual Studio code te openen en direct naar de uitbrei ding pagina Azurite te gaan.

De extensie ondersteunt de volgende Visual Studio-code opdrachten. Druk op F1 in Visual Studio code om het opdracht palet te openen. 

   - **Azurite:** alle Azurite Services opereren-gegevens opnieuw instellen
   - **Azurite: de BLOB-service opschonen** -BLOB-service opschonen
   - **Azurite: wachtrij service opschonen** -wachtrij service wissen
   - **Azurite: sluiten** -alle Azurite-Services sluiten
   - **Azurite: de BLOB-service sluiten** -BLOB-service sluiten
   - **Azurite: de wachtrij service sluiten** -wachtrij service sluiten
   - **Azurite: start** -alle Azurite-Services starten
   - **Azurite: de BLOB-service starten** -BLOB-service starten
   - **Azurite: wachtrij service starten** -wachtrij service starten

Als u Azurite in Visual Studio code wilt configureren, selecteert u het deel venster uitbrei dingen. Selecteer het pictogram **beheren** (vistuig) voor **Azurite**. Selecteer **extensie-instellingen**.

![Azurites configureren](media/storage-use-azurite/azurite-configure-extension-settings.png)

De volgende instellingen worden ondersteund:

   - **Azurite: BLOB host** : het BLOB service luisterende eind punt. De standaard instelling is 127.0.0.1.
   - **Azurite: BLOB-poort** -de BLOB service Luister poort. De standaard poort is 10000.
   - **Azurite: Cert** -pad naar een lokaal vertrouwd PEM-of PFX-certificaat bestandspad om de https-modus in te scha kelen.
   - **Azurite: debug** -het logboek voor fout opsporing uitvoeren op het Azurite-kanaal. De standaardwaarde is **onwaar**.
   - **Azurite: Key** -pad naar een lokaal vertrouwd PEM-sleutel bestand, vereist wanneer **Azurite: Cert** verwijst naar een PEM-bestand.
   - **Azurite: locatie** -het pad naar de locatie van de werk ruimte. De standaard waarde is de werkmap Visual Studio-code.
   - **Azurite:** de soepele modus uitschakelen, waarmee niet-ondersteunde headers en para meters worden genegeerd.
   - **Azurite: OAuth** -optioneel OAuth-niveau.
   - **Azurite: pwd** -wacht woord voor pfx-bestand. Vereist wanneer **Azurite: Cert** verwijst naar een pfx-bestand.
   - **Azurite: wachtrij-host** -het Queue-service luisterende eind punt. De standaard instelling is 127.0.0.1.
   - **Azurite: wachtrij poort** : de Queue-service Luister poort. De standaard poort is 10001.
   - **Azurite:** in de Stille modus wordt het toegangs logboek uitgeschakeld. De standaardwaarde is **onwaar**.
   - **Azurite: Schakel de API-versie controle** over en sla de controle van de API-aanvraag versie over. De standaardwaarde is **onwaar**.

## <a name="install-and-run-azurite-by-using-npm"></a>Azurite installeren en uitvoeren met behulp van NPM

Voor deze installatie methode moet [Node.js versie 8,0 of hoger](https://nodejs.org) zijn geïnstalleerd. Node Package Manager (NPM) is het hulp programma pakket beheer dat bij elke Node.js-installatie is opgenomen. Nadat u Node.js hebt geïnstalleerd, voert `npm` u de volgende opdracht uit om Azurite te installeren.

```console
npm install -g azurite
```

Nadat u Azurite hebt geïnstalleerd, raadpleegt u [Azurite uitvoeren vanaf een opdracht regel](#run-azurite-from-a-command-line).

## <a name="install-and-run-the-azurite-docker-image"></a>De Azurite docker-installatie kopie installeren en uitvoeren

Gebruik [DockerHub](https://hub.docker.com/) voor het ophalen van de [meest recente Azurite-installatie kopie](https://hub.docker.com/_/microsoft-azure-storage-azurite) met behulp van de volgende opdracht:

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**Voer de Azurite docker-installatie kopie uit**:

Met de volgende opdracht wordt de Azurite docker-installatie kopie uitgevoerd. De `-p 10000:10000` para meter stuurt aanvragen van de poort 10000 van de hostcomputer door naar het docker-exemplaar.

```console
docker run -p 10000:10000 -p 10001:10001 \
    mcr.microsoft.com/azure-storage/azurite
```

**De locatie van de werk ruimte opgeven**:

In het volgende voor beeld `-v c:/azurite:/data` geeft de para meter *c:/Azurite* op als de persistente gegevens locatie Azurite. De map, *c:/Azurite*, moet worden gemaakt voordat u de opdracht docker uitvoert.

```console
docker run -p 10000:10000 -p 10001:10001 \
    -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Alleen de BLOB-service uitvoeren**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite \
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
```

Zie [opdracht regel opties](#command-line-options)voor meer informatie over het configureren van Azurite bij het opstarten.

## <a name="clone-build-and-run-azurite-from-the-github-repository"></a>Azurite klonen, bouwen en uitvoeren vanuit de GitHub-opslag plaats

Voor deze installatie methode moet [Git](https://git-scm.com/) zijn geïnstalleerd. Kloon de [github-opslag plaats](https://github.com/azure/azurite) voor het Azurite-project met behulp van de volgende console opdracht.

```console
git clone https://github.com/Azure/Azurite.git
```

Nadat u de bron code hebt gekloond, voert u de volgende opdrachten uit vanuit de hoofdmap van de gekloonde opslag plaats om Azurite te bouwen en te installeren.

```console
npm install
npm run build
npm install -g
```

Ga na het installeren en maken van Azurite naar [Azurite uitvoeren vanaf een opdracht regel](#run-azurite-from-a-command-line).

## <a name="run-azurite-from-a-command-line"></a>Azurite uitvoeren vanaf een opdracht regel

> [!NOTE]
> Azurite kan niet worden uitgevoerd vanaf de opdracht regel als u de Visual Studio code extension alleen hebt geïnstalleerd. Gebruik in plaats daarvan het Visual Studio code-opdracht palet. Zie [Install and run the Azurite Visual Studio code extension](#install-and-run-the-azurite-visual-studio-code-extension)(Engelstalig) voor meer informatie.

Als u direct aan de slag wilt gaan met de opdracht regel, maakt u een map met de naam *c:\azurite* en start u Azurite door de volgende opdracht te geven:

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

Met deze opdracht wordt Azurite om alle gegevens op te slaan in een bepaalde map, *c:\azurite*. Als de `--location` optie wordt wegge laten, wordt de huidige werkmap gebruikt.

## <a name="command-line-options"></a>Opdrachtregelopties

In deze sectie vindt u meer informatie over de opdracht regel parameters die beschikbaar zijn bij het starten van Azurite.

### <a name="help"></a>Help

**Optioneel** : Get opdracht regel Help met behulp van de `-h` of- `--help` Schakel optie.

```console
azurite -h
azurite --help
```

### <a name="blob-listening-host"></a>BLOB luistert host

**Optioneel** : Azurite wordt standaard naar 127.0.0.1 geluisterd als de lokale server. Gebruik de `--blobHost` Schakel optie om het adres in te stellen op uw vereisten.

Alleen aanvragen op de lokale computer accepteren:

```console
azurite --blobHost 127.0.0.1
```

Externe aanvragen toestaan:

```console
azurite --blobHost 0.0.0.0
```

> [!CAUTION]
> Het toestaan van externe aanvragen kan uw systeem kwetsbaar maken voor externe aanvallen.

### <a name="blob-listening-port-configuration"></a>Poort configuratie voor BLOB-Luister

**Optioneel** : Azurite luistert standaard naar de BLOB service op poort 10000. Gebruik de `--blobPort` Schakel optie om de luisterende poort op te geven die u nodig hebt.

> [!NOTE]
> Nadat u een aangepaste poort hebt gebruikt, moet u de connection string of de bijbehorende configuratie bijwerken in uw Azure Storage-hulpprogram ma's of Sdk's.

De Blob service-Luister poort aanpassen:

```console
azurite --blobPort 8888
```

Het systeem automatisch een beschik bare poort laten selecteren:

```console
azurite --blobPort 0
```

De poort die in gebruik is, wordt weer gegeven tijdens het opstarten van Azurite.

### <a name="queue-listening-host"></a>Luisterende host in wachtrij plaatsen

**Optioneel** : Azurite wordt standaard naar 127.0.0.1 geluisterd als de lokale server. Gebruik de `--queueHost` Schakel optie om het adres in te stellen op uw vereisten.

Alleen aanvragen op de lokale computer accepteren:

```console
azurite --queueHost 127.0.0.1
```

Externe aanvragen toestaan:

```console
azurite --queueHost 0.0.0.0
```

> [!CAUTION]
> Het toestaan van externe aanvragen kan uw systeem kwetsbaar maken voor externe aanvallen.

### <a name="queue-listening-port-configuration"></a>Poort configuratie voor Luis teren in wachtrij

**Optioneel** : Azurite luistert standaard naar de Queue-service op poort 10001. Gebruik de `--queuePort` Schakel optie om de luisterende poort op te geven die u nodig hebt.

> [!NOTE]
> Nadat u een aangepaste poort hebt gebruikt, moet u de connection string of de bijbehorende configuratie bijwerken in uw Azure Storage-hulpprogram ma's of Sdk's.

De Queue-service-Luister poort aanpassen:

```console
azurite --queuePort 8888
```

Het systeem automatisch een beschik bare poort laten selecteren:

```console
azurite --queuePort 0
```

De poort die in gebruik is, wordt weer gegeven tijdens het opstarten van Azurite.

### <a name="workspace-path"></a>Pad naar werk ruimte

**Optioneel** : Azurite slaat gegevens op de lokale schijf op tijdens de uitvoering. Gebruik de `-l` of `--location` Schakel over om een pad op te geven als locatie van de werk ruimte. Standaard wordt de huidige werk directory van het proces gebruikt. Let op de kleine letter ' l '.

```console
azurite -l c:\azurite
azurite --location c:\azurite
```

### <a name="access-log"></a>Access-logboek

**Optioneel** : het toegangs logboek wordt standaard weer gegeven in het console venster. Schakel de weer gave van het toegangs logboek uit met behulp van de `-s` of- `--silent` Switch.

```console
azurite -s
azurite --silent
```
### <a name="debug-log"></a>Logboek voor fout opsporing

**Optioneel** : het logboek voor fout opsporing bevat gedetailleerde informatie over elke aanvraag en uitzonderings Stack tracering. Schakel het logboek voor fout opsporing in door een geldig lokaal bestandspad naar de of-switch op te geven `-d` `--debug` .

```console
azurite -d path/debug.log
azurite --debug path/debug.log
```

### <a name="loose-mode"></a>Losse modus

**Optioneel** : standaard past Azurite de strikte modus toe om niet-ondersteunde aanvraag headers en-para meters te blok keren. Schakel de strikte modus uit met behulp van de `-L` of- `--loose` Switch. Noteer het hoofd letter ' L '.

```console
azurite -L
azurite --loose
```
### <a name="version"></a>Versie

**Optioneel** : Geef het geïnstalleerde versie nummer van de Azurite weer met behulp van de `-v` of- `--version` Schakel optie.

```console
azurite -v
azurite --version
```

### <a name="certificate-configuration-https"></a>Certificaat configuratie (HTTPS)

**Optioneel** : standaard maakt Azurite gebruik van het HTTP-protocol. Schakel de HTTPS-modus in door een pad naar een Privacy Enhanced Mail-certificaat bestand (. pem) of [Personal Information Exchange (. pfx)](/windows-hardware/drivers/install/personal-information-exchange---pfx--files) op de switch op te geven `--cert` .

Wanneer `--cert` u een PEM-bestand opgeeft, moet u een overeenkomende `--key` Switch opgeven.

```console
azurite --cert path/server.pem --key path/key.pem
```

Wanneer `--cert` u een pfx-bestand opgeeft, moet u een overeenkomende `--pwd` Switch opgeven.

```console
azurite --cert path/server.pfx --pwd pfxpassword
```

Zie [https Setup](https://github.com/Azure/Azurite/blob/master/README.md#https-setup)(Engelstalig) voor gedetailleerde informatie over het maken van PEM-en pfx-bestanden.

### <a name="oauth-configuration"></a>OAuth-configuratie

**Optioneel** : Schakel OAuth-verificatie voor Azurite in met behulp van de `--oauth` Switch.

```console
azurite --oauth basic --cert path/server.pem --key path/key.pem
```

> [!NOTE]
> OAuth vereist een HTTPS-eind punt. Zorg ervoor dat HTTPS is ingeschakeld door `--cert` de switch samen met de switch op te geven `--oauth` .

Azurite ondersteunt basis verificatie door de `basic` para meter op te geven voor de `--oauth` Switch. Azurite voert basis verificatie uit, zoals het valideren van het binnenkomende Bearer-token, het controleren van de verlener, de doel groep en de verval datum. Azurite controleert de hand tekening van het token of de machtigingen niet.

### <a name="skip-api-version-check"></a>Controle van API-versie overs Laan

**Optioneel** : bij het starten controleert Azurite of de aangevraagde API-versie geldig is. Met de volgende opdracht slaat u de API-versie controle op:

```console
azurite --skipApiVersionCheck
```


## <a name="authorization-for-tools-and-sdks"></a>Autorisatie voor hulpprogram ma's en Sdk's

Maak verbinding met Azurite van Azure Storage Sdk's of hulpprogram ma's, zoals [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), met behulp van een verificatie strategie. Verificatie is vereist. Azurite ondersteunt verificatie met OAuth, gedeelde sleutel en Shared Access signatures (SAS). Azurite biedt ook ondersteuning voor anonieme toegang tot open bare containers.

Als u de Azure Sdk's gebruikt, start u Azurite met de `--oauth basic and --cert --key/--pwd` Opties.

### <a name="well-known-storage-account-and-key"></a>Bekende opslag account en-sleutel

Azurite accepteert dezelfde bekende account en sleutel die wordt gebruikt door de verouderde Azure Storage-emulator.

- Account naam: `devstoreaccount1`
- Account sleutel: `Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

### <a name="custom-storage-accounts-and-keys"></a>Aangepaste opslag accounts en-sleutels

Azurite ondersteunt aangepaste opslag accountnamen en-sleutels door de `AZURITE_ACCOUNTS` omgevings variabele in te stellen in de volgende indeling: `account1:key1[:key2];account2:key1[:key2];...` .

Gebruik bijvoorbeeld een aangepast opslag account met één sleutel:

```cmd
set AZURITE_ACCOUNTS="account1:key1"
```

```bash
export AZURITE_ACCOUNTS="account1:key1"
```

Of gebruik meerdere opslag accounts met twee sleutels:

```cmd
set AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

```bash
export AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

Met Azurite worden aangepaste account namen en sleutels standaard elke minuut vernieuwd van de omgevings variabele. Met deze functie kunt u de account sleutel dynamisch draaien of nieuwe opslag accounts toevoegen zonder dat u Azurite opnieuw hoeft op te starten.

> [!NOTE]
> Het standaard `devstoreaccount1` opslag account is uitgeschakeld wanneer u aangepaste opslag accounts instelt.

### <a name="connection-strings"></a>Verbindingsreeksen

De eenvoudigste manier om verbinding te maken met Azurite vanuit uw toepassing is het configureren van een connection string in het configuratie bestand van uw toepassing dat verwijst naar de snelkoppeling *UseDevelopmentStorage = True*. Hier volgt een voor beeld van een connection string in een *app.config* -bestand:

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="http-connection-strings"></a>HTTP-verbindings reeksen

U kunt de volgende verbindings reeksen door geven aan de [Azure-sdk's](https://aka.ms/azsdk) of-hulpprogram ma's, zoals azure CLI 2,0 of Storage Explorer.

De volledige connection string is:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;`

Als u alleen verbinding wilt maken met de BLOB-service, is de connection string:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;`

Als u alleen verbinding wilt maken met de wachtrij service, is de connection string:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;`

#### <a name="https-connection-strings"></a>HTTPS-verbindings reeksen

De volledige HTTPS-connection string is:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;`

Als u alleen de BLOB-service wilt gebruiken, is de HTTPS-connection string:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;`

Als u de wachtrij service alleen wilt gebruiken, is de HTTPS-connection string:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;`

Als u het `dotnet dev-certs` zelfondertekende certificaat hebt gegenereerd, gebruikt u de volgende Connection String.

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://localhost:10000/devstoreaccount1;QueueEndpoint=https://localhost:10001/devstoreaccount1;`

Werk de connection string bij met [aangepaste opslag accounts en-sleutels](#custom-storage-accounts-and-keys).

Zie [Azure Storage-verbindings reeksen configureren](storage-configure-connection-string.md)voor meer informatie.

### <a name="azure-sdks"></a>Azure-SDK's

Als u Azurite wilt gebruiken met de [Azure-sdk's](https://aka.ms/azsdk), gebruikt u OAuth-en HTTPS-opties:

```console
azurite --oauth basic --cert certname.pem --key certname-key.pem
```

#### <a name="azure-blob-storage"></a>Azure Blob Storage

Vervolgens kunt u een BlobContainerClient, BlobServiceClient of BlobClient instantiëren.

```csharp
// With container URL and DefaultAzureCredential
var client = new BlobContainerClient(
    new Uri("https://127.0.0.1:10000/devstoreaccount1/container-name"), new DefaultAzureCredential()
  );

// With connection string
var client = new BlobContainerClient(
    "DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;", "container-name"
  );

// With account name and key
var client = new BlobContainerClient(
    new Uri("https://127.0.0.1:10000/devstoreaccount1/container-name"),
    new StorageSharedKeyCredential("devstoreaccount1", "Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==")
  );
```

#### <a name="azure-queue-storage"></a>Azure Queue Storage

U kunt ook een QueueClient of QueueServiceClient instantiëren.

```csharp
// With queue URL and DefaultAzureCredential
var client = new QueueClient(
    new Uri("https://127.0.0.1:10001/devstoreaccount1/queue-name"), new DefaultAzureCredential()
  );

// With connection string
var client = new QueueClient(
    "DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;", "queue-name"
  );

// With account name and key
var client = new QueueClient(
    new Uri("https://127.0.0.1:10001/devstoreaccount1/queue-name"),
    new StorageSharedKeyCredential("devstoreaccount1", "Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==")
  );
```

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

U kunt Storage Explorer gebruiken om de gegevens weer te geven die zijn opgeslagen in Azurite.

#### <a name="connect-to-azurite-using-http"></a>Verbinding maken met Azurite met behulp van HTTP

Maak in Storage Explorer verbinding met Azurite door de volgende stappen uit te voeren:

 1. Selecteer het pictogram **accounts beheren**
 1. Selecteer **een account toevoegen**
 1. Selecteer **koppelen aan een lokale emulator**
 1. Selecteer **Volgende**
 1. Bewerk het veld **weergave naam** op een naam van uw keuze
 1. Selecteer **volgende** opnieuw
 1. **Verbinding maken** selecteren

#### <a name="connect-to-azurite-using-https"></a>Verbinding maken met Azurite met behulp van HTTPS

Standaard Storage Explorer geen HTTPS-eind punt openen dat gebruikmaakt van een zelfondertekend certificaat. Als u Azurite met HTTPS uitvoert, gebruikt u waarschijnlijk een zelfondertekend certificaat. In Storage Explorer kunt u SSL-certificaten importeren via het  ->  dialoog venster **SSL-certificaten** bewerken  ->  **certificaten importeren** .

##### <a name="import-certificate-to-storage-explorer"></a>Certificaat importeren in Storage Explorer

1. Zoek het certificaat op uw lokale computer.
1. Ga in Storage Explorer naar **bewerken**  ->  **SSL-certificaten**  ->  **certificaten importeren** en Importeer uw certificaat.

Als u geen certificaat importeert, krijgt u een fout melding:

`unable to verify the first certificate` of `self signed certificate in chain`

##### <a name="add-azurite-via-https-connection-string"></a>Azurite toevoegen via HTTPS-connection string

Voer de volgende stappen uit om Azurite HTTPS toe te voegen aan Storage Explorer:

1. **Scha kelen tussen Verkenner** selecteren
1. **Lokale & gekoppeld** selecteren
1. Klik met de rechter muisknop op **opslag accounts** en selecteer **verbinding maken met Azure Storage**.
1. Selecteer **een Connection String gebruiken**
1. Selecteer **Next**.
1. Voer een waarde in het veld **weergave naam** in.
1. Voer de [https-connection string](#https-connection-strings) in uit de vorige sectie van dit document
1. Selecteer **Volgende**
1. **Verbinding maken** selecteren

## <a name="workspace-structure"></a>Werkruimte structuur

De volgende bestanden en mappen kunnen worden gemaakt op de werkruimte locatie bij het initialiseren van Azurite.

- `__blobstorage__` -Map met de Azurite BLOB-service permanente binaire gegevens
- `__queuestorage__` -Map met Azurite Queue-service permanente binaire gegevens
- `__azurite_db_blob__.json` -Meta gegevensbestand voor Azurite BLOB-service
- `__azurite_db_blob_extent__.json` -Azurite BLOB service-gebied met meta gegevens
- `__azurite_db_queue__.json` -Azurite-meta gegevens bestand van de wachtrij service
- `__azurite_db_queue_extent__.json` -Azurite van het meta gegevens bestand voor de wachtrij service

Als u Azurite wilt opschonen, verwijdert u de bestanden en mappen en start u de emulator opnieuw.

## <a name="differences-between-azurite-and-azure-storage"></a>Verschillen tussen Azurite en Azure Storage

Er zijn functionele verschillen tussen een lokaal exemplaar van Azurite en een Azure Storage-account in de Cloud.

### <a name="endpoint-and-connection-url"></a>Eind punt en verbindings-URL

De service-eind punten voor Azurite verschillen van de eind punten van een Azure Storage-account. De lokale computer voert geen domeinnaam omzetting uit en vereist dat Azurite-eind punten lokale adressen hebben.

Wanneer u een resource in een Azure Storage-account adresseert, is de account naam onderdeel van de URI-hostnaam. De bron die wordt geadresseerd, maakt deel uit van het URI-pad:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

De volgende URI is een geldig adres voor een BLOB in een Azure Storage-account:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Omdat de lokale computer geen domein naam omzetting heeft, maakt de account naam deel uit van het URI-pad in plaats van de hostnaam. Gebruik de volgende URI-indeling voor een resource in Azurite:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Het volgende adres kan worden gebruikt voor toegang tot een BLOB in Azurite:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

### <a name="scaling-and-performance"></a>Schalen en prestaties

Azurite biedt geen ondersteuning voor grote aantallen verbonden clients. Er is geen prestatie garantie. Azurite is bedoeld voor ontwikkelings-en test doeleinden.

### <a name="error-handling"></a>Foutafhandeling

Azurite is uitgelijnd met Azure Storage fout bij het verwerken van fouten, maar er zijn verschillen. Fout berichten kunnen bijvoorbeeld verschillen, terwijl fout status codes worden uitgelijnd.

### <a name="ra-grs"></a>RA-GRS

Azurite ondersteunt geo-redundante replicatie met lees toegang (RA-GRS). Voor opslag bronnen opent u de secundaire locatie door toe te voegen `-secondary` aan de account naam. Het volgende adres kan bijvoorbeeld worden gebruikt voor toegang tot een blob met behulp van de alleen-lezen secundair in Azurite:

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

### <a name="table-support"></a>Tabel ondersteuning

Ondersteuning voor tabellen in Azurite is momenteel in ontwikkeling en open voor bijdragen. Controleer het [Azurite v3-tabel](https://github.com/Azure/Azurite/wiki/Azurite-V3-Table) project voor de laatste voortgang.

Voor de ondersteuning van duurzame functies zijn tabellen vereist.

## <a name="azurite-is-open-source"></a>Azurite is open source

Bijdragen en suggesties voor Azurite zijn welkom. Ga naar de Azurite [github-project](https://github.com/Azure/Azurite/projects) pagina of [github problemen](https://github.com/Azure/Azurite/issues) voor mijl palen en werk items die we volgen op aanstaande functies en oplossingen voor problemen. Gedetailleerde werk items worden ook bijgehouden in GitHub.

## <a name="next-steps"></a>Volgende stappen

- [Gebruik de Azure Storage-emulator voor het ontwikkelen en testen van](storage-use-emulator.md) documenten de verouderde Azure Storage emulator, die wordt vervangen door Azurite.
- [Azure Storage verbindings reeksen configureren](storage-configure-connection-string.md) wordt uitgelegd hoe u een geldige Azure Storage Connection String kunt samen stellen.
