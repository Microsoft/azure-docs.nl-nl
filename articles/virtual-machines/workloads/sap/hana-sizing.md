---
title: Grootte van SAP HANA op Azure (grote exemplaren) | Microsoft Docs
description: Grootte van SAP HANA op Azure (grote exemplaren).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d49191abbba9c189672be4cd8bad4346e9689bf6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675419"
---
# <a name="sizing"></a>Grootte aanpassen

De grootte van HANA grote instanties wijkt niet af van de grootte van HANA in het algemeen. Voor bestaande en geïmplementeerde systemen die u wilt verplaatsen van andere RDBMS naar HANA, biedt SAP een aantal rapporten die worden uitgevoerd op uw bestaande SAP-systemen. Als de data base wordt verplaatst naar HANA, controleren deze rapporten de gegevens en berekenen we de geheugen vereisten voor het HANA-exemplaar. Lees de volgende SAP-opmerkingen voor meer informatie over het uitvoeren van deze rapporten en het verkrijgen van de meest recente patches of versies:

- [SAP-notitie #1793345-grootte voor SAP Suite op HANA](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP-notitie #1872170-Suite op HANA en S/4 HANA-formaat rapport](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP Opmerking #2121330-FAQ: SAP BW op HANA-formaat rapport](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP-notitie #1736976-formaat rapport voor BW op HANA](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP-notitie #2296290: nieuw formaat rapport voor BW op HANA](https://launchpad.support.sap.com/#/notes/2296290)

Voor de implementaties van een groen veld is de snelle grootte van SAP beschikbaar voor het berekenen van de geheugen vereisten van de implementatie van SAP-software bovenop HANA.

De geheugen vereisten voor HANA-toename naarmate het gegevens volume toeneemt. Houd rekening met het huidige geheugen gebruik om u te helpen te voors pellen wat het in de toekomst zal zijn. Op basis van de geheugen vereisten kunt u uw vraag vervolgens toewijzen aan een van de HANA-Sku's voor grote instanties.

**Volgende stappen**
- Raadpleeg de [vereisten voor onboarding](hana-onboarding-requirements.md)