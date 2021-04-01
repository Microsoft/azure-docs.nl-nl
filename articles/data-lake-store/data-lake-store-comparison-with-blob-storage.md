---
title: Vergelijking van Azure Data Lake Storage Gen1 met Blob Storage
description: Meer informatie over de verschillen tussen Azure Data Lake Storage Gen1 en Azure Blob Storage over bepaalde belang rijke aspecten van big data verwerking.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 77ac3c0809c08719d77457c59ef311ad43ef99cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92108334"
---
# <a name="comparing-azure-data-lake-storage-gen1-and-azure-blob-storage"></a>Azure Data Lake Storage Gen1 en Azure Blob Storage vergelijken

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)] 

De tabel in dit artikel bevat een overzicht van de verschillen tussen Azure Data Lake Storage Gen1 en Azure Blob Storage over de belangrijkste aspecten van big data verwerking. Azure Blob Storage is een schaalbaar object archief dat is ontworpen voor een groot aantal verschillende opslag scenario's. Azure Data Lake Storage Gen1 is een opslag plaats in de vorm van een Hyper schaal die is geoptimaliseerd voor big data analytische werk belastingen.

| Categorie | Azure Data Lake Storage Gen1 | Azure Blob Storage |
| -------- | ---------------------------- | ------------------ |
| Doel |Geoptimaliseerde opslag voor big data Analytics-workloads |Object opslag voor algemeen gebruik voor een groot aantal verschillende opslag scenario's, waaronder big data Analytics |
| Gebruiksscenario's |Batch-, interactieve, streaming-analyses en machine learning gegevens, zoals logboek bestanden, IoT-gegevens, klikken op streams, grote gegevens sets |Elk type tekst of binaire gegevens, zoals back-end van de toepassing, back-upgegevens, media opslag voor gegevens van streaming en algemeen gebruik. Daarnaast is volledige ondersteuning voor analytische werk belastingen; batch-, interactieve, streaming-analyses en machine learning gegevens, zoals logboek bestanden, IoT-gegevens, klikken op streams, grote gegevens sets |
| Belangrijkste concepten |Data Lake Storage Gen1 account bevat mappen, die op zijn beurt gegevens bevat die als bestanden zijn opgeslagen |Het opslag account heeft containers, die op zijn beurt gegevens hebben in de vorm van blobs |
| Structuur |Hiërarchisch bestands systeem |Object archief met platte naam ruimte |
| API |REST API via HTTPS |REST API via HTTP/HTTPS |
| API aan server zijde |[WebHDFS-compatibele REST API](/rest/api/datalakestore/) |[Azure Blob Storage REST API](/rest/api/storageservices/Blob-Service-REST-API) |
| Hadoop File System-client |Ja |Ja |
| Gegevens bewerkingen-verificatie |Op basis van [Azure Active Directory-identiteiten](../active-directory/develop/authentication-vs-authorization.md) |Op basis van gedeelde geheimen- [account toegangs sleutels](../storage/common/storage-account-keys-manage.md) en [Shared Access Signature sleutels](../storage/common/storage-sas-overview.md). |
| Gegevens bewerkingen-verificatie Protocol |[OpenID connect verbinding maken](https://openid.net/connect/). Aanroepen moeten een geldige JWT (JSON Web token) bevatten die is uitgegeven door Azure Active Directory.|Op hash gebaseerde Message Authentication Code (HMAC). Aanroepen moeten een SHA-256-Hash met base64-code ring hebben via een deel van de HTTP-aanvraag. |
| Gegevens bewerkingen-autorisatie |POSIX-Access Control lijsten (Acl's).  Acl's op basis van Azure Active Directory-identiteiten kunnen worden ingesteld op het niveau van bestanden en mappen. |Voor autorisatie op account niveau: gebruik [account toegangs sleutels](../storage/common/storage-account-keys-manage.md)<br>Voor account-, container-of BLOB-autorisatie-gebruik [Shared Access Signature sleutels](../storage/common/storage-sas-overview.md) |
| Gegevens bewerkingen-controleren |Beschikken. Zie [hier](data-lake-store-diagnostic-logs.md) voor meer informatie. |Beschikbaar |
| Versleutelen van gegevens in rust |<ul><li>Transparant, aan de server zijde</li> <ul><li>Met door service beheerde sleutels</li><li>Met door de klant beheerde sleutels in azure-sleutel kluis</li></ul></ul> |<ul><li>Transparant, aan de server zijde</li> <ul><li>Met door service beheerde sleutels</li><li>Met door de klant beheerde sleutels in azure-sleutel kluis (preview-versie)</li></ul><li>Clientversleuteling</li></ul> |
| Beheer bewerkingen (bijvoorbeeld account maken) |[Op rollen gebaseerd toegangs beheer voor Azure (Azure RBAC)](../role-based-access-control/overview.md) voor account beheer |[Op rollen gebaseerd toegangs beheer voor Azure (Azure RBAC)](../role-based-access-control/overview.md) voor account beheer |
| Sdk's van ontwikkel aars |.NET, Java, Python, Node.js |.NET, Java, Python, Node.js, C++, Ruby, PHP, go, Android, iOS |
| Prestaties van de analysewerk belasting |Geoptimaliseerde prestaties voor de werk belastingen van parallelle analyses. Hoge door Voer en IOPS. |Geoptimaliseerde prestaties voor de werk belastingen van parallelle analyses. |
| Grootte limieten |Geen limieten voor de grootte van het account, de bestands grootte of het aantal bestanden |Zie [schaalbaarheids doelen voor standaard opslag accounts](../storage/common/scalability-targets-standard-account.md) en [schaal baarheid en prestatie doelen voor Blob Storage](../storage/blobs/scalability-targets.md)voor specifieke limieten. Grotere account limieten beschikbaar door contact op te nemen met de [ondersteuning van Azure](https://azure.microsoft.com/support/faq/) |
| Geografische redundantie |Lokaal redundant (meerdere kopieën van gegevens in één Azure-regio) |Lokaal redundant (LRS), zone redundante (ZRS), wereld wijd redundante (GRS), lees toegang wereld wijd redundante (RA-GRS). Zie [hier](../storage/common/storage-redundancy.md) voor meer informatie |
| Servicestatus |Algemeen beschikbaar |Algemeen beschikbaar |
| Regionale beschikbaarheid |[Hier](https://azure.microsoft.com/regions/#services) bekijken |Beschikbaar in alle Azure-regio's |
| Prijs |[Prijzen](https://azure.microsoft.com/pricing/details/data-lake-store/) bekijken |[Prijzen](https://azure.microsoft.com/pricing/details/storage/) bekijken |