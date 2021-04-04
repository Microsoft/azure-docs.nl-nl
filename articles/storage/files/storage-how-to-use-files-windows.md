---
title: Een Azure-bestandsshare gebruiken met Windows | Microsoft Docs
description: Meer informatie over het gebruik van Azure-bestands shares met Windows en Windows Server. Gebruik Azure-bestands shares met SMB 3,0 op Windows-installaties die on-premises of op virtuele machines van Azure worden uitgevoerd.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e64b7efdd430287a7a3a969c5bf62b0c0e2aec9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94626891"
---
# <a name="use-an-azure-file-share-with-windows"></a>Een Azure-bestandsshare gebruiken met Windows
[Azure Files ](storage-files-introduction.md) is het eenvoudig te gebruiken cloudbestandssysteem van Microsoft. Azure-bestandsshares kunnen probleemloos worden gebruikt in Windows en Windows Server. In dit artikel worden de overwegingen besproken voor het gebruik van een Azure-bestandsshare met Windows en Windows Server.

Als u een Azure-bestandsshare wilt gebruiken buiten de Azure-regio waarin deze wordt gehost, bijvoorbeeld on-premises of in een andere Azure-regio, moet het besturingssysteem ondersteuning bieden voor SMB 3.0. 

U kunt Azure-bestandsshares gebruiken in een Windows-installatie die wordt uitgevoerd in een virtuele machine in Azure of on-premises. In de volgende tabel ziet u welke versies van het besturingssysteem de toegang tot bestandsshares ondersteunen en in welke omgeving:

| Windows-versie        | SMB-versie | Koppelbaar in Azure-VM | Koppelbaar on-premises |
|------------------------|-------------|-----------------------|-----------------------|
| Windows Server 2019 | SMB 3.0 | Ja | Ja |
| Windows 10<sup>1</sup> | SMB 3.0 | Ja | Ja |
| Windows Server Semi-Annual-kanaal<sup>2</sup> | SMB 3.0 | Ja | Ja |
| Windows Server 2016 | SMB 3.0 | Ja | Ja |
| Windows 8.1 | SMB 3.0 | Ja | Ja |
| Windows Server 2012 R2 | SMB 3.0 | Ja | Ja |
| Windows Server 2012 | SMB 3.0 | Ja | Ja |
| Windows 7<sup>3</sup> | SMB 2.1 | Ja | Nee |
| Windows Server 2008 R2<sup>3</sup> | SMB 2.1 | Ja | Nee |

