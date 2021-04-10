---
title: Beperking van uitgaand netwerk verkeer configureren-Azure HDInsight
description: Meer informatie over het configureren van de beperking van uitgaand netwerk verkeer voor Azure HDInsight-clusters.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/17/2020
ms.openlocfilehash: 06990a5bd1d6619f07952e84870a01f5cd5068df
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384422"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall"></a>Uitgaand netwerkverkeer configureren voor Azure HDInsight-clusters met Firewall

In dit artikel worden de stappen beschreven voor het beveiligen van uitgaand verkeer van uw HDInsight-cluster met behulp van Azure Firewall. In de onderstaande stappen wordt ervan uitgegaan dat u een Azure Firewall configureert voor een bestaand cluster. Als u een nieuw cluster achter een firewall implementeert, maakt u eerst uw HDInsight-cluster en subnet. Volg de stappen in deze hand leiding.

## <a name="background"></a>Achtergrond

HDInsight-clusters worden normaal gesp roken geïmplementeerd in een virtueel netwerk. Het cluster heeft afhankelijkheden voor services buiten dat virtuele netwerk.

Het inkomende beheer verkeer kan niet via een firewall worden verzonden. U kunt NSG-service tags gebruiken voor het inkomende verkeer zoals [hier](./hdinsight-service-tags.md)wordt beschreven. 

