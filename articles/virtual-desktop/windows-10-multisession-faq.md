---
title: Veelgestelde vragen over Windows 10 Enter prise multi-session-Azure
description: Veelgestelde vragen en aanbevolen procedures voor het gebruik van Windows 10 Enter prise multi-session voor virtueel bureau blad van Windows.
author: Heidilohr
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5c03d3f9769aec0736d23f18372701e08ad93dac
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802791"
---
# <a name="windows-10-enterprise-multi-session-faq"></a>Veelgestelde vragen over meervoudige Windows 10 Enterprise-sessies

In dit artikel vindt u antwoorden op veelgestelde vragen en worden aanbevolen procedures voor Windows 10 Enter prise multi-session beschreven.

## <a name="what-is-windows-10-enterprise-multi-session"></a>Wat is Windows 10 Enter prise multi-session?

Windows 10 Enter prise multi-session, voorheen bekend als Windows 10 Enter prise voor Virtual desktops (EVD), is een nieuwe Extern bureaublad sessiehost waarmee meerdere gelijktijdige interactieve sessies kunnen worden toegestaan. Voorheen kan dit alleen door Windows Server worden gedaan. Met deze mogelijkheid hebben gebruikers een bekende Windows 10-ervaring, terwijl ze kunnen profiteren van de kosten voordelen van meerdere sessies en bestaande Windows-licenties per gebruiker gebruiken in plaats van RDS-licenties voor client toegang. Zie [prijzen voor Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/)voor meer informatie over licenties en prijzen.

## <a name="how-many-users-can-simultaneously-have-an-interactive-session-on-windows-10-enterprise-multi-session"></a>Hoeveel gebruikers kunnen tegelijkertijd een interactieve sessie op Windows 10 Enter prise Multi-Session hebben?

Hoeveel interactieve sessies tegelijk actief kunnen zijn, is afhankelijk van de hardwarebronnen van uw systeem (vCPU, geheugen, schijf en vGPU), hoe uw gebruikers hun apps gebruiken terwijl ze zich aanmelden bij een sessie en hoe zwaar de werk belasting van uw systeem is. We raden u aan om de prestaties van uw systeem te valideren om inzicht te krijgen in het aantal gebruikers dat u kunt hebben met Windows 10 Enter prise multi-session. Zie [prijzen voor Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/)voor meer informatie.

## <a name="why-does-my-application-report-windows-10-enterprise-multi-session-as-a-server-operating-system"></a>Waarom rapporteert mijn toepassing Windows 10 Enter prise multi-session als een server besturingssysteem?

Windows 10 Enter prise multi-session is een virtuele editie van Windows 10 Enter prise. Een van de verschillen is dat dit besturings systeem (OS) de [product type](/windows/win32/cimwin32prov/win32-operatingsystem) rapporteert als een waarde van 3, dezelfde waarde als Windows Server. Met deze eigenschap blijft het besturings systeem compatibel met bestaande hulpprogram ma's voor het uitvoeren van RDSH-beheer, toepassingen met meerdere sessies op basis van RDSH en voornamelijk systeem prestatie optimalisaties op laag niveau voor RDSH-omgevingen. Sommige installatie Programma's van toepassingen kunnen de installatie van Windows 10 multi-session blok keren, afhankelijk van of ze de product type detecteren, is ingesteld op client. Als uw app niet kan worden geïnstalleerd, neemt u contact op met de leverancier van de toepassing voor een bijgewerkte versie.

## <a name="can-i-run-windows-10-enterprise-multi-session-on-premises"></a>Kan ik on-premises Windows 10 Enter prise multi-session uitvoeren?

Windows 10 Enter prise multi-session kan niet worden uitgevoerd in een on-premises productie omgeving omdat het is geoptimaliseerd voor de virtueel-bureaublad service van Windows voor Azure. U kunt de gebruiksrecht overeenkomst gebruiken om een multi-sessie van Windows 10 Enter prise uit te voeren buiten Azure voor productie doeleinden. Windows 10 Enter prise multi-session kan niet worden geactiveerd op basis van on-premises Key Management Services (KMS).

## <a name="can-i-upgrade-a-windows-10-vm-to-windows-10-enterprise-multi-session"></a>Kan ik een upgrade uitvoeren van een Windows 10 VM naar Windows 10 Enter prise multi-session?

