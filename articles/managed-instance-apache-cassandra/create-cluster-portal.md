---
title: 'Quickstart: Een Azure Managed Instance voor Apache Cassandra-cluster maken op Azure Portal'
description: In deze quickstart ziet u hoe u een Azure Managed Instance voor Apache Cassandra-cluster maakt met behulp van de Azure Portal.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: e42f85bb79dcb1bfe14cacbbfda3576888b841c9
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481325"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-from-the-azure-portal-preview"></a>Quickstart: Een Azure Managed Instance voor Apache Cassandra-cluster maken op Azure Portal (preview)
 
Azure Managed Instance voor Apache Cassandra biedt geautomatiseerde implementatie- en schaalbewerkingen voor beheerde opensource Apache Cassandra-datacenters, waardoor hybride scenario's worden versneld en doorlopend onderhoud wordt verkleind.

> [!IMPORTANT]
> Azure Managed Instance voor Apache Cassandra is momenteel beschikbaar als openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In deze quickstart wordt gedemonstreerd hoe u de Azure Portal om een Azure Managed Instance voor Apache Cassandra-cluster te maken.

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-a-managed-instance-cluster"></a><a id="create-account"></a>Een beheerd exemplaarcluster maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Zoek in de zoekbalk naar **Managed Instance voor Apache Cassandra** en selecteer het resultaat.

   :::image type="content" source="./media/create-cluster-portal/search-portal.png" alt-text="Zoek naar Managed Instance voor Apache Cassandra." lightbox="./media/create-cluster-portal/search-portal.png" border="true":::

1. Selecteer **de knop Beheerd exemplaar voor Apache Cassandra-cluster** maken.

   :::image type="content" source="./media/create-cluster-portal/create-cluster.png" alt-text="Het cluster maken." lightbox="./media/create-cluster-portal/create-cluster.png" border="true":::

1. Voer in **het deelvenster Beheerd exemplaar maken voor Apache Cassandra** de volgende gegevens in:

   * **Abonnement:** selecteer uw Azure-abonnement in de vervolgkeuzekeuze.
   * **Resourcegroep:** geef op of u een nieuwe resourcegroep wilt maken of een bestaande wilt gebruiken. Een resourcegroep is een container met gerelateerde resources voor een Azure-oplossing. Zie het artikel [Overzicht van Azure-resourcegroep voor](../azure-resource-manager/management/overview.md) meer informatie.
   * **Clusternaam:** voer een naam in voor uw cluster.
   * **Locatie:** de locatie waar uw cluster wordt geïmplementeerd.
   * **SKU:** het type SKU voor uw cluster.
   * **Nee. aantal knooppunten:** het aantal knooppunten in een cluster. Deze knooppunten fungeren als replica's voor uw gegevens.
   * **Eerste Cassandra-beheerderswachtwoord:** wachtwoord dat wordt gebruikt om het cluster te maken.
   * **Cassandra-beheerderswachtwoord bevestigen:** uw wachtwoord opnieuw invoeren.

    > [!NOTE]
    > Tijdens de openbare preview kunt u het beheerde exemplaarcluster maken in de regio's VS - oost, VS - west 2, VS - west 2, VS - centraal, VS - zuid-centraal, *Europa - noord, Europa - west, Zuid-Azië - oost* en Australië - oost.

   :::image type="content" source="./media/create-cluster-portal/create-cluster-page.png" alt-text="Vul het formulier cluster maken in." lightbox="./media/create-cluster-portal/create-cluster-page.png" border="true":::

1. Selecteer vervolgens het **tabblad** Netwerken.

1. Kies in **het** deelvenster Netwerken de **Virtual Network** en **subnet**. U kunt een bestaande Virtual Network of een nieuwe maken.

   :::image type="content" source="./media/create-cluster-portal/networking.png" alt-text="Netwerkdetails configureren." lightbox="./media/create-cluster-portal/networking.png" border="true":::

    > [!NOTE]
    > Voor de implementatie van een azure managed instance voor Apache Cassandra is internettoegang vereist. De implementatie mislukt in omgevingen waarin internettoegang is beperkt. Zorg ervoor dat u de toegang binnen uw VNet niet blokkeert tot de volgende essentiële Azure-services die nodig zijn om beheerde Cassandra goed te laten werken:
    > - Azure Storage
    > - Azure KeyVault
    > - Microsoft Azure Virtual Machine Scale Sets
    > - Azure Monitoring
    > - Azure Active Directory
    > - Azure-beveiliging

