---
title: Over Azure Managed HSM-beveiligings domein
description: Overzicht van het beheerde HSM-beveiligings domein, een set kern referenties die nodig zijn om een beheerde HSM te herstellen
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
author: amitbapat
ms.author: ambapat
ms.date: 09/15/2020
ms.openlocfilehash: 37e2541d0b53c96fd3f85da31b2c0ce5b68b551a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90995541"
---
# <a name="about-the-managed-hsm-security-domain"></a>Over het beheerde HSM-beveiligings domein

Het beheerde HSM-beveiligings domein (SD) is een set kern referenties die nodig zijn om een beheerde HSM te herstellen als er sprake is van een nood geval. Het beveiligings domein wordt gegenereerd in de beheerde HSM-hardware en de service software-enclaves en vertegenwoordigt ' eigendom ' van de HSM.

Elke beheerde HSM moet een beveiligings domein hebben om te kunnen worden gebruikt. Wanneer u een nieuwe beheerde HSM aanvraagt, wordt deze ingericht, maar niet geactiveerd totdat u het beveiligings domein downloadt. Wanneer een beheerde HSM is ingericht, maar niet geactiveerd, zijn er twee manieren om deze te activeren:
- Het downloaden van uw beveiligings domein is de standaard methode en biedt u de mogelijkheid om het beveiligings domein veilig op te slaan, zodat het kan worden gebruikt met een andere beheerde HSM of om te herstellen vanaf een totaal nood geval.
- Upload een bestaand beveiligings domein dat u al hebt, zodat u meerdere beheerde HSM-instanties kunt maken die hetzelfde beveiligings domein delen.

## <a name="download-your-security-domain"></a>Uw beveiligings domein downloaden

Wanneer een beheerde HSM wordt ingericht, maar niet geactiveerd, wordt door het downloaden van het beveiligings domein de kern referenties vastgelegd die nodig zijn om een volledig verlies van alle hardware te herstellen. Als u het beveiligings domein wilt downloaden, moet u ten minste 3 (Maxi maal 10) RSA-sleutel paren maken en deze open bare sleutels verzenden naar de service bij het aanvragen van het beveiligings domein downloaden. U moet ook het minimale aantal vereiste sleutels (quorum) opgeven om het beveiligings domein in de toekomst te ontsleutelen. De beheerde HSM initialiseert het beveiligings domein en versleutelt het met de open bare sleutels die u opgeeft met behulp van het algoritme voor het [delen van geheime Shamir](https://dl.acm.org/doi/10.1145/359168.359176). De BLOB voor het beveiligings domein die u downloadt, kan alleen worden ontsleuteld als er ten minste een quorum met persoonlijke sleutels beschikbaar is; u moet de persoonlijke sleutels veilig houden om te zorgen voor beveiliging van het beveiligings domein. Zodra het downloaden is voltooid, wordt de beheerde HSM geactiveerd en klaar voor gebruik.  

> [!IMPORTANT]
> Voor een volledig herstel na nood gevallen moet u het beveiligings domein en het quorum van persoonlijke sleutels die zijn gebruikt om het te beveiligen, en een volledige HSM-back-up. Als u het beveiligings domein of voldoende van de RSA-sleutels (persoonlijke sleutel) kwijtraakt om het quorum te verliezen en er geen actieve instanties van de beheerde HSM aanwezig zijn, is herstel na nood gevallen niet mogelijk.

## <a name="upload-a-security-domain"></a>Een beveiligings domein uploaden

Wanneer een beheerde HSM is ingericht, maar niet geactiveerd, kunt u een herstel proces voor een beveiligings domein initiëren. Beheerde HSM genereert een RSA-sleutel paar en retourneert de open bare sleutel. Vervolgens kunt u het beveiligings domein uploaden naar de beheerde HSM. Voordat u het uploadt, moet de client (Azure CLI of Power shell) worden voorzien van het minimale quorum aantal persoonlijke sleutels dat u hebt gebruikt tijdens het downloaden van het beveiligings domein. De client ontsleutelt het beveiligings domein met behulp van dit quorum en versleutelt het opnieuw met de open bare sleutel die u hebt gedownload toen u het herstel hebt aangevraagd. Zodra het uploaden is voltooid, wordt de beheerde HSM geactiveerd.

## <a name="backup-and-restore-behavior"></a>Gedrag voor back-up en herstel

Back-ups (volledige back-up of een back-up van één sleutel) kunnen alleen worden hersteld als de door de bron beheerde HSM (waarbij de back-up is gemaakt) en de door het doel beheerde HSM (waarbij de back-up wordt hersteld) hetzelfde beveiligings domein delen. Op deze manier definieert een beveiligings domein ook een cryptografische grens voor elke beheerde HSM.

## <a name="next-steps"></a>Volgende stappen

- Bekijk een [Overzicht van beheerde HSM's](overview.md)
- Meer informatie over het [beheren van beheerde HSM met Azure cli](key-management.md)
- Beoordeel [Best practices voor beheerde HSM's](best-practices.md)
