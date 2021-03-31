---
title: Toepassings consistente back-ups van virtuele Linux-machines
description: Maak toepassings consistente back-ups van uw virtuele Linux-machines naar Azure. In dit artikel wordt uitgelegd hoe u het script Framework kunt configureren voor het maken van back-ups van door Azure geïmplementeerde Linux-Vm's Dit artikel bevat ook informatie over het oplossen van problemen.
ms.topic: conceptual
ms.date: 01/12/2018
ms.openlocfilehash: 22053004026a2dd8976027359f11d50a5663b334
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88999237"
---
# <a name="application-consistent-backup-of-azure-linux-vms"></a>Toepassingsconsistente back-up van Azure Linux-VM’s

Bij het maken van back-upmomentopnamen van uw Vm's betekent toepassings consistentie dat uw toepassingen worden gestart wanneer de Vm's worden opgestart nadat deze zijn hersteld. Zo kunt u zich Voorst Ellen dat de consistentie van toepassingen zeer belang rijk is. Om ervoor te zorgen dat uw Linux-Vm's toepassings consistent zijn, kunt u het Linux pre-script en post-script-Framework gebruiken om toepassings consistente back-ups te maken. Het Framework pre-script en post-script ondersteunt door Azure Resource Manager geïmplementeerde virtuele Linux-machines. Scripts voor toepassings consistentie bieden geen ondersteuning voor door Service Manager geïmplementeerde virtuele machines of virtuele Windows-machines.

## <a name="how-the-framework-works"></a>Hoe het Framework werkt

Het Framework biedt een optie voor het uitvoeren van aangepaste pre-scripts en post scripts tijdens het maken van VM-moment opnamen. Pre-scripts worden uitgevoerd voordat u de moment opname van de virtuele machine maakt en post scripts worden onmiddellijk nadat u de moment opname van de virtuele machine hebt gemaakt, uitgevoerd. Scripts en post scripts bieden de flexibiliteit om uw toepassing en omgeving te beheren, terwijl u moment opnamen van virtuele machines maakt.

Pre-scripts aanroepen systeem eigen toepassings-Api's, die de IOs stil leggen en in-Memory-inhoud naar de schijf legen. Deze acties zorgen ervoor dat de moment opname toepassings consistent is. Post-scripts gebruiken systeem eigen toepassings-Api's voor het ontdooien van het IOs, waardoor de toepassing normale bewerkingen kan hervatten na de moment opname van de virtuele machine.

## <a name="steps-to-configure-pre-script-and-post-script"></a>Stappen voor het configureren van pre-script en post-script

1. Meld u aan als hoofd gebruiker voor de virtuele Linux-machine waarvan u een back-up wilt maken.

2. In [github](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig)downloadt u **VMSnapshotScriptPluginConfig.js** en kopieert u deze naar de map **/etc/Azure** voor alle vm's waarvan u een back-up wilt maken. Als de map **/etc/Azure** niet bestaat, maakt u deze.

3. Kopieer het pre-script en post-script voor uw toepassing op alle Vm's waarvan u een back-up wilt maken. U kunt de scripts kopiëren naar elke locatie op de virtuele machine. Zorg ervoor dat u het volledige pad van de script bestanden in de **VMSnapshotScriptPluginConfig.js** in het bestand bijwerkt.

4. Zorg ervoor dat u de volgende machtigingen voor deze bestanden hebt:

   - **VMSnapshotScriptPluginConfig.jsop**: machtiging ' 600 '. Alleen ' root '-gebruikers moeten bijvoorbeeld ' Lees-en schrijf machtigingen ' hebben voor dit bestand en geen enkele gebruiker machtigingen voor ' uitvoeren ' moet hebben.

   - **Pre-script bestand**: machtiging "700".  Alleen de ' root '-gebruiker moet bijvoorbeeld de machtigingen lezen, schrijven en uitvoeren hebben voor dit bestand.

   - **Post-script** Machtiging ' 700 '. Alleen de ' root '-gebruiker moet bijvoorbeeld de machtigingen lezen, schrijven en uitvoeren hebben voor dit bestand.

   > [!IMPORTANT]
   > Het-Framework geeft gebruikers veel stroom. Beveilig het Framework en zorg ervoor dat alleen de hoofd gebruiker toegang heeft tot essentiële JSON-en script bestanden.
   > Als niet aan de vereisten wordt voldaan, wordt het script niet uitgevoerd, wat resulteert in een crash van het bestands systeem en inconsistente back-up.
   >

