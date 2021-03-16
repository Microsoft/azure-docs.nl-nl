---
title: 'Bepalen wat een gebruiker op het bestands niveau kan doen: Azure-bestands shares'
description: Meer informatie over het configureren van Windows Acl's-machtigingen voor on-premises AD DS-verificatie voor Azure-bestands shares. U kunt profiteren van nauw keurig toegangs beheer.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/16/2020
ms.author: rogarana
ms.openlocfilehash: 698b4ebedfc9b41e8c5732a0a81226a971d65585
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103470761"
---
# <a name="part-three-configure-directory-and-file-level-permissions-over-smb"></a>Deel drie: machtigingen voor mappen en bestands niveau configureren via SMB 

Voordat u aan dit artikel begint, moet u ervoor zorgen dat u het vorige artikel hebt voltooid, [machtigingen op share niveau toewijzen aan een identiteit](storage-files-identity-ad-ds-assign-permissions.md) om ervoor te zorgen dat uw machtigingen op share niveau aanwezig zijn.

Nadat u machtigingen op share niveau hebt toegewezen met Azure RBAC, moet u de juiste Windows-Acl's configureren op basis van de hoofdmap, map of bestands niveau om te profiteren van nauw keurig toegangs beheer. U kunt de machtigingen op share niveau van Azure RBAC beschouwen als de gate keeper die bepaalt of een gebruiker toegang heeft tot de share. Hoewel de Windows-Acl's op een meer gedetailleerd niveau werken om te bepalen welke bewerkingen de gebruiker kan uitvoeren op het niveau van de map of het bestand. Machtigingen op share niveau en op het niveau van bestanden en mappen worden afgedwongen wanneer een gebruiker toegang probeert te krijgen tot een bestand of map, dus als er een verschil is tussen een van beide, wordt alleen de meest beperkende waarde toegepast. Als een gebruiker bijvoorbeeld lees-en schrijf toegang heeft op bestands niveau, maar alleen op share niveau leest, kan het bestand alleen worden gelezen. Hetzelfde geldt als het is omgekeerd, en een gebruiker had Lees-en schrijf toegang op het niveau van de share, maar alleen lezen op bestands niveau, maar kan het bestand nog steeds lezen.

## <a name="azure-rbac-permissions"></a>Azure RBAC-machtigingen

De volgende tabel bevat de Azure RBAC-machtigingen die zijn gerelateerd aan deze configuratie:


| Ingebouwde rol  | NTFS-machtiging  | Resulterende toegang  |
|---------|---------|---------|
|Lezer voor opslagbestandgegevens via SMB-share | Volledig beheer, wijzigen, lezen, schrijven, uitvoeren | Lezen & uitvoeren  |
|     |   Lezen |     Lezen  |
|Inzender voor opslagbestandsgegevens via SMB-share  |  Volledig beheer    |  Wijzigen, lezen, schrijven, uitvoeren |
|     |  Wijzigen         |  Wijzigen    |
|     |  Lezen & uitvoeren |  Lezen & uitvoeren |
|     |  Lezen           |  Lezen    |
|     |  Schrijven          |  Schrijven   |
|Inzender met verhoogde bevoegdheden voor opslagbestandsgegevens via SMB-share | Volledig beheer  |  Wijzigen, lezen, schrijven, bewerken, uitvoeren |
|     |  Wijzigen          |  Wijzigen |
|     |  Lezen & uitvoeren  |  Lezen & uitvoeren |
|     |  Lezen            |  Lezen   |
|     |  Schrijven           |  Schrijven  |



## <a name="supported-permissions"></a>Ondersteunde machtigingen

Azure Files ondersteunt de volledige set basis-en geavanceerde Windows-Acl's. U kunt Windows-Acl's op mappen en bestanden in een Azure-bestands share weer geven en configureren door de share te koppelen en vervolgens met behulp van Windows Verkenner, de Windows [icacls](/windows-server/administration/windows-commands/icacls) -opdracht of de [set-ACL](/powershell/module/microsoft.powershell.security/set-acl) opdracht uit te voeren. 

Als u Acl's wilt configureren met machtigingen voor de super gebruiker, moet u de share koppelen met behulp van de sleutel van uw opslag account van de virtuele machine die lid is van het domein. Volg de instructies in de volgende sectie voor het koppelen van een Azure-bestands share vanaf de opdracht prompt en voor het configureren van Windows-Acl's.

De volgende machtigingen zijn opgenomen in de hoofdmap van een bestands share:

- BUILTIN\Administrators: (OI) (CI) (F)
- BUILTIN\Users: (RX)
- BUILTIN\Users: (OI) (CI) (i/o) (GR, GE)
- NT AUTHORITY\Authenticated-gebruikers: (OI) (CI) (M)
- NT AUTHORITY\SYSTEM: (OI) (CI) (F)
- NT AUTHORITY\SYSTEM: (F)
- MAKER EIGENAAR: (OI) (CI) (I/O) (F)

