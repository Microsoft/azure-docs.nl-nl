---
title: Probleem met Synapse Studio oplossen
description: Problemen met Azure Synapse Studio oplossen
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 7d91001024ad547e52fe48ee30749fee9a4fb4a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98116174"
---
# <a name="azure-synapse-studio-troubleshooting"></a>Problemen met Azure Synapse Studio oplossen

Deze hand leiding voor het oplossen van problemen bevat instructies over welke informatie moet worden geboden bij het openen van een ondersteunings ticket voor problemen met de netwerk verbinding. Met de juiste informatie kunnen we het probleem mogelijk sneller oplossen.

## <a name="serverless-sql-pool-service-connectivity-issue"></a>Serverloze connectiviteits probleem met SQL-groeps service

### <a name="symptom-1"></a>Symptoom 1

De optie ' serverloze SQL-groep ' is niet beschikbaar in de vervolg keuzelijst verbinding maken met.

![symptom1](media/troubleshooting-synapse-studio/symptom1v2.png)

### <a name="symptom-2"></a>Symptoom 2

Als u de query uitvoert met ' serverloze SQL-groep ', wordt het fout bericht ' kan geen verbinding maken met server ' weer gegeven.

![symptoom 2](media/troubleshooting-synapse-studio/symptom2.png)
 

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

> [!NOTE] 
>    De volgende stappen voor probleem oplossing zijn van chroom rand en Chrome. U kunt andere browsers (zoals FireFox) gebruiken met dezelfde probleemoplossings stappen, maar het venster voor ontwikkel aars heeft mogelijk een andere indeling dan de scherm afbeeldingen in deze TSG. Gebruik zo mogelijk geen klassieke rand voor het oplossen van problemen, omdat deze mogelijk onnauwkeurige informatie in een bepaalde situatie weergeeft.

Open het deel venster diagnostische gegevens, selecteer de knop diagnose downloaden. De gedownloade informatie voor fout rapportage te hand haven. U kunt in plaats daarvan de sessie-ID kopiëren en deze koppelen wanneer het ondersteunings ticket wordt geopend.

![Diagnostische gegevens](media/troubleshooting-synapse-studio/diagnostic-info-download.png)

Voer de bewerking uit die u in azure Synapse Studio hebt uitgevoerd om te beginnen met het oplossen van problemen.

- Selecteer voor symptoom 1 de knop Vernieuwen rechts van de vervolg keuzelijst ' Data Base gebruiken ' op het tabblad SQL-script en controleer of u ' serverloze SQL-groep ' kunt zien.
- Voor symptoom 2 voert u de query opnieuw uit om te zien of deze met succes wordt uitgevoerd.

Als het probleem zich nog steeds voordoet, drukt u op F12 in uw browser om "Ontwikkelhulpprogramma's" (DevTools) te openen.

Ga in het venster Ontwikkelhulpprogramma's naar het deel venster "netwerk". Selecteer zo nodig de knop wissen op de werk balk in het deel venster netwerk.
Controleer of "cache uitschakelen" in het paneel netwerk is ingeschakeld.

Voer de bewerking opnieuw uit die u hebt uitgevoerd in azure Synapse Studio. U ziet mogelijk nieuwe items die worden weer gegeven in de lijst ' netwerk ' in Ontwikkelhulpprogramma's. Noteer uw huidige systeem tijd om in het ondersteunings ticket op te geven.

![netwerk paneel 1](media/troubleshooting-synapse-studio/network-panel.png)

Zoek het item waarvan de URL-kolom overeenkomt met het volgende patroon:

`https://[*A*]-ondemand.database.windows.net:1443/databases/[*B*]/query?api-version=2018-08-01-preview&application=ArcadiaSqlOnDemandExplorer`

Waarbij [*A*] de naam van uw werk ruimte is en "-OnDemand" kan '-sqlod ' zijn en waarbij [*B*] een database naam moet zijn, zoals "Master". Er mogen Maxi maal twee items met dezelfde URL-waarde maar andere methode waarden zijn. OPTIES en POST. Controleer of deze twee items ' 200 ' of ' 20x ' bevatten in de kolom Status, waarbij ' x ' één cijfer kan zijn.

Als een van beide items een andere waarde heeft dan ' 20x ' en:

- Status begint met ' (mislukt) ', breid de kolom ' status ' uit of Beweeg de muis aanwijzer over de status tekst om de volledige tekst te zien. Neem de tekst en/of de scherm opname op wanneer u het ondersteunings ticket opent.

    ![status tekst](media/troubleshooting-synapse-studio/status-text.png)

    - Als ERR_NAME_NOT_RESOLVED en u uw werk ruimte binnen 10 minuten hebt gemaakt, wacht u 10 minuten en probeert u opnieuw of het probleem zich nog steeds voordoet.
    - Als ERR_INTERNET_DISCONNECTED of ERR_NETWORK_CHANGED wordt weer geven, is het mogelijk dat de netwerk verbinding van uw PC problemen ondervindt. Controleer uw netwerk verbinding en voer de bewerking opnieuw uit.
    - Als ERR_CONNECTION_RESET, ERR_SSL_PROTOCOL_ERROR of andere fout codes met "SSL" worden weer geven, is het mogelijk dat uw lokale SSL-configuratie problemen ondervindt of dat uw netwerk beheerder de toegang tot de serverloze SQL-groeps server heeft geblokkeerd. Open een ondersteunings ticket en koppel de fout code in de beschrijving.
    - Als ERR_NETWORK_ACCESS_DENIED wordt weer geven, moet u mogelijk de beheerder vragen of uw lokale firewall beleid de toegang heeft geblokkeerd tot een database.windows.net-domein of externe poort 1443.
    - Probeer eventueel dezelfde bewerking direct op een andere computer en/of netwerk omgeving om een probleem met de netwerk configuratie op uw PC uit te voeren.

- De status is "40x", "50x" of andere nummers. Selecteer de items om de details weer te geven. U ziet nu de item gegevens aan de rechter kant. De sectie antwoord header zoeken; Controleer vervolgens of een item met de naam ' Access-Control-Allow-Origin ' bestaat. Als dit het geval is, controleert u of deze een van de volgende waarden heeft:

    - `*` (enkel sterretje)
    - https://web.azuresynapse.net/ (of een andere waarde die de tekst in de adres balk van de browser begint met)

Als de antwoord header een van de bovenstaande waarden bevat, betekent dit dat de informatie over de fout al is verzameld. U kunt indien nodig een ondersteunings ticket openen en desgewenst de scherm afbeelding van de item gegevens toevoegen.

Als de koptekst niet kan worden weer gegeven of als de koptekst niet een van de bovenstaande waarden bevat, koppelt u een scherm opname van de item gegevens wanneer u het ticket opent.

 
![Details van item](media/troubleshooting-synapse-studio/item-details.png)
 
Als u met de bovenstaande stappen het probleem niet kunt oplossen, moet u mogelijk een ondersteunings ticket openen. Wanneer u uw ondersteunings ticket verzendt, neemt u de "sessie-ID" of "diagnostische gegevens" die aan het begin van deze hand leiding zijn gedownload.

Wanneer u het probleem meldt, kunt u eventueel een scherm opname maken van het tabblad ' console ' in het ' Ontwikkelhulpprogramma's ' en dit koppelen aan het ondersteunings ticket. De inhoud schuiven en meer dan één scherm opname maken, indien nodig, om het hele bericht vast te leggen.

![console voor ontwikkel hulpprogramma's](media/troubleshooting-synapse-studio/developer-tool-console.png)

Als u scherm afbeeldingen koppelt, geeft u de tijd (of een schatting van het tijds bereik) op van het moment waarop u de scherm opnamen hebt gemaakt. Het helpt ons bij het inkijken van het probleem.

Bepaalde browsers ondersteunen het weer geven van tijds tempels op het tabblad ' console '. Open voor chroom rand/Chrome het dialoog venster instellingen in Ontwikkelhulpprogramma's en schakel het selectie vakje tijds tempels weer geven in op het tabblad voor keuren.

![instellingen voor ontwikkelaars hulpprogramma console](media/troubleshooting-synapse-studio/developer-tool-console-settings.png)

![tijds tempel weer geven](media/troubleshooting-synapse-studio/show-time-stamp.png)

## <a name="next-steps"></a>Volgende stappen
Als de vorige stappen niet helpen om uw probleem op te lossen, [maakt u een ondersteunings ticket](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)