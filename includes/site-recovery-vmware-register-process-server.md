---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 088cd5447b1f96dbf172b5918c29e4f3293289a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008468"
---
1. Stel een verbinding via Extern bureaublad in op de computer waarop de processerver wordt uitgevoerd. 
2. Voer cspsconfigtool.exe uit om het hulpprogramma voor de configuratie van de processerver van Azure Site Recovery te starten.
    - Het hulpprogramma wordt de eerste keer dat u zich aanmeldt bij de processerver, automatisch gestart.
    - Als het niet automatisch wordt geopend, klikt u op de snelkoppeling op het bureaublad.

3. Geef in **Configuration server FQDN or IP** de naam of het IP-adres van de configuratieserver op waarmee de processerver moet worden geregistreerd.
4. Controleer in **Configuration Server Port** of 443 is opgegeven. Dit is de poort waarop de configuratieserver naar aanvragen luistert.
5. Geef in **Connection Passphrase** de wachtwoordzin op die u hebt opgegeven bij het instellen van de configuratieserver. U kunt de wachtwoordzin als volgt vinden:
    -  Ga op de configuratieserver naar de installatiemap van Site Recovery * *\home\svssystems\bin\** :
    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    ```
    - Voer de onderstaande opdracht uit om de huidige wachtwoordzin af te drukken:
    ```
    genpassphrase.exe -n
    ```

6. In **Data Transfer Port** laat u de standaardwaarde staan, tenzij u een aangepaste poort hebt opgegeven.

7. Klik op **Save** om de instellingen op te slaan en registreer de processerver.

    
    ![Processerver registreren](./media/site-recovery-vmware-register-process-server/register-ps.png)
