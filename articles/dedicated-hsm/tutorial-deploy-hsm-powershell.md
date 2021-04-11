---
title: 'Zelfstudie: PowerShell gebruiken voor implementatie in een bestaand virtueel netwerk - Azure Toegewezen HSM | Microsoft Docs'
description: Zelfstudie voor het implementeren van een toegewezen HSM in een bestaand virtueel netwerk met behulp van PowerShell
services: dedicated-hsm
documentationcenter: na
author: msmbaldwin
manager: rkarlin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18, devx-track-azurepowershell
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 5ed5ac90f446f74c54488f6d0cf23adbd63a3e1e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606875"
---
# <a name="tutorial--deploying-hsms-into-an-existing-virtual-network-using-powershell"></a>Zelfstudie: PowerShell gebruiken om HSM's te implementeren in een bestaand virtueel netwerk

De service Azure Toegewezen HSM biedt een fysiek apparaat voor gebruik door één klant, met volledige administratieve controle en verantwoordelijkheid voor volledig beheer. Omdat fysieke hardware wordt verstrekt, moet Microsoft regelen hoe deze apparaten worden toegewezen om ervoor te zorgen dat capaciteit effectief wordt beheerd. Dit betekent dat de Toegewezen HSM-service in een Azure-abonnement niet gewoon zichtbaar is voor de inrichting van resources. Elke Azure-klant die toegang nodig heeft tot de Toegewezen HSM-service, moet eerst contact opnemen met de beheerder van zijn Microsoft-account om registratie aan te vragen voor de Toegewezen HSM-service. Pas wanneer dit proces is voltooid, is inrichting mogelijk.
In deze zelfstudie wordt een typisch inrichtingsproces getoond, waarin:

- Een klant al een virtueel netwerk heeft
- De klant een virtuele machine heeft
- De klant HSM-resources moet toevoegen aan de bestaande omgeving.

Een typische implementatiearchitectuur met hoge beschikbaarheid in meerdere regio's kan er als volgt uitzien:

