---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 385ebffb4780543b532c81b89f1847f5c84cd0ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582107"
---
De volgende voor behoud zijn van toepassing op gegevens die worden verplaatst naar Azure.

- We raden aan dat er niet meer dan één apparaat naar dezelfde container moet schrijven.
- Als u een bestaand Azure-object (zoals een BLOB of een bestand) in de Cloud hebt die dezelfde naam heeft als het object dat wordt gekopieerd, overschrijft het apparaat het bestand in de Cloud.
- Een lege Directory-hiërarchie (zonder bestanden) die is gemaakt onder share mappen wordt niet geüpload naar de BLOB-containers.
- U kunt de gegevens kopiëren met behulp van slepen en neerzetten met de Verkenner of via de opdracht regel. Als de totale grootte van gekopieerde bestanden groter is dan 10 GB, raden we u aan een programma voor bulksgewijs kopiëren te gebruiken, zoals `Robocopy` of `rsync` . De hulpprogram ma's voor bulksgewijs kopiëren proberen de Kopieer bewerking opnieuw uit te voeren op onregelmatige fouten en bieden extra tolerantie.
- Als de share die is gekoppeld aan de Azure storage-container blobs uploadt die niet overeenkomen met het type blobs dat is gedefinieerd voor de share op het moment dat deze wordt gemaakt, worden dergelijke blobs niet bijgewerkt. Als u bijvoorbeeld een blok-BLOB share op het apparaat maakt, koppelt u de share met een bestaande Cloud container met pagina-blobs, vernieuw die share om de bestanden te downloaden en wijzig vervolgens enkele van de vernieuwde bestanden die al zijn opgeslagen als pagina-blobs in de Cloud, er worden upload fouten weer geven.
- Nadat een bestand in de shares is gemaakt, kan de naam van het bestand niet meer worden gewijzigd.
- Als een bestand uit een share wordt verwijderd, wordt de vermelding in het opslagaccount niet verwijderd.
- Als u `rsync` gebruikt voor het kopiëren van gegevens, wordt de optie `rsync -a` niet ondersteund.