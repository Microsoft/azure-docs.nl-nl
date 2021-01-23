---
title: Wat is Windows Virtual Desktop? - Azure
description: Een overzicht van Windows Virtual Desktop.
author: Heidilohr
ms.topic: overview
ms.date: 09/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 36a15560b88c823ff2ae41f160839796bf21e4f8
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730782"
---
# <a name="what-is-windows-virtual-desktop"></a>Wat is Windows Virtual Desktop?

Windows Virtual Desktop is een virtualisatieservice voor desktops en apps die in de cloud wordt uitgevoerd.

Dit kunt u doen met Windows Virtual Desktop in Azure:

* Een Windows 10-implementatie met meerdere sessies instellen voor een volledige en schaalbare Windows 10
* Microsoft 365-apps voor ondernemingen virtualiseren en optimaliseren voor gebruik in virtuele scenario's met meerdere gebruikers
* Gratis uitgebreide beveiligingsupdates bieden voor virtuele Windows 7-desktops
* Uw bestaande Extern bureaublad-services en Windows Server-desktops en -apps meenemen op elke computer
* Zowel desktops als apps virtualiseren
* Windows 10-, Windows Server- en Windows 7-desktops en -apps beheren vanuit een geïntegreerde beheerervaring

## <a name="introductory-video"></a>Inleidende video

In deze video krijgt u meer informatie over Windows Virtual Desktop, wat het zo uniek maakt en wat er nieuw is:

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

Bekijk [onze afspeellijst](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev) voor meer video's over Windows Virtual Desktop.

## <a name="key-capabilities"></a>Belangrijkste mogelijkheden

Met Windows Virtual Desktop kunt u een schaalbare en flexibele omgeving instellen:

* Maak een volledige virtualisatieomgeving voor desktops in uw Azure-abonnement zonder extra gatewayservers uit te voeren.
* Publiceer zoveel hostgroepen als u nodig hebt voor uw verschillende werkbelastingen.
* Gebruik uw eigen installatiekopie voor productieworkloads of voer tests uit vanuit de Azure Gallery.
* Beperk kosten met gegroepeerde resources voor meerdere sessies. Met de nieuwe mogelijkheid voor meerdere sessies van Windows 10 Enterprise, exclusief voor Windows Virtual Desktop en de RDSH-rol (Remote Desktop Session Host) in Windows Server kunt u het aantal virtuele machines en de overhead voor het besturingssysteem beperken zonder dat uw gebruikers minder resources ter beschikking hebben.
* Bied individuele eigendom aan via persoonlijke (permanente) desktops.

De virtuele desktops implementeren en beheren:

* Gebruik de interfaces van Azure Portal, Windows Virtual Desktop PowerShell en REST om de hostgroepen te configureren, app-groepen te maken, gebruikers toe te wijzen en resources te publiceren.
* Publiceer volledige desktop- of individuele externe apps vanuit één hostgroep, maak individuele app-groepen voor verschillende sets van gebruikers of wijs gebruikers toe aan meerdere app-groepen om het aantal installatiekopieën te beperken.
* Beheer uw omgeving en gebruik ingebouwde gedelegeerde toegang om rollen toe te wijzen en verzamel diagnostische gegevens om de verschillende configuratie- en gebruikersfouten te begrijpen.
* Gebruik de nieuwe Diagnoseservice om problemen op te lossen.
* Beheer enkel de installatiekopieën en virtuele machines, niet de infrastructuur. U moet de Extern bureaublad-rollen niet persoonlijk beheren zoals bij Extern bureaublad-services, maar enkel de virtuele machines in uw Azure-abonnement.

U kunt ook gebruikers toewijzen en verbinden met uw virtuele desktops:

* Zodra ze zijn toegewezen kunnen gebruikers elke Windows Virtual Desktop-client opstarten om gebruikers te verbinden met hun toegewezen Windows-desktops en toepassingen. Maak verbinding vanaf elk apparaat via een native toepassing op uw apparaat of de Windows Virtual Desktop HTML5-webclient.
* Maak veilig gebruikers aan via omgekeerde verbindingen met de service, zodat u nooit een binnenkomende poort moet laten openstaan.

## <a name="requirements"></a>Vereisten

