---
title: 'Preview: Een vertrouwde start-VM implementeren'
description: Implementeer een VM die gebruikmaakt van een vertrouwde start.
author: khyewei
ms.author: khwei
ms.reviewer: cynthn
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: how-to
ms.date: 04/06/2021
ms.custom: template-how-to
ms.openlocfilehash: 295579d17f3b24adcf43f6907cc4b1aca01dcae2
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565913"
---
# <a name="deploy-a-vm-with-trusted-launch-enabled-preview"></a>Een VM implementeren met vertrouwde start ingeschakeld (preview)

[Een vertrouwde start](trusted-launch.md) is een manier om de beveiliging van virtuele VM's [van de tweede](generation-2.md) generatie te verbeteren. Vertrouwd starten beschermt tegen geavanceerde en permanente aanvalstechnieken door infrastructuurtechnologieën zoals vTPM en beveiligd opstarten te combineren.

> [!IMPORTANT]
> Vertrouwd starten is momenteel in openbare preview.
> 
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
>
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="deploy-using-the-portal"></a>Implementeren met behulp van de portal

Maak een virtuele machine met vertrouwde start ingeschakeld.

1. Meld u aan bij de Azure [Portal](https://aka.ms/TL_preview).
   > [!NOTE] 
   > De portalkoppeling is uniek voor de preview van een vertrouwde start.
   >  
2. Zoek naar **Virtual Machines**.
3. Selecteer **virtuele machines** onder **Services**.
4. Selecteer op **de pagina Virtuele machines** de optie **Toevoegen** en selecteer vervolgens **Virtuele machine.**
5. Zorg **ervoor dat onder Projectdetails** het juiste abonnement is geselecteerd.
6. Selecteer **onder Resourcegroep** de optie **Nieuwe maken** en typ een naam voor de resourcegroep of selecteer een bestaande resourcegroep in de vervolgkeuzekeuze.
7. Typ **onder Exemplaardetails** een naam voor de naam van de virtuele machine en kies een regio die ondersteuning biedt [voor vertrouwd starten.](trusted-launch.md#public-preview-limitations)
8. Selecteer **onder Afbeelding** een Gen 2-afbeelding die ondersteuning biedt voor vertrouwd [starten.](trusted-launch.md#public-preview-limitations) Zorg ervoor dat u het volgende bericht ziet: **Deze afbeelding biedt ondersteuning voor de preview van een vertrouwde start. Configureer op het tabblad Geavanceerd.**
   > [!TIP]
   > Als u de Versie gen 2 van de want-afbeelding  niet ziet in de vervolgkeuzekeuze, selecteert u Alle afbeeldingen bekijken en wijzigt u vervolgens het filter **Generatie van VM's** om alleen Gen 2-afbeeldingen weer te geven. Zoek de afbeelding in de lijst en gebruik vervolgens de **vervolgkeuzelijst** Selecteren om de versie Gen 2 te selecteren.

    :::image type="content" source="media/trusted-launch/gen-2-image.png" alt-text="Schermopname van het bericht waarin wordt bevestigd dat dit een Gen2-afbeelding is die ondersteuning biedt voor een vertrouwde start.":::

13. Selecteer een VM-grootte die ondersteuning biedt voor vertrouwd starten. Zie de lijst met [ondersteunde grootten](trusted-launch.md#public-preview-limitations).
14. Vul de gegevens van **het beheerdersaccount** in en vervolgens **Regels voor binnenkomende poort.** 
1. Schakel over naar **het tabblad** Geavanceerd door het bovenaan de pagina te selecteren.
1. Schuif omlaag naar de **sectie VM-generatie.** Zorg ervoor **dat Gen 2** is geselecteerd.
1. Schuif op het **tabblad Geavanceerd** omlaag naar Vertrouwd **starten** en schakel vervolgens het selectievakje **Vertrouwd** starten in. Hierdoor worden er nog twee opties weergegeven: Beveiligd opstarten en vTPM. Selecteer de juiste opties voor uw implementatie.

    :::image type="content" source="media/trusted-launch/trusted-launch-portal.png" alt-text="Schermopname met de opties voor het starten met vertrouwde vertrouwensmogelijkheden.":::

15. Selecteer onder aan de pagina Beoordelen **en maken**
16. Op de **pagina Een virtuele machine** maken ziet u de details van de virtuele machine die u gaat implementeren. Wanneer u klaar bent, selecteert u **Maken**.

    :::image type="content" source="media/trusted-launch/validation.png" alt-text="Eenceenshot van de validatiepagina, waarin de opties voor het starten met vertrouwen zijn opgenomen.":::


Het duurt een paar minuten voor uw virtuele machine is geïmplementeerd. 

## <a name="deploy-using-a-template"></a>Implementeren met behulp van een sjabloon

U kunt vertrouwde start-VM's implementeren met behulp van een quickstart-sjabloon:

**Linux**:    
[![Implementeren in Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2FcreateUiDefinition.json)

**Windows**:    
[![Implementeren in Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2FcreateUiDefinition.json)

## <a name="view-and-update"></a>Weergeven en bijwerken

U kunt de configuratie van de vertrouwde start voor een bestaande VM bekijken door naar **de** pagina Overzicht voor de VM in de portal te gaan.

Als u de configuratie voor vertrouwd starten wilt wijzigen, selecteert u configuratie **in** het linkermenu onder de **sectie** Instellingen. U kunt Beveiligd opstarten en vTPM in- of uitschakelen in de **sectie Vertrouwd** starten. Selecteer **Opslaan** bovenaan de pagina wanneer u klaar bent. 

:::image type="content" source="media/trusted-launch/configuration.png" alt-text="Schermopname van het wijzigen van de configuratie voor vertrouwde start.":::

Als de VM wordt uitgevoerd, ontvangt u een bericht dat de VM opnieuw wordt opgestart om de gewijzigde vertrouwde startconfiguratie toe te passen. Selecteer **Ja** en wacht tot de VM opnieuw is opgestart voordat de wijzigingen van kracht worden.


## <a name="verify-secure-boot-and-vtpm"></a>Beveiligd opstarten en vTPM controleren

U kunt controleren of beveiligd opstarten en vTPM zijn ingeschakeld op de virtuele machine.
    
### <a name="linux-validate-if-secure-boot-is-running"></a>Linux: valideren of beveiligd opstarten wordt uitgevoerd

Ga met SSH naar de VM en voer de volgende opdracht uit: 

```bash
mokutil --sb-state
```

Als beveiligd opstarten is ingeschakeld, wordt met de opdracht het volgende retourneren:
 
```bash
SecureBoot enabled 
```

### <a name="linux-validate-if-vtpm-is-enabled"></a>Linux: valideren of vTPM is ingeschakeld

SSH in uw VM. Controleer of het TPM0-apparaat aanwezig is: 

```bash
ls /dev/tpm0
```

Als vTPM is ingeschakeld, retourneerde de opdracht:

```output
/dev/tpm0
```

Als vTPM is uitgeschakeld, wordt met de opdracht het volgende retourneren:

```output
ls: cannot access '/dev/tpm0': No such file or directory
```

### <a name="windows-validate-that-secure-boot-is-running"></a>Windows: controleren of beveiligd opstarten wordt uitgevoerd

Maak verbinding met de VM via extern bureaublad en voer vervolgens `msinfo32.exe` uit.

Controleer in het rechterdeelvenster of de status beveiligd opstarten AAN **is.**

## <a name="enable-the-azure-security-center-experience"></a>De Azure Security Center inschakelen

Als u Azure Security Center over uw vertrouwde start-VM's wilt weergeven, moet u verschillende beleidsregels inschakelen. De eenvoudigste manier om het beleid in te stellen, is door deze sjabloon Resource Manager [uw](https://github.com/prash200/azure-quickstart-templates/tree/master/101-asc-trustedlaunch-policies) abonnement te implementeren. 

Selecteer de onderstaande knop om het beleid te implementeren in uw abonnement:

[![Implementeren in Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-asc-trustedlaunch-policies%2Fazuredeploy.json)

De sjabloon hoeft slechts één keer per abonnement te worden geïmplementeerd. Het installeert en extensies `GuestAttestation` automatisch `AzureSecurity` op alle ondersteunde VM's. Als er fouten optreden, probeert u de sjabloon opnieuw teployeren.

Zie Een aangepast initiatief toevoegen aan uw abonnement voor aanbevelingen voor vTPM en beveiligd opstarten voor vertrouwde [start-VM's.](../security-center/custom-security-policies.md#to-add-a-custom-initiative-to-your-subscription)
 
## <a name="sign-things-for-secure-boot-on-linux"></a>Sign things for Secure Boot on Linux

In sommige gevallen moet u mogelijk dingen ondertekenen voor beveiligd opstarten met UEFI.  Mogelijk moet u bijvoorbeeld How [to sign things for Secure Boot](https://ubuntu.com/blog/how-to-sign-things-for-secure-boot) for Ubuntu (Dingen ondertekenen voor Beveiligd opstarten voor Ubuntu) door nemen. In dergelijke gevallen moet u het MOK-hulpprogramma invoeren om sleutels voor uw VM in te schrijven. Hiervoor moet u de seriële console van Azure gebruiken om toegang te krijgen tot het MOK-hulpprogramma.

1. Schakel De seriële console van Azure voor Linux in. Zie Serial [Console for Linux (Seriële console voor Linux) voor meer informatie.](/troubleshoot/azure/virtual-machines/serial-console-linux)
1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zoek naar **Virtuele machines** en selecteer uw virtuele machine in de lijst.
1. Selecteer in het menu links onder **Ondersteuning en probleemoplossing** **de optie Seriële console.** Aan de rechterkant wordt een pagina geopend met de seriële console.
1. Meld u aan bij de VM met behulp van de seriële console van Azure. Voer **voor aanmelding** de gebruikersnaam in die u hebt gebruikt bij het maken van de VM. Bijvoorbeeld *azureuser*. Wanneer u hier om wordt gevraagd, voert u het wachtwoord in dat is gekoppeld aan de gebruikersnaam.
1. Nadat u bent aangemeld, gebruikt u om `mokutil` het bestand met de openbare sleutel te `.der` importeren.

    ```bash
    sudo mokutil –import <path to public key.der> 
    ```
1. Start de machine opnieuw op vanuit de seriële console van Azure door te `sudo reboot` typen. Er wordt een aftelling van 10 seconden begonnen.
1. Druk op de toets omhoog of omlaag om de aftelling te onderbreken en te wachten in de UEFI-consolemodus. Als de timer niet wordt onderbroken, wordt het opstartproces voortgezet en gaan alle MOK-wijzigingen verloren.
1. Selecteer de juiste actie in het menu van het MOK-hulpprogramma.

    :::image type="content" source="media/trusted-launch/mok-mangement.png" alt-text="Schermopname van de beschikbare opties in het MOK-beheermenu in de seriële console.":::


## <a name="next-steps"></a>Volgende stappen

Meer informatie over [vertrouwde start-](trusted-launch.md) en [generatie 2-VM's.](generation-2.md)
