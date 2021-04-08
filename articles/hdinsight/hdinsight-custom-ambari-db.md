---
title: Aangepaste Apache Ambari-Data Base op Azure HDInsight
description: Meer informatie over het maken van HDInsight-clusters met uw eigen aangepaste Apache Ambari-data base.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: fe38ddc594060c78a2d26e9b25476e38736b4cf7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946058"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>HDInsight-clusters met een aangepaste Ambari-data base instellen

Apache Ambari vereenvoudigt het beheer en de bewaking van een Apache Hadoop cluster. Ambari biedt een gebruiks vriendelijke webgebruikersinterface en REST API. Ambari is opgenomen in HDInsight-clusters en wordt gebruikt om het cluster te bewaken en configuratiewijzigingen aan te brengen.

In het normale cluster maken, zoals beschreven in andere artikelen, zoals het [instellen van clusters in hdinsight](hdinsight-hadoop-provision-linux-clusters.md), wordt Ambari geïmplementeerd in een [s0-Azure SQL database](../azure-sql/database/resource-limits-dtu-single-databases.md#standard-service-tier) die wordt beheerd door HDInsight en niet toegankelijk is voor gebruikers.

Met de functie Custom Ambari DB kunt u een nieuw cluster en installatie-Ambari implementeren in een externe data base die u beheert. De implementatie wordt uitgevoerd met een Azure Resource Manager sjabloon. Deze functie biedt de volgende voor delen:

- Aanpassing: u kiest de grootte en de verwerkings capaciteit van de data base. Als er grote clusters zijn die intensieve workloads verwerken, kan een Ambari-data base met lagere specificaties een bottleneck vormen voor beheer bewerkingen.
- Flexibiliteit: u kunt de Data Base naar wens schalen voor uw behoeften.
- Beheer: u kunt back-ups en beveiliging voor uw Data Base op een manier beheren die past bij uw organisatie vereisten.

In de rest van dit artikel komen de volgende punten aan bod:

- vereisten voor het gebruik van de functie Custom Ambari DB
- de stappen die nodig zijn om HDInsight-clusters in te richten met uw eigen externe data base voor Apache Ambari

## <a name="custom-ambari-db-requirements"></a>Aangepaste Ambari DB-vereisten

U kunt een aangepaste Ambari-data base implementeren met alle cluster typen en versies. Meerdere clusters kunnen niet dezelfde Ambari-Data Base gebruiken.

De aangepaste Ambari DB heeft de volgende vereisten:

- De naam van de data base mag geen afbreek streepjes of spaties bevatten
- U moet beschikken over een bestaande Azure SQL DB-server en-data base.
- De data base die u voor de installatie van Ambari opgeeft, moet leeg zijn. Er mogen geen tabellen zijn in het standaard dbo-schema.
- De gebruiker die wordt gebruikt om verbinding te maken met de data base moet machtigingen voor selecteren, CREATE TABLE en invoegen hebben voor de data base.
- Schakel de optie in om [toegang tot Azure-Services toe te staan](../azure-sql/database/vnet-service-endpoint-rule-overview.md#azure-portal-steps) op de server waarop u Ambari wilt hosten.
- IP-adressen van beheer van HDInsight-service moeten worden toegestaan in de firewall regel. Zie [IP-adressen van HDInsight-beheer](hdinsight-management-ip-addresses.md) voor een lijst met IP-adressen die moeten worden toegevoegd aan de firewall regel op server niveau.

Wanneer u uw Apache Ambari DB host in een externe data base, moet u rekening houden met de volgende punten:

- U bent verantwoordelijk voor de extra kosten van de Azure SQL-data base die Ambari bevat.
- Maak regel matig een back-up van uw aangepaste Ambari DB. Azure SQL Database worden automatisch back-ups gegenereerd, maar de Bewaar periode voor back-ups is afhankelijk van. Zie [informatie over automatische SQL database back-ups](../azure-sql/database/automated-backups-overview.md)voor meer informatie.

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>Clusters implementeren met een aangepaste Ambari-data base

Als u een HDInsight-cluster wilt maken dat gebruikmaakt van uw eigen externe Ambari-data base, gebruikt u de [aangepaste AMBARI DB Quick](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db)start-sjabloon.

Bewerk de para meters in de `azuredeploy.parameters.json` om informatie op te geven over het nieuwe cluster en de data base die Ambari zal bevatten.

U kunt de implementatie starten met behulp van de Azure CLI. Vervang door `<RESOURCEGROUPNAME>` de resource groep waar u uw cluster wilt implementeren.

```azurecli
az deployment group create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="database-sizing"></a>Database grootte

De volgende tabel bevat richt lijnen voor het selecteren van de Azure SQL DB-laag op basis van de grootte van uw HDInsight-cluster.

| Aantal worker-knoop punten | Vereiste DB-laag |
|---|---|
| <= 4 | S0 |
| >4 && <= 8 | S1 |
| >8 && <= 16 | S2 |
| >16 && <= 32 | S3 |
| >32 && <= 64 | S4 |
| >64 && <= 128 | P2 |
| >128 | Contact met ondersteuning |

## <a name="next-steps"></a>Volgende stappen

- [Externe metagegevensopslag gebruiken in Azure HDInsight](hdinsight-use-external-metadata-stores.md)
