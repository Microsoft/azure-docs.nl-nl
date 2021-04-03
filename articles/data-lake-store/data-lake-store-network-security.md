---
title: Netwerkbeveiliging in Azure Data Lake Storage Gen1 | Microsoft Docs
description: Begrijpen hoe de integratie van virtueel netwerk werkt in Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/09/2018
ms.author: elsung
ms.openlocfilehash: 716e3766fdd7c1999efa12456346862a9902d7a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92108708"
---
# <a name="virtual-network-integration-for-azure-data-lake-storage-gen1"></a>Integratie van virtuele netwerken voor Azure Data Lake Storage Gen1

In dit artikel komt u meer te weten over de integratie van virtuele netwerken in Azure Data Lake Storage Gen1. Bij het integreren van virtuele netwerken kunt u uw accounts zodanig configureren dat alleen verkeer van specifieke virtuele netwerken en subnetten wordt geaccepteerd. 

Deze functie helpt bij het beveiligen van uw Data Lake Storage-account tegen externe bedreigingen.

Bij integratie van virtuele netwerken in Data Lake Storage Gen1 wordt gebruikgemaakt van de service-eindpuntbeveiliging tussen de virtuele netwerken en Azure Active Directory om extra beveiligingsclaims te genereren in het toegangstoken. Deze claims worden vervolgens gebruikt om het virtuele netwerk te verifiëren bij het Data Lake Storage Gen1-account en toegang toe te staan.

