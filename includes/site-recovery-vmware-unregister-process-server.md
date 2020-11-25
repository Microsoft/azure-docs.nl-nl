---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: bff9457220efbb6a6b318550a5b2dd9af4ae230f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019179"
---
Volg de stappen voor uw specifieke omstandigheden.

### <a name="unregister-a-connected-process-server"></a>Registratie van een verbonden processerver ongedaan maken

1. Breng als beheerder een externe verbinding met de processerver tot stand.
2. Ga naar het **Configuratiescherm** en open **Programma's > Een programma verwijderen**.
3. Verwijder het programma **Microsoft Azure Site Recovery Mobility Service/Master Target Server**.
4. Verwijder het programma **Microsoft Azure Site Recovery Configuration/Process Server**.
5. Als de programma's uit stappen 3 en 4 zijn verwijderd, verwijdert u **Microsoft Azure Site Recovery Configuration/Process Server Dependencies**.

### <a name="unregister-a-disconnected-process-server"></a>Registratie van een niet-verbonden processerver beëindigen

Gebruik deze stappen alleen als het niet mogelijk is om de computer waarop de processerver is geïnstalleerd, aan de praat te krijgen.

1. Meld u bij uw configuratieserver aan als beheerder.
2. Open een opdrachtprompt met beheerdersbevoegdheden en blader naar `%ProgramData%\ASR\home\svsystems\bin`.
3. Voer deze opdracht uit om een lijst met de beschikbare processervers weer te geven.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - S. No: het serienummer van de processerver.
    - IP/Name: het IP-adres en de naam van de computer waarop de processerver wordt uitgevoerd.
    - Heartbeat: laatste heartbeat van de computer met de processerver.
    ![Schermopname met informatie over de processervers en de tekst Please choose one of the above servers to un-register.(media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. Geef het serienummer op van de processerver waarvan u de registratie ongedaan wilt maken.
5. Als u de registratie van een processerver ongedaan maakt, worden alle gegevens van de server uit het systeem verwijderd en wordt het volgende bericht weergegeven: **Successfully unregistered server-name> (IP-adres van server)**

