---
title: Azure HDInsight-Azure Data Lake Storage Gen2-portal maken
description: Meer informatie over het gebruik van Azure Data Lake Storage Gen2 met Azure HDInsight-clusters met behulp van de portal.
author: guyhay
ms.author: guyhay
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 09/17/2020
ms.openlocfilehash: 5eb6cfc7f0dbc0bc886550e1d601dc52dff66267
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866366"
---
# <a name="create-a-cluster-with-data-lake-storage-gen2-using-the-azure-portal"></a>Een cluster met Data Lake Storage Gen2 maken met behulp van de Azure Portal

De Azure Portal is een webgebaseerd hulp programma voor het beheren van services en resources die worden gehost in de Microsoft Azure Cloud. In dit artikel leert u hoe u Azure HDInsight-clusters op basis van Linux maakt met behulp van de portal. Meer informatie over het [maken van HDInsight-clusters](./hdinsight-hadoop-provision-linux-clusters.md)is beschikbaar.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Als u een HDInsight-cluster wilt maken dat gebruikmaakt van Data Lake Storage Gen2 voor opslag, volgt u deze stappen voor het configureren van een opslag account met een hiërarchische naam ruimte.

## <a name="create-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit maken

Maak een door de gebruiker toegewezen beheerde identiteit als u deze nog niet hebt.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Klik linksboven op **een resource maken**.
1. Typ door de **gebruiker toegewezen** in het zoekvak en klik op door de **gebruiker toegewezen beheerde identiteit**.
1. Klik op **Create**.
1. Voer een naam in voor uw beheerde identiteit, selecteer het juiste abonnement, de resource groep en de locatie.
1. Klik op **Create**.

Zie [beheerde identiteiten in azure hdinsight](hdinsight-managed-identities.md)voor meer informatie over de werking van beheerde identiteiten in azure hdinsight.

:::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png" alt-text="Een door de gebruiker toegewezen beheerde identiteit maken":::

## <a name="create-a-storage-account-to-use-with-data-lake-storage-gen2"></a>Een opslag account maken voor gebruik met Data Lake Storage Gen2

Maak een opslag account om te gebruiken met Azure Data Lake Storage Gen2.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Klik linksboven op **een resource maken**.
1. Typ **opslag** in het zoekvak en klik op **opslag account**.
1. Klik op **Create**.
1. In het scherm **opslag account maken** :
    1. Selecteer de juiste abonnement en resource groep.
    1. Voer een naam in voor uw opslag account met Data Lake Storage Gen2.
    1. Klik op het tabblad **Geavanceerd** .
    1. Klik op **ingeschakeld** naast **hiërarchische naam ruimte** onder **Data Lake Storage Gen2**.
    1. Klik op **Controleren + maken**.
    1. Klik op **Maken**.

Zie [Quick Start: een opslag account maken voor Azure data Lake Storage Gen2](../storage/blobs/create-data-lake-storage-account.md)voor meer informatie over andere opties tijdens het maken van een opslag account.

:::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png" alt-text="Scherm opname van het maken van een opslag account in de Azure Portal":::

## <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2"></a>Stel machtigingen in voor de beheerde identiteit op de Data Lake Storage Gen2

Wijs de beheerde identiteit toe aan de rol van de eigenaar van de **opslag-BLOB** voor het opslag account.

