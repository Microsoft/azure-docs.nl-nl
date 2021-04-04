---
title: Handleiding voor het oplossen van problemen
titleSuffix: Microsoft Genomics
description: Meer informatie over strategieën voor het oplossen van problemen met het gebruik van Microsoft Genomics, waaronder fout berichten en hoe u deze kunt oplossen.
keywords: problemen oplossen, fouten opsporen
services: genomics
author: ruchir
ms.author: ruchir
ms.service: genomics
ms.workload: genomics
ms.topic: troubleshooting
ms.date: 10/29/2018
ms.openlocfilehash: c508c10d619cde1a16d89b446c5cfd1a3ce81daf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96023820"
---
# <a name="troubleshooting-guide"></a>Handleiding voor het oplossen van problemen

Hier volgen enkele tips voor het oplossen van problemen die u kunt tegen komen wanneer u de Microsoft Genomics-service gebruikt, MSGEN.

 Zie [Veelgestelde vragen](frequently-asked-questions-genomics.md)voor meer informatie over het oplossen van problemen.
## <a name="step-1-locate-error-codes-associated-with-the-workflow"></a>Stap 1: fout codes zoeken die zijn gekoppeld aan de werk stroom

U vindt de fout berichten die zijn gekoppeld aan de werk stroom door:

1. Gebruik de opdracht regel en typ in  `msgen status`
2. De inhoud van standardoutput.txt te controleren.

### <a name="1-using-the-command-line-msgen-status"></a>1. met behulp van de opdracht regel `msgen status`

```bash
msgen status -u URL -k KEY -w ID 
```




Er zijn drie vereiste argumenten:

* URL-de basis-URI voor de API
* SLEUTEL: de toegangs sleutel voor uw Genomics-account
    * Als u uw URL en sleutel wilt vinden, gaat u naar Azure Portal en opent u de pagina Microsoft Genomics-account. Kies **toegangs sleutels** onder de kop **beheer** . Daar vindt u de API-URL en de toegangs sleutels.

  
* ID: de werk stroom-ID
    * Om uw werk stroom-ID-type in opdracht te vinden `msgen list` . Ervan uitgaande dat uw configuratie bestand de URL en de toegangs sleutels bevat en zich op dezelfde locatie bevindt als uw msgen exe, ziet de opdracht er als volgt uit: 
        
        ```bash
        msgen list -f "config.txt"
        ```
        De uitvoer van deze opdracht ziet er als volgt uit:
        
        ```bash
            Microsoft Genomics command-line client v0.7.4
                Copyright (c) 2018 Microsoft. All rights reserved.
                
                Workflow List
                -------------
                Total Count  : 1
                
                Workflow ID     : 10001
                Status          : Completed successfully
                Message         :
                Process         : snapgatk-20180730_1
                Description     :
                Created Date    : Mon, 27 Aug 2018 20:27:24 GMT
                End Date        : Mon, 27 Aug 2018 20:55:12 GMT
                Wall Clock Time : 0h 27m 48s
                Bases Processed : 1,348,613,600 (1 GBase)
        ```

  > [!NOTE]
  >  U kunt ook het pad naar het configuratie bestand toevoegen in plaats van de URL en sleutel rechtstreeks in te voeren. Als u deze argumenten opneemt in de opdracht regel, evenals het configuratie bestand, hebben de opdracht regel argumenten prioriteit.  

De opdracht ziet er als volgt uit als werk stroom-ID 1001 en config.txt bestand dat in hetzelfde pad wordt geplaatst als het uitvoer bare msgen:

```bash
msgen status -w 1001 -f "config.txt"
```

### <a name="2--examine-the-contents-of-standardoutputtxt"></a>2. Controleer de inhoud van standardoutput.txt 
Zoek de uitvoer container voor de betreffende werk stroom. MSGEN maakt een,   `[workflowfilename].logs.zip` map na elke uitvoering van de werk stroom. Pak de map uit om de inhoud ervan weer te geven:

* outputFileList.txt: een lijst met de uitvoer bestanden die zijn geproduceerd tijdens de werk stroom
* standarderror.txt-dit bestand is leeg.
* standardoutput.txt: Hiermee worden alle status berichten op het hoogste niveau vastgelegd, inclusief fouten die zijn opgetreden tijdens het uitvoeren van de werk stroom.
* GATK-logboek bestanden: alle andere bestanden in de `logs` map

