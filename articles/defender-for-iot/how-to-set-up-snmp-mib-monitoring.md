---
title: SNMP MIB-bewaking instellen
description: U kunt sensor status controle uitvoeren met behulp van SNMP. De sensor reageert op SNMP-query's die worden verzonden vanaf een geautoriseerde bewakings server.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/14/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 051ce1be66f91d60f719ca3695f15e6c8001b20f
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523818"
---
# <a name="set-up-snmp-mib-monitoring"></a>SNMP MIB-bewaking instellen

U kunt de sensor status bewaking met behulp van Simple Network Management Protocol (SNMP) uitvoeren. De sensor reageert op SNMP-query's die worden verzonden vanaf een geautoriseerde bewakings server. De SNMP-monitor pollt regel matig de sensor-Oid's (Maxi maal 50 keer per seconde).

De ondersteunde SNMP-versies zijn SNMP v2 of SNMP v3. SNMP gebruikt UDP als transport protocol met poort 161 (SNMP).

Voordat u begint met het configureren van SNMP-bewaking, moet u de poort UDP 161 openen in de firewall.

## <a name="oids"></a>Oid's

| Beheer console en sensor | NOGMAALS | Indeling | Beschrijving |
|--|--|--|--|
| Naam van apparaat | 1.3.6.1.2.1.1.5.0 | DISPLAY | Naam van apparaat voor de on-premises beheer console |
| Leverancier | 1.3.6.1.2.1.1.4.0 | DISPLAY | Microsoft Ondersteuning (support.microsoft.com) |
| Platform | 1.3.6.1.2.1.1.1.0 | DISPLAY | Sensor of on-premises beheer console |
| Serienummer | 1.3.6.1.4.1.9.9.53313.1 | DISPLAY | De teken reeks die door de licentie wordt gebruikt |
| Software versie | 1.3.6.1.4.1.9.9.53313.2 | DISPLAY | Teken reeks voor volledige versie van Xsense en beheer van volledige versie |
| CPU-gebruik | 1.3.6.1.4.1.9.9.53313.3.1 | GAUGE32 | Aanduiding voor nul tot 100 |
| CPU-Tempe ratuur | 1.3.6.1.4.1.9.9.53313.3.2 | DISPLAY | Celsius aanwijzing voor nul tot 100 op basis van Linux-invoer |
| Geheugengebruik | 1.3.6.1.4.1.9.9.53313.3.3 | GAUGE32 | Aanduiding voor nul tot 100 |
| Schijf gebruik | 1.3.6.1.4.1.9.9.53313.3.4 | GAUGE32 | Aanduiding voor nul tot 100 |
| Servicestatus | 1.3.6.1.4.1.9.9.53313.5 | DISPLAY | Online of offline als een van de vier essentiële onderdelen niet beschikbaar is |
| Bandbreedte | Buiten het bereik van 2,4 |  | De band breedte die is ontvangen op elke monitor interface in Xsense |

   - Niet-bestaande sleutels reageren met Null, HTTP 200, op basis van [stack overflow](https://stackoverflow.com/questions/51419026/querying-for-non-existing-record-returns-null-with-http-200).
    
   - Hardware-gerelateerde Mib's (CPU-gebruik, CPU-Tempe ratuur, geheugen gebruik, schijf gebruik) moet worden getest op alle architecturen en fysieke Sens oren. De CPU-Tempe ratuur op virtuele machines wordt naar verwachting niet van toepassing.

U kunt het logboek downloaden dat alle SNMP-query's bevat die de sensor ontvangt, met inbegrip van de verbindings gegevens en onbewerkte gegevens van het pakket.

SNMP v2-status controle definiëren:

1. Selecteer **systeem instellingen** in het menu aan de zijkant.

2. Selecteer in het deel venster **actieve detectie** de optie **SNMP MIB-bewaking** :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false"::: .

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="Bewerk het SNMP-venster.":::

3. Selecteer in de sectie **toegestane hosts** de optie **host toevoegen** en voer het IP-adres in van de server die de systeem status controle uitvoert.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="Voer het IP-adres voor de toegestane hosts in.":::

4. Voer in de sectie **authenticatie** in het vak **SNMP v2 Community-teken** reeks de teken reeks in. De teken reeks voor de SNMP-community kan Maxi maal 32 tekens bevatten en een combi natie van alfanumerieke tekens (hoofd letters, kleine letters en cijfers) bevatten. Spaties zijn niet toegestaan.

5. Selecteer **Opslaan**.

SNMP v3-status controle definiëren:

1. Selecteer **systeem instellingen** in het menu aan de zijkant.

2. Selecteer in het deel venster **actieve detectie** de optie **SNMP MIB-bewaking** :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false"::: .

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="Bewerk het SNMP-venster.":::

3. Selecteer in de sectie **toegestane hosts** de optie **host toevoegen** en voer het IP-adres in van de server die de systeem status controle uitvoert.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="Voer het IP-adres voor de toegestane hosts in.":::

4. Stel in het gedeelte **authenticatie** de volgende para meters in:

    | Parameter | Beschrijving |
    |--|--|
    | **Gebruikersnaam** | De SNMP-gebruikers naam mag Maxi maal 32 tekens bevatten en een combi natie van alfanumerieke tekens (hoofd letters, kleine letters en cijfers) bevatten. Spaties zijn niet toegestaan. <br /> <br />De gebruikers naam voor de SNMP v3-verificatie moet op het systeem en op de SNMP-server worden geconfigureerd. |
    | **Wachtwoord** | Voer een hoofdletter gevoelig verificatie wachtwoord in. Het wacht woord voor verificatie kan 8 tot 12 tekens lang zijn en een combi natie van alfanumerieke tekens (hoofd letters, kleine letters en cijfers) bevatten. <br /> <br/>De gebruikers naam voor de SNMP v3-verificatie moet op het systeem en op de SNMP-server worden geconfigureerd. |
    | **Verificatietype** | Selecteer MD5 of SHA. |
    | **Versleuteling** | Selecteer DES of AES. |
    | **Geheime sleutel** | De sleutel moet uit precies acht tekens bestaan en een combi natie van alfanumerieke tekens (hoofd letters, kleine letters en cijfers) bevatten. |

5. Selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

[Logboeken voor het oplossen van problemen exporteren](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)
