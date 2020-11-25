---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/25/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: f8959904482bda8dc26e4df626377027e1ab4bd1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005538"
---
Als u een v2-opslagaccount voor algemeen gebruik wilt maken in de Azure Portal, volgt u deze stappen:

1. Selecteer **Alle services** in het menu van Azure Portal. Typ in de lijst met resources **Opslagaccounts**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Opslagaccounts**.
1. Kies in het venster **Opslagaccounts** dat wordt weergegeven de optie **Toevoegen**.
1. Selecteer op het tabblad **Basisbeginselen** het abonnement waarin u het opslagaccount wilt maken.
1. Selecteer onder het veld **Resourcegroep** de gewenste resourcegroep of maak een nieuwe.  Zie [Azure Resource Manager overview](../articles/azure-resource-manager/management/overview.md) (Overzicht van Azure Resource Manager) voor meer informatie over Azure-resourcegroepen.
1. Voer vervolgens een naam in voor het opslagaccount. De naam die u kiest, moet uniek zijn binnen Azure. Verder moet de naam 3 tot 24 tekens lang zijn en mag deze alleen cijfers en kleine letters bevatten.
1. Selecteer een locatie voor uw opslagaccount of gebruik de standaardlocatie.
1. Selecteer een prestatielaag. De standaardlaag is *Standard*.
1. Stel het veld **Soort account** in op *Opslag V2 (algemeen gebruik v2)* .
1. Geef aan hoe de opslagaccount moet worden gerepliceerd. De standaardoptie voor replicatie is *Geografisch redundante opslag met leestoegang (RA-GRS)* . Zie [Azure Storage redundancy](../articles/storage/common/storage-redundancy.md) (Azure Storage-redundantie) voor meer informatie over beschikbare replicatieopties.
1. Geef de toegangslaag voor blobs in de opslagaccount op. De standaardlaag is *dynamisch*. Meer informatie over blob-toegangslagen vindt u in [Dynamische, statische en archiveringstoegangslagen voor blobs](../articles/storage/blobs/storage-blob-storage-tiers.md).
1. Als u Azure Data Lake Storage wilt gebruiken, kiest u het tabblad **Geavanceerd** en stelt u **Hiërarchische naamruimte** in op **Ingeschakeld**. Zie [Introduction to Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md) (Inleiding tot Azure Data Lake Storage Gen2) voor meer informatie
1. Selecteer **Beoordelen en maken** om uw opslagaccountinstellingen te bekijken en het account te maken.
1. Selecteer **Maken**.

In de volgende afbeelding ziet u de instellingen op het tabblad **Basisbeginselen** voor een nieuw opslagaccount:

:::image type="content" source="media/storage-create-account-portal-include/account-create-portal.png" alt-text="Schermopname waarin te zien is hoe u in Azure Portal een opslagaccount maakt":::