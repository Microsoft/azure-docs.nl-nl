---
title: bestand opnemen
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/26/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6c54bdc2738f9455926882c439ff8942c1c0a5a6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759757"
---
Azure-bestandsshares worden geïmplementeerd in *opslagaccounts*. Dat zijn objecten op het hoogste niveau die een gedeelde opslagpool vertegenwoordigen. Deze opslagpool kan worden gebruikt om meerdere bestandsshares en andere opslagresources, zoals blobcontainers, wachtrijen of tabellen, te implementeren. Alle opslagresources die in een opslagaccount worden geïmplementeerd, delen de limieten die van toepassing zijn op dat opslagaccount. Zie Schaalbaarheids- en prestatiedoelen [voor Azure Files opslagaccountlimieten.](../articles/storage/files/storage-files-scale-targets.md)

Er zijn twee belangrijke soorten opslagaccounts die u voor Azure Files-implementatie gaat gebruiken: 
- **GPv2-opslagaccounts (versie twee voor algemeen gebruik)** : Met GPv2-opslagaccounts kunt u Azure-bestandsshares implementeren op (HDD-)hardware met een standaard/harde schijf. Naast het opslaan van Azure-bestandsshares kunnen GPv2-opslagaccounts andere opslagresources, zoals blobcontainers, wachtrijen of tabellen, opslaan. 
- **FileStorage-opslagaccounts**: Met FileStorage-opslagaccounts kunt u Azure-bestandsshares implementeren op (SSD-)hardware met een premium/solid-state schijf. FileStorage-accounts kunnen alleen worden gebruikt voor het opslaan van Azure-bestandsshares. Er kunnen geen andere opslagresources (blobcontainers, wachtrijen, tabellen enz.) worden geïmplementeerd in een FileStorage-account. Alleen FileStorage-accounts kunnen zowel SMB- als NFS-bestandsshares implementeren.

Er zijn verschillende andere typen opslagaccounts die u kunt tegenkomen in Azure Portal, PowerShell of CLI. Twee typen opslagaccounts, BlockBlobStorage- en BlobStorage-opslagaccounts, mogen geen Azure-bestandsshares bevatten. De andere twee typen opslagaccounts die u mogelijk ziet, zijn GPv1-opslagaccounts (versie 1 voor algemeen gebruik) en klassieke opslagaccounts. Beide typen kunnen Azure-bestandsshares bevatten. Hoewel GPv1-opslagaccounts en klassieke opslagaccounts Azure-bestandsshares kunnen bevatten, zijn de meeste nieuwe functies van Azure Files alleen beschikbaar in GPv2-en FileStorage-opslagaccounts. Daarom raden we u aan om alleen GPv2- en FileStorage-opslagaccounts te gebruiken voor nieuwe implementaties en om GPv1-opslagaccounts en klassieke opslagaccounts bij te werken als deze al in uw omgeving aanwezig zijn.  