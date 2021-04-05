---
title: Beveiligingsoverzicht van Azure Data Share
description: Beveiligingsoverzicht van Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/17/2020
ms.openlocfilehash: 4e62645dd5a7a8336df4fccf12daebc730a91168
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97678419"
---
# <a name="security-overview-for-azure-data-share"></a>Beveiligingsoverzicht van Azure Data Share

Dit artikel bevat een overzicht van de beveiliging van de Azure data share-service.

## <a name="security-overview"></a>Beveiligingsoverzicht

Azure data share maakt gebruik van de onderliggende beveiliging die Azure biedt voor het beveiligen van gegevens in rust en onderweg. Gegevens worden op rest versleuteld, waarbij ondersteund door het onderliggende gegevens archief. Gegevens worden ook versleuteld tijdens de overdracht met behulp van TLS 1,2. Meta gegevens over een gegevens share worden ook versleuteld in rust en onderweg. De inhoud van de klant gegevens die worden gedeeld, wordt niet opgeslagen door de Azure-gegevens share.

Azure data share maakt gebruik van beheerde identiteiten (voorheen bekend als MSI) voor toegang tot gegevens archieven die worden gebruikt voor het delen van gegevens. Er is geen uitwisseling van referenties tussen een gegevens provider en een gegevens verbruiker. Raadpleeg [beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)voor meer informatie over beheerde identiteiten. Raadpleeg [rollen en vereisten](concepts-roles-permissions.md)voor meer informatie over de rollen en machtigingen die nodig zijn voor het delen van gegevens.

## <a name="access-control"></a>Toegangsbeheer

Toegangs beheer voor Azure data share kan worden ingesteld op het resource niveau van de gegevens share om te controleren of het toegankelijk is voor gebruikers die zijn gemachtigd. Eigenaar en bijdrager van een gegevens share bron kunnen gegevens delen, shares ontvangen en wijzigingen aanbrengen in bestaande shares. Lezer van een gegevens share bron kan shares weer geven, maar kan geen wijzigingen aanbrengen. 

Zodra een share is gemaakt of ontvangen, kunnen gebruikers met de juiste machtigingen voor de gegevens share bron wijzigingen aanbrengen. Wanneer een gebruiker die een share maakt of ontvangt, de organisatie verlaat, wordt de share of stop data flow van de gegevens niet beëindigd. Andere gebruikers met de juiste machtigingen voor de gegevens share bron kunnen de share blijven beheren.

## <a name="share-data-from-or-to-data-stores-with-firewall-enabled"></a>Gegevens delen van of naar gegevens archieven waarvoor Firewall is ingeschakeld
Als u gegevens wilt delen van of naar opslag accounts waarvoor Firewall is ingeschakeld, moet u **vertrouwde micro soft-Services** inschakelen in uw opslag account. Zie [Azure Storage firewalls en virtuele netwerken configureren](
https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services) voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over hoe u gegevens kunt beginnen delen, gaat u verder naar de zelfstudie [uw gegevens delen](share-your-data.md).
