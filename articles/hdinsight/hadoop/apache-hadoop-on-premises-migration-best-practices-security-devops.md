---
title: 'Beveiliging: migratie van on-premises Apache Hadoop naar Azure HDInsight'
description: Leer de best practices voor beveiliging en DevOps voor het migreren van on-premises Hadoop-clusters naar Azure HDInsight.
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/19/2019
ms.openlocfilehash: fa6a4a8686fe5a33a6f240a8e972a687e872732a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98939743"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---security-and-devops-best-practices"></a>On-premises Apache Hadoop clusters migreren naar Azure HDInsight-beveiligings-en DevOps best practices

Dit artikel bevat aanbevelingen voor beveiliging en DevOps in azure HDInsight-systemen. Het is onderdeel van een serie die aanbevolen procedures biedt voor het migreren van on-premises Apache Hadoop systemen naar Azure HDInsight.

## <a name="secure-and-govern-cluster-with-enterprise-security-package"></a>Cluster beveiligen en beheren met Enterprise Security Package

De Enterprise Security Package (ESP) ondersteunt op Active Directory gebaseerde verificatie, ondersteuning voor meerdere gebruikers en toegangs beheer op basis van rollen. Als de ESP-optie is gekozen, wordt HDInsight-cluster gekoppeld aan het Active Directory domein en kan de ondernemings beheerder op rollen gebaseerd toegangs beheer (RBAC) configureren voor Apache Hive beveiliging met behulp van Apache zwerver. De beheerder kan ook de gegevens toegang door werk nemers en alle wijzigingen die worden uitgevoerd voor toegangs beheer beleid controleren.

ESP is beschikbaar op de volgende cluster typen: Apache Hadoop, Apache Spark, Apache HBase, Apache Kafka en Interactive query (Hive LLAP).

Gebruik de volgende stappen om het HDInsight-cluster dat is gekoppeld aan het domein te implementeren:

- Implementeer Azure Active Directory (AAD) door de domein naam door te geven.
- Azure Active Directory Domain Services implementeren (AAD DS).
- Maak de vereiste Virtual Network en het subnet.
- Implementeer een virtuele machine in de Virtual Network om AAD DS te beheren.
- Voeg de virtuele machine toe aan het domein.
- Installeer AD-en DNS-hulpprogram ma's.
- Laat de AAD DS-beheerder een organisatie-eenheid (OE) maken.
- LDAPS inschakelen voor AAD DS.
- Maak een service account in Azure Active Directory met gedelegeerde Lees & machtiging voor het schrijven van beheerders voor de organisatie-eenheid, zodat het kan. Dit service account kan vervolgens worden toegevoegd aan het domein en computer-principals in de organisatie-eenheid worden geplaatst. Het kan ook service-principals maken binnen de OE die u opgeeft tijdens het maken van het cluster.

    > [!Note]
    > Het service account hoeft geen AD-domein beheerders account te zijn.

- Implementeer het HDInsight ESP-cluster door de volgende para meters in te stellen:

    |Parameter |Beschrijving |
    |---|---|
    |Domeinnaam|De domein naam die is gekoppeld aan Azure AD DS.|
    |Domein gebruikers naam|Het service account in het door Azure AD DS DC beheerde domein dat u in de vorige sectie hebt gemaakt, bijvoorbeeld: `hdiadmin@contoso.onmicrosoft.com` . Deze domein gebruiker is de beheerder van dit HDInsight-cluster.|
    |Domein wachtwoord|Het wacht woord van het service account.|
    |Organisatie-eenheid|De DN-naam van de organisatie-eenheid die u wilt gebruiken met het HDInsight-cluster, bijvoorbeeld: `OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com` . Als deze OE niet bestaat, probeert het HDInsight-cluster de OE te maken met behulp van de bevoegdheden van het service account.|
    |URL VAN LDAPS|bijvoorbeeld `ldaps://contoso.onmicrosoft.com:636` .|
    |Gebruikers groep openen|De beveiligings groepen waarvan u de gebruikers wilt synchroniseren met het cluster, bijvoorbeeld: `HiveUsers` . Als u meerdere gebruikers groepen wilt opgeven, scheidt u deze met punt komma '; '. De groep (en) moeten aanwezig zijn in de map voordat u het ESP-cluster maakt.|

Raadpleeg voor meer informatie de volgende artikelen:

