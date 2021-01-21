---
title: Een opslag account maken voor Azure Data Lake Storage Gen2
description: Meer informatie over het maken van een opslag account voor gebruik met Azure Data Lake Storage Gen2.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 08/31/2020
ms.service: storage
ms.reviewer: stewu
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 712f1dc0679ee49791831e782fb68c39a757870a
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624334"
---
# <a name="create-a-storage-account-to-use-with-azure-data-lake-storage-gen2"></a>Een opslag account maken voor gebruik met Azure Data Lake Storage Gen2

Als u Data Lake Storage Gen2 mogelijkheden wilt gebruiken, maakt u een opslag account met een hiërarchische naam ruimte.

## <a name="choose-a-storage-account-type"></a>Een type opslag account kiezen

Data Lake Storage mogelijkheden worden ondersteund in de volgende typen opslag accounts:

- Algemeen gebruik v2
- BlockBlobStorage

Zie [overzicht van opslag accounts](../common/storage-account-overview.md)voor meer informatie over hoe u ertussen kunt kiezen.

## <a name="create-a-storage-account-with-a-hierarchical-namespace"></a>Een opslag account maken met een hiërarchische naam ruimte

Maak een [v2-account voor algemeen gebruik](../common/storage-account-create.md) of een [BlockBlobStorage](storage-blob-create-account-block-blob.md) -account met de instelling **hiërarchische naam ruimte** ingeschakeld.

Ontgrendel Data Lake Storage mogelijkheden wanneer u het account maakt door de instelling **hiërarchische naam ruimte** in te scha kelen op het tabblad **Geavanceerd** van de pagina **opslag account maken** . U moet deze instelling inschakelen wanneer u het account maakt. U kunt deze later niet inschakelen.

In de volgende afbeelding ziet u deze instelling op de pagina **opslag account maken** .

> [!div class="mx-imgBorder"]
> ![Instelling voor hiërarchische naam ruimte](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

Als u een bestaand opslag account hebt dat u wilt gebruiken met Data Lake Storage en de instelling van de hiërarchische naam ruimte is uitgeschakeld, moet u de gegevens migreren naar een nieuw opslag account waarvoor de instelling is ingeschakeld.

> [!NOTE]
> De **gegevens bescherming** en de **hiërarchische naam ruimte** kunnen niet tegelijkertijd worden ingeschakeld.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van opslagaccounts](../common/storage-account-overview.md)
- [Azure Data Lake Storage Gen2 gebruiken voor big data vereisten](data-lake-storage-data-scenarios.md)
- [Toegangsbeheer in Data Lake Storage Gen2](data-lake-storage-access-control.md)
