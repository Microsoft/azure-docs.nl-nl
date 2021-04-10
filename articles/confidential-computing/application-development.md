---
title: Ontwikkel hulpprogramma's van Azure vertrouwelijk computing
description: Hulpprogram ma's en bibliotheken gebruiken om toepassingen te ontwikkelen voor vertrouwelijke computing
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: 0ba6ee92111da66a2118ba4c490b94e5bc9449e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102551382"
---
# <a name="application-development-on-intel-sgx"></a>Toepassings ontwikkeling op Intel SGX 


Voor de infra structuur voor vertrouwelijke computers zijn specifieke hulpprogram ma's en software vereist. Op deze pagina worden de concepten besproken die betrekking hebben op het ontwikkelen van toepassingen voor virtuele Azure-machines die worden uitgevoerd op Intel SGX. Lees, voordat u deze pagina leest, [de introductie van virtuele machines van Intel SGX en enclaves](confidential-computing-enclaves.md). 

Als u de kracht van enclaves en geïsoleerde omgevingen wilt gebruiken, hebt u hulpprogramma's nodig die confidential computing ondersteunen. Er zijn verschillende hulpprogramma's die ondersteuning bieden voor de ontwikkeling van enclavetoepassingen. U kunt bijvoorbeeld deze open-source frameworks gebruiken: 

- [De open enclave Software Development Kit (OE SDK)](#oe-sdk)
- [The Confidential Consortium Framework (CCF)](#ccf)

## <a name="overview"></a>Overzicht

Een toepassing die is gebouwd met enclaves, wordt op twee manieren gepartitioneerd:

1. Een 'niet-vertrouwd' onderdeel (de host)
1. Een 'vertrouwd' onderdeel (de enclave)


![Ontwikkeling van apps](media/application-development/oe-sdk.png)


**De host** is waar overheen uw enclavetoepassing wordt uitgevoerd; het is een niet-vertrouwde omgeving. De enclavecode die op de host is geïmplementeerd, kan niet worden geopend door de host. 

**De enclave** is waar de toepassingscode en de gegevens in de cache/het geheugen worden uitgevoerd. Beveiligde berekeningen moeten in de enclaves worden uitgevoerd om ervoor te zorgen dat geheimen en gevoelige gegevens beveiligd blijven. 


Tijdens het toepassingsontwerp is het belangrijk om te identificeren en te bepalen welk gedeelte van de toepassing moet worden uitgevoerd in de enclaves. De code die u in het vertrouwde onderdeel wilt plaatsen, is geïsoleerd van de rest van uw toepassing. Zodra de enclave is geïnitialiseerd en de code in het geheugen wordt geladen, kan de code niet worden gelezen of gewijzigd vanuit de niet-vertrouwde onderdelen. 

## <a name="open-enclave-software-development-kit-oe-sdk"></a>Open Enclave Software Development Kit (OE SDK) <a id="oe-sdk"></a>

Gebruik een bibliotheek die of framework dat door uw provider wordt ondersteund als u code wilt schrijven die in een enclave wordt uitgevoerd. De [Open Enclave SDK](https://github.com/openenclave/openenclave) (OE SDK) is een open-source SDK die abstractie mogelijk maakt over verschillende apparaten met confidential computing-functionaliteit. 

De OE SDK is gebouwd als één abstractielaag op willekeurige hardware en willekeurige CSP. De OE SDK kan worden gebruikt over virtuele machines met Azure Confidential Computing heen om toepassingen op enclaves te maken en uit te voeren.

## <a name="confidential-consortium-framework-ccf"></a>Vertrouwelijk consortium Framework (CCF) <a id="ccf"></a>

De [CCF](https://github.com/Microsoft/CCF) is een gedistribueerd netwerk van knoop punten, elk met hun eigen enclaves. Met het vertrouwde knooppunt netwerk kunt u een gedistribueerd groot boek uitvoeren. Het groot boek biedt beveiligde, betrouw bare onderdelen voor het protocol dat kan worden gebruikt. 

![CCF-knoop punten](media/application-development/ccf.png)

Dit open-source-framework maakt grote, verfijnde vertrouwelijkheid en consortium governance voor Block chain mogelijk. Met elk knoop punt dat gebruikmaakt van TEEs, kunt u zorgen voor beveiligde consensus en transactie verwerking.


## <a name="next-steps"></a>Volgende stappen 
- [Een vertrouwelijke computing DCsv2-Series virtuele machine implementeren](quick-create-portal.md)
- [Down load en installeer de OE SDK en begin met het ontwikkelen van toepassingen](https://github.com/openenclave/openenclave)