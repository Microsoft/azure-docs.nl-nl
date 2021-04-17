---
title: Verbinding maken met virtuele machine starten - Azure
description: Het configureren van de virtuele machine starten op de verbindingsfunctie.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: af95cf5d3e4112c717d653062f186797d48fb515
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389805"
---
# <a name="start-virtual-machine-on-connect-preview"></a>Virtuele machine starten bij verbinding maken (preview)

> [!IMPORTANT]
> De functie VM starten in Verbinding maken is momenteel beschikbaar als openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Met de functie Virtuele machine (VM) starten op Verbinding maken (preview) kunt u kosten besparen door de toewijzing van uw VM's op te geven wanneer u deze niet gebruikt. Wanneer u de VM opnieuw moet gebruiken, hoeft u alleen uw VM's weer in te zetten.

>[!NOTE]
>Windows Virtual Desktop (klassiek) biedt geen ondersteuning voor deze functie.

## <a name="requirements-and-limitations"></a>Vereisten en beperkingen

U kunt de functie VM starten op Verbinding maken alleen inschakelen voor persoonlijke hostgroepen. Zie voor meer informatie over persoonlijke hostgroepen [Windows Virtual Desktop omgeving.](environment-setup.md#host-pools)

De volgende extern bureaublad-clients ondersteunen de functie VM starten op verbinding:

- [De webclient](connect-web.md)
- [De Windows-client (versie 1.2748 of hoger)](connect-windows-7-10.md)

U kunt controleren op aankondigingen over updates en clientondersteuning op het [Tech Community-forum.](https://aka.ms/wvdtc)

De Azure Government cloud biedt momenteel geen ondersteuning voor VM starten op verbinding.

## <a name="create-a-custom-role-for-start-vm-on-connect"></a>Een aangepaste rol maken voor het starten van een VM in Connect

Voordat u de functie VM starten in Verbinding maken kunt configureren, moet u aan uw VM een aangepaste RBAC-rol (op rollen gebaseerd toegangsbeheer) toewijzen. Met deze rol kunt Windows Virtual Desktop VM's in uw abonnement beheren. U kunt deze rol ook gebruiken om VM's in te stellen, de status ervan te controleren en diagnostische gegevens te rapporteren. Als u meer wilt weten over wat elke rol doet, bekijkt u [Aangepaste Azure-rollen.](../role-based-access-control/custom-roles.md)

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

Als u de Azure Portal om een aangepaste rol toe te wijzen voor VM starten op Verbinding maken:

1. Open de Azure Portal en ga naar **Abonnementen.**

2. Ga naar **Toegangsbeheer (IAM)** en selecteer **Een aangepaste rol toevoegen.**

    > [!div class="mx-imgBorder"]
    > ![Een schermopname van een vervolgkeuzelijst van de knop Toevoegen in Toegangsbeheer (IAM). 'Een aangepaste rol toevoegen' is rood gemarkeerd.](media/add-custom-role.png)

3. Geef vervolgens de aangepaste rol een naam en voeg een beschrijving toe. U wordt aangeraden deze de naam 'VM starten bij verbinding' te geven.

4. Voeg op **het tabblad** Machtigingen de volgende machtigingen toe aan het abonnement aan wie u de rol toewijst: 
 
   - Microsoft.Compute/virtualMachines/start/action
   - Microsoft.Compute/virtualMachines/read

5. Wanneer u klaar bent, selecteert u **OK.**

Daarna moet u de rol toewijzen om toegang te verlenen tot Windows Virtual Desktop.

De aangepaste rol toewijzen:

1. Selecteer op **het tabblad Toegangsbeheer (IAM)** **de optie Roltoewijzingen toevoegen.**

2. Selecteer de rol die u zojuist hebt gemaakt.

3. Voer in de zoekbalk in en selecteer **Windows Virtual Desktop**.

      >[!NOTE]
      >Mogelijk ziet u twee apps als u deze hebt Windows Virtual Desktop (klassiek). Wijs de rol toe aan beide apps die u ziet.
      >
      > [!div class="mx-imgBorder"]
      > ![Een schermopname van het tabblad Toegangsbeheer (IAM). In de zoekbalk zijn zowel Windows Virtual Desktop als Windows Virtual Desktop (klassiek) rood gemarkeerd.](media/add-role-assignment.png)

### <a name="create-a-custom-role-with-a-json-file-template"></a>Een aangepaste rol maken met een JSON-bestandssjabloon

Als u een JSON-bestand gebruikt om de aangepaste rol te maken, ziet u in het volgende voorbeeld een basissjabloon die u kunt gebruiken. Zorg ervoor dat u de waarde van de abonnements-id vervangt door de abonnements-id waar u de rol aan wilt toewijzen.

```json
{
    "properties": {
        "roleName": "start VM on connect",
        "description": "Friendly description.",
        "assignableScopes": [
            "/subscriptions/<SubscriptionID>"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Compute/virtualMachines/start/action",
                    "Microsoft.Compute/virtualMachines/read"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
    }
}
```

## <a name="configure-the-start-vm-on-connect-feature"></a>De functie VM starten bij verbinding maken configureren

Nu u de rol aan uw abonnement hebt toegewezen, is het tijd om de functie VM starten op verbinding te maken te configureren.

### <a name="deployment-considerations"></a>Overwegingen bij de implementatie 

VM starten op Verbinding maken is een instelling voor de hostgroep. Als u wilt dat alleen een bepaalde groep gebruikers deze functie gebruikt, moet u ervoor zorgen dat u alleen de vereiste rol toewijst aan de gebruikers die u wilt toevoegen.

>[!IMPORTANT]
> U kunt deze functie alleen configureren in bestaande hostgroepen. Deze functie is niet beschikbaar wanneer u een nieuwe hostgroep maakt.

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

Ga als volgende te werk om Azure Portal VM starten bij verbinding maken te configureren:

1. Open uw browser en ga naar [de Azure Portal.](https://portal.azure.com)

2. Ga in Azure Portal naar **Windows Virtual Desktop**.

3. Selecteer **Hostgroepen** en zoek vervolgens de hostgroep met de persoonlijke bureaubladen aan wie u de rol hebt toegewezen.

   >[!NOTE]
   > De hostgroep waarin u deze functie configureert, moet persoonlijke bureaubladen met directe roltoewijzingen hebben. Als de bureaubladen in de hostgroep niet correct zijn geconfigureerd, werkt het configuratieproces niet.

4. Selecteer Eigenschappen in de **hostgroep.** Selecteer **ja onder VM starten** bij verbinding maken en selecteer vervolgens Opslaan **om** de instelling direct toe te passen. 

    > [!div class="mx-imgBorder"]
    > ![Een schermopname van de venster Eigenschappen. De optie VM starten bij verbinding maken is rood gemarkeerd.](media/properties-start-vm-on-connect.png)

### <a name="use-powershell"></a>PowerShell gebruiken

Als u deze instelling wilt configureren met PowerShell, moet u ervoor zorgen dat u de namen hebt van de resourcegroep en hostgroepen die u wilt configureren. U moet ook de module Azure PowerShell [installeren (versie 2.1.0 of hoger).](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.1.0)

VM starten op verbinding maken met PowerShell configureren:

1. Open een PowerShell-opdrachtvenster.

2. Voer de volgende cmdlet uit om VM starten bij Verbinding maken in te stellen:

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$true
    ```

3. Voer de volgende cmdlet uit om VM starten bij verbinding maken uit te schakelen:

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$false
    ```

## <a name="user-experience"></a>Gebruikerservaring

In gewone sessies neemt de tijd die een gebruiker nodig heeft om verbinding te maken met een VM die niet is toegewezen toe, toe omdat de VM tijd nodig heeft om opnieuw in te zetten, net zoals bij het in-/uitschakelen van een fysieke computer. De Extern bureaublad-client heeft een indicator die de gebruiker laat weten dat de pc wordt ingeschakeld terwijl ze verbinding maken.

## <a name="troubleshooting"></a>Problemen oplossen

Als er problemen zijn met de functie, raden we u aan de functie Windows Virtual Desktop [gebruiken om](diagnostics-log-analytics.md) te controleren op problemen. Als u een foutbericht ontvangt, let dan goed op de inhoud van het bericht en kopieer de foutnaam ergens ter referentie.

U kunt ook Azure Monitor [voor Windows Virtual Desktop](azure-monitor.md) om suggesties te krijgen voor het oplossen van problemen.

## <a name="next-steps"></a>Volgende stappen

Als u problemen hebt die niet kunnen worden opgelost met de documentatie voor probleemoplossing of de diagnostische functie, raadpleegt u de Veelgestelde vragen over VM starten [op verbinding maken.](start-virtual-machine-connect-faq.md)