---
title: Windows-MSIX-app voor het koppelen van preview Power shell-Azure
description: MSIX-app-koppeling voor Windows Virtual Desktop instellen met behulp van Power shell.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8aa6a2168bff6e90d636770804900fa93f081ced
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97425877"
---
# <a name="set-up-msix-app-attach-preview-using-powershell"></a>MSIX-app-koppeling instellen (preview) met behulp van Power shell

> [!IMPORTANT]
> MSIX app attach is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Naast de Azure Portal, kunt u ook hand matig MSIX-app-koppeling (preview) instellen met Power shell. In dit artikel wordt uitgelegd hoe u Power shell kunt gebruiken om MSIX-app attach in te stellen.

## <a name="requirements"></a>Vereisten

>[!IMPORTANT]
>Voordat u aan de slag gaat, moet u ervoor zorgen dat [dit formulier](https://aka.ms/enablemsixappattach) wordt ingevuld en verzonden om de MSIX-app-koppeling in uw abonnement in te scha kelen. Als u geen goedgekeurde aanvraag hebt, werkt MSIX app attach niet. De goed keuring van aanvragen kan tot 24 uur duren tijdens kantoor dagen. U ontvangt een e-mail wanneer uw aanvraag is geaccepteerd en voltooid.

Dit is wat u nodig hebt om de MSIX-app-koppeling te configureren:

- Een werkende implementatie van virtueel bureau blad in Windows. Zie [een Tenant maken in Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)voor meer informatie over het implementeren van Windows virtueel bureau blad (klassiek). Zie [een hostgroep maken met de Azure Portal](./create-host-pools-azure-marketplace.md)voor meer informatie over het implementeren van Windows virtueel bureau blad met Azure Resource Manager-integratie.
- Een Windows-host voor virtuele Bureau bladen met ten minste één actieve sessiehost.
- Een extern bureau blad-app-groep.
- Het MSIX-verpakkings programma.
- Een MSIX-toepassing die is uitgepakt in een MSIX-installatie kopie die is geüpload naar een bestands share.
- Een bestands share in uw Windows-implementatie voor virtueel bureau blad waar het MSIX-pakket wordt opgeslagen.
- De bestands share waar u de MSIX-installatie kopie hebt geüpload, moet ook toegankelijk zijn voor alle virtuele machines (Vm's) in de hostgroep. Gebruikers hebben alleen-lezen-machtigingen nodig voor toegang tot de installatie kopie.
- Down load en installeer Power shell core.
- Down load de module open bare preview Azure PowerShell en breid deze uit naar een lokale map.
- Installeer de Azure-module door de volgende cmdlet uit te voeren:

    ```powershell
    Install-Module -Name Az -Force
    ```

## <a name="sign-in-to-azure-and-import-the-module"></a>Meld u aan bij Azure en importeer de module

Zodra u klaar bent met de vereisten, opent u Power shell core in een opdracht prompt met verhoogde bevoegdheid en voert u deze cmdlet uit:

```powershell
Connect-AzAccount
```

Nadat u het hebt uitgevoerd, verifieert u uw account met behulp van uw referenties. In dit geval wordt u mogelijk gevraagd om een apparaat-URL of een token.

## <a name="import-the-azwindowsvirtualdesktop-module"></a>De module AZ. WindowsVirtualDesktop importeren

U hebt de module AZ. DesktopVirtualization nodig om de instructies in dit artikel te volgen.

>[!NOTE]
>Voor de open bare preview geven we de module op als afzonderlijke ZIP-bestanden die hand matig moeten worden geïmporteerd.

Voordat u begint, kunt u de volgende cmdlet uitvoeren om te zien of de module AZ. DesktopVirtualization al is geïnstalleerd in uw sessie of virtuele machine:

```powershell
Get-Module | Where-Object { $_.Name -Like "desktopvirtualization" }
```

Als u een bestaand exemplaar van de module wilt verwijderen en opnieuw wilt starten, voert u de volgende cmdlet uit:

```powershell
Uninstall-Module Az.DesktopVirtualization
```

Als de module is geblokkeerd op uw virtuele machine, voert u deze cmdlet uit om deze uit te blok keren:

```powershell
Unblock-File "<path>\Az.DesktopVirtualization.psm1"
```

Met deze opschoning uit de weg is het tijd om de module te importeren.

1. Voer de volgende cmdlet uit en druk op de **R** -toets wanneer u wordt gevraagd om de aangepaste code uit te voeren.

   ```powershell
   Import-Module -Name "<path>\Az.DesktopVirtualization.psm1" -Verbose
   ```

2. Nadat u de cmdlet Import hebt uitgevoerd, controleert u of deze de cmdlets voor MSIX bevat door de volgende cmdlet uit te voeren:

   ```powershell
   Get-Command -Module Az.DesktopVirtualization | Where-Object { $_.Name -match "MSIX" }
   ```

   Als de cmdlets aanwezig zijn, ziet de uitvoer er als volgt uit:

   ```powershell
   CommandType     Name                                               Version    Source

   -----------     ----                                               -------    ------

   Function        Expand-AzWvdMsixImage                              0.0        Az.DesktopVirtualization

   Function        Get-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        New-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        Remove-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization

   Function        Update-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization
   ```

   Als deze uitvoer niet wordt weer gegeven, sluit u alle Power shell-en Power shell-kern sessies en probeert u het opnieuw.

## <a name="set-up-helper-variables"></a>Hulp variabelen instellen

Wanneer u de module hebt geïmporteerd, moet u de hulp variabelen instellen. In de volgende voor beelden ziet u hoe u deze kunt doen.

Uw abonnements-ID ophalen:

```powershell
Get-AzContext -ListAvailable | fl
```

De context van een Azure-Tenant en-abonnement met een naam selecteren:

```powershell
$obj = Select-AzContext -Name "<Name>"
```

De abonnements variabele instellen:

```powershell
$subId = $obj.Subscription.Id
```

De naam van de werk ruimte instellen:

```powershell
$ws = "<WorksSpaceName>"
```

De naam van de hostgroep instellen:

```powershell
$hp = "<HostPoolName>"
```

De resource groep instellen waar de host-Vm's voor de sessiehost worden geconfigureerd:

```powershell
$rg = "<ResourceGroupName>"
```

En ten slotte, om te bevestigen dat u alle variabelen juist hebt ingesteld:

```powershell
Get-AzWvdWorkspace -Name $ws -ResourceGroupName $rg -SubscriptionId $subID
```

## <a name="add-an-msix-package-to-a-host-pool"></a>Een MSIX-pakket toevoegen aan een hostgroep

Zodra u alles hebt ingesteld, is het tijd om het MSIX-pakket toe te voegen aan een hostgroep. Hiervoor moet u eerst het UNC-pad naar de MSIX-installatie kopie ophalen.

Voer met behulp van het UNC-pad deze cmdlet uit om de MSIX-installatie kopie uit te vouwen:

```powershell
$obj = Expand-AzWvdMsixImage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subID -Uri <UNCPath>
```

Voer deze cmdlet uit om het MSIX-pakket toe te voegen aan de gewenste hostgroep:

```powershell
New-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId -PackageAlias $obj.PackageAlias -DisplayName <DisplayName> -ImagePath <UNCPath> -IsActive:$true
```

Wanneer u klaar bent, controleert u of het pakket is gemaakt met deze cmdlet:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object {$_.PackageFamilyName -eq $obj.PackageFamilyName}
```

## <a name="remove-an-msix-package-from-a-host-pool"></a>Een MSIX-pakket verwijderen uit een hostgroep

Een pakket verwijderen uit een hostgroep:

Haal een lijst op met alle pakketten die aan een hostgroep zijn gekoppeld met deze cmdlet en zoek vervolgens de naam van het pakket dat u wilt verwijderen in de uitvoer:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId 
```

U kunt ook een bepaald pakket ophalen op basis van de weergave naam met deze cmdlet:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object { $_.Name -like "Power" }
```

Als u het pakket wilt verwijderen, voert u deze cmdlet uit:

```powershell
Remove-AzWvdMsixPackage -FullName $obj.PackageFullName -HostPoolName $hp -ResourceGroupName $rg
```

## <a name="publish-msix-apps-to-an-app-group"></a>MSIX-apps publiceren naar een app-groep

U kunt de instructies in deze sectie alleen volgen als u klaar bent met het volgen van de instructies in de vorige secties. Als u een hostgroep hebt met een actieve sessiehost, ten minste één bureau blad-app-groep en u een MSIX-pakket aan de hostgroep hebt toegevoegd, bent u klaar om te gaan.

Als u een app uit het MSIX-pakket naar een app-groep wilt publiceren, moet u de naam ervan vinden en vervolgens die naam gebruiken in de cmdlet voor publiceren.

Een app publiceren:

Voer deze cmdlet uit om alle beschik bare app-groepen weer te geven:

```powershell
Get-AzWvdApplicationGroup -ResourceGroupName $rg -SubscriptionId $subId
```

Wanneer u de naam van de app-groep die u wilt publiceren apps hebt gevonden, gebruikt u de naam in deze cmdlet:

```powershell
$grName = "<AppGroupName>"
```

Ten slotte moet u de app publiceren.

- Als u een MSIX-toepassing wilt publiceren in een desktop-app-groep, voert u deze cmdlet uit:

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0
   ```

- Als u de app wilt publiceren naar een externe app-groep, voert u de volgende cmdlet uit:

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0 -MsixPackageApplicationId $obj.PackageApplication.AppId
   ```

>[!NOTE]
>Als een gebruiker wordt toegewezen aan zowel een externe app-groep als een bureau blad-app in dezelfde hostgroep en de gebruiker verbinding maakt met hun externe bureau blad, worden er MSIX apps uit beide groepen weer geven.

## <a name="next-steps"></a>Volgende stappen

Vraag onze vragen van de community over deze functie op het [virtuele bureau blad-TechCommunity van Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

U kunt ook feedback geven voor Windows virtueel bureau blad op de [Windows Virtual Desktop feedback hub](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

Hier volgen enkele andere artikelen die u mogelijk handig vindt:

- [Woorden lijst voor het toevoegen van MSIX-apps](app-attach-glossary.md)
- [Veelgestelde vragen over het koppelen van MSIX-apps](app-attach-faq.md)