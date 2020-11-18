---
title: Clusters voor Active Directory-integratie configureren
titleSuffix: Azure HDInsight
description: Meer informatie over het instellen en configureren van een HDInsight-cluster dat is geïntegreerd met Active Directory met behulp van Azure Active Directory Domain Services en de Enterprise Security Package functie.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seodec18,seoapr2020, contperfq2
ms.date: 10/30/2020
ms.openlocfilehash: 4c0d12e4c37476b9ae71962251105ef92aa39120
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94845200"
---
# <a name="configure-hdinsight-clusters-for-active-directory-integration-with-enterprise-security-package"></a>HDInsight-clusters configureren voor integratie met Active Directory met Enterprise Security Package

In dit artikel leert u hoe u een HDInsight-cluster kunt maken en configureren dat is geïntegreerd met Active Directory met behulp van een functie met de naam Enterprise Security Package (ESP), Azure Active Directory Domain Services (Azure AD DS) en uw bestaande on-premises Active Directory.

Zie [Enterprise Security Package clusters maken en configureren in azure HDInsight](apache-domain-joined-create-configure-enterprise-security-cluster.md)voor een zelf studie over het instellen en configureren van een domein in Azure en het maken van een ESP-cluster.

## <a name="background"></a>Achtergrond

Enterprise Security Package (ESP) biedt Active Directory-integratie voor Azure HDInsight. Met deze integratie kunnen domein gebruikers hun domein referenties gebruiken om te verifiëren met HDInsight-clusters en big data-taken uit te voeren.

> [!NOTE]  
> ESP is algemeen beschikbaar in HDInsight 3,6 en 4,0 voor deze cluster typen: Apache Spark, Interactive, Hadoop en HBase. ESP voor het Apache Kafka-cluster type wordt in Preview weer gegeven met de best mogelijke ondersteuning. ESP-clusters die zijn gemaakt vóór de ESP-GA-datum (1 oktober 2018) worden niet ondersteund.

## <a name="prerequisites"></a>Vereisten

Er zijn enkele vereisten om te volt ooien voordat u een HDInsight-cluster kunt maken dat geschikt is voor ESP:

- Schakel Azure AD-DS in.
- Controleer de status van Azure AD-DS om te controleren of de synchronisatie is voltooid.
- Een beheerde identiteit maken en autoriseren.
- Voltooi de netwerk installatie voor DNS en gerelateerde problemen.

Elk van deze items wordt hieronder nader besproken.

### <a name="enable-azure-ad-ds"></a>Azure AD DS inschakelen

Het inschakelen van Azure AD DS is een vereiste voordat u een HDInsight-cluster met ESP kunt maken. Zie [Azure Active Directory Domain Services inschakelen met behulp van de Azure Portal](../../active-directory-domain-services/tutorial-create-instance.md)voor meer informatie.

Als Azure AD DS is ingeschakeld, beginnen alle gebruikers en objecten standaard met het synchroniseren van Azure Active Directory (Azure AD) naar Azure AD DS. De lengte van de synchronisatie bewerking is afhankelijk van het aantal objecten in azure AD. De synchronisatie kan enkele dagen duren voor honderd duizenden objecten.

De domein naam die u met Azure AD DS gebruikt, moet 39 tekens of minder zijn om met HDInsight te kunnen werken.

U kunt ervoor kiezen om alleen de groepen te synchroniseren die toegang nodig hebben tot de HDInsight-clusters. Deze optie om alleen bepaalde groepen te synchroniseren, wordt *scoped Synchronization* genoemd. Zie voor instructies [scoped Synchronization from Azure AD configureren voor uw beheerde domein](../../active-directory-domain-services/scoped-synchronization.md).

