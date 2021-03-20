---
title: 'Zelfstudie: een beheer-VM voor Azure Active Directory Domain Services maken | Microsoft Docs'
description: In deze zelfstudie leert u hoe u een virtuele Windows-machine maakt en configureert waarmee u een door Azure Active Directory Domain Services beheerd domein kunt beheren.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: d017fc3fc53c9f89978893670df10ecd731da904
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96618175"
---
# <a name="tutorial-create-a-management-vm-to-configure-and-administer-an-azure-active-directory-domain-services-managed-domain"></a>Zelfstudie: Een beheer-VM maken om een door Azure AD Domain Services beheerd domein te configureren en te beheren

Azure AD DS (Azure Active Directory Domain Services) biedt beheerde domeinservices, zoals lid worden van een domein, groepsbeleid, LDAP en Kerberos/NTLM-verificatie, die volledig compatibel zijn met Windows Server Active Directory. U beheert dit beheerde domein met behulp van dezelfde Remote Server Administration Tools (RSAT), net als bij een on-premises domein van Active Directory Domain Services. Omdat Azure AD DS een beheerde service is, zijn er enkele beheertaken die u niet kunt uitvoeren, zoals het gebruik van Remote Desktop Protocol (RDP) om verbinding te maken met de domeincontrollers.

In deze zelfstudie leert u hoe u een Windows Server-VM configureert in Azure en de vereiste hulpprogramma's installeert voor het beheren van een door Azure AD DS beheerd domein.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Inzicht krijgen in de beschikbare beheertaken in een beheerd domein
> * De Active Directory-beheerprogramma's installeren op een Windows Server-VM
> * Algemene taken uitvoeren met behulp van het Active Directory-beheercentrum

Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om deze zelfstudie te voltooien, hebt u de volgende resources en machtigingen nodig:

