---
title: Microsoft Office FSLogix-toepassings containers installeren in virtueel bureau blad van Windows-Azure
description: De app Rule editor gebruiken om een FSLogix-toepassings container te maken met Office in virtueel bureau blad van Windows.
author: Heidilohr
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7d66d370f321323ec1aeb45ad0fe628904b14fe6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746039"
---
# <a name="install-microsoft-office-using-fslogix-application-containers"></a>Microsoft Office installeren met behulp van FSLogix-toepassings containers

U kunt Microsoft Office snel en efficiënt installeren met behulp van een FSLogix-toepassings container als een sjabloon voor de andere virtuele machines (Vm's) in uw hostgroep.

Ga als volgt te werk om de installatie sneller te maken met behulp van een FSLogix-app-container:

- Door uw Office-apps te offloaden naar een app-container, worden de vereisten voor de grootte van uw C-station verminderd.
- Moment opnamen of back-ups van uw VM nemen minder resources in beslag.
- Als u een automatische pijp lijn maakt via het bijwerken van een enkele installatie kopie, worden uw Vm's eenvoudiger bijgewerkt.
- U hebt slechts één installatie kopie nodig om Office (en andere apps) te installeren op alle virtuele machines in uw Windows-implementatie voor virtueel bureau blad.

In dit artikel wordt uitgelegd hoe u een FSLogix-toepassings container kunt instellen met Office.

## <a name="requirements"></a>Vereisten

U hebt de volgende zaken nodig om de regel editor in te stellen:

- een VM waarop Windows wordt uitgevoerd zonder Office geïnstalleerd
- een exemplaar van Office
- Er is een kopie van FSLogix geïnstalleerd in uw implementatie
- een netwerk share waarvoor alle virtuele machines in uw hostgroep alleen-lezen toegang hebben tot

## <a name="install-office"></a>Office installeren

Als u Office wilt installeren op uw VHD of VHDX, schakelt u de Remote Desktop Protocol in uw virtuele machine in en volgt u de instructies in [Office installeren op een VHD-hoofd installatie kopie](install-office-on-wvd-master-image.md). Zorg ervoor dat u [de juiste licenties](overview.md#requirements)gebruikt wanneer u installeert.

>[!NOTE]
>Voor het virtuele bureau blad van Windows is share computer activering (SCA) vereist.

## <a name="install-fslogix"></a>FSLogix installeren

Volg de instructies in [FSLogix downloaden en installeren](/fslogix/install-ht)om FSLogix en de regel editor te installeren.

## <a name="create-and-prepare-a-vhd-to-store-office"></a>Een VHD maken en voorbereiden voor het opslaan van Office

Vervolgens moet u een VHD-installatie kopie maken en voorbereiden voor het gebruik van de regel Editor op:

1. Open een opdrachtprompt als beheerder. en voer de volgende opdracht uit:

    ```cmd
        taskkill /F /IM MicrosoftEdge.exe /T
    ```

    >[!NOTE]
    > Zorg ervoor dat u de lege spaties in deze opdracht laat staan.

2. Voer vervolgens de volgende opdracht uit:

    ```cmd
    sc queryex type=service state=all | find /i "ClickToRunSvc"
    ```
    
   Als u de service vindt, start u de VM opnieuw op voordat u doorgaat met stap 3.

    ```cmd
    net stop ClickToRunSvc
    ```

3. Daarna gaat u naar **Program Files**  >  **FSLogix**  >  **apps** en voert u de volgende opdracht uit om de doel-VHD te maken:

    ```cmd
    frx moveto-vhd -filename <path to network share>\office.vhdx -src "C:\Program Files\Microsoft Office" -size-mbs 5000 
    ```

    De VHD die u met deze opdracht maakt, moet de map C: \\ Program Files \\ Microsoft Office bevatten.

    >[!NOTE]
    >Als er fouten worden weer geven, verwijdert u Office en begint u met stap 1.

## <a name="configure-the-rule-editor"></a>De regel Editor configureren

Nu u de installatie kopie hebt voor bereid, moet u de regel Editor configureren en een bestand maken om uw regels op te slaan in.

1. Ga naar **programma bestanden**  >  **FSLogix**  >  **apps** en voer **RuleEditor.exe** uit.

2. Selecteer **bestand**  >  **Nieuw**  >  **maken** om een nieuwe regelset te maken en sla die regel vervolgens op in een lokale map.

3. Selecteer een **lege regelset** en selecteer **OK**.

4. Selecteer de knop **+**. Hiermee opent u het venster **regel toevoegen** . Hiermee worden de opties in het dialoog venster **regel toevoegen** gewijzigd.

5. Selecteer in de vervolg keuzelijst **app-container (VHD) regel**.

6. Voer **C: \\ Program Files \\ Microsoft Office** in het veld **map** .

7. Selecteer in het veld **schijf bestand** de optie **\<path\> \\ Office. VHD** in het gedeelte **doel-VHD maken** .

8. Selecteer **OK**.

9. Ga naar de werkmap op **C: \\ gebruikers \\ \<username\> \\ documenten \\ FSLogix regel sets** en zoek naar de bestanden. frx en. fxa. U moet deze bestanden verplaatsen naar de map rules die zich bevinden op **C: \\ Program Files \\ FSLogix \\ apps \\ Rules** om te beginnen met de werking van de regels.

10. Selecteer **regels Toep assen op systeem** om de regels van kracht te laten worden.

     >[!NOTE]
     > U moet de app-regel bestanden Toep assen om alle sessie-hosts te kunnen gebruiken.

## <a name="next-steps"></a>Volgende stappen

Als u meer wilt weten over FSLogix, raadpleegt u onze [FSLogix-documentatie](/fslogix/).