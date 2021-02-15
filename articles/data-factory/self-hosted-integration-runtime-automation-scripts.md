---
title: Zelf-hostende Integration runtime-installatie automatiseren met lokale Power shell-scripts
description: De installatie van zelf-hostende Integration Runtime op lokale computers automatiseren.
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: seo-lt-2019
ms.date: 05/09/2020
ms.openlocfilehash: 8cbe54a23cb1c8b55afd86a18b51c0e392c3f78a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376204"
---
# <a name="automating-self-hosted-integration-runtime-installation-using-local-powershell-scripts"></a>Zelf-hostende Integration runtime-installatie automatiseren met lokale Power shell-scripts
Als u de installatie van zelf-hostende Integration Runtime op lokale computers (met uitzonde ring van virtuele Azure-machines waar we in plaats daarvan gebruikmaken van het Resource Manager-sjabloon) wilt automatiseren, kunt u lokale Power shell-scripts gebruiken. In dit artikel vindt u een inleiding tot twee scripts die u kunt gebruiken.

## <a name="prerequisites"></a>Vereisten

* Start Power shell op uw lokale machine. Als u de scripts wilt uitvoeren, moet u **uitvoeren als Administrator** kiezen.
* [Down load](https://www.microsoft.com/download/details.aspx?id=39717) de zelf-hostende Integration runtime-software. Kopieer het pad naar het gedownloade bestand. 
* U hebt ook een **verificatie sleutel** nodig om de zelf-hostende Integration runtime te registreren.
* Voor het automatiseren van hand matige updates moet u een vooraf geconfigureerde, zelf-hostende Integration runtime hebben.

## <a name="scripts-introduction"></a>Scripts Inleiding 

> [!NOTE]
> Deze scripts worden gemaakt met behulp van het [gedocumenteerde opdracht regel programma](./create-self-hosted-integration-runtime.md#set-up-an-existing-self-hosted-ir-via-local-powershell) in de zelf-hostende Integration runtime. Zo nodig kan een van deze scripts worden aangepast aan de behoeften van hun automatisering.
> De scripts moeten worden toegepast per knoop punt. Zorg er dus voor dat u deze uitvoert op alle knoop punten in het geval van een hoge Beschik baarheid instellen (2 of meer knoop punten).

* Voor het automatiseren van Setup: Installeer en Registreer een nieuwe zelf-hostende Integration runtime-knoop punt met **[InstallGatewayOnLocalMachine.ps1](https://github.com/nabhishek/SelfHosted-IntegrationRuntime_AutomationScripts/blob/master/InstallGatewayOnLocalMachine.ps1)** -het script kan worden gebruikt om het zelf-hostende Integration runtime-knoop punt te installeren en te registreren met een verificatie sleutel. Het script accepteert twee argumenten, waarbij u **eerst** de locatie opgeeft van de [zelf-hostende Integration runtime](https://www.microsoft.com/download/details.aspx?id=39717) op een lokale **schijf, waarbij** u de **verificatie sleutel** opgeeft (voor het registreren van zelf-hostende IR-knoop punten).

* Voor het automatiseren van hand matige updates: werk het zelf-hostende IR-knoop punt bij met een specifieke versie of naar de meest recente versie **[script-update-gateway.ps1](https://github.com/nabhishek/SelfHosted-IntegrationRuntime_AutomationScripts/blob/master/script-update-gateway.ps1)** . dit wordt ook ondersteund als u de automatische update hebt uitgeschakeld of als u meer controle wilt hebben over updates. Het script kan worden gebruikt om het zelf-hostende Integration runtime-knoop punt bij te werken naar de meest recente versie of naar een opgegeven hogere versie (downgrade werkt niet). Er wordt een argument geaccepteerd voor het opgeven van het versie nummer (voor beeld:-versie 3.13.6942.1). Als er geen versie is opgegeven, wordt de zelf-hostende IR altijd bijgewerkt naar de meest recente versie die in de [down loads](https://www.microsoft.com/download/details.aspx?id=39717)is gevonden.
    > [!NOTE]
    > Alleen de laatste 3 versies kunnen worden opgegeven. In het ideale geval wordt dit gebruikt om een bestaand knoop punt bij te werken naar de meest recente versie. **hierbij wordt ervan uitgegaan dat u een geregistreerde zelf-hosted IR hebt**. 

## <a name="usage-examples"></a>Gebruiksvoorbeelden

### <a name="for-automating-setup"></a>Voor het automatiseren van Setup
1. Down load [hier](https://www.microsoft.com/download/details.aspx?id=39717)de zelf-hostende IR. 
1. Geef het pad op waar de hierboven gedownloade SHIR MSI (installatie bestand) is. Als het pad bijvoorbeeld *C:\Users\username\Downloads\IntegrationRuntime_4.7.7368.1.msi* is, kunt u het onderstaande voor beeld van een Power shell-opdracht regel gebruiken voor deze taak:

   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\InstallGatewayOnLocalMachine.ps1 -path "C:\Users\username\Downloads\IntegrationRuntime_4.7.7368.1.msi" -authKey "[key]"
   ```

    > [!NOTE]
    > Vervang [sleutel] door de verificatie sleutel om uw IR te registreren.
    > Vervang "username" door de gebruikers naam.
    > Geef de locatie van het bestand ' InstallGatewayOnLocalMachine.ps1 ' op wanneer het script wordt uitgevoerd. In dit voor beeld hebben we het opgeslagen op het bureau blad.

1. Als er één vooraf geïnstalleerde zelf-hostende IR op uw computer is geïnstalleerd, wordt deze door het script automatisch verwijderd en wordt er een nieuwe geconfigureerd. U ziet het volgende venster uit: ![ Integration runtime configureren](media/self-hosted-integration-runtime-automation-scripts/integration-runtime-configure.png)

1. Wanneer de installatie en de sleutel registratie is voltooid, ziet u *dat u de gateway kunt installeren* en de *Gateway resultaten kunt registreren* in uw lokale Power shell.
        [![resultaat van script 1-uitvoering](media/self-hosted-integration-runtime-automation-scripts/script-1-run-result.png)](media/self-hosted-integration-runtime-automation-scripts/script-1-run-result.png#lightbox)

### <a name="for-automating-manual-updates"></a>Voor het automatiseren van hand matige updates
Dit script wordt gebruikt om de nieuwste zelf-hostende Integration runtime bij te werken/te installeren en te registreren. De uitvoering van het script voert de volgende stappen uit:
1. Huidige zelf-hostende IR-versie controleren
2. Nieuwste versie of opgegeven versie van argument ophalen
3. Als er een nieuwere versie is dan de huidige versie:
    * zelf-hostende IR MSI downloaden
    * upgrade uitvoeren

Hieronder volgt een voor beeld van de volgende opdracht regel om dit script te kunnen gebruiken:
* Nieuwste gateway downloaden en installeren:

   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\script-update-gateway.ps1
   ```    
* Gateway van de opgegeven versie downloaden en installeren:
   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\script-update-gateway.ps1 -version 3.13.6942.1
   ``` 
   Als uw huidige versie al het meest recent is, ziet u het volgende resultaat, waardoor het Voorst Ellen dat er geen update is vereist.   
    [![script 2 resultaat uitvoeren](media/self-hosted-integration-runtime-automation-scripts/script-2-run-result.png)](media/self-hosted-integration-runtime-automation-scripts/script-2-run-result.png#lightbox)