---
title: Release opmerkingen voor Azure-toepassing consistent momentopname hulp programma voor Azure NetApp Files | Microsoft Docs
description: Bevat opmerkingen bij de release voor het hulp programma voor consistente moment opnamen van Azure-toepassing dat u met Azure NetApp Files kunt gebruiken.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: phjensen
ms.openlocfilehash: 01fb93dcd0a1d5c1db615c47d7811a0baa863c9b
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105111431"
---
# <a name="release-notes-for-azure-application-consistent-snapshot-tool-preview"></a>Release opmerkingen voor Azure-toepassing consistent momentopname programma (preview-versie)

Deze pagina geeft een overzicht van belang rijke wijzigingen in AzAcSnap om nieuwe functionaliteit te bieden of defecten op te lossen.

## <a name="march-2021"></a>Maart-2021

### <a name="azacsnap-v50-preview-build2021031830771"></a>AzAcSnap v 5.0 Preview (Build: 20210318.30771)

AzAcSnap v 5.0 Preview (Build: 20210318.30771) is uitgebracht met de volgende oplossingen en verbeteringen:

- De nood zaak om de AZACSNAP-gebruiker toe te voegen aan de SAP HANA Tenant Db's, raadpleegt u de sectie [communicatie inschakelen met SAP Hana](azacsnap-installation.md#enable-communication-with-sap-hana) .
- Oplossing voor het toestaan van een [herstel bewerking](azacsnap-cmd-ref-restore.md) met volumes die zijn geconfigureerd met hand matige QoS.
- Er is een mutex-besturings element toegevoegd om SSH-verbindingen te beperken voor een groot Azure-exemplaar.
- Herstel het installatie programma voor het afhandelen van padnamen met spaties en andere gerelateerde problemen.
- Bij de voor bereiding voor de ondersteuning van andere database servers, is de optionele para meter--hanasid naar '--dbsid ' gewijzigd.

Down load de [nieuwste versie](https://aka.ms/azacsnapdownload) van het installatie programma en lees hoe u aan de [slag kunt gaan](azacsnap-get-started.md).

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag met Azure-toepassing consistent momentopname programma](azacsnap-get-started.md)