> [!NOTE]
> Er zijn geen extra kosten gemoeid met het gebruik van deze mogelijkheden. Er worden tegen het standaardtarief van Data Lake Storage Gen1 kosten doorberekend aan uw account. Ga voor meer informatie naar het overzicht van [prijzen](https://azure.microsoft.com/pricing/details/data-lake-store/?cdn=disable). Voor alle andere Azure-services die u gebruikt, gaat u naar [Prijzen](https://azure.microsoft.com/pricing/#product-picker).

## <a name="scenarios-for-virtual-network-integration-for-data-lake-storage-gen1"></a>Scenario's met integratie van virtuele netwerken voor Data Lake Storage Gen1

Middels de integratie van virtuele netwerken in Data Lake Storage Gen1 kunt u de toegang tot uw Data Lake Storage Gen1-account beperken vanaf specifieke virtuele netwerken en subnetten. Wanneer uw account is vergrendeld voor het opgegeven subnet van een virtueel netwerk, krijgen andere virtuele netwerken/VM's uit Azure geen toegang. Functioneel biedt de integratie van virtuele netwerken in Data Lake Storage Gen1 hetzelfde scenario als [service-eindpunten voor virtuele netwerken](../virtual-network/virtual-network-service-endpoints-overview.md). In de volgende secties worden een paar belangrijke verschillen gedetailleerd besproken. 

![Scenariodiagram over de integratie van virtuele netwerken in Data Lake Storage Gen1](media/data-lake-store-network-security/scenario-diagram.png)

> [!NOTE]
> De bestaande IP-firewallregels kunnen worden gebruikt naast de regels voor virtuele netwerken, om ook toegang vanaf on-premises netwerken toe te staan. 

## <a name="optimal-routing-with-data-lake-storage-gen1-virtual-network-integration"></a>Optimale routering met virtuele-netwerkintegratie in Data Lake Storage Gen1

Een belangrijk voordeel van service-eindpunten voor virtuele netwerken is [optimale routering](../virtual-network/virtual-network-service-endpoints-overview.md#key-benefits) vanuit het virtuele netwerk. U kunt dezelfde optimale routering gebruiken voor Data Lake Storage Gen1-accounts. Gebruik de volgende [door gebruikers gedefinieerde routes](../virtual-network/virtual-networks-udr-overview.md#user-defined) uit uw virtuele netwerk voor uw Data Lake Storage Gen1-account.

**Openbaar IP-adres van Data Lake Storage**: gebruik het openbare IP-adres voor de Data Lake Storage Gen1-doelaccounts. U kunt de IP-adressen voor het Data Lake Storage Gen1-account identificeren door [de DNS-namen van de accounts](./data-lake-store-connectivity-from-vnets.md#enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity) om te zetten. Maak een afzonderlijke vermelding voor elk adres.

```azurecli
# Create a route table for your resource group.
az network route-table create --resource-group $RgName --name $RouteTableName

# Create route table rules for Data Lake Storage public IP addresses.
# There's one rule per Data Lake Storage public IP address. 
az network route-table route create --name toADLSregion1 --resource-group $RgName --route-table-name $RouteTableName --address-prefix <ADLS Public IP Address> --next-hop-type Internet

# Update the virtual network, and apply the newly created route table to it.
az network vnet subnet update --vnet-name $VnetName --name $SubnetName --resource-group $RgName --route-table $RouteTableName
```

## <a name="data-exfiltration-from-the-customer-virtual-network"></a>Gegevensoverdracht vanuit het virtuele netwerk van de klant

Naast het beveiligen van de toegang tot Data Lake Storage-accounts voor toegang vanuit het virtuele netwerk, wilt u wellicht ook voorkomen dat gegevens worden overgedragen naar een niet-gemachtigd account.

Gebruik hiervoor een firewalloplossing in uw virtuele netwerk voor het filteren van uitgaand verkeer op basis van de URL van het doelaccount. Verleen alleen toegang aan goedgekeurde Data Lake Storage Gen1-accounts.

Een aantal beschikbare opties zijn:
- [Azure Firewall](../firewall/overview.md): [implementeer en configureer een Azure-firewall](../firewall/tutorial-firewall-deploy-portal.md) voor uw virtuele netwerk. Beveilig het uitgaande Data Lake Storage-verkeer en beperk het tot de bekende en goedgekeurde account-URL.
- [Firewall voor het virtuele netwerkapparaat](https://azure.microsoft.com/solutions/network-appliances/): de beheerder staat mogelijk alleen het gebruik van bepaalde commerciële firewalls toe. Gebruik een firewalloplossing voor virtuele-netwerkapparaten die beschikbaar is in Azure Marketplace en waarmee dezelfde functie kan worden vervuld.

> [!NOTE]
> Door firewalls te gebruiken in het gegevenspad wordt er een extra sprong in het gegevenspad gecreëerd. Dit kan van invloed zijn op de netwerkprestaties voor end-to-endgegevensuitwisseling. De doorvoerbeschikbaarheid en verbindingslatentie kunnen worden beïnvloed. 

## <a name="limitations"></a>Beperkingen

- HDInsight-clusters die zijn gemaakt vóórdat ondersteuning van Data Lake Storage Gen1-virtuele-netwerkintegratie beschikbaar was, moeten opnieuw worden gemaakt zodat er ondersteuning wordt geboden voor de nieuwe functie.
 
- Wanneer u een nieuw HDInsight-cluster maakt en u een Data Lake Storage Gen1-account selecteert waarvoor virtuele-netwerkintegratie is ingeschakeld, mislukt het proces. Schakel om te beginnen de regel voor het virtuele netwerk uit. U kunt ook op de blade **Firewall en virtuele netwerken** van het Data Lake Storage-account de optie **Toegang vanuit alle netwerken en services toestaan** selecteren. Maak vervolgens het HDInsight-cluster voordat u de regel voor het virtuele netwerk opnieuw inschakelt of **Toegang uit alle netwerken en services** deselecteert. Raadpleeg de sectie [Uitzonderingen](#exceptions) voor meer informatie.

- De van virtuele-netwerkintegratie voor Data Lake Storage Gen1 werkt niet in combinatie met [beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md).
  
- Bestands- en mapgegevens in uw Data Lake Storage Gen1-account met virtuele-netwerkfunctionaliteit zijn niet toegankelijk vanuit de portal. Deze beperking omvat toegang vanuit een VM in het virtuele netwerk en activiteiten zoals het gebruik van Data Explorer. Accountbeheeractiviteiten blijven werken. Bestands- en mapgegevens in uw Data Lake Storage-account met virtuele-netwerkfunctionaliteit zijn toegankelijk via alle niet-portalresources. Deze resources omvatten bijvoorbeeld SDK-toegang, PowerShell-scripts en andere Azure-services als deze niet afkomstig zijn uit de portal. 

## <a name="configuration"></a>Configuratie

### <a name="step-1-configure-your-virtual-network-to-use-an-azure-ad-service-endpoint"></a>Stap 1: uw virtuele netwerk configureren voor het gebruik van een Azure AD-service-eindpunt

1.  Ga naar de Azure-portal en meld u aan bij uw account.
 
2.  [Maak een nieuw virtueel netwerk](../virtual-network/quick-create-portal.md) in uw abonnement. U kunt ook naar een bestaand virtueel netwerk gaan. Het virtuele netwerk moet zich in dezelfde regio bevinden als het Data Lake Store Gen 1-account.
 
3.  Kies op de blade **Virtueel netwerk** de optie **Service-eindpunten**.
 
4.  Selecteer **Toevoegen** om een nieuw service-eindpunt toe te voegen.

    ![Een service-eindpunt voor een virtueel netwerk toevoegen](media/data-lake-store-network-security/config-vnet-1.png)

5.  Selecteer **Microsoft.AzureActiveDirectory** als service voor het eindpunt.

     ![Het service-eindpunt Microsoft.AzureActiveDirectory selecteren](media/data-lake-store-network-security/config-vnet-2.png)

6.  Selecteer de subnetten waarvoor u connectiviteit wilt toestaan. Selecteer **Toevoegen**.

    ![Het subnet selecteren](media/data-lake-store-network-security/config-vnet-3.png)

7.  Het duurt maximaal 15 minuten voordat het service-eindpunt is toegevoegd. Na het toevoegen wordt het in de lijst weergegeven. Controleer of het inderdaad wordt weergegeven en of alle configuratiedetails kloppen.
 
    ![Geslaagde toevoeging van het service-eindpunt](media/data-lake-store-network-security/config-vnet-4.png)

### <a name="step-2-set-up-the-allowed-virtual-network-or-subnet-for-your-data-lake-storage-gen1-account"></a>Stap 2: het toegestane virtuele netwerk of subnet instellen voor uw Data Lake Storage Gen1-account

1.  Wanneer u uw virtuele netwerk hebt geconfigureerd, [maakt u een nieuw account voor Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account) in uw abonnement. U kunt ook naar een bestaand Data Lake Storage Gen1-account gaan. Het Data Lake Storage Gen1-account moet zich in dezelfde regio bevinden als het virtuele netwerk.
 
2.  Selecteer **Firewall en virtuele netwerken**.

    > [!NOTE]
    > Als u **Firewall en virtuele netwerken** niet ziet in de instellingen, meldt u zich af bij de portal. Sluit de browser en wis de browsercache. Start de computer opnieuw op en probeer het nog een keer.

       ![Een regel voor virtuele netwerken toevoegen aan uw Data Lake Storage-account](media/data-lake-store-network-security/config-adls-1.png)

3.  Selecteer **Geselecteerde netwerken**.
 
4.  Selecteer **bestaand virtueel netwerk toevoegen**.

    ![Bestaand virtueel netwerk toevoegen](media/data-lake-store-network-security/config-adls-2.png)

5.  Selecteer de virtuele netwerken en subnetten om connectiviteit mogelijk te maken. Selecteer **Toevoegen**.

    ![Het virtuele netwerk en de subnetten kiezen](media/data-lake-store-network-security/config-adls-3.png)

6.  Zorg ervoor dat de virtuele netwerken en subnetten juist worden weergegeven in de lijst. Selecteer **Opslaan**.

    ![De nieuwe regel opslaan](media/data-lake-store-network-security/config-adls-4.png)

    > [!NOTE]
    > Na het opslaan duurt het maximaal 5 minuten voordat de instellingen zijn doorgevoerd.

7.  [optioneel] Op de pagina **Firewall en virtuele netwerken**, in het gedeelte **Firewall**, kunt u connectiviteit vanaf specifieke IP-adressen toestaan. 

## <a name="exceptions"></a>Uitzonderingen
U kunt connectiviteit vanuit Azure-services en VM's buiten uw geselecteerde virtuele netwerken inschakelen. Op de blade **Firewall en virtuele netwerken** kunt u in het gedeelte **Uitzonderingen** kiezen uit twee opties:
 
- **Verleen alle Azure-services toegang tot dit Data Lake Storage Gen1-account**. Middels deze optie kunnen Azure-services zoals Azure Data Factory, Azure Event Hubs en alle virtuele machines van Azure communiceren met uw Data Lake Storage-account.

- **Verleen Azure Data Lake Analytics toegang tot dit Data Lake Storage Gen1-account**. Met deze optie wordt Data Lake Analytics-connectiviteit met dit Data Lake Storage-account mogelijk. 

  ![Uitzonderingen voor de firewall en virtuele netwerken](media/data-lake-store-network-security/firewall-exceptions.png)

Het wordt aangeraden om deze uitzonderingen uitgeschakeld te houden. Schakel ze alleen in als u connectiviteit nodig hebt via andere services buiten uw virtuele netwerk.