---
title: Ontwikkelhulpprogramma's voor Azure Confidential Computing
description: Hulpprogramma's en bibliotheken gebruiken om toepassingen te ontwikkelen voor confidential computing
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: 571c1a4ce545976db09f46a07d963d5344c02c29
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791009"
---
# <a name="application-development-on-intel-sgx"></a>Toepassingsontwikkeling op Intel SGX 


Confidential Computing-infrastructuur vereist specifieke hulpprogramma's en software. Deze pagina bespreekt specifiek concepten met betrekking tot de ontwikkeling van toepassingen voor virtuele azure-machines met confidential computing die worden uitgevoerd op Intel SGX. Lees voordat u deze pagina leest [de introductie van virtuele Intel SGX-machines en enclaves](confidential-computing-enclaves.md). 

Als u de kracht van enclaves en geïsoleerde omgevingen wilt gebruiken, hebt u hulpprogramma's nodig die confidential computing ondersteunen. Er zijn verschillende hulpprogramma's die ondersteuning bieden voor de ontwikkeling van enclavetoepassingen. U kunt bijvoorbeeld deze open-source frameworks gebruiken: 

- [De Open Enclave Software Development Kit (OE SDK)](#oe-sdk)
- [De EGo Software Development Kit](#ego)
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

## <a name="ego-software-development-kit"></a>EGo Software Development Kit <a id="ego"></a>

[EGo](https://ego.dev/) is een opensource-SDK waarmee u toepassingen kunt uitvoeren die zijn geschreven in de programmeertaal Go in enclaves. EGo bouwt voort op de OE SDK en wordt geleverd met een in-enclave Go-bibliotheek voor attestation en verzegeling. Veel bestaande Go-toepassingen worden zonder wijzigingen uitgevoerd op EGo.  

## <a name="confidential-consortium-framework-ccf"></a>Confidential Consortium Framework (CCF) <a id="ccf"></a>

De [CCF](https://github.com/Microsoft/CCF) is een gedistribueerd netwerk van knooppunten die elk hun eigen enclaves uitvoeren. Met het vertrouwde knooppuntnetwerk kunt u een gedistribueerd grootboek uitvoeren. Het grootboek biedt veilige, betrouwbare onderdelen die het protocol kan gebruiken. 

![CCF-knooppunten](media/application-development/ccf.png)

Dit opensource-framework maakt een hoge mate van vertrouwelijkheid en consortiumgovernance voor blockchain mogelijk. Met elk knooppunt dat TEE's gebruikt, kunt u zorgen voor een veilige consensus en transactieverwerking.


## <a name="next-steps"></a>Volgende stappen 
- [Een confidential computing-DCsv2-Series virtuele machine implementeren](quick-create-portal.md)
- [De OE SDK downloaden en installeren en beginnen met het ontwikkelen van toepassingen](https://github.com/openenclave/openenclave)
