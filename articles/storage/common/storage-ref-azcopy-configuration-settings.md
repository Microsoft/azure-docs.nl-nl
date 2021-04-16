---
title: AzCopy v10-configuratie-instelling (Azure Storage) | Microsoft Docs
description: Dit artikel bevat naslaginformatie voor configuratie-instellingen voor AzCopy V10.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 4fcbcf145dc417d2a7f78913e70429c3929cd902
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509074"
---
# <a name="azcopy-v10-configuration-settings-azure-storage"></a>AzCopy v10-configuratie-instellingen (Azure Storage)

AzCopy is een opdrachtregelprogramma dat u kunt gebruiken om blobs of bestanden vanuit of naar een opslagaccount te kopiëren. Dit artikel bevat een lijst met omgevingsvariabelen die u kunt gebruiken om AzCopy v10 te configureren.

> [!NOTE]
> Zie Aan de slag met AzCopy als u inhoud zoekt om aan de slag te gaan [met AzCopy.](storage-use-azcopy-v10.md)

## <a name="azcopy-v10-environment-variables"></a>AzCopy v10-omgevingsvariabelen

De volgende tabel beschrijft elke omgevingsvariabele en bevat koppelingen naar inhoud die u kunnen helpen bij het gebruik van de variabele.

| Omgevingsvariabele | Description |
|--|--|
| AWS_ACCESS_KEY_ID | Amazon Web Services-toegangssleutel. Biedt een sleutel om met de Amazon Web Services. [Gegevens kopiëren van Amazon S3 naar Azure Storage met behulp van AzCopy](storage-use-azcopy-s3.md) |
| AWS_SECRET_ACCESS_KEY | Amazon Web Services geheime toegangssleutel Biedt een geheime sleutel om te autor maken met Amazon Web Services. [Gegevens kopiëren van Amazon S3 naar Azure Storage met behulp van AzCopy](storage-use-azcopy-s3.md) |
| AZCOPY_ACTIVE_DIRECTORY_ENDPOINT | Het Azure Active Directory eindpunt dat moet worden gebruikt. Deze variabele wordt alleen gebruikt voor automatische aanmelding. Gebruik in plaats daarvan de opdrachtregelvlag bij het aanroepen van de aanmeldingsopdracht. |
| AZCOPY_AUTO_LOGIN_TYPE | Stel deze variabele in op `DEVICE` `MSI` , of `SPN` . Deze variabele biedt de mogelijkheid om te machtigen zonder de `azcopy login` opdracht te gebruiken. Dit mechanisme is handig in gevallen waarin uw besturingssysteem geen geheime opslag heeft, zoals een *Linux-sleutelring*. Zie [Autor toestemming geven zonder een geheim opslag.](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_BUFFER_GB | Geef de maximale hoeveelheid systeemgeheugen op die AzCopy moet gebruiken bij het downloaden en uploaden van bestanden. Druk deze waarde uit in gigabytes (GB). Zie [Geheugengebruik optimaliseren](storage-use-azcopy-optimize.md#optimize-memory-use) |
| AZCOPY_CACHE_PROXY_LOOKUP | Standaard worden met AzCopy in Windows zoekups van proxyservers op hostnaamniveau in de cache opgeslagen (er wordt geen rekening gehouden met het URL-pad). Stel in op een andere waarde dan 'true' om de cache uit te schakelen. |
| AZCOPY_CONCURRENCY_VALUE | Hiermee geeft u het aantal gelijktijdige aanvragen die kunnen optreden. U kunt deze variabele gebruiken om de doorvoer te verhogen. Als uw computer minder dan 5 CPU's heeft, wordt de waarde van deze variabele ingesteld op `32` . Anders is de standaardwaarde gelijk aan 16 vermenigvuldigd met het aantal CPU's. De maximale standaardwaarde van deze variabele is , maar u kunt deze waarde handmatig hoger of `3000` lager instellen. Zie [Gelijktijdigheid verhogen](storage-use-azcopy-optimize.md#increase-concurrency) |
| AZCOPY_CONCURRENT_FILES | Overschrijven (bij benadering) het aantal bestanden dat op een moment wordt uitgevoerd, door te bepalen voor hoeveel bestanden we gelijktijdig overdrachten initiëren. |
| AZCOPY_CONCURRENT_SCAN | Hiermee bepaalt u de (maximale) mate van parallellisme die tijdens het scannen wordt gebruikt. Is alleen van invloed op ge parallelliseerde enumerators, waaronder Azure Files/Blobs en lokale bestandssystemen. |
| AZCOPY_DEFAULT_SERVICE_API_VERSION | Overschrijven de service-API-versie zodat AzCopy aangepaste omgevingen kan gebruiken, zoals Azure Stack. |
| AZCOPY_DISABLE_HIERARCHICAL_SCAN | Is alleen van toepassing wanneer Azure Blobs de bron is. Gelijktijdig scannen is sneller, maar maakt gebruik van de API voor hiërarchische vermeldingen, wat kan leiden tot meer IO's/kosten. Geef 'true' op om de prestaties te beïnvloeden, maar bespaar op kosten. |
| AZCOPY_JOB_PLAN_LOCATION | Overschrijvingen waarbij de jobplanbestanden (gebruikt voor het bijhouden en hervatten van de voortgang) worden opgeslagen, om te voorkomen dat een schijf wordt gevuld. |
| AZCOPY_LOG_LOCATION | Overschrijvingen waarbij de logboekbestanden worden opgeslagen, om te voorkomen dat een schijf wordt gevuld. |
| AZCOPY_MSI_CLIENT_ID | De client-id van een door de gebruiker toegewezen beheerde identiteit. Gebruik wanneer `AZCOPY_AUTO_LOGIN_TYPE` is ingesteld op `MSI` . Zie [Autor toestemming geven zonder een geheim winkel](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_MSI_OBJECT_ID | De object-id van de door de gebruiker toegewezen beheerde identiteit. Gebruik wanneer `AZCOPY_AUTO_LOGIN_TYPE` is ingesteld op `MSI` . Zie [Autor toestemming geven zonder een geheim winkel](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_MSI_RESOURCE_STRING | De resource-id van de door de gebruiker toegewezen beheerde identiteit. Zie [Autor toestemming geven zonder een geheim winkel](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_PACE_PAGE_BLOBS | Moet de doorvoer voor pagina-blobs automatisch worden aangepast aan de servicelimieten? De standaardwaarde is true. Ingesteld op 'false' om uit te schakelen |
| AZCOPY_PARALLEL_STAT_FILES | Zorgt ervoor dat AzCopy bestandseigenschappen op zoek gaat op parallelle 'threads' bij het scannen van het lokale bestandssysteem.  De threads worden uit de pool getekend die is gedefinieerd door AZCOPY_CONCURRENT_SCAN.  Als u dit instelt op true, kunnen de scanprestaties in Linux worden verbeterd.  Niet nodig of aanbevolen in Windows. |
| AZCOPY_SHOW_PERF_STATES | Als dit is ingesteld op iets, bevat de uitvoer op het scherm tellingen van segmenten per status |
| AZCOPY_SPA_APPLICATION_ID | De toepassings-id van de app-registratie van uw service-principal. Gebruik wanneer `AZCOPY_AUTO_LOGIN_TYPE` is ingesteld op `SPN` . Zie [Autor toestemming geven zonder een geheim winkel](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_SPA_CERT_PASSWORD | Het wachtwoord van een certificaat. Gebruik wanneer `AZCOPY_AUTO_LOGIN_TYPE` is ingesteld op `SPN` . Zie [Autor toestemming geven zonder een geheim winkel](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_SPA_CERT_PATH | Het relatieve of volledig gekwalificeerde pad naar een certificaatbestand. Gebruik wanneer `AZCOPY_AUTO_LOGIN_TYPE` is ingesteld op `SPN` . Zie [Autor toestemming geven zonder een geheim winkel](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_SPA_CLIENT_SECRET | Het clientgeheim. Gebruik wanneer `AZCOPY_AUTO_LOGIN_TYPE` is ingesteld op `SPN` . Zie [Autor toestemming geven zonder een geheim winkel](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_TENANT_ID | De Azure Active Directory tenant-id die moet worden gebruikt voor interactieve aanmelding op het OAuth-apparaat. Deze variabele wordt alleen gebruikt voor automatische aanmelding. Gebruik in plaats daarvan de opdrachtregelvlag bij het aanroepen van de aanmeldingsopdracht. |
| AZCOPY_TUNE_TO_CPU | Ingesteld op false om te voorkomen dat AzCopy rekening houdt met CPU-gebruik bij het automatisch afstemmen van het gelijktijdigheidsniveau (bijvoorbeeld in de benchmarkopdracht). |
| AZCOPY_USER_AGENT_PREFIX | Voeg een voorvoegsel toe aan de standaard AzCopy-gebruikersagent, die wordt gebruikt voor telemetriedoeleinden. Er wordt automatisch een spatie ingevoegd. |
| GOOGLE_APPLICATION_CREDENTIALS | Het absolute pad naar het sleutelbestand van het serviceaccount Bevat een sleutel voor autor mailen met Google Cloud Storage. [Gegevens kopiëren van Google Cloud Storage naar Azure Storage met azcopy (preview)](storage-ref-azcopy-configuration-settings.md) |
| HTTPS_PROXY | Hiermee configureert u proxyinstellingen voor AzCopy. Stel deze variabele in op het IP-adres van de proxy en het proxypoortnummer. Bijvoorbeeld `xx.xxx.xx.xxx:xx`. Als u AzCopy uitvoert in Windows, detecteert AzCopy proxyinstellingen automatisch. U hoeft deze instelling dus niet te gebruiken in Windows. Als u ervoor kiest om deze instelling te gebruiken in Windows, wordt automatische detectie overschreven. Zie [Proxy-instellingen configureren](#configure-proxy-settings) |


## <a name="configure-proxy-settings"></a>Proxyinstellingen configureren

Als u de proxyinstellingen voor AzCopy wilt configureren, stelt u de `HTTPS_PROXY` omgevingsvariabele in. Als u AzCopy uitvoert in Windows, detecteert AzCopy proxyinstellingen automatisch. U hoeft deze instelling dus niet te gebruiken in Windows. Als u ervoor kiest om deze instelling te gebruiken in Windows, wordt automatische detectie overschreven.

| Besturingssysteem | Opdracht  |
|--------|-----------|
| **Windows** | Gebruik in een opdrachtprompt: `set HTTPS_PROXY=<proxy IP>:<proxy port>`<br> Gebruik in PowerShell: `$env:HTTPS_PROXY="<proxy IP>:<proxy port>"`|
| **Linux** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |
| **MacOS** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |

AzCopy biedt momenteel geen ondersteuning voor proxies waarvoor verificatie met NTLM of Kerberos is vereist.

### <a name="bypassing-a-proxy"></a>Een proxy omzeilen

Als u AzCopy in Windows gebruikt en u wilt zeggen dat er helemaal geen _proxy_ moet worden gebruikt (in plaats van de instellingen automatisch te detecteren), gebruikt u deze opdrachten. Met deze instellingen zal AzCopy geen proxy zoeken of gebruiken.

| Besturingssysteem | Omgeving | Opdracht  |
|--------|-----------|----------|
| **Windows** | Opdrachtprompt (CMD) | `set HTTPS_PROXY=dummy.invalid` <br>`set NO_PROXY=*`|
| **Windows** | PowerShell | `$env:HTTPS_PROXY="dummy.invalid"` <br>`$env:NO_PROXY="*"`<br>|

Laat op andere besturingssystemen de variabele HTTPS_PROXY uit als u geen proxy wilt gebruiken.

## <a name="see-also"></a>Zie ook

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [De prestaties van AzCopy v10 optimaliseren met Azure Storage](storage-use-azcopy-optimize.md)
- [Problemen met AzCopy V10 in Azure Storage oplossen met behulp van logboekbestanden](storage-use-azcopy-configure.md)
