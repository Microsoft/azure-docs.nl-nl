---
title: Symantec ICDx-gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het gebruik van de Symantec ICDx-connector om eenvoudig al uw Symantec Security-oplossings logboeken met Azure Sentinel te verbinden.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 7b32baa23a31342095e542d60745a503b81e89a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100092703"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Verbinding maken met uw Symantec ICDx-apparaat 



Met de Symantec ICDx-connector kunt u eenvoudig al uw Symantec Security-oplossings logboeken koppelen aan uw Azure-Sentinel, om Dash boards weer te geven, aangepaste waarschuwingen te maken en het onderzoek te verbeteren. Dit geeft u meer inzicht in het netwerk van uw organisatie en verbetert de mogelijkheden van beveiligings bewerkingen. Integratie tussen Symantec ICDx en Azure Sentinel maakt gebruik van REST API.


> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="configure-and-connect-symantec-icdx"></a>Symantec ICDx configureren en verbinden 

Symantec ICDx kan Logboeken rechtstreeks integreren en exporteren naar Azure Sentinel.

1. Open de ICDx-beheer console om Microsoft Azure Sentinel (Log Analytics)-doorstuur servers toe te voegen.
2. Klik op de ICDx-navigatie balk op **configuratie**. 
3. Klik boven aan het **configuratie** scherm op **doorstuur servers**.
4. Onder **doorstuur servers**, naast Microsoft Azure Sentinel (log Analytics), klikt u op **toevoegen**. 
4. Klik in het **Microsoft Azure Sentinel (log Analytics)** -venster op **Geavanceerd weer geven**. 
5. Ga als volgt te werk boven aan het venster uitvouwen tot Microsoft Azure Sentinel (Log Analytics):
    -   **Naam**: Typ een naam voor de doorstuur server die uit niet meer dan 30 tekens bestaat. Kies een unieke, herken bare naam. Deze naam wordt weer gegeven in de lijst met doorstuur servers op het scherm **configuratie** en in de Dash boards op het **dash board** . Bijvoorbeeld: Microsoft Azure Log Analytics East. Dit veld is vereist.
    -   **Beschrijving**: Typ een beschrijving voor de doorstuur server. Deze beschrijving wordt ook weer gegeven in de lijst met doorstuur servers op het **configuratie** scherm. Neem Details op zoals het gebeurtenis type dat wordt doorgestuurd en de groep die de gegevens moet controleren.
    -   **Opstart type**: Selecteer de methode voor opstarten voor de configuratie van de doorstuur server. Uw opties zijn hand matig en automatisch.<br>De standaard instelling is automatisch. 
6. Onder **gebeurtenissen** gaat u als volgt te werk: 
    - **Bron**: Selecteer een of meer archieven waarvan u gebeurtenissen wilt door sturen. U kunt actieve Collector archieven (inclusief het algemene archief), zwevende Collector archieven (archieven voor de door u verwijderde verzamelaars), ICDx receiver-archieven of het systeem archief selecteren. <br>De standaard waarde is archief.
      > [!NOTE]
      > ICDx ontvanger-archieven worden afzonderlijk vermeld op naam. 
 
    - **Filter**: een filter toevoegen waarmee de subset gebeurtenissen worden opgegeven die moeten worden doorgestuurd. Voer een van de volgende handelingen uit:
        - Als u een filter voorwaarde wilt selecteren, klikt u op een type, kenmerk, operator en waarde. 
        - Controleer de filter voorwaarde in het veld Filter. U kunt deze rechtstreeks in het veld bewerken of verwijderen als dat nodig is.
        - Klik op en of of om toe te voegen aan uw filter voorwaarde.
        - U kunt ook op opgeslagen Query's klikken om een opgeslagen query toe te passen.
    - **Opgenomen kenmerken**: Typ de door komma's gescheiden lijst met kenmerken die moeten worden opgenomen in de doorgestuurde gegevens. De Inge sloten kenmerken hebben voor rang op uitgesloten kenmerken.
    - **Uitgesloten kenmerken**: Typ de door komma's gescheiden lijst met kenmerken die moeten worden uitgesloten van de doorgestuurde gegevens.
    - **Batch grootte**: Selecteer het aantal gebeurtenissen dat per batch moet worden verzonden. Uw opties zijn 10, 50, 100, 500 en 1000.<br>De standaardwaarde is 100. 
    - **Frequentie limiet**: Selecteer de snelheid waarmee gebeurtenissen worden doorgestuurd, uitgedrukt als gebeurtenissen per seconde. Uw opties zijn onbeperkt, 500, 1000, 5000 en 10000. <br> De standaard waarde is 5000. 
7. Onder **Azure Destination** gaat u als volgt te werk: 
    - **Werk ruimte-id**: plak de werk ruimte-id hieronder. Dit veld is vereist.
    - **Primaire sleutel**: plak de primaire sleutel van hieronder. Dit veld is vereist.
    - **Aangepaste logboek naam**: Typ de naam van het aangepaste logboek in het Microsoft Azure-Portal log Analytics werk ruimte waarnaar u gebeurtenissen wilt door sturen. De standaard waarde is SymantecICDx. Dit veld is vereist.
8. Klik op *Opslaan* om de configuratie van de doorstuur server te volt ooien. 
9. Als u de doorstuur server wilt starten, klikt u op **meer** onder **Opties** en vervolgens op **starten**.
10. Als u het relevante schema in Log Analytics voor de Symantec ICDx-gebeurtenissen wilt gebruiken, zoekt u naar **SymantecICDx_CL**.


## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan Maxi maal 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven. 



## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Symantec ICDx verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.


