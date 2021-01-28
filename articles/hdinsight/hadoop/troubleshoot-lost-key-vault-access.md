---
title: Azure HDInsight-clusters met schijf versleuteling gaan verloren Key Vault toegang
description: Stappen voor probleem oplossing en mogelijke oplossingen voor Key Vault toegangs problemen tijdens interactie met Azure HDInsight-clusters.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.openlocfilehash: ce2929ca84746de1ab8b51882f3004c3699f17ca
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943113"
---
# <a name="scenario-azure-hdinsight-clusters-with-disk-encryption-lose-key-vault-access"></a>Scenario: Azure HDInsight-clusters met schijf versleuteling, verlies Key Vault toegang

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

De RHC-waarschuwing (Resource Health Center) `The HDInsight cluster is unable to access the key for BYOK encryption at rest` wordt weer gegeven voor Bring your own Key-clusters (BYOK) waarin de cluster knooppunten de toegang tot klanten Key Vault (kV) hebben verloren. Vergelijk bare waarschuwingen kunnen ook worden weer gegeven op Apache Ambari-gebruikers interface.

## <a name="cause"></a>Oorzaak

De waarschuwing zorgt ervoor dat KV toegankelijk is vanaf de cluster knooppunten, waardoor de netwerk verbinding, de status van KV en het toegangs beleid voor de door de gebruiker toegewezen beheerde identiteit worden gegarandeerd. Deze waarschuwing is slechts een waarschuwing dat het afsluiten van de Broker op het volgende knoop punt opnieuw wordt opgestart. het cluster blijft functioneren totdat knoop punten opnieuw worden opgestart.

Navigeer naar Apache Ambari UI voor meer informatie over de waarschuwing van **schijf versleuteling Key Vault status**. Deze waarschuwing bevat details over de reden voor de verificatie fout.

## <a name="resolution"></a>Oplossing

### <a name="kvaad-outage"></a>KV/AAD-onderbreking

Bekijk [Azure Key Vault Beschik baarheid en redundantie](../../key-vault/general/disaster-recovery-guidance.md) en de pagina status van Azure voor meer informatie https://status.azure.com/

### <a name="kv-accidental-deletion"></a>Onbedoeld verwijderen van KV

* Herstellen van verwijderde sleutel op KV naar automatisch herstellen. Zie [verwijderde sleutel herstellen](/rest/api/keyvault/recoverdeletedkey)voor meer informatie.
* Neem contact op met het team van KV om onbedoelde verwijderingen te herstellen.

### <a name="kv-access-policy-changed"></a>Toegangs beleid voor KV is gewijzigd

Herstel het toegangs beleid voor de door de gebruiker toegewezen beheerde identiteit die is toegewezen aan het HDI-cluster voor toegang tot de KV.

### <a name="key-permitted-operations"></a>Sleutel toegestane bewerkingen

Voor elke sleutel in KV kunt u de set toegestane bewerkingen kiezen. Zorg ervoor dat u over de functie voor het inpakken en uitpakken hebt ingeschakeld voor de BYOK-sleutel

### <a name="expired-key"></a>Verlopen sleutel

Als het verloop is verstreken en de sleutel niet is geroteerd, herstelt u de sleutel van de back-uphsm of neemt u contact op met het KV-team om de verval datum te wissen.

### <a name="kv-firewall-blocking-access"></a>KV firewall blokkeert toegang

Los de KV Firewall-instellingen op om BYOK-cluster knooppunten toegang te geven tot de KV.

### <a name="nsg-rules-on-virtual-network-blocking-access"></a>NSG-regels voor het blok keren van toegang tot virtueel netwerk

Controleer de NSG-regels die zijn gekoppeld aan het virtuele netwerk dat aan het cluster is gekoppeld.

## <a name="mitigation-and-prevention-steps"></a>Stappen voor het beperken en voor komen van problemen

### <a name="kv-accidental-deletion"></a>Onbedoeld verwijderen van KV

* Configureer Key Vault met [resource Lock set](../../azure-resource-manager/management/lock-resources.md).
* Maak een back-up van sleutels in hun hardware Security-module.

### <a name="key-deletion"></a>Sleutel verwijderen

Het cluster moet worden verwijderd voordat de sleutel wordt verwijderd.

### <a name="kv-access-policy-changed"></a>Toegangs beleid voor KV is gewijzigd

Regel matig toegangs beleid controleren en testen.

### <a name="expired-key"></a>Verlopen sleutel

* Maak een back-up van sleutels naar uw HSM.
* Gebruik een sleutel zonder verloopset.
* Als het verloop moet worden ingesteld, roteert u de sleutels vóór de verval datum.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](../../azure-portal/supportability/how-to-create-azure-support-request.md)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).