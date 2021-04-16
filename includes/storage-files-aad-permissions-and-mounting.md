---
title: bestand opnemen
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/26/2020
ms.author: rogara
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 200bf290543747cf9abab6113b8013e2eec852a8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107513211"
---
## <a name="assign-access-permissions-to-an-identity"></a>Toegangsmachtigingen toewijzen aan een identiteit

Voor toegang Azure Files resources met verificatie op basis van identiteit, moet een identiteit (een gebruiker, groep of service-principal) de benodigde machtigingen hebben op shareniveau. Dit proces is vergelijkbaar met het opgeven van machtigingen voor Windows-share, waarbij u het type toegang opgeeft dat een bepaalde gebruiker heeft voor een bestands share. In de richtlijnen in deze sectie wordt gedemonstreerd hoe u lees-, schrijf- of verwijdermachtigingen voor een bestands share toewijst aan een identiteit. 

We hebben drie ingebouwde Azure-rollen geïntroduceerd voor het verlenen van machtigingen op shareniveau aan gebruikers:

- **SMB-sharelezer voor opslagbestandsgegevens** biedt leestoegang in Azure Storage via SMB.
- **Inzender voor opslagbestandsgegevens via SMB-share** staat lees-, schrijf- en verwijdertoegang toe in Azure Storage via SMB.
- **Inzender voor** opslagbestandsgegevens met verhoogde SMB-share staat lezen, schrijven, verwijderen en wijzigen van NTFS-machtigingen toe in Azure Storage via SMB.

> [!IMPORTANT]
> Voor volledig beheer van een bestands share, inclusief de mogelijkheid om eigenaar te worden van een bestand, is het gebruik van de sleutel van het opslagaccount vereist. Beheer wordt niet ondersteund met Azure AD-referenties.

U kunt de Azure Portal, PowerShell of Azure CLI gebruiken om de ingebouwde rollen toe te wijzen aan de Azure AD-identiteit van een gebruiker voor het verlenen van machtigingen op shareniveau. Het kan even duren voor de Azure-roltoewijzing op shareniveau van kracht is. 

> [!NOTE]
> Vergeet niet [om uw AD DS te synchroniseren met Azure AD](../articles/active-directory/hybrid/how-to-connect-install-roadmap.md) als u van plan bent om uw on-premises AD DS te gebruiken voor verificatie. Wachtwoordhashsynchronisatie van AD DS naar Azure AD is optioneel. Machtigingen op shareniveau worden verleend aan de Azure AD-identiteit die is gesynchroniseerd vanuit uw on-premises AD DS.

De algemene aanbeveling is om machtigingen op shareniveau te gebruiken voor toegangsbeheer op hoog niveau voor een AD-groep die een groep gebruikers en identiteiten vertegenwoordigt, en vervolgens NTFS-machtigingen te gebruiken voor gedetailleerd toegangsbeheer op map-/bestandsniveau. 

### <a name="assign-an-azure-role-to-an-ad-identity"></a>Een Azure-rol toewijzen aan een AD-identiteit