![implementatie in meerdere regio's](media/tutorial-deploy-hsm-powershell/high-availability.png)

Deze zelfstudie richt zich op twee HSM's en de vereiste ExpressRoute-gateway (zie Subnet 1 hierboven), die worden geïntegreerd in een bestaand virtueel netwerk (zie VNET 1 hierboven).  Alle andere resources zijn standaard Azure-resources. Hetzelfde integratieproces kan worden gebruikt voor HSM's in subnet 4 op VNET 3 hierboven.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Azure Toegewezen HSM is momenteel niet beschikbaar in de Azure-portal, dus alle interactie met de service loopt via de opdrachtregel of PowerShell. In deze zelfstudie wordt PowerShell in de Azure Cloud Shell gebruikt. Als u niet bekend bent met PowerShell, volg dan de instructies in: [Aan de slag met Azure PowerShell](/powershell/azure/get-started-azureps).

Veronderstellingen:

- U hebt het registratieproces voor Azure Toegewezen HSM uitgevoerd en u mag de service gebruiken. Als dat niet het geval is, neemt u contact op met de vertegenwoordiger van uw Microsoft-account voor meer informatie. 
- U hebt een resourcegroep voor deze resources gemaakt en de nieuwe recources die u in deze zelfstudie hebt geïmplementeerd, gaan deel uitmaken van die groep.
- U hebt het benodigde virtuele netwerk, het subnet en de virtuele machines al gemaakt aan de hand van het diagram hierboven en nu wilt u 2 HSM's integreren in deze implementatie.

In alle onderstaande instructies wordt ervan uitgegaan dat u al naar de Azure-portal bent gegaan en Cloud Shell hebt geopend (selecteer \>\_ ergens in de rechterbovenhoek van de portal).

## <a name="provisioning-a-dedicated-hsm"></a>Een toegewezen HSM inrichten

De inrichting van de HSM's en integratie ervan in een bestaand virtueel netwerk via ExpressRoute-gateway wordt gevalideerd met behulp van het ssh-opdrachtregelprogramma, om bereikbaarheid en algemene beschikbaarheid van het HSM-apparaat voor eventuele andere configuratieactiviteiten te garanderen. Voor de volgende opdrachten wordt een Resource Manager-sjabloon gebruikt om de HSM-resources en bijbehorende netwerkresources te maken.

### <a name="validating-feature-registration"></a>Functieregistratie valideren

Zoals hierboven is uitgelegd, vereist elke inrichtingsactiviteit dat de Toegewezen HSM-service voor uw abonnement wordt geregistreerd. Om deze te valideren, voert u de volgende PowerShell-opdracht uit in de cloudshell van de Azure-portal. 

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.HardwareSecurityModules -FeatureName AzureDedicatedHsm
```

De opdracht moet de status 'Geregistreerd' retourneren (zoals hieronder wordt weergegeven) voordat u verdergaat.  Als u niet voor deze service bent geregistreerd, neem dan contact op met de vertegenwoordiger van uw Microsoft-account.

![abonnementsstatus](media/tutorial-deploy-hsm-powershell/subscription-status.png)

### <a name="creating-hsm-resources"></a>HSM-resources maken

Een HSM-apparaat is ingericht in het virtueel netwerk van een klant. Dit impliceert de vereiste voor een subnet. Een afhankelijkheid voor de HSM om communicatie tussen het virtuele netwerk en het fysieke apparaat mogelijk te maken, is een ExpressRoute-gateway en tot slot is een virtuele machine vereist voor toegang tot het HSM-apparaat via de Thales-client software. Deze resources zijn voor het gebruiksgemak verzameld in een sjabloonbestand met bijbehorend parameterbestand. Neem rechtstreeks via HSMrequest@Microsoft.com contact op met Microsoft voor de bestanden.

Wanneer u de bestanden hebt, moet u het parameterbestand bewerken om uw voorkeursnamen voor resources in te voegen. Dit betekent dat u de regels met 'waarde' moet bewerken.

- `namingInfix` Voorvoegsel voor namen van HSM-resources
- `ExistingVirtualNetworkName` Naam van het virtuele netwerk dat voor de HSM's wordt gebruikt
- `DedicatedHsmResourceName1` Naam van de HSM-resource in datacenter stempel 1
- `DedicatedHsmResourceName2` Naam van de HSM-resource in datacenter stempel 2
- `hsmSubnetRange` IP-adresbereik van subnet voor HSM's
- `ERSubnetRange` IP-adresbereik van subnet voor VNET-gateway

Hier volgt een voorbeeld van deze wijzigingen:

```json
{
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "namingInfix": {
      "value": "MyHSM"
    },
    "ExistingVirtualNetworkName": {
      "value": "MyHSM-vnet"
    },
    "DedicatedHsmResourceName1": {
      "value": "HSM1"
    },
    "DedicatedHsmResourceName2": {
      "value": "HSM2"
    },
    "hsmSubnetRange": {
      "value": "10.0.2.0/24"
    },
    "ERSubnetRange": {
      "value": "10.0.255.0/26"
    },
  }
}
```

Met het gekoppelde Resource Manager-sjabloonbestand worden 6 resources gemaakt met deze informatie:

- Een subnet voor de HSM's in het opgegeven VNET
- Een subnet voor de gateway van het virtuele netwerk 
- Een virtuele-netwerkgateway die het VNET verbindt met de HSM-apparaten
- Een openbaar IP-adres voor de gateway
- Een HSM in stempel 1
- Een HSM in stempel 2

Wanneer de parameterwaarden zijn ingesteld, moeten de bestanden worden geüpload naar de bestandsshare van de cloudshell in de Azure-portal voor gebruik. Klik rechtsboven in de Azure-portal op het symbool van de cloudshell '\>\_' om het onderste gedeelte van het scherm te wijzigen in een opdrachtomgeving. De opties hiervoor zijn BASH en PowerShell, en u moet BASH selecteren als dat nog niet is gebeurd.

De opdrachtshell heeft een optie Uploaden/Downloaden op de werkbalk. Selecteer deze om de sjabloon en parameterbestanden te uploaden naar de bestandsshare:

![bestandsshare](media/tutorial-deploy-hsm-powershell/file-share.png)

Wanneer de bestanden zijn geüpload, bent u klaar om resources te maken.
Voordat u nieuwe HSM-resources maakt, controleert u eerst of de volgende vereiste resources aanwezig zijn. U moet een virtueel netwerk hebben met subnetbereiken voor compute, HSM's en gateway. De volgende opdrachten zijn een voorbeeld van hoe een dergelijk virtueel netwerk wordt gemaakt.

```powershell
$compute = New-AzVirtualNetworkSubnetConfig `
  -Name compute `
  -AddressPrefix 10.2.0.0/24
