---
title: Er worden installatie kopieën van virtuele machines (VM) verplaatst naar Managed Disk Storage in azure Marketplace
description: Om sneller, betrouwbaardere opslag en ondersteuning te bieden voor nieuwe Marketplace-functies en-mogelijkheden, verplaatsen we VM-installatie kopieën van Marketplace naar Managed Disk Storage.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/22/2019
author: kriti-ms
ms.author: krsh
ms.openlocfilehash: e3d20b5e9206cf05d749f75982a07937a8caf078
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201087"
---
# <a name="were-moving-virtual-machine-vm-images-on-azure-marketplace-to-managed-disk-storage"></a>Er worden installatie kopieën van virtuele machines (VM) op Azure Marketplace verplaatst naar Managed Disk Storage

Om sneller, betrouwbaardere opslag en ondersteuning te bieden voor nieuwe Marketplace-functies en-mogelijkheden, verplaatsen we VM-installatie kopieën van Marketplace naar Managed Disk Storage.

Vanaf 2 januari 2020 worden VM-installatie kopieën verplaatst naar beheerde schijf opslag in fasen. In de eerste fase verplaatsen we alleen installatie kopieën zonder nieuwe implementaties of actieve Vm's in de afgelopen 90 dagen. Voordat we een van de installatie kopieën verplaatsen, sturen we een e-mail om de uitgever te laten weten welke afbeeldingen worden verplaatst en wanneer ze worden verplaatst.

Uitgevers of consumenten hoeven geen actie te ondernemen en gebruikers worden niet beïnvloed. De Marketplace-aanbiedingen blijven beschikbaar en klanten kunnen nog steeds beheerde virtuele machines implementeren vanaf deze installatie kopieën, tijdens en na de verplaatsing.

Als u vragen hebt, kunt u [contact met ons](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff)opnemen.

## <a name="faqs"></a>Veelgestelde vragen

### <a name="would-the-users-of-my-vm-images-experience-an-outage"></a>Werken de gebruikers van mijn VM-installatie kopieën een storing?

Gebruikers van de VM-installatie kopieën hebben geen onderbrekingen. 

### <a name="how-long-does-it-take-for-the-process-to-complete"></a>Hoe lang duurt het om het proces te volt ooien?

Het kan tot 24 uur duren voordat de migratie is voltooid.

### <a name="do-i-need-to-take-any-action"></a>Moet ik actie ondernemen?

Nee. Uitgevers of consumenten hoeven geen actie te ondernemen.

### <a name="do-i-have-to-update-my-system-to-call-the-cloud-portal-apis-in-a-different-way-after-they-are-moved-to-managed-disk-storage"></a>Moet ik mijn systeem bijwerken om de Cloud Portal-Api's op een andere manier aan te roepen nadat ze zijn verplaatst naar Managed Disk-opslag?

Nee. Uw bestaande API-aanroepen blijven werken.

### <a name="would-all-my-vm-images-be-moved-to-managed-disk-at-the-same-time"></a>Moeten al mijn VM-installatie kopieën tegelijk worden verplaatst naar de beheerde schijf?

We gaan al uw VM-installatie kopieën op dezelfde dag verplaatsen. U ontvangt een melding zodra deze zijn verplaatst.

### <a name="can-i-request-to-schedule-the-move-of-my-vm-images-to-a-later-time"></a>Kan ik aanvragen om het verplaatsen van mijn VM-installatie kopieën naar een later tijdstip te plannen?

U wordt aangeraden de installatie kopieën op de geplande datum te verplaatsen. Als u echter problemen hebt, kunt u contact met ons opnemen om de verplaatsing opnieuw te plannen.

### <a name="can-i-publish-updates-to-my-vm-images-during-the-move"></a>Kan ik tijdens de verhuizing updates voor mijn VM-installatie kopieën publiceren?

Updates van de VM-installatie kopieën kunnen niet worden gemaakt tijdens de verplaatsing.

### <a name="will-the-publishing-process-change-after-my-vm-image-is-moved-to-managed-disk"></a>Wordt het publicatie proces gewijzigd nadat mijn VM-installatie kopie naar een beheerde schijf is verplaatst?

Nee, het publicatie proces blijft hetzelfde. 

### <a name="can-the-publisher-move-their-offers-to-managed-disk"></a>Kan de uitgever hun aanbiedingen verplaatsen naar een beheerde schijf?

Nee, de uitgevers kunnen hun aanbiedingen niet naar Managed Disk verplaatsen. Ze moeten wachten en hun afbeeldingen worden automatisch verplaatst. Er worden meldingen verzonden naar de uitgever voordat we wijzigingen aanbrengen.