Bekijk de inhoud van standardoutput.txt en noteer eventuele fout berichten die worden weer gegeven voor het oplossen van problemen.


## <a name="step-2-try-recommended-steps-for-common-errors"></a>Stap 2: Probeer de aanbevolen stappen voor veelvoorkomende fouten

In deze sectie worden veelvoorkomende fouten beschreven die worden uitgevoerd door Microsoft Genomics service (msgen) en de strategieën die u kunt gebruiken om ze op te lossen. 

De Microsoft Genomics-service (msgen) kan de volgende twee soorten fouten genereren:

1. Interne service fouten: fouten die intern zijn voor de service, die mogelijk niet worden opgelost door para meters of invoer bestanden te herstellen. Soms worden deze fouten mogelijk opgelost door de werk stroom opnieuw te verzenden.
2. Invoer fouten: fouten die kunnen worden opgelost met de juiste argumenten of bestands indelingen herstellen.

### <a name="1-internal-service-errors"></a>1. interne service fouten

Een interne service fout is niet van gebruikers actie. U kunt de werk stroom opnieuw verzenden, maar als dat niet werkt, neemt u contact op met Microsoft Genomics ondersteuning

| Foutbericht                                                                                                                            | Aanbevolen stappen voor probleemoplossing                                                                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Er is een interne fout opgetreden. Probeer de werk stroom opnieuw te verzenden. Als deze fout opnieuw wordt weer geven, neemt u contact op met Microsoft Genomics ondersteuning voor hulp | Dien de werk stroom opnieuw in. Neem contact op met Microsoft Genomics ondersteuning voor hulp als het probleem zich blijft voordoen door een ondersteunings [ticket](file-support-ticket-genomics.md )te maken. |

### <a name="2-input-errors"></a>2. invoer fouten

Deze fouten kunnen gebruikers actie ondernemen. Op basis van het bestands type en fout code Microsoft Genomics service uitvoer afzonderlijke fout codes. Volg de aanbevolen stappen voor probleem oplossing hieronder.

