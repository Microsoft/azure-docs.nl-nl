---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/24/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 75acbb30c2bf811b7ae72d6939b9f164554fdd32
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98859729"
---
- Alleen [software-en HSM RSA-sleutels](../articles/key-vault/keys/about-keys.md) met een grootte van 2.048 bits, 3.072-bits en 4.096 bits worden ondersteund, geen andere sleutels of grootten.
    - Voor [HSM](../articles/key-vault/keys/hsm-protected-keys.md) -sleutels is de **Premium** -laag van Azure-sleutel kluizen vereist.
- Schijven die zijn gemaakt op basis van aangepaste installatie kopieën die zijn versleuteld met versleuteling aan de server zijde en door de klant beheerde sleutels moeten worden versleuteld met dezelfde door de klant beheerde sleutels en moeten zich in hetzelfde abonnement bevinden.
- Moment opnamen die zijn gemaakt op basis van schijven die zijn versleuteld met versleuteling aan de server zijde en door de klant beheerde sleutels moeten worden versleuteld met dezelfde door de klant beheerde sleutels.
- Alle resources met betrekking tot uw door de klant beheerde sleutels (Azure Key kluizen, schijf versleutelings sets, Vm's, schijven en moment opnamen) moeten zich in hetzelfde abonnement en dezelfde regio bevinden.
- Schijven, moment opnamen en installatie kopieën die zijn versleuteld met door de klant beheerde sleutels, kunnen niet worden verplaatst naar een andere resource groep en een ander abonnement.
- Beheerde schijven die momenteel of eerder zijn versleuteld met Azure Disk Encryption kunnen niet worden versleuteld met door de klant beheerde sleutels.
- Kan Maxi maal 1000 schijf versleutelings sets per regio per abonnement maken.
- Zie [Preview: door de klant beheerde sleutels gebruiken voor het versleutelen van afbeeldingen](../articles/virtual-machines/image-version-encryption.md)voor meer informatie over het gebruik van door de klant beheerde sleutels met galerieën met gedeelde afbeeldingen.
