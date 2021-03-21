---
title: Een virtuele Windows Server-machine toevoegen aan een door Azure AD Domain Services beheerd domein | Microsoft Docs
description: In deze zelfstudie krijgt u informatie over het toevoegen van een virtuele Windows-machine aan een door Azure Active Directory Domain Services beheerd domein.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 869c827485d9b7a6baf68d2619af98d4c2ee82b9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619569"
---
# <a name="tutorial-join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Zelfstudie: Een virtuele Windows-machine toevoegen aan een door Azure Active Directory Domain Services beheerd domein

Azure AD DS (Azure Active Directory Domain Services) biedt beheerde domeinservices, zoals lid worden van een domein, groepsbeleid, LDAP, Kerberos/NTLM-verificatie, die volledig compatibel zijn met Windows Server Active Directory. Met een door Azure AD DS beheerd domein, kunt u domeindeelnamefuncties en -beheer bieden voor virtuele machines (VM's) in Azure. In deze zelfstudie ziet u hoe u een Windows Server-VM maakt en deze vervolgens toevoegt aan een beheerd domein.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Windows Server-VM maken
> * De virtuele Windows Server-machine verbinden met een virtueel Azure-netwerk
> * De VM aan het beheerde domein toevoegen

Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u de volgende resources nodig:

* Een actief Azure-abonnement.
    * Als u nog geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory-tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises map of een cloudmap.
    * [Maak zo nodig een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* Een door Azure Active Directory Domain Services beheerd domein dat in uw Azure AD-tenant is ingeschakeld en geconfigureerd.
    * [Maak en configureer, indien nodig, een door Azure Active Directory Domain Services beheerd domein][create-azure-ad-ds-instance].
* Een gebruikersaccount dat deel uitmaakt van het beheerde domein.
    * Zorg ervoor dat de Azure AD Connect-wachtwoordhash is gesynchroniseerd of de self-service voor wachtwoordherstel is uitgevoerd, zodat het account zich kan aanmelden bij het beheerde domein.
* Een Azure Bastion-host die is geïmplementeerd in uw virtuele Azure AD DS-netwerk.
    * [Maak een Azure Bastion-host][azure-bastion] als dat nodig is.

Als u al een virtuele machine hebt die u lid wilt maken van een domein, slaat u de sectie over en voegt u [de virtuele machine toe aan het beheerde domein](#join-the-vm-to-the-managed-domain).

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

In deze zelfstudie maakt u een Windows Server-VM die u wilt toevoegen aan uw beheerde domein met behulp van Azure Portal. Meld u eerst aan bij [Azure Portal](https://portal.azure.com) om aan de slag te gaan.

## <a name="create-a-windows-server-virtual-machine"></a>Een virtuele Windows Server-machine maken

We gaan een virtuele Windows Server-machine maken om te tonen hoe u een computer kunt toevoegen aan een beheerd domein. Deze VM is verbonden met een virtueel Azure-netwerk dat verbinding maakt met het beheerde domein. Het proces om lid te worden van een beheerd domein is hetzelfde als het lid worden van een gewoon on-premises Active Directory Domain Services-domein.

Als u al een virtuele machine hebt die u lid wilt maken van een domein, slaat u de sectie over en voegt u [de virtuele machine toe aan het beheerde domein](#join-the-vm-to-the-managed-domain).

1. Selecteer in het menu van Azure Portal of op de **startpagina** de optie **Een resource maken**.

1. Kies **Windows Server 2016 Datacenter** vanuit **Aan de slag gaan**.

    ![Kies voor het maken van een VM met Windows Server 2016 Datacenter in Azure Portal](./media/join-windows-vm/select-vm-image.png)

1. Configureer in het venster **Basisinstellingen** de basisinstellingen voor de virtuele machine. Behoud de standaardinstellingen voor *Beschikbaarheidsopties*, *Installatiekopie* en *Grootte*.

    | Parameter            | Voorgestelde waarde   |
    |----------------------|-------------------|
    | Resourcegroep       | Selecteer of maak een resourcegroep zoals *myResourceGroup* |
    | Naam van de virtuele machine | Voer een naam in voor de VM, zoals *myVM* |
    | Regio               | Kies de regio waar u uw VM wilt maken, zoals *US - oost* |
    | Gebruikersnaam             | Voer een gebruikersnaam, zoals *azureuser*, in voor het account van de lokale beheerder om items op de VM te maken |
    | Wachtwoord             | Voer een veilig wachtwoord in voor de lokale beheerder om items op de VM te maken, en bevestig dit. Geef geen referenties op voor een account van domeingebruikers. |

1. Virtuele machines die in Azure worden gemaakt, zijn standaard toegankelijk via internet met behulp van RDP. Als RDP is ingeschakeld, worden er waarschijnlijk geautomatiseerde aanmeldingsaanvallen uitgevoerd, waardoor accounts met algemene namen, zoals *admin* of *beheerder*, mogelijk worden uitgeschakeld als gevolg van meerdere mislukte opeenvolgende aanmeldingspogingen.

    RDP moet alleen worden ingeschakeld als dit vereist is, en moet worden beperkt tot een reeks geautoriseerde IP-bereiken. Met deze configuratie wordt de beveiliging van de virtuele machine verbeterd en wordt het gebied voor mogelijke aanvallen verminderd. U kunt ook een Azure Bastion-host maken en gebruiken die alleen toegankelijk is door middel van Azure Portal via TLS. In de volgende stap van deze zelfstudie gebruikt u een Azure Bastion-host om veilig verbinding te maken met de virtuele machine.

    Bij **Openbare poorten voor inkomend verkeer** selecteert u *Geen*.

1. Wanneer u klaar bent, selecteert u **Volgende: Schijven**.
1. Kies in de vervolgkeuzelijst voor het **schijftype met het besturingssysteem** *Standard - SSD* en selecteer vervolgens **Volgende: Netwerken**.
1. Uw virtuele machine moet verbinding maken met een subnet van het virtuele Azure-netwerk dat kan communiceren met het subnet waarin uw beheerde domein is geïmplementeerd. Het is raadzaam dat u een beheerd domein hebt geïmplementeerd in een eigen toegewezen subnet. Implementeer uw virtuele machine niet in hetzelfde subnet als uw beheerde domein.

    Er zijn twee hoofdmanieren om uw virtuele machine te implementeren en verbinding te laten maken met een geschikt subnet van het virtuele netwerk:
    
    * Maak of selecteer een bestaand subnet in hetzelfde virtuele netwerk als waar uw beheerde domein wordt geïmplementeerd.
    * Selecteer een subnet in een virtueel Azure-netwerk dat ermee is verbonden met behulp van [peering van virtuele Azure-netwerken][vnet-peering].
    
    Als u een subnet van een virtueel netwerk selecteert dat niet is verbonden met het subnet voor uw beheerde domein, kunt u de VM niet toevoegen aan het beheerde domein. Voor deze zelfstudie maken we een nieuw subnet in het virtuele Azure-netwerk.

    Selecteer in het deelvenster **Netwerken** het virtuele netwerk waarin uw beheerde domein is geïmplementeerd, zoals *aaads-vnet*
1. In dit voorbeeld wordt het bestaande *aaads-subnet* weergegeven waarmee het beheerde domein is verbonden. Verbind uw virtuele machine niet met dit subnet. Als u een subnet voor de virtuele machine wilt maken, selecteert u **Subnetconfiguratie beheren**.

    ![Kies ervoor om de configuratie van het subnet in Azure Portal te beheren](./media/join-windows-vm/manage-subnet.png)

1. Selecteer in het menu aan de linkerkant van het venster Virtueel netwerk **Adresruimte**. Het virtuele netwerk is gemaakt met één adresruimte van *10.0.2.0/24*, dat wordt gebruikt door het standaardsubnet. Andere subnetten, zoals voor *workloads* of Azure Bastion bestaan mogelijk al.

    Voeg een extra IP-adresbereik toe aan het virtuele netwerk. De grootte van dit adresbereik en het daadwerkelijke te gebruiken IP-adresbereik is afhankelijk van andere netwerkresources die al zijn geïmplementeerd. Het IP-adresbereik mag geen overlap vertonen met de bestaande adresbereiken in uw Azure-omgeving of on-premises omgeving. Zorg ervoor dat u het IP-adresbereik voldoende groot hebt gemaakt voor het aantal VM's dat u in het subnet verwacht te implementeren.

    In het volgende voorbeeld wordt een extra IP-adresbereik van *10.0.5.0/24* toegevoegd. Selecteer **Opslaan** wanneer u klaar bent.

    ![Een extra IP-adresbereik toevoegen voor het virtuele netwerk in Azure Portal](./media/join-windows-vm/add-vnet-address-range.png)

1. Selecteer vervolgens in het menu aan de linkerkant van het venster Virtueel netwerk **Subnetten** en kies vervolgens **+ Subnet** om een subnet toe te voegen.

1. Selecteer **+ Subnet** en voer een naam in voor het subnet, zoals *beheer*. Geef een **Adresbereik (CIDR-blok)** op, zoals *10.0.5.0/24*. Zorg ervoor dat dit IP-adresbereik geen overlap vertoont met andere, bestaande Azure-adresbereiken of on-premises adresbereiken. Houd voor de overige opties de standaardwaarden aan en selecteer daarna **OK**.

    ![Een subnetconfiguratie maken in Azure Portal](./media/join-windows-vm/create-subnet.png)

1. Het duurt een paar seconden voordat het subnet is gemaakt. Zodra het is gemaakt, selecteert u de *X* om het subnetvenster te sluiten.
1. Ga terug naar het deelvenster **Netwerken** om een virtuele machine te maken, kies het subnet dat u hebt gemaakt in de vervolgkeuzelijst, zoals *Beheer*. Zorg er nogmaals voor dat u het juiste subnet kiest en uw virtuele machine niet in hetzelfde subnet implementeert als uw beheerde domein.
1. Selecteer *Geen* in de vervolgkeuzelijst bij **Openbaar IP**. Wanneer u in deze zelfstudie Azure Bastion gebruikt om verbinding te maken met het beheer, hoeft er geen openbaar IP-adres te zijn toegewezen aan de VM.
1. Houd voor de overige opties de standaardwaarden aan en selecteer daarna **Beheer**.
1. Stel **Diagnostische gegevens over opstarten** in op *Uit*. Houd voor de overige opties de standaardwaarden aan, en selecteer daarna **Controleren en maken**.
1. Controleer de instellingen van de VM en selecteer vervolgens **Maken**.

Het duurt een paar minuten om de virtuele machine te maken. In Azure Portal wordt de status van de implementatie weergegeven. Zodra de VM klaar is, selecteert u **Ga naar resource**.

![Ga naar de VM-resource in Azure Portal zodra deze is gemaakt](./media/join-windows-vm/vm-created.png)

## <a name="connect-to-the-windows-server-vm"></a>Verbinding maken met de Windows Server-VM

Gebruik een Azure Bastion-host om veilig verbinding te maken met uw VM's. Met Azure Bastion wordt een beheerde host geïmplementeerd in uw virtuele netwerk, waarmee wordt voorzien in webgebaseerde RDP- of SSH-verbindingen met VM's. Er zijn geen openbare IP-adressen vereist voor de VM's en u hoeft geen regels voor netwerkbeveiligingsgroepen te openen voor extern verkeer. U maakt verbinding met virtuele machines met behulp van Azure Portal vanuit uw webbrowser. [Maak een Azure Bastion-host][azure-bastion] als dat nodig is.

Als u een Bastion-host wilt gebruiken om verbinding te maken met uw virtuele machine, voert u de volgende stappen uit:

1. Selecteer in het deelvenster **Overzicht** voor uw virtuele machine **Verbinding maken** en vervolgens **Bastion**.

    ![Verbinding maken met een virtuele Windows-machine met behulp van Bastion in Azure Portal](./media/join-windows-vm/connect-to-vm.png)

1. Voer de referenties in voor uw virtuele machine die u in de vorige sectie hebt opgegeven en selecteer vervolgens **Verbinding maken**.

   ![Verbinding maken via de Bastion-host in Azure Portal](./media/join-windows-vm/connect-to-bastion.png)

Laat, indien nodig, uw webbrowser pop-ups openen voor de weer te geven Bastion-verbinding. Het duurt een paar seconden om de verbinding met uw virtuele machine te maken.

## <a name="join-the-vm-to-the-managed-domain"></a>De VM aan het beheerde domein toevoegen

Nu de virtuele machine is gemaakt en een webgebaseerde RDP-verbinding tot stand is gebracht met behulp van Azure Bastion, gaan we nu de Windows Server-VM aan het beheerde domein toevoegen. Het proces is gelijk als die van een computer die verbinding maakt met een gewoon on-premises Active Directory Domain Services-domein.

1. Als **Serverbeheer** niet standaard wordt geopend wanneer u zich aanmeldt bij de VM, selecteert u het menu **Start** en kiest u vervolgens **Serverbeheer**.
1. Selecteer in het linkerdeelvenster van het venster **Serverbeheer** de optie **Lokale server**. Kies onder **Eigenschappen** in het rechterdeelvenster **Werkgroep**.

    ![Serverbeheer openen op de VM en de werkgroepeigenschap bewerken](./media/join-windows-vm/server-manager.png)

1. Selecteer in het venster **Systeemeigenschappen** **Wijzigen** om lid te worden van het beheerde domein.

    ![Kiezen om de eigenschappen van de werkgroep of het domein te wijzigen](./media/join-windows-vm/change-domain.png)

1. Geef in het vak **Domein** de naam op van uw beheerde domein, zoals *aaddscontoso.com* en selecteer vervolgens **OK**.

    ![Het beheerde domein opgeven om lid te worden](./media/join-windows-vm/join-domain.png)

1. Voer de domeinreferenties in om lid te worden van het domein. Geef referenties op voor een gebruiker die deel uitmaakt van het beheerde domein. Het account moet deel uitmaken van het beheerde domein of de Azure AD-tenant; accounts uit externe mappen die zijn gekoppeld aan uw Azure AD-tenant kunnen niet juist worden geverifieerd tijdens het domeindeelnameproces.

    U kunt de accountreferenties op een van de volgende manieren opgeven:

    * **UPN-indeling** (aanbevolen): Voer het achtervoegsel van de user principal name (UPN) in voor het gebruikersaccount, zoals deze is geconfigureerd in Azure AD. Het UPN-achtervoegsel van de gebruiker *contosoadmin* is bijvoorbeeld `contosoadmin@aaddscontoso.onmicrosoft.com`. Er zijn enkele veelvoorkomende situaties waarbij de UPN-indeling veilig kan worden gebruikt om u aan te melden bij het domein in plaats van de *SAMAccountName*-indeling:
        * Als het UPN-voorvoegsel van een gebruiker lang is, zoals *deehasareallylongname*, kan de *SAMAccountName* automatisch worden gegenereerd.
        * Als meerdere gebruikers hetzelfde UPN-voorvoegsel hebben in uw Azure AD-Tenant, zoals *dee*, kan de *SAMAccountName*-indeling automatisch worden gegenereerd.
    * **SAMAccountName-indeling**: Geef de accountnaam op in de *SAMAccountName*-indeling. De *SAMAccountName* van de gebruiker *contosoadmin* is bijvoorbeeld `AADDSCONTOSO\contosoadmin`.

1. Het duurt een paar seconden om deze aan het beheerde domein toe te voegen. Wanneer dit is voltooid, wordt u met het volgende bericht welkom geheten bij het domein:

    ![Welkom bij het domein](./media/join-windows-vm/join-domain-successful.png)

    Selecteer **OK** om door te gaan.

1. Start de VM opnieuw op om het proces te voltooien om lid te worden van het beheerde domein.

> [!TIP]
> U kunt een virtuele machine met behulp van PowerShell aan een domein toevoegen met de cmdlet [Add-Computer][add-computer]. In het volgende voorbeeld wordt het domein *AADDSCONTOSO* toegevoegd en wordt de virtuele machine opnieuw opgestart. Voer desgevraagd de referenties in voor een gebruiker die deel uitmaakt van het beheerde domein:
>
> `Add-Computer -DomainName AADDSCONTOSO -Restart`
>
> Als u een virtuele machine aan een domein wilt toevoegen zonder ermee verbinding te maken en de verbinding handmatig te configureren, kunt u de cmdlet [Set-AzVmAdDomainExtension][set-azvmaddomainextension] van Azure PowerShell gebruiken.

Zodra de Windows Server-VM opnieuw is opgestart, worden alle beleidsregels die in het beheerde domein worden toegepast naar de VM gepusht. U kunt zich nu ook aanmelden bij de virtuele Windows Server-machine met behulp van de juiste domeinreferenties.

## <a name="clean-up-resources"></a>Resources opschonen

In de volgende zelfstudie gebruikt u deze Windows Server-VM om de beheerprogramma's te installeren waarmee u het beheerde domein kunt beheren. Als u niet wilt doorgaan in deze reeks zelfstudies, raadpleegt u de volgende stappen om [de VM te verwijderen](#delete-the-vm). Ga anders [verder met de volgende zelfstudie](#next-steps).

### <a name="unjoin-the-vm-from-the-managed-domain"></a>De VM loskoppelen van het beheerde domein

Als u de virtuele machine uit het beheerde domein wilt verwijderen, volgt u weer de stappen om de virtuele machine [toe te voegen aan een domein](#join-the-vm-to-the-managed-domain). In plaats van deze aan het beheerde domein toe te voegen, kiest u ervoor om deze aan een werkgroep toe te voegen, zoals de standaard *WORKGROUP*. Nadat de VM opnieuw is opgestart, wordt het computerobject verwijderd uit het beheerde domein.

Als u de [VM](#delete-the-vm) verwijdert zonder de toevoeging aan het domein ongedaan te maken, blijft een zwevend computerobject achter in Azure AD DS.

### <a name="delete-the-vm"></a>De VM verwijderen

Als u deze Windows Server-VM niet gaat gebruiken, verwijdert u de VM via de volgende stappen:

1. Selecteer **Resourcegroepen** in het linkermenu
1. Kies uw resourcegroep, bijvoorbeeld *myResourceGroup*.
1. Kies uw VM, bijvoorbeeld *myVM*, en selecteer **Verwijderen**. Selecteer **Ja** om de verwijdering van de resource te bevestigen. Het duurt een paar minuten om de VM te verwijderen.
1. Wanneer de VM wordt verwijderd, selecteert u de schijf met het besturingssysteem, de netwerkinterfacekaart en eventuele andere resources met het voorvoegsel *myVM* en verwijdert u deze.

## <a name="troubleshoot-domain-join-issues"></a>Problemen met domeindeelname oplossen

De Windows Server-VM moet worden toegevoegd aan het beheerde domein, op dezelfde manier als een normale on-premises computer zou worden toegevoegd aan een Active Directory Domain Services domein. Als de Windows Server-VM geen lid kan worden van het beheerde domein, wordt hiermee aangegeven dat er een probleem is met de connectiviteit of met betrekking tot referenties. Raadpleeg de volgende secties voor probleemoplossing om lid te worden van het beheerde domein.

### <a name="connectivity-issues"></a>Connectiviteitsproblemen

Als u niet wordt om referenties om lid te kunnen worden van het domein, is er een probleem met de verbinding. De VM kan het beheerde domein niet bereiken op het virtuele netwerk.

Probeer, als u elk van deze probleemoplossingsstappen hebt geprobeerd, de Windows Server-VM opnieuw aan het beheerde domein toe te voegen.

* Controleer of de VM is verbonden met hetzelfde virtuele netwerk waarin Azure AD DS is ingeschakeld, of over een gekoppelde netwerkverbinding beschikt.
* Voer een ping uit voor de DNS-domeinnaam van het beheerde domein, zoals `ping aaddscontoso.com`.
    * Als de ping-aanvraag mislukt, probeert u de IP-adressen voor het beheerde domein te pingen, zoals `ping 10.0.0.4`. Het IP-adres voor uw omgeving wordt weergegeven op de pagina *Eigenschappen* wanneer u het beheerde domein selecteert in uw lijst met Azure-resources.
    * Als u het IP-adres kunt pingen, maar niet het domein, is het DNS mogelijk onjuist geconfigureerd. Controleer of de IP-adressen van het beheerde domein zijn geconfigureerd als DNS-servers voor het virtuele netwerk.
* Probeer de cache van de DNS-resolver op de virtuele machine leeg te maken met behulp van de `ipconfig /flushdns`-opdracht.

### <a name="credentials-related-issues"></a>Problemen met betrekking tot referenties

Als u wordt gevraagd om referenties voor deelname aan het domein, maar vervolgens een foutmelding wordt weergegeven nadat u deze referenties hebt ingevoerd, kan de virtuele machine verbinding maken met het beheerde domein. Met de referenties die u hebt ingevoerd, kan de VM niet aan het beheerde domein worden toegevoegd.

Probeer, als u elk van deze probleemoplossingsstappen hebt geprobeerd, de Windows Server-VM opnieuw aan het beheerde domein toe te voegen.

* Zorg ervoor dat het gebruikersaccount dat u opgeeft, behoort tot het beheerde domein.
* Controleer of het account deel uitmaakt van het beheerde domein of de Azure AD-tenant. Accounts uit externe mappen die zijn gekoppeld aan uw Azure AD-tenant, kunnen niet juist worden geverifieerd tijdens het domeindeelnameproces.
* Gebruik de UPN-indeling om referenties op te geven, zoals `contosoadmin@aaddscontoso.onmicrosoft.com`. Als er veel gebruikers zijn met hetzelfde UPN-voorvoegsel in uw tenant, of als uw UPN-voorvoegsel te lang is, kan de *SAMAccountName* voor uw account automatisch worden gegenereerd. In dergelijke gevallen kan de *SAMAccountName*-indeling voor uw account afwijken van wat u verwacht of gebruikt in uw on-premises domein.
* Controleer of [wachtwoordsynchronisatie is ingeschakeld][password-sync] voor uw beheerde domein. Zonder deze configuratiestap zijn de vereiste wachtwoordhashes niet aanwezig in het beheerde domein om uw aanmeldingspoging correct te verifiëren.
* Wacht tot de wachtwoordsynchronisatie is voltooid. Wanneer het wachtwoord van een gebruikersaccount wordt gewijzigd, wordt met een automatische achtergrondsynchronisatie vanuit Azure AD het wachtwoord in Azure AD DS bijgewerkt. Het duurt enige tijd voordat het wachtwoord kan worden gebruikt voor deelname aan een domein.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een Windows Server-VM maken
> * De virtuele Windows Server-machine verbinden met een virtueel Azure-netwerk
> * De VM aan het beheerde domein toevoegen

Als u uw beheerde domein wilt beheren, configureert u een beheer-VM met behulp van het Active Directory-beheercentrum (ADAC).

> [!div class="nextstepaction"]
> [Beheerprogramma's installeren op een beheer-VM](tutorial-create-management-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[password-sync]: ./tutorial-create-instance.md
[add-computer]: /powershell/module/microsoft.powershell.management/add-computer
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[set-azvmaddomainextension]: /powershell/module/az.compute/set-azvmaddomainextension