---
title: Virtuele Windows-bureau blad-taak verdeling configureren-Azure
description: De taakverdelings methode configureren voor een virtuele Windows-desktop omgeving.
author: Heidilohr
ms.topic: how-to
ms.date: 10/12/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2c57ac10fbd318dd4bbb2dc86457e186dd824834
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91951652"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>De taakverdelingsmethode voor Windows Virtual Desktop configureren

Als u de taakverdelings methode voor een hostgroep configureert, kunt u de virtuele Windows-bureaublad omgeving aanpassen aan uw behoeften.

>[!NOTE]
> Dit is niet van toepassing op een permanente bureau blad-hostgroep, omdat gebruikers altijd een 1:1-koppeling hebben met een sessie-host binnen de hostgroep.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u de instructies in [set up the Windows Virtual Desktop Power shell-module](powershell-module.md) hebt gevolgd om de Power shell-module te downloaden en te installeren en u aan te melden bij uw Azure-account.

## <a name="configure-breadth-first-load-balancing"></a>Gelijkmatige taak verdeling configureren

Breedte-eerste taak verdeling is de standaard configuratie voor nieuwe niet-permanente hostgroepen. Breed-First Load Balancing distribueert nieuwe gebruikers sessies over alle beschik bare sessie-hosts in de hostgroep. Bij het configureren van gelijkmatige taak verdeling kunt u een maximum aantal sessies per sessiehost instellen in de hostgroep.

Voer de volgende Power shell-cmdlet uit om een hostgroep zodanig te configureren dat de taak verdeling breed is zonder de maximale sessie limiet aan te passen:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst'
```

Daarna voert u de volgende cmdlet uit om ervoor te zorgen dat u de taak verdelings methode breed hebt ingesteld:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType

Name             : hostpoolname
LoadBalancerType : BreadthFirst
```

Voer de volgende Power shell-cmdlet uit om een hostgroep zodanig te configureren dat de taak verdeling breed is en een nieuwe maximale sessie limiet te gebruiken:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst' -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Diepte configureren-eerste taak verdeling

Diepte: voor de taak verdeling worden nieuwe gebruikers sessies gedistribueerd naar een beschik bare sessiehost met het hoogste aantal verbindingen, maar de drempel waarde voor de maximale sessie limiet is niet bereikt.

>[!IMPORTANT]
>Bij het configureren van diepte-eerste taak verdeling moet u een maximum aantal sessies per sessiehost instellen in de hostgroep.

Voer de volgende Power shell-cmdlet uit om een hostgroep te configureren voor het uitvoeren van diepte-eerste taak verdeling:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'DepthFirst' -MaxSessionLimit ###
```

>[!NOTE]
> Met het algoritme voor het gelijkmatige taak verdeling worden sessies gedistribueerd naar sessie-hosts op basis van de maximum limiet voor de sessie-host ( `-MaxSessionLimit` ). De standaard waarde van deze para meter is `999999` . Dit is ook het hoogste mogelijke getal waarmee u deze variabele kunt instellen. Deze para meter is vereist wanneer u het algoritme voor de taak verdeling van de diepte eerste gebruikt. Voor de best mogelijke gebruikers ervaring moet u de maximum limiet voor de sessie-hostparameters wijzigen in een getal dat het beste bij uw omgeving past.

Voer de volgende cmdlet uit om ervoor te zorgen dat de instelling is bijgewerkt:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType, MaxSessionLimit

Name             : hostpoolname
LoadBalancerType : DepthFirst
MaxSessionLimit  : 6
```

## <a name="configure-load-balancing-with-the-azure-portal"></a>Taak verdeling configureren met de Azure Portal

U kunt ook taak verdeling configureren met de Azure Portal.

Taak verdeling configureren:

1. Meld u aan bij de Azure Portal op https://portal.azure.com .
2. Zoek en selecteer **Windows Virtual Desktop** onder Services.
3. Selecteer op de Windows Virtual Desktop-pagina de optie **Hostpools**.
4. Selecteer de naam van de hostpool die u wilt bewerken.
5. Selecteer **Eigenschappen**.
6. Voer de **maximale sessie limiet** in het veld in en selecteer de gewenste **taakverdelings algoritme** voor deze hostgroep in de vervolg keuzelijst.
7. Selecteer **Opslaan**. Hiermee worden de nieuwe instellingen voor taak verdeling toegepast.