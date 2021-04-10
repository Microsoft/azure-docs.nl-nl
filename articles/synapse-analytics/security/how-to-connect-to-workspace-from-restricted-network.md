---
title: Verbinding maken met werkruimte bronnen in azure Synapse Analytics Studio vanuit een beperkt netwerk
description: In dit artikel leert u hoe u verbinding kunt maken met uw werkruimte bronnen vanuit een beperkt netwerk
author: xujxu
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 10/25/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: de7c5dba5a4868b7a8fdb390f974134cfaef7395
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100384517"
---
# <a name="connect-to-workspace-resources-from-a-restricted-network"></a>Verbinding maken met werkruimte bronnen vanuit een beperkt netwerk

Stel dat u een IT-beheerder bent die het beperkte netwerk van uw organisatie beheert. U wilt de netwerk verbinding inschakelen tussen Azure Synapse Analytics Studio en een werk station binnen dit beperkte netwerk. In dit artikel wordt beschreven hoe u dit kunt doen.

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**: als u nog geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* **Azure Synapse Analytics-werk ruimte**: u kunt er een maken op basis van Azure Synapse Analytics. U hebt de naam van de werk ruimte nodig in stap 4.
* **Een beperkt netwerk**: de IT-beheerder onderhoudt het beperkte netwerk voor de organisatie en heeft toestemming voor het configureren van het netwerk beleid. U hebt de naam van het virtuele netwerk en het bijbehorende subnet nodig in stap 3.


## <a name="step-1-add-network-outbound-security-rules-to-the-restricted-network"></a>Stap 1: uitgaande beveiligings regels voor het netwerk toevoegen aan het beperkte netwerk

U moet vier regels voor uitgaande netwerk beveiliging toevoegen met vier service tags. 
* AzureResourceManager
* AzureFrontDoor. front-end
* AzureActiveDirectory
* AzureMonitor (dit type regel is optioneel. Voeg deze alleen toe als u de gegevens met micro soft wilt delen.)

De volgende scherm afbeelding toont Details voor de uitgaande regel Azure Resource Manager.

![Scherm opname van Azure Resource Manager servicetag Details.](./media/how-to-connect-to-workspace-from-restricted-network/arm-servicetag.png)

Wanneer u de andere drie regels maakt, vervangt u de waarde van de **doel service-tag** door **AzureFrontDoor.** front-end, **AzureActiveDirectory** of **AzureMonitor** uit de lijst.

Zie [overzicht van service Tags](../../virtual-network/service-tags-overview.md)voor meer informatie.

## <a name="step-2-create-private-link-hubs"></a>Stap 2: Maak een persoonlijke koppelings hubs

Maak vervolgens de persoonlijke koppelings hubs vanuit het Azure Portal. Als u dit wilt vinden in de portal, zoekt u naar *Azure Synapse Analytics (persoonlijke koppelingen hubs)* en vult u vervolgens de vereiste gegevens in om deze te maken. 

![Scherm opname van de Synapse private link hub.](./media/how-to-connect-to-workspace-from-restricted-network/private-links.png)

## <a name="step-3-create-a-private-endpoint-for-your-synapse-studio"></a>Stap 3: Maak een persoonlijk eind punt voor uw Synapse Studio

Voor toegang tot Azure Synapse Analytics Studio moet u een persoonlijk eind punt maken op basis van de Azure Portal. Als u dit wilt vinden in de portal, zoekt u naar een *privé-koppeling*. Selecteer in het **persoonlijke koppelings centrum** **persoonlijke eind punt maken** en vul vervolgens de vereiste gegevens in om deze te maken. 

> [!Note]
> Zorg ervoor dat de waarde **regio** gelijk is aan die van uw Azure Synapse Analytics-werk ruimte.

![Scherm afbeelding van het tabblad basis beginselen van een persoonlijk eind punt maken.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-1.png)

Kies op het tabblad **resource** de hub private link, die u in stap 2 hebt gemaakt.

![Scherm afbeelding van het tabblad Resource maken van een persoonlijk eind punt.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-2.png)

Op het tabblad **configuratie** : 
* Voor **virtueel netwerk** selecteert u de beperkte naam van het virtuele netwerk.
* Selecteer bij **subnet** het subnet van het beperkte virtuele netwerk. 
* Selecteer **Ja** als u wilt **integreren met een privé-DNS-zone**.