Nee. Het is momenteel niet mogelijk een upgrade uit te voeren van een bestaande virtuele machine (VM) met Windows 10 Professional of ENTER prise naar Windows 10 Enter prise multi-session. Als u een virtuele machine met Windows 10 Enter prise met meerdere sessies implementeert en de product code vervolgens bijwerkt naar een andere versie, kunt u de VM niet weer overschakelen naar Windows 10 Enter prise Multi-Session en moet u de virtuele machine opnieuw implementeren.

## <a name="how-do-i-customize-the-windows-10-enterprise-multi-session-image-for-my-organization"></a>Hoe kan ik de Windows 10 Enter prise-installatie kopie voor meerdere sessies aanpassen voor mijn organisatie?

U kunt een virtuele machine in azure starten met Windows 10 Enter prise Multi-Session en deze aanpassen door LOB-toepassingen, sysprep/generalize te installeren en vervolgens een installatie kopie te maken met behulp van de Azure Portal.

Om aan de slag te gaan, maakt u een virtuele machine in azure met Windows 10 Enter prise multi-session. In plaats van de virtuele machine in azure te starten, kunt u de VHD rechtstreeks downloaden. Daarna kunt u de VHD die u hebt gedownload gebruiken om een nieuwe generatie 1 VM te maken op een Windows 10-computer waarop Hyper-V is ingeschakeld.

Pas de installatie kopie aan uw behoeften aan door LOB-toepassingen te installeren en Sysprep uit te voeren op de installatie kopie. Wanneer u klaar bent met aanpassen, uploadt u de installatie kopie naar Azure met de VHD in. Daarna kunt u Windows virtueel bureau blad ophalen van Azure Marketplace en gebruiken om een nieuwe hostgroep te implementeren met de aangepaste installatie kopie.

## <a name="how-do-i-manage-windows-10-enterprise-multi-session-after-deployment"></a>Hoe kan ik Windows 10 Enter prise multi-session beheren na de implementatie?

U kunt elk ondersteund configuratie programma gebruiken, maar we raden Configuration Manager versie 1906 aan omdat deze Windows 10 Enter prise multi-session ondersteunt. Er wordt momenteel gewerkt aan Microsoft Intune ondersteuning.

## <a name="can-windows-10-enterprise-multi-session-be-azure-active-directory-ad-joined"></a>Kan Windows 10 Enter prise Multi-Session worden Azure Active Directory (AD)-gekoppeld?

Windows 10 Enter prise multi-session wordt momenteel ondersteund als hybride Azure AD-join. Nadat Windows 10 Enter prise multi-session is toegevoegd aan een domein, gebruikt u het bestaande groepsbeleid-object om Azure AD-registratie in te scha kelen. Zie voor meer informatie [de implementatie van hybride Azure Active Directory-deelname plannen](../active-directory/devices/hybrid-azuread-join-plan.md).

## <a name="where-can-i-find-the-windows-10-enterprise-multi-session-image"></a>Waar vind ik de Windows 10 Enter prise-installatie kopie voor meerdere sessies?

Windows 10 Enter prise multi-session bevindt zich in de Azure Gallery. Om het te vinden, gaat u naar de Azure Portal en zoekt u naar de release van Windows 10 Enter prise voor virtuele Bureau bladen. Voor een installatie kopie die is geïntegreerd met Microsoft 365-apps voor bedrijven, gaat u naar de Azure Portal en zoekt u naar **micro soft Windows 10 + Microsoft 365 apps voor bedrijven**.

## <a name="which-windows-10-enterprise-multi-session-image-should-i-use"></a>Welke Windows 10 Enter prise-afbeelding voor meerdere sessies moet ik gebruiken?

De Azure Gallery bevat verschillende versies, waaronder Windows 10 Enter prise multi-session, versie 1809 en Windows 10 Enter prise multi-session, versie 1903. U kunt het beste de meest recente versie gebruiken voor verbeterde prestaties en betrouw baarheid.

## <a name="which-windows-10-enterprise-multi-session-versions-are-supported"></a>Welke versies van Windows 10 Enter prise met meerdere sessies worden ondersteund?

Windows 10 Enter prise meerdere sessies, versie 1809 en hoger worden ondersteund en zijn beschikbaar in de Azure Gallery. Deze releases volgen hetzelfde ondersteunings levenscyclus beleid als Windows 10 Enter prise. Dit betekent dat de versie van maart wordt ondersteund voor 18 maanden en de release van september gedurende 30 maanden.

## <a name="which-profile-management-solution-should-i-use-for-windows-10-enterprise-multi-session"></a>Welke oplossing voor Profiel beheer moet ik gebruiken voor meerdere sessies van Windows 10 Enter prise?

