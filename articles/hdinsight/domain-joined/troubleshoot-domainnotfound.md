---
title: Het maken van een cluster mislukt met DomainNotFound-fout in azure HDInsight
description: Probleemoplossings stappen en mogelijke oplossingen voor problemen bij interactie met Azure HDInsight-clusters
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: 54096e6d1741a7be9f50d4b4f7f0fcabbe880588
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943352"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>Scenario: het maken van een cluster mislukt met DomainNotFound-fout in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Het maken van een HDI Secure (Enterprise Security Package)-cluster mislukt met `DomainNotFound` fout bericht.

## <a name="cause"></a>Oorzaak

Onjuiste DNS-instellingen.

## <a name="resolution"></a>Oplossing

Wanneer de aan het domein gekoppelde clusters zijn geïmplementeerd, maakt HDI een interne gebruikers naam en wacht woord in AAD DS (voor elk cluster) en worden alle cluster knooppunten toegevoegd aan dit domein. De toevoeging aan het domein is voltooid met behulp van samba-hulpprogram ma's. Zorg ervoor dat aan de volgende vereisten wordt voldaan:

* De domein naam moet worden omgezet via DNS.
* Het IP-adres van de domein controllers moet worden ingesteld in de DNS-instellingen voor het virtuele netwerk waarin het cluster wordt geïmplementeerd.
* Als het virtuele netwerk is gekoppeld aan het virtuele netwerk van AAD DS, moet het hand matig worden uitgevoerd.
* Als u DNS-doorstuur servers gebruikt, moet de domein naam correct worden omgezet in het virtuele netwerk.
* Het beveiligings beleid (Nsg's) mag het lidmaatschap van het domein niet blok keren.

### <a name="additional-debugging-steps"></a>Aanvullende stappen voor fout opsporing

* Een Windows-VM implementeren in hetzelfde subnet, domein lid worden van de computer met een gebruikers naam en wacht woord (dit kan worden gedaan via de gebruikers interface van het configuratie scherm) of

* Een Ubuntu-VM implementeren in hetzelfde subnet en domein lid worden van de computer
  * SSH in de computer
  * sudo su
  * Voer het script uit met de gebruikers naam en het wacht woord
  * Het script wordt gepingd, de vereiste configuratie bestanden en vervolgens het domein gemaakt. Als de service slaagt, zijn uw DNS-instellingen goed.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]