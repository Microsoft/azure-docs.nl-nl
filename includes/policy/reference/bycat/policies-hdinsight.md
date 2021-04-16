---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 60278160646de1b80d224cabc6d781872a5624d9
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498717"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure HDInsight clusters moeten worden geïnjecteerd in een virtueel netwerk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0ab5b05-1c98-40f7-bb9e-dc568e41b501) |Het injecteren Azure HDInsight clusters in een virtueel netwerk ontgrendelt geavanceerde HDInsight-netwerk- en beveiligingsfuncties en biedt u controle over uw netwerkbeveiligingsconfiguratie. |Controleren, uitgeschakeld, weigeren |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_VNETInjection_Audit.json) |
|[Azure HDInsight clusters moeten door de klant beheerde sleutels gebruiken om data-at-rest te versleutelen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F64d314f6-6062-4780-a861-c23e8951bee5) |Gebruik door de klant beheerde sleutels om de versleuteling in de rest van uw Azure HDInsight beheren. Standaard worden klantgegevens versleuteld met door de service beheerde sleutels, maar door de klant beheerde sleutels zijn doorgaans vereist om te voldoen aan wettelijke nalevingsstandaarden. Met door de klant beheerde sleutels kunnen de gegevens worden versleuteld met een Azure Key Vault sleutel die door u is gemaakt en eigendom is. U hebt de volledige controle en verantwoordelijkheid voor de levenscyclus van de sleutel, met inbegrip van rotatie en beheer. Meer informatie op [https://aka.ms/hdi.cmk](https://aka.ms/hdi.cmk) . |Controleren, Weigeren, Uitgeschakeld |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_CMK_Audit.json) |
|[Azure HDInsight clusters moeten versleuteling op de host gebruiken om data-at-rest te versleutelen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fd32ebd-e4c3-4e13-a54a-d7422d4d95f6) |Door versleuteling op de host in te stellen, kunt u uw gegevens beschermen en beschermen om te voldoen aan de beveiligings- en nalevingsverplichtingen van uw organisatie. Wanneer u versleuteling op de host inschakelen, worden gegevens die zijn opgeslagen op de VM-host versleuteld at rest en stromen versleuteld naar de Storage-service. |Controleren, Weigeren, Uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionAtHost_Audit.json) |
|[Azure HDInsight clusters moeten versleuteling 'in transit' gebruiken om communicatie tussen Azure HDInsight clusterknooppunten te versleutelen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9da03a1-f3c3-412a-9709-947156872263) |Er kan met gegevens worden geknoeid tijdens de overdracht tussen Azure HDInsight clusterknooppunten. Het inschakelen van versleuteling tijdens overdracht lost problemen van misbruik en manipulatie tijdens deze overdracht op. |Controleren, Weigeren, Uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionInTransit_Audit.json) |