1. Als u in de laatste stap een nieuw VNet hebt gemaakt, gaat u verder met stap 8. Als u een bestaand VNet hebt geselecteerd voordat u uw cluster maakt, moet u een aantal speciale machtigingen toepassen op Virtual Network en het subnet. U doet dit door de opdracht te gebruiken, , en te `az role assignment create` vervangen door de juiste `<subscription ID>` `<resource group name>` `<VNet name>` waarden:

   ```azurecli-interactive
   az role assignment create --assignee a232010e-820c-4083-83bb-3ace5fc29d0b --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>
   ```

   > [!NOTE]
   > De `assignee` waarden en in de vorige opdracht zijn vaste waarden. Voer deze waarden exact in `role` zoals vermeld in de opdracht . Als u dit niet doet, leidt dit tot fouten bij het maken van het cluster. Als er fouten optreden bij het uitvoeren van deze opdracht, hebt u mogelijk geen machtigingen om deze uit te voeren. Neem contact op met uw beheerder voor machtigingen.

1. Nu u klaar bent met netwerken, klikt u op **Controleren en**  >  **maken**

    > [!NOTE]
    > Het kan tot 15 minuten duren voordat het cluster is gemaakt.

   :::image type="content" source="./media/create-cluster-portal/review-create.png" alt-text="Bekijk de samenvatting om het cluster te maken." lightbox="./media/create-cluster-portal/review-create.png" border="true":::


1. Nadat de implementatie is voltooid, controleert u de resourcegroep om het zojuist gemaakte beheerde exemplaarcluster te zien:

   :::image type="content" source="./media/create-cluster-portal/managed-instance.png" alt-text="Overzichtspagina nadat het cluster is gemaakt." lightbox="./media/create-cluster-portal/managed-instance.png" border="true":::

1. Als u door de clusterknooppunten wilt bladeren, gaat u naar het  deelvenster Virtual Network u hebt gebruikt om het cluster te maken en opent u het deelvenster Overzicht om ze weer te geven:

   :::image type="content" source="./media/create-cluster-portal/resources.png" alt-text="Bekijk de clusterbronnen." lightbox="./media/create-cluster-portal/resources.png" border="true":::


## <a name="connecting-to-your-cluster"></a>Verbinding maken met uw cluster

Azure Managed Instance voor Apache Cassandra maakt geen knooppunten met openbare IP-adressen, dus als u verbinding wilt maken met uw zojuist gemaakte Cassandra-cluster, moet u een andere resource binnen het VNet maken. Dit kan een toepassing zijn of een virtuele machine met het opensource-queryhulpprogramma [CQLSH van](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) Apache geïnstalleerd. U kunt een sjabloon [gebruiken om](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/) een virtuele Ubuntu-machine te implementeren. Gebruik SSH om verbinding te maken met de computer en installeer CQLSH met behulp van de onderstaande opdrachten:

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

## <a name="troubleshooting"></a>Problemen oplossen

Als er een fout wordt weergegeven bij het toepassen van machtigingen op uw Virtual Network, zoals Kan gebruiker of service-principal niet vinden in grafiekdatabase voor *'e5007d2c-4b13-4a74-9b6a-605d99f03501',* kunt u dezelfde machtiging handmatig toepassen vanuit de Azure Portal. Als u machtigingen wilt toepassen vanuit de portal, gaat u naar het deelvenster Toegangsbeheer **(IAM)** van uw bestaande virtuele netwerk en voegt u een roltoewijzing voor 'Azure Cosmos DB' toe aan de rol Netwerkbeheerder. Als er twee vermeldingen worden weergegeven wanneer u zoekt naar 'Azure Cosmos DB', voegt u beide vermeldingen toe, zoals wordt weergegeven in de volgende afbeelding: 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="Machtigingen toepassen" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> De Azure Cosmos DB roltoewijzing wordt alleen gebruikt voor implementatiedoeleinden. Azure Managed Instanced voor Apache Cassandra heeft geen back-Azure Cosmos DB.   

## <a name="clean-up-resources"></a>Resources opschonen

Als u dit beheerde exemplaarcluster niet meer gaat gebruiken, verwijdert u het met de volgende stappen:

1. Selecteer resourcegroepen in Azure Portal menu aan **de linkerkant.**
1. Selecteer de resourcegroep die u eerder voor deze quickstart hebt gemaakt uit de lijst.
1. Selecteer resourcegroep verwijderen in **het** deelvenster Overzicht **van de resourcegroep.**
1. Selecteer in het volgende venster de naam van de resourcegroep die u wilt verwijderen en selecteer vervolgens **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een Azure Managed Instance voor Apache Cassandra-cluster maakt met behulp van Azure Portal. U kunt nu aan de slag met het cluster:

> [!div class="nextstepaction"]
> [Een beheerd Apache Spark implementeren met Azure Databricks](deploy-cluster-databricks.md)
