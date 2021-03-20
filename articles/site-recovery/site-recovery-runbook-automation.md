---
title: Azure Automation runbooks toevoegen aan Site Recovery-herstel plannen
description: Meer informatie over het uitbreiden van herstel plannen met Azure Automation voor herstel na nood geval met behulp van Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: ramamill
ms.openlocfilehash: a141280338632fdad7053cbbe76c8bdf2797443d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89424868"
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Azure Automation-runbooks aan herstelplannen toevoegen

In dit artikel wordt beschreven hoe u Azure Automation runbooks integreert om [Azure site Recovery](site-recovery-overview.md) herstel plannen uit te breiden. We laten u zien hoe u basis taken automatiseert die anders hand matig moeten worden uitgevoerd en hoe u een herstel met meerdere stappen kunt omzetten in een actie met één klik.

## <a name="recovery-plans"></a>Herstelplannen 

U kunt herstel plannen gebruiken bij het uitvoeren van een failover van on-premises machines of Azure-Vm's. Met herstel plannen kunt u een systematisch herstel proces definiëren dat definieert hoe machines failover uitvoeren en hoe ze worden gestart en hersteld na een failover. 

Herstel van grote apps kan ingewikkeld zijn. Herstel plannen zorgen ervoor dat herstel consistent, herhaalbaar en geautomatiseerd is. U kunt taken in een herstel plan automatiseren met behulp van scripts en Azure Automation runbooks. Voor beelden hiervan zijn het configureren van instellingen op een virtuele Azure-machine na een failover, of het opnieuw configureren van een app die wordt uitgevoerd op de VM.

- [Meer informatie](recovery-plan-overview.md) over herstel plannen.
- [Meer informatie](../automation/automation-runbook-types.md) over Azure Automation runbooks.



## <a name="runbooks-in-recovery-plans"></a>Runbooks in herstel plannen

U voegt een Azure Automation-account en runbooks toe aan een herstel plan. Het runbook wordt aangeroepen wanneer het herstel plan wordt uitgevoerd.

- Het Automation-account kan zich in een Azure-regio bevinden en moet zich in hetzelfde abonnement bevinden als de Site Recovery kluis. 
- Een runbook kan worden uitgevoerd in een herstel plan tijdens een failover van een primaire locatie naar een secundair of tijdens het failback van de secundaire locatie naar het primaire.
- Runbooks in een herstel plan worden serieel op één na elkaar uitgevoerd in de set-volg orde.
- Als runbooks in een herstel plan Vm's configureren om in verschillende groepen te starten, wordt het herstel plan alleen voortgezet wanneer Azure alle Vm's rapporteert als actief.
- Herstel plannen blijven actief, zelfs als een script mislukt.

### <a name="recovery-plan-context"></a>Context van herstel plan

Wanneer een script wordt uitgevoerd, wordt een context voor het herstel plan ingevoegd in het runbook. De context bevat de variabelen die in de tabel worden samenvatten.

| **Naam van de variabele** | **Beschrijving** |
| --- | --- |
| RecoveryPlanName |Naam van het herstel plan. Wordt gebruikt in acties op basis van de naam. |
| FailoverType |Hiermee geeft u op of het een test-of productie-failover is. 
| FailoverDirection | Hiermee geeft u op of herstel op een primaire of secundaire locatie is. |
| Groep |Hiermee wordt het groeps nummer in het herstel plan geïdentificeerd wanneer het plan wordt uitgevoerd. |
| VmMap |Een matrix van alle virtuele machines in de groep. |
| VMMap-sleutel |Een unieke sleutel (GUID) voor elke virtuele machine. |
| SubscriptionId |De ID van het Azure-abonnement waarin de virtuele machine is gemaakt. |
| ResourceGroupName | De naam van de resource groep waarin de virtuele machine zich bevindt.
| CloudServiceName |De naam van de Azure-Cloud service waaronder de virtuele machine is gemaakt. |
| RoleName |De naam van de Azure-VM. |
| RecoveryPointId|De tijds tempel voor het herstel van de virtuele machine. |

>[!Note]
>De waarde voor de variabele ' FailoverDirection ' is ' PrimaryToSecondary ' in het geval van een failover en ' SecondaryToPrimary ' in het geval van failback.

In het volgende voor beeld ziet u een context variabele:

```
{"RecoveryPlanName":"hrweb-recovery",
"FailoverType":"Test",
"FailoverDirection":"PrimaryToSecondary",
"GroupId":"1",
"VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":
    { "SubscriptionId":"7a1111111-c1d6-49c5-8c5d-111ce8dd183",
    "ResourceGroupName":"ContosoRG",
    "CloudServiceName":"pod02hrweb-Chicago-test",
    "RoleName":"Fabrikam-Hrweb-frontend-test",
    "RecoveryPointId":"TimeStamp"}
    }
}
```

