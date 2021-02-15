---
title: load balancer gegevens ophalen met behulp van de Azure-Instance Metadata Service
titleSuffix: Azure Load Balancer
description: Ga aan de slag met het gebruik van Azure Instance Metadata Service om load balancer informatie op te halen.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: dcc9f71404e5a7c6509e4a8e821d66831ba02382
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417826"
---
# <a name="retrieve-load-balancer-information-by-using-the-azure-instance-metadata-service"></a>load balancer gegevens ophalen met behulp van de Azure-Instance Metadata Service

IMDS (Azure Instance Metadata Service) bevat informatie over actieve instanties van virtuele machines. De service is een REST API die beschikbaar is via een bekend, niet-routeerbaar IP-adres (169.254.169.254). 

Wanneer u exemplaren van virtuele machines of virtuele machines hebt ingesteld achter een Azure-Standard Load Balancer, gebruikt u de IMDS om meta gegevens op te halen die betrekking hebben op de load balancer en de instanties.

De meta gegevens bevatten de volgende informatie voor de virtuele machines of virtuele-machine schaal sets:

* Standaard SKU openbaar IP-adres.
* Binnenkomende regel configuraties van de load balancer van elk particulier IP-adres van de netwerk interface.
* Uitgaande regel configuraties van de load balancer van elk particulier IP-adres van de netwerk interface.

## <a name="access-the-load-balancer-metadata-using-the-imds"></a>Toegang tot de load balancer meta gegevens met behulp van de IMDS

Zie [de Azure-instance metadata service gebruiken om toegang te krijgen tot Load Balancer informatie](howto-load-balancer-imds.md) voor meer informatie over het openen van de Load Balancer meta gegevens. om toegang te krijgen tot load balancer informatie.

## <a name="troubleshoot-common-error-codes"></a>Veelvoorkomende fout codes oplossen

Zie voor meer informatie over veelvoorkomende fout codes en de risico methoden [veelvoorkomende fout codes oplossen wanneer u IMDS gebruikt](troubleshoot-load-balancer-imds.md). 

## <a name="support"></a>Ondersteuning

Als u na meerdere pogingen geen meta gegevens reactie kunt ophalen, maakt u een ondersteunings probleem in de Azure Portal.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [Azure instance metadata service](/virtual-machines/windows/instance-metadata-service)

[Een standaard load balancer implementeren](quickstart-load-balancer-standard-public-portal.md)