5. Configureer **VMSnapshotScriptPluginConfig.jsop** zoals hier wordt beschreven:
    - **invoegtoepassing**: verlaat dit veld niet, of uw scripts werken mogelijk niet zoals verwacht.

    - **preScriptLocation**: Geef het volledige pad op van het pre-script op de virtuele machine waarvan u een back-up wilt maken.

    - **postScriptLocation**: Geef het volledige pad op van het post script op de virtuele machine waarvan u een back-up wilt maken.

    - **preScriptParams**: Geef de optionele para meters op die moeten worden door gegeven aan het pre-script. Alle para meters moeten tussen aanhalings tekens staan. Als u meerdere para meters gebruikt, scheidt u de para meters met een komma.

    - **postScriptParams**: Geef de optionele para meters op die moeten worden door gegeven aan het post script. Alle para meters moeten tussen aanhalings tekens staan. Als u meerdere para meters gebruikt, scheidt u de para meters met een komma.

    - **preScriptNoOfRetries**: Stel het aantal keren in dat het pre-script opnieuw moet worden uitgevoerd als er een fout is opgetreden voordat wordt beëindigd. Bij nul wordt slechts één poging geprobeerd en niet opnieuw proberen als er een fout optreedt.

    - **postScriptNoOfRetries**: Stel het aantal keren in dat het post script moet worden herhaald als er een fout is opgetreden voordat het proces wordt afgebroken. Bij nul wordt slechts één poging geprobeerd en niet opnieuw proberen als er een fout optreedt.

    - **timeoutInSeconds**: Geef afzonderlijke time-outs op voor het pre-script en het post-script (maximum waarde kan 1800 zijn).

    - **continueBackupOnFailure**: Stel deze waarde in op **True** als u wilt dat Azure backup terugvallen op een consistente en crash consistente back-up van een bestands systeem als het pre-script of het post script mislukt. Als u deze optie instelt op **False** , mislukt de back-up als er een script fout optreedt (behalve wanneer u een virtuele machine met één schijf hebt die terugvalt op crash consistente back-up, ongeacht deze instelling). Wanneer de **continueBackupOnFailure** -waarde is ingesteld op False en de back-up mislukt, wordt de back-upbewerking opnieuw geprobeerd op basis van een nieuwe logica in de service (voor het opgegeven aantal pogingen).

    - **fsFreezeEnabled**: Geef op of Linux fsfreeze moet worden aangeroepen tijdens het maken van de VM-moment opname om consistentie van het bestands systeem te garanderen. U wordt aangeraden deze instelling in te stellen op **waar** , tenzij uw toepassing afhankelijk is van het uitschakelen van fsfreeze.

    - **ScriptsExecutionPollTimeSeconds**: Stel de tijd in die de uitbrei ding moet overzetten tussen elke polling en de uitvoering van het script. Als de waarde bijvoorbeeld 2 is, controleert de uitbrei ding of de uitvoering van het script elke 2 seconden is voltooid. De minimum-en maximum waarde die het kan duren, zijn respectievelijk 1 en 5. De waarde moet strikt een geheel getal zijn.

6. Het script Framework is nu geconfigureerd. Als de back-up van de virtuele machine al is geconfigureerd, roept de volgende back-up de scripts aan en wordt de toepassings consistente back-up geactiveerd. Als de back-up van de virtuele machine niet is geconfigureerd, configureert u deze met behulp van [back-ups van virtuele Azure-machines naar Recovery Services-kluizen.](./backup-azure-vms-first-look-arm.md)

## <a name="troubleshooting"></a>Problemen oplossen