Als u in een lus toegang wilt krijgen tot alle Vm's in VMMap, kunt u de volgende code gebruiken:

```
$VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
$vmMap = $RecoveryPlanContext.VmMap
    foreach($VMID in $VMinfo)
    {
        $VM = $vmMap.$VMID                
            if( !(($VM -eq $Null) -Or ($VM.ResourceGroupName -eq $Null) -Or ($VM.RoleName -eq $Null))) {
            #this check is to ensure that we skip when some data is not available else it will fail
    Write-output "Resource group name ", $VM.ResourceGroupName
    Write-output "Rolename " = $VM.RoleName
            }
        }
```


De Aman-blog van Sharma in de [oogst van Clouds](http://harvestingclouds.com) is een nuttig voor beeld van een [context script van een herstel plan](http://harvestingclouds.com/post/script-sample-azure-automation-runbook-for-asr-recovery-plan/).



## <a name="before-you-start"></a>Voordat u begint

- Als u nog geen ervaring hebt met Azure Automation, kunt u [zich registreren](https://azure.microsoft.com/services/automation/) en [voorbeeld scripts downloaden](https://azure.microsoft.com/documentation/scripts/).
- Zorg ervoor dat het Automation-account de volgende modules bevat:
    - AzureRM. profile
    - AzureRM.Resources
    - AzureRM.Automation
    - AzureRM.Network
    - AzureRM.Compute

    Alle modules moeten compatibele versies hebben. De eenvoudigste manier is om altijd de nieuwste versies van alle modules te gebruiken.



## <a name="customize-the-recovery-plan"></a>Het herstel plan aanpassen

1. Selecteer in de kluis **herstel plannen (site Recovery)**
2. Klik op **+ herstel plan** om een herstel plan te maken. [Meer informatie](site-recovery-create-recovery-plans.md). Als u al een herstel plan hebt, selecteert u om het te openen.
3. Klik op de pagina herstel plan op **aanpassen**.

    ![Klik op de knop aanpassen](media/site-recovery-runbook-automation-new/custom-rp.png)

2. Klik op de weglatings tekens (...) naast **groep 1:** de  >  **actie post toevoegen** starten.
3. Controleer in **actie invoegen** of **script** is geselecteerd en geef een naam op voor het script (**Hallo wereld**).
4. Geef een Automation-account op en selecteer een runbook. Klik op **OK** om het script op te slaan. Het script wordt toegevoegd aan **groep 1: post-stappen**.


## <a name="reuse-a-runbook-script"></a>Een runbook-script opnieuw gebruiken

U kunt één runbook-script in meerdere herstel plannen gebruiken met behulp van externe variabelen. 

- U gebruikt [Azure Automation variabelen](../automation/shared-resources/variables.md) om para meters op te slaan voor het uitvoeren van een herstel plan.
- Door de naam van het herstel plan als een voor voegsel aan de variabele toe te voegen, kunt u afzonderlijke variabelen voor elk herstel plan maken. Gebruik vervolgens de variabelen als para meters.
- U kunt een para meter wijzigen zonder het script te wijzigen, maar toch de manier wijzigen waarop het script werkt.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Een eenvoudige teken reeks variabele gebruiken in een runbook-script

In dit voor beeld neemt een script de invoer van een netwerk beveiligings groep (NSG) en past deze toe op de virtuele machines in een herstel plan. 

1. Als het script kan detecteren welk herstel plan wordt uitgevoerd, gebruikt u de context van het herstel plan:

    ```
    workflow AddPublicIPAndNSG {
        param (
              [parameter(Mandatory=$false)]
              [Object]$RecoveryPlanContext
        )

        $RPName = $RecoveryPlanContext.RecoveryPlanName
    ```

2. Noteer de naam van de NSG en de resource groep. U gebruikt deze variabelen als invoer voor scripts voor herstel plannen. 
1. In de assets van het Automation-account. Maak een variabele om de NSG-naam op te slaan. Voeg een voor voegsel toe aan de naam van de variabele met de naam van het herstel plan.

    ![Een variabele met de naam NSG maken](media/site-recovery-runbook-automation-new/var1.png)

2. Maak een variabele om de naam van de resource groep op te slaan voor de NSG-resource. Voeg een voor voegsel toe aan de naam van de variabele met de naam van het herstel plan.

    ![Een naam voor de NSG-resource groep maken](media/site-recovery-runbook-automation-new/var2.png)


3.  Gebruik in het script deze referentie code om de variabele waarden op te halen:

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  Gebruik de variabelen in het runbook om de NSG toe te passen op de netwerk interface van de failover-VM:

    ```
    InlineScript {
    if (($Using:NSGname -ne $Null) -And ($Using:NSGRGname -ne $Null)) {
            $NSG = Get-AzureRmNetworkSecurityGroup -Name $Using:NSGname -ResourceGroupName $Using:NSGRGname
            Write-output $NSG.Id
            #Apply the NSG to a network interface
            #$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
            #Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            #  -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $NSG
        }
    }
    ```


Maak voor elk herstel plan onafhankelijke variabelen, zodat u het script opnieuw kunt gebruiken. Voeg een voor voegsel toe met behulp van de naam van het herstel plan. 

Bekijk [Dit script](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee)voor een volledig, end-to-end script voor dit scenario.


### <a name="use-a-complex-variable-to-store-more-information"></a>Een complexe variabele gebruiken om meer informatie op te slaan

In sommige scenario's kunt u mogelijk geen afzonderlijke variabelen maken voor elk herstel plan. Overweeg een scenario waarin u met één script een openbaar IP-adres aan specifieke Vm's wilt toewijzen. In een ander scenario wilt u mogelijk verschillende Nsg's Toep assen op verschillende Vm's (niet op alle virtuele machines). Opmerking:

- U kunt een script maken dat voor elk herstel plan opnieuw kan worden gebruikt.
- Elk herstel plan kan een variabel aantal Vm's hebben.
- Een share point-herstel heeft bijvoorbeeld twee front-ends. Een Basic Line-of-Business-toepassing (LOB) heeft slechts één front-end.
- In dit scenario kunt u geen afzonderlijke variabelen maken voor elk herstel plan.

In het volgende voor beeld maken we een [complexe variabele](/powershell/module/servicemanagement/azure.service/set-azureautomationvariable) in het Azure Automation-account.

We doen dit door meerdere waarden op te geven met behulp van Azure PowerShell.

1. Meld u in Power shell aan bij uw Azure-abonnement:

    ```
    Connect-AzureRmAccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. Als u de para meters wilt opslaan, maakt u de complexe variabele met de naam van het herstel plan:

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. In deze complexe variabele is **VMDetails** de VM-id voor de beveiligde virtuele machine. Als u de VM-ID wilt ophalen, bekijkt u in het Azure Portal de eigenschappen van de virtuele machine. De volgende scherm afbeelding toont een variabele waarin de details van twee Vm's worden opgeslagen:

    ![De VM-ID als GUID gebruiken](media/site-recovery-runbook-automation-new/vmguid.png)

4. Gebruik deze variabele in uw runbook. Als de aangegeven VM-GUID wordt gevonden in de context van het herstel plan, past u de NSG op de virtuele machine toe:

    ```
    $VMDetailsObj = (Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName).ToObject([hashtable])
    ```

4. Door loop de Vm's van de context van het herstel plan in uw runbook. Controleer of de virtuele machine bestaat in **$VMDetailsObj**. Als deze bestaat, opent u de eigenschappen van de variabele om de NSG toe te passen:

    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap

        foreach($VMID in $VMinfo) {
            $VMDetails = $VMDetailsObj[$VMID].ToObject([hashtable]);
            Write-output $VMDetails
            if ($VMDetails -ne $Null) { #If the VM exists in the context, this will not be Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetails.NSGName
                $NSGRGname = $VMDetails.NSGResourceGroupName

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

U kunt hetzelfde script gebruiken voor verschillende herstel plannen. Voer verschillende para meters in door de waarde op te slaan die overeenkomt met een herstel plan in verschillende variabelen.

## <a name="sample-scripts"></a>Voorbeeldscripts

Als u voorbeeld scripts wilt implementeren in uw Automation-account, klikt u op de knop **implementeren in azure** .

[![Implementeren in Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Deze video bevat een ander voor beeld. In dit voor beeld ziet u hoe u een WordPress-toepassing met twee lagen naar Azure herstelt:


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over een [Azure Automation uitvoeren als-account](../automation/manage-runas-account.md)
- Bekijk [Azure Automation voorbeeld scripts](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=User&f%5B0%5D.Value=SC%20Automation%20Product%20Team&f%5B0%5D.Text=SC%20Automation%20Product%20Team).
- [Meer informatie](site-recovery-failover.md) over het uitvoeren van failovers.
