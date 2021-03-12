---
title: 'Quick Start: een Azure Managed instance voor Apache Cassandra-cluster maken op basis van de Azure Portal'
description: In deze Quick start ziet u hoe u een door Azure beheerd exemplaar voor Apache Cassandra-cluster maakt met behulp van de Azure Portal.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: db3f188cc796642285d9b082b46371879491c632
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103225231"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-from-the-azure-portal-preview"></a>Snelstartgids: een door Azure beheerd exemplaar voor Apache Cassandra-cluster maken op basis van de Azure Portal (preview-versie)
 
Een door Azure beheerde instantie voor Apache Cassandra biedt geautomatiseerde implementatie-en schaal bewerkingen voor beheerde open source Apache Cassandra-data centers, het versnellen van hybride scenario's en het verminderen van doorlopend onderhoud.

> [!IMPORTANT]
> Een door Azure beheerde instantie voor Apache Cassandra is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In deze Quick start ziet u hoe u de Azure Portal gebruikt voor het maken van een door Azure beheerd exemplaar voor Apache Cassandra-cluster.

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-a-managed-instance-cluster"></a><a id="create-account"></a>Een beheerd exemplaar cluster maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Zoek in de zoek balk naar een **beheerd exemplaar voor Apache Cassandra** en selecteer het resultaat.

   :::image type="content" source="./media/create-cluster-portal/search-portal.png" alt-text="Zoek naar een beheerd exemplaar voor Apache Cassandra." lightbox="./media/create-cluster-portal/search-portal.png" border="true":::

1. Selecteer **beheerde instantie maken voor Apache Cassandra-cluster** knop.

   :::image type="content" source="./media/create-cluster-portal/create-cluster.png" alt-text="Het cluster maken." lightbox="./media/create-cluster-portal/create-cluster.png" border="true":::

1. Voer in het deel venster **beheerde instantie voor Apache Cassandra maken** de volgende gegevens in:

   * **Abonnement** : Selecteer uw Azure-abonnement in de vervolg keuzelijst.
   * **Resource groep**: Geef op of u een nieuwe resource groep wilt maken of een bestaande wilt gebruiken. Een resourcegroep is een container met gerelateerde resources voor een Azure-oplossing. Zie overzichts artikel over [Azure-resource groepen](../azure-resource-manager/management/overview.md) voor meer informatie.
   * **Cluster naam** : Voer een naam in voor het cluster.
   * **Locatie** -locatie waar uw cluster wordt geïmplementeerd.
   * **SKU** : het type SKU voor uw cluster.
   * **Nee. knoop punten**: het aantal knoop punten in een cluster. Deze knoop punten fungeren als replica's voor uw gegevens.
   * Het **eerste Cassandra** -wacht woord voor het beheerders account dat wordt gebruikt om het cluster te maken.
   * **Bevestig het wacht woord** van de Cassandra-beheerder-Voer uw wacht woord opnieuw in.

    > [!NOTE]
    > Tijdens de open bare preview kunt u het beheerde exemplaar van het cluster maken in het *VS-Oost, VS-West, VS-Oost 2, VS-West 2, VS-midden, Zuid-Centraal VS, Europa-Noord, Europa-West, zuid Azië-Oost en Australië-Oost* regio's.

   :::image type="content" source="./media/create-cluster-portal/create-cluster-page.png" alt-text="Vul het formulier voor het maken van het cluster in." lightbox="./media/create-cluster-portal/create-cluster-page.png" border="true":::

1. Selecteer vervolgens het tabblad **netwerken** .

1. Kies in het deel venster **netwerken** de **Virtual Network** naam en het **subnet**. U kunt een bestaande Virtual Network selecteren of een nieuwe maken.

   :::image type="content" source="./media/create-cluster-portal/networking.png" alt-text="Netwerk gegevens configureren." lightbox="./media/create-cluster-portal/networking.png" border="true":::