* Een actief Azure-abonnement.
    * Als u nog geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory-tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises map of een cloudmap.
    * [Maak zo nodig een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* Een door Azure Active Directory Domain Services beheerd domein dat in uw Azure AD-tenant is ingeschakeld en geconfigureerd.
    * Bekijk zo nodig de eerste zelfstudie voor [het maken en configureren van een door Azure Active Directory Domain Services beheerd domein][create-azure-ad-ds-instance].
* Een Windows Server-VM die is gekoppeld aan het beheerde domein.
    * Raadpleeg, indien nodig, de vorige zelfstudie om [een Windows Server-VM te maken en deze aan een beheerd domein toe te voegen][create-join-windows-vm].
* Een gebruikersaccount dat lid is van de *Azure AD DC-beheerdersgroep* in uw Azure AD-tenant.
* Een Azure Bastion-host die is geïmplementeerd in uw virtuele Azure AD DS-netwerk.
    * [Maak een Azure Bastion-host][azure-bastion] als dat nodig is.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

In deze zelfstudie gaat u een beheer-VM maken en configureren met behulp van Azure Portal. Meld u eerst aan bij [Azure Portal](https://portal.azure.com) om aan de slag te gaan.

## <a name="available-administrative-tasks-in-azure-ad-ds"></a>Beschikbare beheertaken in Azure AD DS

Azure AD DS biedt een beheerd domein dat u kunt gebruiken voor uw gebruikers, toepassingen en services. Met deze aanpak worden enkele van de beschikbare beheertaken gewijzigd die u kunt uitvoeren, evenals de bevoegdheden die u hebt in het beheerde domein. Deze taken en machtigingen kunnen afwijken van wat u met een gebruikelijke on-premises Active Directory Domain Services-omgeving ervaart. U kunt ook geen verbinding maken met domeincontrollers op het beheerde domein met behulp van Extern bureaublad.

### <a name="administrative-tasks-you-can-perform-on-a-managed-domain"></a>Beheertaken die u kunt uitvoeren op een beheerd domein

Leden van de *AAD DC-beheerdersgroep* krijgen bevoegdheden voor het beheerde domein waarmee ze taken kunnen uitvoeren, zoals:

* Het ingebouwde groepsbeleidobject (GPO) voor de containers *AADDC Computers* en *AADDC Users* in het beheerde domein configureren.
* DNS beheren in het beheerde domein.
* Aangepaste organisatie-eenheden (OU's) maken en beheren in het beheerde domein.
* Beheerderstoegang verkrijgen tot computers die aan het beheerde domein zijn gekoppeld.

### <a name="administrative-privileges-you-dont-have-on-a-managed-domain"></a>Beheerdersbevoegdheden die u niet hebt in een beheerd domein

Het beheerde domein is vergrendeld, dus u hebt geen bevoegdheden om bepaalde beheertaken uit te voeren in het domein. Enkele van de volgende voorbeelden zijn taken die u niet kunt uitvoeren:

* Het schema van het beheerde domein uitbreiden.
* Verbinding maken met domeincontrollers voor het beheerde domein met behulp van Extern bureaublad.
* Domeincontrollers toevoegen aan het beheerde domein.
* U beschikt niet over de machtigingen van *domeinbeheerder* of *ondernemingsbeheerder* voor het beheerde domein.

## <a name="sign-in-to-the-windows-server-vm"></a>Meld u aan bij de Windows Server-VM

In de vorige zelfstudie is een Windows Server-VM gemaakt en aan het beheerde domein toegevoegd. Gebruik die VM om de beheerprogramma's te installeren. Volg, indien nodig, [de stappen in de zelfstudie om een Windows Server-VM te maken en aan een beheerd domein toe te voegen][create-join-windows-vm].

> [!NOTE]
> In deze zelfstudie gebruikt u een Windows Server-VM in Azure die is toegevoegd aan het beheerde domein. U kunt ook een Windows-client, zoals Windows 10, gebruiken die aan het beheerde domein is toegevoegd.
>
> Zie [Beheerhulpprogramma's voor externe servers (Remote Server Administration Tools, RSAT) installeren](https://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx) voor meer informatie over het installeren van de beheerprogramma's op een Windows-client

Maak als volgt verbinding met de Windows Server-VM om aan de slag te gaan:

1. Selecteer in Azure Portal aan de linkerkant de optie **Resourcegroepen**. Kies de resourcegroep waar uw VM is gemaakt, zoals *myResourceGroup*, en selecteer vervolgens de VM, zoals *myVM*.
1. Selecteer in het deelvenster **Overzicht** voor uw virtuele machine **Verbinding maken** en vervolgens **Bastion**.

    ![Verbinding maken met een virtuele Windows-machine met behulp van Bastion in Azure Portal](./media/join-windows-vm/connect-to-vm.png)

1. Voer de referenties voor uw VM in en selecteer **Verbinding maken**.

   ![Verbinding maken via de Bastion-host in Azure Portal](./media/join-windows-vm/connect-to-bastion.png)

Laat, indien nodig, uw webbrowser pop-ups openen voor de weer te geven Bastion-verbinding. Het duurt een paar seconden om de verbinding met uw virtuele machine te maken.

## <a name="install-active-directory-administrative-tools"></a>Active Directory-beheerprogramma's installeren

U gebruikt dezelfde beheerprogramma's in een beheerd domein als in on-premises AD DS-omgevingen, zoals het Active Directory-beheercentrum (ADAC) of AD PowerShell. Deze hulpprogramma's kunnen worden geïnstalleerd als onderdeel van de functie Beheerhulpprogramma's voor externe servers (Remote Server Administration Tools, RSAT) op Windows Server- en clientcomputers. Leden van de *AAD DC-beheerdersgroep* kunnen vervolgens met behulp van deze AD-beheerprogramma's beheerde domeinen op afstand beheren vanaf een computer die is toegevoegd aan het beheerde domein.

Voer de volgende stappen uit om de Active Directory-beheerprogramma's te installeren op een VM die aan het domein is toegevoegd:

1. Als **Serverbeheer** niet standaard wordt geopend wanneer u zich aanmeldt bij de VM, selecteert u het menu **Start** en kiest u vervolgens **Serverbeheer**.
1. Selecteer in het deelvenster *Dashboard* van het venster **Serverbeheer** de optie **Functies en onderdelen toevoegen**.
1. Selecteer op de pagina **Voordat u begint** van de wizard *Functies en onderdelen toevoegen* **Volgende**.
1. Voor het *installatietype* moet u de optie van het selectievakje **Installatie die op de functie of het onderdeel is gebaseerd** ingeschakeld laten en **Volgende** selecteren.
1. Kies op de pagina **Selectie van servers** de huidige VM uit de servergroep, zoals *myvm.aaddscontoso.com*, en selecteer vervolgens **Volgende**.
1. Klik op de pagina **Serverfuncties** op **Volgende**.
1. Vouw op de pagina **Functies** het knooppunt **Beheerhulpprogramma's voor externe servers** uit en vouw vervolgens het knooppunt **Hulpprogramma's voor functiebeheer** uit.

    Kies de functie **AD DS- en AD LDS-hulpprogramma's** uit de lijst met hulpprogramma's voor functiebeheer, en selecteer vervolgens **Volgende**.

    ![De AD DS- en AD LDS-hulpprogramma's vanuit de pagina Functies installeren](./media/tutorial-create-management-vm/install-features.png)

1. Selecteer op de pagina **Bevestiging** **Installeren**. Het kan een paar minuten duren voordat de beheerprogramma's zijn geïnstalleerd.
1. Wanneer de installatie van de functie is voltooid, selecteert u **Sluiten** om de wizard **Functies en onderdelen toevoegen** af te sluiten.

## <a name="use-active-directory-administrative-tools"></a>Active Directory-beheerprogramma's gebruiken

Nu de beheerprogramma's zijn geïnstalleerd, gaan we kijken hoe u deze kunt gebruiken om het beheerde domein te beheren. Zorg ervoor dat u bent aangemeld bij de virtuele machine met een gebruikersaccount dat lid is van de *AAD DC-beheerdersgroep*.

1. Selecteer in het menu **Start** **Windows Systeembeheer**. De AD-beheerprogramma's die in de vorige stap zijn geïnstalleerd, worden weergegeven.

    ![Lijst met beheerprogramma's die op de server zijn geïnstalleerd](./media/tutorial-create-management-vm/list-admin-tools.png)

1. Selecteer **Active Directory-beheercentrum**.
1. Als u het beheerde domein wilt verkennen, kiest u de domeinnaam in het linkerdeelvenster, bijvoorbeeld *aaddscontoso*. Twee containers met de naam *AADDC Computers* en *AADDC Users* bevinden zich bovenaan de lijst.

    ![Een lijst maken van het onderdeel beschikbare containers van het beheerde domein](./media/tutorial-create-management-vm/active-directory-administrative-center.png)

1. Als u de gebruikers en groepen wilt zien die tot het beheerde domein behoren, selecteert u de container **AADDC Users**. De gebruikersaccounts en -groepen van uw Azure AD-tenant worden weergegeven in deze container.

    In de volgende voorbeelduitvoer worden een gebruikersaccount met de naam *Contoso Admin* en een groep voor *AAD DC-beheerders* weergegeven in deze container.

    ![Bekijk de lijst met Azure AD DS-domeingebruikers in het Active Directory-beheercentrum](./media/tutorial-create-management-vm/list-azure-ad-users.png)

1. Selecteer de container **AADDC Computers** om de computers te zien die lid zijn van het beheerde domein. Er wordt een vermelding voor de huidige virtuele machine weergegeven, bijvoorbeeld *myVM*. Computeraccounts voor alle apparaten die lid zijn van het beheerde domein worden opgeslagen in deze container *AADDC Computers*.

Algemene acties van het Active Directory-beheercentrum, zoals het opnieuw instellen van het wachtwoord voor een gebruikersaccount of het beheren van groepslidmaatschap, zijn beschikbaar. Deze acties werken alleen voor gebruikers en groepen die rechtstreeks in het beheerde domein zijn gemaakt. Identiteitsgegevens worden alleen *van* Azure AD naar Azure AD DS gesynchroniseerd. Terugschrijven van Azure AD DS naar Azure AD vindt niet plaats. U kunt geen wachtwoorden of lidmaatschap van beheerde groepen wijzigen voor gebruikers die zijn gesynchroniseerd vanuit Azure AD en deze wijzigingen gesynchroniseerd terugkrijgen.

U kunt ook via de *Active Directory-module voor Windows PowerShell*, die is geïnstalleerd als onderdeel van het systeembeheer, algemene acties in uw beheerde domein beheren.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Inzicht krijgen in de beschikbare beheertaken in een beheerd domein
> * De Active Directory-beheerprogramma's installeren op een Windows Server-VM
> * Algemene taken uitvoeren met behulp van het Active Directory-beheercentrum

Schakel het veilige Lightweight Directory Access Protocol (LDAPS) in om veilig met uw beheerde domein te communiceren vanuit andere toepassingen.

> [!div class="nextstepaction"]
> [Veilig LDAP configureren voor uw beheerde domein](tutorial-configure-ldaps.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md