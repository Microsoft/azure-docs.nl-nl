---
title: Windows-MSIX-app voor het koppelen van apps-Azure
description: MSIX-app-koppeling voor Windows Virtual Desktop instellen met behulp van de Azure Portal.
author: Heidilohr
ms.topic: how-to
ms.date: 02/11/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: a849b65fd25e6943925ffa245430cd8a27529fdb
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448420"
---
# <a name="set-up-msix-app-attach-with-the-azure-portal"></a>MSIX-app-koppeling instellen met de Azure-portal

> [!IMPORTANT]
> MSIX app attach is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In dit artikel vindt u instructies voor het instellen van MSIX app attach (preview) in een virtuele Windows-desktop omgeving.

## <a name="requirements"></a>Vereisten

>[!IMPORTANT]
>Voordat u aan de slag gaat, moet u ervoor zorgen dat [dit formulier](https://aka.ms/enablemsixappattach) wordt ingevuld en verzonden om de MSIX-app-koppeling in uw abonnement in te scha kelen. Als u geen goedgekeurde aanvraag hebt, werkt MSIX app attach niet. De goed keuring van aanvragen kan tot 24 uur duren tijdens kantoor dagen. U ontvangt een e-mail wanneer uw aanvraag is geaccepteerd en voltooid.

Dit is wat u nodig hebt om de MSIX-app-koppeling te configureren:

- Een werkende implementatie van virtueel bureau blad in Windows. Zie [een Tenant maken in Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)voor meer informatie over het implementeren van Windows virtueel bureau blad (klassiek). Zie [een hostgroep maken met de Azure Portal](./create-host-pools-azure-marketplace.md)voor meer informatie over het implementeren van Windows virtueel bureau blad met Azure Resource Manager-integratie.
- Een Windows-host voor virtuele Bureau bladen met ten minste één actieve sessiehost.
- Deze hostgroep moet zich in de validatie omgeving bestaan. 
- Het MSIX-verpakkings programma.
- Een MSIX-toepassing die is uitgepakt in een MSIX-installatie kopie die is geüpload naar een bestands share.
- Een bestands share in uw Windows-implementatie voor virtueel bureau blad waar het MSIX-pakket wordt opgeslagen.
- De bestands share waar u de MSIX-installatie kopie hebt geüpload, moet ook toegankelijk zijn voor alle virtuele machines (Vm's) in de hostgroep. Gebruikers hebben alleen-lezen-machtigingen nodig voor toegang tot de installatie kopie.
- Als het certificaat niet openbaar wordt vertrouwd, volgt u de instructies in [certificaten installeren](app-attach.md#install-certificates).

## <a name="turn-off-automatic-updates-for-msix-app-attach-applications"></a>Automatische updates voor MSIX app attach-toepassingen uitschakelen

Voordat u aan de slag gaat, moet u automatische updates voor MSIX app attach-toepassingen uitschakelen. Als u automatische updates wilt uitschakelen, moet u de volgende opdrachten uitvoeren in een opdracht prompt met verhoogde bevoegdheid:

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
>U wordt aangeraden de virtuele machine opnieuw op te starten nadat u Hyper-V hebt ingeschakeld.

## <a name="configure-the-msix-app-attach-management-interface"></a>De MSIX-app voor het koppelen van apps configureren

Vervolgens moet u de MSIX-app voor het koppelen van apps voor de Azure Portal downloaden en configureren.

De beheer interface instellen:

1. [Open de Azure Portal](https://portal.azure.com).
2. Als u wordt gevraagd of u de uitbrei ding betrouwt, selecteert u **toestaan**.

      > [!div class="mx-imgBorder"]
      > ![Een scherm opname van het venster met niet-vertrouwde extensies. ' Toestaan ' is rood gemarkeerd.](media/untrusted-extensions.png)


## <a name="add-an-msix-image-to-the-host-pool"></a>Een MSIX-installatie kopie toevoegen aan de hostgroep

Vervolgens moet u de MSIX-installatie kopie toevoegen aan de hostgroep.

De MSIX-installatie kopie toevoegen:

1. Open Azure Portal.

2. Geef **Windows virtueel bureau blad** op in de zoek balk en selecteer vervolgens de naam van de service.

      > [!div class="mx-imgBorder"]
      > ![Een scherm afbeelding van een gebruiker die ' Windows virtueel bureau blad ' selecteert in de vervolg keuzelijst zoek balk in het Azure Portal. ' Windows virtueel bureau blad ' is rood gemarkeerd.](media/find-and-select.png)

3. Selecteer de hostgroep waar u de MSIX-Apps wilt plaatsen.

4. Selecteer **MSIX-pakketten** om het gegevens raster te openen met alle **MSIX-pakketten** die momenteel aan de hostgroep zijn toegevoegd.

5. Selecteer **+ toevoegen** om het tabblad **MSIX-pakket toevoegen** te openen.

6. Voer op het tabblad **MSIX-pakket toevoegen** de volgende waarden in:

      - Voer voor **MSIX-installatie kopie pad** een geldig UNC-pad in dat verwijst naar de MSIX-installatie kopie op de bestands share. (Bijvoorbeeld `\\storageaccount.file.core.windows.net\msixshare\appfolder\MSIXimage.vhd` .) Wanneer u klaar bent, selecteert u **toevoegen** om de MSIX-container te vragen om te controleren of het pad geldig is.

      - Selecteer voor **MSIX-pakket** de relevante naam van het MSIX-pakket in de vervolg keuzelijst. Dit menu wordt alleen ingevuld als u een geldig pad naar een afbeelding hebt opgegeven in **MSIX-afbeeldings pad**.

      - Voor **pakket toepassingen** zorgt u ervoor dat de lijst alle MSIX-toepassingen bevat die u beschikbaar wilt maken voor gebruikers in uw MSIX-pakket.

      - U kunt desgewenst een **weergave naam** invoeren als u wilt dat uw pakket een gebruiks vriendelijkere gebruikers implementaties bevat.

      - Controleer of de **versie** het juiste versie nummer heeft.

      - Selecteer het **registratie type** dat u wilt gebruiken. Welk account u gebruikt, is afhankelijk van uw behoeften:

    - **Bij registratie op aanvraag** wordt de volledige registratie van de MSIX-toepassing uitgesteld totdat de gebruiker de toepassing start. Dit is het registratie type dat u het beste kunt gebruiken.

    - **Logboek registratie blokkeert** alleen registraties terwijl de gebruiker zich aanmeldt. Dit type wordt niet aanbevolen omdat het kan leiden tot langere aanmeldings tijden voor gebruikers.

7.  Selecteer de gewenste status voor de **status**.
    -  Met de **actieve** status kunnen gebruikers communiceren met het pakket.
    -  De status **inactief** zorgt ervoor dat Windows virtueel bureau blad het pakket negeert en het niet aan gebruikers levert.

8. Wanneer u klaar bent, selecteert u **toevoegen**.

## <a name="publish-msix-apps-to-an-app-group"></a>MSIX-apps publiceren naar een app-groep

Vervolgens moet u de apps in het pakket publiceren. U moet dit doen voor zowel bureau blad-als externe app-toepassings groepen.

Als u al een MSIX-installatie kopie hebt, gaat u verder met het [publiceren van MSIX-apps naar een app-groep](#publish-msix-apps-to-an-app-group). Als u oudere toepassingen wilt testen, volgt u de instructies in een [MSIX-pakket maken op basis van een installatie programma van een desktop computer op een virtuele machine](/windows/msix/packaging-tool/create-app-package-msi-vm/) om de verouderde toepassing te converteren naar een MSIX-pakket.

De apps publiceren:

1. Selecteer in de Windows-Resource provider voor virtueel bureau blad het tabblad **toepassings groepen** .

2. Selecteer de toepassings groep waarnaar u de apps wilt publiceren.

   >[!NOTE]
   >MSIX-toepassingen kunnen worden geleverd met een MSIX-app die is gekoppeld aan zowel de externe app-als de bureau blad-app-groepen

3. Zodra u zich in de app-groep bevindt, selecteert u het tabblad **toepassingen** . In het raster **toepassingen** worden alle bestaande apps in de app-groep weer gegeven.

4. Selecteer **+ toevoegen** om het tabblad **toepassing toevoegen** te openen.

      > [!div class="mx-imgBorder"]
      > ![Een scherm afbeelding van de gebruiker die u selecteert en toevoegen om het tabblad toepassing toevoegen te openen](media/select-add.png)

5. Kies voor **toepassings bron** de bron voor uw toepassing.
    - Als u een bureau blad-app-groep gebruikt, kiest u **MSIX-pakket**.
      
      > [!div class="mx-imgBorder"]
      > ![Een scherm opname van een klant en MSIX-pakket selecteren in de vervolg keuzelijst toepassings bron. Het MSIX-pakket is rood gemarkeerd.](media/select-source.png)
    
    - Als u een externe app-groep gebruikt, kiest u een van de volgende opties:
        
        - Startmenu
        - Pad naar app
        - MSIX-pakket

    - Voer bij **toepassings naam** een beschrijvende naam in voor de toepassing.

    U kunt ook de volgende optionele functies configureren:
   
    - Voer bij **weergave naam** een nieuwe naam in voor het pakket dat uw gebruikers te zien krijgen.

    - Voer bij **Beschrijving** een korte beschrijving in van het app-pakket.

    - Als u een externe app-groep gebruikt, kunt u deze opties ook configureren:

        - **Pad naar pictogram**
        - **Pictogram index**
        - **Weer geven in webfeed**

6. Selecteer **Opslaan** als u klaar bent.

>[!NOTE]
>Wanneer een gebruiker wordt toegewezen aan de groep externe app-groep en bureau blad-app in dezelfde hostgroep, wordt de groep bureau blad-apps weer gegeven in de feed.

## <a name="assign-a-user-to-an-app-group"></a>Een gebruiker toewijzen aan een app-groep

Nadat u MSIX-apps aan een app-groep hebt toegewezen, moet u gebruikers toegang verlenen tot deze toepassingen. U kunt toegang toewijzen door gebruikers of gebruikers groepen toe te voegen aan een app-groep met gepubliceerde MSIX-toepassingen. Volg de instructies in [app-groepen beheren met de Azure Portal](manage-app-groups.md) om uw gebruikers toe te wijzen aan een app-groep.

>[!NOTE]
>MSIX-app koppelen externe apps worden mogelijk verwijderd uit de feed wanneer u externe apps tijdens de open bare preview test. De apps worden niet weer gegeven omdat de hostgroep die u in de evaluatie omgeving gebruikt, wordt geleverd door een extern bureau blad-Broker in de productie omgeving. Omdat de RD-Broker in de productie omgeving de aanwezigheid van de MSIX-app voor het koppelen van externe apps niet registreert, worden de apps niet weer gegeven in de feed.

## <a name="change-msix-package-state"></a>Status van MSIX-pakket wijzigen

Vervolgens moet u de status van het MSIX-pakket wijzigen in **actief** of **inactief**, afhankelijk van wat u met het pakket wilt doen. Actieve pakketten zijn pakketten die uw gebruikers kunnen gebruiken wanneer ze zijn gepubliceerd. Inactieve pakketten worden door Windows Virtual Desktop genegeerd, zodat uw gebruikers niet kunnen communiceren met de apps in.

### <a name="change-state-with-the-applications-list"></a>Status wijzigen in de lijst met toepassingen

Wijzig de status van het pakket met de lijst met toepassingen:

1. Ga naar uw hostgroep en selecteer **MSIX-pakketten**. U ziet een lijst met alle bestaande MSIX-pakketten binnen de hostgroep.

2. Selecteer de MSIX-pakketten waarvan u de status wilt wijzigen, en selecteer vervolgens **status wijzigen**.

### <a name="change-state-with-update-package"></a>Status wijzigen met update pakket

De pakket status wijzigen met een update pakket:

1. Ga naar uw hostgroep en selecteer **MSIX-pakketten**. U ziet een lijst met alle bestaande MSIX-pakketten binnen de hostgroep.

2. Selecteer in de lijst MSIX pakket de naam van het pakket waarvan u de status wilt wijzigen. Hiermee opent u het tabblad **Update pakket** .

3. Schakel de **status** switch in op **inactief** of **actief** en selecteer vervolgens **opslaan.**

## <a name="change-msix-package-registration-type"></a>Het registratie type van het MSIX-pakket wijzigen

Het registratie type van het pakket wijzigen:

1. Selecteer **MSIX-pakketten**. U ziet een lijst met alle bestaande MSIX-pakketten binnen de hostgroep.

2. Selecteer **pakket naam in** het **raster MSIX-pakketten** Hiermee wordt de Blade geopend om het pakket bij te werken.

3. Schakel het **registratie type** via de knop **op aanvraag/logboeken op** de gewenste locatie in en selecteer **opslaan.**

## <a name="remove-an-msix-package"></a>Een MSIX-pakket verwijderen

Een MSIX-pakket verwijderen uit uw hostgroep:

1. Selecteer **MSIX-pakketten**.  U ziet een lijst met alle bestaande MSIX-pakketten binnen de hostgroep.

2. Selecteer het beletsel teken aan de rechter kant van de naam van het pakket dat u wilt verwijderen en selecteer vervolgens **verwijderen**.

## <a name="remove-msix-apps"></a>MSIX-apps verwijderen

Afzonderlijke MSIX-apps uit uw pakket verwijderen:

1. Ga naar de hostgroep en selecteer **toepassings groepen**.

2. Selecteer de toepassings groep waarvan u de MSIX-Apps wilt verwijderen.

3. Open het tabblad **toepassingen** .

4. Selecteer de app die u wilt verwijderen en selecteer vervolgens **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Vraag onze vragen van de community over deze functie op het [virtuele bureau blad-TechCommunity van Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

U kunt ook feedback geven voor Windows virtueel bureau blad op de [Windows Virtual Desktop feedback hub](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

Hier volgen enkele andere artikelen die u mogelijk handig vindt:

- [Woorden lijst voor het toevoegen van MSIX-apps](app-attach-glossary.md)
- [Veelgestelde vragen over het koppelen van MSIX-apps](app-attach-faq.md)
