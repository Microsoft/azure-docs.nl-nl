---
title: Een Azure AD Domain Services resource-forest maken met behulp van Azure PowerShell | Microsoft Docs
description: In dit artikel vindt u informatie over het maken en configureren van een Azure Active Directory Domain Services bron-forest en een uitgaand forest naar een on-premises Active Directory Domain Services omgeving met behulp van Azure PowerShell.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: justinha
ms.openlocfilehash: ebfc2476b7955b926f86094de03973155386eb8f
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619964"
---
# <a name="create-an-azure-active-directory-domain-services-resource-forest-and-outbound-forest-trust-to-an-on-premises-domain-using-azure-powershell"></a>Een Azure Active Directory Domain Services resource-forest en een uitgaand forest-vertrouwens relatie met een on-premises domein maken met behulp van Azure PowerShell

In omgevingen waarin u geen wachtwoordhashes kunt synchroniseren, of waarin gebruikers werken die zich alleen aanmelden via een smartcard (zodat ze hun wachtwoord niet kennen), kunt u een resourceforest gebruiken in Azure AD DS (Azure Active Directory Domain Services). Een resourceforest maakt gebruik van een uitgaande vertrouwensrelatie in één richting van Azure AD DS naar één of meer on-premises AD DS-omgevingen. Met deze vertrouwensrelatie kunnen gebruikers, toepassingen en computers worden geverifieerd in een on-premises domein vanuit het met Azure AD DS beheerde domein. Bij gebruik van een resourceforest worden hashes van on-premises wachtwoorden nooit gesynchroniseerd.

