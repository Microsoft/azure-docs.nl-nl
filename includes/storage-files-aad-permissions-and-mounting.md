---
title: bestand opnemen
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/26/2020
ms.author: rogara
ms.custom: include file
ms.openlocfilehash: 4773446ec0007ffbed99bc01939d1f92f5823d99
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95559406"
---
## <a name="assign-access-permissions-to-an-identity"></a>Toegangs machtigingen toewijzen aan een identiteit

Om toegang te krijgen tot Azure Files bronnen met verificatie op basis van de identiteit, moet een identiteit (een gebruiker, groep of Service-Principal) de benodigde machtigingen op het share niveau hebben. Dit proces is vergelijkbaar met het opgeven van machtigingen voor Windows share, waar u het type toegang opgeeft dat een bepaalde gebruiker heeft voor een bestands share. De richt lijnen in deze sectie laten zien hoe u machtigingen voor lezen, schrijven of verwijderen voor een bestands share toewijst aan een identiteit. 

We hebben drie ingebouwde rollen geïntroduceerd voor het verlenen van machtigingen op share niveau voor gebruikers:

- Met de **SMB-share lezer van het opslag bestand** wordt lees toegang tot Azure Storage bestands shares via SMB toegestaan.
- **Gegevens van het opslag bestand SMB delen Inzender** maakt lees-, schrijf-en verwijder toegang mogelijk in azure Storage bestands shares via SMB.
- **Opslag bestands gegevens SMB-share met verhoogde bevoegdheid** is voor lezen, schrijven, verwijderen en wijzigen van NTFS-machtigingen in azure Storage bestands shares via SMB toegestaan.

> [!IMPORTANT]
> Volledig beheer van een bestands share, inclusief de mogelijkheid om eigenaar te worden van een bestand, vereist het gebruik van de sleutel van het opslag account. Beheer beheer wordt niet ondersteund met Azure AD-referenties.

U kunt de Azure Portal, Power shell of Azure CLI gebruiken om de ingebouwde rollen toe te wijzen aan de Azure AD-identiteit van een gebruiker voor het verlenen van machtigingen op share niveau. Houd er rekening mee dat de toewijzing van Azure-rollen op share niveau enige tijd kan duren. 

> [!NOTE]
> Vergeet niet om [uw AD DS referenties te synchroniseren met Azure AD](../articles/active-directory/hybrid/how-to-connect-install-roadmap.md) als u van plan bent om uw on-premises AD DS voor verificatie te gebruiken. Wachtwoord-hash-synchronisatie van AD DS naar Azure AD is optioneel. Machtigingen op share niveau worden verleend aan de Azure AD-identiteit die is gesynchroniseerd met uw on-premises AD DS.

De algemene aanbeveling is om machtigingen op share niveau te gebruiken voor toegangs beheer op hoog niveau tot een AD-groep die een groep gebruikers en identiteiten vertegenwoordigt. vervolgens wordt gebruikgemaakt van NTFS-machtigingen voor gedetailleerd toegangs beheer op Directory-of bestands niveau. 

### <a name="assign-an-azure-role-to-an-ad-identity"></a>Een Azure-rol toewijzen aan een AD-identiteit