![Scherm opname van een persoonlijk eind punt maken, tabblad Configuratie.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-3.png)

Nadat het eind punt van de persoonlijke koppeling is gemaakt, kunt u toegang krijgen tot de aanmeldings pagina van de web tool voor Azure Synapse Analytics Studio. U hebt echter nog geen toegang tot de resources in uw werk ruimte. Hiervoor moet u de volgende stap volt ooien.

## <a name="step-4-create-private-endpoints-for-your-workspace-resource"></a>Stap 4: privé-eind punten maken voor uw werkruimte resource

Als u toegang wilt krijgen tot de resources in uw Azure Synapse Analytics Studio-werkruimte resource, moet u het volgende maken:

- Ten minste één persoonlijk eind punt met een **subresource** van het type **dev**.
- Twee andere optionele persoonlijke koppelings eindpunten met typen **SQL** of **SqlOnDemand**, afhankelijk van de resources in de werk ruimte die u wilt gebruiken.

Het maken van deze in de vorige stap is vergelijkbaar met hoe u het eind punt maakt.  

Op het tabblad **resource** :

* Selecteer voor **resource type** **micro soft. Synapse/Workspaces**.
* Voor **resource** selecteert u de naam van de werk ruimte die u eerder hebt gemaakt.
* Selecteer bij **doel-subresource** het eindpunt type:
  * **SQL** is voor het uitvoeren van SQL-query's in de SQL-groep.
  * **SqlOnDemand** is voor de uitvoering van SQL ingebouwde query's.
  * **Dev** is voor het openen van alle andere gegevens binnen Azure Synapse Analytics studio-werk ruimten. U moet ten minste één persoonlijk eind punt van dit type maken.

![Scherm afbeelding van een persoonlijk eind punt maken, tabblad Resource, werk ruimte.](./media/how-to-connect-to-workspace-from-restricted-network/plinks-endpoint-ws-1.png)


## <a name="step-5-create-private-endpoints-for-workspace-linked-storage"></a>Stap 5: persoonlijke eind punten maken voor werkruimte gekoppelde opslag

Als u toegang wilt krijgen tot de gekoppelde opslag met de opslag Verkenner in de Azure Synapse Analytics studio-werk ruimte, moet u één persoonlijk eind punt maken. De stappen hiervoor zijn vergelijkbaar met die van stap 3. 

Op het tabblad **resource** :
* Selecteer voor **resource type** **micro soft. Synapse/Storage accounts**.
* Voor **resource** selecteert u de naam van het opslag account dat u eerder hebt gemaakt.
* Selecteer bij **doel-subresource** het eindpunt type:
  * de **BLOB** is voor Azure Blob Storage.
  * **DFS** is voor Azure data Lake Storage Gen2.

![Scherm opname van een persoonlijk eind punt maken, het tabblad Resource, opslag.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-storage.png)

Nu kunt u toegang krijgen tot de gekoppelde opslag resource. In uw virtuele netwerk, in uw Azure Synapse Analytics studio-werk ruimte, kunt u de opslag Verkenner gebruiken om toegang te krijgen tot de gekoppelde opslag resource.

U kunt een beheerd virtueel netwerk inschakelen voor uw werk ruimte, zoals wordt weer gegeven in deze scherm afbeelding:

![Scherm opname van de Synapse-werk ruimte maken met de optie beheerde virtuele netwerken inschakelen gemarkeerd.](./media/how-to-connect-to-workspace-from-restricted-network/ws-network-config.png)

Als u wilt dat uw notebook toegang krijgt tot de gekoppelde opslag resources onder een bepaald opslag account, voegt u beheerde persoonlijke eind punten toe onder uw Azure Synapse Analytics Studio. De naam van het opslag account moet zijn dat uw notitie blok toegang moet hebben. Zie [een beheerd persoonlijk eind punt maken voor uw gegevens bron](./how-to-create-managed-private-endpoints.md)voor meer informatie.