Wanneer u beveiligd LDAP inschakelt, plaatst u de domein naam in de onderwerpnaam. En de alternatieve naam voor het onderwerp in het certificaat. Als uw domein naam *contoso100.onmicrosoft.com* is, moet u ervoor zorgen dat de exacte naam bestaat in de onderwerpnaam van het certificaat en de alternatieve naam voor het onderwerp. Zie [Configure secure LDAP for a Azure AD DS Managed Domain](../../active-directory-domain-services/tutorial-configure-ldaps.md)(Engelstalig) voor meer informatie.

In het volgende voor beeld wordt een zelfondertekend certificaat gemaakt. De domein naam *contoso100.onmicrosoft.com* bevindt zich in beide `Subject` (object naam) en `DnsName` (alternatieve naam voor onderwerp).

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

> [!NOTE]  
> Alleen Tenant beheerders hebben de bevoegdheid om Azure AD DS in te scha kelen. Als de cluster opslag is Azure Data Lake Storage Gen1 of Gen2, moet u Azure AD-Multi-Factor Authentication alleen uitschakelen voor gebruikers die toegang moeten hebben tot het cluster met behulp van basis-Kerberos-authenticatie.
>
> U kunt met behulp van [vertrouwde IP-adressen](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) of [voorwaardelijke toegang](../../active-directory/conditional-access/overview.md) multi-factor Authentication uitschakelen voor *specifieke gebruikers wanneer* ze toegang hebben tot het IP-bereik voor het virtuele netwerk van het HDInsight-cluster. Als u voorwaardelijke toegang gebruikt, zorgt u ervoor dat het eind punt van de Active Directory service in ingeschakeld is op het virtuele HDInsight-netwerk.
>
> Als de cluster opslag Azure Blob-opslag is, moet u Multi-Factor Authentication niet uitschakelen.

### <a name="check-azure-ad-ds-health-status"></a>De status van Azure AD DS controleren

Bekijk de status van Azure Active Directory Domain Services door de **status** te selecteren in de categorie **beheren** . Zorg ervoor dat de status van de Azure-AD DS groen is (wordt uitgevoerd) en de synchronisatie is voltooid.