|Gebruikers|Definitie|
|---|---|
|BUILTIN\Administrators|Alle gebruikers die domein beheerders zijn van de on-premises AD DS omgeving.
|BUILTIN\Users|Ingebouwde beveiligings groep in AD. Het bevat standaard NT AUTHORITY\Authenticated-gebruikers. Voor een traditionele Bestands server kunt u de definitie van het lidmaatschap per server configureren. Voor Azure Files is er geen hosting server, dus BUILTIN\Users bevat dezelfde set gebruikers als NT AUTHORITY\Authenticated-gebruikers.|
|NT AUTHORITY\SYSTEM|Het service account van het besturings systeem van de bestands server. Dit service account is niet van toepassing in Azure Files context. De map is opgenomen in de hoofdmap om consistent te zijn met de Windows-Server ervaring voor hybride scenario's.|
|NT AUTHORITY\Authenticated-gebruikers|Alle gebruikers in AD die een geldig Kerberos-token kunnen verkrijgen.|
|MAKER EIGENAAR|Elk object van een map of bestand heeft een eigenaar voor dat object. Als er voor dat object Acl's zijn toegewezen aan de maker eigenaar, heeft de gebruiker die de eigenaar van dit object is, de machtigingen voor het object dat door de ACL is gedefinieerd.|



## <a name="mount-a-file-share-from-the-command-prompt"></a>Een bestands share koppelen vanaf de opdracht prompt

Gebruik de Windows- `net use` opdracht om de Azure-bestands share te koppelen. Vergeet niet om de waarden van de tijdelijke aanduidingen in het volgende voor beeld te vervangen door uw eigen waarden. Zie [een Azure-bestands share gebruiken met Windows](storage-how-to-use-files-windows.md)voor meer informatie over het koppelen van bestands shares. 

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
  net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
} 
else 
{
  Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN,   Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

Als u problemen ondervindt bij het maken van verbinding met Azure Files, raadpleegt u [het hulp programma voor probleem oplossing dat is gepubliceerd voor Azure files-montage fouten in Windows](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/). We bieden ook [richt lijnen](./storage-files-faq.md#on-premises-access) voor het omzeilen van scenario's wanneer poort 445 wordt geblokkeerd. 

## <a name="configure-windows-acls"></a>Windows-Acl's configureren

Als de bestands share is gekoppeld aan de sleutel voor het opslag account, moet u de Windows-Acl's (ook wel NTFS-machtigingen genoemd) configureren. U kunt de Windows-Acl's configureren met behulp van Windows Verkenner of icacls.

Als u mappen of bestanden hebt in on-premises bestands servers met Windows-DACL'S die zijn geconfigureerd voor de AD DS identiteiten, kunt u deze kopiëren naar Azure Files persistentie van de Acl's met bestanden voor het traditionele Kopieer programma zoals Robocopy of [Azure AzCopy v 10.4 +](https://github.com/Azure/azure-storage-azcopy/releases). Als uw mappen en bestanden worden getierd tot Azure Files via Azure File Sync, worden uw Acl's overgenomen en bewaard in de oorspronkelijke indeling.

### <a name="configure-windows-acls-with-icacls"></a>Windows-Acl's configureren met icacls

Gebruik de volgende Windows-opdracht om volledige machtigingen te verlenen aan alle mappen en bestanden onder de bestands share, met inbegrip van de hoofdmap. Vergeet niet om de waarden van de tijdelijke aanduidingen in het voor beeld te vervangen door uw eigen waarden.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Zie voor meer informatie over het gebruik van icacls voor het instellen van Windows-Acl's en voor de verschillende typen ondersteunde machtigingen [de opdracht regel verwijzing voor icacls](/windows-server/administration/windows-commands/icacls).

### <a name="configure-windows-acls-with-windows-file-explorer"></a>Windows-Acl's configureren met Windows-bestanden Verkenner

Gebruik Windows Verkenner om volledige machtigingen te verlenen aan alle mappen en bestanden onder de bestands share, met inbegrip van de hoofdmap. Als u de AD-domein gegevens niet correct in Windows Verkenner kunt laden, is dit waarschijnlijk het gevolg van een vertrouwens configuratie in uw on-premises AD-omgeving. De client computer kan de AD-domein controller die is geregistreerd voor Azure Files authenticatie niet bereiken. In dit geval gebruikt u icacls voor configurating Windows-Acl's.

1. Open Windows Verkenner, klik met de rechter muisknop op het bestand of de map en selecteer **Eigenschappen**.
1. Selecteer het tabblad **Beveiliging**.
1. Selecteer **bewerken..** om machtigingen te wijzigen.
1. U kunt de machtigingen van bestaande gebruikers wijzigen of **toevoegen selecteren...** om machtigingen te verlenen aan nieuwe gebruikers.
1. Geef in het prompt venster voor het toevoegen van nieuwe gebruikers de doel gebruikersnaam op waaraan u machtigingen wilt verlenen in het vak **Geef de object namen op** en selecteer **Namen controleren** om de volledige UPN-naam van de doel gebruiker te zoeken.
1.    Selecteer **OK**.
1.    Selecteer op het tabblad **beveiliging** alle machtigingen die u wilt toewijzen aan de nieuwe gebruiker.
1.    Selecteer **Toepassen**.


## <a name="next-steps"></a>Volgende stappen

Nu de functie is ingeschakeld en geconfigureerd, gaat u verder met het volgende artikel, waar u uw Azure-bestands share koppelt van een virtuele machine die lid is van een domein.

[Deel vier: een bestands share koppelen vanaf een virtuele machine die lid is van een domein](storage-files-identity-ad-ds-mount-file-share.md)