![Diagram van forestvertrouwensrelatie van Azure AD DS naar on-premises AD DS](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een Azure AD DS resource-forest maken met behulp van Azure PowerShell
> * Een eenrichtings vertrouwensrelatie voor uitgaande forests in het beheerde domein maken met behulp van Azure PowerShell
> * DNS configureren in een on-premises AD DS omgeving ter ondersteuning van beheerde domein connectiviteit
> * Een binnenkomende forestvertrouwensrelatie in één richting maken in een on-premises AD DS-omgeving
> * De vertrouwensrelatie testen en valideren voor verificatie en resourcetoegang

Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

> [!IMPORTANT]
> De resource forests van het beheerde domein ondersteunen momenteel geen Azure HDInsight of Azure Files. De standaard gebruikers forests van het beheerde domein ondersteunen beide extra services.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources en bevoegdheden nodig om dit artikel te volt ooien:

* Een actief Azure-abonnement.
    * Als u nog geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory-tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises map of een cloudmap.
    * [Maak zo nodig een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account][associate-azure-ad-tenant].

* Installeer en configureer Azure PowerShell.
    * Indien nodig, volgt u de instructies voor [installeren van de Azure PowerShell-module en verbinding maken met uw Azure-abonnement](/powershell/azure/install-az-ps).
    * Zorg ervoor dat u zich bij uw Azure-abonnement aanmeldt met behulp van de [Connect-AzAccount][Connect-AzAccount]-cmdlet.
* Installeer en configureer Azure AD PowerShell.
    * Indien nodig, volgt u de instructies voor het [installeren van de Azure AD PowerShell-module en verbinding maken met Azure AD](/powershell/azure/active-directory/install-adv2).
    * Zorg ervoor dat u zich bij de Azure-tenant aanmeldt met behulp van de [Connect-AzureAD][Connect-AzureAD]-cmdlet.
* U hebt *bevoegdheden van een globale beheerder* voor de Azure AD-tenant nodig om Azure AD DS in te schakelen.
* U hebt bevoegdheden van een *Inzender* voor uw Azure-abonnement nodig om de vereiste Azure AD DS-resources te maken.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

In dit artikel maakt en configureert u de uitgaande forestvertrouwensrelatie vanuit een beheerd domein met behulp van de Azure Portal. Meld u eerst aan bij de [Azure-portal](https://portal.azure.com) om aan de slag te gaan.

## <a name="deployment-process"></a>Implementatieproces

Het is een proces met meerdere onderdelen voor het maken van een beheerd domein resource forest en de vertrouwens relatie met een on-premises AD DS. De volgende stappen op hoog niveau maken uw vertrouwde, hybride omgeving:

1. Maak een service-principal voor een beheerd domein.
1. Een resource forest voor een beheerd domein maken.
1. Een hybride netwerk verbinding maken met behulp van site-naar-site VPN of Express route.
1. Maak de beheerde domein zijde van de vertrouwens relatie.
1. De on-premises AD DS zijde van de vertrouwens relatie maken.

Voordat u begint, moet u ervoor zorgen dat u inzicht krijgt in de [netwerk overwegingen, naamgeving van forests en DNS-vereisten](tutorial-create-forest-trust.md#networking-considerations). U kunt de naam van het beheerde domein forest niet wijzigen nadat deze is geïmplementeerd.

## <a name="create-the-azure-ad-service-principal"></a>De Azure AD-service-principal maken

Voor Azure AD DS is een service-principal met gegevens synchronisatie vanuit Azure AD vereist. Deze principal moet worden gemaakt in uw Azure AD-Tenant voordat u het beheerde domein resource-forest maakt.

Een Azure AD-service-principal voor Azure AD DS maken om te communiceren en zichzelf te verifiëren. Er wordt een specifieke toepassings-id gebruikt met de naam *Domeincontrollerservices* met de id *6ba9a5d4-8456-4118-b521-9c5ca10cdf84*. Wijzig deze toepassings-id niet.

Maak een service-principal in Azure AD met behulp van de [New-AzureADServicePrincipal][New-AzureADServicePrincipal]-cmdlet:

```powershell
New-AzureADServicePrincipal -AppId "6ba9a5d4-8456-4118-b521-9c5ca10cdf84"
```

## <a name="create-a-managed-domain-resource-forest"></a>Een resource forest voor een beheerd domein maken

Gebruik het script om een resource forest voor een beheerd domein te maken `New-AzureAaddsForest` . Dit script maakt deel uit van een bredere set opdrachten die ondersteuning biedt voor het maken en beheren van beheerde domein resource forests, waaronder het maken van een eenrichtings gebonden forest in een van de volgende secties. Deze scripts zijn beschikbaar via de [PowerShell Gallery](https://www.powershellgallery.com/) en zijn digitaal ondertekend door het technische team van Azure AD.

1. Maak eerst een resource groep met behulp van de cmdlet [New-AzResourceGroup][New-AzResourceGroup] . De resourcegroep in het volgende voorbeeld heet *myResourceGroup* en is gemaakt in de regio *westus*. Gebruik uw eigen naam en gewenste regio:

    ```azurepowershell
    New-AzResourceGroup `
      -Name "myResourceGroup" `
      -Location "WestUS"
    ```

1. Installeer het `New-AaddsResourceForestTrust` script vanuit de [PowerShell Gallery][powershell-gallery] met behulp van de cmdlet [install-script][Install-Script] :

    ```powershell
    Install-Script -Name New-AaddsResourceForestTrust
    ```

1. Controleer de volgende para meters die nodig zijn voor het `New-AzureAaddsForest` script. Zorg ervoor dat u ook beschikt over de vereiste **Azure PowerShell** en **Azure AD Power shell** -modules. Zorg ervoor dat u de vereisten voor het virtuele netwerk hebt gepland om toepassingen en on-premises connectiviteit te bieden.

    | Naam                         | Script parameter          | Beschrijving |
    |:-----------------------------|---------------------------|:------------|
    | Abonnement                 | *-azureSubscriptionId*    | Abonnements-ID die wordt gebruikt voor Azure AD DS-facturering. U kunt de lijst met abonnementen ophalen met behulp van de cmdlet [Get-AzureRMSubscription][Get-AzureRMSubscription] . |
    | Resourcegroep               | *-aaddsResourceGroupName* | De naam van de resource groep voor het beheerde domein en de gekoppelde resources. |
    | Locatie                     | *-aaddsLocation*          | De Azure-regio voor het hosten van uw beheerde domein. Zie [ondersteunde regio's voor Azure AD DS](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all) voor beschik bare regio's. |
    | Beheerder van Azure AD DS    | *-aaddsAdminUser*         | De user principal name van de eerste beheerde domein beheerder. Dit account moet een bestaand Cloud gebruikers account in uw Azure Active Directory zijn. De gebruiker en de gebruiker die het script uitvoert, worden toegevoegd aan de groep *Aad DC-Administrators* . |
    | Domein naam van Azure AD DS      | *-aaddsDomainName*        | De FQDN-naam van het beheerde domein, op basis van de vorige richt lijnen voor het kiezen van een Forestnaam. |

    `New-AzureAaddsForest`Met het script kunt u het virtuele Azure-netwerk en het azure AD DS-subnet maken als deze resources nog niet bestaan. Het script kan eventueel de subnetten van de werk belasting maken, indien opgegeven:

    | Naam                              | Script parameter                  | Beschrijving |
    |:----------------------------------|:----------------------------------|:------------|
    | Naam van virtueel netwerk              | *-aaddsVnetName*                  | De naam van het virtuele netwerk voor het beheerde domein.|
    | Adresruimte                     | *-aaddsVnetCIDRAddressSpace*      | Het adres bereik van het virtuele netwerk in CIDR-notatie (bij het maken van het virtuele netwerk).|
    | Naam van Azure AD DS subnet           | *-aaddsSubnetName*                | De naam van het subnet van het virtuele *aaddsVnetName* -netwerk dat als host fungeert voor het beheerde domein. Implementeer uw eigen Vm's en workloads niet in dit subnet. |
    | Adres bereik van Azure AD DS         | *-aaddsSubnetCIDRAddressRange*    | Het adres bereik van het subnet in CIDR-notatie voor het AAD DS-exemplaar, zoals *192.168.1.0/24*. Het adres bereik moet zijn opgenomen in het adres bereik van het virtuele netwerk en verschilt van andere subnetten. |
    | Naam van workload-subnet (optioneel)   | *-workloadSubnetName*             | Een optionele naam van een subnet in het virtuele netwerk van *aaddsVnetName* om te maken voor werk belastingen van uw eigen toepassing. Vm's en toepassingen en zijn ook verbonden met een virtueel Azure Virtual Network. |
    | Adres bereik van werk belasting (optioneel) | *-workloadSubnetCIDRAddressRange* | Het adres bereik van een optioneel subnet in CIDR-notatie voor de werk belasting van de toepassing, zoals *192.168.2.0/24*. Het adres bereik moet zijn opgenomen in het adres bereik van het virtuele netwerk en verschilt van andere subnetten.|

1. Maak nu een forest met een beheerd domein resource met behulp van het `New-AzureAaaddsForest` script. In het volgende voor beeld wordt een forest gemaakt met de naam *addscontoso.com* en wordt een workload-subnet gemaakt. Geef uw eigen parameter namen en IP-adresbereiken of bestaande virtuele netwerken op.

    ```azurepowershell
    New-AzureAaddsForest `
        -azureSubscriptionId <subscriptionId> `
        -aaddsResourceGroupName "myResourceGroup" `
        -aaddsLocation "WestUS" `
        -aaddsAdminUser "contosoadmin@contoso.com" `
        -aaddsDomainName "aaddscontoso.com" `
        -aaddsVnetName "myVnet" `
        -aaddsVnetCIDRAddressSpace "192.168.0.0/16" `
        -aaddsSubnetName "AzureADDS" `
        -aaddsSubnetCIDRAddressRange "192.168.1.0/24" `
        -workloadSubnetName "myWorkloads" `
        -workloadSubnetCIDRAddressRange "192.168.2.0/24"
    ```

    Het kan enige tijd duren om het beheerde domein resource-forest en de ondersteunende resources te maken. Toestaan dat het script is voltooid. Ga verder met de volgende sectie om uw on-premises netwerk verbinding te configureren terwijl de Azure AD-resource-forests op de achtergrond worden ingericht.

## <a name="configure-and-validate-network-settings"></a>Netwerk instellingen configureren en valideren

Wanneer het beheerde domein blijft implementeren, configureert en valideert u de hybride netwerk verbinding met het on-premises Data Center. U hebt ook een beheer-VM nodig voor het gebruik van het beheerde domein voor normaal onderhoud. Sommige van de hybride connectiviteit bestaat mogelijk al in uw omgeving, of u moet mogelijk samen werken met anderen in uw team om de verbindingen te configureren.

Voordat u begint, moet u ervoor zorgen dat u inzicht krijgt in de [overwegingen en aanbevelingen](tutorial-create-forest-trust.md#networking-considerations)van het netwerk.

1. De hybride verbinding met uw on-premises netwerk met Azure maken met behulp van een Azure VPN-of Azure ExpressRoute-verbinding. De configuratie van het hybride netwerk valt buiten het bereik van deze documentatie en kan al bestaan in uw omgeving. Raadpleeg de volgende artikelen voor meer informatie over specifieke scenario's:

    * [Azure site-naar-site-VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
    * [Overzicht van Azure ExpressRoute](../expressroute/expressroute-introduction.md).

    > [!IMPORTANT]
    > Als u de verbinding rechtstreeks maakt naar het virtuele netwerk van uw beheerde domein, gebruikt u een afzonderlijk gateway-subnet. Maak de gateway niet in het subnet van het beheerde domein.

1. Als u een beheerd domein wilt beheren, maakt u een beheer-VM, voegt u deze toe aan het beheerde domein en installeert u de vereiste AD DS-beheer hulpprogramma's.

    Terwijl het forest met het beheerde domein resource wordt geïmplementeerd, [maakt u een Windows Server-VM](./join-windows-vm.md) en installeert u vervolgens [de kern AD DS-beheer hulpprogramma's](./tutorial-create-management-vm.md) om de benodigde beheer hulpprogramma's te installeren. Wacht tot een van de volgende stappen wordt uitgevoerd nadat het domein met succes is geïmplementeerd.

1. Valideer de netwerk verbinding tussen uw on-premises netwerk en het virtuele Azure-netwerk.

    * Controleer of de on-premises domein controller bijvoorbeeld verbinding kan maken met de beheerde VM met `ping` of extern bureau blad.
    * Controleer of de virtuele machine van het beheer verbinding kan maken met uw on-premises domein controllers, met een hulp programma zoals `ping` .

1. Zoek en selecteer **Azure AD Domain Services** in de Azure-portal. Kies uw beheerde domein, bijvoorbeeld *aaddscontoso.com* , en wacht totdat de status is gerapporteerd als **actief**.

    Bij uitvoering werkt u de [DNS-instellingen voor het virtuele Azure-netwerk](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network) bij en schakelt u vervolgens [gebruikers accounts voor Azure AD DS](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) in om de configuraties voor het forest met het beheerde domein resource te volt ooien.

1. Noteer de DNS-adressen die worden weer gegeven op de pagina overzicht. U hebt deze adressen nodig bij het configureren van de on-premises Active Directory zijde van de vertrouwens relatie in een van de volgende secties.
1. Start de beheer-VM opnieuw op om de nieuwe DNS-instellingen te ontvangen en [Voeg de virtuele machine vervolgens toe aan het beheerde domein](join-windows-vm.md#join-the-vm-to-the-managed-domain).
1. Nadat de beheer-VM is gekoppeld aan het beheerde domein, maakt u opnieuw verbinding met extern bureau blad.

    Gebruik vanaf een opdracht prompt `nslookup` en de naam van het beheerde domein resource-forest voor het valideren van naam omzetting voor het bron-forest.

    ```console
    nslookup aaddscontoso.com
    ```

    De opdracht moet twee IP-adressen retour neren voor de bron-forest.

## <a name="create-the-forest-trust"></a>De forest-vertrouwens relatie maken

De vertrouwens relatie tussen forests bestaat uit twee delen: de eenrichtings vertrouwensrelatie voor uitgaande forests in het forest van het beheerde domein en de eenrichtings vertrouwensrelatie voor binnenkomende forests in het on-premises AD DS-forest. U maakt hand matig beide zijden van deze vertrouwens relatie. Wanneer beide zijden zijn gemaakt, kunnen gebruikers en bronnen worden geverifieerd met de vertrouwens relatie van het forest. Een onderliggend domein resource forest ondersteunt Maxi maal 5 1 uitgaande forest-vertrouwens relaties met on-premises forests.

### <a name="create-the-managed-domain-side-of-the-trust-relationship"></a>De beheerde domein zijde van de vertrouwens relatie maken

Gebruik het `Add-AaddsResourceForestTrust` script om de beheerde domein zijde van de vertrouwens relatie te maken. Installeer eerst het `Add-AaddsResourceForestTrust` script vanuit het [PowerShell Gallery][powershell-gallery] met behulp van de cmdlet [install-script][Install-Script] :

```powershell
Install-Script -Name Add-AaddsResourceForestTrust
```

Geef het script nu de volgende informatie:

| Naam                               | Script parameter     | Beschrijving |
|:-----------------------------------|:---------------------|:------------|
| Domein naam van Azure AD DS            | *-ManagedDomainFqdn* | FQDN-namen van het beheerde domein, zoals *aaddscontoso.com* |
| Domein naam van on-premises AD DS      | *-TrustFqdn*         | De FQDN van het vertrouwde forest, zoals *onprem.contoso.com* |
| Beschrijvende naam van vertrouwens relatie                | *-TrustFriendlyName* | Beschrijvende naam van de vertrouwens relatie. |
| On-premises AD DS DNS IP-adressen | *-TrustDnsIPs*       | Een door komma's gescheiden lijst met IPv4-adressen van de DNS-server voor het vermelde vertrouwde domein. |
| Wacht woord vertrouwen                     | *-TrustPassword*     | Een complex wacht woord voor de vertrouwens relatie. Dit wacht woord wordt ook ingevoerd bij het maken van een eenrichtings inkomend vertrouwens relatie in de on-premises AD DS. |
| Referenties                        | *-Referenties*       | De referenties die worden gebruikt om te verifiëren bij Azure. De gebruiker moet zich in de *groep Aad DC-Administrators* bestaan. Als u dit niet opgeeft, vraagt het script om verificatie. |

In het volgende voor beeld wordt een vertrouwens relatie met de naam *myAzureADDSTrust* naar *onprem.contoso.com* gemaakt. Gebruik uw eigen parameter namen en wacht woorden:.

```azurepowershell
Add-AaddsResourceForestTrust `
    -ManagedDomainFqdn "aaddscontoso.com" `
    -TrustFqdn "onprem.contoso.com" `
    -TrustFriendlyName "myAzureADDSTrust" `
    -TrustDnsIPs "10.0.1.10,10.0.1.11" `
    -TrustPassword <complexPassword>
```

> [!IMPORTANT]
> Onthoud uw vertrouwens wachtwoord. U moet hetzelfde wacht woord gebruiken wanneer u de on-premises kant van de vertrouwens relatie maakt.

## <a name="configure-dns-in-the-on-premises-domain"></a>DNS configureren in het on-premises domein

Als u het beheerde domein correct wilt omzetten vanuit de on-premises omgeving, moet u mogelijk doorstuurservers toevoegen aan de bestaande DNS-server. Als u de on-premises omgeving niet hebt geconfigureerd voor communicatie met het beheerde domein, voltooit u de volgende stappen vanuit een beheerwerkstation voor het on-premises AD DS-domein:

1. Selecteer **Start | Systeembeheer | DNS**
1. Selecteer met de rechtermuisknop de DNS-server, bijvoorbeeld *myAD01*. Selecteer vervolgens **Eigenschappen**
1. Kies **Doorstuurservers** en vervolgens **Bewerken** om extra doorstuurservers toe te voegen.
1. Voeg de IP-adressen van het beheerde domein toe, zoals *10.0.1.4* en *10.0.1.5*.
1. Vanuit een lokale opdracht prompt valideert u naam omzetting met behulp van **nslookup** van de domein naam van het forest van het beheerde domein. `Nslookup aaddscontoso.com`Moet bijvoorbeeld de twee IP-adressen retour neren voor het forest met het beheerde domein resource.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>Een binnenkomend forestvertrouwensrelatie maken in het on-premises domein

Het on-premises AD DS-domein heeft een binnenkomende forestvertrouwensrelatie voor het beheerde domein nodig. Deze vertrouwensrelatie moet handmatig zijn gemaakt in het on-premises AD DS-domein. Deze kan niet worden gemaakt in de Azure-portal.

Als u een binnenkomende vertrouwensrelatie in het on-premises AD DS-domein wilt configureren, voltooit u de volgende stappen vanuit een beheerwerkstation voor het on-premises AD DS-domein:

1. Selecteer **Start | Systeembeheer | Active Directory: domeinen en vertrouwensrelaties**
1. Selecteer met de rechtermuisknop het domein, bijvoorbeeld *onprem.contoso.com*. Selecteer vervolgens **Eigenschappen**
1. Kies het tabblad **Vertrouwensrelaties** en vervolgens **Nieuwe vertrouwensrelatie**
1. Voer de naam van het beheerde domein in, bijvoorbeeld *aaddscontoso.com*, en selecteer vervolgens **volgende**
1. Selecteer de optie om een **Forestvertrouwensrelatie** te maken. Maak vervolgens een vertrouwensrelatie van het type **In één richting: binnenkomend**.
1. Kies ervoor om de vertrouwensrelatie te maken voor **Alleen dit domein**. In de volgende stap maakt u de vertrouwensrelatie in de Azure-portal voor het beheerde domein.
1. Kies voor het gebruik van **Forestdekkende verificatie**. Voer vervolgens een wachtwoord voor de vertrouwensrelatie in en bevestig dit. Dit wachtwoord wordt ook ingevoerd in de Azure-portal in de volgende sectie.
1. Doorloop de volgende paar vensters met standaardopties, en kies vervolgens de optie **Nee, uitgaande vertrouwensrelatie niet bevestigen**. U kunt de vertrouwens relatie niet valideren omdat het gedelegeerde beheerders account aan het forest van het beheerde domein niet over de vereiste machtigingen beschikt. Dit gedrag is standaard.
1. Selecteer **Voltooien**.

## <a name="validate-resource-authentication"></a>Verificatie van resources valideren

Met de volgende algemene scenario's kunt u valideren of met de forestvertrouwensrelatie gebruikers op de juiste wijze worden geverifieerd en toegang tot resources krijgen:

* [On-premises gebruikersverificatie vanuit de Azure AD DS-resourceforest](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [Toegang krijgen tot resources in het Azure AD DS-resourceforest met behulp van on-premises gebruiker](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [Bestands- en printerdeling inschakelen](#enable-file-and-printer-sharing)
    * [Een beveiligingsgroep maken en leden toevoegen](#create-a-security-group-and-add-members)
    * [Een bestandsshare maken voor toegang tussen forests](#create-a-file-share-for-cross-forest-access)
    * [Verificatie tussen forests valideren voor een resource](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>On-premises gebruikersverificatie vanuit de Azure AD DS-resourceforest

U moet een virtuele Windows Server-machine koppelen aan het resource domein van het beheerde domein. Gebruik deze virtuele machine om te testen of uw on-premises gebruiker kan worden geverifieerd op de virtuele machine.

1. Maak verbinding met de VM van het beheerde domein met behulp van Extern bureaublad en de referenties van uw beheerde domein beheerder. Als er een verificatie op netwerkniveau-fout (NLA) wordt weer geven, controleert u of het gebruikers account dat u hebt gebruikt geen domein gebruikers account is.

    > [!TIP]
    > Als u een beveiligde verbinding wilt maken met uw Vm's die zijn toegevoegd aan Azure AD Domain Services, kunt u de [Azure bastion host-service](../bastion/bastion-overview.md) gebruiken in ondersteunde Azure-regio's.

1. Open een opdrachtprompt en gebruik de opdracht `whoami` om de DN-naam van de momenteel geverifieerde gebruiker weer te geven:

    ```console
    whoami /fqdn
    ```

1. Gebruik de opdracht `runas` om te verifiëren als een gebruiker van het on-premises domein. Vervang in de volgende opdracht `userUpn@trusteddomain.com` door de UPN van een gebruiker van het vertrouwde on-premises domein. De opdracht vraagt u om het wachtwoord van de gebruiker:

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. Als de verificatie is geslaagd, wordt een nieuwe opdrachtprompt geopend. De titel van de nieuwe opdrachtprompt bevat `running as userUpn@trusteddomain.com`.
1. Gebruik `whoami /fqdn` in de nieuwe opdrachtprompt om de DN-naam van de geverifieerde gebruiker van de on-premises Active Directory weer te geven.

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>Toegang krijgen tot resources in het Azure AD DS-resourceforest met behulp van on-premises gebruiker

Met behulp van de Windows Server-VM die is toegevoegd aan het forest met het beheerde domein, kunt u het scenario testen waarbij gebruikers toegang hebben tot de resources die worden gehost in de bron-forest wanneer ze worden geverifieerd vanaf computers in het on-premises domein met gebruikers van het on-premises domein. In de volgende voorbeelden ziet u hoe u verschillende veelvoorkomende scenario's kunt maken en testen.

#### <a name="enable-file-and-printer-sharing"></a>Bestands- en printerdeling inschakelen

1. Maak verbinding met de VM van het beheerde domein met behulp van Extern bureaublad en de referenties van uw beheerde domein beheerder. Als er een verificatie op netwerkniveau-fout (NLA) wordt weer geven, controleert u of het gebruikers account dat u hebt gebruikt geen domein gebruikers account is.

    > [!TIP]
    > Als u een beveiligde verbinding wilt maken met uw Vm's die zijn toegevoegd aan Azure AD Domain Services, kunt u de [Azure bastion host-service](../bastion/bastion-overview.md) gebruiken in ondersteunde Azure-regio's.

1. Open **Windows-instellingen**. Zoek en selecteer vervolgens **Netwerkcentrum**.
1. Kies de optie **Geavanceerde instellingen voor delen wijzigen**.
1. Selecteer onder het **Domeinprofiel** de optie **Bestands- en printerdeling inschakelen** en vervolgens **Wijzigingen opslaan**.
1. Sluit het **Netwerkcentrum**.

#### <a name="create-a-security-group-and-add-members"></a>Een beveiligingsgroep maken en leden toevoegen

1. Open **Active Directory: gebruikers en computers**.
1. Klik met de rechtermuisknop op de domeinnaam, kies **Nieuw** en selecteer vervolgens **Organisatie-eenheid**.
1. Typ *LocalObjects* in het naamvak, en selecteer vervolgens **OK**.
1. Selecteer en klik met de rechtermuisknop op **LocalObjects** in het navigatiedeelvenster. Selecteer **Nieuw** en vervolgens **Groep**.
1. Typ *FileServerAccess* in het vak **Groepsnaam**. Selecteer voor het **Groepsbereik** de optie **Lokaal domein** en kies vervolgens **OK**.
1. Dubbelklik in het inhoudsvenster op **FileServerAccess**. Selecteer **Leden**, kies **Toevoegen** en selecteer **Locaties**.
1. Selecteer uw on-premises Active Directory op de weergave **Locatie** en kies vervolgens **OK**.
1. Typ *Domeingebruikers* in het vak **Geef de objectnamen op**. Selecteer **Namen controleren**, geef referenties op voor de on-premises Active Directory en selecteer vervolgens **OK**.

    > [!NOTE]
    > U moet referenties opgeven, omdat de vertrouwensrelatie slechts één manier is. Dit betekent dat gebruikers van het beheerde domein geen toegang krijgen tot bronnen of kunnen zoeken naar gebruikers of groepen in het vertrouwde (on-premises) domein.

1. De groep **Domeingebruikers** uit uw on-premises Active Directory moet lid zijn van de groep **FileServerAccess**. Selecteer **OK** om de groep op te slaan, en sluit het venster.

#### <a name="create-a-file-share-for-cross-forest-access"></a>Een bestandsshare maken voor toegang tussen forests

1. Maak op de Windows Server-VM lid van het forest beheerde domein resource een map en geef een naam op, bijvoorbeeld *CrossForestShare*.
1. Klik met de rechtermuisknop op de map en kies **Eigenschappen**.
1. Selecteer het tabblad **Beveiliging** en kies vervolgens **Bewerken**.
1. Selecteer in het dialoogvenster *Machtigingen voor CrossForestShare* de optie **Toevoegen**.
1. Typ *FileServerAccess* in **Geef de objectnamen op**. Selecteer vervolgens **OK**.
1. Selecteer *FileServerAccess* in de lijst **Groepen of gebruikersnamen**. Kies in de lijst **Machtigingen voor FileServerAccess** de optie *Toestaan* voor de machtigingen **Wijzigen** en  **Schrijven**. Selecteer vervolgens **OK**.
1. Selecteer het tabblad **Delen**. Kies vervolgens **Geavanceerd delen...**
1. Kies **Deze map delen**. Voer vervolgens bij **Naam delen**  een gemakkelijk te onthouden naam in voor de bestandsshare, bijvoorbeeld *CrossForestShare*.
1. Selecteer **Machtigingen**. Kies in de lijst **Machtigingen voor iedereen** de optie **Toestaan** bij de machtiging voor **Wijzigen**.
1. Selecteer twee keer **OK** en vervolgens **Sluiten**.

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>Verificatie tussen forests valideren voor een resource

1. Meld u aan op een Windows-computer die is gekoppeld aan uw on-premises Active Directory, met behulp van een gebruikersaccount uit uw on-premises Active Directory.
1. Gebruik **Windows Verkenner** om verbinding te maken met de share die u hebt gemaakt, met behulp van de volledig gekwalificeerde hostnaam en de share zoals `\\fs1.aaddscontoso.com\CrossforestShare`.
1. Als u de schrijfmachtiging wilt valideren, klikt u met de rechtermuisknop in de map, kiest u **Nieuw**, en selecteert u vervolgens **Tekstdocument**. Gebruik de standaardnaam **Nieuw tekstdocument**.

    Als de schrijfmachtigingen juist zijn ingesteld, wordt er een nieuw tekstdocument gemaakt. Met de volgende stappen kunt u het bestand vervolgens openen, bewerken en verwijderen, indien nodig.
1. Open **Nieuw tekstdocument** om de leesmachtiging te valideren.
1. Als u de machtiging voor wijzigen wilt valideren, voegt u tekst toe aan het bestand en sluit u **Kladblok**. Wanneer u wordt gevraagd om de wijzigingen op te slaan, kiest u **Opslaan**.
1. Als u de machtiging voor verwijderen wilt valideren, klikt u met de rechtermuisknop op **Nieuw tekstdocument** en kiest u **Verwijderen**. Kies **Ja** om het verwijderen van het bestand te bevestigen.

## <a name="update-or-remove-outbound-forest-trust"></a>Uitgaande forest-vertrouwens relatie bijwerken of verwijderen

Als u een bestaande eenrichtings-uitgaande forest uit het beheerde domein wilt bijwerken, kunt u de `Get-AaddsResourceForestTrusts` scripts en gebruiken `Set-AaddsResourceForestTrust` . Deze scripts helpen u bij scenario's waarin u de beschrijvende naam of het vertrouwens wachtwoord van de forest-vertrouwens relatie wilt bijwerken. Als u een eenrichtings vertrouwensrelatie uit het beheerde domein wilt verwijderen, kunt u het script gebruiken `Remove-AaddsResourceForestTrust` . U moet de eenrichtings vertrouwensrelatie voor binnenkomende forests in het gekoppelde on-premises AD DS-forest hand matig verwijderen.

### <a name="update-a-forest-trust"></a>Een forest-vertrouwens relatie bijwerken

Normaal gesp roken onderhandelen het forest van het beheerde domein en het on-premises forest met een normaal wachtwoord update proces. Dit maakt deel uit van het beveiligings proces van de normale AD DS vertrouwens relatie. U hoeft het vertrouwens wachtwoord niet hand matig te draaien, tenzij de vertrouwens relatie een probleem heeft ondervonden en u hand matig opnieuw wilt instellen op een bekend wacht woord. Zie voor meer informatie het [vertrouwde domein object wachtwoord wijzigingen](concepts-forest-trust.md#tdo-password-changes).

In de volgende voorbeeld stappen ziet u hoe u een bestaande vertrouwens relatie bijwerkt als u het wacht woord voor uitgaande vertrouwens relaties hand matig opnieuw wilt instellen:

1. Installeer de `Get-AaddsResourceForestTrusts` `Set-AaddsResourceForestTrust` scripts en van de [PowerShell Gallery][powershell-gallery] met behulp van de cmdlet [install-script][Install-Script] :

    ```powershell
    Install-Script -Name Get-AaddsResourceForestTrusts,Set-AaddsResourceForestTrust
    ```

1. Voordat u een bestaande vertrouwens relatie kunt bijwerken, moet u eerst de vertrouwens bron ophalen met behulp van het `Get-AaddsResourceForestTrusts` script. In het volgende voor beeld wordt de bestaande vertrouwens relatie toegewezen aan een object met de naam *existingTrust*. Geef de naam van uw eigen beheerde domein-forest en de naam van de on-premises forest op die u wilt bijwerken:

    ```powershell
    $existingTrust = Get-AaddsResourceForestTrust `
        -ManagedDomainFqdn "aaddscontoso.com" `
        -TrustFqdn "onprem.contoso.com" `
        -TrustFriendlyName "myAzureADDSTrust"
    ```

1. Gebruik het script om het bestaande vertrouwens wachtwoord bij te werken `Set-AaddsResourceForestTrust` . Geef het bestaande vertrouwens object op uit de vorige stap en vervolgens een nieuw wacht woord voor de vertrouwens relatie. Er wordt geen wachtwoord complexiteit afgedwongen door Power shell, dus zorg ervoor dat u een beveiligd wacht woord voor uw omgeving genereert en gebruikt.

    ```powershell
    Set-AaddsResourceForestTrust `
        -Trust $existingTrust `
        -TrustPassword <newComplexPassword>
    ```

### <a name="delete-a-forest-trust"></a>Een forestvertrouwensrelatie verwijderen

Als u de eenrichtings vertrouwensrelatie voor uitgaande forests van het beheerde domein naar een on-premises AD DS forest niet meer nodig hebt, kunt u het verwijderen. Zorg ervoor dat er geen toepassingen of services moeten worden geverifieerd aan de hand van de on-premises AD DS-forest voordat u de vertrouwens relatie verwijdert. U moet de eenrichtings binnenkomende vertrouwens relatie ook hand matig verwijderen uit de on-premises AD DS-forest.

1. Installeer het `Remove-AaddsResourceForestTrust` script vanuit de [PowerShell Gallery][powershell-gallery] met behulp van de cmdlet [install-script][Install-Script] :

    ```powershell
    Install-Script -Name Remove-AaddsResourceForestTrust
    ```

1. Verwijder nu de vertrouwens relatie tussen forests met behulp van het `Remove-AaddsResourceForestTrust` script. In het volgende voor beeld wordt de vertrouwens relatie met de naam *myAzureADDSTrust* tussen het beheerde domein forest met de naam *aaddscontoso.com* en het *onprem.contoso.com* van het on-premises forest verwijderd. Geef de naam van uw eigen beheerde domein-forest en de naam van de on-premises forest op die u wilt verwijderen:

    ```powershell
    Remove-AaddsResourceForestTrust `
        -ManagedDomainFqdn "aaddscontoso.com" `
        -TrustFqdn "onprem.contoso.com" `
        -TrustFriendlyName "myAzureADDSTrust"
    ```

Als u de eenrichtings binnenkomende vertrouwens relatie van het on-premises AD DS-forest wilt verwijderen, maakt u verbinding met een beheer computer met toegang tot het on-premises AD DS forest en voert u de volgende stappen uit:

1. Selecteer **Start | Systeembeheer | Active Directory: domeinen en vertrouwensrelaties**
1. Selecteer met de rechtermuisknop het domein, bijvoorbeeld *onprem.contoso.com*. Selecteer vervolgens **Eigenschappen**
1. Klik op het tabblad **vertrouwens relaties** en selecteer vervolgens de bestaande binnenkomende vertrouwens relatie in het forest van het beheerde domein.
1. Selecteer **verwijderen** en bevestig dat u de binnenkomende vertrouwens relatie wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een resource forest voor een beheerd domein maken met behulp van Azure PowerShell
> * Een eenrichtings vertrouwensrelatie voor uitgaande forests in het beheerde domein maken met behulp van Azure PowerShell
> * DNS configureren in een on-premises AD DS omgeving ter ondersteuning van de connectiviteit van het beheerde domein
> * Een binnenkomende forestvertrouwensrelatie in één richting maken in een on-premises AD DS-omgeving
> * De vertrouwensrelatie testen en valideren voor verificatie en resourcetoegang

Zie [Wat zijn resourceforests?][concepts-forest] en [Werking van forestvertrouwensrelaties in Azure AD DS][concepts-trust] voor conceptuele informatie over foresttypen in Azure AD DS.

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[network-peering]: ../virtual-network/virtual-network-peering-overview.md
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[Get-AzureRMSubscription]: /powershell/module/AzureRM.Profile/Get-AzureRmSubscription
[Install-Script]: /powershell/module/powershellget/install-script

<!-- EXTERNAL LINKS -->
[powershell-gallery]: https://www.powershellgallery.com/