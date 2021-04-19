---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/15/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5798ad8721d8bf2924aa97876d0c8354681d3568
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718158"
---
Als u een v2-opslagaccount voor algemeen gebruik wilt maken in de Azure Portal, volgt u deze stappen:

1. Selecteer **Alle services** in het menu van Azure Portal. Typ in de lijst met resources **Opslagaccounts**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Opslagaccounts**.
1. Kies + **Nieuw in** het venster Opslagaccounts dat wordt **weergegeven.**
1. Selecteer op **de** blade Basisinformatie het abonnement waarin u het opslagaccount wilt maken.
1. Selecteer onder het veld **Resourcegroep** de gewenste resourcegroep of maak een nieuwe.  Zie [Azure Resource Manager overview](../articles/azure-resource-manager/management/overview.md) (Overzicht van Azure Resource Manager) voor meer informatie over Azure-resourcegroepen.
1. Voer vervolgens een naam in voor het opslagaccount. De naam die u kiest, moet uniek zijn binnen Azure. Verder moet de naam 3 tot 24 tekens lang zijn en mag deze alleen cijfers en kleine letters bevatten.
1. Selecteer een regio voor uw opslagaccount of gebruik de standaardregio.
1. Selecteer een prestatielaag. De standaardlaag is *Standard*.
1. Geef aan hoe de opslagaccount moet worden gerepliceerd. De standaardoptie voor redundantie is *GEOGRAFISCH redundante opslag (GRS).* Zie [Azure Storage redundancy](../articles/storage/common/storage-redundancy.md) (Azure Storage-redundantie) voor meer informatie over beschikbare replicatieopties.
1. Er zijn extra opties beschikbaar op **de blades Geavanceerd,** **Netwerken,** **Gegevensbeveiliging** en **Tags.** Als u Azure Data Lake Storage, kiest **u** de blade Geavanceerd en stelt u **Hiërarchische naamruimte in** op **Ingeschakeld.** Zie [Introduction to Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md) (Inleiding tot Azure Data Lake Storage Gen2) voor meer informatie
1. Selecteer **Beoordelen en maken** om uw opslagaccountinstellingen te bekijken en het account te maken.
1. Selecteer **Maken**.

In de volgende afbeelding ziet u de instellingen op de blade **Basisinformatie** voor een nieuw opslagaccount:

:::image type="content" source="media/storage-create-account-portal-include/account-create-portal.png" alt-text="Schermopname die laat zien hoe u een opslagaccount maakt in Azure Portal." lightbox="media/storage-create-account-portal-include/account-create-portal.png":::
