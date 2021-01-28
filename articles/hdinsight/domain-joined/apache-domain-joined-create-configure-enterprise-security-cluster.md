---
title: Enterprise Security Package clusters maken, configureren-Azure
description: Meer informatie over het maken en configureren van Enterprise Security Package clusters in azure HDInsight
services: hdinsight
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/10/2019
ms.openlocfilehash: 914acfab3935bc81e7d8382163ca9283c7f71a53
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98934034"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Enterprise Security Package clusters maken en configureren in azure HDInsight

Enterprise Security Package (ESP) voor Azure HDInsight biedt u toegang tot op Active Directory gebaseerde verificatie, ondersteuning voor meerdere gebruikers en toegangs beheer op basis van rollen voor uw Apache Hadoop clusters in Azure. Met HDInsight ESP-clusters kunnen organisaties die voldoen aan het strikte beveiligings beleid van het bedrijf, gevoelige gegevens veilig verwerken.

In deze hand leiding wordt beschreven hoe u een Azure HDInsight-cluster met ESP maakt. Ook wordt beschreven hoe u een Windows IaaS-VM maakt waarop Active Directory en Domain Name System (DNS) zijn ingeschakeld. Gebruik deze hand leiding om de benodigde bronnen te configureren zodat lokale gebruikers zich kunnen aanmelden bij een HDInsight-cluster met ESP-functionaliteit.

De server die u maakt, fungeert als vervanging voor uw *eigen eigen* on-premises omgeving. U gebruikt deze voor de installatie-en configuratie stappen. Later herhaalt u de stappen in uw eigen omgeving.

Deze hand leiding helpt u ook bij het maken van een hybride identiteits omgeving met behulp van wacht woord-hash synchroniseren met Azure Active Directory (Azure AD). De gids is een aanvulling [op ESP in HDInsight](apache-domain-joined-architecture.md).

Voordat u dit proces in uw eigen omgeving gebruikt:

* Active Directory en DNS instellen.
* Schakel Azure AD in.
* On-premises gebruikers accounts synchroniseren met Azure AD.