# <a name="portal"></a>[Portal](#tab/azure-portal)
Als u een Azure-rol wilt toewijzen aan een Azure AD-identiteit met behulp van Azure Portal [,](https://portal.azure.com)volgt u deze stappen:

1. Ga in Azure Portal naar uw bestands share of [Maak een bestands share](../articles/storage/files/storage-how-to-create-file-share.md).
2. Selecteer **Access Control (IAM)**.
3. Selecteer **Een roltoewijzing toevoegen**
4. Selecteer op **de** blade Roltoewijzing toevoegen de juiste ingebouwde rol (Lezer van opslagbestandsgegevens SMB-share, Inzender voor opslagbestandsgegevens SMB-share) in **de lijst Rol.** Laat **Toegang toewijzen aan op** de standaardinstelling: Azure **AD-gebruiker, -groep of -service-principal.** Selecteer de Azure AD-doelidentiteit op naam of e-mailadres.
5. Selecteer **Opslaan om** de roltoewijzingsbewerking te voltooien.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

In het volgende PowerShell-voorbeeld ziet u hoe u een Azure-rol toewijst aan een Azure AD-identiteit op basis van de aanmeldingsnaam. Zie Toegang beheren met [RBAC](../articles/role-based-access-control/role-assignments-powershell.md)en Azure PowerShell voor meer informatie over het toewijzen van Azure-rollen Azure PowerShell.

Voordat u het volgende voorbeeldscript gaat uitvoeren, moet u waarden van tijdelijke aanduidingen, inclusief haken, vervangen door uw eigen waarden.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
  
De volgende CLI 2.0-opdracht laat zien hoe u een Azure-rol toewijst aan een Azure AD-identiteit op basis van de aanmeldingsnaam. Zie Toegang beheren met RBAC en Azure CLI voor meer informatie over het toewijzen van [Azure-rollen met Azure CLI.](../articles/role-based-access-control/role-assignments-cli.md) 

Voordat u het volgende voorbeeldscript gaat uitvoeren, moet u waarden van tijdelijke aanduidingen, inclusief haken, vervangen door uw eigen waarden.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```
---

## <a name="configure-ntfs-permissions-over-smb"></a>NTFS-machtigingen configureren via SMB

Nadat u machtigingen op shareniveau met RBAC hebt toegewezen, moet u de juiste NTFS-machtigingen toewijzen op hoofd-, map- of bestandsniveau. U kunt machtigingen op shareniveau zien als de gatekeeper op hoog niveau die bepaalt of een gebruiker toegang heeft tot de share. Terwijl NTFS-machtigingen op een gedetailleerder niveau werken om te bepalen welke bewerkingen de gebruiker op map- of bestandsniveau kan uitvoeren.

Azure Files biedt ondersteuning voor de volledige set basis- en geavanceerde NTFS-machtigingen. U kunt NTFS-machtigingen voor mappen en bestanden in een Azure-bestands share weergeven en configureren door de share te mounten en vervolgens Windows Verkenner te gebruiken of de Windows [icacls-](/windows-server/administration/windows-commands/icacls) of [Set-ACL-opdracht](/powershell/module/microsoft.powershell.security/set-acl) uit te voeren. 

Als u NTFS wilt configureren met superuser-machtigingen, moet u de share met behulp van uw opslagaccountsleutel van uw domein-VM toevoegen. Volg de instructies in de volgende sectie om een Azure-bestands share te maken vanaf de opdrachtprompt en NTFS-machtigingen dienovereenkomstig te configureren.

De volgende sets machtigingen worden ondersteund in de hoofdmap van een bestands share:

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Geverifieerde gebruikers:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>Een bestands share vanaf de opdrachtprompt toevoegen

Gebruik de **Windows-opdracht net use** om de Azure-bestands share te installeren. Vergeet niet om de waarden van de tijdelijke aanduiding in het volgende voorbeeld te vervangen door uw eigen waarden. Zie Een Azure-bestands share gebruiken met Windows voor meer informatie over het toevoegen [van bestands shares.](../articles/storage/files/storage-how-to-use-files-windows.md) 

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

Als u problemen hebt met het maken van verbinding met Azure Files, raadpleegt u het hulpprogramma voor probleemoplossing dat we hebben gepubliceerd voor Azure Files koppelen [in Windows.](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/) We bieden ook [richtlijnen](../articles/storage/files/storage-files-faq.md#on-premises-access) voor scenario's waarin poort 445 is geblokkeerd. 


### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>NTFS-machtigingen configureren met Windows Verkenner

Gebruik Windows Verkenner om volledige machtigingen te verlenen aan alle mappen en bestanden onder de bestands share, met inbegrip van de hoofdmap.

1. Open Windows Verkenner, klik met de rechtermuisknop op het bestand/de map en selecteer **Eigenschappen.**
2. Selecteer het tabblad **Beveiliging**.
3. Selecteer **Bewerken.** om machtigingen te wijzigen.
4. U kunt de machtigingen van bestaande gebruikers wijzigen of **Toevoegen...** selecteren om machtigingen te verlenen aan nieuwe gebruikers.
5. Voer in het promptvenster voor het toevoegen van nieuwe gebruikers de naam in van de doelgebruiker aan wie u machtiging wilt verlenen in het vak Geef de **objectnamen** op die u wilt selecteren en selecteer Namen controleren om de volledige UPN-naam van de doelgebruiker te vinden. 
7.    Selecteer **OK**.
8.    Selecteer op **het** tabblad Beveiliging alle machtigingen die u aan uw nieuwe gebruiker wilt verlenen.
9.    Selecteer **Toepassen**.

### <a name="configure-ntfs-permissions-with-icacls"></a>NTFS-machtigingen configureren met icacls

Gebruik de volgende Windows-opdracht om volledige machtigingen te verlenen aan alle mappen en bestanden onder de bestands share, met inbegrip van de hoofdmap. Vergeet niet om de tijdelijke aanduidingen in het voorbeeld te vervangen door uw eigen waarden.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Zie de [opdrachtregelverwijzing voor icacls](/windows-server/administration/windows-commands/icacls)voor meer informatie over het gebruik van icacls om NTFS-machtigingen in te stellen en over de verschillende typen ondersteunde machtigingen.

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Een bestands share vanaf een VM die lid is van een domein

Met het volgende proces wordt gecontroleerd of uw bestands share en toegangsmachtigingen correct zijn ingesteld en of u toegang hebt tot een Azure-bestands share vanaf een domein-VM. Het kan even duren voor de Azure-roltoewijzing op shareniveau van kracht is. 

Meld u aan bij de VM met behulp van de Azure AD-identiteit waaraan u machtigingen hebt verleend, zoals wordt weergegeven in de volgende afbeelding. Als u on-premises verificatie voor AD DS hebt Azure Files, gebruikt u uw AD DS referenties. Voor Azure AD DS verificatie meldt u zich aan met Azure AD-referenties.

![Schermopname van het aanmeldingsscherm van Azure AD voor gebruikersverificatie](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

Gebruik de volgende opdracht om de Azure-bestands share te maken. Vergeet niet om de waarden van de tijdelijke aanduidingen te vervangen door uw eigen waarden. Omdat u bent geverifieerd, hoeft u de sleutel van het opslagaccount, de on-premises AD DS referenties of de Azure AD DS op te geven. Een aanmeldingservaring wordt ondersteund voor verificatie met on-premises AD DS of Azure AD DS. Als u problemen hebt met het maken van AD DS referenties, raadpleegt u [Troubleshoot Azure Files problems in Windows (Problemen](../articles/storage/files/storage-troubleshoot-windows-file-connection-problems.md) in Windows oplossen).

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
