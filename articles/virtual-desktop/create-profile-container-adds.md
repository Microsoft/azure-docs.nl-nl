---
title: FSLogix-profiel container maken Azure Files Active Directory Domain Services-Azure
description: In dit artikel wordt beschreven hoe u een FSLogix-profiel container maakt met Azure Files en Azure Active Directory Domain Services.
author: Heidilohr
ms.topic: how-to
ms.date: 04/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 70a56b7efc34ba2fd3c06521c6e4cac6ea28778f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96302479"
---
# <a name="create-a-profile-container-with-azure-files-and-azure-ad-ds"></a>Een profiel container maken met Azure Files en Azure AD DS

In dit artikel wordt uitgelegd hoe u een FSLogix-profiel container maakt met Azure Files en Azure Active Directory Domain Services (AD DS).

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u al een Azure AD DS-exemplaar hebt ingesteld. Als u nog geen account hebt, volgt u eerst de instructies in [Create a Basic Managed Domain](../active-directory-domain-services/tutorial-create-instance.md) en vervolgens keert u hier terug.

## <a name="add-azure-ad-ds-admins"></a>Azure AD DS-beheerders toevoegen

U kunt extra beheerders toevoegen door een nieuwe gebruiker te maken en deze machtigingen te verlenen.

Een beheerder toevoegen:

1. Selecteer **Azure Active Directory** in de zijbalk, selecteer **alle gebruikers** en selecteer vervolgens **nieuwe gebruiker**.

2.  Voer de gebruikers gegevens in de velden in.

3. Selecteer in het deel venster Azure Active Directory aan de linkerkant van het scherm de optie **groepen**.

4. Selecteer de groep **Aad DC-Administrators** .

5. Selecteer **leden** in het linkerdeel venster en selecteer vervolgens **leden toevoegen** in het hoofd venster. Hiermee wordt een lijst weer gegeven met alle gebruikers die beschikbaar zijn in azure AD. Selecteer de naam van het gebruikers profiel dat u zojuist hebt gemaakt.

## <a name="set-up-an-azure-storage-account"></a>Een Azure Storage account instellen

Nu is het tijd om Azure AD DS-verificatie in te scha kelen via SMB (Server Message Block).

Verificatie inschakelen:

1. Als u nog geen v2 Azure Storage-account voor algemeen gebruik hebt ingesteld en geïmplementeerd, volgt u de instructies in [een Azure Storage-account maken](../storage/common/storage-account-create.md).

2. Wanneer u klaar bent met het instellen van uw account, selecteert u **Ga naar resource**.

3. Selecteer **configuratie** in het deel venster aan de linkerkant van het scherm en Schakel **Azure Active Directory verificatie in voor Azure files** in het hoofd venster. Selecteer **Opslaan** als u klaar bent.

4. Selecteer **overzicht** in het deel venster aan de linkerkant van het scherm en selecteer vervolgens **bestanden** in het hoofd venster.

5. Selecteer **Bestands share** en voer de **naam** en het **quotum** in de velden in die aan de rechter kant van het scherm worden weer gegeven.

## <a name="assign-access-permissions-to-an-identity"></a>Toegangs machtigingen toewijzen aan een identiteit

Andere gebruikers hebben Toegangs machtigingen nodig voor toegang tot uw bestands share. Hiervoor moet u elke gebruiker een rol toewijzen met de juiste toegangs machtigingen.

Gebruikers toegangs machtigingen toewijzen:

1. Open vanuit het Azure Portal de bestands share die u hebt gemaakt in [een Azure Storage account instellen](#set-up-an-azure-storage-account).

2. Selecteer **Access Control (IAM)**.

3. Selecteer **een roltoewijzing toevoegen**.

4. Selecteer op het tabblad **toewijzing van rol toevoegen** de juiste ingebouwde rol in de lijst met rollen. U moet mini maal de machtiging **opslag bestands gegevens SMB delen** voor het account selecteren om de juiste machtigingen te krijgen.

5. Selecteer **Azure Active Directory gebruiker, groep of Service-Principal** om **toegang toe te wijzen**.

6. Selecteer een naam of e-mail adres voor de identiteit van het doel Azure Active Directory.

7. Selecteer **Opslaan**.

## <a name="get-the-storage-account-access-key"></a>De toegangs sleutel voor het opslag account ophalen

Vervolgens moet u de toegangs sleutel voor uw opslag account ophalen.

De toegangs sleutel voor het opslag account ophalen:

1. Selecteer op de Azure Portal zijbalk **opslag accounts**.

2. Selecteer in de lijst met opslag accounts het account waarvoor u Azure AD DS hebt ingeschakeld en maak de aangepaste rollen in de bovenstaande stappen.

3. Selecteer onder **instellingen** de optie **toegangs sleutels** en kopieer de sleutel van **key1**.

4. Ga naar het tabblad **virtual machines** en zoek naar een virtuele machine die deel gaat uitmaken van de hostgroep.

5. Selecteer de naam van de virtuele machine (VM) onder **virtual machines (adVM)** en selecteer **verbinding maken**

    Hiermee wordt een RDP-bestand gedownload waarmee u zich met de eigen referenties kunt aanmelden bij de VM.

    > [!div class="mx-imgBorder"]
    > ![Een scherm opname van het tabblad RDP van het venster verbinding maken met virtuele machine.](media/rdp-tab.png)

6. Wanneer u zich hebt aangemeld bij de VM, voert u een opdracht prompt uit als beheerder.

7. Voer de volgende opdracht uit:

     ```cmd
     net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
     ```

    - Vervang door `<desired-drive-letter>` een stationsletter van uw keuze (bijvoorbeeld `y:` ).
    - Vervang alle exemplaren van `<storage-account-name>` door de naam van het opslag account dat u eerder hebt opgegeven.
    - Vervang door `<share-name>` de naam van de share die u eerder hebt gemaakt.
    - Vervang door `<storage-account-key>` de sleutel van het opslag account van Azure.

    Bijvoorbeeld:

     ```cmd
     net use y: \\fsprofile.file.core.windows.net\share HDZQRoFP2BBmoYQ=(truncated)= /user:Azure\fsprofile)
     ```

8. Voer de volgende opdrachten uit om uw Windows virtueel-bureaublad gebruikers in staat te stellen hun eigen profiel container te maken en de toegang tot de profiel containers van andere gebruikers te blok keren.

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

    - Vervang door `<mounted-drive-letter>` de letter van het station dat u hebt gebruikt om het station toe te wijzen.
    - Vervang door `<user-email>` de UPN van de gebruiker of Active Directory groep die de gebruikers bevat die toegang moeten hebben tot de share.

    Bijvoorbeeld:

     ```cmd
     icacls <mounted-drive-letter>: /grant john.doe@contoso.com:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

## <a name="create-a-profile-container"></a>Een profielcontainer maken

Nu uw profielen klaar zijn om te worden, gaan we een FSLogix-profiel container maken.

Een FSLogix-profiel container configureren:

1. Meld u aan bij de host-VM die u aan het begin van dit artikel hebt geconfigureerd. vervolgens [downloadt en installeert u de FSLogix-agent](/fslogix/install-ht/).

2. Pak het FSLogix-agent bestand dat u hebt gedownload uit en ga naar **x64**  >  -**releases** en open **FSLogixAppsSetup.exe**.

3. Wanneer het installatie programma wordt gestart, selecteert **u ik ga akkoord met de licentie voorwaarden.** Geef, indien van toepassing, een nieuwe sleutel op.

4. Selecteer **Installeren**.

5. Open **station C** en ga naar **programma bestanden**  >  **FSLogix**  >  **apps** om te controleren of de FSLogix-agent correct is geïnstalleerd.

     >[!NOTE]
     > Als er meerdere virtuele machines in de hostgroep zijn, moet u stap 1 tot en met 5 herhalen voor elke VM.

6. Voer de **REGI ster-editor** (regedit) uit als Administrator.

7. Navigeer naar **computer**  >  **HKEY_LOCAL_MACHINE**  >  **Software**  >  **FSLogix**, klik met de rechter muisknop op **FSLogix**, selecteer **Nieuw** en selecteer vervolgens **sleutel**.

8. Maak een nieuwe sleutel met de naam **Profiles**.

9.  Klik met de rechter muisknop op **profielen**, selecteer **Nieuw** en selecteer vervolgens **DWORD-waarde (32-bits).** Geef de waarde **ingeschakeld** en stel de **gegevens** waarde in op **1**.

    > [!div class="mx-imgBorder"]
    > ![Een scherm afbeelding van de sleutel Profiles. Het REG_DWORD bestand wordt gemarkeerd en de bijbehorende gegevens waarde is ingesteld op 1.](media/dword-value.png)

10. Klik met de rechter muisknop op **profielen**, selecteer **Nieuw** en selecteer vervolgens **waarde met meerdere teken reeksen**. Noem de waarde **VHDLocations** en stel de URI voor de Azure Files share `\\fsprofile.file.core.windows.net\share` in als gegevens waarde.

    > [!div class="mx-imgBorder"]
    > ![Een scherm afbeelding van de sleutel Profiles waarin het VHDLocations-bestand wordt weer gegeven. De gegevens waarde bevat de URI voor de Azure Files share.](media/multi-string-value.png)

## <a name="assign-users-to-a-session-host"></a>Gebruikers toewijzen aan een sessie-host

Nu moet u gebruikers aan uw sessiehost toewijzen.

Gebruikers toewijzen:

1. Voer Windows Power shell uit als beheerder en voer de volgende cmdlet uit om u aan te melden bij Windows Virtual Desktop met Power shell:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell

   #Optional
   Install-Module Microsoft.RdInfra.RdPowershell

   $brokerurl = "https://rdbroker.wvd.microsoft.com"

   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

   Wanneer u om referenties wordt gevraagd, voert u dezelfde gebruiker in die de rol TenantCreator, RDS owner of RDS contributor heeft verleend op de virtuele bureau blad-Tenant van Windows.

2. Voer de volgende cmdlets uit om de gebruiker toe te wijzen aan de groep extern bureau blad:

     ```powershell
     $tenant = "<your-wvd-tenant>"

     $pool1 = "<wvd-pool>"

     $appgroup = "Desktop Application Group"

     $user1 = "<user-principal>"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

    Net als bij de eerdere cmdlets vervangt u, `<your-wvd-tenant>` `<wvd-pool>` en door `<user-principal>` de relevante waarden.

    Bijvoorbeeld:

     ```powershell
     $pool1 = "contoso"

     $tenant = "contoso"

     $appgroup = "Desktop Application Group"

     $user1 = "jane.doe@contoso.com"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

## <a name="make-sure-your-profile-works"></a>Controleren of uw profiel werkt

Nu hoeft u alleen maar te controleren of het profiel dat u hebt gemaakt, bestaat en werkt zoals bedoeld.

Uw profiel verifiëren:

1. Open een browser en ga naar [de web-client met virtueel bureau blad van Windows](https://rdweb.wvd.microsoft.com/arm/webclient).

2. Meld u aan met het gebruikers account dat is toegewezen aan de Extern bureaublad groep.

3. Zodra de gebruikers sessie tot stand is gebracht, opent u de Azure Portal en meldt u zich aan met een Administrator-account.

4. Selecteer **opslag accounts** in de zijbalk.

5. Selecteer het opslag account dat u hebt geconfigureerd als de bestands share voor uw Session Host-groep en is ingeschakeld met Azure AD DS.

6. Selecteer het pictogram **bestanden** en vouw vervolgens uw share uit.

    Als alles goed is ingesteld, ziet u een **map** met een naam die als volgt is opgemaakt: `<user SID>-<username>` .

## <a name="next-steps"></a>Volgende stappen

Als u op zoek bent naar alternatieve manieren om FSLogix-profiel containers te maken, raadpleegt u de volgende artikelen:

- [Maak een profiel container voor een hostgroep met een bestands share](create-host-pools-user-profile.md).
- [Een FSLogix-profiel container maken voor een hostgroep met Azure NetApp Files](create-fslogix-profile-container.md)

U vindt meer gedetailleerde informatie over concepten met betrekking tot FSlogix-containers voor Azure files in [FSlogix-profiel containers en Azure files](fslogix-containers-azure-files.md).