![Diagram van Azure AD-architectuur](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-an-on-premises-environment"></a>Een on-premises omgeving maken

In deze sectie gebruikt u een Azure Quick Start-implementatie sjabloon voor het maken van nieuwe Vm's, het configureren van DNS en het toevoegen van een nieuwe Active Directory-forest.

1. Ga naar de Snelstartgids voor het [maken van een Azure VM met een nieuwe Active Directory-forest](https://azure.microsoft.com/resources/templates/active-directory-new-domain/).

1. Selecteer **implementeren naar Azure**.
1. Meld u aan bij uw Azure-abonnement.
1. Geef op de pagina **een Azure VM maken met een nieuwe AD-forest** de volgende informatie op:

    |Eigenschap | Waarde |
    |---|---|
    |Abonnement|Selecteer het abonnement waarin u de resources wilt implementeren.|
    |Resourcegroep|Selecteer **nieuwe maken** en voer de naam in `OnPremADVRG`|
    |Locatie|Selecteer een locatie.|
    |Gebruikersnaam van beheerder|`HDIFabrikamAdmin`|
    |Beheerderswachtwoord|Voer een wachtwoord in.|
    |Domeinnaam|`HDIFabrikam.com`|
    |DNS-voor voegsel|`hdifabrikam`|

    De overige standaard waarden behouden.

    ![Sjabloon voor het maken van een Azure VM met een nieuw Azure AD-forest](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

1. Bekijk de voor **waarden** en selecteer **Ik ga akkoord met de bovenstaande voor waarden**.
1. Selecteer **kopen** en controleer de implementatie en wacht totdat deze is voltooid. Het duurt ongeveer 30 minuten om de implementatie te volt ooien.

## <a name="configure-users-and-groups-for-cluster-access"></a>Gebruikers en groepen configureren voor toegang tot het cluster

In deze sectie maakt u de gebruikers die toegang hebben tot het HDInsight-cluster aan het einde van deze hand leiding.

1. Maak verbinding met de domein controller met behulp van Extern bureaublad.
    1. Navigeer vanuit het Azure Portal naar **resource groepen**  >  **OnPremADVRG**  >  **adVM**  >  **Connect**.
    1. Selecteer in de vervolg keuzelijst **IP-adres** het open bare IP-adres.
    1. Selecteer **RDP-bestand downloaden** en open vervolgens het bestand.
    1. Gebruik `HDIFabrikam\HDIFabrikamAdmin` als de gebruikers naam.
    1. Voer het wacht woord in dat u hebt gekozen voor het beheerders account.
    1. Selecteer **OK**.

1. Navigeer op de domein controller **Serverbeheer** dash board naar **hulpprogram ma's**  >  **Active Directory gebruikers en computers**.

    ![Open Active Directory Management op het Serverbeheer-dash board](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Maak twee nieuwe gebruikers: **HDIAdmin** en **HDIUser**. Deze twee gebruikers worden aangemeld bij HDInsight-clusters.

    1. Klik op de pagina **Active Directory gebruikers en computers** met de rechter muisknop op `HDIFabrikam.com` en navigeer naar **nieuwe**  >  **gebruiker**.

        ![Een nieuwe Active Directory gebruiker maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. Op de pagina **Nieuw object-gebruiker** voert u `HDIUser` voor de voor **naam** en **aanmeldings naam** van de gebruiker in. De overige velden worden automatisch ingevuld. Selecteer vervolgens **Volgende**.

        ![Het eerste gebruikers object voor de beheerder maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. Voer in het pop-upvenster dat wordt weer gegeven een wacht woord voor het nieuwe account in. Selecteer **wacht woord verloopt nooit** en klik vervolgens op **OK** in het pop-upbericht.
    1. Klik op **volgende** en vervolgens op **volt ooien** om het nieuwe account te maken.
    1. Herhaal de bovenstaande stappen voor het maken van de gebruiker `HDIAdmin` .

        ![Een tweede gebruikers object voor de beheerder maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. Een globale beveiligings groep maken.

    1. Klik met de rechter muisknop op **Active Directory gebruikers en computers** `HDIFabrikam.com` en navigeer vervolgens naar **nieuwe**  >  **groep**.

    1. Voer `HDIUserGroup` in het tekstvak **groeps naam** in.

    1. Selecteer **OK**.

    ![Een nieuwe Active Directory groep maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![Een nieuw object maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. Leden toevoegen aan **HDIUserGroup**.

    1. Klik met de rechter muisknop op **HDIUser** en selecteer **toevoegen aan groep...**.
    1. Typ in het tekstvak **Geef de object namen op** `HDIUserGroup` . Selecteer **OK** en **Klik nogmaals op** de pop-up.
    1. Herhaal de vorige stappen voor het **HDIAdmin** -account.

        ![Voeg de leden HDIUser toe aan de groep HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

U hebt nu uw Active Directory-omgeving gemaakt. U hebt twee gebruikers en een gebruikers groep toegevoegd die toegang hebben tot het HDInsight-cluster.

De gebruikers worden gesynchroniseerd met Azure AD.

### <a name="create-an-azure-ad-directory"></a>Een Azure AD-Directory maken

1. Meld u aan bij de Azure-portal.
1. Selecteer **een resource maken** en type `directory` . Selecteer **Azure Active Directory**  >  **maken**.
1. Voer onder **organisatie naam** in `HDIFabrikam` .
1. Voer bij **initiële domein naam** in `HDIFabrikamoutlook` .
1. Selecteer **Maken**.

    ![Een Azure AD-Directory maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

### <a name="create-a-custom-domain"></a>Een aangepast domein maken

1. Selecteer in uw nieuwe **Azure Active Directory** onder **beheren** de optie **aangepaste domein namen**.
1. Selecteer **+ Aangepast domein toevoegen**.
1. Voer onder **aangepaste domein naam**, `HDIFabrikam.com` en selecteer vervolgens **domein toevoegen**.
1. Voeg vervolgens [uw DNS-gegevens toe aan de registratie van het domein](../../active-directory/fundamentals/add-custom-domain.md#add-your-dns-information-to-the-domain-registrar).

![Een aangepast domein maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

### <a name="create-a-group"></a>Een groep maken

1. Selecteer in uw nieuwe **Azure Active Directory** onder **beheren** de optie **groepen**.
1. Selecteer **+ nieuwe groep**.
1. Voer in het tekstvak **groeps naam** in `AAD DC Administrators` .
1. Selecteer **Maken**.

## <a name="configure-your-azure-ad-tenant"></a>Uw Azure AD-Tenant configureren

Nu gaat u uw Azure AD-Tenant configureren zodat u gebruikers en groepen van het on-premises Active Directory-exemplaar kunt synchroniseren met de Cloud.

Maak een Active Directory-Tenant beheerder.

1. Meld u aan bij de Azure Portal en selecteer uw Azure AD-Tenant, **HDIFabrikam**.

1. Navigeren om gebruikers te **beheren**  >    >  **nieuwe gebruiker**.

1. Voer de volgende gegevens in voor de nieuwe gebruiker:

    **Identiteit**

    |Eigenschap |Beschrijving |
    |---|---|
    |Gebruikersnaam|Voer `fabrikamazureadmin` in het tekstvak in. Selecteer in de vervolg keuzelijst domein naam de optie `hdifabrikam.com`|
    |Naam| Voer `fabrikamazureadmin` in.|

    **Wachtwoord**
    1. Selecteer **Ik wil het wacht woord maken**.
    1. Voer een veilig wacht woord van uw keuze in.

    **Groepen en rollen**
    1. Selecteer **0 groepen geselecteerd**.
    1. Selecteer **Aad DC-Administrators** en **Selecteer**.

    ![Het dialoog venster Azure AD-groepen](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-group-member.png)

    1. Selecteer **gebruiker**.
    1. Selecteer **globale beheerder** en **Selecteer**.

    ![Het dialoog venster Azure AD-rol](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-role-member.png)

1. Selecteer **Maken**.

1. Zorg er vervolgens voor dat de nieuwe gebruiker zich aanmeldt bij de Azure Portal waar u wordt gevraagd het wacht woord te wijzigen. U moet dit doen voordat u Microsoft Azure Active Directory Connect configureert.

## <a name="sync-on-premises-users-to-azure-ad"></a>On-premises gebruikers synchroniseren met Azure AD

### <a name="configure-microsoft-azure-active-directory-connect"></a>Microsoft Azure Active Directory Connect configureren

1. Down load [Microsoft Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594)van de domein controller.

1. Open het uitvoer bare bestand dat u hebt gedownload en ga akkoord met de licentie voorwaarden. Selecteer **Doorgaan**.

1. Selecteer **Express-instellingen gebruiken**.

1. Voer op de pagina **verbinding maken met Azure AD** de gebruikers naam en het wacht woord in van de globale beheerder voor Azure AD. Gebruik de gebruikers naam `fabrikamazureadmin@hdifabrikam.com` die u hebt gemaakt tijdens het configureren van uw Active Directory-Tenant. Selecteer vervolgens **Volgende**.

    ![De pagina verbinding maken met Azure A D.](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)

1. Voer op de pagina **verbinding maken met Active Directory Domain Services** de gebruikers naam en het wacht woord in voor een ondernemings Administrator-account. Gebruik de gebruikers naam `HDIFabrikam\HDIFabrikamAdmin` en het wacht woord dat u eerder hebt gemaakt. Selecteer vervolgens **Volgende**.

   ![De pagina verbinding maken met D D S.](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. Selecteer **volgende** op de pagina **aanmeldings configuratie van Azure AD** .
   ![De pagina configuratie van Azure AD-aanmelding](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)

1. Selecteer **installeren** op de pagina **gereed voor configuratie** .

   ![De pagina gereed om te configureren](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)

1. Selecteer de pagina **Configuratie voltooid** op **Afsluiten**.
   ![De pagina configuratie voltooid](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. Nadat de synchronisatie is voltooid, controleert u of de gebruikers die u hebt gemaakt in de map IaaS, worden gesynchroniseerd met Azure AD.
   1. Meld u aan bij de Azure-portal.
   1. Selecteer **Azure Active Directory**  >  **HDIFabrikam**-  >  **gebruikers**.

### <a name="create-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit maken

Maak een door de gebruiker toegewezen beheerde identiteit die u kunt gebruiken om Azure AD Domain Services (Azure AD DS) te configureren. Zie een [rol maken, weer geven, verwijderen of toewijzen aan een door de gebruiker toegewezen beheerde identiteit met behulp van de Azure Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)voor meer informatie.

1. Meld u aan bij de Azure-portal.
1. Selecteer **een resource maken** en type `managed identity` . Selecteer door de **gebruiker toegewezen beheerde identiteit**  >  **maken**.
1. Voer in voor de **resource naam** `HDIFabrikamManagedIdentity` .
1. Selecteer uw abonnement.
1. Onder **resource groep** selecteert u **nieuwe maken** en invoeren `HDIFabrikam-CentralUS` .
1. Onder **locatie** selecteert u **Centraal VS**.
1. Selecteer **Maken**.

![Een nieuwe door de gebruiker toegewezen beheerde identiteit maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-ad-ds"></a>Azure AD DS inschakelen

Volg deze stappen om Azure AD DS in te scha kelen. Zie [Azure AD DS inschakelen met behulp van de Azure Portal](../../active-directory-domain-services/tutorial-create-instance.md)voor meer informatie.

1. Maak een virtueel netwerk om Azure AD DS te hosten. Voer de volgende Power shell-code uit.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }

    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
    
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Meld u aan bij de Azure-portal.
1. Selecteer **resource maken**, invoeren `Domain services` en selecteer **Azure AD Domain Services**  >  **maken**.
1. Op de pagina **basis beginselen** :
    1. Selecteer bij **mapnaam** de Azure AD-map die u hebt gemaakt: **HDIFabrikam**.
    1. Voer voor **DNS-domein naam** *HDIFabrikam.com* in.
    1. Selecteer uw abonnement.
    1. Geef de resource groep **HDIFabrikam-centralus** op. Selecteer voor **locatie** de optie **Centraal VS**.

        ![Basis Details van Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. Selecteer op de pagina **netwerk** het netwerk (**HDIFabrikam-VNET**) en het subnet (**AADDS-subnet**) dat u hebt gemaakt met behulp van het Power shell-script. Of kies **nieuwe maken** om nu een virtueel netwerk te maken.

    ![De stap ' virtueel netwerk maken '](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. Op de pagina **beheer groep** ziet u een melding dat er al een groep met de naam **Aad DC-Administrators** is gemaakt voor het beheren van deze groep. U kunt het lidmaatschap van deze groep wijzigen als u wilt, maar in dit geval hoeft u deze niet te wijzigen. Selecteer **OK**.

    ![De Azure AD-Beheerders groep weer geven](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. Schakel volledige synchronisatie in op de pagina **synchronisatie** door **alle**  >  **OK** te selecteren.

    ![Azure AD DS-synchronisatie inschakelen](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. Controleer op de pagina **samen vatting** de details van Azure AD DS en selecteer **OK**.

    ![De samen vatting van ' inschakelen Azure AD Domain Services '](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

Nadat u Azure AD DS hebt ingeschakeld, wordt een lokale DNS-server uitgevoerd op de Azure AD-Vm's.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Uw Azure AD DS virtuele netwerk configureren

Gebruik de volgende stappen om uw Azure AD DS virtuele netwerk (**HDIFabrikam-AADDSVNET**) te configureren voor het gebruik van uw aangepaste DNS-servers.

1. Zoek de IP-adressen van uw aangepaste DNS-servers.
    1. Selecteer de `HDIFabrikam.com` Azure AD DS-resource.
    1. Selecteer **Eigenschappen** onder **Beheren**.
    1. Zoek de IP-adressen onder **IP-adres in het virtuele netwerk**.

    ![Aangepaste DNS-IP-adressen voor Azure AD DS zoeken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. Configureer **HDIFabrikam-AADDSVNET** om aangepaste IP-adressen 10.0.0.4 en 10.0.0.5 te gebruiken.

    1. Selecteer bij **instellingen** de optie **DNS-servers**.
    1. selecteer **Aangepast**.
    1. Voer in het tekstvak het eerste IP-adres (*10.0.0.4*) in.
    1. Selecteer **Opslaan**.
    1. Herhaal de stappen om het andere IP-adres (*10.0.0.5*) toe te voegen.

In ons scenario hebben we Azure AD DS geconfigureerd voor het gebruik van IP-adressen 10.0.0.4 en 10.0.0.5, waarbij hetzelfde IP-adres op het virtuele netwerk van Azure AD DS wordt ingesteld:

![De pagina aangepaste DNS-servers](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>LDAP-verkeer beveiligen

LDAP (Lightweight Directory Access Protocol) wordt gebruikt voor het lezen van en schrijven naar Azure Active Directory. U kunt LDAP-verkeer vertrouwelijk en beveiligd maken met behulp van Secure Sockets Layer (SSL) of Transport Layer Security (TLS)-technologie. U kunt LDAP via SSL (LDAPS) inschakelen door een goed opgemaakt certificaat te installeren.

Zie [LDAPS configureren voor een door Azure AD DS beheerd domein](../../active-directory-domain-services/tutorial-configure-ldaps.md)voor meer informatie over het beveiligen van LDAP.

In deze sectie maakt u een zelfondertekend certificaat, downloadt u het certificaat en configureert u LDAPS voor het **HDIFabrikam** Azure AD DS beheerde domein.

Met het volgende script maakt u een certificaat voor **HDIFabrikam**. Het certificaat wordt opgeslagen in het pad *LocalMachine* .

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE]  
> Elk hulp programma of een toepassing die een geldige PKCS 10-aanvraag (Public Key Cryptography Standards) maakt, \# kan worden gebruikt om de TLS/SSL-certificaat aanvraag te vormen.

Controleer of het certificaat is geïnstalleerd in het **persoonlijke** archief van de computer:

1. Start micro soft Management Console (MMC).
1. Voeg de module **certificaten** toe waarmee certificaten op de lokale computer worden beheerd.
1. Vouw **Certificaten (lokale computer)**  > **Persoonlijk** > **Certificaten** uit. Er moet een nieuw certificaat bestaan in het **persoonlijke** archief. Dit certificaat wordt uitgegeven aan de volledig gekwalificeerde hostnaam.

    ![Lokale certificaten maken verifiëren](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. Klik in het rechterdeel venster met de rechter muisknop op het certificaat dat u hebt gemaakt. Wijs **alle taken** aan en selecteer vervolgens **exporteren**.

1. Selecteer op de pagina **persoonlijke sleutel exporteren** **de optie Ja, de persoonlijke sleutel exporteren**. De computer waarop de sleutel wordt geïmporteerd, heeft de persoonlijke sleutel nodig om de versleutelde berichten te lezen.

    ![De pagina persoonlijke sleutel exporteren van de wizard Certificaat exporteren](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. Behoud op de pagina **bestands indeling voor export** de standaard instellingen en selecteer **volgende**.
1. Typ een wacht woord voor de persoonlijke sleutel op de pagina **wacht woord** . Voor **versleuteling** selecteert u **TripleDES-SHA1**. Selecteer vervolgens **Volgende**.
1. Typ het pad en de naam voor het geëxporteerde certificaat bestand op de pagina **te exporteren bestand** en selecteer vervolgens **volgende**. De bestands naam moet de extensie. pfx hebben. Dit bestand wordt geconfigureerd in de Azure Portal om een beveiligde verbinding tot stand te brengen.
1. Schakel LDAPS in voor een beheerd domein van Azure AD DS.
    1. Selecteer het domein uit de Azure Portal `HDIFabrikam.com` .
    1. Selecteer onder **beheren** de optie **secure LDAP**.
    1. Selecteer op de pagina **secure LDAP** onder **secure LDAP** de optie **inschakelen**.
    1. Blader naar het. pfx-certificaat bestand dat u hebt geëxporteerd op uw computer.
    1. Voer het certificaat wachtwoord in.

    ![Secure LDAP inschakelen](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. Nu u LDAPS hebt ingeschakeld, moet u ervoor zorgen dat deze bereikbaar is door poort 636 in te scha kelen.
    1. Selecteer in de resource groep **HDIFabrikam-centralus** de netwerk beveiligings groep **AADDS-HDIFabrikam.com-NSG**.
    1. Selecteer onder **instellingen** de optie **regels voor inkomende beveiliging**  >  **toevoegen**.
    1. Voer op de pagina **inkomende beveiligings regel toevoegen** de volgende eigenschappen in en selecteer **toevoegen**:

        | Eigenschap | Waarde |
        |---|---|
        | Bron | Elk |
        | Poortbereiken van bron | * |
        | Doel | Alle |
        | Poortbereik van doel | 636 |
        | Protocol | Elk |
        | Actie | Toestaan |
        | Prioriteit | \<Desired number> |
        | Naam | Port_LDAP_636 |

    ![Het dialoog venster "binnenkomende beveiligings regel toevoegen"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

**HDIFabrikamManagedIdentity** is de door de gebruiker toegewezen beheerde identiteit. De functie voor het beheren van de HDInsight Domain Services-rol is ingeschakeld voor de beheerde identiteit waarmee de identiteit van domein Services kan worden gelezen, gemaakt, gewijzigd en verwijderd.

![Een door de gebruiker toegewezen beheerde identiteit maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>Een HDInsight-cluster met ESP-functionaliteit maken

Voor deze stap zijn de volgende vereisten vereist:

1. Maak een nieuwe resource groep *HDIFabrikam-westus* in de locatie **VS West**.
1. Maak een virtueel netwerk waarmee het HDInsight-cluster met ESP-functionaliteit wordt gehost.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Maak een peer-relatie tussen het virtuele netwerk dat als host fungeert voor Azure AD DS ( `HDIFabrikam-AADDSVNET` ) en het virtuele netwerk dat het HDInsight-cluster met ESP-functionaliteit () moet hosten `HDIFabrikam-HDIVNet` . Gebruik de volgende Power shell-code voor de peering van de twee virtuele netwerken.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Maak een nieuw Azure Data Lake Storage Gen2 account met de naam **Hdigen2store**. Configureer het account met de door de gebruiker beheerde identiteit **HDIFabrikamManagedIdentity**. Zie [Azure data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-data-lake-storage-gen2.md)voor meer informatie.

1. Stel aangepaste DNS in op het virtuele netwerk **HDIFabrikam-AADDSVNET** .
    1. Ga naar het Azure Portal > **resource groepen**  >  **OnPremADVRG**  >  **HDIFabrikam-AADDSVNET**  >  **DNS-servers**.
    1. Selecteer **aangepast** en voer *10.0.0.4* en *10.0.0.5* in.
    1. Selecteer **Opslaan**.

        ![Aangepaste DNS-instellingen voor een virtueel netwerk opslaan](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. Maak een nieuw met ESP ingeschakelde HDInsight Spark-cluster.
    1. Selecteer **aangepast (grootte, instellingen, apps)**.
    1. Voer Details in voor **basis beginselen** (sectie 1). Zorg ervoor dat het **cluster type** **Spark 2,3 (HDI 3,6)** is. Zorg ervoor dat de **resource groep** **HDIFabrikam-centralus** is.

    1. Voor **beveiliging en netwerken** (sectie 2) vult u de volgende gegevens in:
        * Selecteer onder **Enterprise Security Package** **ingeschakeld**.
        * Selecteer **gebruiker cluster beheerder** en selecteer het **HDIAdmin** -account dat u hebt gemaakt als de on-premises beheer gebruiker. Klik op **Selecteren**.
        * Selecteer **cluster toegangs groep**  >  **HDIUserGroup**. Alle gebruikers die u in de toekomst aan deze groep toevoegt, hebben toegang tot HDInsight-clusters.

            ![Selecteer de cluster toegangs groep HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. Voer de overige stappen van de cluster configuratie uit en controleer de details van de **cluster samenvatting**. Selecteer **Maken**.

1. Meld u aan bij de Ambari-gebruikers interface voor het zojuist gemaakte cluster op `https://CLUSTERNAME.azurehdinsight.net` . Gebruik de gebruikers naam van uw beheerder `hdiadmin@hdifabrikam.com` en het bijbehorende wacht woord.

    ![Het aanmeldings venster van de Apache Ambari-gebruikers interface](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. Selecteer in het cluster dashboard **rollen**.
1. Voer op de pagina **functies** onder **rollen toewijzen aan deze** in naast de rol **cluster beheerder** de groep *hdiusergroup*. 

    ![Wijs de rol cluster beheerder toe aan hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. Open uw SSH-client (Secure Shell) en meld u aan bij het cluster. Gebruik de **hdiuser** die u hebt gemaakt in het on-premises Active Directory-exemplaar.

    ![Aanmelden bij het cluster met behulp van de SSH-client](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

Als u zich kunt aanmelden met dit account, hebt u uw ESP-cluster op de juiste wijze geconfigureerd om te synchroniseren met uw on-premises Active Directory-exemplaar.

## <a name="next-steps"></a>Volgende stappen

Lees [een inleiding tot Apache Hadoop beveiliging met ESP](hdinsight-security-overview.md).