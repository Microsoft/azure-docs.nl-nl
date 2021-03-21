---
title: Een Azure Files bestands share maken met een domein controller-Azure
description: Stel een FSLogix-profiel container in op een Azure-bestands share in een bestaande virtuele Windows-bureau blad-hostgroep met uw Active Directory domein.
author: Heidilohr
ms.topic: how-to
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8f8086aced26fc46fb1430df074082e8c3365baa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92746807"
---
# <a name="create-a-profile-container-with-azure-files-and-ad-ds"></a>Een profiel container maken met Azure Files en AD DS

In dit artikel leert u hoe u een Azure-bestands share maakt die wordt geverifieerd door een domein controller op een bestaande virtuele Windows-bureau blad-hostgroep. U kunt deze bestands share gebruiken om opslag profielen op te slaan.

Dit proces maakt gebruik van Active Directory Domain Services (AD DS). Dit is een on-premises adreslijst service. Zie [een FSLogix-profiel container maken met Azure files](create-profile-container-adds.md)als u op zoek bent naar informatie over het maken van een FSLogix-profiel container met Azure AD DS.

## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u ervoor zorgen dat uw domein controller is gesynchroniseerd met Azure en kan worden omgezet van het virtuele Azure-netwerk (VNET) waarmee uw sessie-hosts zijn verbonden.

## <a name="set-up-a-storage-account"></a>Een opslag account instellen

Eerst moet u een opslag account voor Azure Files instellen.

Een opslag account instellen:

1. Meld u aan bij Azure Portal.

2. Zoek naar het **opslag account** in de zoek balk.

3. Selecteer **+Toevoegen**.

4. Voer de volgende gegevens in op de pagina  **opslag account maken** :

    - Een nieuwe resourcegroep maken.
    - Voer een unieke naam in voor het opslagaccount.
    - Voor de **locatie** raden we u aan om dezelfde locatie te kiezen als de Windows-hostgroep voor virtueel bureau blad.
    - Selecteer bij **Prestaties** de optie **Standaard**. (Afhankelijk van uw IOPS-vereisten. Zie [opslag opties voor FSLogix-profiel containers in Windows Virtual Desktop](store-fslogix-profile.md)voor meer informatie.)
    - Voor **account type** selecteert u **StorageV2** of **FileStorage** (alleen beschikbaar als het prestatie niveau Premium is).
    - Voor **replicatie** selecteert u **lokaal redundante opslag (LRS)**.

5. Wanneer u klaar bent, selecteert u **controleren + maken** en selecteert u **maken**.

