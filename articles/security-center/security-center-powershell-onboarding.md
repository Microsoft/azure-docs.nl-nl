---
title: Onboarden naar Azure Security Center met PowerShell
description: Dit document helpt u bij het inschakelen van Azure Security Center met Power shell-cmdlets.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 25639509cfb689e025ea4388f302378648af8ba6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102439490"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>Onboarding naar Azure Security Center automatiseren met PowerShell

U kunt uw Azure-workloads programmatisch beveiligen met behulp van de PowerShell-module van Azure Security Center.
Met behulp van PowerShell kunt u taken automatiseren en menselijke fouten die inherent zijn aan handmatige taken voorkomen. Dit is vooral handig bij grootschalige implementaties waarbij tientallen abonnementen betrokken zijn met honderden en duizenden resources, die allemaal vanaf het begin moeten worden beveiligd.

Met onboarding naar Azure Security Center met behulp van PowerShell kunt u uw Azure-resources op een programmatische manier onboarden en beheren en de benodigde beveiligingscontroles toevoegen.

Dit artikel bevat een voorbeeld van een PowerShell-script dat kan worden gewijzigd en in uw omgeving kan worden gebruikt om Security Center in uw abonnementen uit te rollen. 

In dit voorbeeld wordt Security Center ingeschakeld voor een abonnement met ID: d07c0080-170c-4c24-861d-9c817742786c en worden de aanbevolen instellingen toegepast die een hoog niveau van beveiliging bieden door Azure Defender in te schakelen. Dit biedt geavanceerde mogelijkheden voor bescherming tegen en detectie van bedreigingen:

1. Schakel [Azure Defender](azure-defender.md) in. 
 
2. Stel de Log Analytics-werkruimte in waarnaar de Log Analytics-agent de verzamelde gegevens gaat verzenden op de virtuele machines die zijn gekoppeld aan het abonnement. In dit voorbeeld is dit een bestaande, door de gebruiker gedefinieerde werkruimte (myWorkspace).

3. Activeer de automatische agentinrichting van Security Center die [de Log Analytics-agent](security-center-enable-data-collection.md#auto-provision-mma) implementeert.

5. Stel de [CISO van de organisatie in als de beveiligingscontactpersoon voor Security Center-waarschuwingen en gebeurtenissen die aandacht vereisen](security-center-provide-security-contact-details.md).

6. Wijs [standaardbeveiligingsbeleid](tutorial-security-policy.md) van Security Center toe.

## <a name="prerequisites"></a>Vereisten

U moet deze stappen uitvoeren voordat u de Security Center-cmdlets uitvoert:

1. Voer PowerShell uit als beheerder.

1. Voer de volgende opdrachten uit in PowerShell:
      
    ```Set-ExecutionPolicy -ExecutionPolicy AllSigned```

    ```Install-Module -Name Az.Security -Force```

## <a name="onboard-security-center-using-powershell"></a>Security Center onboarden met PowerShell

1. Registreer uw abonnementen bij de resourceprovider van Security Center:

    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```

    ```Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security'```

1. Optioneel: Stel het dekkingsniveau (Azure Defender aan/uit) van de abonnementen in. Als u dit niet hebt ingesteld, is Defender uitgeschakeld:

    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```

    ```Set-AzSecurityPricing -Name "default" -PricingTier "Standard"```

1. Configureer een Log Analytics-werkruimte waaraan de agents rapporteren. U moet een Log Analytics-werkruimte hebben die u al hebt gemaakt, waaraan de VM's van het abonnement rapporteren. U kunt meerdere abonnementen definiëren om aan dezelfde werkruimte te rapporteren. Als dit niet is gedefinieerd, wordt de standaardwerkruimte gebruikt.

    ```Set-AzSecurityWorkspaceSetting -Name "default" -Scope "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"```

1. Richt de installatie van de Log Analytics-agent op uw Azure-VM's automatisch in:
    
    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```
    
    ```Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision```

    > [!NOTE]
    > Het wordt aanbevolen om automatische inrichting in te schakelen om ervoor te zorgen dat uw virtuele Azure-machines automatisch worden beveiligd door Azure Security Center.
    >

1. Optioneel: Het wordt ten zeerste aangeraden om de contactgegevens voor beveiliging te definiëren voor de abonnementen die u wilt onboarden. Deze worden gebruikt als ontvangers van waarschuwingen en meldingen die worden gegenereerd door Security Center:

    ```Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -Phone "2142754038" -AlertAdmin -NotifyOnAlert```

1. Wijs het standaardbeleidsinitiatief van Security Center toe:

    ```Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'```

    ```$Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ 'Azure Security Benchmark'} New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'```

De onboarding van Azure Security Center met PowerShell is geslaagd.

U kunt deze PowerShell-cmdlets nu gebruiken met automatiseringsscripts om de onboarding op een programmatische manier te herhalen voor abonnementen en resources. Dit bespaart tijd en vermindert de kans op menselijke fouten. U kunt dit [voorbeeldscript](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1) als referentie gebruiken.




## <a name="see-also"></a>Zie ook
Zie het volgende artikel voor meer informatie over hoe u PowerShell kunt gebruiken om onboarding naar Security Center te automatiseren:

* [Az.Security](/powershell/module/az.security)

Zie het volgende artikel voor meer informatie over Security Center:

* [Setting security policies in Azure Security Center](tutorial-security-policy.md) (Beveiligingsbeleid instellen in Azure Security Center): leer hoe u beveiligingsbeleid voor uw Azure-abonnementen en -resourcegroepen configureert.
* [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md) (Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center): ontdek hoe u beveiligingswaarschuwingen kunt beheren en erop kunt reageren.