1. Als u in de laatste stap een nieuwe VNet hebt gemaakt, gaat u verder met stap 8. Als u een bestaand VNet hebt geselecteerd voordat u het cluster maakt, moet u enkele speciale machtigingen Toep assen op de Virtual Network en het subnet. Als u dit wilt doen, gebruikt u de `az role assignment create` opdracht, vervangt `<subscription ID>` ,, `<resource group name>` `<VNet name>` en `<subnet name>` met de juiste waarden:

   ```azurecli-interactive
   az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>
   ```

   > [!NOTE]
   > De `assignee` `role` waarden en in de vorige opdracht zijn vaste waarden. Voer deze waarden precies zoals vermeld in de opdracht in. Als u dit niet doet, leidt dit tot fouten bij het maken van het cluster. Als er fouten optreden tijdens het uitvoeren van deze opdracht, bent u mogelijk niet gemachtigd om deze uit te voeren. Neem contact op met uw beheerder voor machtigingen.

1. Nu u klaar bent met het netwerk, klikt u op **controleren +** aanmaken  >  **maken**

    > [!NOTE]
    > Het kan tot vijf tien minuten duren voordat het cluster is gemaakt.

   :::image type="content" source="./media/create-cluster-portal/review-create.png" alt-text="Bekijk de samen vatting om het cluster te maken." lightbox="./media/create-cluster-portal/review-create.png" border="true":::


1. Nadat de implementatie is voltooid, controleert u de resource groep om het zojuist gemaakte beheerde exemplaar cluster te zien:

   :::image type="content" source="./media/create-cluster-portal/managed-instance.png" alt-text="Overzichts pagina nadat het cluster is gemaakt." lightbox="./media/create-cluster-portal/managed-instance.png" border="true":::

1. Als u door de cluster knooppunten wilt bladeren, gaat u naar het deel venster Virtual Network dat u hebt gebruikt om het cluster te maken en opent u het deel venster **overzicht** om ze te bekijken:

   :::image type="content" source="./media/create-cluster-portal/resources.png" alt-text="De cluster bronnen weer geven." lightbox="./media/create-cluster-portal/resources.png" border="true":::



## <a name="connecting-to-your-cluster"></a>Verbinding maken met uw cluster

Azure Managed instance voor Apache Cassandra maakt geen knoop punten met een openbaar IP-adres, zodat u verbinding kunt maken met het zojuist gemaakte Cassandra-cluster. u moet dan een andere resource in het VNet aanmaken. Dit kan een toepassing zijn, of een virtuele machine met open source query tool [CQLSH](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) geïnstalleerd. U kunt een [sjabloon](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/) gebruiken om een virtuele Ubuntu-machine te implementeren. Gebruik SSH om verbinding te maken met de computer en installeer CQLSH met behulp van de onderstaande opdrachten, wanneer u deze implementeert:

```bash
# Install default-jre and default-jdk
sudo apt update
sudo apt install openjdk-8-jdk openjdk-8-jre

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to CQLSH (replace <IP> with the private IP addresses of the nodes in your Datacenter):
host=("<IP>" "<IP>" "<IP>")
cqlsh $host 9042 -u cassandra -p cassandra --ssl
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze beheerde-exemplaar cluster niet meer wilt gebruiken, verwijdert u deze met de volgende stappen:

1. Selecteer **resource groepen** in het menu aan de linkerkant van Azure Portal.
1. Selecteer de resourcegroep die u eerder voor deze quickstart hebt gemaakt uit de lijst.
1. Selecteer **resource groep verwijderen** in het deel venster **overzicht** van de resource groep.
1. Selecteer in het volgende venster de naam van de resourcegroep die u wilt verwijderen en selecteer vervolgens **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een door Azure beheerde instantie voor Apache Cassandra-cluster maakt met behulp van Azure Portal. U kunt nu aan de slag gaan met het cluster:

> [!div class="nextstepaction"]
> [Een beheerd Apache Spark cluster implementeren met Azure Databricks](deploy-cluster-databricks.md)