De afhankelijkheden voor uitgaand verkeer van HDInsight zijn bijna volledig gedefinieerd met FQDN-verwijzingen. Er zijn geen statische IP-adressen achter. Het ontbreken van statische adressen betekent dat netwerk beveiligings groepen (Nsg's) uitgaand verkeer van een cluster niet kunnen vergren delen. De IP-adressen die vaak voldoende worden gewijzigd, kunnen geen regels instellen op basis van de huidige naam omzetting en het gebruik.

Beveilig uitgaande adressen met een firewall waarmee het uitgaande verkeer op basis van FQDN-namen kan worden beheerd. Azure Firewall beperkt het uitgaande verkeer op basis van de FQDN van de doel-of [FQDN-Tags](../firewall/fqdn-tags.md).

## <a name="configuring-azure-firewall-with-hdinsight"></a>Azure Firewall configureren met HDInsight

Een samen vatting van de stappen voor het vergren delen van uitgaand verkeer van uw bestaande HDInsight met Azure Firewall zijn:

1. Maak een subnet.
1. Maak een firewall.
1. Voeg toepassings regels toe aan de firewall.
1. Voeg netwerk regels toe aan de firewall.
1. Een routerings tabel maken.

### <a name="create-new-subnet"></a>Nieuw subnet maken

Maak een subnet met de naam **AzureFirewallSubnet** in het virtuele netwerk waarin uw cluster zich bevindt.

### <a name="create-a-new-firewall-for-your-cluster"></a>Een nieuwe firewall voor uw cluster maken

Maak een firewall met de naam **test-FW01** met behulp van de stappen in de hand leiding voor **het implementeren van de firewall** [: Implementeer en configureer Azure Firewall met behulp van de Azure Portal](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall).

### <a name="configure-the-firewall-with-application-rules"></a>De firewall configureren met toepassings regels

Maak een toepassings regel verzameling waarmee het cluster belang rijke communicatie kan verzenden en ontvangen.

1. Selecteer de nieuwe firewall **test-FW01** van de Azure Portal.

1. Navigeer naar **instellingen**  >  **regels**  >  **toepassings regel verzameling**  >  **+ toepassings regel verzameling toevoegen**.

    :::image type="content" source="./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png" alt-text="Titel: toepassings regel verzameling toevoegen":::

1. Geef op het scherm **toepassings regel toevoegen** de volgende informatie op:

    **Bovenste sectie**

    | Eigenschap|  Waarde|
    |---|---|
    |Naam| FwAppRule|
    |Prioriteit|200|
    |Bewerking|Toestaan|

    **Sectie FQDN-Tags**

    | Name | Bron adres | FQDN-label | Notities |
    | --- | --- | --- | --- |
    | Rule_1 | * | WindowsUpdate en HDInsight | Vereist voor HDI-Services |

    **Sectie FQDN-doel items**

    | Name | Bron adressen | Protocol:Poort | Doel-FQDN-naam | Notities |
    | --- | --- | --- | --- | --- |
    | Rule_2 | * | https:443 | login.windows.net | Windows-aanmeldings activiteit toestaan |
    | Rule_3 | * | https:443 | login.microsoftonline.com | Windows-aanmeldings activiteit toestaan |
    | Rule_4 | * | https:443 | storage_account_name. blob. core. Windows. net | Vervang door `storage_account_name` de werkelijke naam van het opslag account. Zorg ervoor dat ["beveiligde overdracht vereist"](../storage/common/storage-require-secure-transfer.md) is ingeschakeld op het opslag account. Als u een privé-eind punt gebruikt voor toegang tot opslag accounts, is deze stap niet nodig en wordt het opslag verkeer niet doorgestuurd naar de firewall.|

   :::image type="content" source="./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png" alt-text="Titel: Details van toepassings regel verzameling invoeren":::

1. Selecteer **Toevoegen**.

### <a name="configure-the-firewall-with-network-rules"></a>De firewall met netwerk regels configureren

Maak de netwerk regels om uw HDInsight-cluster correct te configureren. 

1. Ga verder met de vorige stap, navigeer naar **netwerk regel verzameling**  >  **+ verzameling netwerk regels toevoegen**.

1. Geef in het scherm **verzameling van netwerk regels toevoegen** de volgende informatie op:

    **Bovenste sectie**

    | Eigenschap|  Waarde|
    |---|---|
    |Naam| FwNetRule|
    |Prioriteit|200|
    |Bewerking|Toestaan|

    **Sectie Service Tags**

    | Name | Protocol | Bron adressen | Servicetags | Doel poorten | Notities |
    | --- | --- | --- | --- | --- | --- |
    | Rule_5 | TCP | * | SQL | 1433, 11000-11999 | Als u de standaard SQL-servers gebruikt die door HDInsight worden meegeleverd, configureert u een netwerk regel in het gedeelte service tags voor SQL waarmee u SQL-verkeer kunt registreren en controleren. Tenzij u service-eind punten voor SQL Server op het HDInsight-subnet hebt geconfigureerd, waardoor de firewall wordt overgeslagen. Als u een aangepaste SQL Server gebruikt voor Ambari, Oozie, zwerver en Hive-meta Stores, hoeft u alleen het verkeer naar uw eigen aangepaste SQL-servers toe te staan. Raadpleeg [Azure SQL database en de connectiviteits architectuur van Azure Synapse](../azure-sql/database/connectivity-architecture.md) voor meer informatie over waarom het poort bereik van 11000-11999 ook nodig is naast 1433. |
    | Rule_6 | TCP | * | Azure Monitor | * | Beschrijving Klanten die de functie voor automatisch schalen willen gebruiken, moeten deze regel toevoegen. |
    
   :::image type="content" source="./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png" alt-text="Titel: toepassings regel verzameling invoeren":::

1. Selecteer **Toevoegen**.

### <a name="create-and-configure-a-route-table"></a>Een route tabel maken en configureren 

Maak een route tabel met de volgende vermeldingen:

* Alle IP-adressen van [status-en beheer Services](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-all-regions) met het volgende hop-type **Internet**. Het moet 4 Ip's van de algemene regio's en 2 Ip's bevatten voor uw specifieke regio. Deze regel is alleen nodig als de ResourceProviderConnection is ingesteld op *binnenkomend*. Als de ResourceProviderConnection is ingesteld op *outbound* , zijn deze IP-adressen niet nodig in de UDR. 

* Eén virtuele-toestel route voor IP-adres 0.0.0.0/0 met de volgende hop wordt uw Azure Firewall privé-IP-adres.

Gebruik bijvoorbeeld de volgende stappen om de route tabel te configureren voor een cluster dat is gemaakt in de regio VS-Oost VS:

1. Selecteer uw Azure Firewall **-test-FW01**. Kopieer het **privé-IP-adres** dat wordt weer gegeven op de pagina **overzicht** . In dit voor beeld gebruiken we een voor beeld **van een adres van 10.0.2.4**.

1. Ga vervolgens naar **alle services**  >  **netwerk**  >  **route tabellen** en **Maak een route tabel**.

1. Navigeer vanuit uw nieuwe route naar **instellingen**  >  **routes**  >  **en toevoegen**. Voeg de volgende routes toe:

| Routenaam | Adresvoorvoegsel | Volgend hoptype | Adres van de volgende hop |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | NA |
| 23.99.5.239 | 23.99.5.239/32 | Internet | NA |
| 168.61.48.131 | 168.61.48.131/32 | Internet | NA |
| 138.91.141.162 | 138.91.141.162/32 | Internet | NA |
| 13.82.225.233 | 13.82.225.233/32 | Internet | NA |
| 40.71.175.99 | 40.71.175.99/32 | Internet | NA |
| 0.0.0.0 | 0.0.0.0/0 | Virtueel apparaat | 10.0.2.4 |

De configuratie van de route tabel volt ooien:

1. Wijs de route tabel die u hebt gemaakt aan uw HDInsight-subnet toe door **subnetten** onder **instellingen** te selecteren.

1. Selecteer **+ koppelen**.

1. Selecteer in het scherm **subnet koppelen** het virtuele netwerk waarin het cluster is gemaakt. En het **subnet** dat u hebt gebruikt voor uw HDInsight-cluster.

1. Selecteer **OK**.

## <a name="edge-node-or-custom-application-traffic"></a>Edge-knoop punt of aangepast toepassings verkeer

Met de bovenstaande stappen kan het cluster zonder problemen worden uitgevoerd. U moet nog steeds afhankelijkheden configureren voor uw aangepaste toepassingen die worden uitgevoerd op de rand knooppunten, indien van toepassing.

Toepassings afhankelijkheden moeten worden geïdentificeerd en aan de Azure Firewall of de route tabel worden toegevoegd.

Routes moeten worden gemaakt voor het toepassings verkeer om asymmetrische routerings problemen te voor komen.

Als uw toepassingen andere afhankelijkheden hebben, moeten ze worden toegevoegd aan uw Azure Firewall. Maak toepassings regels om HTTP/HTTPS-verkeer en netwerk regels voor alle andere toe te staan.

## <a name="logging-and-scale"></a>Logboek registratie en-schaal

Azure Firewall kunt logboeken naar een aantal verschillende opslag systemen verzenden. Volg de stappen in de [zelf studie: Monitor Azure firewall logboeken en metrische gegevens](../firewall/firewall-diagnostics.md)voor instructies over het configureren van logboek registratie voor uw firewall.

Als u de logboek registratie hebt voltooid, kunt u, als u Log Analytics gebruikt, het geblokkeerde verkeer bekijken met een query zoals:

```Kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Het integreren van Azure Firewall met Azure Monitor-Logboeken is handig wanneer u voor het eerst een toepassing werkt. Met name wanneer u niet op de hoogte bent van alle toepassings afhankelijkheden. U kunt meer informatie over Azure Monitor logboeken van het [analyseren van logboek gegevens in azure monitor](../azure-monitor/logs/log-query-overview.md)

Zie [Dit](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits) document of Raadpleeg de [Veelgestelde vragen](../firewall/firewall-faq.yml)voor meer informatie over de schaal limieten van Azure firewall en het aanvragen van verg Roten.

## <a name="access-to-the-cluster"></a>Toegang tot het cluster

Nadat de firewall is ingesteld, kunt u het interne eind punt () gebruiken `https://CLUSTERNAME-int.azurehdinsight.net` om toegang te krijgen tot de Ambari vanuit het virtuele netwerk.

Als u het open bare eind punt ( `https://CLUSTERNAME.azurehdinsight.net` ) of SSH-eind punt () wilt gebruiken `CLUSTERNAME-ssh.azurehdinsight.net` , moet u ervoor zorgen dat u de juiste routes in de tabel route en NSG hebt om te voor komen dat het asymmetrische routerings probleem [hier](../firewall/integrate-lb.md)wordt uitgelegd. In dit geval moet u het IP-adres van de client in de regels voor binnenkomende NSG toestaan en dit ook toevoegen aan de door de gebruiker gedefinieerde route tabel waarbij de volgende hop is ingesteld als `internet` . Als de route ring niet op de juiste wijze is ingesteld, ziet u een time-outfout.

## <a name="next-steps"></a>Volgende stappen

* [Azure HDInsight Virtual Network-architectuur](hdinsight-virtual-network-architecture.md)
* [Virtueel netwerkapparaat configureren](./network-virtual-appliance.md)
