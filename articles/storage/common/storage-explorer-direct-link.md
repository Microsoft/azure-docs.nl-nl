---
title: Azure Storage Explorer directe koppeling | Microsoft Docs
description: Documentatie van Azure Storage Explorer direct-koppeling
services: storage
author: chuye
ms.service: storage
ms.topic: article
ms.date: 02/24/2021
ms.author: chuye
ms.openlocfilehash: 121df1e1c3ab6d0741d3e4da1144a86938da70ed
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582255"
---
# <a name="azure-storage-explorer-direct-link"></a>Azure Storage Explorer directe koppeling

In Windows en macOS ondersteunt Storage Explorer Uri's met het `storageexplorer://` protocol. Deze Uri's worden "rechtstreekse koppelingen" genoemd. Een directe koppeling verwijst naar een Azure Storage Resource in Storage Explorer. Als u een rechtstreekse koppeling volgt, wordt Storage Explorer geopend en gaat u naar de resource waarnaar wordt verwezen. In dit artikel wordt beschreven hoe directe koppelingen werken en hoe u deze kunt gebruiken.

## <a name="how-direct-links-work"></a>Hoe direct koppelingen werken

Storage Explorer gebruikt de structuur weergave voor het visualiseren van resources in Azure. Een directe koppeling bevat de hiërarchische informatie voor het gekoppelde resource knooppunt in de boom structuur. Wanneer een directe koppeling wordt gevolgd, wordt Storage Explorer geopend en ontvangt de para meters in de direct-koppeling. Storage Explorer vervolgens deze para meters gebruikt om naar de gekoppelde resource in de structuur weergave te navigeren.

> [!IMPORTANT]
> U moet zijn aangemeld en de benodigde machtigingen hebben om toegang te krijgen tot de gekoppelde resource voor een directe koppeling naar werk.

## <a name="parameters"></a>Parameters

Een Storage Explorer directe koppeling begint altijd met het protocol `storageexplorer://` . In de volgende tabel worden de mogelijke para meters in een directe koppeling uitgelegd.

Parameter | Beschrijving
:---------| :---------
`v`         | De versie van het protocol direct link.
`accountid` | De Azure Resource Manager Resource-ID van het opslag account voor de gekoppelde resource. Als de gekoppelde resource een opslag account is, is deze ID de Azure Resource Manager Resource-ID van dat opslag account. Anders is deze ID de Azure Resource Manager Resource-ID van het opslag account waartoe de gekoppelde resource behoort.
`resourcetype` | Optioneel. Wordt alleen gebruikt als de gekoppelde resource een BLOB-container, een bestands share, een wachtrij of een tabel is. Moet een van de ' Azure. zijn ', ' Azure. file share ', ' Azure. Queue ', ' Azure. file share ' zijn.
`resourcename` | Optioneel. Wordt alleen gebruikt als de gekoppelde resource een BLOB-container, een bestands share, een wachtrij of een tabel is. De naam van de gekoppelde resource.

Hier volgt een voor beeld van een rechtstreekse koppeling naar een BLOB-container. 
`storageexplorer://v=1&accountid=/subscriptions/the_subscription_id/resourceGroups/the_resource_group_name/providers/Microsoft.Storage/storageAccounts/the_storage_account_name&subscriptionid=the_subscription_id&resourcetype=Azure.BlobContainer&resourcename=the_blob_container_name`

## <a name="get-a-direct-link-from-storage-explorer"></a>Een directe koppeling van Storage Explorer ophalen

U kunt Storage Explorer gebruiken om een directe koppeling voor een resource op te halen. Open het context menu van het knoop punt voor de resource in de structuur weergave. Gebruik vervolgens de actie ' direct koppelen ' om de directe koppeling naar het klem bord te kopiëren.

## <a name="direct-link-limitations"></a>Beperkingen voor directe koppelingen

Directe koppelingen worden alleen ondersteund voor resources onder abonnements knooppunten. Daarnaast worden alleen de volgende resource typen ondersteund:

- Storage Accounts
- Blobcontainers
- Wachtrijen
- Bestandsshares
- Tables