Zorg ervoor dat u de juiste logboek registratie toevoegt tijdens het schrijven van uw pre-script en post-script en controleer uw script Logboeken om eventuele script problemen op te lossen. Als er nog steeds problemen zijn met het uitvoeren van scripts, raadpleegt u de volgende tabel voor meer informatie.

| Fout | Foutbericht | Aanbevolen actie |
| ------------------------ | -------------- | ------------------ |
| Pre-ScriptExecutionFailed |Het pre-script heeft een fout geretourneerd, waardoor de back-up mogelijk niet toepassings consistent is.| Bekijk de fout logboeken voor het script om het probleem op te lossen.|  
|Post-ScriptExecutionFailed |Het post script retourneert een fout die van invloed kan zijn op de toepassings status. |Bekijk de fouten logboeken voor het script om het probleem op te lossen en controleer de toepassings status. |
| Pre-ScriptNotFound |Het pre-script is niet gevonden op de locatie die is opgegeven in het **VMSnapshotScriptPluginConfig.js** in het configuratie bestand. |Zorg ervoor dat het pre-script aanwezig is in het pad dat is opgegeven in het configuratie bestand om te zorgen voor toepassings consistente back-up.|
| Post-ScriptNotFound |Het post script is niet gevonden op de locatie die is opgegeven in het **VMSnapshotScriptPluginConfig.jsin** het configuratie bestand. |Zorg ervoor dat het post script aanwezig is op het pad dat is opgegeven in het configuratie bestand om te zorgen voor toepassings consistente back-up.|
| IncorrectPluginhostFile |Het **Pluginhost** -bestand, dat wordt meegeleverd met de uitbrei ding VmSnapshotLinux, is beschadigd, dus pre-script en post-script kan niet worden uitgevoerd en de back-up is niet toepassings consistent.| Verwijder de **VmSnapshotLinux** -extensie en wordt deze automatisch opnieuw geïnstalleerd met de volgende back-up om het probleem op te lossen. |
| IncorrectJSONConfigFile | De **VMSnapshotScriptPluginConfig.jsin** het bestand is onjuist, dus het script kan niet worden uitgevoerd en het script is niet consistent. | Down load het exemplaar van [github](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) en configureer het opnieuw. |
| InsufficientPermissionforPre-Script | Voor het uitvoeren van scripts moet de hoofd gebruiker de eigenaar van het bestand zijn en moet het bestand ' 700 ' machtigingen hebben (dat wil zeggen dat alleen ' eigenaar ' de machtigingen lezen, schrijven en uitvoeren heeft). | Zorg ervoor dat ' root ' gebruiker de ' eigenaar ' van het script bestand is en dat alleen ' eigenaar ' de machtigingen lezen, schrijven en uitvoeren heeft. |
| InsufficientPermissionforPost-Script | Voor het uitvoeren van scripts moet de hoofd gebruiker de eigenaar van het bestand zijn en moet het bestand ' 700 ' machtigingen hebben (dat wil zeggen dat alleen ' eigenaar ' de machtigingen lezen, schrijven en uitvoeren heeft). | Zorg ervoor dat ' root ' gebruiker de ' eigenaar ' van het script bestand is en dat alleen ' eigenaar ' de machtigingen lezen, schrijven en uitvoeren heeft. |
| Pre-ScriptTimeout | Er is een time-out opgetreden tijdens het uitvoeren van de toepassings consistente back-up. | Controleer het script en verhoog de time-out in de **VMSnapshotScriptPluginConfig.jsop** het bestand dat zich op **/etc/Azure** bevindt. |
| Post-ScriptTimeout | Er is een time-out opgetreden tijdens het uitvoeren van de toepassings consistente back-up. | Controleer het script en verhoog de time-out in de **VMSnapshotScriptPluginConfig.jsop** het bestand dat zich op **/etc/Azure** bevindt. |

## <a name="next-steps"></a>Volgende stappen

[VM-back-ups configureren in een Recovery Services kluis](./backup-azure-vms-first-look-arm.md)