Zie [regionale Beschik baarheid](../storage/files/storage-files-identity-auth-active-directory-enable.md#regional-availability)als u meer gedetailleerde configuratie-instructies nodig hebt.

## <a name="create-an-azure-file-share"></a>Een Azure-bestandsshare maken

Vervolgens moet u een Azure-bestands share maken.

Een bestandsshare maken:

1. Selecteer **Ga naar resource**.

2. Selecteer op de pagina Overzicht **Bestandsshares**.

3. Selecteer **+ Bestands** shares, maak een nieuwe bestands share met de naam **profielen** en voer vervolgens een geschikt quotum in of laat het veld leeg voor geen quota.

4. Selecteer **Maken**.

## <a name="enable-active-directory-authentication"></a>Active Directory-verificatie inschakelen

Vervolgens moet u Active Directory (AD)-verificatie inschakelen. Als u dit beleid wilt inschakelen, moet u de instructies in deze sectie volgen op een computer die al lid is van een domein. Als u verificatie wilt inschakelen, volgt u deze instructies op de VM waarop de domein controller wordt uitgevoerd:

1. Remote Desktop Protocol de virtuele machine die lid is van het domein.

2. Volg de instructies in [enable AD DS Authentication voor uw Azure-bestands shares](../storage/files/storage-files-identity-ad-ds-enable.md) om de AzFilesHybrid-module te installeren en verificatie in te scha kelen.

3.  Open de Azure Portal, open uw opslag account, selecteer **configuratie** en bevestig dat **Active Directory (AD)** is ingesteld op **ingeschakeld**.

     > [!div class="mx-imgBorder"]
     > ![Een scherm afbeelding van de configuratie pagina waarop Azure Active Directory (AD) is ingeschakeld.](media/active-directory-enabled.png)

## <a name="assign-azure-rbac-permissions-to-windows-virtual-desktop-users"></a>Azure RBAC-machtigingen toewijzen aan Windows-virtuele bureau blad-gebruikers

Voor alle gebruikers die FSLogix-profielen moeten hebben die zijn opgeslagen op het opslag account moet de rol opslag bestands gegevens SMB delen worden toegewezen.

Gebruikers die zich aanmelden bij de Windows Virtual Desktop-sessiehosts, hebben toegangsmachtigingen nodig voor toegang tot uw bestandsshare. Om toegang te verlenen tot een Azure-bestandsshare moeten machtigingen op zowel shareniveau als NTFS-niveau worden geconfigureerd, net als voor een traditionele Windows-share.

Als u machtigingen op share niveau wilt configureren, wijst u aan elke gebruiker een rol toe met de juiste toegangs machtigingen. Machtigingen kunnen worden toegewezen aan afzonderlijke gebruikers of een Azure AD-groep. Zie [toegangs machtigingen toewijzen aan een identiteit](../storage/files/storage-files-identity-ad-ds-assign-permissions.md)voor meer informatie.

>[!NOTE]
>De accounts of groepen waaraan u machtigingen toewijst, moeten zijn gemaakt in het domein en zijn gesynchroniseerd met Azure AD. Accounts die zijn gemaakt in Azure AD werken niet.

Machtigingen voor Azure op rollen gebaseerde toegangs beheer (Azure RBAC) toewijzen:

1. Open Azure Portal.

2. Open het opslag account dat u hebt gemaakt in [een opslag account instellen](#set-up-a-storage-account).

3. Selecteer **Bestands shares** en selecteer vervolgens de naam van de bestands share die u wilt gebruiken.

4. Selecteer **Access Control (IAM)**.

5. Selecteer **een roltoewijzing toevoegen**.

6. Selecteer in het tabblad roltoewijzing **toevoegen** de optie **opslag bestands gegevens SMB-share met verhoogde bevoegdheid** voor het beheerders account.

     Volg dezelfde instructies om gebruikersmachtigingen toe te wijzen voor hun FSLogix-profielen. Wanneer u echter naar stap 5 gaat, selecteert u in plaats daarvan **opslag bestands gegevens SMB delen** .

7. Selecteer **Opslaan**.

## <a name="assign-users-permissions-on-the-azure-file-share"></a>Gebruikers machtigingen toewijzen aan de Azure-bestands share

Zodra u Azure RBAC-machtigingen hebt toegewezen aan uw gebruikers, moet u de NTFS-machtigingen configureren.

U hebt twee dingen van de Azure Portal nodig om aan de slag te gaan:

- Het UNC-pad.
- De opslagaccountsleutel.

### <a name="get-the-unc-path"></a>Het UNC-pad ophalen

U kunt als volgt het UNC-pad ophalen:

1. Open Azure Portal.

2. Open het opslag account dat u hebt gemaakt in [een opslag account instellen](#set-up-a-storage-account).

3. Selecteer **instellingen** en selecteer vervolgens **Eigenschappen**.

4. Kopieer de URI van de **primaire bestands service-eind punt** naar de tekst editor van uw keuze.

5. Nadat u de URI hebt gekopieerd, moet u de volgende dingen doen om deze te wijzigen in de UNC:

    - Verwijderen `https://` en vervangen door `\\`
    - Vervang de slash door `/` een back slash `\` .
    - Voeg de naam toe van de bestands share die u hebt gemaakt in [een Azure-bestands share maken](#create-an-azure-file-share) aan het einde van de UNC.

        Bijvoorbeeld: `\\customdomain.file.core.windows.net\<fileshare-name>`

### <a name="get-the-storage-account-key"></a>Opslagaccountsleutel opvragen

De sleutel van het opslag account ophalen:

1. Open Azure Portal.

2. Open het opslag account dat u hebt gemaakt in [een opslag account instellen](#set-up-a-storage-account).

3. Op het tabblad **opslag account** selecteert u **toegangs sleutels**.

4. Kopieer **key1** of **Key2** naar een bestand op uw lokale computer.

### <a name="configure-ntfs-permissions"></a>NTFS-machtigingen configureren

Uw NTFS-machtigingen configureren:

1. Open een opdracht prompt op een VM die is gekoppeld aan een domein.

2. Voer de volgende opdracht uit om de Azure-bestands share te koppelen en een stationsletter toe te wijzen:

     ```cmd
     net use <desired-drive-letter>: <UNC-path> <SA-key> /user:Azure\<SA-name>
     ```

3. Voer de volgende opdracht uit om de toegangs machtigingen voor de Azure-bestands share te controleren:

    ```cmd
    icacls <mounted-drive-letter>:
    ```

    Vervang door `<mounted-drive-letter>` de letter van het station dat u hebt toegewezen aan.

    Zowel *NT Authority\Authenticated-gebruikers* als *BUILTIN\Users* hebben standaard bepaalde machtigingen. Met deze standaard machtigingen kunnen deze gebruikers de profiel containers van andere gebruikers lezen. Met de machtigingen die zijn beschreven in [opslag machtigingen configureren voor gebruik met profiel containers en Office-containers](/fslogix/fslogix-storage-config-ht) kunnen gebruikers echter niet de profiel containers van de andere personen lezen.

4. Voer de volgende opdrachten uit om uw Windows virtueel-bureaublad gebruikers in staat te stellen hun eigen profiel container te maken en de toegang tot hun profiel containers van andere gebruikers te blok keren.

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

     - Vervang <gekoppelde stationsletter> door de letter van het station dat u hebt gebruikt om het station toe te wijzen.
     - Vervang <e-mail> van de gebruiker door de UPN van de gebruiker of Active Directory groep die de gebruikers bevat die toegang moeten hebben tot de share.

     Bijvoorbeeld:

     ```cmd
     icacls <mounted-drive-letter>: /grant john.doe@contoso.com:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

## <a name="configure-fslogix-on-session-host-vms"></a>FSLogix configureren op Vm's van de host van sessies

In deze sectie wordt uitgelegd hoe u een virtuele machine configureert met FSLogix. U moet deze instructies volgen telkens wanneer u een sessiehost configureert. Voordat u begint met configureren, volgt u de instructies in [FSLogix downloaden en installeren](/fslogix/install-ht). Er zijn verschillende opties beschikbaar om ervoor te zorgen dat de registersleutels zijn ingesteld op alle sessiehosts. U kunt deze opties instellen in een installatiekopie of een groepsbeleid configureren.

Zo configureert u FSLogix op de VM van uw sessiehost:

1. RDP van de sessiehost van de host-VM van de virtuele Windows-bureau blad-groep.

2. [Down load en installeer FSLogix](/fslogix/install-ht).

5. Volg de instructies in [Registerinstellingen voor profielcontainer configureren](/fslogix/configure-profile-container-tutorial#configure-profile-container-registry-settings):

    - Navigeer naar **computer**  >  **HKEY_LOCAL_MACHINE**  >  **Software**  >  **FSLogix**.

    - Een sleutel voor **profielen** maken.

    - Maak **ingeschakeld, DWORD** met de waarde 1.

    - Maak **VHDLocations, MULTI_SZ**.

    - Stel de waarde van **VHDLocations** in op het UNC-pad dat u hebt gegenereerd in [het UNC-pad ophalen](#get-the-unc-path).

6. Start de VM opnieuw op.

## <a name="testing"></a>Testen

Nadat u FSLogix hebt geïnstalleerd en geconfigureerd, kunt u uw implementatie testen door u aan te melden met een gebruikers account dat is toegewezen aan een app-groep of bureau blad in de hostgroep. Zorg ervoor dat het gebruikers account waarmee u zich aanmeldt, gemachtigd is voor de bestands share.

Als de gebruiker zich eerder heeft aangemeld, hebben ze een bestaand lokaal profiel dat tijdens deze sessie wordt gebruikt. Om te voor komen dat u een lokaal profiel maakt, moet u een nieuw gebruikers account maken om te testen of de configuratie methoden gebruiken die worden beschreven in [zelf studie: profiel container configureren om gebruikers profielen om te leiden](/fslogix/configure-profile-container-tutorial/).

Controleer uw machtigingen voor uw sessie als volgt:

1. Start een sessie op het virtuele bureau blad van Windows.

2. Open Azure Portal.

3. Open het opslag account dat u hebt gemaakt in [een opslag account instellen](#set-up-a-storage-account).

4. Selecteer **een share maken** op de pagina een Azure-bestands share maken.

5. Zorg ervoor dat er in uw bestanden een map met het gebruikers profiel voor komt.

Volg de instructies in om te [controleren of uw profiel werkt](create-profile-container-adds.md#make-sure-your-profile-works)voor extra testen.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg [deze hand leiding voor het oplossen](/fslogix/fslogix-trouble-shooting-ht)van problemen met FSLogix.