| Type bestand | Foutcode | Foutbericht                                                                           | Aanbevolen stappen voor probleemoplossing                                                                                         |
|--------------|------------|-----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Alle          | 701        | Lezen [gereed] heeft [numberOfBases] bases, maar de limiet is [maxReadLength]           | De meest voorkomende reden voor deze fout is de bestands beschadiging waardoor twee Lees bewerkingen worden samengevoegd. Controleer uw invoer bestanden. |
| BAM          | 200        |   Kan bestand [yourFileName] niet lezen.                                                                                       | Controleer de indeling van het BAM-bestand. Verzend de werk stroom opnieuw met een correct opgemaakt bestand.                                                                           |
| BAM          | 201        |  Kan het BAM-bestand niet lezen [File_name].                                                                                      |Controleer de indeling van het BAM-bestand.  Verzend de werk stroom met een correct opgemaakt bestand.                                                                            |
| BAM          | 202        | Kan het BAM-bestand niet lezen [File_name]. Het bestand is te klein en er ontbreekt een header.                                                                                        | Controleer de indeling van het BAM-bestand.  Verzend de werk stroom met een correct opgemaakt bestand.                                                                            |
| BAM          | 203        |   Kan het BAM-bestand niet lezen [File_name]. De header van het bestand is beschadigd.                                                                                      |Controleer de indeling van het BAM-bestand.  Verzend de werk stroom met een correct opgemaakt bestand.                                                                           |
| BAM          | 204        |    Kan het BAM-bestand niet lezen [File_name]. De header van het bestand is beschadigd.                                                                                     | Controleer de indeling van het BAM-bestand.  Verzend de werk stroom met een correct opgemaakt bestand.                                                                           |
| BAM          | 205        |    Kan het BAM-bestand niet lezen [File_name]. De header van het bestand is beschadigd.                                                                                     | Controleer de indeling van het BAM-bestand.  Verzend de werk stroom met een correct opgemaakt bestand.                                                                            |
| BAM          | 206        |   Kan het BAM-bestand niet lezen [File_name]. De header van het bestand is beschadigd.                                                                                      | Controleer de indeling van het BAM-bestand.  Verzend de werk stroom met een correct opgemaakt bestand.                                                                            |
| BAM          | 207        |  Kan het BAM-bestand niet lezen [File_name]. Het bestand is afgekapt bij offset [offset].                                                                                       | Controleer de indeling van het BAM-bestand.  Verzend de werk stroom met een correct opgemaakt bestand.                                                                            |
| BAM          | 208        |   Ongeldig BAM-bestand. De Gereede [Read_Id] heeft geen volg orde in het bestand [File_name].                                                                                      | Controleer de indeling van het BAM-bestand.  Verzend de werk stroom met een correct opgemaakt bestand.                                                                             |
| FASTQ        | 300        |  Kan het FASTQ-bestand niet lezen. [File_name] eindigt niet met een nieuwe regel.                                                                                     | Corrigeer de indeling van het FASTQ-bestand en verzend de werk stroom opnieuw.                                                                           |
| FASTQ        | 301        |   Kan het FASTQ-bestand niet lezen [File_name]. FASTQ-record is groter dan buffer grootte bij offset: [_offset]                                                                                      | Corrigeer de indeling van het FASTQ-bestand en verzend de werk stroom opnieuw.                                                                         |
| FASTQ        | 302        |     FASTQ-syntaxis fout. Bestand [File_name] bevat een lege regel.                                                                                    | Corrigeer de indeling van het FASTQ-bestand en verzend de werk stroom opnieuw.                                                                         |
| FASTQ        | 303        |       FASTQ-syntaxis fout. Bestand [File_name] heeft een ongeldig begin teken bij offset: [_offset], regel type: [line_type], teken: [_char]                                                                                  | Corrigeer de indeling van het FASTQ-bestand en verzend de werk stroom opnieuw.                                                                         |
| FASTQ        | 304      |  FASTQ-syntaxis fout bij het gereed [_ReadID].  Het eerste Lees van de batch is niet gereed voor het aflopen van/1 in het bestand [File_name]                                                                                       | Corrigeer de indeling van het FASTQ-bestand en verzend de werk stroom opnieuw.                                                                         |
| FASTQ        | 305        |  FASTQ-syntaxis fout bij het gereed [_readID]. De tweede lees bewerking van de batch is niet gereed voor afeindigend op/2 in het bestand [File_name]                                                                                      | Corrigeer de indeling van het FASTQ-bestand en verzend de werk stroom opnieuw.                                                                          |
| FASTQ        | 306        |  FASTQ-syntaxis fout bij het gereed [_ReadID]. De eerste Lees van het paar heeft geen ID die eindigt op/1 in het bestand [File_name]                                                                                       | Corrigeer de indeling van het FASTQ-bestand en verzend de werk stroom opnieuw.                                                                          |
| FASTQ        | 307        |   FASTQ-syntaxis fout bij het gereed [_ReadID]. Gereed voor gebruik van/1 of/2. Bestand [File_name] kan niet worden gebruikt als een gekoppeld FASTQ-bestand.                                                                                      |Corrigeer de indeling van het FASTQ-bestand en verzend de werk stroom opnieuw.                                                                          |
| FASTQ        | 308        |  FASTQ-Lees fout. Lees bewerkingen van beide uiteinden hebben een andere reactie. Hebt u de juiste FASTQ-bestanden gekozen?                                                                                       | Corrigeer de indeling van het FASTQ-bestand en verzend de werk stroom opnieuw.                                                                         |
|        |       |                                                                                        |                                                                           |

## <a name="step-3-contact-microsoft-genomics-support"></a>Stap 3: contact opnemen met Microsoft Genomics ondersteuning

Als er taak fouten optreden of als u andere vragen hebt, neemt u contact op met Microsoft Genomics ondersteuning van de Azure Portal. Meer informatie over het verzenden van een ondersteunings aanvraag vindt u [hier](file-support-ticket-genomics.md).

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u veelvoorkomende problemen met de Microsoft Genomics-service kunt oplossen en oplossen. Zie [Veelgestelde vragen](frequently-asked-questions-genomics.md)voor meer informatie en meer algemene veelgestelde vragen. 