We raden u aan om FSLogix-profiel containers te gebruiken wanneer u Windows 10 Enter prise configureert in niet-permanente omgevingen of in andere scenario's waarvoor een centraal opgeslagen profiel nodig is. FSLogix zorgt ervoor dat het gebruikers profiel beschikbaar is en up-to-date is voor elke gebruikers sessie. We raden u ook aan uw FSLogix-profiel container te gebruiken om een gebruikers profiel op te slaan in een SMB-share met de juiste machtigingen, maar u kunt indien nodig gebruikers profielen opslaan in een Azure-pagina-Blob-opslag. Windows-gebruikers van virtueel bureau blad kunnen FSLogix zonder extra kosten gebruiken.  FSLogix wordt vooraf geïnstalleerd op alle Windows 10 Enter prise-installatie kopieën voor meerdere sessies, maar de IT-beheerder is nog steeds verantwoordelijk voor het configureren van de FSLogix-profiel container.

Zie [Configure the FSLogix profile container](create-host-pools-user-profile.md#configure-the-fslogix-profile-container)(Engelstalig) voor meer informatie over het configureren van een FSLogix-profiel container.

## <a name="which-license-do-i-need-to-access-windows-10-enterprise-multi-session"></a>Welke licentie heb ik nodig om toegang te krijgen tot de multi-sessie van Windows 10 Enter prise?

Zie [prijzen voor Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/)voor een volledige lijst van toepasselijke licenties.

## <a name="why-do-my-apps-disappear-after-i-sign-out"></a>Waarom verdwijnen mijn apps na het afmelden?

Dit gebeurt omdat u Windows 10 Enter prise multi-session gebruikt met een oplossing voor Profiel beheer, zoals FSLogix. Uw beheerder of profiel oplossing heeft uw systeem geconfigureerd om gebruikers profielen te verwijderen wanneer gebruikers zich afmelden. Deze configuratie betekent dat wanneer uw systeem uw gebruikers profiel verwijdert nadat u zich hebt afgemeld, alle apps die u tijdens uw sessie hebt geïnstalleerd, worden verwijderd. Als u de apps die u hebt geïnstalleerd, wilt blijven gebruiken, moet u uw beheerder vragen deze apps in te richten voor alle gebruikers in uw Windows Virtual Desktop-omgeving.

## <a name="how-do-i-make-sure-apps-dont-disappear-when-users-sign-out"></a>Hoe kan ik zorg ervoor dat de apps niet verdwijnen wanneer gebruikers zich afmelden?

De meeste gevirtualiseerde omgevingen worden standaard geconfigureerd om te voor komen dat gebruikers extra apps op hun profielen installeren. Als u ervoor wilt zorgen dat een app niet verdwijnt wanneer uw gebruiker zich afmeldt bij Windows Virtual Desktop, moet u die app inrichten voor alle gebruikers profielen in uw omgeving. Bekijk de volgende bronnen voor meer informatie over het inrichten van apps:

- [Ingebouwde apps publiceren in virtueel bureau blad van Windows](publish-apps.md)
- [Opdracht regel opties voor onderhoud van DISM-app-pakket](/windows-hardware/manufacture/desktop/dism-app-package--appx-or-appxbundle--servicing-command-line-options)
- [Add-AppxProvisionedPackage](/powershell/module/dism/add-appxprovisionedpackage)

## <a name="how-do-i-make-sure-users-dont-download-and-install-apps-from-the-microsoft-store"></a>Hoe kan ik weet u zeker dat gebruikers geen apps van de Microsoft Store downloaden en installeren?

U kunt de app Microsoft Store uitschakelen om ervoor te zorgen dat gebruikers geen extra apps downloaden buiten de apps die u al hebt ingericht.

De Store-app uitschakelen:

1. Maak een nieuwe groepsbeleid.
2. Selecteer **computer configuratie**  >  **Beheersjablonen**  >  **Windows-onderdelen**.
3. Selecteer **Store**.
4. Selecteer **Store-toepassing**.
5. Selecteer **uitgeschakeld** en selecteer **OK**.
6. Selecteer **Toepassen**.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Windows Virtual Desktop en Windows 10 Enter prise multi-session:

- Lees onze [documentatie voor virtueel Windows-bureau blad](overview.md)
- Bezoek onze [Windows-TechCommunity voor virtueel bureau blad](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)
- Uw Windows-implementatie voor virtueel bureau blad instellen met de [zelf studies voor virtueel Windows-bureau blad](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)
