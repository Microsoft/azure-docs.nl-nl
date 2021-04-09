---
title: Meerdere HDInsight-clusters & een Azure Data Lake Storage account
description: Meer informatie over het gebruik van meer dan één HDInsight-cluster met één Data Lake Storage account
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: 6e220592f53103320c3bdb586fcbd0106219bfed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98939536"
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-storage-account"></a>Meerdere HDInsight-clusters gebruiken met een Azure Data Lake Storage-account

Vanaf HDInsight versie 3,5 kunt u HDInsight-clusters maken met Azure Data Lake Storage accounts als standaard bestandssysteem.
Data Lake Storage ondersteunt onbeperkte opslag, waardoor het niet alleen ideaal is voor het hosten van grote hoeveel heden gegevens. maar ook voor het hosten van meerdere HDInsight-clusters die één Data Lake Storage account delen. Zie [Quick Start: clusters instellen in HDInsight](./hdinsight-hadoop-provision-linux-clusters.md)voor instructies over het maken van een HDInsight-cluster met data Lake Storage als opslag.

In dit artikel worden aanbevelingen gedaan voor de Data Lake Storage beheerder voor het instellen van een enkele en gedeelde Data Lake Storage account die kan worden gebruikt in meerdere **actieve** HDInsight-clusters. Deze aanbevelingen zijn van toepassing op het hosten van meerdere beveiligings-en niet-beveiligde Apache Hadoop clusters op een gedeeld Data Lake Storage-account.

## <a name="data-lake-storage-file-and-folder-level-acls"></a>Acl's voor bestanden en mapniveau Data Lake Storage

In de rest van dit artikel wordt ervan uitgegaan dat u een goede kennis hebt van Acl's op het niveau van bestanden en mappen op het Azure Data Lake Storage, dat in detail wordt beschreven in [Access Control in azure data Lake Storage](../data-lake-store/data-lake-store-access-control.md).

## <a name="data-lake-storage-setup-for-multiple-hdinsight-clusters"></a>Data Lake Storage-installatie voor meerdere HDInsight-clusters

Laat ons een maphiërarchie op twee niveaus nemen om de aanbevelingen voor het gebruik van meerdere HDInsight-clusters met een Data Lake Storage-account te verklaren. Stel dat u een Data Lake Storage-account hebt met de mappen structuur **/clusters/Finance**. Met deze structuur kunnen alle door de financiële organisatie vereiste clusters gebruikmaken van/clusters/Finance als opslag locatie. Als een andere organisatie in de toekomst een HDInsight-cluster wil maken met behulp van hetzelfde Data Lake Storage account, kunnen ze/clusters/marketing. maken We gebruiken nu alleen **/clusters/Finance**.

Om ervoor te zorgen dat deze mapstructuur effectief kan worden gebruikt door HDInsight-clusters, moet de Data Lake Storage-beheerder de juiste machtigingen toewijzen, zoals beschreven in de tabel. De machtigingen die in de tabel worden weer gegeven, komen overeen met Access-Acl's en niet standaard-Acl's.

|Map  |Machtigingen  |Gebruiker die eigenaar is  |Groep die eigenaar is  | Benoemde gebruiker | Benoemde gebruikers machtigingen | Benoemde groep | Benoemde groeps machtigingen |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x--x  |beheerder |beheerder  |Service-principal |--x  |FINGRP   |r-x         |
|/clusters | rwxr-x--x |beheerder |beheerder |Service-principal |--x  |FINGRP |r-x         |
|/clusters/finance | rwxr-x--t |beheerder |FINGRP  |Service-principal |LSU  |-  |-     |

In de tabel,

- **Administrator** is de maker en beheerder van het data Lake Storage-account.
- De **Service-Principal** is de service-principal van de Azure Active Directory (Aad) die aan het account is gekoppeld.
- **FINGRP** is een gebruikers groep die in Aad is gemaakt en die gebruikers van de organisatie Financiën bevat.

Zie [een Aad-toepassing maken](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)voor instructies over het maken van een Aad-toepassing (die ook een Service-Principal maakt). Zie [groepen beheren in azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)voor instructies over het maken van een gebruikers groep in Aad.

Enkele belang rijke punten om rekening mee te houden.