```

```powershell
$delegation = New-AzDelegation `
  -Name "myDelegation" `
  -ServiceName "Microsoft.HardwareSecurityModules/dedicatedHSMs"

```

```powershell
$hsmsubnet = New-AzVirtualNetworkSubnetConfig ` 
  -Name hsmsubnet ` 
  -AddressPrefix 10.2.1.0/24 ` 
  -Delegation $delegation 

```

```powershell

$gwsubnet= New-AzVirtualNetworkSubnetConfig `
  -Name GatewaySubnet `
  -AddressPrefix 10.2.255.0/26

```

```powershell

New-AzVirtualNetwork `
  -Name myHSM-vnet `
  -ResourceGroupName myRG `
  -Location westus `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $compute, $hsmsubnet, $gwsubnet

```

>[!NOTE]
>De belangrijkste configuratie die u voor het virtuele netwerk moet onthouden, is dat het subnet voor het HSM-apparaat delegaties moet hebben die zijn ingesteld op 'Microsoft.HardwareSecurityModules/dedicatedHSMs'.  De HSM-inrichting werkt anders niet.

Wanneer aan alle vereisten is voldaan, voert u de volgende opdracht uit om de Resource Manager-sjabloon te gebruiken om te zorgen dat de waarden worden bijgewerkt met uw unieke namen (in ieder geval de naam van de resourcegroep):

```powershell

New-AzResourceGroupDeployment -ResourceGroupName myRG `
     -TemplateFile .\Deploy-2HSM-toVNET-Template.json `
     -TemplateParameterFile .\Deploy-2HSM-toVNET-Params.json `
     -Name HSMdeploy -Verbose

```

Het duurt ongeveer 20 minuten om deze opdracht te voltooien. Het gebruik van de optie '-verbose' zorgt ervoor dat de status voortdurend wordt weergegeven.

![inrichtingsstatus](media/tutorial-deploy-hsm-powershell/progress-status.png)

Wanneer de opdracht is voltooid, wat wordt weergegeven door 'provisioningState': 'Succeeded', kunt u zich aanmelden bij uw bestaande virtuele machine en SSH gebruiken om de beschikbaarheid van het HSM-apparaat te garanderen.

## <a name="verifying-the-deployment"></a>De implementatie controleren

Voer de volgende opdrachtenset uit om te controleren of de apparaten zijn ingericht en de apparaatkenmerken weer te geven. Controleer of de resourcegroep op de juiste wijze is ingesteld en de resourcenaam overeenkomt met die in het parameterbestand.

```powershell

$subid = (Get-AzContext).Subscription.Id
$resourceGroupName = "myRG"
$resourceName = "HSM1"  
Get-AzResource -Resourceid /subscriptions/$subId/resourceGroups/$resourceGroupName/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/$resourceName

```

![inrichtingsstatus](media/tutorial-deploy-hsm-powershell/progress-status2.png)

Nu kunt u ook de resources zien met behulp van de [Azure Resource Explorer](https://resources.azure.com/).   In de Explorer vouwt u aan de linkerkant 'Subscriptions' uit. Vouw achtereenvolgens uw specifieke abonnement voor Toegewezen HSM, 'Resource Groups' en de gebruikte resourcegroep uit. Selecteer tot slot het item 'Resources'.

## <a name="testing-the-deployment"></a>De implementatie testen

Om de implementatie te testen, maakt u verbinding met een virtuele machine die toegang heeft tot de HSM('s) en maakt u vervolgens rechtstreeks verbinding met het HSM-apparaat. Deze acties bevestigen dat de HSM bereikbaar is.
Het SSH-hulpprogramma wordt gebruikt om verbinding te maken met de virtuele machine. De opdracht is vergelijkbaar met de volgende, maar met de beheerdersnaam en DNS-naam die u hebt opgegeven in de parameter.

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

Gebruik het wachtwoord uit het parameterbestand.
Wanneer u bent aangemeld op de Linux-VM, kunt u zich bij de HSM aanmelden met behulp van het privé IP-adres dat u in de portal kunt vinden voor de resource \<prefix>hsm_vnic.

```powershell

