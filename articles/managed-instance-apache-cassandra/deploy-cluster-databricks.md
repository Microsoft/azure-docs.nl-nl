---
title: 'Quick Start: een beheerd Apache Spark cluster implementeren met Azure Databricks'
description: In deze Quick start ziet u hoe u een beheerd Apache Spark cluster met Azure Databricks kunt implementeren met behulp van de Azure Portal.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: fd0d5c5b73ae1fb1eae7f38a22913018555ebe11
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101748492"
---
# <a name="quickstart-deploy-a-managed-apache-spark-cluster-preview-with-azure-databricks"></a>Snelstartgids: een beheerd Apache Spark cluster (preview) implementeren met Azure Databricks

Een door Azure beheerde instantie voor Apache Cassandra biedt geautomatiseerde implementatie-en schaal bewerkingen voor beheerde open source Apache Cassandra-data centers, het versnellen van hybride scenario's en het verminderen van doorlopend onderhoud.

> [!IMPORTANT]
> Een door Azure beheerde instantie voor Apache Cassandra is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In deze Quick start ziet u hoe u met behulp van de Azure Portal een volledig beheerd Apache Spark cluster maakt in de Azure-Virtual Network van uw door Azure beheerde exemplaar voor Apache Cassandra-cluster. U maakt het Spark-cluster in Azure Databricks. Later kunt u notitie blokken maken of koppelen aan het cluster, gegevens uit verschillende gegevens bronnen lezen en inzichten analyseren.

U kunt ook meer te weten komen over [de implementatie van Azure Databricks in uw Azure Virtual Network (Virtual Network injectie)](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).

## <a name="create-an-azure-databricks-cluster"></a>Een Azure Databricks-cluster maken

Volg deze stappen voor het maken van een Azure Databricks cluster in een Virtual Network met het door Azure beheerde exemplaar voor Apache Cassandra:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Zoek in het navigatie venster aan de linkerkant **resource groepen** en navigeer naar de resource groep die de Virtual Network bevat waar uw beheerde exemplaar is geïmplementeerd.

1. Open de **Virtual Network** resource en noteer de **adres ruimte**:

    :::image type="content" source="./media/deploy-cluster-databricks/virtual-network-address-space.png" alt-text="De adres ruimte van uw Virtual Network ophalen." border="true":::

1. Selecteer in de resource groep de optie **toevoegen** en zoeken naar **Azure Databricks** in het zoek veld:

    :::image type="content" source="./media/deploy-cluster-databricks/databricks.png" alt-text="Zoeken naar Azure Databricks." border="true":::

1. Selecteer **maken** om een Azure Databricks account te maken:

    :::image type="content" source="./media/deploy-cluster-databricks/databricks-create.png" alt-text="Maak een Azure Databricks-account." border="true":::

1. Vul de volgende waarden in:

   * **Werkruimte naam** : Geef een naam op voor uw Databricks-werk ruimte.
   * **Regio** : Zorg ervoor dat u dezelfde regio selecteert als uw Virtual Network.
   * **Prijs categorie** : Kies tussen Standard, Premium of proef versie. Bekijk de pagina [Prijzen voor Databricks](https://azure.microsoft.com/pricing/details/databricks/) voor meer informatie over deze categorieën.

    :::image type="content" source="./media/deploy-cluster-databricks/select-name.png" alt-text="Vul de naam van de werk ruimte, de regio en de prijs categorie voor het Databricks-account in." border="true":::

1. Selecteer vervolgens het tabblad **netwerken** en vul de volgende gegevens in:

   * **Azure Databricks-werk ruimte implementeren in uw Virtual Network (VNet)** : Selecteer **Ja**.
   * **Virtual Network** : Kies in de vervolg keuzelijst de Virtual Network waar uw beheerde exemplaar bestaat.
   * **Naam van openbaar subnet** : Voer een naam in voor het open bare subnet.
   * **CIDR-bereik voor openbaar subnet** : Voer een IP-bereik in voor het open bare subnet.
   * **Naam van persoonlijk subnet** : Voer een naam in voor het privé-subnet.
   * **CIDR-bereik privé-subnet** : Voer een IP-bereik in voor het privé-subnet.

   Zorg ervoor dat u hogere bereiken selecteert om bereik conflicten te voor komen. Gebruik, indien nodig, een [Visual-subnet reken machine](https://www.fryguy.net/wp-content/tools/subnets.html) om de bereiken te verdelen:

   :::image type="content" source="./media/deploy-cluster-databricks/subnet-calculator.png" alt-text="Gebruik de Virtual Network-subnet Calculator." border="true":::

   In de volgende scherm afbeelding ziet u voor beelden van Details in het deel venster netwerk:

   :::image type="content" source="./media/deploy-cluster-databricks/subnets.png" alt-text="Geef namen van open bare en particuliere subnetten op." border="true":::

1. Selecteer **controleren en maken** en vervolgens **maken** om de werk ruimte te implementeren.

1. **Open de werk ruimte** nadat deze is gemaakt.

1. U wordt omgeleid naar de Azure Databricks-portal. Selecteer in de portal **Nieuw cluster**.

1. Accepteer in het deel venster **Nieuw cluster** de standaard waarden voor alle velden, met uitzonde ring van de volgende velden:

   * **Cluster naam** : Voer een naam in voor het cluster.
   * **Databricks runtime versie** : selecteer scala 2,11 of een lagere versie die wordt ondersteund door de Cassandra-connector.

    :::image type="content" source="./media/deploy-cluster-databricks/spark-cluster.png" alt-text="Selecteer de Databricks-runtime versie en het Spark-cluster." border="true":::

1. Vouw **Geavanceerde opties** uit en voeg de volgende configuratie toe. Zorg ervoor dat u de knoop punten Ip's en referenties vervangt:

    ```java
    spark.cassandra.connection.host <node1 IP>,<node 2 IP>, <node IP>
    spark.cassandra.auth.password cassandra
    spark.cassandra.connection.port 9042
    spark.cassandra.auth.username cassandra
    spark.cassandra.connection.ssl.enabled true
    ```

1. Installeer op het tabblad **tape wisselaars** de meest recente versie van Spark connector voor Cassandra (*Spark-Cassandra-connector*) en start het cluster opnieuw:

    :::image type="content" source="./media/deploy-cluster-databricks/connector.png" alt-text="Installeer de Cassandra-connector." border="true":::

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze beheerde-exemplaar cluster niet meer wilt gebruiken, verwijdert u deze met de volgende stappen:

1. Selecteer **resource groepen** in het menu aan de linkerkant van Azure Portal.
1. Selecteer de resourcegroep die u eerder voor deze quickstart hebt gemaakt uit de lijst.
1. Selecteer **resource groep verwijderen** in het deel venster **overzicht** van de resource groep.
3. Selecteer in het volgende venster de naam van de resourcegroep die u wilt verwijderen en selecteer vervolgens **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een volledig beheerd Apache Spark cluster maakt in de Virtual Network van uw door Azure beheerde exemplaar voor Apache Cassandra-cluster. Vervolgens leert u hoe u de cluster-en Datacenter bronnen kunt beheren: 

> [!div class="nextstepaction"]
> [Een beheerd exemplaar van Azure beheren voor Apache Cassandra-resources met behulp van Azure CLI](manage-resources-cli.md)