- De mapstructuur van het twee niveau (**/clusters/Finance/**) moet worden gemaakt en ingericht met de juiste machtigingen van de beheerder van de data Lake Storage **voordat** het opslag account voor clusters wordt gebruikt. Deze structuur wordt niet automatisch gemaakt tijdens het maken van clusters.
- In het bovenstaande voor beeld wordt aanbevolen de groep die eigenaar is van **/clusters/Finance** in te stellen als **FINGRP** en om **r-x** toegang te geven tot de volledige maphiërarchie vanaf de hoofdmap. Dit zorgt ervoor dat de leden van FINGRP kunnen navigeren door de mappen structuur vanaf de hoofdmap.
- Als verschillende AAD-service-principals clusters kunnen maken onder **/clusters/Finance**, zorgt de plak-bit (wanneer ingesteld in de map **Financiën** ) ervoor dat mappen die zijn gemaakt met een Service-Principal, niet kunnen worden verwijderd door de andere.
- Zodra de mappen structuur en de machtigingen zijn geïmplementeerd, maakt het proces voor het maken van HDInsight-clusters een specifieke opslag locatie voor het cluster onder **/clusters/Finance/**. Zo kan de opslag voor een cluster met de naam fincluster01 **/clusters/Finance/fincluster01** zijn. Het eigendom en de machtigingen voor de mappen die zijn gemaakt met het HDInsight-cluster, worden hier weer gegeven in de tabel.

    |Map  |Machtigingen  |Gebruiker die eigenaar is  |Groep die eigenaar is  | Benoemde gebruiker | Benoemde gebruikers machtigingen | Benoemde groep | Benoemde groeps machtigingen |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/clusters/finanace/ fincluster01 | rwxr-x---  |Service-principal |FINGRP  |- |-  |-   |-  |

## <a name="recommendations-for-job-input-and-output-data"></a>Aanbevelingen voor de invoer-en uitvoer gegevens van de taak

We raden u aan om gegevens in te voeren voor een taak en de uitvoer van een taak worden opgeslagen in een map buiten **/clusters**. Dit zorgt ervoor dat zelfs als de cluster-specifieke map wordt verwijderd om een aantal opslag ruimte vrij te maken, de invoer en uitvoer van de taak nog steeds beschikbaar zijn voor toekomstig gebruik. Zorg er in dat geval voor dat de maphiërarchie voor het opslaan van de taak invoer en uitvoer het juiste toegangs niveau voor de Service-Principal toestaat.

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>Limiet voor clusters die één opslag account delen

De limiet voor het aantal clusters dat één Data Lake Storage account kan delen, is afhankelijk van de werk belasting die wordt uitgevoerd op die clusters. Als er te veel clusters of zeer zware werk belastingen zijn op de clusters die een opslag account delen, kan dit ertoe leiden dat het opslag account niet kan worden getrapt.

## <a name="support-for-default-acls"></a>Ondersteuning voor Default-ACLs

Bij het maken van een service-principal met de naam gebruikers toegang (zoals weer gegeven in de tabel hierboven), raden we aan de benoemde gebruiker **niet** toe te voegen met een standaard-ACL. Het inrichten van de naam gebruikers toegang met behulp van standaard-Acl's resulteert in de toewijzing van 770 machtigingen voor eigenaar-gebruiker, eigendom en anderen. Hoewel deze standaard waarde van 770 geen machtigingen van eigenaar-gebruiker (7) of eigendoms groep (7) accepteert, worden alle machtigingen voor anderen (0) verwijderd. Dit resulteert in een bekend probleem met een van de specifieke gebruiks gevallen die uitvoerig worden beschreven in de sectie [bekende problemen en tijdelijke oplossingen](#known-issues-and-workarounds) .

## <a name="known-issues-and-workarounds"></a>Bekende problemen en tijdelijke oplossingen

In deze sectie vindt u de bekende problemen met het gebruik van HDInsight met Data Lake Storage en de tijdelijke oplossingen.

### <a name="publicly-visible-localized-apache-hadoop-yarn-resources"></a>Openbaar zicht bare gelokaliseerde Apache Hadoop GARENs

Wanneer een nieuw Azure Data Lake Storage-account wordt gemaakt, wordt de hoofdmap automatisch ingericht met Access-ACL-machtiging bits ingesteld op 770. De gebruiker die eigenaar is van de hoofdmap is ingesteld op de gebruiker die het account heeft gemaakt (de Data Lake Storage beheerder) en de groep die eigenaar is, wordt ingesteld op de primaire groep van de gebruiker die het account heeft gemaakt. Er wordt geen toegang voor ' anderen ' gegeven.

Deze instellingen zijn bekend als invloed op één specifieke HDInsight-use-case vastgelegd in [garen 247](https://hwxmonarch.atlassian.net/browse/YARN-247). Taak inzendingen kunnen mislukken met een fout bericht van de volgende strekking:

```output
Resource XXXX is not publicly accessible and as such cannot be part of the public cache.
```

Zoals vermeld in de GARENs die eerder zijn gekoppeld, terwijl open bare bronnen worden gelokaliseerd, valideert de localizer dat alle aangevraagde bronnen inderdaad openbaar zijn door hun machtigingen op het externe bestands systeem te controleren. LocalResource die niet aan deze voor waarde voldoen, worden voor lokalisatie afgewezen. De controle op machtigingen, bevat Lees toegang tot het bestand voor "anderen". Dit scenario werkt niet out-of-the-box bij het hosten van HDInsight-clusters op Azure Data Lake, omdat Azure Data Lake geen toegang meer heeft tot ' anderen ' op het niveau van de hoofdmap.

#### <a name="workaround"></a>Tijdelijke oplossing

Stel de machtigingen lezen-uitvoeren voor **anderen** in via de hiërarchie, bijvoorbeeld op **/** **/clusters** en **/clusters/Finance** , zoals weer gegeven in de bovenstaande tabel.

## <a name="see-also"></a>Zie ook

- [Snelstartgids: Clusters instellen in HDInsight](./hdinsight-hadoop-provision-linux-clusters.md)
- [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](hdinsight-hadoop-use-data-lake-storage-gen2.md)