(Get-AzResource -ResourceGroupName myRG -Name HSMdeploy -ExpandProperties).Properties.networkProfile.networkInterfaces.privateIpAddress

```
Wanneer u het IP-adres hebt, voert u de volgende opdracht uit:

`ssh tenantadmin@<ip address of HSM>`

Als dit lukt, wordt u om een wachtwoord gevraagd. Het standaardwachtwoord is PASSWORD. De HSM vraagt u om het wachtwoord te wijzigen. Stel een sterk wachtwoord in en gebruik het mechanisme waaraan uw organisatie de voorkeur geeft om het wachtwoord op te slaan en verlies te voorkomen.  

>[!IMPORTANT]
>Als u dit wachtwoord kwijtraakt, moet de HSM opnieuw worden ingesteld, wat verlies van uw codes betekent.

Wanneer u met behulp van SSH met het HSM-apparaat bent verbonden, voert u de volgende opdracht uit om te controleren of de HSM functioneert.

`hsm show`

De uitvoer moet er uitzien als in de afbeelding hieronder wordt weergegeven:

![Schermopname van de uitvoer van de opdracht hsm show.](media/tutorial-deploy-hsm-powershell/output.png)

Op dit moment hebt u alle resources toegewezen voor een implementatie van twee HSM's met hoge beschikbaarheid en hebt u de toegang en operationele status gevalideerd. Voor verdere configuratie of tests is meer werk met het HSM-apparaat zelf vereist. Hiervoor moet u de instructies in de Thales Luna 7 HSM-beheer handleiding hoofd stuk 7 volgen om de HSM te initialiseren en partities te maken. Alle documentatie en software zijn direct beschikbaar vanaf Thales voor downloaden zodra u bent geregistreerd in de [Thales-portal voor klanten ondersteuning](https://supportportal.thalesgroup.com/csm) en een klant-id hebt. Download versie 7.2 van de clientsoftware om alle vereiste onderdelen op te halen.

## <a name="delete-or-clean-up-resources"></a>Resources verwijderen of opschonen

Als u klaar bent met het HSM-apparaat, kan het als resource worden verwijderd en worden geretourneerd aan de vrije pool. Uiteraard moet u zorg dragen voor eventuele vertrouwelijke gegevens van klanten die zich op het apparaat bevinden. De snelste manier om een apparaat op nul te zetten is het HSM-beheerderswachtwoord 3 keer fout in te voeren. (Opmerking: dit is niet de apparaatbeheerder, maar de HSM-beheerder zelf.) Als veiligheidsmaatregel om belangrijk materiaal te beschermen, kan het apparaat pas worden verwijderd als Azure-resource als het de nulstatus heeft.

> [!NOTE]
> Als u problemen hebt met de configuratie van een Thales-apparaat, neemt u contact op met [Thales-klantondersteuning](https://supportportal.thalesgroup.com/csm).

Als u de HSM-resource in Azure wilt verwijderen, kunt u de volgende opdracht gebruiken om de '$'-variabelen te vervangen door uw unieke parameters:

```powershell

$subid = (Get-AzContext).Subscription.Id
$resourceGroupName = "myRG" 
$resourceName = "HSMdeploy"  
Remove-AzResource -Resourceid /subscriptions/$subId/resourceGroups/$resourceGroupName/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/$resourceName 

```

## <a name="next-steps"></a>Volgende stappen

Wanneer u de stappen in de zelfstudie hebt voltooid, zijn toegewezen HSM-resources ingericht en beschikbaar in uw virtuele netwerk. Nu kunt u deze implementatie aanvullen met meer resources als dit nodig is voor de implementatiearchitectuur waaraan u de voorkeur geeft. Zie de Concepts-documenten voor meer informatie over het plannen van een implementatie. Een ontwerp met twee HSM's in een primaire regio voor beschikbaarheid op rekniveau, en twee HSM's in een secundaire regio voor regionale beschikbaarheid wordt aanbevolen. Het sjabloonbestand dat in deze zelfstudie wordt gebruikt, kan eenvoudig worden gebruikt als basis voor een implementatie met twee HSM's, maar de parameters ervan moeten worden gewijzigd om te voldoen aan uw vereisten.

* [Hoge beschikbaarheid](high-availability.md)
* [Fysieke beveiliging](physical-security.md)
* [Netwerken](networking.md)
* [Controle](monitoring.md)
* [Ondersteuning](supportability.md)
