---
title: 'Voor beeld: een vertrouwde start-VM implementeren'
description: Implementeer een virtuele machine die gebruikmaakt van vertrouwde start.
author: khyewei
ms.author: khwei
ms.reviewer: cynthn
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: template-how-to
ms.openlocfilehash: dec9c7581bbcf55196b04e0a76e9e61f81a27244
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104582065"
---
# <a name="deploy-a-vm-with-trusted-launch-enabled-preview"></a>Een VM implementeren met behulp van vertrouwde start ingeschakeld (preview-versie)

[Trusted Launch](trusted-launch.md) is een manier om de beveiliging van virtuele machines van de [tweede generatie](generation-2.md) te verbeteren. Trusted Launch beschermt tegen geavanceerde en permanente aanvals technieken door infrastructuur technologieën zoals vTPM en Secure boot te combi neren.

> [!IMPORTANT]
> Vertrouwde start is momenteel beschikbaar als open bare preview.
> 
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
>
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="deploy-using-the-portal"></a>Implementeren met behulp van de portal

Een virtuele machine maken waarvoor vertrouwde start is ingeschakeld.

1. Meld u aan bij de Azure [Portal](https://aka.ms/TL_preview).
1. Zoeken naar **virtual machines**.
1. Selecteer **virtuele machines** onder **Services**.
1. Selecteer op de pagina **virtuele machines** de optie **toevoegen** en selecteer vervolgens **virtuele machine**.
1. Controleer onder **Project Details** of het juiste abonnement is geselecteerd.
1. Selecteer onder **resource groep** de optie **nieuwe maken** en typ een naam voor de resource groep of selecteer een bestaande resource groep in de vervolg keuzelijst.
1. Typ onder **Details van exemplaar** een naam voor de naam van de virtuele machine en kies een regio die ondersteuning biedt voor [vertrouwde start](trusted-launch.md#public-preview-limitations).
1. Selecteer onder **afbeelding** een [installatie kopie die ondersteuning biedt voor vertrouwde start](trusted-launch.md#public-preview-limitations). Mogelijk ziet u alleen de versie 1 van de installatie kopie, die u in orde hebt, gaat u verder met de volgende stap.
1. Schakel over naar het tabblad **Geavanceerd** door het boven aan de pagina te selecteren.
1. Schuif omlaag naar de sectie voor het genereren van de **virtuele machine** en selecteer vervolgens **gen 2**.
1. Terwijl u nog steeds op het tabblad **Geavanceerd** , schuift u omlaag naar **vertrouwd starten** en selecteert u vervolgens het selectie vakje **vertrouwd starten** . Zo worden er nog twee opties weer gegeven: beveiligd opstarten en vTPM. Selecteer de juiste opties voor uw implementatie.

    :::image type="content" source="media/trusted-launch/trusted-launch-portal.png" alt-text="Scherm afbeelding met de opties voor vertrouwde start.":::

1. Ga terug naar het tabblad **basis principes** , onder **afbeelding**, en zorg ervoor dat het volgende bericht wordt weer gegeven: **deze afbeelding ondersteunt de preview van vertrouwde start. Configureer op het tabblad Geavanceerd**. U moet nu de installatie kopie van generatie 2 selecteren.

    :::image type="content" source="media/trusted-launch/gen-2-image.png" alt-text="Scherm afbeelding met het bericht dat wordt bevestigd dat dit een Gen2-installatie kopie is die een vertrouwde start ondersteunt.":::

1.  Selecteer een VM-grootte die een vertrouwde start ondersteunt. Zie de lijst met [ondersteunde grootten](trusted-launch.md#public-preview-limitations).
1.  Vul de gegevens van het **Administrator-account** en vervolgens de **Binnenkomende poort regels** in.
1.  Selecteer onder aan de pagina de optie **bekijken + maken**
1.  Op de pagina **een virtuele machine maken** ziet u de details van de VM die u gaat implementeren. Wanneer u klaar bent, selecteert u **Maken**.

    :::image type="content" source="media/trusted-launch/validation.png" alt-text="De Sceenshot van de validatie pagina, met daarin de opties voor het betrouw bare starten, zijn opgenomen.":::


Het duurt een paar minuten voor uw virtuele machine is geïmplementeerd. 

## <a name="deploy-using-a-template"></a>Implementeren met behulp van een sjabloon

U kunt vertrouwde Launch Vm's implementeren met behulp van een Quick Start-sjabloon:

**Linux**:    
[![Implementeren in Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2FcreateUiDefinition.json)

**Windows**:    
[![Implementeren in Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2FcreateUiDefinition.json)

## <a name="view-and-update"></a>Weer geven en bijwerken

U kunt de betrouw bare start configuratie voor een bestaande virtuele machine bekijken door de pagina **overzicht** voor de virtuele machine in de portal te bezoeken.

Als u de configuratie van een vertrouwde start wilt wijzigen, selecteert u in het menu links **configuratie** onder de sectie **instellingen** . U kunt beveiligd opstarten en vTPM in-of uitschakelen in het gedeelte **vertrouwde starten** . Selecteer boven aan de pagina **Opslaan** wanneer u klaar bent. 

:::image type="content" source="media/trusted-launch/configuration.png" alt-text="Scherm afbeelding van het wijzigen van de configuratie van een vertrouwde start.":::

Als de virtuele machine wordt uitgevoerd, wordt er een bericht weer gegeven dat de VM opnieuw wordt opgestart om de gewijzigde vertrouwde start configuratie toe te passen. Selecteer **Ja** en wacht totdat de virtuele machine opnieuw wordt opgestart voordat de wijzigingen van kracht worden.


## <a name="verify-secure-boot-and-vtpm"></a>Beveiligd opstarten en vTPM controleren

U kunt valideren dat beveiligd opstarten en vTPM zijn ingeschakeld op de virtuele machine.
    
### <a name="linux-validate-if-secure-boot-is-running"></a>Linux: valideren als beveiligd opstarten wordt uitgevoerd

SSH naar de virtuele machine en voer de volgende opdracht uit: 

```bash
mokutil --sb-state
```

Als beveiligd opstarten is ingeschakeld, wordt de volgende opdracht weer gegeven:
 
```bash
SecureBoot enabled 
```

### <a name="linux-validate-if-vtpm-is-enabled"></a>Linux: valideren of vTPM is ingeschakeld

SSH in uw VM. Controleren of het tpm0-apparaat aanwezig is: 

```bash
ls /dev/tpm0
```

Als vTPM is ingeschakeld, wordt de opdracht geretourneerd:

```output
/dev/tpm0
```

Als vTPM is uitgeschakeld, wordt de volgende opdracht weer gegeven:

```output
ls: cannot access '/dev/tpm0': No such file or directory
```

### <a name="windows-validate-that-secure-boot-is-running"></a>Windows: valideren dat beveiligd opstarten wordt uitgevoerd

Maak verbinding met de virtuele machine met behulp van extern bureau blad en voer uit `msinfo32.exe` .

Controleer in het rechterdeel venster of de modus voor beveiligd opstarten is **ingeschakeld**.

## <a name="enable-the-azure-security-center-experience"></a>De Azure Security Center-ervaring inschakelen

Als Azure Security Center informatie over uw vertrouwde start-Vm's wilt weer geven, moet u verschillende beleids regels inschakelen. De eenvoudigste manier om het beleid in te scha kelen, is door deze [Resource Manager-sjabloon](https://github.com/prash200/azure-quickstart-templates/tree/master/101-asc-trustedlaunch-policies) te implementeren in uw abonnement. 

Selecteer de onderstaande knop om het beleid te implementeren voor uw abonnement:

[![Implementeren in Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-asc-trustedlaunch-policies%2Fazuredeploy.json)

De sjabloon moet slechts één keer per abonnement worden geïmplementeerd. De service installeert `GuestAttestation` en `AzureSecurity` uitbrei dingen automatisch op alle ondersteunde vm's. Probeer de sjabloon opnieuw te implementeren als u fouten krijgt.

Zie [een aangepast initiatief toevoegen aan uw abonnement](../security-center/custom-security-policies.md#to-add-a-custom-initiative-to-your-subscription)om aanbevelingen voor vTPM en beveiligd opstarten op te halen voor vertrouwde vm's.
 
## <a name="sign-things-for-secure-boot-on-linux"></a>Onderteken dingen voor beveiligd opstarten op Linux

In sommige gevallen moet u mogelijk dingen ondertekenen voor UEFI Secure boot.  U moet bijvoorbeeld de [procedure volgen voor het afwijzen van beveiligd opstarten](https://ubuntu.com/blog/how-to-sign-things-for-secure-boot) voor Ubuntu. In dergelijke gevallen moet u de sleutels voor het registreren van het MOK-hulp programma voor uw virtuele machine invoeren. Hiervoor moet u de Azure Serial console gebruiken om toegang te krijgen tot het MOK-hulp programma.

1. Schakel Azure Serial console in voor Linux. Zie voor meer informatie [seriële console voor Linux](/troubleshoot/azure/virtual-machines/serial-console-linux).
1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zoek naar **virtuele machines** en selecteer uw virtuele machine in de lijst.
1. Selecteer **seriële console** in het linkermenu onder **ondersteuning en probleem oplossing**. Er wordt een pagina aan de rechter kant geopend, met de seriële console.
1. Meld u aan bij de VM met behulp van de Azure Serial console. Voer bij **aanmelding** de gebruikers naam in die u hebt gebruikt bij het maken van de virtuele machine. Bijvoorbeeld *azureuser*. Wanneer u hierom wordt gevraagd, voert u het wacht woord in dat is gekoppeld aan de gebruikers naam.
1. Wanneer u bent aangemeld, gebruikt u `mokutil` om het bestand met de open bare sleutel te importeren `.der` .

    ```bash
    sudo mokutil –import <path to public key.der> 
    ```
1. Start de machine opnieuw op vanuit Azure Serial console door te typen `sudo reboot` . Er wordt een aftelling van 10 seconden gestart.
1. Druk op de toets omhoog of omlaag om de aftelling te onderbreken en te wachten in de UEFI-console modus. Als de timer niet wordt onderbroken, wordt het opstart proces voortgezet en gaan alle wijzigingen in de MOK verloren.
1. Selecteer de gewenste actie in het menu van het MOK-hulp programma.

    :::image type="content" source="media/trusted-launch/mok-mangement.png" alt-text="Scherm opname van de beschik bare opties in het menu MOK beheer van de seriële console.":::


## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [betrouw bare](trusted-launch.md) vm's voor starten en [generatie 2](generation-2.md) .