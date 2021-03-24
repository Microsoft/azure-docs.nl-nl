---
title: Overzicht van bedrijfsbeveiliging in Azure HDInsight
description: Meer informatie over de verschillende methoden om bedrijfsbeveiliging in Azure HDInsight te garanderen.
ms.service: hdinsight
ms.topic: overview
ms.custom: seoapr2020
ms.date: 08/24/2020
ms.openlocfilehash: 713559a747b58e65d04c8310755baedc93db929e
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863153"
---
# <a name="overview-of-enterprise-security-in-azure-hdinsight"></a>Overzicht van bedrijfsbeveiliging in Azure HDInsight

Azure HDInsight biedt een aantal methoden om aan de vereisten van uw bedrijfsbeveiliging te voldoen. De meeste van deze oplossingen worden standaard niet geactiveerd. Dankzij deze flexibiliteit kunt u de beveiligingsfuncties kiezen die voor u het belangrijkst zijn en voorkomt u dat u betaalt voor functies die u niet nodig hebt. Deze flexibiliteit betekent ook dat het uw verantwoordelijkheid is om ervoor te zorgen dat de juiste oplossingen zijn ingeschakeld voor uw installatie en omgeving.

In dit artikel wordt gekeken naar beveiligingsoplossingen door beveiligingsoplossingen te verdelen in vier traditionele beveiligingspijlers: perimeterbeveiliging, verificatie, autorisatie en versleuteling.

In dit artikel wordt ook het **Azure HDInsight Enterprise Security Package (ESP)** geïntroduceerd, dat voorziet in op Active Directory gebaseerde verificatie, ondersteuning voor meerdere gebruikers en op rollen gebaseerd toegangsbeheer voor HDInsight-clusters.

## <a name="enterprise-security-pillars"></a>Enterprise Security Package

Vanuit een bepaald oogpunt kan bedrijfsbeveiliging worden gezien als een verdeling van beveiligingsoplossingen in vier hoofdgroepen op basis van het beheertype. Deze groepen worden ook wel beveiligingspijlers genoemd en bestaan uit de volgende typen: perimeterbeveiliging, verificatie, autorisatie en versleuteling.

### <a name="perimeter-security"></a>Perimeterbeveiliging

