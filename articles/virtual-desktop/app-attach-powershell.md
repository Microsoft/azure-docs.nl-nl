---
title: Windows Virtual Desktop MSIX-app koppelen PowerShell - Azure
description: MsIX-app-attach instellen voor Windows Virtual Desktop behulp van PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: f44cbf3764063c511c896f11bb7ebfaae2973f0c
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365396"
---
# <a name="set-up-msix-app-attach-using-powershell"></a>MSIX-app-attach instellen met behulp van PowerShell

Naast de Azure Portal kunt u de MSIX-app ook handmatig koppelen met PowerShell. In dit artikel wordt beschreven hoe u PowerShell gebruikt om een MSIX-app-attach in te stellen.

## <a name="requirements"></a>Vereisten

>[!IMPORTANT]
>Voordat u aan de slag gaat, [](https://aka.ms/enablemsixappattach) moet u dit formulier invullen en verzenden om MSIX-app-koppelen in te kunnenschakelen in uw abonnement. Als u geen goedgekeurde aanvraag hebt, werkt het koppelen van de MSIX-app niet. Het goedkeuren van aanvragen kan tijdens werkdagen maximaal 24 uur duren. U ontvangt een e-mail wanneer uw aanvraag is geaccepteerd en voltooid.

Dit is wat u nodig hebt om een MSIX-app-attach te configureren:

- Een werkende Windows Virtual Desktop implementatie. Zie Een tenant maken in Windows Virtual Desktop voor meer informatie over het implementeren [Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md). Zie Een hostgroep maken met de Windows Virtual Desktop voor meer informatie over het implementeren van Azure Resource Manager met [Azure Portal.](./create-host-pools-azure-marketplace.md)
- Een Windows Virtual Desktop hostgroep met ten minste één actieve sessiehost.
- Deze hostgroep moet zich in de validatieomgeving.
- Een externe bureaublad-app-groep.
- Het MSIX-pakkethulpprogramma.
- Een toepassing met MSIX-pakket uitgebreid naar een MSIX-afbeelding die wordt geüpload naar een bestands share.
- Een bestands share in uw Windows Virtual Desktop implementatie waar het MSIX-pakket wordt opgeslagen.
- De bestands share waar u de MSIX-afbeelding hebt geüpload, moet ook toegankelijk zijn voor alle virtuele machines (VM's) in de hostgroep. Gebruikers hebben alleen-lezenmachtigingen nodig voor toegang tot de afbeelding.
- Download en installeer PowerShell Core.
- Download de openbare preview Azure PowerShell module en vouw deze uit naar een lokale map.
- Installeer de Azure-module door de volgende cmdlet uit te uitvoeren:

    ```powershell
    Install-Module -Name Az -Force
    ```

## <a name="sign-in-to-azure-and-import-the-module"></a>Meld u aan bij Azure en importeer de module

Zodra u alle vereisten gereed hebt, opent u PowerShell Core in een opdrachtprompt met verhoogde bevoegdheid en voer u deze cmdlet uit:

```powershell
Connect-AzAccount
```

Nadat u het hebt uitgevoerd, verifieert u uw account met uw referenties. In dit geval wordt u mogelijk gevraagd om een apparaat-URL of een token.

## <a name="import-the-azwindowsvirtualdesktop-module"></a>De Az.WindowsVirtualDesktop-module importeren

U hebt de module Az.DesktopVirtualization nodig om de instructies in dit artikel te volgen.

>[!NOTE]
>Voor de openbare preview bieden we de module als afzonderlijke ZIP-bestanden die u handmatig moet importeren.

Voordat u begint, kunt u de volgende cmdlet uitvoeren om te zien of de module Az.DesktopVirtualization al is geïnstalleerd op uw sessie of VM:

```powershell
Get-Module | Where-Object { $_.Name -Like "desktopvirtualization" }
```

Als u een bestaande kopie van de module wilt verwijderen en opnieuw wilt beginnen, moet u deze cmdlet uitvoeren:

```powershell
Uninstall-Module Az.DesktopVirtualization
```

Als de module is geblokkeerd op uw VM, moet u deze cmdlet uitvoeren om de blokkering op te opheffen:

```powershell
Unblock-File "<path>\Az.DesktopVirtualization.psm1"
```

Nu het opschonen is uit de weg, is het tijd om de module te importeren.

1. Voer de volgende cmdlet uit en druk op **R** wanneer u wordt gevraagd om akkoord te gaan met het uitvoeren van de aangepaste code.

   ```powershell
   Import-Module -Name "<path>\Az.DesktopVirtualization.psm1" -Verbose
   ```

2. Nadat u de import-cmdlet hebt uitgevoerd, controleert u of deze de cmdlets voor MSIX heeft door de volgende cmdlet uit te voeren:

   ```powershell
   Get-Command -Module Az.DesktopVirtualization | Where-Object { $_.Name -match "MSIX" }
   ```

   Als de cmdlets er zijn, moet de uitvoer er als volgende uitzien:

   ```powershell
   CommandType     Name                                               Version    Source

   -----------     ----                                               -------    ------

   Function        Expand-AzWvdMsixImage                              0.0        Az.DesktopVirtualization

   Function        Get-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        New-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        Remove-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization

   Function        Update-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization
   ```

   Als u deze uitvoer niet ziet, sluit u alle PowerShell en PowerShell Core en probeert u het opnieuw.

## <a name="set-up-helper-variables"></a>Helpervariabelen instellen

Nadat u de module hebt geïmporteerd, moet u de helpervariabelen instellen. In de volgende voorbeelden ziet u hoe u elk voorbeeld kunt doen.

Ga als volgende te werk om uw abonnements-id op te halen:

```powershell
Get-AzContext -ListAvailable | fl
```

De context van een Azure-tenant en -abonnement met een naam selecteren:

```powershell
$obj = Select-AzContext -Name "<Name>"
```

De abonnementsvariabele instellen:

```powershell
$subId = $obj.Subscription.Id
```

De naam van de werkruimte instellen:

```powershell
$ws = "<WorksSpaceName>"
```

De naam van de hostgroep instellen:

```powershell
$hp = "<HostPoolName>"
```

De resourcegroep instellen waarin de sessiehost-VM's zijn geconfigureerd:

```powershell
$rg = "<ResourceGroupName>"
```

En ten slotte, om te bevestigen dat u alle variabelen correct hebt ingesteld:

```powershell
Get-AzWvdWorkspace -Name $ws -ResourceGroupName $rg -SubscriptionId $subID
```

## <a name="add-an-msix-package-to-a-host-pool"></a>Een MSIX-pakket toevoegen aan een hostgroep

Zodra u alles hebt ingesteld, is het tijd om het MSIX-pakket toe te voegen aan een hostgroep. Als u dit wilt doen, moet u eerst het UNC-pad naar de MSIX-afbeelding krijgen.

Voer met behulp van het UNC-pad deze cmdlet uit om de MSIX-afbeelding uit te vouwen:

```powershell
$obj = Expand-AzWvdMsixImage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subID -Uri <UNCPath>
```

Voer deze cmdlet uit om het MSIX-pakket toe te voegen aan de gewenste hostgroep:

```powershell
New-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId -PackageAlias $obj.PackageAlias -DisplayName <DisplayName> -ImagePath <UNCPath> -IsActive:$true
```

Als u klaar bent, controleert u of het pakket is gemaakt met deze cmdlet:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object {$_.PackageFamilyName -eq $obj.PackageFamilyName}
```

## <a name="remove-an-msix-package-from-a-host-pool"></a>Een MSIX-pakket uit een hostgroep verwijderen

Een pakket uit een hostgroep verwijderen:

Haal een lijst op met alle pakketten die zijn gekoppeld aan een hostgroep met deze cmdlet en zoek vervolgens de naam van het pakket dat u wilt verwijderen in de uitvoer:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId 
```

U kunt ook een bepaald pakket krijgen op basis van de weergavenaam met deze cmdlet:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object { $_.Name -like "Power" }
```

Voer deze cmdlet uit om het pakket te verwijderen:

```powershell
Remove-AzWvdMsixPackage -FullName $obj.PackageFullName -HostPoolName $hp -ResourceGroupName $rg
```

## <a name="publish-msix-apps-to-an-app-group"></a>MSIX-apps publiceren naar een app-groep

U kunt de instructies in deze sectie alleen volgen als u klaar bent met het volgen van de instructies in de vorige secties. Als u een hostgroep hebt met een actieve sessiehost, ten minste één bureaublad-app-groep en een MSIX-pakket aan de hostgroep hebt toegevoegd, bent u klaar om te gaan.

Als u een app vanuit het MSIX-pakket wilt publiceren naar een app-groep, moet u de naam ervan vinden en die naam vervolgens gebruiken in de publicatie-cmdlet.

Een app publiceren:

Voer deze cmdlet uit om alle beschikbare app-groepen weer te maken:

```powershell
Get-AzWvdApplicationGroup -ResourceGroupName $rg -SubscriptionId $subId
```

Wanneer u de naam hebt gevonden van de app-groep waarin u apps wilt publiceren, gebruikt u de naam ervan in deze cmdlet:

```powershell
$grName = "<AppGroupName>"
```

Ten slotte moet u de app publiceren.

- Als u de MSIX-toepassing wilt publiceren naar een bureaublad-app-groep, moet u deze cmdlet uitvoeren:

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0
   ```

- Als u de app wilt publiceren naar een externe app-groep, moet u in plaats daarvan deze cmdlet uitvoeren:

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0 -MsixPackageApplicationId $obj.PackageApplication.AppId
   ```

>[!NOTE]
>Als een gebruiker is toegewezen aan zowel een externe app-groep als een bureaublad-app-groep in dezelfde hostgroep, zien gebruikers msix-apps van beide groepen wanneer ze verbinding maken met hun externe bureaublad.

## <a name="next-steps"></a>Volgende stappen

Stel onze communityvragen over deze functie op de [Windows Virtual Desktop TechCommunity.](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)

U kunt ook feedback geven voor Windows Virtual Desktop in Windows Virtual Desktop [feedbackhub.](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)

Hier zijn enkele andere artikelen die mogelijk nuttig zijn:

- [Verklarende woordenlijst voor het koppelen van MSIX-apps](app-attach-glossary.md)
- [Veelgestelde vragen over het koppelen van MSIX-apps](app-attach-faq.md)