- [Een inleiding tot Apache Hadoop beveiliging met HDInsight-clusters die zijn toegevoegd aan een domein](../domain-joined/hdinsight-security-overview.md)
- [Apache Hadoop clusters die zijn toegevoegd aan het Azure-domein plannen in HDInsight](../domain-joined/apache-domain-joined-architecture.md)
- [Een HDInsight-cluster dat is gekoppeld aan een domein configureren met behulp van Azure Active Directory Domain Services](../domain-joined/apache-domain-joined-configure-using-azure-adds.md)
- [Azure Active Directory-gebruikers synchroniseren met een HDInsight-cluster](../hdinsight-sync-aad-users-to-cluster.md)
- [Apache Hive-beleid configureren in HDInsight die lid is van een domein](../domain-joined/apache-domain-joined-run-hive.md)
- [Apache Oozie uitvoeren in HDInsight Hadoop-clusters die zijn toegevoegd aan een domein](../domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)

## <a name="implement-end-to-end-enterprise-security"></a>End-to-end Enter prise Security implementeren

End-to-end Bedrijfs beveiliging kan worden bereikt met de volgende besturings elementen:

**Persoonlijke en beveiligde gegevens pijplijn (beveiliging op perimeter niveau)**
    - Beveiliging op perimeter niveau kan worden bereikt via virtuele netwerken van Azure, netwerk beveiligings groepen en Gateway Service.

**Verificatie en autorisatie voor gegevens toegang**
    - Maak een HDInsight-cluster dat is gekoppeld aan het domein met behulp van Azure Active Directory Domain Services. (Enterprise Security Package).
    - Gebruik Ambari om op rollen gebaseerde toegang tot cluster bronnen voor AD-gebruikers te bieden.
    - Gebruik Apache zwerver om toegangs beheer beleid in te stellen voor Hive op het niveau van tabel/kolom/rij.
    - SSH-toegang tot het cluster kan alleen worden beperkt tot de beheerder.

**Controle**
    - Alle toegang tot de bronnen en gegevens van het HDInsight-cluster weer geven en rapporteren.
    - Alle wijzigingen in het toegangs beheer beleid weer geven en rapporteren.

**Versleuteling**
    - Transparante Server-Side versleuteling met door micro soft beheerde sleutels of door de klant beheerde sleutels.
    - In transit versleuteling met Client-Side versleuteling, HTTPS en TLS.

Raadpleeg voor meer informatie de volgende artikelen:

- [Overzicht van Azure Virtual Networks](../../virtual-network/virtual-networks-overview.md)
- [Overzicht van Azure-netwerk beveiligings groepen](../../virtual-network/network-security-groups-overview.md)
- [Peering van virtuele netwerken in Azure](../../virtual-network/virtual-network-peering-overview.md)
- [Azure Storage-beveiligingshandleiding](../../storage/blobs/security-recommendations.md)
- [Versleuteling van Azure Storage-service bij rest](../../storage/common/storage-service-encryption.md)

## <a name="use-monitoring--alerting"></a>Bewakings & waarschuwingen gebruiken

Zie voor meer informatie het artikel:

[Overzicht van Azure Monitor](../../azure-monitor/overview.md)

## <a name="upgrade-clusters"></a>Upgrade clusters

Voer regel matig een upgrade uit naar de nieuwste HDInsight-versie om te profiteren van de nieuwste functies. De volgende stappen kunnen worden gebruikt om het cluster bij te werken naar de meest recente versie:

1. Maak een nieuw HDInsight-TESTLAB-cluster met de meest recente versie van HDInsight.
1. Test op het nieuwe cluster om te controleren of de taken en werk belastingen naar verwachting werken.
1. Wijzig taken of toepassingen of werk belastingen zoals vereist.
1. Maak een back-up van tijdelijke gegevens die lokaal zijn opgeslagen op de cluster knooppunten.
1. Verwijder het bestaande cluster.
1. Maak een cluster van de meest recente HDInsight-versie in hetzelfde subnet van het virtuele netwerk met dezelfde standaard gegevens en hetzelfde meta archief als het vorige cluster.
1. Importeer alle tijdelijke gegevens waarvan een back-up is gemaakt.
1. Start taken/Ga door met de verwerking met het nieuwe cluster.

Zie het artikel: [HDInsight-cluster upgraden naar een nieuwe versie](../hdinsight-upgrade-cluster.md)voor meer informatie.

## <a name="patch-cluster-operating-systems"></a>Patch voor cluster besturingssystemen

Zie het artikel: [patches voor het besturings systeem voor HDInsight](../hdinsight-os-patching.md)voor meer informatie.

## <a name="post-migration"></a>Na de migratie

1. **Toepassingen herstellen** -iteratieve de benodigde wijzigingen aanbrengen in de taken, processen en scripts.
2. **Voer tests uit** om functionele en prestatie tests van iteratief uit te voeren.
3. **Optimaliseer** : Los alle prestatie problemen op op basis van de bovenstaande test resultaten en voer vervolgens opnieuw een test uit om de prestatie verbeteringen te bevestigen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [HDInsight 4,0](./apache-hadoop-introduction.md).