Er zijn een aantal dingen die u nodig heeft om Windows Virtual Desktop in te stellen en uw gebruikers te verbinden met hun Windows-desktops en -toepassingen.

We bieden ondersteuning voor de volgende besturingssystemen, zorg er dus voor dat u over de [juiste licenties](https://azure.microsoft.com/pricing/details/virtual-desktop/) beschikt voor de gebruikers op basis van de desktop en apps die u wilt implementeren:

|OS|Vereiste licentie|
|---|---|
|Windows 10 Enterprise met meerdere sessies of Windows 10 Enterprise|Microsoft 365 E3, E5, A3, A5, F3, Business Premium<br>Windows E3, E5, A3, A5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, A3, A5, F3, Business Premium<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019|RDS Licentie voor clienttoegang (CAL) met Softwaregarantie|

Uw infrastructuur heeft de volgende zaken nodig om Windows Virtual Desktop te ondersteunen:

* Een [Azure Active Directory](../active-directory/index.yml).
* Een Windows Server Active Directory die gesynchroniseerd is met Azure Active Directory. U kunt dit configureren met behulp van Azure AD Connect (voor hybride organisaties) of Azure AD Domain Services (voor hybride organisaties of cloudorganisaties).
  * Een Windows Server AD die met Azure Active Directory is gesynchroniseerd. De gebruiker wordt verkregen uit Windows Server AD en de virtuele machine van Windows Virtual Desktop wordt gekoppeld aan het Windows Server AD-domein.
  * Een Windows Server AD die met Azure Active Directory is gesynchroniseerd. De gebruiker wordt verkregen uit Windows Server AD en de virtuele machine van Windows Virtual Desktop wordt gekoppeld aan het Azure AD Domain Services-domein.
  * Een Azure AD Domain Services-domein. De gebruiker wordt verkregen uit Azure Active Directory en de virtuele machine van Windows Virtual Desktop wordt gekoppeld aan het Azure AD Domain Services-domein.
* Een Azure-abonnement, dat een onderliggend element moet zijn van dezelfde Azure AD-tenant, met een virtueel netwerk dat de Windows Server Active Directory of een Azure AD DS-instantie bevat of ermee is verbonden.

Gebruikersvereisten om verbinding te maken met Windows Virtual Desktop:

* De gebruiker moet worden verkregen uit dezelfde Active Directory die met Azure AD is verbonden. Windows Virtual Desktop biedt geen ondersteuning voor B2B- of MSA-accounts.
* De UPN die u gebruikt om u op Windows Virtual Desktop te abonneren, moet bestaan in het Active Directory-domein waaraan de virtuele machine is gekoppeld.

De virtuele Azure-machines die u maakt voor Windows Virtual Desktop moeten:

* [toegevoegd zijn aan een Standard-domein](../active-directory-domain-services/compare-identity-solutions.md) of [toegevoegd zijn aan Hybrid AD](../active-directory/devices/hybrid-azuread-join-plan.md). Virtuele machines kunnen mogen niet toegevoegd zijn aan Azure AD.
* Een van de volgende [ondersteunde installatiekopieën van een besturingssysteem](#supported-virtual-machine-os-images) uitvoeren.

>[!NOTE]
>Als u een Azure-abonnement nodig heeft, dan kunt u [zich registreren voor een gratis proefversie van een maand](https://azure.microsoft.com/free/). Als u de gratis proefversie van Azure gebruikt, moet u Azure AD Domain Services gebruiken om uw Windows Server Active Directory gesynchroniseerd te houden met Azure Active Directory.

Zie onze [Lijst met veilige URL's](safe-url-list.md) voor een lijst van URL's die u dient te deblokkeren opdat uw Windows Virtual Desktop-implementatie naar behoren kan werken.

Windows Virtual Desktop bestaat uit de Windows-desktops en -apps die u levert aan gebruikers en de beheersoplossing, die door Microsoft als een service wordt gehost in Azure. Desktops en apps kunnen worden geïmplementeerd op virtuele machines in elke Azure-regio. De beheersoplossing en gegevens voor deze VM's bevinden zich dan in de Verenigde Staten. Dit kan als gevolg hebben dat er gegevens worden overgedragen naar de Verenigde Staten.

Zorg ervoor dat uw netwerk aan de volgende vereisten voldoet voor optimale prestaties:

* De retourlatentie tussen het netwerk van de client en de Azure-regio waar de hostgroepen zijn geïmplementeerd, moeten minder dan 150 ms bedragen. Gebruik de [ervaringsschatting](https://azure.microsoft.com/services/virtual-desktop/assessment) om de status van uw verbinding en de aanbevolen Azure-regio weer te geven.
* Netwerkverkeer kan buiten de grenzen van het land/de regio stromen wanneer virtuele machines die desktops en apps hosten, verbinding maken met de beheerservice.
* Voor optimale netwerkprestaties raden we aan dat de virtuele machines van de host van de sessie zich in dezelfde Azure-regio bevinden als de beheerservice.

U kunt een typische architectuur van Windows Virtual Desktop voor bedrijven bekijken in onze [documentatie over architectuur](/azure/architecture/example-scenario/wvd/windows-virtual-desktop).

## <a name="supported-remote-desktop-clients"></a>Ondersteunde Extern bureaublad-clients

De volgende Extern bureaublad-clients bieden ondersteuning voor Windows Virtual Desktop:

* [Windows Desktop](connect-windows-7-10.md)
* [Web](connect-web.md)
* [MacOS](connect-macos.md)
* [iOS](connect-ios.md)
* [Android](connect-android.md)
* Microsoft Store-client

> [!IMPORTANT]
> Windows Virtual Desktop biedt geen ondersteuning voor de client voor RemoteApp- en bureaubladverbindingen of de client voor verbinding met extern bureaublad.

Zie de [Lijst met veilige URL's](safe-url-list.md) voor meer informatie over URL's die u moet deblokkeren om de clients te gebruiken.

## <a name="supported-virtual-machine-os-images"></a>Ondersteunde installatiekopieën van besturingssystemen voor virtuele machines

Windows Virtual Desktop biedt ondersteuning voor de volgende installatiekopieën van x64-besturingssystemen:

* Windows 10 Enterprise voor meerdere sessies, versie 1809 of hoger
* Windows 10 Enterprise, versie 1809 of hoger
* Windows 7 Enterprise
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

Windows Virtual Desktop biedt geen ondersteuning voor x86-(32-bits), Windows 10 Enter prise N-, Windows 10 Pro-of Windows 10 Enter prise-besturings systeem installatie kopieën. Windows 7 biedt ook geen ondersteuning voor VHD- of VHDX-gebaseerde profieloplossingen die gehost worden op beheerde Azure Storage omwille van een beperking van de sectorgrootte.

De beschikbare opties voor automatisering en implementatie zijn afhankelijk van het besturingssysteem en de versie die u kiest, zoals te zien is in de volgende tabel:

|Besturingssysteem|Azure Image Gallery|Handmatige VM-implementatie|Integratie van Azure Resource Manager-sjabloon|Inrichting hostgroepen in Azure Marketplace|
|--------------------------------------|:------:|:------:|:------:|:------:|
|Windows 10 Enterprise (voor meerdere sessies), versie 2004|Ja|Ja|Ja|Ja|
|Windows 10 Enterprise (voor meerdere sessies), versie 1909|Ja|Ja|Ja|Ja|
|Windows 10 Enterprise (voor meerdere sessies), versie 1903|Ja|Ja|Nee|Nee|
|Windows 10 Enterprise (voor meerdere sessies), versie 1809|Ja|Ja|Nee|Nee|
|Windows 7 Enterprise|Ja|Ja|Nee|Nee|
|Windows Server 2019|Ja|Ja|Nee|Nee|
|Windows Server 2016|Ja|Ja|Ja|Ja|
|Windows Server 2012 R2|Ja|Ja|Nee|Nee|

## <a name="next-steps"></a>Volgende stappen

Als u de Windows Virtual Desktop-versie (klassiek) gebruikt, ga dan aan de slag met onze zelfstudie op [Een tenant maken in Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).

Als u de Windows Virtual Desktop in combinatie met Azure Resource Manager-integratie gebruikt, dan moet u in de plaats daarvan een hostgroep maken. Ga naar de volgende zelfstudie om te beginnen.

> [!div class="nextstepaction"]
> [Een hostpool maken met de Azure-portal](create-host-pools-azure-marketplace.md)