1. Ga in het [Azure Portal](https://portal.azure.com)naar uw opslag account.
1. Selecteer uw opslag account en selecteer vervolgens **toegangs beheer (IAM)** om de instellingen voor toegangs beheer voor het account weer te geven. Selectter het tabblad **Roltoewijzingen** om de lijst met roltoewijzingen te zien.

    :::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png" alt-text="Scherm opname van instellingen voor opslag toegangs beheer":::

1. Selecteer de knop **+ roltoewijzing toevoegen** om een nieuwe rol toe te voegen.
1. Selecteer in het venster **roltoewijzing toevoegen** de rol **Storage BLOB-gegevens eigenaar** . Selecteer vervolgens het abonnement met het beheerde identiteits-en opslag account. Zoek vervolgens naar de door de gebruiker toegewezen beheerde identiteit die u eerder hebt gemaakt. Ten slotte selecteert u de beheerde identiteit en wordt deze weer gegeven onder **geselecteerde leden**.

    :::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png" alt-text="Scherm afbeelding die laat zien hoe een Azure-rol kan worden toegewezen":::

1. Selecteer **Opslaan**. De gebruikers-id die u hebt geselecteerd, wordt nu weer gegeven onder de geselecteerde rol.
1. Nadat deze initiële installatie is voltooid, kunt u een cluster maken via de portal. Het cluster moet zich in dezelfde Azure-regio bevinden als het opslag account. Selecteer op het tabblad **opslag** van het menu cluster maken de volgende opties:

    * Selecteer **Azure data Lake Storage Gen2** voor het **primaire opslag type**.
    * Onder **primair opslag account** zoekt en selecteert u het zojuist gemaakte opslag account met data Lake Storage Gen2 opslag.

    * Selecteer onder **identiteit** de zojuist gemaakte door de gebruiker toegewezen beheerde identiteit.

        :::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png" alt-text="Opslag instellingen voor het gebruik van Data Lake Storage Gen2 met Azure HDInsight":::

    > [!NOTE]
    > * Als u een secundair opslag account met Data Lake Storage Gen2 wilt toevoegen, moet u op het niveau van het opslag account eenvoudigweg de beheerde identiteit toewijzen die u eerder hebt gemaakt voor de nieuwe Data Lake Storage Gen2 die u wilt toevoegen. U wordt aangeraden een secundair opslag account met Data Lake Storage Gen2 toe te voegen via de Blade ' extra opslag accounts ' op HDInsight wordt niet ondersteund.
    > * U kunt RA-GRS of RA-ZRS inschakelen op het Azure Blob-opslag account dat door HDInsight wordt gebruikt. Het maken van een cluster op basis van het ' RA-GRS-of RA-ZRS-secundaire eind punt wordt echter niet ondersteund.

## <a name="delete-the-cluster"></a>Het cluster verwijderen

Zie [een HDInsight-cluster verwijderen met behulp van uw browser, Power shell of de Azure cli](./hdinsight-delete-cluster.md).

## <a name="troubleshoot"></a>Problemen oplossen

Zie [Vereisten voor toegangsbeheer](./hdinsight-hadoop-customize-cluster-linux.md#access-control) als u problemen ondervindt met het maken van HDInsight-clusters.

## <a name="next-steps"></a>Volgende stappen

U hebt een HDInsight-cluster gemaakt. Meer informatie over het werken met uw cluster.

### <a name="apache-spark-clusters"></a>Apache Spark clusters

* [Op Linux gebaseerde HDInsight-clusters aanpassen met behulp van script acties](hdinsight-hadoop-customize-cluster-linux.md)
* [Een zelfstandige toepassing maken met behulp van Scala](spark/apache-spark-create-standalone-application.md)
* [Apache Livy gebruiken om taken op afstand uit te voeren in een Apache Spark-cluster](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark met BI: interactieve gegevens analyses uitvoeren met behulp van Spark in HDInsight met BI-hulpprogram ma's](spark/apache-spark-use-bi-tools.md)
* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken om voedsel inspectie resultaten te voors pellen](spark/apache-spark-machine-learning-mllib-ipython.md)

### <a name="apache-hadoop-clusters"></a>Apache Hadoop clusters

* [Apache Hive gebruiken met HDInsight](hadoop/hdinsight-use-hive.md)
* [MapReduce gebruiken met HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-clusters

* [Aan de slag met Apache HBase in HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Ontwikkel Java-toepassingen voor Apache HBase in HDInsight](hbase/apache-hbase-build-java-maven-linux.md)