Nadat u dit eind punt hebt gemaakt, wordt in de goedkeurings status de status **in behandeling** weer gegeven. Goed keuring aanvragen van de eigenaar van dit opslag account, op het tabblad verbindingen van het **particuliere eind punt** van dit opslag account in de Azure Portal. Nadat deze is goedgekeurd, heeft uw notebook toegang tot de gekoppelde opslag resources onder dit opslag account.

Nu is alle ingesteld. U hebt toegang tot uw Azure Synapse Analytics Studio-werkruimte resource.

## <a name="appendix-dns-registration-for-private-endpoint"></a>Bijlage: DNS-registratie voor persoonlijk eind punt

Als de ' integreren met particuliere DNS-zone ' niet is ingeschakeld tijdens het maken van het privé-eind punt, moet u voor elk van uw privé-eind punten de **privé-DNS zone** maken.
![Scherm opname van Synapse persoonlijke DNS-zone 1 maken.](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-1.png)

Zoek naar *privé-DNS zone* om de **privé-DNS zone** te vinden in de portal. Vul in de **zone privé-DNS** de vereiste gegevens hieronder in om deze te maken.

* Voer bij **naam** de speciale naam voor de persoonlijke DNS-zone in voor een specifiek persoonlijk eind punt, zoals hieronder wordt beschreven:
  * **`privatelink.azuresynapse.net`** is voor het persoonlijke eind punt van toegang tot de Azure Synapse Analytics Studio-gateway. Zie dit type privé-eind punt maken in stap 3.
  * **`privatelink.sql.azuresynapse.net`** is voor dit type privé-eind punt voor het uitvoeren van SQL-query's in de SQL-groep en de ingebouwde groep. Zie het maken van een eind punt in stap 4.
  * **`privatelink.dev.azuresynapse.net`** is voor dit type privé-eind punt van toegang tot alles in azure Synapse Analytics studio-werk ruimten. Zie dit type privé-eind punt maken in stap 4.
  * **`privatelink.dfs.core.windows.net`** is voor het persoonlijke eind punt van de toegang tot de gekoppelde Azure Data Lake Storage Gen2 van de werk ruimte. Zie dit type privé-eind punt maken in stap 5.
  * **`privatelink.blob.core.windows.net`** is voor het persoonlijke eind punt van de toegang tot de gekoppelde Azure-Blob Storage werkruimte. Zie dit type privé-eind punt maken in stap 5.

![Scherm opname van Synapse persoonlijke DNS-zone 2 maken.](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-2.png)

Nadat de **privé-DNS zone** is gemaakt, voert u de gemaakte persoonlijke DNS-zone in en selecteert u de koppelingen naar het **virtuele netwerk** om de koppeling toe te voegen aan uw virtuele netwerk. 

![Scherm opname van Synapse private DNS zone 3.](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-3.png)

Vul de verplichte velden in zoals hieronder:
* Voer bij **naam van koppeling** de naam van de koppeling in.
* Selecteer het virtuele netwerk voor het **virtuele netwerk**.

![Scherm opname van Synapse private DNS zone 4.](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-4.png)

Nadat de koppeling met het virtuele netwerk is toegevoegd, moet u de DNS-recordset toevoegen in de **privé-DNS zone** die u eerder hebt gemaakt.

* Voer bij **naam** de toegewezen naam teken reeksen in voor het verschillende persoonlijke eind punt: 
  * **Web** is voor het persoonlijke eind punt van toegang tot Azure Synapse Analytics Studio.
  * '***YourWorkSpaceName***' is voor het persoonlijke eind punt van SQL-query uitvoering in SQL-groep en ook voor het persoonlijke eind punt van toegang tot alles in azure Synapse Analytics studio-werk ruimten.
  * "***YourWorkSpaceName *-OnDemand**" is voor het persoonlijke eind punt van SQL-query uitvoering in de ingebouwde groep.
* Selecteer bij **type** alleen DNS-record type **A** . 
* Voer bij **IP-adres** het bijbehorende IP-adres van elk privé-eind punt in. U kunt het IP-adres in de **netwerk interface** ophalen uit het overzicht van uw persoonlijke eind punt.

![Scherm opname van Synapse private DNS zone 5.](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-5.png)


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [virtuele netwerk van de beheerde werk ruimte](./synapse-workspace-managed-vnet.md).

Meer informatie over [beheerde privé-eind punten](./synapse-workspace-managed-private-endpoints.md).
