---
title: 'Zelfstudie: Een forestvertrouwensrelatie maken in Azure AD Domain Services | Microsoft Docs'
description: Meer informatie over het maken van een uitgaande forest in één richting naar een on-premises AD DS-domein in de Azure-portal voor Azure AD Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: faa46178262777454d4d67d23bbd0bb013974ab5
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208485"
---
# <a name="tutorial-create-an-outbound-forest-trust-to-an-on-premises-domain-in-azure-active-directory-domain-services"></a>Zelfstudie: Een uitgaande forestvertrouwensrelatie in één richting maken naar een on-premises domein in Azure Active Directory Domain Services

In omgevingen waarin u geen wachtwoordhashes kunt synchroniseren, of waarin gebruikers werken die zich alleen aanmelden via een smartcard (zodat ze hun wachtwoord niet kennen), kunt u een resourceforest gebruiken in Azure AD DS (Azure Active Directory Domain Services). Een resourceforest maakt gebruik van een uitgaande vertrouwensrelatie in één richting van Azure AD DS naar één of meer on-premises AD DS-omgevingen. Met deze vertrouwensrelatie kunnen gebruikers, toepassingen en computers worden geverifieerd in een on-premises domein vanuit het met Azure AD DS beheerde domein. Bij gebruik van een resourceforest worden hashes van on-premises wachtwoorden nooit gesynchroniseerd.

