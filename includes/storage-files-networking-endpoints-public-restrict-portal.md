---
title: bestand opnemen
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 01/25/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3c76988557bfa338bcfb606f568036422a536c1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98798575"
---
Ga naar het opslagaccount waarvoor u het openbare eindpunt wilt beperken tot bepaalde virtuele netwerken. Selecteer in de inhouds opgave voor het opslag account **netwerk**. 

Selecteer bovenaan de pagina het keuzerondje **Geselecteerde netwerken**. Hierdoor komen er een aantal instellingen beschikbaar voor het beperken van de toegang tot het openbare eindpunt. Klik op **+ Bestaand virtueel netwerk toevoegen** om het specifieke virtuele netwerk te selecteren dat toegang mag hebben tot het opslagaccount via het openbare eindpunt. Hiervoor moet u een virtueel netwerk en een subnet selecteren voor dat virtuele netwerk. 

Selecteer **Vertrouwde Microsoft-services toegang geven tot dit serviceaccount** om vertrouwde Microsoft-services, zoals Azure File Sync, toegang te geven tot het opslagaccount.

[![Scherm afbeelding van de netwerk Blade met een specifiek virtueel netwerk dat toegang heeft tot het opslag account via het open bare eind punt](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png)](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png#lightbox)