---
title: Voer een upgrade uit van Basic Public naar Standard Public-Azure Load Balancer
description: In dit artikel wordt beschreven hoe u een upgrade uitvoert van de open bare Azure-Load Balancer van de Basic-SKU naar de standaard-SKU
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: 125d4a02d06e2792f9a2a4e646c3788dcf223318
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102612827"
---
# <a name="upgrade-azure-public-load-balancer"></a>Open bare Azure-Load Balancer bijwerken
[Azure Standard Load Balancer](load-balancer-overview.md) biedt een uitgebreide set functionaliteit en hoge Beschik baarheid via zone redundantie. Zie [vergelijkings tabel](./skus.md#skus)voor meer informatie over Load Balancer SKU.

Er zijn twee fasen in een upgrade:

1. Wijzig de IP-toewijzings methode van dynamisch in statisch.
2. Voer het Power shell-script uit om de upgrade en verkeer migratie te volt ooien.

> [!IMPORTANT]
> Het script is momenteel onderhouds werkzaamheden. U kunt [hier](../virtual-network/virtual-network-public-ip-address-upgrade.md) de instructies raadplegen voor het upgraden van open bare IP-adressen van de Basic-SKU en de standaard-SKU.

## <a name="upgrade-overview"></a>Overzicht van de upgrade

Er is een Azure PowerShell script beschikbaar dat het volgende doet:

* Hiermee maakt u een standaard-SKU Load Balancer met een locatie die u opgeeft in dezelfde resource groep van de basis Standard Load Balancer.
* Hiermee wordt het open bare IP-adres van de Basic-SKU bijgewerkt naar de standaard-SKU.
* Hiermee worden de configuraties van de basis-SKU Load Balancer naadloos gekopieerd naar de zojuist gemaakte Standard Load Balancer.
* Hiermee maakt u een standaard regel voor uitgaand verkeer waarmee uitgaande verbindingen mogelijk worden gemaakt.

### <a name="caveatslimitations"></a>Caveats\Limitations

* Script ondersteunt alleen Public Load Balancer upgrade. Raadpleeg [Deze pagina](./upgrade-basicinternal-standard.md) voor instructies voor interne basis Load Balancer upgrade.
* De toewijzings methode van het open bare IP-adres moet worden gewijzigd in statisch voordat het script wordt uitgevoerd. 
* Als uw Load Balancer geen frontend-IP-configuratie of back-end-pool heeft, zult u waarschijnlijk een fout bij het uitvoeren van het script aangaan. Zorg ervoor dat deze niet leeg zijn.

### <a name="change-allocation-method-of-the-public-ip-address-to-static"></a>Toewijzings methode van het open bare IP-adres wijzigen in statisch

* * * Hier volgen onze aanbevolen stappen:

    1. Als u de taken in deze quickstart wilt uitvoeren, moet u zich aanmelden bij de [Azure-portal](https://portal.azure.com).
 
    1. Selecteer **alle resources** in het menu links en selecteer vervolgens het **basis-open bare IP-adres dat is gekoppeld aan basis Load Balancer** uit de lijst met resources.
   
    1. Selecteer **configuraties** onder **instellingen**.
   
    1. Selecteer onder **toewijzing** de optie **statisch**.
    1. Selecteer **Opslaan**.
    >[!NOTE]
    >Voor Vm's met open bare Ip's moet u eerst standaard IP-adressen maken waarbij hetzelfde IP-adres niet gegarandeerd is. Koppel Vm's uit Basic Ip's en koppel deze aan de zojuist gemaakte standaard IP-adressen. Daarna kunt u de instructies volgen om Vm's toe te voegen aan de back-end-groep van Standard Load Balancer. 

* **Nieuwe Vm's maken om toe te voegen aan de back-endservers van de zojuist gemaakte standaard open bare Load Balancer**.
    * Meer instructies voor het maken van een VM en het koppelen hiervan aan Standard Load Balancer vindt u [hier](./quickstart-load-balancer-standard-public-portal.md#create-virtual-machines).


## <a name="download-the-script"></a>Het script downloaden

Down load het migratie script van de  [PowerShell Gallery](https://www.powershellgallery.com/packages/AzurePublicLBUpgrade/4.0).
## <a name="use-the-script"></a>Het script gebruiken

Er zijn twee opties voor u afhankelijk van de instellingen en voor keuren van uw lokale Power shell-omgeving:

* Als u de Azure AZ-modules niet hebt geïnstalleerd of als u niet van mening bent dat u de Azure AZ-modules verwijdert, kunt u het beste de `Install-Script` optie gebruiken om het script uit te voeren.
* Als u de Azure AZ-modules wilt blijven gebruiken, is het verstandig om het script te downloaden en het rechtstreeks uit te voeren.

Als u wilt weten of u de Azure AZ-modules hebt geïnstalleerd, voert u uit `Get-InstalledModule -Name az` . Als er geen geïnstalleerde AZ-modules worden weer geven, kunt u de- `Install-Script` methode gebruiken.

### <a name="install-using-the-install-script-method"></a>Installeren met behulp van de Install-Script methode

Als u deze optie wilt gebruiken, moet u de Azure AZ-modules niet op uw computer installeren. Als ze zijn geïnstalleerd, wordt een fout weer gegeven in de volgende opdracht. U kunt de Azure AZ-modules verwijderen of de andere optie gebruiken om het script hand matig te downloaden en uit te voeren.
  
Voer het script uit met de volgende opdracht:

`Install-Script -Name AzurePublicLBUpgrade`

Met deze opdracht worden ook de vereiste AZ-modules geïnstalleerd.  

### <a name="install-using-the-script-directly"></a>Rechtstreeks installeren met behulp van het script

Als er sommige Azure AZ-modules zijn geïnstalleerd en deze niet kunnen verwijderen (of niet wilt verwijderen), kunt u het script hand matig downloaden met het tabblad **hand matig downloaden** in de koppeling voor het downloaden van het script. Het script wordt gedownload als een onbewerkt nupkg-bestand. Zie [hand matig downloaden van pakketten](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)als u het script wilt installeren vanuit dit nupkg-bestand.

Het script uitvoeren:

1. Gebruiken `Connect-AzAccount` om verbinding te maken met Azure.

1. Gebruiken `Import-Module Az` voor het importeren van de AZ-modules.

1. Controleer de vereiste para meters:

   * **oldRgName: [teken reeks]: vereist** – dit is de resource groep voor de bestaande basis Load Balancer u een upgrade wilt uitvoeren. Als u deze teken reeks waarde wilt vinden, gaat u naar Azure Portal, selecteert u de basis Load Balancer bron en klikt u op het **overzicht** van de Load Balancer. De resource groep bevindt zich op deze pagina.
   * **oldLBName: [teken reeks]: vereist** – dit is de naam van de bestaande Basic-Balancer die u wilt bijwerken. 
   * **newLBName: [string]: vereist** : dit is de naam voor de Standard Load Balancer die u wilt maken.
1. Voer het script uit met de juiste para meters. Het kan vijf tot zeven minuten duren voordat de bewerking is voltooid.

    **Voorbeeld**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg" -oldLBName "LBForPublic" -newLbName "LBForUpgrade"
   ```

### <a name="create-an-outbound-rule-for-outbound-connection"></a>Een uitgaande regel voor de uitgaande verbinding maken

Volg de [instructies](./quickstart-load-balancer-standard-public-powershell.md#create-outbound-rule-configuration) voor het maken van een uitgaande regel, zodat u kunt
* Geef de uitgaande NAT van een geheel nieuwe definitie op.
* Het gedrag van bestaande uitgaande NAT schalen en afstemmen.

## <a name="common-questions"></a>Veelgestelde vragen

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Zijn er beperkingen met het Azure PowerShell script voor het migreren van de configuratie van v1 naar v2?

Ja. Zie [aanvullende informatie/beperkingen](#caveatslimitations).

### <a name="how-long-does-the-upgrade-take"></a>Hoe lang duurt de upgrade?

Het duurt meestal ongeveer 5-10 minuten voordat het script is voltooid en het kan langer duren, afhankelijk van de complexiteit van uw Load Balancer configuratie. Houd daarom rekening met de downtime en plan de failover, indien nodig.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Schakelt het Azure PowerShell script ook over op het verkeer van mijn basis Load Balancer naar de zojuist gemaakte Standard Load Balancer?

Ja. Het Azure PowerShell script voert niet alleen een upgrade uit van het open bare IP-adres, kopieert de configuratie van Basic naar Standard Load Balancer, maar migreert ook de virtuele machine naar achter de zojuist gemaakte standaard open bare Load Balancer. 

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over Standard Load Balancer](load-balancer-overview.md)