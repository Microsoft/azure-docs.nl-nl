---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: dc18efe03cbc8a3f657ae4afc781941e8e76611c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96466757"
---
De volgende voor behoud zijn van toepassing op gegevens die worden verplaatst naar Azure.

- We raden aan dat er niet meer dan één apparaat naar dezelfde container moet schrijven.
- Als u een bestaand Azure-object (zoals een BLOB of een bestand) in de Cloud hebt met dezelfde naam als het object dat wordt gekopieerd, overschrijft het apparaat het bestand in de Cloud.
- Een lege Directory-hiërarchie (zonder bestanden) die is gemaakt onder share mappen wordt niet geüpload naar de BLOB-containers.
- U kunt de gegevens kopiëren met behulp van slepen/neerzetten, of via de opdrachtregel. Als de grootten van alle bestanden die worden gekopieerd, samen meer zijn dan 10 GB, raden we u aan een programma voor bulkgewijs kopiëren te gebruiken, zoals Robocopy of Rsync. Met de hulpprogramma’s voor bulkgewijs kopiëren wordt de kopieerbewerking opnieuw geprobeerd bij onregelmatige fouten. Deze programma’s bieden extra tolerantie. Als u Blob Storage via REST, AzCopy of Azure Storage Explorer kunt gebruiken.
- Als de share die is gekoppeld aan de Azure storage-container blobs uploadt die niet overeenkomen met het type blobs dat is gedefinieerd voor de share op het moment dat deze wordt gemaakt, worden dergelijke blobs niet bijgewerkt. U kunt bijvoorbeeld een blok-blobshare maken op het apparaat. Koppel de share aan een bestaande cloudcontainer die pagina-blobs bevat. Vernieuw die share om de bestanden te downloaden. Wijzig enkele van de vernieuwde bestanden die al in de cloud zijn opgeslagen als pagina-blobs. U ziet nu uploadfouten.
- Nadat een bestand in de shares is gemaakt, kan de naam van het bestand niet meer worden gewijzigd.
- Als een bestand uit een share wordt verwijderd, wordt de vermelding in het opslagaccount niet verwijderd.
- Als rsync wordt gebruikt om gegevens te kopiëren, `rsync -a` wordt de optie niet ondersteund.
