---
title: storage-files-create-storage-account-portal
description: Een opslagaccount maken voor Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 03/28/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 03cf20e5c796a7092dc16c466934f377c945ad48
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96509500"
---
Een opslagaccount is een gedeelde opslaggroep waarin u Azure-bestandsshares of andere opslagresources, zoals blobs of wachtrijen, kunt implementeren. Een opslagaccount kan een onbeperkt aantal shares bevatten. Een share kan een onbeperkt aantal bestanden opslaan, tot de capaciteitslimiet van het opslagaccount.

Een opslagaccount maken:

1. Selecteer **+** om een resource te maken in het menu aan de linkerkant.
1. Typ **opslagaccount** in het zoekvak en selecteer **Opslagaccount - blob, bestand, tabel, wachtrij** en klik vervolgens op **Maken**.
    ![Een schermopname van hoe de invoer voor het opslagaccount eruit moet zien in het dialoogvenster voor het zoeken van resources](../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png)

1. Typ *mystorageacct* in het vak **Naam**, gevolgd door een paar willekeurige cijfers totdat u een groen vinkje ziet en u weet dat dit een unieke naam is. De naam van een opslagaccount mag alleen kleine letters bevatten en moet globaal uniek zijn. Noteer de naam van het opslagaccount. U gebruikt dit later. 
1. Laat bij **Implementatiemodel** de standaardwaarde **Resource Manager** geselecteerd. Meer informatie over de verschillen tussen Azure Resource Manager en de klassieke implementatie vindt u in [Implementatiemodellen en de status van uw resources begrijpen](../articles/azure-resource-manager/management/deployment-models.md).
1. Laat bij **Prestaties** de standaardwaarde **Standard** geselecteerd.
    
    > [!NOTE]
    > In deze quickstart wordt een standaardbestandsshare gemaakt. Als u Premium-bestandsshares wilt gebruiken, selecteert u in plaats hiervan **Premium**.

1. Selecteer **StorageV2** bij **Soort account**. Zie [Azure Storage-accounts begrijpen](../articles/storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) voor meer informatie over de verschillende soorten opslagaccounts.

    > [!NOTE]
    > In deze quickstart wordt een v2-account voor algemeen gebruik gemaakt. Als u Premium-bestandsshares wilt gebruiken, selecteert u in plaats hiervan **FileStorage**.

1. Selecteer **Lokaal redundante opslag (LRS)** bij **Replicatie**. 
1. Wij adviseren om bij **Veilige overdracht vereist** altijd **Ingeschakeld** te selecteren. Zie [Versleuteling in-transit begrijpen](../articles/storage/common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) voor meer informatie over deze optie.
1. Selecteer bij **Abonnement** het abonnement waarin u het opslagaccount wilt maken. Als u slechts één abonnement hebt, wordt dit abonnement standaard weergegeven.
1. Selecteer voor **Resourcegroep** de optie **Nieuwe maken**. Voer als naam *myResourceGroup* in.
1. Selecteer bij **Locatie** **VS - oost**.
1. Laat bij **Virtuele netwerken** de standaardoptie **Uitgeschakeld** staan. 
1. Selecteer **Vastmaken aan dashboard** om het opslagaccount gemakkelijker te vinden.
1. Als u klaar bent, klikt u op **Maken** om de implementatie te starten.