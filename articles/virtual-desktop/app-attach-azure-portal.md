---
title: Windows Virtual Desktop MSIX-app koppelen - Azure
description: MsIX-app-attach instellen voor Windows Virtual Desktop met behulp van de Azure Portal.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 21dbab6c8d4fb12fe79434a6994dd7f5b8a49190
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502705"
---
# <a name="set-up-msix-app-attach-with-the-azure-portal"></a>MSIX-app-koppeling instellen met de Azure-portal

In dit artikel wordt beschreven hoe u een MSIX-app-attach in een Windows Virtual Desktop instellen.

## <a name="requirements"></a>Vereisten

Dit is wat u nodig hebt om een MSIX-app-bijlage te configureren:

- Een werkende Windows Virtual Desktop implementatie. Zie Een tenant maken in Windows Virtual Desktop voor meer informatie over het implementeren [Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md). Zie Create a host pool with the Azure Portal (Een hostgroep maken met de Azure Portal) voor meer informatie over het implementeren van Windows Virtual Desktop [met Azure Resource Manager-integratie.](./create-host-pools-azure-marketplace.md)
- Een Windows Virtual Desktop hostgroep met ten minste één actieve sessiehost.
- Het MSIX-pakkethulpprogramma.
- Een toepassing met MSIX-pakket uitgebreid naar een MSIX-afbeelding die wordt geüpload naar een bestands share.
- Een bestands share in uw Windows Virtual Desktop implementatie waarin het MSIX-pakket wordt opgeslagen.
- De bestands share waar u de MSIX-afbeelding hebt geüpload, moet ook toegankelijk zijn voor alle virtuele machines (VM's) in de hostgroep. Gebruikers hebben alleen-lezenmachtigingen nodig voor toegang tot de afbeelding.
- Als het certificaat niet openbaar wordt vertrouwd, volgt u de instructies in [Certificaten installeren.](app-attach.md#install-certificates)

## <a name="turn-off-automatic-updates-for-msix-app-attach-applications"></a>Automatische updates uitschakelen voor MSIX-app-koppelingstoepassingen

Voordat u aan de slag gaat, moet u automatische updates uitschakelen voor MSIX-app-koppelingstoepassingen. Als u automatische updates wilt uitschakelen, moet u de volgende opdrachten uitvoeren in een opdrachtprompt met verhoogde opdracht:

```cmd
rem Disable Store auto update:

reg add HKLM\Software\Policies\Microsoft\WindowsStore /v AutoDownload /t REG_DWORD /d 0 /f
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Automatic app update" /Disable
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Scheduled Start" /Disable

rem Disable Content Delivery auto download apps that they want to promote to users:

reg add HKCU\Software\Microsoft\Windows\CurrentVersion\ContentDeliveryManager /v PreInstalledAppsEnabled /t REG_DWORD /d 0 /f

reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager\Debug /v ContentDeliveryAllowedOverride /t REG_DWORD /d 0x2 /f

```

>[!NOTE]
>U wordt aangeraden de virtuele machine opnieuw op te starten nadat u Hyper-V hebt inschakelen.

## <a name="configure-the-msix-app-attach-management-interface"></a>De beheerinterface voor het koppelen van MSIX-apps configureren

Vervolgens moet u de beheerinterface voor het koppelen van MSIX-apps downloaden en configureren voor de Azure Portal.

De beheerinterface instellen:

1. [Open de Azure Portal](https://portal.azure.com).
2. Als u wordt gevraagd of u de extensie betrouwbaar vindt, selecteert u **Toestaan.**

      > [!div class="mx-imgBorder"]
      > ![Een schermopname van het venster met niet-vertrouwde extensies. Toestaan is rood gemarkeerd.](media/untrusted-extensions.png)


## <a name="add-an-msix-image-to-the-host-pool"></a>Een MSIX-afbeelding toevoegen aan de hostgroep

Vervolgens moet u de MSIX-afbeelding toevoegen aan uw hostgroep.

De MSIX-afbeelding toevoegen:

1. Open Azure Portal.

2. Voer **Windows Virtual Desktop** in de zoekbalk in en selecteer vervolgens de servicenaam.

      > [!div class="mx-imgBorder"]
      > ![Een schermopname van een gebruiker die 'Windows Virtual Desktop' selecteert in de vervolgkeuzelijst van de zoekbalk in het Azure Portal. 'Windows Virtual Desktop' is rood gemarkeerd.](media/find-and-select.png)

3. Selecteer de hostgroep waarin u de MSIX-apps wilt plaatsen.

4. Selecteer **MSIX-pakketten om** het gegevensraster te openen met alle **MSIX-pakketten die** momenteel zijn toegevoegd aan de hostgroep.

5. Selecteer **+ Toevoegen om** het tabblad **MSIX-pakket toevoegen te** openen.

6. Voer op **het tabblad MSIX-pakket** toevoegen de volgende waarden in:

      - Voer **voor MSIX-afbeeldingspad** een geldig UNC-pad in dat verwijst naar de MSIX-afbeelding op de bestands share. `\\storageaccount.file.core.windows.net\msixshare\appfolder\MSIXimage.vhd`(Bijvoorbeeld.) Wanneer u klaar bent, selecteert u **Toevoegen om** de MSIX-container te controleren of het pad geldig is.

      - Selecteer **voor MSIX-pakket** de relevante MSIX-pakketnaam in de vervolgkeuzelijst. Dit menu wordt alleen ingevuld als u een geldig pad naar de afbeelding hebt ingevoerd in **het MSIX-afbeeldingspad**.

      - Voor **Pakkettoepassingen moet** u ervoor zorgen dat de lijst alle MSIX-toepassingen bevat die beschikbaar moeten zijn voor gebruikers in uw MSIX-pakket.

      - Voer desgewenst een **Weergavenaam in** als u wilt dat uw pakket gebruiksvriendelijker is voor uw gebruikersimplementaties.

      - Zorg ervoor dat **de versie** het juiste versienummer heeft.

      - Selecteer het **registratietype** dat u wilt gebruiken. Welke u gebruikt, is afhankelijk van uw behoeften:

    - **Registratie op aanvraag stelt** de volledige registratie van de MSIX-toepassing uit totdat de gebruiker de toepassing start. Dit is het registratietype dat u kunt gebruiken.

    - **Bij het blokkeren van aanmelding** worden alleen registers geregistreerd wanneer de gebruiker zich aanmeldt. Dit type wordt niet aanbevolen omdat dit kan leiden tot langere aanmeldingstijden voor gebruikers.

7.  Selecteer **bij Status** de gewenste status.
    -  Met **de** status Actief kunnen gebruikers met het pakket communiceren.
    -  De **status Inactief** zorgt ervoor Windows Virtual Desktop het pakket negeert en niet aan gebruikers levert.

8. Wanneer u klaar bent, selecteert u **Toevoegen.**

## <a name="publish-msix-apps-to-an-app-group"></a>MSIX-apps publiceren naar een app-groep

Vervolgens moet u de apps in het pakket publiceren. U moet dit doen voor zowel bureaublad- als externe app-toepassingsgroepen.

Als u al een MSIX-afbeelding hebt, gaat u verder met [MSIX-apps publiceren naar een app-groep](#publish-msix-apps-to-an-app-group). Als u oudere toepassingen wilt testen, volgt u de instructies in Create an MSIX package from a desktop installer on a VM to convert the legacy application to an MSIX package (Een MSIX-pakket maken van een [desktop-installatieprogramma](/windows/msix/packaging-tool/create-app-package-msi-vm/) op een VM) om de verouderde toepassing te converteren naar een MSIX-pakket.

De apps publiceren:

1. Selecteer in Windows Virtual Desktop resourceprovider het **tabblad Toepassingsgroepen.**

2. Selecteer de toepassingsgroep waar u de apps naar wilt publiceren.

   >[!NOTE]
   >MSIX-toepassingen kunnen worden geleverd met MSIX-app-koppelen aan groepen voor zowel externe apps als bureaublad-apps

3. Als u zich in de app-groep hebt, selecteert u het **tabblad** Toepassingen. In **het** raster Toepassingen worden alle bestaande apps in de app-groep weergegeven.

4. Selecteer **+ Toevoegen om** het tabblad Toepassing toevoegen **te** openen.

      > [!div class="mx-imgBorder"]
      > ![Een schermopname van de gebruiker die + Toevoegen selecteert om het tabblad Toepassing toevoegen te openen](media/select-add.png)

5. Kies **voor Toepassingsbron** de bron voor uw toepassing.
    - Als u een bureaublad-app-groep gebruikt, kiest u **MSIX-pakket**.
      
      > [!div class="mx-imgBorder"]
      > ![Een schermopname van een klant die het MSIX-pakket selecteert in de vervolgkeuzelijst voor de toepassingsbron. HET MSIX-pakket is rood gemarkeerd.](media/select-source.png)
    
    - Als u een externe app-groep gebruikt, kiest u een van de volgende opties:
        
        - Startmenu
        - App-pad
        - MSIX-pakket

    - Voer **bij Toepassingsnaam** een beschrijvende naam in voor de toepassing.

    U kunt ook de volgende optionele functies configureren:
   
    - Voer **bij Weergavenaam** een nieuwe naam in voor het pakket dat uw gebruikers te zien krijgen.

    - Voer **bij Beschrijving** een korte beschrijving van het app-pakket in.

    - Als u een externe app-groep gebruikt, kunt u ook deze opties configureren:

        - **Pictogrampad**
        - **Pictogramindex**
        - **Weergegeven in webfeed**

6. Selecteer **Opslaan** als u klaar bent.

>[!NOTE]
>Wanneer een gebruiker is toegewezen aan een externe app-groep en bureaublad-app-groep uit dezelfde hostgroep, wordt de bureaublad-app-groep weergegeven in de feed.

## <a name="assign-a-user-to-an-app-group"></a>Een gebruiker toewijzen aan een app-groep

Nadat u MSIX-apps aan een app-groep hebt toegewezen, moet u gebruikers toegang verlenen tot deze apps. U kunt toegang toewijzen door gebruikers of gebruikersgroepen toe te voegen aan een app-groep met gepubliceerde MSIX-toepassingen. Volg de instructies in [App-groepen beheren met de Azure Portal](manage-app-groups.md) om uw gebruikers toe te wijzen aan een app-groep.

>[!NOTE]
>Externe apps die aan de MSIX-app zijn gekoppeld, kunnen uit de feed verdwijnen wanneer u externe apps test tijdens de openbare preview. De apps worden niet weergegeven omdat de hostgroep die u in de evaluatieomgeving gebruikt, wordt bediend door een RD Broker in de productieomgeving. Omdat de RD Broker in de productieomgeving de aanwezigheid van externe apps die zijn gekoppeld aan de MSIX-app niet registreert, worden de apps niet weergegeven in de feed.

## <a name="change-msix-package-state"></a>MSIX-pakkettoestand wijzigen

Vervolgens moet u de status van het MSIX-pakket wijzigen in **Actief** of **Inactief,** afhankelijk van wat u met het pakket wilt doen. Actieve pakketten zijn pakketten die uw gebruikers kunnen gebruiken zodra ze zijn gepubliceerd. Inactieve pakketten worden genegeerd door Windows Virtual Desktop, zodat uw gebruikers niet kunnen communiceren met de apps in de app.

### <a name="change-state-with-the-applications-list"></a>Status wijzigen met de lijst met toepassingen

De status van het pakket wijzigen met de lijst met toepassingen:

1. Ga naar uw hostgroep en selecteer **MSIX-pakketten.** Als het goed is, ziet u een lijst met alle bestaande MSIX-pakketten in de hostgroep.

2. Selecteer de MSIX-pakketten waarvan u de status wilt wijzigen en selecteer **vervolgens Status wijzigen.**

### <a name="change-state-with-update-package"></a>Status wijzigen met updatepakket

De pakkettoestand wijzigen met een updatepakket:

1. Ga naar uw hostgroep en selecteer **MSIX-pakketten.** Als het goed is, ziet u een lijst met alle bestaande MSIX-pakketten in de hostgroep.

2. Selecteer de naam van het pakket waarvan u de status wilt wijzigen in de lijst met MSIX-pakketten. Hiermee opent u het **tabblad Pakket** bijwerken.

3. Zet de **schakelknop Status** op **Inactief of** **Actief** en selecteer vervolgens **Opslaan.**

## <a name="change-msix-package-registration-type"></a>MsIX-pakketregistratietype wijzigen

Het registratietype van het pakket wijzigen:

1. Selecteer **MSIX-pakketten.** Als het goed is, ziet u een lijst met alle bestaande MSIX-pakketten in de hostgroep.

2. Selecteer **Pakketnaam in het** raster **MSIX-pakketten.** Hiermee wordt de blade geopend om het pakket bij te werken.

3. Schakel het **registratietype naar wens** in via de blokkeringsknop **On-demand/Log on** en selecteer **Opslaan.**

## <a name="remove-an-msix-package"></a>Een MSIX-pakket verwijderen

Een MSIX-pakket uit uw hostgroep verwijderen:

1. Selecteer **MSIX-pakketten.**  Als het goed is, ziet u een lijst met alle bestaande MSIX-pakketten in de hostgroep.

2. Selecteer het beletselteken aan de rechterkant de naam van het pakket dat u wilt verwijderen en selecteer **vervolgens Verwijderen.**

## <a name="remove-msix-apps"></a>MSIX-apps verwijderen

Afzonderlijke MSIX-apps uit uw pakket verwijderen:

1. Ga naar de hostgroep en selecteer **Toepassingsgroepen.**

2. Selecteer de toepassingsgroep waar u MSIX-apps uit wilt verwijderen.

3. Open het **tabblad** Toepassingen.

4. Selecteer de app die u wilt verwijderen en selecteer **vervolgens Verwijderen.**

## <a name="next-steps"></a>Volgende stappen

Stel onze communityvragen over deze functie op de [Windows Virtual Desktop TechCommunity.](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)

U kunt ook feedback geven voor Windows Virtual Desktop in Windows Virtual Desktop [feedbackhub.](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)

Hier zijn enkele andere artikelen die mogelijk nuttig zijn:

- [Verklarende woordenlijst voor MSIX-app-koppelen](app-attach-glossary.md)
- [Veelgestelde vragen over het koppelen van MSIX-apps](app-attach-faq.md)