![Diagram van forestvertrouwensrelatie van Azure AD DS naar on-premises AD DS](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * DNS configureren in een on-premises AD DS-omgeving om ondersteuning te bieden voor Azure AD DS-connectiviteit
> * Een binnenkomende forestvertrouwensrelatie in één richting maken in een on-premises AD DS-omgeving
> * Een uitgaande vertrouwensrelatie in één richting maken in Azure AD DS
> * De vertrouwensrelatie testen en valideren voor verificatie en resourcetoegang

Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om deze zelfstudie te voltooien, hebt u de volgende resources en machtigingen nodig:

* Een actief Azure-abonnement.
    * Als u nog geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory-tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises map of een cloudmap.
    * [Maak zo nodig een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* Een met Azure Active Directory Domain Services beheerd domein dat is gemaakt met behulp van een resourceforest, en is geconfigureerd in uw Azure AD-tenant.
    * [Maak en configureer zo nodig een met Azure Active Directory Domain Services beheerd domein][create-azure-ad-ds-instance-advanced].
    
    > [!IMPORTANT]
    > Zorg ervoor dat u een beheerd domein maakt met behulp van een *resourceforest*. Met de standaardoptie gebruikt u een *gebruikersforest*. Vertrouwensrelaties naar on-premises AD DS-omgevingen kunnen alleen worden gemaakt met resourceforests.
    >
    > U moet ook minstens een *Enterprise* SKU gebruiken voor het beheerde domein. Indien nodig, kunt u [de SKU voor een beheerd domein wijzigen][howto-change-sku].

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

In deze zelfstudie maakt en configureert u de uitgaande forestvertrouwensrelatie vanuit Azure AD DS met behulp van de Azure-portal. Meld u eerst aan bij de [Azure-portal](https://portal.azure.com) om aan de slag te gaan.

## <a name="networking-considerations"></a>Aandachtspunten voor netwerken

Het virtuele netwerk dat als host fungeert voor het Azure AD DS-resourceforest, moet een netwerkverbinding hebben met uw on-premises Active Directory. Toepassingen en services hebben ook een netwerkverbinding nodig met het virtuele netwerk waarin de Azure AD DS-resourceforest wordt gehost. De netwerkverbinding met de Azure AD DS-resourceforest moet altijd zijn ingeschakeld en stabiel zijn, anders kunnen gebruikers mogelijk niet verifiëren of toegang krijgen tot resources.

Voordat u een forestvertrouwensrelatie configureert in Azure AD DS, moet u controleren of uw netwerken tussen de Azure-omgeving en de on-premises omgeving voldoen aan de volgende vereisten:

* Ze moeten gebruikmaken van privé-IP-adressen. Ze mogen niet afhankelijk zijn van DHCP met dynamische toewijzing van IP-adres.
* Vermijd overlappende IP-adresruimten, zodat bij peering van virtuele netwerken en routering goed kan worden gecommuniceerd tussen Azure en on-premises.
* Een virtueel Azure-netwerk heeft een gatewaysubnet nodig om een [Azure site-naar-site (S2S) VPN][vpn-gateway]- of [ExpressRoute][expressroute]-verbinding te configureren
* Maak subnetten met voldoende IP-adressen ter ondersteuning van uw scenario.
* Zorg ervoor dat Azure AD DS een eigen subnet heeft. Deel dit subnet van het virtuele netwerk niet met de toepassings-VM's en -services.
* Gekoppelde virtuele netwerken zijn NIET transitief.
    * Peering van virtuele Azure-netwerken moet worden gemaakt tussen alle virtuele netwerken waarin u de vertrouwensrelatie van de Azure AD DS-resourceforest naar de on-premises AD DS-omgeving wilt gebruiken.
* Bied continue netwerkconnectiviteit met uw on-premises Active Directory-forest. Gebruik geen verbindingen op aanvraag.
* Zorg ervoor dat er sprake is van een continue naamomzetting (DNS) tussen de naam van uw Azure AD DS-resourceforest en de naam van uw on-premises Active Directory-forest.

## <a name="configure-dns-in-the-on-premises-domain"></a>DNS configureren in het on-premises domein

Als u het beheerde domein correct wilt omzetten vanuit de on-premises omgeving, moet u mogelijk doorstuurservers toevoegen aan de bestaande DNS-server. Als u de on-premises omgeving niet hebt geconfigureerd voor communicatie met het beheerde domein, voltooit u de volgende stappen vanuit een beheerwerkstation voor het on-premises AD DS-domein:

1. Selecteer **Start**  >  **systeem beheer tools**  >  **DNS**.
1. Selecteer de DNS-server met de rechter muisknop, zoals *myAD01*, en selecteer vervolgens **Eigenschappen**.
1. Kies **Doorstuurservers** en vervolgens **Bewerken** om extra doorstuurservers toe te voegen.
1. Voeg de IP-adressen van het beheerde domein toe, bijvoorbeeld *10.0.2.4* en *10.0.2.5*.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>Een binnenkomend forestvertrouwensrelatie maken in het on-premises domein

Het on-premises AD DS-domein heeft een binnenkomende forestvertrouwensrelatie voor het beheerde domein nodig. Deze vertrouwensrelatie moet handmatig zijn gemaakt in het on-premises AD DS-domein. Deze kan niet worden gemaakt in de Azure-portal.

Als u een binnenkomende vertrouwensrelatie in het on-premises AD DS-domein wilt configureren, voltooit u de volgende stappen vanuit een beheerwerkstation voor het on-premises AD DS-domein:

1. Selecteer **Start | Systeem beheer | Active Directory domeinen en vertrouwens relaties**.
1. Klik met de rechter muisknop op domein, zoals *onprem.contoso.com*, en selecteer vervolgens **Eigenschappen**.
1. Klik op het tabblad **vertrouwens relaties** en vervolgens op **nieuw vertrouwen**.
1. Voer de naam voor de Azure AD DS-domein naam in, bijvoorbeeld *aaddscontoso.com*, en selecteer vervolgens **volgende**.
1. Selecteer de optie om een **Forestvertrouwensrelatie** te maken. Maak vervolgens een vertrouwensrelatie van het type **In één richting: binnenkomend**.
1. Kies ervoor om de vertrouwensrelatie te maken voor **Alleen dit domein**. In de volgende stap maakt u de vertrouwensrelatie in de Azure-portal voor het beheerde domein.
1. Kies voor het gebruik van **Forestdekkende verificatie**. Voer vervolgens een wachtwoord voor de vertrouwensrelatie in en bevestig dit. Dit wachtwoord wordt ook ingevoerd in de Azure-portal in de volgende sectie.
1. Doorloop de volgende paar vensters met standaardopties, en kies vervolgens de optie **Nee, uitgaande vertrouwensrelatie niet bevestigen**.
1. Selecteer **Finish**.

## <a name="create-outbound-forest-trust-in-azure-ad-ds"></a>Een uitgaande vertrouwensrelatie maken in Azure AD DS

Nu het on-premises AD DS-domein is geconfigureerd om het beheerde domein om te zetten en er een binnenkomende forestvertrouwensrelatie is gemaakt, maakt u de uitgaande vertrouwensrelatie. Met deze uitgaande forestvertrouwensrelatie is de vertrouwensrelatie tussen het on-premises AD DS-domein en het beheerde domein compleet.

Om de uitgaande vertrouwensrelatie voor het beheerde domein in de Azure-portal te maken voert u de volgende stappen uit:

1. In de Azure Portal zoekt en selecteert u **Azure AD Domain Services** en selecteert u vervolgens uw beheerde domein, zoals *aaddscontoso.com*.
1. Selecteer in het menu aan de linkerkant van het beheerde domein **Vertrouwensrelaties**, en kies vervolgens **+ Toevoegen** voor een vertrouwensrelatie.

   > [!NOTE]
   > Als u de menuoptie **Vertrouwensrelaties** niet ziet, zoekt u onder **Eigenschappen** naar het *Foresttype*. Vertrouwensrelaties kunnen alleen worden gemaakt met *resourceforests*. Als het foresttype *Gebruikers* is, kunt u geen vertrouwensrelaties maken. Het is momenteel niet mogelijk om het foresttype van een beheerd domein te wijzigen. U moet het beheerde domein verwijderen en opnieuw maken als een resourceforest.

1. Voer een weergave naam in die de vertrouwens relatie identificeert en vervolgens de naam van het on-premises vertrouwde forest-DNS, zoals *onprem.contoso.com*.
1. Geef hetzelfde wachtwoord voor de vertrouwensrelatie op dat in de vorige sectie is gebruikt bij het configureren van de binnenkomende forestvertrouwensrelatie voor het on-premises AD DS-domein.
1. Geef ten minste twee DNS-servers op voor het on-premises AD DS domein, zoals *10.1.1.4* en *10.1.1.5*.
1. Wanneer u klaar bent, **slaat** u de uitgaande forestvertrouwensrelatie op.

    ![Een uitgaande vertrouwensrelatie maken in de Azure-portal](./media/tutorial-create-forest-trust/portal-create-outbound-trust.png)

## <a name="validate-resource-authentication"></a>Verificatie van resources valideren

Met de volgende algemene scenario's kunt u valideren of met de forestvertrouwensrelatie gebruikers op de juiste wijze worden geverifieerd en toegang tot resources krijgen:

* [On-premises gebruikersverificatie vanuit de Azure AD DS-resourceforest](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [Toegang krijgen tot resources in het Azure AD DS-resourceforest met behulp van on-premises gebruiker](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [Bestands- en printerdeling inschakelen](#enable-file-and-printer-sharing)
    * [Een beveiligingsgroep maken en leden toevoegen](#create-a-security-group-and-add-members)
    * [Een bestandsshare maken voor toegang tussen forests](#create-a-file-share-for-cross-forest-access)
    * [Verificatie tussen forests valideren voor een resource](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>On-premises gebruikersverificatie vanuit de Azure AD DS-resourceforest

U moet een virtuele Windows Server-machine koppelen aan het beheerde domein. Gebruik deze virtuele machine om te testen of uw on-premises gebruiker kan worden geverifieerd op de virtuele machine. Als dat nodig is, [een Windows-VM maken en deze koppelen aan het beheerde domein][join-windows-vm].

1. Maak verbinding met de Windows Server-VM die is gekoppeld aan de Azure AD DS-resourceforest, met behulp van [Azure Bastion](../bastion/bastion-overview.md) en uw Azure AD DS-beheerdersreferenties.
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

Met behulp van de Windows Server-VM die is gekoppeld aan de Azure AD-forest kunt u het scenario testen waarbij gebruikers toegang hebben tot resources die zijn gehost in de resourceforest, wanneer ze verifiëren op computers in het on-premises domein met gebruikers van het on-premises domein. In de volgende voorbeelden ziet u hoe u verschillende veelvoorkomende scenario's kunt maken en testen.

#### <a name="enable-file-and-printer-sharing"></a>Bestands- en printerdeling inschakelen

1. Maak verbinding met de Windows Server-VM die is gekoppeld aan de Azure AD DS-resourceforest, met behulp van [Azure Bastion](../bastion/bastion-overview.md) en uw Azure AD DS-beheerdersreferenties.

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
    > U moet referenties opgeven, omdat de vertrouwensrelatie slechts één manier is. Dit betekent dat gebruikers van het beheerde Azure AD DS-domein geen toegang hebben tot resources en niet kunnen zoeken naar gebruikers of groepen in het vertrouwde (on-premises) domein.

1. De groep **Domeingebruikers** uit uw on-premises Active Directory moet lid zijn van de groep **FileServerAccess**. Selecteer **OK** om de groep op te slaan, en sluit het venster.

#### <a name="create-a-file-share-for-cross-forest-access"></a>Een bestandsshare maken voor toegang tussen forests

1. Maak op de Windows Server-VM die is gekoppeld aan de Azure AD DS-resourceforest, een map en geef een naam op, bijvoorbeeld *CrossForestShare*.
1. Klik met de rechtermuisknop op de map en kies **Eigenschappen**.
1. Selecteer het tabblad **Beveiliging** en kies vervolgens **Bewerken**.
1. Selecteer in het dialoogvenster *Machtigingen voor CrossForestShare* de optie **Toevoegen**.
1. Typ *FileServerAccess* in **Geef de objectnamen op**. Selecteer vervolgens **OK**.
1. Selecteer *FileServerAccess* in de lijst **Groepen of gebruikersnamen**. Kies in de lijst **Machtigingen voor FileServerAccess** de optie *Toestaan* voor de machtigingen **Wijzigen** en  **Schrijven**. Selecteer vervolgens **OK**.
1. Selecteer het tabblad **delen** en kies vervolgens **Geavanceerd delen...**.
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

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * DNS configureren in een on-premises AD DS-omgeving om ondersteuning te bieden voor Azure AD DS-connectiviteit
> * Een binnenkomende forestvertrouwensrelatie in één richting maken in een on-premises AD DS-omgeving
> * Een uitgaande vertrouwensrelatie in één richting maken in Azure AD DS
> * De vertrouwensrelatie testen en valideren voor verificatie en resourcetoegang

Zie [Wat zijn resourceforests?][concepts-forest] en [Werking van forestvertrouwensrelaties in Azure AD DS][concepts-trust] voor conceptuele informatie over foresttypen in Azure AD DS.

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[howto-change-sku]: change-sku.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[expressroute]: ../expressroute/expressroute-introduction.md
[join-windows-vm]: join-windows-vm.md