# <a name="portal"></a>[Portal](#tab/azure-portal)
Voer de volgende stappen uit om een Azure-rol toe te wijzen aan een Azure AD-identiteit met behulp van de [Azure Portal](https://portal.azure.com):

1. Ga in het Azure Portal naar de bestands share of [Maak een bestands share](../articles/storage/files/storage-how-to-create-file-share.md).
2. Selecteer **Access Control (IAM)**.
3. Selecteer **een roltoewijzing toevoegen**
4. Selecteer in de Blade **roltoewijzing toevoegen** de geschikte ingebouwde rol (opslag BESTANDS gegevens SMB-share, opslag BESTANDS gegevens SMB delen Inzender) uit de lijst met **functies** . Zorg ervoor dat er geen **toegang wordt toegewezen aan** de standaard instelling: **Azure AD-gebruiker,-groep of Service-Principal**. Selecteer de Azure AD-doel-id op naam of e-mail adres.
5. Selecteer **Opslaan** om de roltoewijzings bewerking te volt ooien.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

In het volgende Power shell-voor beeld ziet u hoe u een Azure-rol toewijst aan een Azure AD-identiteit, op basis van aanmeldings naam. Zie [toegang beheren met RBAC en Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md)voor meer informatie over het toewijzen van Azure-functies met Power shell.

Voordat u het volgende voorbeeld script uitvoert, moet u de waarden van de tijdelijke aanduidingen vervangen door uw eigen waarden.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
  
De volgende CLI 2,0-opdracht laat zien hoe u een Azure-rol toewijst aan een Azure AD-identiteit, op basis van aanmeldings naam. Zie [toegang beheren met RBAC en Azure cli](../articles/role-based-access-control/role-assignments-cli.md)voor meer informatie over het toewijzen van Azure-rollen met Azure cli. 

Voordat u het volgende voorbeeld script uitvoert, moet u de waarden van de tijdelijke aanduidingen vervangen door uw eigen waarden.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```
---

## <a name="configure-ntfs-permissions-over-smb"></a>NTFS-machtigingen configureren via SMB

Nadat u machtigingen op share niveau hebt toegewezen met RBAC, moet u de juiste NTFS-machtigingen toewijzen op het niveau root, Directory of file. U kunt machtigingen op share niveau beschouwen als de gate keeper op hoog niveau die bepaalt of een gebruiker toegang heeft tot de share. Terwijl NTFS-machtigingen op een meer gedetailleerd niveau handelen om te bepalen welke bewerkingen de gebruiker kan uitvoeren op het niveau van de map of het bestand.

Azure Files ondersteunt de volledige set met NTFS Basic-en geavanceerde machtigingen. U kunt NTFS-machtigingen voor mappen en bestanden in een Azure-bestands share weer geven en configureren door de share te koppelen en vervolgens Windows Verkenner of de opdracht Windows [icacls](/windows-server/administration/windows-commands/icacls) of [set-ACL](/powershell/module/microsoft.powershell.security/set-acl) uit te voeren. 

Als u NTFS wilt configureren met Gebruikersaccountbeheer, moet u de share koppelen met behulp van de sleutel van uw opslag account van de virtuele machine die lid is van het domein. Volg de instructies in de volgende sectie om een Azure-bestands share te koppelen vanaf de opdracht prompt en de NTFS-machtigingen dienovereenkomstig te configureren.

De volgende sets machtigingen worden ondersteund in de hoofdmap van een bestands share:

- BUILTIN\Administrators: (OI) (CI) (F)
- NT AUTHORITY\SYSTEM: (OI) (CI) (F)
- BUILTIN\Users: (RX)
- BUILTIN\Users: (OI) (CI) (i/o) (GR, GE)
- NT AUTHORITY\Authenticated-gebruikers: (OI) (CI) (M)
- NT AUTHORITY\SYSTEM: (F)
- MAKER EIGENAAR: (OI) (CI) (I/O) (F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>Een bestands share koppelen vanaf de opdracht prompt

Gebruik de Windows **net use** -opdracht om de Azure-bestands share te koppelen. Vergeet niet om de waarden van de tijdelijke aanduidingen in het volgende voor beeld te vervangen door uw eigen waarden. Zie [een Azure-bestands share gebruiken met Windows](../articles/storage/files/storage-how-to-use-files-windows.md)voor meer informatie over het koppelen van bestands shares. 

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
 net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
 Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

Als u problemen ondervindt bij het maken van verbinding met Azure Files, raadpleegt u [het hulp programma voor probleem oplossing dat is gepubliceerd voor Azure files-koppelings fouten in Windows](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/). We bieden ook [richt lijnen](../articles/storage/files/storage-files-faq.md#on-premises-access) voor het omzeilen van scenario's wanneer poort 445 wordt geblokkeerd. 


### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>NTFS-machtigingen configureren met Windows Verkenner

Gebruik Windows Verkenner om volledige machtigingen te verlenen aan alle mappen en bestanden onder de bestands share, met inbegrip van de hoofdmap.

1. Open Windows Verkenner, klik met de rechter muisknop op het bestand of de map en selecteer **Eigenschappen**.
2. Selecteer het tabblad **Beveiliging**.
3. Selecteer **bewerken..** om machtigingen te wijzigen.
4. U kunt de machtigingen van bestaande gebruikers wijzigen of **toevoegen selecteren...** om machtigingen te verlenen aan nieuwe gebruikers.
5. In het prompt venster voor het toevoegen van nieuwe gebruikers typt u de naam van de doel gebruiker waaraan u machtigingen wilt verlenen in het vak **Geef de object namen op** en selecteert u **Namen controleren** om de volledige UPN-naam van de doel gebruiker te zoeken.
7.    Selecteer **OK**.
8.    Selecteer op het tabblad **beveiliging** alle machtigingen die u wilt toewijzen aan de nieuwe gebruiker.
9.    Selecteer **Toepassen**.

### <a name="configure-ntfs-permissions-with-icacls"></a>NTFS-machtigingen configureren met icacls

Gebruik de volgende Windows-opdracht om volledige machtigingen te verlenen aan alle mappen en bestanden onder de bestands share, met inbegrip van de hoofdmap. Vergeet niet om de waarden van de tijdelijke aanduidingen in het voor beeld te vervangen door uw eigen waarden.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Zie voor meer informatie over het gebruik van icacls voor het instellen van NTFS-machtigingen en voor de verschillende typen ondersteunde machtigingen [de opdracht regel verwijzing voor icacls](/windows-server/administration/windows-commands/icacls).

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Een bestands share koppelen vanaf een virtuele machine die lid is van een domein

Met het volgende proces wordt gecontroleerd of uw bestands share en toegangs machtigingen juist zijn ingesteld en of u toegang hebt tot een Azure-bestands share vanaf een virtuele machine die lid is van een domein. Houd er rekening mee dat de toewijzing van Azure-rollen op share niveau enige tijd kan duren. 

Meld u aan bij de virtuele machine met behulp van de Azure AD-identiteit waaraan u machtigingen hebt verleend, zoals wordt weer gegeven in de volgende afbeelding. Als u on-premises AD DS verificatie voor Azure Files hebt ingeschakeld, gebruikt u uw AD DS referenties. Meld u aan met Azure AD-referenties voor Azure AD DS-verificatie.

![Scherm afbeelding van het aanmeldings scherm van Azure AD voor gebruikers verificatie](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

Gebruik de volgende opdracht om de Azure-bestands share te koppelen. Vergeet niet om de waarden van de tijdelijke aanduidingen te vervangen door uw eigen waarden. Omdat u bent geverifieerd, hoeft u de sleutel voor het opslag account, de lokale AD DS referenties of de referenties van de Azure-AD DS niet op te geven. Eenmalige aanmelding wordt ondersteund voor verificatie met on-premises AD DS of Azure AD DS. Als u problemen ondervindt met het koppelen van AD DS referenties, raadpleegt u [Azure files problemen oplossen in Windows](../articles/storage/files/storage-troubleshoot-windows-file-connection-problems.md) voor meer informatie.

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
 net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
 Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}
```