![Status van Azure AD DS](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

### <a name="create-and-authorize-a-managed-identity"></a>Een beheerde identiteit maken en autoriseren

Gebruik een door de *gebruiker toegewezen beheerde identiteit* voor het vereenvoudigen van beveiligde Domain Services-bewerkingen. Wanneer u de functie voor het beheren van de **HDInsight Domain Services** -rol toewijst aan de beheerde identiteit, kan deze Domain Services-bewerkingen lezen, maken, wijzigen en verwijderen.

Bepaalde Domain Services-bewerkingen, zoals het maken van organisatie-eenheden en service-principals, zijn vereist voor HDInsight-Enterprise Security Package. U kunt in elk abonnement beheerde identiteiten maken. Zie [beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over beheerde identiteiten in het algemeen. Zie [beheerde identiteiten in azure hdinsight](../hdinsight-managed-identities.md)voor meer informatie over de werking van beheerde identiteiten in azure hdinsight.

Als u ESP-clusters wilt instellen, moet u een door de gebruiker toegewezen beheerde identiteit maken als u er nog geen hebt. Zie [`Create, list, delete, or assign a role to a user-assigned managed identity by using the Azure portal`](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

Wijs vervolgens de rol van **HDInsight Domain Services-Inzender** toe aan de beheerde identiteit in **toegangs beheer** voor Azure AD DS. U hebt Azure AD DS-beheerders bevoegdheden nodig om deze roltoewijzing te kunnen maken.

![Toegangs beheer Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Als u de functie voor het toewijzen van de **HDInsight Domain Services-Inzender** hebt, zorgt u ervoor dat deze identiteit `on behalf of` over de juiste () toegang beschikt voor de bewerkingen van domein Services op het Azure AD DS domein. Deze bewerkingen zijn onder andere het maken en verwijderen van organisatie-eenheden.

Wanneer de beheerde identiteit de rol krijgt, beheert de Azure AD DS-beheerder wie deze gebruikt. Eerst selecteert de beheerder de beheerde identiteit in de portal. Selecteert vervolgens **Access Control (IAM)** onder **overzicht**. De beheerder wijst de rol **Managed Identity-operator** toe aan gebruikers of groepen die ESP-clusters willen maken.

De Azure AD DS-beheerder kan bijvoorbeeld deze rol toewijzen aan de groep **MarketingTeam** voor de beheerde **sjmsi** -identiteit. Er wordt een voor beeld weer gegeven in de volgende afbeelding. Deze toewijzing zorgt ervoor dat de juiste personen in de organisatie de beheerde identiteit kunnen gebruiken om ESP-clusters te maken.

![Roltoewijzing beheerde identiteits operator voor HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

### <a name="network-configuration"></a>Netwerkconfiguratie

> [!NOTE]  
> Azure AD DS moet worden geïmplementeerd in een op Azure Resource Manager gebaseerd virtueel netwerk. Klassieke virtuele netwerken worden niet ondersteund voor Azure-AD DS. Zie [Azure Active Directory Domain Services inschakelen met behulp van de Azure Portal](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network)voor meer informatie.

Schakel Azure AD DS in. Vervolgens wordt een lokale Domain Name System (DNS)-server uitgevoerd op de Active Directory virtuele machines (Vm's). Configureer uw Azure AD DS virtuele netwerk voor het gebruik van deze aangepaste DNS-servers. Als u de juiste IP-adressen wilt vinden, selecteert u **Eigenschappen** in de categorie **beheren** en kijkt u onder **IP-adres op virtueel netwerk**.

![IP-adressen voor lokale DNS-servers zoeken](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

Wijzig de configuratie van de DNS-servers in het virtuele netwerk van Azure AD DS. Als u deze aangepaste Ip's wilt gebruiken, selecteert u **DNS-servers** in de categorie **instellingen** . Vervolgens selecteert u de optie **aangepast** , voert u het eerste IP-adres in het tekstvak in en selecteert u **Opslaan**. Voeg meer IP-adressen toe aan de hand van dezelfde stappen.

![De DNS-configuratie van het virtuele netwerk bijwerken](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

Het is eenvoudiger om zowel het Azure AD DS-exemplaar als het HDInsight-cluster in hetzelfde virtuele Azure-netwerk te plaatsen. Als u andere virtuele netwerken wilt gebruiken, moet u deze virtuele netwerken koppelen zodat de domein controller zichtbaar is voor HDInsight Vm's. Zie [peering van virtuele netwerken](../../virtual-network/virtual-network-peering-overview.md)voor meer informatie.

Nadat de virtuele netwerken zijn gekoppeld, configureert u het virtuele HDInsight-netwerk voor het gebruik van een aangepaste DNS-server. En voer de Azure AD DS privé Ip's in als de DNS-server adressen. Als beide virtuele netwerken dezelfde DNS-servers gebruiken, wordt uw aangepaste domein naam omgezet in het juiste IP-adres en is deze bereikbaar vanaf HDInsight. Als uw domein naam bijvoorbeeld is `contoso.com` , moet na deze stap `ping contoso.com` worden omgezet naar het juiste Azure AD DS IP-adres.

![Aangepaste DNS-servers configureren voor een gekoppeld virtueel netwerk](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Als u regels voor netwerk beveiligings groepen (NSG) in uw HDInsight-subnet gebruikt, moet u de [vereiste ip's](../hdinsight-management-ip-addresses.md) toestaan voor zowel binnenkomend als uitgaand verkeer.

Als u de netwerk installatie wilt testen, voegt u een virtuele Windows-machine toe aan het HDInsight-netwerk/subnet en pingt u de domein naam. (Het moet worden omgezet in een IP-adres.) Voer **ldp.exe** uit voor toegang tot het Azure AD DS-domein. Voeg deze Windows-VM vervolgens toe aan het domein om te bevestigen dat alle vereiste RPC-aanroepen tussen de client en de server zijn geslaagd.

Gebruik **nslookup** om de netwerk toegang tot uw opslag account te bevestigen. Of een externe data base die u mogelijk gebruikt (bijvoorbeeld externe Hive-metastore of zwerver DB). Zorg ervoor dat de [vereiste poorten](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) zijn toegestaan in de NSG-regels van het Azure AD DS-subnet als een NSG Azure AD DS beveiligt. Als het domein dat lid is van deze Windows-VM is voltooid, kunt u door gaan met de volgende stap en ESP-clusters maken.

## <a name="create-an-hdinsight-cluster-with-esp"></a>Een HDInsight-cluster maken met ESP

Nadat u de vorige stappen op de juiste manier hebt ingesteld, is de volgende stap het maken van het HDInsight-cluster met ESP ingeschakeld. Wanneer u een HDInsight-cluster maakt, kunt u Enterprise Security Package inschakelen op het tabblad **beveiliging en netwerk** . Gebruik de portal-ervaring voor een Azure Resource Manager sjabloon voor implementatie. Down load vervolgens de vooraf opgevulde sjabloon op de pagina **beoordeling + maken** voor toekomstig gebruik.

U kunt de functie [HDINSIGHT id Broker](identity-broker.md) ook inschakelen tijdens het maken van het cluster. Met de functie ID Broker kunt u zich aanmelden bij Ambari met behulp van Multi-Factor Authentication en de vereiste Kerberos-tickets ophalen zonder dat wacht woord-hashes nodig zijn in azure AD DS.

> [!NOTE]  
> De eerste zes tekens van de ESP-cluster namen moeten uniek zijn in uw omgeving. Als u bijvoorbeeld meerdere ESP-clusters in verschillende virtuele netwerken hebt, kiest u een naam Conventie die ervoor zorgt dat de eerste zes tekens op de cluster namen uniek zijn.

![Domein validatie voor Azure HDInsight Enterprise Security Package](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

Nadat u ESP hebt ingeschakeld, worden veelvoorkomende onjuiste configuratie-items die betrekking hebben op Azure AD DS automatisch gedetecteerd en gevalideerd. Nadat u deze fouten hebt opgelost, kunt u door gaan met de volgende stap.

![Domein validatie van Azure HDInsight Enterprise Security Package is mislukt](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

Wanneer u een HDInsight-cluster met ESP maakt, moet u de volgende para meters opgeven:

* **Gebruiker van Cluster beheerder**: Kies een beheerder voor uw cluster uit uw gesynchroniseerde Azure AD DS-exemplaar. Dit domein account moet al worden gesynchroniseerd en beschikbaar zijn in azure AD DS.

* **Cluster toegangs groepen**: de beveiligings groepen waarvan u de gebruikers wilt synchroniseren en die toegang hebben tot het cluster, moeten beschikbaar zijn in azure AD DS. Een voor beeld is de groep HiveUsers. Zie [een groep maken en leden toevoegen in azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)voor meer informatie.

* **URL van LDAPS**: een voor beeld is `ldaps://contoso.com:636` .

De beheerde identiteit die u hebt gemaakt, kan worden gekozen in de vervolg keuzelijst door de **gebruiker toegewezen beheerde identiteit** wanneer u een nieuw cluster maakt.

![Beheerde identiteit van Azure HDInsight ESP Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>Volgende stappen

* Zie [configure Apache Hive policies for HDInsight clusters with ESP](apache-domain-joined-run-hive.md)(Engelstalig) voor het configureren van Hive-beleid en het uitvoeren van Hive-query's.
* Zie [SSH gebruiken met Apache Hadoop op basis van Linux in hdinsight via Linux, UNIX of OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight)voor het gebruik van SSH om verbinding te maken met hdinsight-clusters met ESP.
