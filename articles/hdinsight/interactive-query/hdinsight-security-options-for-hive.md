---
title: Beveiligings opties voor Hive in azure HDInsight
description: Beveiligings opties voor-onderdelen in standaard-en ESP-clusters.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/02/2020
ms.openlocfilehash: 1189a320d0dc700756c9f7664d0a6303be5dab51
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98940891"
---
# <a name="security-options-for-hive-in-azure-hdinsight"></a>Beveiligings opties voor Hive in azure HDInsight

In dit document worden de aanbevolen beveiligings opties voor Hive in HDInsight beschreven. Deze opties kunnen worden geconfigureerd via Ambari.

![Beveiligings opties voor Hive](./media/hdinsight-security-options-for-hive/security-options-hive.png "Beveiligings opties voor Hive")

## <a name="hiveserver2-authentication"></a>HiveServer2-verificatie

Voor standaard clusters is de aanbevolen instelling voor HiveServer2-verificatie de standaard waarde die geen is. Als u verificatie wilt inschakelen, kunt u het beste een upgrade uitvoeren naar een [ESP](../domain-joined/hdinsight-security-overview.md) -cluster (Enterprise Security package). 

Voor ESP-clusters is [Kerberos](https://web.mit.edu/Kerberos/) -verificatie standaard ingeschakeld. Pluggable verificatie modules (PAM) en aangepaste verificatie schema's worden niet ondersteund.

## <a name="hiveserver2-authorization"></a>HiveServer2-autorisatie

Voor standaard clusters is de standaard instelling geen. [SqlStdAuth (op SQL-standaarden gebaseerde autorisatie)](https://cwiki.apache.org/confluence/display/Hive/SQL+Standard+based+hive+authorization) kan worden ingeschakeld. Autorisatie via [Apache zwerver](https://ranger.apache.org/) wordt niet ondersteund voor standaard clusters. U kunt het beste een upgrade uitvoeren naar een ESP-cluster voor de autorisatie van zwerver. 

Voor ESP-clusters is de autorisatie via zwerver standaard ingeschakeld. 


## <a name="ssl-encryption-for-hiveserver2"></a>SSL-versleuteling voor HiveServer2

Het inschakelen van Hiveserver2 SSL wordt niet aanbevolen voor de standaard-of ESP-clusters. SSL is ingeschakeld op de gateway. [Versleuteling in transit](../domain-joined/encryption-in-transit.md) kan worden ingeschakeld om de communicatie tussen de cluster knooppunten te versleutelen met behulp van [Internet Protocol Security (IPSec)](https://en.wikipedia.org/wiki/IPsec).


## <a name="next-steps"></a>Volgende stappen
* [Overzicht van HiveServer2-verificatie](https://cwiki.apache.org/confluence/display/Hive/Setting+up+HiveServer2#SettingUpHiveServer2-Authentication/SecurityConfiguration)
* [Overzicht van HiveServer2-autorisatie](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Authorization)
* [Validatie van op SQL-standaarden gebaseerde Hive inschakelen](https://community.cloudera.com/t5/Community-Articles/Getting-started-with-SQLStdAuth/ta-p/244263)
* [Apache zwerver met hive](../domain-joined/apache-domain-joined-run-hive.md)
