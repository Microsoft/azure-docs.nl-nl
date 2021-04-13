---
title: Verbinding met virtuele machine starten-Azure
description: De functie voor het starten van de virtuele machine in Connect configureren.
author: Heidilohr
ms.topic: how-to
ms.date: 04/10/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: d3ef8e3656051c4a99ab52a7b52a0d623fdf9ce2
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303952"
---
# <a name="start-virtual-machine-on-connect-preview"></a>Virtuele machine starten bij verbinding maken (preview)

> [!IMPORTANT]
> De functie voor het starten van de VM in Connect is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Met de functie voor het starten van de virtuele machine (VM) in Connect (preview) kunt u kosten besparen door de toewijzing van uw Vm's ongedaan te maken wanneer u deze niet gebruikt. Wanneer u de virtuele machine opnieuw moet gebruiken, hoeft u alleen maar uw Vm's weer in te scha kelen.

>[!NOTE]
>Windows virtueel bureau blad (klassiek) biedt geen ondersteuning voor deze functie.

## <a name="requirements-and-limitations"></a>Vereisten en beperkingen

U kunt de functie VM starten bij verbinding maken alleen inschakelen voor persoonlijke hostgroepen. Zie [Windows Virtual Desktop Environment](environment-setup.md#host-pools)voor meer informatie over persoonlijke hostgroepen.

De volgende extern bureau blad-clients ondersteunen de functie voor het starten van VM'S op Connect:

- [De webclient](connect-web.md)
- [De Windows-client (versie 1,2748 of hoger)](connect-windows-7-10.md)

U kunt op het [tech Community-Forum](https://aka.ms/wvdtc)controleren op aankondigingen over updates en client ondersteuning.

De Azure Government Cloud biedt momenteel geen ondersteuning voor het starten van VM op Connect.

## <a name="create-a-custom-role-for-start-vm-on-connect"></a>Een aangepaste rol maken voor het starten van een virtuele machine bij het verbinden

Voordat u de functie voor het starten van de VM in Connect kunt configureren, moet u uw virtuele machine toewijzen aan een aangepaste RBAC (op rollen gebaseerd toegangs beheer). Met deze rol kan het virtuele bureau blad van Windows de Vm's in uw abonnement beheren. U kunt deze rol ook gebruiken om Vm's in te scha kelen, hun status te controleren en diagnostische gegevens te rapporteren. Als u meer wilt weten over de werking van elke rol, kunt u een kijkje nemen in [Azure aangepaste rollen](../role-based-access-control/custom-roles.md).

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

Als u de Azure Portal wilt gebruiken om een aangepaste rol toe te wijzen voor de start-VM bij Connect:

1. Open de Azure Portal en ga naar **abonnementen**.

2. Ga naar **toegangs beheer (IAM)** en selecteer **een aangepaste rol toevoegen**.

    > [!div class="mx-imgBorder"]
    > ![Een scherm opname van een vervolg keuzemenu van de knop toevoegen in toegangs beheer (IAM). "Een aangepaste rol toevoegen" is rood gemarkeerd.](media/add-custom-role.png)

3. Geef vervolgens de aangepaste rol een naam en voeg een beschrijving toe. U kunt het beste de naam ' VM starten bij het maken '.

4. Voeg op het tabblad **machtigingen** de volgende machtigingen toe aan het abonnement waaraan u de rol wilt toewijzen: 
 
   - Micro soft. Compute/informatie/start/Action
   - Micro soft. Compute/informatie/lezen

5. Wanneer u klaar bent, selecteert u **OK**.

Daarna moet u de rol toewijzen om toegang te verlenen aan het virtuele bureau blad van Windows.

De aangepaste rol toewijzen:

1. Selecteer op het **tabblad toegangs beheer (IAM)** de optie **roltoewijzingen toevoegen**.

2. Selecteer de rol die u zojuist hebt gemaakt.

3. Voer in de zoek balk **Windows virtueel bureau blad** in en selecteer deze.

      >[!NOTE]
      >U ziet mogelijk twee apps als u virtueel bureau blad van Windows (klassiek) hebt geïmplementeerd. Wijs de rol toe aan beide apps die u ziet.
      >
      > [!div class="mx-imgBorder"]
      > ![Een scherm afbeelding van het tabblad toegangs beheer (IAM). In de zoek balk worden zowel Windows virtueel bureau blad als virtueel bureau blad van Windows (klassiek) rood gemarkeerd.](media/add-role-assignment.png)

### <a name="create-a-custom-role-with-a-json-file-template"></a>Een aangepaste rol maken met een JSON-bestand sjabloon

Als u een JSON-bestand gebruikt om de aangepaste rol te maken, ziet u in het volgende voor beeld een basis sjabloon die u kunt gebruiken. Zorg ervoor dat u de abonnements-ID-waarde vervangt door de abonnements-ID waaraan u de rol wilt toewijzen.

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

## <a name="configure-the-start-vm-on-connect-feature"></a>De functie voor het starten van virtuele machines in Connect configureren

Nu u uw abonnement de rol hebt toegewezen, is het tijd om de functie voor het starten van de VM in Connect te configureren.

### <a name="deployment-considerations"></a>Overwegingen bij de implementatie 

VM starten bij verbinden is een instelling voor een hostgroep. Als u wilt dat een bepaalde groep gebruikers deze functie gebruikt, zorg er dan voor dat u alleen de vereiste rol toewijst aan de gebruikers die u wilt toevoegen.

>[!IMPORTANT]
> U kunt deze functie alleen configureren in bestaande hostgroepen. Deze functie is niet beschikbaar wanneer u een nieuwe hostgroep maakt.

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

Als u de Azure Portal wilt gebruiken om de start-VM te configureren bij verbinding maken:

1. Open uw browser en ga naar [de Azure Portal](https://portal.azure.com/?feature.startVMonConnect=true#home). U wordt aangeraden om de Azure Portal in een InPrivate-venster te openen.

2. Ga in het Azure Portal naar **virtueel bureau blad van Windows**.

3. Selecteer **hostgroepen** en zoek vervolgens de hostgroep die de persoonlijke bureau bladen bevat waaraan u de rol hebt toegewezen.

   >[!NOTE]
   > De hostgroep waarvoor u deze functie configureert, moet beschikken over persoonlijke bureau bladen met directe roltoewijzingen. Als de Bureau bladen in de hostgroep niet correct zijn geconfigureerd, werkt het configuratie proces niet.

4. Selecteer **Eigenschappen** in de hostgroep. Selecteer onder **virtuele machine starten bij verbinding maken** de optie **Ja** en selecteer vervolgens **Opslaan** om de instelling onmiddellijk toe te passen.

    > [!div class="mx-imgBorder"]
    > ![Een scherm opname van de venster Eigenschappen. De optie VM starten bij verbinding maken is rood gemarkeerd.](media/properties-start-vm-on-connect.png)

### <a name="use-powershell"></a>PowerShell gebruiken

Als u deze instelling wilt configureren met Power shell, moet u ervoor zorgen dat u de namen van de resource groep en hostgroepen hebt die u wilt configureren. U moet ook [de Azure PowerShell-module installeren (versie 2.1.0 of hoger)](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.1.0).

Start-VM configureren bij verbinden met behulp van Power shell:

1. Open een Power shell-opdracht venster.

2. Voer de volgende cmdlet uit om start VM in te scha kelen bij verbinden:

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$true
    ```

3. Voer de volgende cmdlet uit om het starten van de virtuele machine op Connect uit te scha kelen:

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$false
    ```

## <a name="user-experience"></a>Gebruikerservaring

In typische sessies is de tijd die een gebruiker nodig heeft om verbinding te maken met een ontoegewezen VM, omdat de VM opnieuw moet worden ingeschakeld, net als bij het inschakelen van een fysieke computer. De Extern bureaublad-client heeft een indicator waarmee de gebruiker weet dat de PC wordt ingeschakeld terwijl er verbinding wordt gemaakt.

## <a name="troubleshooting"></a>Problemen oplossen

Als de functie wordt uitgevoerd op problemen, raden we u aan de Windows- [functie diagnostische gegevens](diagnostics-log-analytics.md) voor virtuele Bureau bladen te gebruiken om te controleren of er problemen zijn. Als er een fout bericht wordt weer gegeven, moet u ervoor zorgen dat u de inhoud van het bericht nauw keurig benadert en de fout naam ergens ter referentie kopieert.

U kunt ook [Azure monitor voor Windows Virtual Desktop](azure-monitor.md) gebruiken om suggesties te krijgen voor het oplossen van problemen.

## <a name="next-steps"></a>Volgende stappen

Als u problemen ondervindt die de documentatie voor probleem oplossing of de functie diagnostische gegevens niet kunnen oplossen, raadpleegt u de [Veelgestelde vragen over het starten van een virtuele machine op Connect](start-virtual-machine-connect-faq.md).