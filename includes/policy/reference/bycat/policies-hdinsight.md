---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 88945738b33cb6d1d7760eec1015a006132d3c3f
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220380"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure HDInsight-clusters moeten door de klant beheerde sleutels gebruiken om gegevens in rust te versleutelen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F64d314f6-6062-4780-a861-c23e8951bee5) |Gebruik door de klant beheerde sleutels (CMK) voor het beheren van de versleuteling op rest van uw Azure HDInsight-clusters. Standaard worden de klantgegevens versleuteld met door service beheerde sleutels, maar CMK‘s zijn doorgaans vereist om te voldoen aan de normen voor naleving van regelgeving. CMK‘s zorgen ervoor dat de gegevens worden versleuteld met een Azure Key Vault-sleutel die u hebt gemaakt en waarvan u eigenaar bent. U hebt de volledige controle en verantwoordelijkheid voor de levenscyclus van de sleutel, met inbegrip van rotatie en beheer. Zie voor meer informatie over CMK-versleuteling: [https://aka.ms/hdi.cmk](https://aka.ms/hdi.cmk).  |Controleren, Weigeren, Uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_CMK_Audit.json) |
|[Azure HDInsight-clusters moeten versleuteling op de host gebruiken om gegevens in rust te versleutelen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fd32ebd-e4c3-4e13-a54a-d7422d4d95f6) |Door versleuteling in te scha kelen op host kunt u uw gegevens beschermen en beveiligen om te voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie. Wanneer u versleuteling inschakelt op de host, worden gegevens die op de VM-host zijn opgeslagen, versleuteld op rest en stromen die zijn versleuteld naar de opslag service. |Controleren, Weigeren, Uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionAtHost_Audit.json) |
|[Azure HDInsight-clusters moeten versleuteling in transit gebruiken om de communicatie tussen Azure HDInsight-cluster knooppunten te versleutelen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9da03a1-f3c3-412a-9709-947156872263) |Er kan met gegevens worden geknoeid tijdens de verzen ding tussen Azure HDInsight-cluster knooppunten. Het inschakelen van versleuteling in transit lost problemen op tijdens deze verzen ding. |Controleren, Weigeren, Uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionInTransit_Audit.json) |
