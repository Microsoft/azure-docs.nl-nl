---
title: Onbedoeld verwijderen voor komen-Azure-bestands shares
description: Meer informatie over zacht verwijderen voor Azure-bestands shares en hoe u deze kunt gebruiken voor het herstellen van gegevens en het voor komen van onopzettelijke verwijderingen.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 0fecc9fc954a1ac648e8f60badf69ad1d2e8f1cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93126937"
---
# <a name="prevent-accidental-deletion-of-azure-file-shares"></a>Onopzettelijke verwijdering van Azure-bestands shares voor komen

Azure Storage biedt nu een tijdelijke verwijdering voor bestands shares. Met zacht verwijderen kunt u de bestands share herstellen wanneer deze per ongeluk door een toepassing of een ander opslag account wordt verwijderd.

## <a name="how-soft-delete-works"></a>Hoe zacht verwijderen werkt

Als zacht verwijderen voor Azure-bestands shares is ingeschakeld en een bestands share wordt verwijderd, wordt deze overgezet naar een voorlopig verwijderde status in plaats van dat ze permanent worden gewist. U kunt de hoeveelheid tijd waarvoor tijdelijke verwijderde gegevens kunnen worden hersteld, configureren voordat deze definitief wordt verwijderd en de share tijdens deze Bewaar periode op elk gewenst moment verwijderen. Nadat de verwijdering is ongedaan gemaakt, wordt de share en alle inhoud, met inbegrip van moment opnamen, hersteld naar de status waarin deze zich bevond vóór de verwijdering. Zacht verwijderen werkt alleen op een bestands share niveau: afzonderlijke bestanden die worden verwijderd, worden nog steeds permanent gewist.

Zacht verwijderen kan worden ingeschakeld op nieuwe of bestaande bestands shares. Voorlopig verwijderen is ook achterwaarts compatibel, zodat u geen wijzigingen hoeft aan te brengen in uw toepassingen om te kunnen profiteren van de beveiliging van de tijdelijke verwijdering. 

Als u een bestands share permanent wilt verwijderen voordat de verloop tijd verloopt, moet u het verwijderen van de share ongedaan maken, de optie voor het verwijderen van de software uitschakelen en vervolgens de share opnieuw verwijderen. Vervolgens moet u voorlopig verwijderen opnieuw inschakelen omdat andere bestands shares in dat opslag account kwetsbaar zijn voor onbedoeld verwijderen, terwijl zacht verwijderen is uitgeschakeld.

Voor soft-verwijderde Premium-bestands shares wordt het quotum voor bestands shares (de ingerichte grootte van een bestands share) gebruikt in de berekening van het totale opslag account tot de tijdelijke datum waarop de share verloopt, wanneer deze volledig is verwijderd.

## <a name="configuration-settings"></a>Configuratie-instellingen

### <a name="enabling-or-disabling-soft-delete"></a>Voorlopig verwijderen in-of uitschakelen

Voorlopig verwijderen voor bestands shares is ingeschakeld op het niveau van het opslag account, waardoor de instellingen voor voorlopig verwijderen van toepassing zijn op alle bestands shares in een opslag account. U kunt voorlopig verwijderen op elk gewenst moment in-of uitschakelen. Wanneer u een nieuw opslag account maakt, is de functie voor het voorlopig verwijderen van bestands shares standaard uitgeschakeld. u kunt deze functie inschakelen tijdens de implementatie of op elk gewenst moment. Voorlopig verwijderen blijft standaard uitgeschakeld voor bestaande opslag accounts. Als u [Azure file share-back-up](../../backup/azure-file-share-backup-overview.md) voor een Azure-bestands share hebt geconfigureerd, wordt er automatisch een tijdelijke verwijdering voor Azure-bestands shares ingeschakeld op het opslag account van die share.

Als u de optie voor het tijdelijk verwijderen van bestands shares inschakelt, verwijdert u enkele bestands shares en schakelt u de optie voorlopig verwijderen uit. als de shares zijn opgeslagen in die periode, kunt u deze bestands shares nog steeds openen en herstellen. Wanneer u voorlopig verwijderen inschakelt, moet u ook de Bewaar periode configureren.

### <a name="retention-period"></a>Bewaarperiode

De retentie periode is de hoeveelheid tijd die de tijdelijke verwijderde bestands shares worden opgeslagen en beschikbaar zijn voor herstel. Voor bestands shares die expliciet worden verwijderd, wordt de klok van de Bewaar periode gestart wanneer de gegevens worden verwijderd. Op dit moment kunt u een Bewaar periode tussen 1 en 365 dagen opgeven. U kunt de tijdelijke Bewaar periode voor verwijderen op elk gewenst moment wijzigen. Een bijgewerkte Bewaar periode is alleen van toepassing op shares die zijn verwijderd nadat de Bewaar periode is bijgewerkt. Shares die zijn verwijderd voordat de retentie periode is bijgewerkt, verloopt op basis van de retentie periode die is geconfigureerd tijdens het verwijderen van de gegevens.

## <a name="pricing-and-billing"></a>Prijzen en facturering

Standaard-en Premium-bestands shares worden gefactureerd op de gebruikte capaciteit wanneer deze worden verwijderd, in plaats van de ingerichte capaciteit. Daarnaast worden Premium-bestands shares gefactureerd op basis van de moment opname met de status voorlopig verwijderen. Standaard bestands shares worden gefactureerd op basis van het normale aantal in de status voorlopig verwijderen. Er worden geen kosten in rekening gebracht voor gegevens die permanent worden verwijderd na de geconfigureerde Bewaar periode.

Zie de [pagina met prijzen voor azure File Storage](https://azure.microsoft.com/pricing/details/storage/files/)voor meer informatie over de prijzen voor Azure-file storage in het algemeen.

Wanneer u voorlopig verwijderen voor het eerst inschakelt, kunt u het beste een kleine Bewaar periode gebruiken om beter te begrijpen hoe de functie van invloed is op uw factuur.

## <a name="next-steps"></a>Volgende stappen

Als u wilt weten hoe u voorlopig verwijderen kunt inschakelen en gebruiken, gaat u verder met het [inschakelen van zacht verwijderen](storage-files-enable-soft-delete.md).
