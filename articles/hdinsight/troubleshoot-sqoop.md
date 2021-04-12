---
title: De Sqoop-opdracht voor importeren/exporteren mislukt voor sommige gebruikers in ESP-clusters-Azure HDInsight
description: "De opdracht Apache Sqoop import/export mislukt met het importeren is mislukt: Java. io. IOException: het eigendom van de tijdelijke directory/user/yourusername/.staging is niet zoals verwacht ' fout voor sommige gebruikers in azure HDInsight ESP-cluster"
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/01/2021
ms.openlocfilehash: 79aae099e30bae7d5201fae3384f7b89b6c30bcf
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387329"
---
# <a name="scenario-sqoop-importexport-command-fails-for-usernames-greater-than-20-characters-in-azure-hdinsight-esp-clusters"></a>Scenario: de opdracht Sqoop importeren/exporteren mislukt voor gebruikers namen van meer dan 20 tekens in azure HDInsight ESP-clusters

In dit artikel worden een bekend probleem en tijdelijke oplossing beschreven voor het gebruik van Azure HDInsight ESP (Enter prise Security Pack) ingeschakelde clusters met behulp van ADLS Gen2-opslag account (ABFS).

## <a name="issue"></a>Probleem

Bij het uitvoeren van de sqoop-opdracht voor importeren/exporteren mislukt de fout bij sommige gebruikers:

```
ERROR tool.ImportTool: Import failed: java.io.IOException:
The ownership on the staging directory /user/yourlongdomainuserna/.staging is not as expected. 
It is owned by yourlongdomainusername.
The directory must be owned by the submitter yourlongdomainuserna or yourlongdomainuserna@AADDS.CONTOSO.COM
```

In het bovenstaande voor beeld `/user/yourlongdomainuserna/.staging` wordt de naam van het afgekapte 20 tekens voor de gebruikers naam weer gegeven `yourlongdomainusername` .

## <a name="cause"></a>Oorzaak

De gebruikers naam mag Maxi maal 20 tekens lang zijn. 

Raadpleeg [hoe objecten en referenties worden gesynchroniseerd in een Azure Active Directory Domain Services beheerd domein](../active-directory-domain-services/synchronization.md) voor meer informatie.

## <a name="workaround"></a>Tijdelijke oplossing

Gebruik een gebruikers naam die kleiner is dan of gelijk is aan 20 tekens.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]