Perimeterbeveiliging in HDInsight wordt bereikt door het gebruik van [virtuele netwerken](../hdinsight-plan-virtual-network-deployment.md). Een bedrijfsbeheerder kan een cluster in een virtueel netwerk (VNet) maken en via netwerkbeveiligingsgroepen (NSG's) de toegang tot het virtuele netwerk beperken. Alleen de toegestane IP-adressen in de NSG-regels voor binnenkomend verkeer kunnen communiceren met het HDInsight-cluster. Deze configuratie biedt een perimeterbeveiliging.

Alle clusters die zijn geïmplementeerd in een VNet, hebben ook een privé-eindpunt. Het eindpunt wordt omgezet in een privé-IP-adres in het VNET voor privé-HTTP-toegang tot de clustergateways.

### <a name="authentication"></a>Verificatie

[Enterprise Security Package](apache-domain-joined-architecture.md) van HDInsight biedt verificatie op basis van Active Directory, ondersteuning voor meerdere gebruikers en toegangsbeheer op basis van rollen. De integratie met Active Directory wordt bewerkstelligd via [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md). Met deze functionaliteit kunt u een HDInsight-cluster maken dat is gekoppeld aan een Active Directory-domein. Configureer vervolgens een lijst met werknemers van de onderneming die bij het cluster kan worden geverifieerd.

Met deze instelling kunnen de werknemers van het bedrijf zich met hun domeinreferenties aanmelden bij de clusterknooppunten. Ze kunnen ook hun domeinreferenties gebruiken om te verifiëren bij andere goedgekeurde eindpunten. Zoals Apache Ambari Views, ODBC, JDBC, PowerShell en REST API's voor interactie met het cluster.

### <a name="authorization"></a>Autorisatie

Een door de meeste bedrijven gevolgde best practice is ervoor te zorgen dat niet alle werknemers toegang hebben tot alle bedrijfsresources. Zo kan de beheerder ook toegangscontrolebeleid op basis van rollen definiëren voor de clusterresources. Deze actie is alleen beschikbaar in de ESP-clusters.

De Hadoop-beheerder kan op rollen gebaseerd toegangsbeheer (RBAC) configureren. De configuraties beveiligen Apache [Hive](apache-domain-joined-run-hive.md), [HBase](apache-domain-joined-run-hbase.md) en [Kafka](apache-domain-joined-run-kafka.md) met Apache Ranger-invoegtoepassingen. Met het configureren van het RBAC-beleid kunt u machtigingen koppelen aan een rol in de organisatie. Deze abstractielaag maakt het gemakkelijker om ervoor te zorgen dat personen alleen de machtigingen hebben die nodig zijn om de werkzaamheden uit te voeren waarvoor ze verantwoordelijk zijn. Daarnaast kunt u met Ranger de gegevenstoegang van werknemers en alle wijzigingen die worden uitgevoerd in toegangsbeheerbeleid controleren.

De beheerder kan bijvoorbeeld [Apache Ranger](https://ranger.apache.org/) configureren om toegangscontrolebeleid in te stellen voor Hive. Deze functionaliteit zorgt ervoor dat wordt gefilterd op rijniveau en op kolomniveau (gegevensmaskering). Tevens worden de gevoelige gegevens van niet-geautoriseerde gebruikers gefilterd.

### <a name="auditing"></a>Controleren

Het controleren van de toegang tot clusterresources is nodig voor het bijhouden van onbevoegde of onbedoelde toegang tot de resources. Het is even belangrijk als het beveiligen van de clusterresources tegen onbevoegde toegang.

De beheerder kan alle toegang tot de HDInsight-clusterresources en -gegevens bekijken en rapporteren. De beheerder kan wijzigingen in het toegangsbeheerbeleid bekijken en rapporteren.

[Schakel Azure Monitor in](../hdinsight-hadoop-oms-log-analytics-tutorial.md#cluster-auditing) om toegang te krijgen tot Apache Ranger- en Ambari-auditlogboeken en logboeken voor SSH-toegang en bekijk de tabellen met controlerecords.

### <a name="encryption"></a>Versleuteling

Het beveiligen van gegevens is belangrijk voor het voldoen aan de beveiligings- en nalevingsvereisten van de organisatie. U moet niet alleen de toegang van onbevoegde werknemers tot gegevens beperken, maar u moet deze gegevens ook versleutelen.

HDInsight biedt ondersteuning voor versleuteling van data-at-rest met zowel via het platform als [door de klant beheerde sleutels](../disk-encryption.md). Versleuteling van data-in-transit wordt verwerkt met zowel TLS als IPSec. Zie [Versleuteling-in-transit voor Azure HDInsight](encryption-in-transit.md) voor meer informatie.

### <a name="compliance"></a>Naleving

Azure-aanbiedingen voor naleving zijn gebaseerd op verschillende soorten garanties, waaronder formele certificeringen. Tevens zijn deze gebaseerd op verklaringen, validaties en autorisaties. Evaluaties die door onafhankelijke auditkantoren van derden worden geproduceerd. Contractuele wijzigingen, zelfevaluaties en richtsnoeren ten aanzien van klanten die door Microsoft worden geproduceerd. Zie het [Microsoft-vertrouwenscentrum](https://www.microsoft.com/trust-center) en het document [Overzicht van Microsoft Azure-naleving](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) voor nalevingsinformatie over HDInsight.

## <a name="shared-responsibility-model"></a>Het model van gedeelde verantwoordelijkheid

De volgende afbeelding bevat een overzicht van de belangrijkste systeembeveiligingsgebieden en de beveiligingsoplossingen die voor u in elk gebied beschikbaar zijn. Het markeert ook voor welke beveiligingsgebieden u verantwoordelijk bent als klant. En welke gebieden de verantwoordelijkheid zijn van HDInsight als serviceprovider.

:::image type="content" source="./media/hdinsight-security-overview/hdinsight-shared-responsibility.png" alt-text="Het diagram van met HDInsight gedeelde verantwoordelijkheden" border="false":::

De volgende tabel bevat koppelingen naar resources voor elk type beveiligingsoplossing.

| Beveiligingsgebied | Beschikbare oplossingen | Verantwoordelijke partij |
|---|---|---|
| Beveiliging van gegevenstoegang | [Toegangsbeheerlijsten (ACL's)](../../storage/blobs/data-lake-storage-access-control.md) voor Data Lake Storage Gen1 en Gen2 configureren  | Klant |
|  | De eigenschap [Beveiligde overdracht vereist](../../storage/common/storage-require-secure-transfer.md) bij opslagaccounts inschakelen. | Klant |
|  | [Firewalls en virtuele netwerken voor Azure Storage](../../storage/common/storage-network-security.md) configureren | Klant |
|  | [Service-eindpunten voor virtueel netwerk van Azure](../../virtual-network/virtual-network-service-endpoints-overview.md) configureren voor Cosmos DB en [Azure SQL-database](../../azure-sql/database/vnet-service-endpoint-rule-overview.md) | Klant |
|  | Controleer of de functie voor [Versleuteling-in-transit](./encryption-in-transit.md) is ingeschakeld om TLS en IPSec te gebruiken voor communicatie binnen het cluster. | Klant |
|  | [Door de klant beheerde sleutels](../../storage/common/customer-managed-keys-configure-key-vault.md) configureren voor Azure Storage-versleuteling | Klant |
|  | Toegang tot uw gegevens beheren via Azure-ondersteuning met behulp van [Klanten-lockbox](../../security/fundamentals/customer-lockbox-overview.md) | Klant |
| Beveiliging van toepassingen en middleware | Integreren met AAD-DS en [ESP configureren](apache-domain-joined-configure-using-azure-adds.md) of [HIB for OAuth Authentication](identity-broker.md) gebruiken| Klant |
|  | Beleid voor [Apache Ranger-autorisatie](apache-domain-joined-run-hive.md) configureren | Klant |
|  | [Azure Monitor-logboeken](../hdinsight-hadoop-oms-log-analytics-tutorial.md) gebruiken | Klant |
| Beveiliging van besturingssysteem | Clusters met de nieuwste beveiligde basisinstallatiekopie maken | Klant |
|  | Periodiek ervoor zorgen dat [patches op het besturingssysteem worden toegepast](../hdinsight-os-patching.md) | Klant |
|  | Zorgen voor [CMK-schijfversleuteling voor VM's](../disk-encryption.md) | Klant |
| Netwerkbeveiliging | [Een virtueel netwerk](../hdinsight-plan-virtual-network-deployment.md) configureren |
|  | [Regels voor binnenkomend verkeer van netwerkbeveiligingsgroepen (NSG)](../control-network-traffic.md) of [privékoppeling](../hdinsight-private-link.md) configureren | Klant |
|  | [Beperkingen voor uitgaand verkeer](../hdinsight-restrict-outbound-traffic.md) configureren met een firewall | Klant |
|  | [IPSec-versleuteling in transit](encryption-in-transit.md) tussen clusterknooppunten configureren | Klant |
| Gevirtualiseerde infrastructuur | N.v.t. | HDInsight (cloudprovider) |
| Fysieke beveiligingsinfrastructuur | N.v.t. | HDInsight (cloudprovider) |

## <a name="next-steps"></a>Volgende stappen

* [HDInsight-clusters plannen met ESP](apache-domain-joined-architecture.md)
* [HDInsight-clusters configureren met ESP](./apache-domain-joined-configure-using-azure-adds.md)
* [HDInsight-clusters beheren met ESP](apache-domain-joined-manage.md)