<sup>1</sup> Windows 10, versies 1507, 1607, 1803, 1809, 1903, 1909 en 2004.  
<sup>2</sup> Windows Server, versies 1809, 1903, 1909, 2004.  
<sup>3</sup> Reguliere micro soft-ondersteuning voor Windows 7 en Windows Server 2008 R2 is beëindigd. Het is mogelijk om aanvullende ondersteuning voor beveiligings updates te kopen via het [Extended Security Update (ESU)-programma](https://support.microsoft.com/help/4497181/lifecycle-faq-extended-security-updates). We raden u ten zeerste aan de migratie uit te voeren van deze besturings systemen.

> [!Note]  
> We raden altijd aan de meest recente KB voor uw versie van Windows te nemen.

## <a name="prerequisites"></a>Vereisten 

Zorg ervoor dat poort 445 open is: het SMB-protocol vereist dat TCP-poort 445 open is; verbindingen mislukken als poort 445 is geblokkeerd. U kunt controleren of de firewall poort 445 blokkeert met de `Test-NetConnection` cmdlet. Zie de sectie [Oorzaak 1: poort 445 is geblokkeerd](storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked) van de Windows-probleemoplossings gids voor meer informatie over manieren om een geblokkeerde 445-poort te omzeilen.

## <a name="using-an-azure-file-share-with-windows"></a>Een Azure-bestandsshare gebruiken met Windows
Als u een Azure-bestandsshare met Windows wilt gebruiken, moet u deze koppelen, wat betekent dat u er een stationsletter of koppelingspunt aan moet toewijzen. U kunt ook toegang krijgen tot de share via het [UNC-pad](/windows/win32/fileio/naming-a-file). 

In dit artikel wordt de sleutel van het opslag account gebruikt om toegang te krijgen tot de bestands share. Een sleutel voor een opslag account is een beheerders sleutel voor een opslag account, met inbegrip van beheerders machtigingen voor alle bestanden en mappen in de bestands share waartoe u toegang hebt, en voor alle bestands shares en andere opslag resources (blobs, wacht rijen, tabellen, enzovoort) die zijn opgenomen in uw opslag account. Als dit niet voldoende is voor uw werk belasting, kan [Azure file sync](storage-sync-files-planning.md) worden gebruikt of kunt u [verificatie op basis van identiteiten via SMB](storage-files-active-directory-overview.md)gebruiken.

Een algemeen patroon om Line-Of-Business-toepassingen die een SMB-bestandsshare van Azure verwachten te verplaatsen, is om een Azure-bestandsshare te gebruiken als alternatief voor het uitvoeren van een toegewezen Windows-bestandsserver in een virtuele Azure-machine. Als u een Line-Of-Business-toepassing succesvol wilt migreren zodat deze een Azure-bestandsshare gebruikt, is een belangrijk aandachtspunt dat veel Line-of-Business-toepassingen worden uitgevoerd in de context van een toegewezen serviceaccount met beperkte systeemmachtigingen, in plaats van het beheerdersaccount van de virtuele machine. U moet er daarom voor zorgen dat u de referenties voor de Azure-bestandsshare koppelt/opslaat in de context van een serviceaccount in plaats van uw beheerdersaccount.

### <a name="mount-the-azure-file-share"></a>De Azure-bestands share koppelen

De Azure Portal biedt u een script dat u kunt gebruiken om uw bestands share rechtstreeks aan een host te koppelen. Het is raadzaam dit meegeleverde script te gebruiken.

Dit script ophalen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Navigeer naar het opslag account dat de bestands share bevat die u wilt koppelen.
1. Selecteer **Bestandsshares**.
1. Selecteer de bestands share die u wilt koppelen.

    :::image type="content" source="media/storage-how-to-use-files-windows/select-file-shares.png" alt-text="Hierbij":::

1. Selecteer **Verbinding maken**.

    :::image type="content" source="media/storage-how-to-use-files-windows/file-share-connect-icon.png" alt-text="Scherm afbeelding van het verbindings pictogram voor de bestands share.":::

1. Selecteer de stationsletter waaraan u de share wilt koppelen.
1. Kopieer het meegeleverde script.

    :::image type="content" source="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png" alt-text="Voorbeeld tekst":::

1. Plak het script in een shell op de host waaraan u de bestands share wilt koppelen en voer dit uit.

U hebt nu uw Azure-bestands share gekoppeld.

### <a name="mount-the-azure-file-share-with-file-explorer"></a>De Azure-bestandsshare koppelen met de Verkenner
> [!Note]  
> Houd er rekening mee dat de volgende instructies worden weergegeven in Windows 10 en enigszins kunnen verschillen in oudere versies. 

1. Open Verkenner. Dit kan worden gedaan door deze te openen vanuit het menu Start of door op de snelkoppeling Win + E te drukken.

1. Navigeer naar **deze PC** aan de linkerkant van het venster. Hiermee wijzigt u de menu's die beschikbaar zijn in het lint. Selecteer in het menu computer de optie **netwerk station toewijzen**.
    
    ![Een schermafbeelding van de vervolgkeuzelijst 'Netwerkverbinding maken'](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

1. Selecteer de stationsletter en voer het UNC-pad in, de indeling van het UNC-pad `\\<storageAccountName>.file.core.windows.net\<fileShareName>` . Bijvoorbeeld: `\\anexampleaccountname.file.core.windows.net\example-share-name`.
    
    ![Een schermafbeelding van het dialoogvenster 'Netwerkverbinding maken'](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

1. Gebruik de opslagaccountnaam voorafgegaan door `AZURE\` als de gebruikersnaam en een toegangssleutel als het wachtwoord.
    
    ![Een schermafbeelding van het dialoogvenster voor netwerkreferenties](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

1. Gebruik de Azure-bestandsshare naar wens.
    
    ![De Azure-bestandsshare is nu gekoppeld](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

1. Wanneer u klaar bent om de Azure-bestandsshare te ontkoppelen, kunt u dit doen door met de rechtermuisknop in de Verkenner op de vermelding voor de share onder **Netwerklocaties** te klikken en **Verbinding verbreken** te selecteren.

### <a name="accessing-share-snapshots-from-windows"></a>Toegang tot momentopnamen van Windows-shares
Als u een momentopname van een share hebt gemaakt, ofwel handmatig ofwel automatisch met behulp van een script of een service zoals Azure Backup, kunt u eerdere versies van een share, een map of een bepaald bestand op een bestandsshare van Windows bekijken. U kunt een moment opname van een share maken met behulp van [Azure PowerShell](storage-how-to-use-files-powershell.md), [Azure CLI](storage-how-to-use-files-cli.md)of de [Azure Portal](storage-how-to-use-files-portal.md).

#### <a name="list-previous-versions"></a>Vorige versies weergeven
Blader naar het item of het bovenliggende item dat moet worden teruggezet. Dubbelklik om naar de gewenste map te gaan. Klik er met de rechtermuisknop op en selecteer **Eigenschappen** in het menu.

![Snelmenu voor een geselecteerde map](./media/storage-how-to-use-files-windows/snapshot-windows-previous-versions.png)

Selecteer **Vorige versies** om de lijst met momentopnamen van shares voor deze map weer te geven. Het kan enkele seconden duren voordat deze lijst is geladen. Dit hangt af van de netwerksnelheid en het aantal momentopnamen van shares in de map.

![Tabblad Vorige versies](./media/storage-how-to-use-files-windows/snapshot-windows-list.png)

U kunt **Openen** selecteren om een bepaalde momentopname te openen. 

![Geopende momentopname](./media/storage-how-to-use-files-windows/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>Terugzetten op basis van een vorige versie
Selecteer **Terugzetten** om de inhoud van de gehele map op de aanmaaktijd van de momentopname van de share recursief naar de oorspronkelijke locatie te kopiëren.

 ![De knop Terugzetten in een waarschuwingsbericht](./media/storage-how-to-use-files-windows/snapshot-windows-restore.png) 

## <a name="securing-windowswindows-server"></a>Windows/Windows Server beveiligen
Als u een Azure-bestandsshare in Windows wilt koppelen, moet poort 445 toegankelijk zijn. Veel organisaties blokkeren poort 445 vanwege de beveiligingsrisico's die bij SMB 1 horen. SMB 1, ook wel bekend als CIFS (Common Internet File System), is een oud bestandssysteemprotocol in Windows en Windows Server. SMB 1 is een verouderd, inefficiënt en bovenal onveilig protocol. Het goede nieuws is dat Azure Files SMB 1 niet ondersteunt en dat alle ondersteunde versies van Windows en Windows Server het mogelijk maken om SMB 1 te verwijderen of uit te schakelen. We raden altijd [ten zeerste](https://aka.ms/stopusingsmb1) aan om de SMB 1-client en -server in Windows te verwijderen of uit te schakelen voordat u Azure-bestandsshares in een productieomgeving gebruikt.

In de volgende tabel staat gedetailleerde informatie over de status van SMB 1 in elke versie van Windows:

| Windows-versie                           | Standaardstatus van SMB 1 | Uitschakel-/verwijdermethode       | 
|-------------------------------------------|----------------------|-----------------------------|
| Windows Server 2019                       | Uitgeschakeld             | Verwijderen met Windows-functie |
| Windows Server-versie 1709 en hoger            | Uitgeschakeld             | Verwijderen met Windows-functie |
| Windows 10-versie 1709 en hoger                | Uitgeschakeld             | Verwijderen met Windows-functie |
| Windows Server 2016                       | Ingeschakeld              | Verwijderen met Windows-functie |
| Windows 10-versies 1507, 1607 en 1703 | Ingeschakeld              | Verwijderen met Windows-functie |
| Windows Server 2012 R2                    | Ingeschakeld              | Verwijderen met Windows-functie | 
| Windows 8.1                               | Ingeschakeld              | Verwijderen met Windows-functie | 
| Windows Server 2012                       | Ingeschakeld              | Uitschakelen met Register       | 
| Windows Server 2008 R2                    | Ingeschakeld              | Uitschakelen met Register       |
| Windows 7                                 | Ingeschakeld              | Uitschakelen met Register       | 

### <a name="auditing-smb-1-usage"></a>SMB 1-gebruik controleren
> Van toepassing op Windows Server 2019, Windows Server Semi-Annual-kanaal (versie 1709 en 1803), Windows Server 2016, Windows 10 (versies 1507, 1607, 1703, 1709 en 1803), Windows Server 2012 R2 en Windows 8,1

Voordat u SMB 1 uit uw omgeving verwijdert, wilt u mogelijk het gebruik van SMB 1 controleren om na te gaan of er geen clients beschadigd raken door de wijziging. Als er verzoeken worden gedaan aan SMB-shares met SMB 1, wordt er in het gebeurtenissenlogboek onder `Applications and Services Logs > Microsoft > Windows > SMBServer > Audit` een controlegebeurtenis geregistreerd. 

> [!Note]  
> Als u ondersteuning voor controle wilt inschakelen op Windows Server 2012 R2 en Windows 8.1, installeert u ten minste [KB4022720](https://support.microsoft.com/help/4022720/windows-8-1-windows-server-2012-r2-update-kb4022720).

Als u controle wilt inschakelen, voert u de volgende cmdlet uit in een PowerShell-sessie met verhoogde bevoegdheden:

```powershell
Set-SmbServerConfiguration –AuditSmb1Access $true
```

### <a name="removing-smb-1-from-windows-server"></a>SMB 1 verwijderen van Windows Server
> Van toepassing op Windows Server 2019, Windows Server Semi-Annual-kanaal (versie 1709 en 1803), Windows Server 2016, Windows Server 2012 R2

Als u SMB 1 van een Windows Server-exemplaar wilt verwijderen, voert u de volgende cmdlet uit in een PowerShell-sessie met verhoogde bevoegdheden:

```powershell
Remove-WindowsFeature -Name FS-SMB1
```

Start uw server opnieuw op om het verwijderingsproces te voltooien. 

> [!Note]  
> Vanaf Windows 10 en Windows Server-versie 1709 is SMB 1 niet meer standaard geïnstalleerd en heeft SMB 1 afzonderlijke Windows-functies voor de SMB 1-client en SMB 1-server. We raden altijd aan om zowel de SMB 1-server (`FS-SMB1-SERVER`) als de SMB 1-client (`FS-SMB1-CLIENT`) niet te installeren.

### <a name="removing-smb-1-from-windows-client"></a>SMB 1 verwijderen van een Windows-client
> Van toepassing op Windows 10 (versies 1507, 1607, 1703, 1709 en 1803) en Windows 8.1

Als u SMB 1 wilt verwijderen van uw Windows-client, voert u de volgende cmdlet uit in een PowerShell-sessie met verhoogde bevoegdheden:

```powershell
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
```

Start uw pc opnieuw op om het verwijderingsproces te voltooien.

### <a name="disabling-smb-1-on-legacy-versions-of-windowswindows-server"></a>SMB 1 uitschakelen op oudere versies van Windows/Windows Server
> Van toepassing op Windows Server 2012, Windows Server 2008 R2 en Windows 7

SMB 1 kan niet volledig worden verwijderd van ouders versies van Windows/Windows Server, maar kan worden uitgeschakeld via het register. Als u SMB 1 wilt uitschakelen, maakt u een nieuw registersleutel `SMB1` van type `DWORD` met een waarde van `0` onder `HKEY_LOCAL_MACHINE > SYSTEM > CurrentControlSet > Services > LanmanServer > Parameters`.

Dit kunt u ook eenvoudig doen met de volgende PowerShell-cmdlet:

```powershell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" SMB1 -Type DWORD -Value 0 –Force
```

Nadat u deze registersleutel hebt gemaakt, moet u uw server opnieuw opstarten om SMB 1 uit te schakelen.

### <a name="smb-resources"></a>SMB-resources
- [Stoppen met SMB 1 gebruiken](https://blogs.technet.microsoft.com/filecab/2016/09/16/stop-using-smb1/)
- [SMB 1 Product Clearinghouse](https://blogs.technet.microsoft.com/filecab/2017/06/01/smb1-product-clearinghouse/)
- [SMB 1 detecteren in uw omgeving met DSCEA](/archive/blogs/ralphkyttle/discover-smb1-in-your-environment-with-dscea)
- [SMB 1 uitschakelen via groepsbeleid](/archive/blogs/secguide/disabling-smbv1-through-group-policy)

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende koppelingen voor meer informatie over Azure Files:
- [Een Azure Files-implementatie plannen](storage-files-planning.md)
- [Veelgestelde vragen](./storage-files-faq.md)
- [Problemen oplossen in Windows](storage-troubleshoot-windows-file-connection-problems.md)