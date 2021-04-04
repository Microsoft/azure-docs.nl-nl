---
title: Een sjabloon gebruiken om een virtuele Windows-machine toe te voegen aan Azure AD DS | Microsoft Docs
description: Meer informatie over het gebruik van Azure Resource Manager sjablonen om een nieuwe of bestaande virtuele Windows Server-machine toe te voegen aan een Azure Active Directory Domain Services beheerd domein.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: e7245e8e468ea051ee095d97cc250ad303aa80a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619433"
---
# <a name="join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain-using-a-resource-manager-template"></a>Een virtuele machine met Windows Server toevoegen aan een Azure Active Directory Domain Services beheerd domein met behulp van een resource manager-sjabloon

Als u de implementatie en configuratie van virtuele machines van Azure (Vm's) wilt automatiseren, kunt u een resource manager-sjabloon gebruiken. Met deze sjablonen kunt u elke keer consistente implementaties maken. Uitbrei dingen kunnen ook worden opgenomen in sjablonen voor het automatisch configureren van een virtuele machine als onderdeel van de implementatie. Een handige uitbrei ding koppelt Vm's aan een domein, dat kan worden gebruikt met Azure Active Directory Domain Services (Azure AD DS) beheerde domeinen.

In dit artikel wordt beschreven hoe u een Windows Server-VM maakt en aan een Azure AD DS beheerd domein toevoegt met behulp van Resource Manager-sjablonen. U leert ook hoe u een bestaande Windows Server-VM kunt koppelen aan een Azure AD DS-domein.

## <a name="prerequisites"></a>Vereisten

Om deze zelfstudie te voltooien, hebt u de volgende resources en machtigingen nodig:

* Een actief Azure-abonnement.
    * Als u nog geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory-tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises map of een cloudmap.
    * [Maak zo nodig een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* Een door Azure Active Directory Domain Services beheerd domein dat in uw Azure AD-tenant is ingeschakeld en geconfigureerd.
    * Bekijk zo nodig de eerste zelfstudie voor [het maken en configureren van een door Azure Active Directory Domain Services beheerd domein][create-azure-ad-ds-instance].
* Een gebruikersaccount dat deel uitmaakt van het beheerde domein.

## <a name="azure-resource-manager-template-overview"></a>Overzicht van Azure Resource Manager sjablonen

Met Resource Manager-sjablonen kunt u de Azure-infra structuur in code definiëren. De vereiste resources, netwerk verbindingen of configuratie van Vm's kunnen allemaal worden gedefinieerd in een sjabloon. Deze sjablonen maken elke keer consistente, reproduceer bare implementaties en kunnen worden genoteerd wanneer u wijzigingen aanbrengt. Zie [Azure Resource Manager sjablonen Overview][template-overview](Engelstalig) voor meer informatie.

Elke resource wordt gedefinieerd in een sjabloon met behulp van JavaScript Object Notation (JSON). In het volgende JSON-voor beeld wordt het resource type *micro soft. Compute/informatie/Extensions* gebruikt voor het installeren van de Active Directory-extensie voor domein deelname. Para meters worden gebruikt die u tijdens de implementatie tijd opgeeft. Wanneer de uitbrei ding wordt geïmplementeerd, wordt de virtuele machine gekoppeld aan het opgegeven beheerde domein.

```json
 {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('dnsLabelPrefix'),'/joindomain')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('dnsLabelPrefix'))]"
      ],
      "properties": {
        "publisher": "Microsoft.Compute",
        "type": "JsonADDomainExtension",
        "typeHandlerVersion": "1.3",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "Name": "[parameters('domainToJoin')]",
          "OUPath": "[parameters('ouPath')]",
          "User": "[concat(parameters('domainToJoin'), '\\', parameters('domainUsername'))]",
          "Restart": "true",
          "Options": "[parameters('domainJoinOptions')]"
        },
        "protectedSettings": {
          "Password": "[parameters('domainPassword')]"
        }
      }
    }
```

Deze VM-extensie kan worden geïmplementeerd, zelfs als u geen virtuele machine in dezelfde sjabloon maakt. In de voor beelden in dit artikel ziet u de volgende methoden:

* [Een Windows Server-VM maken en lid worden van een beheerd domein](#create-a-windows-server-vm-and-join-to-a-managed-domain)
* [Een bestaande virtuele machine met Windows Server toevoegen aan een beheerd domein](#join-an-existing-windows-server-vm-to-a-managed-domain)

## <a name="create-a-windows-server-vm-and-join-to-a-managed-domain"></a>Een Windows Server-VM maken en lid worden van een beheerd domein

Als u een virtuele machine met Windows Server nodig hebt, kunt u er een maken en configureren met behulp van een resource manager-sjabloon. Wanneer de virtuele machine wordt geïmplementeerd, wordt vervolgens een uitbrei ding geïnstalleerd om de virtuele machine toe te voegen aan een beheerd domein. Als u al een virtuele machine hebt die u wilt toevoegen aan een beheerd domein, gaat u door met het [toevoegen van een bestaande Windows Server-VM aan een beheerd domein](#join-an-existing-windows-server-vm-to-a-managed-domain).

Als u een Windows Server-VM wilt maken, voegt u deze toe aan een beheerd domein, voert u de volgende stappen uit:

1. Blader naar de [Quick](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)start-sjabloon. Selecteer de optie om **te implementeren naar Azure**.
1. Voer op de pagina **aangepaste implementatie** de volgende informatie in om een virtuele machine van Windows Server te maken en toe te voegen aan het beheerde domein:

    | Instelling                   | Waarde |
    |---------------------------|-------|
    | Abonnement              | Kies hetzelfde Azure-abonnement waarin u Azure AD Domain Services hebt ingeschakeld. |
    | Resourcegroep            | Kies de resource groep voor de virtuele machine. |
    | Locatie                  | Selecteer de locatie van voor uw VM. |
    | Bestaande VNET-naam        | De naam van het bestaande virtuele netwerk waarmee de virtuele machine moet worden verbonden, zoals *myVnet*. |
    | Bestaande Subnetnaam      | De naam van het bestaande subnet van het virtuele netwerk, zoals *werk belastingen*. |
    | Voorvoegsel van DNS-label          | Voer een DNS-naam in om te gebruiken voor de virtuele machine, zoals *myvm*. |
    | VM-grootte                   | Geef een VM-grootte op, bijvoorbeeld *Standard_DS2_v2*. |
    | Domein om lid te worden            | De DNS-naam van het beheerde domein, zoals *aaddscontoso.com*. |
    | Domein gebruikers naam           | Het gebruikers account in het beheerde domein dat moet worden gebruikt om de virtuele machine toe te voegen aan het beheerde domein, zoals `contosoadmin@aaddscontoso.com` . Dit account moet deel uitmaken van het beheerde domein. |
    | Domein wachtwoord           | Het wacht woord voor het gebruikers account dat is opgegeven in de vorige instelling. |
    | Optioneel pad naar OE          | De aangepaste organisatie-eenheid waaraan de virtuele machine moet worden toegevoegd. Als u geen waarde opgeeft voor deze para meter, wordt de virtuele machine toegevoegd aan de standaard organisatie-eenheid voor *Aad DC-computers* . |
    | Gebruikers naam van de VM-beheerder         | Geef een lokaal Administrator-account op om te maken op de VM. |
    | Wacht woord voor de VM-beheerder         | Geef een lokaal beheerders wachtwoord op voor de virtuele machine. Maak een sterk wacht woord voor de lokale beheerder om te beschermen tegen aanvallen met een wacht woord. |

1. Bekijk de voor waarden en schakel vervolgens het selectie vakje in voor de **bovenstaande voor waarden**. Wanneer u klaar bent, selecteert u **kopen** om de virtuele machine te maken en aan het beheerde domein toe te voegen.

> [!WARNING]
> **Wacht woorden met een waarschuwing.**
> Het sjabloon parameter bestand vraagt het wacht woord aan voor een gebruikers account dat deel uitmaakt van het beheerde domein. Voer geen waarden hand matig in dit bestand in en laat het toegankelijk op bestands shares of andere gedeelde locaties.

Het duurt enkele minuten voordat de implementatie is voltooid. Wanneer u klaar bent, wordt de Windows-VM gemaakt en gekoppeld aan het beheerde domein. De virtuele machine kan worden beheerd of aangemeld met behulp van domein accounts.

## <a name="join-an-existing-windows-server-vm-to-a-managed-domain"></a>Een bestaande virtuele machine met Windows Server toevoegen aan een beheerd domein

Als u een bestaande virtuele machine of een groep virtuele machines hebt die u wilt toevoegen aan een beheerd domein, kunt u een resource manager-sjabloon gebruiken om alleen de VM-extensie te implementeren.

Voer de volgende stappen uit om een bestaande virtuele machine met Windows Server toe te voegen aan een beheerd domein:

1. Blader naar de [Quick](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)start-sjabloon. Selecteer de optie om **te implementeren naar Azure**.
1. Voer op de pagina **aangepaste implementatie** de volgende gegevens in om de virtuele machine aan het beheerde domein toe te voegen:

    | Instelling                   | Waarde |
    |---------------------------|-------|
    | Abonnement              | Kies hetzelfde Azure-abonnement waarin u Azure AD Domain Services hebt ingeschakeld. |
    | Resourcegroep            | Kies de resource groep met uw bestaande VM. |
    | Locatie                  | Selecteer de locatie van de bestaande virtuele machine. |
    | VM-lijst                   | Voer de door komma's gescheiden lijst in van de bestaande virtuele machine (s) die u wilt toevoegen aan het beheerde domein, zoals *myVM1, myVM2*. |
    | Gebruikers naam voor domein deelname     | Het gebruikers account in het beheerde domein dat moet worden gebruikt om de virtuele machine toe te voegen aan het beheerde domein, zoals `contosoadmin@aaddscontoso.com` . Dit account moet deel uitmaken van het beheerde domein. |
    | Domein deelname gebruikers wachtwoord | Het wacht woord voor het gebruikers account dat is opgegeven in de vorige instelling. |
    | Optioneel pad naar OE          | De aangepaste organisatie-eenheid waaraan de virtuele machine moet worden toegevoegd. Als u geen waarde opgeeft voor deze para meter, wordt de virtuele machine toegevoegd aan de standaard organisatie-eenheid voor *Aad DC-computers* . |

1. Bekijk de voor waarden en schakel vervolgens het selectie vakje in voor de **bovenstaande voor waarden**. Wanneer u klaar bent, selecteert u **kopen** om de virtuele machine toe te voegen aan het beheerde domein.

> [!WARNING]
> **Wacht woorden met een waarschuwing.**
> Het sjabloon parameter bestand vraagt het wacht woord aan voor een gebruikers account dat deel uitmaakt van het beheerde domein. Voer geen waarden hand matig in dit bestand in en laat het toegankelijk op bestands shares of andere gedeelde locaties.

Het duurt even voordat de implementatie is voltooid. Wanneer u klaar bent, worden de opgegeven Windows-Vm's gekoppeld aan het beheerde domein en kunnen ze worden beheerd of aangemeld met behulp van domein accounts.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u de Azure Portal gebruikt voor het configureren en implementeren van resources met behulp van sjablonen. U kunt resources ook implementeren met Resource Manager-sjablonen met behulp van [Azure PowerShell][deploy-powershell] of de [Azure cli][deploy-cli].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[template-overview]: ../azure-resource-manager/templates/overview.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
