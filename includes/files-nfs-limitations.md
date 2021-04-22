---
title: bestand opnemen
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/04/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6e819e1078ac90ef16070702e7961122b06c1d6f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107879570"
---
In de preview-versie heeft NFS de volgende beperkingen:

- NFS 4.1 ondersteunt momenteel alleen de meeste functies van de [protocolspecificatie](https://tools.ietf.org/html/rfc5661). Sommige functies, zoals overdrachten en callback van alle soorten, vergrendelingsupgrades en downgrades, Kerberos-verificatie en versleuteling worden niet ondersteund.
- Als het merendeel van uw aanvragen op metagegevens is gericht, is de latentie slechter in vergelijking met lees-/schrijf-/updatebewerkingen.
- NFS-shares kunnen alleen worden ingeschakeld/gemaakt op nieuwe opslagaccounts en niet op de bestaande
- Alleen de REST API's voor het beheervlak worden ondersteund. REST API's voor gegevensvlak zijn niet beschikbaar, wat betekent dat hulpprogramma's zoals Storage Explorer niet werken met NFS-shares en u ook niet door NFS-sharegegevens in de Azure Portal.
- AzCopy wordt momenteel niet ondersteund.
- Alleen beschikbaar voor de Premium-laag.
- NFS-shares accepteren alleen numerieke UID/GID. Om te voorkomen dat uw clients alfanumerieke UID/GID verzenden, moet u id-toewijzing uitschakelen.
- Shares kunnen alleen vanuit één opslagaccount op een afzonderlijke VM worden koppelt bij het gebruik van privékoppelingen. Het koppelen van shares vanuit andere opslagaccounts mislukt.
- U kunt het beste vertrouwen op de machtigingen die zijn toegewezen aan de primaire groep. Soms kunnen machtigingen die zijn toegewezen aan de niet-primaire groep van de gebruiker ertoe leiden dat toegang wordt geweigerd vanwege een bekende fout.

### <a name="azure-storage-features-not-yet-supported"></a>Azure Storage nog niet ondersteund

Bovendien zijn de volgende Azure Files niet beschikbaar met NFS-shares:

- Verificatie op basis van identiteit
- Ondersteuning voor Azure Backup
- Momentopnamen
- Voorlopig verwijderen
- Volledige ondersteuning voor versleuteling in transit (zie [NFS-beveiliging) voor meer informatie](../articles/storage/files/storage-files-compare-protocols.md#security)
- Azure File Sync (alleen beschikbaar voor Windows-clients, die NFS 4.1 niet ondersteunt)
