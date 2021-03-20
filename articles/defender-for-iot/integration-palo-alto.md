---
title: Palo Alto-integratie
titleSuffix: Azure Defender for IoT
description: Defender voor IoT heeft zijn continue ICS-bewakings platform geïntegreerd met Palo Alto-firewalls van de volgende generatie zodat kritieke bedreigingen sneller en efficiënter kunnen worden geblokkeerd.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/17/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 85a7622223861f857ce75b8136b509ba279f3d96
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98558064"
---
# <a name="about-the-palo-alto-integration"></a>Over de Palo Alto-integratie

Defender voor IoT heeft zijn continue ICS-bewakings platform geïntegreerd met Palo Alto-firewalls van de volgende generatie zodat kritieke bedreigingen sneller en efficiënter kunnen worden geblokkeerd.

De volgende integratie typen zijn beschikbaar:

- Automatische blokkerende optie: direct Defender for IoT-Palo Alto-integratie

- Aanbevelingen verzenden voor blok keren naar het centrale beheer systeem: Defender for IoT-Panorama-integratie

## <a name="configure-immediate-blocking-by-specified-palo-alto-firewall"></a>Directe blok kering configureren door opgegeven Palo Alto firewall

In kritieke gevallen, zoals met malware gerelateerde waarschuwingen, kunt u automatisch blok keren inschakelen. Dit doet u door een doorstuur regel in Defender voor IoT te configureren die de Blokkeer opdracht rechtstreeks naar een specifieke Palo Alto-firewall verzendt.

Wanneer Defender voor IoT een kritieke bedreiging identificeert, verzendt het een waarschuwing met een optie voor het blok keren van de geïnfecteerde bron. Als u de **blok bron** in de details van de waarschuwing selecteert, wordt de doorstuur regel geactiveerd, die de Blokkeer opdracht verzendt naar de opgegeven Palo Alto-firewall.

Configureren:

1. Selecteer **door sturen** in het linkerdeel venster.

   :::image type="content" source="media/integration-paloalto/forwarding.png" alt-text="Het scherm voor het door sturen van waarschuwingen.":::

1. Selecteer **doorstuur regel maken**.

   :::image type="content" source="media/integration-paloalto/forward-rule.png" alt-text="Doorstuur regel maken":::

1. De Palo Alto NGFW-doorstuur regel configureren:  
 
   - Definieer de para meters voor de standaard regel en selecteer in de vervolg keuzelijst **acties** de optie **verzenden naar Palo Alto NGFW**.
   
   :::image type="content" source="media/integration-paloalto/edit.png" alt-text="Bewerk uw doorstuur regel.":::

1. Stel in het deel venster acties de volgende para meters in:

   - **Host**: Voer het IP-adres van de NGFW-server in.
   - **Poort**: Geef de NGFW-server poort op.
   - **Gebruikers naam**: Voer de gebruikers naam van de NGFW-server in.
   - **Wacht woord**: Voer het wacht woord voor de NGFW-server in.
   - **Configureren**: Stel de volgende opties in om het blok keren van de verdachte bronnen door de Palo Alto-firewall toe te staan:
     - **Ongeldige functie codes blok keren**: schendingen van het protocol-ongeldige veld waarde schenden de specificatie van het ICS-protocol (mogelijke aanvallen).
     - Niet **-geautoriseerde PLC Programming/firmware-updates blok keren**: niet-geautoriseerde PLC-wijzigingen.
     - **Niet-geautoriseerde PLC-stop blok keren**: PLC stoppen (downtime).
     - **Waarschuwingen met betrekking tot malware blok keren**: blok keren van industriële malware-pogingen (Triton, NotPetya, enzovoort). U kunt de optie voor **automatisch blok keren** selecteren. In dat geval wordt de blok kering automatisch en onmiddellijk uitgevoerd.
     - Niet- **geautoriseerd scannen blok keren**: niet-geautoriseerd scannen (mogelijke Reconnaissance).
     
1. Selecteer **Indienen**.

De verdachte bron blok keren: 

1. Selecteer in het deel venster **waarschuwingen** de waarschuwing die betrekking heeft op de Palo Alto-integratie. Het dialoog venster **waarschuwings Details** wordt weer gegeven.
   
   :::image type="content" source="media/integration-paloalto/unauthorized.png" alt-text="Waarschuwings Details":::

1. Selecteer **blok bron** om de verdachte bron automatisch te blok keren. Het dialoog venster **bevestigen** wordt weer gegeven.

   :::image type="content" source="media/integration-paloalto/please.png" alt-text="Bevestig blok kering op het scherm bevestigen.":::

1. Selecteer **OK** in het dialoog venster **bevestigen** . De verdachte bron wordt geblokkeerd door de Palo Alto-firewall.

## <a name="sending-blocking-policies-to-palo-alto-panorama"></a>Blokkerend beleid verzenden naar Palo Alto Panorama

Defender voor IoT-en Palo Alto-netwerken hebben een externe integratie die automatisch nieuwe beleids regels maakt in Palo Alto-netwerken NMS, Panorama. Deze integratie vereist bevestiging van de panorama-beheerder en staat automatische blok kering niet toe.

De integratie is bedoeld voor de volgende incidenten:

- Niet- **geautoriseerde PLC-wijzigingen:** Een update van de ladder of firmware van een apparaat. Dit kan een legitieme activiteit vertegenwoordigen of een poging om het apparaat te manipuleren. Dit kan gebeuren door schadelijke code in te voegen, zoals een Trojaans paard voor externe toegang (RAT) of para meters die het fysieke proces veroorzaken, zoals een draaiende turbine, om op een onveilige manier te worden uitgevoerd.

- **Schending van het Protocol:** Een pakket structuur of veld waarde die in strijd is met de protocol specificatie. Dit kan duiden op een onjuist geconfigureerde toepassing of een kwaad aardige poging om het apparaat te vervalsen. Bijvoorbeeld, veroorzaakt een buffer overloop in het doel apparaat.

- **PLC stoppen:** Een opdracht die ervoor zorgt dat het apparaat niet meer werkt, waardoor het fysieke proces dat wordt beheerd door de PLC wordt geriskeerd.

- **Industriële malware gevonden in het ICS-netwerk:** Schadelijke software die ICS-apparaten manipuleert met hun eigen protocollen, zoals TRITON en Industroyer. Defender voor IoT detecteert ook de schadelijke software die zich op een later tijdstip in de ICS-en SCADA-omgeving heeft verplaatst, zoals Conficker, WannaCry en NotPetya.

- **Malware scannen:** Reconnaissance-hulpprogram ma's voor het verzamelen van gegevens over de systeem configuratie in een pre-attack-fase. Bijvoorbeeld: met het paard van de SCADA hebben we industriële netwerken gescand op apparaten met behulp van OPC. Dit is een standaard protocol dat wordt gebruikt door Windows-systemen op basis van het netwerk om te communiceren met ICS-apparaten.

## <a name="the-process"></a>Het proces

Wanneer Defender voor IoT een vooraf geconfigureerd use-case detecteert, wordt de knop **blok bron** toegevoegd aan de waarschuwing. Wanneer de gebruiker van **cyberx** vervolgens de knop voor **blok bronnen** selecteert, maakt Defender voor IOT beleids regels op het panorama door de vooraf gedefinieerde doorstuur regel te verzenden.

Het beleid wordt alleen toegepast wanneer de panorama-beheerder het pusht naar de relevante NGFW in het netwerk.

In IT-netwerken kunnen er dynamische IP-adressen zijn. Daarom moet het beleid voor deze subnetten worden gebaseerd op FQDN (DNS-naam) en niet op het IP-adres. Defender voor IoT voert reverse lookup uit en komt overeen met het dynamische IP-adres voor de FQDN (DNS-naam) van elk geconfigureerd aantal uren.

Daarnaast stuurt Defender voor IoT een e-mail bericht naar de relevante Panorama-gebruiker om te melden dat een nieuw beleid dat is gemaakt door Defender voor IoT, in afwachting is van de goed keuring. In de afbeelding hieronder ziet u de Defender for IoT-Panorama Integration-architectuur.

:::image type="content" source="media/integration-paloalto/structure.png" alt-text="Architectuur van cyberx-Panorama-integratie":::

## <a name="create-panorama-blocking-policies-in-defender-for-iot-configuration"></a>Panorama-blokkerings beleid maken in Defender voor IoT-configuratie

### <a name="to-configure-dns-lookup"></a>DNS-zoek opdracht configureren

1. Selecteer **systeem instellingen** in het linkerdeel venster.

1. Selecteer **DNS-instellingen** in het deel venster **systeem instellingen** :::image type="icon" source="media/integration-paloalto/settings.png"::: .

   :::image type="content" source="media/integration-paloalto/configuration.png" alt-text="De DNS-instellingen configureren.":::

1. Stel in het dialoog venster **DNS-instellingen bewerken** de volgende para meters in:

   - **Status**: de status van de DNS-resolver.

   - **DNS-server adres**: Voer het IP-adres of de FQDN-naam van de netwerk-DNS-server in.
   - **DNS-server poort**: Geef de poort op die wordt gebruikt om een query uit te voeren op de DNS-server.
   - **Subnetten**: Stel het subnet van het dynamische IP-adres in. Het bereik dat Defender voor IoT reverse lookup van het IP-adres in de DNS-server moet overeenkomen met de huidige FQDN-naam.
   - **Reverse lookup plannen**: Definieer de plannings opties als volgt:
     - Op specifieke tijdstippen: Geef op wanneer u de reverse lookup dagelijks wilt uitvoeren.
     - Op vaste intervallen (in uren): Stel de frequentie in voor het uitvoeren van de reverse lookup.
   - **Aantal labels**: Geef Defender voor IOT opdracht om IP-adressen van het netwerk automatisch om te zetten naar FQDN-namen van apparaten. <br />Als u de DNS FQDN-omzetting wilt configureren, voegt u het aantal domein labels toe dat moet worden weer gegeven. Van links naar rechts worden Maxi maal 30 tekens weer gegeven.
1. Selecteer **SAVE** (Opslaan).
1. Selecteer **test voor zoeken** om ervoor te zorgen dat uw DNS-instellingen correct zijn. De test zorgt ervoor dat het IP-adres en de DNS-server poort van de DNS-server correct zijn ingesteld.

### <a name="to-configure-a-forwarding-rule-to-blocks-suspected-traffic-with-the-palo-alto-firewall"></a>Een doorstuur regel configureren om verdacht verkeer met de Palo Alto firewall te blok keren

1. Selecteer **door sturen** in het linkerdeel venster. Het deel venster door sturen wordt weer gegeven.

   :::image type="content" source="media/integration-paloalto/forward.png" alt-text="Het scherm voor door sturen.":::

1. Selecteer **door sturings regel maken** in het deel venster **door** sturen.

   :::image type="content" source="media/integration-paloalto/forward-rule.png" alt-text="Doorstuur regel maken":::

1. De Palo Alto Panorama-doorstuur regel configureren:

   Definieer de para meters voor de standaard regel en selecteer in de vervolg keuzelijst **acties** de optie **verzenden naar Palo Alto Panorama**. Het deel venster met actie Details wordt weer gegeven.

   :::image type="content" source="media/integration-paloalto/details.png" alt-text="Actie selecteren":::

1. Stel in het deel venster acties de volgende para meters in:

   - **Host**: Voer het IP-adres van de panorama-server in.

   - **Poort**: Geef de panorama-server poort op.
   - **Gebruikers naam**: Voer de naam van het panorama-server in.
   - **Wacht woord**: Voer het panorama server-wacht woord in.
   - **Rapport adres**: definiëren hoe de blok kering wordt uitgevoerd, als volgt:
   
     - Op **IP-adres**: maakt beleid voor blok keren altijd op panorama op basis van het IP-adres.
     
     - Op **FQDN of IP-adres**: maakt blokkerings beleid op panorama op basis van FQDN als dit bestaat, anders door het IP-adres.
     
   - **E-mail**: Stel het e-mail adres voor de e-mail melding voor het beleid in
     > [!NOTE]
     > Zorg ervoor dat u een e-mail server hebt geconfigureerd in de Defender voor IoT. Als er geen e-mail adres is opgegeven, wordt er door Defender voor IoT geen e-mail melding verzonden.
   - **Een DNS-zoek opdracht uitvoeren op detectie van waarschuwingen (selectie vakje)**: wanneer de optie op FQDN of IP-adres is ingesteld in het rapport adres. Dit selectie vakje is standaard ingeschakeld. Als alleen het IP-adres is ingesteld, is deze optie uitgeschakeld.
   - **Configureren**: Stel de volgende opties in om het blok keren van de verdachte bronnen door de Palo Alto Panorama toe te staan:
   
     - **Ongeldige functie codes blok keren**: schendingen van het protocol-ongeldige veld waarde schenden ICS, protocol specificatie (mogelijke aanvallen).
     
     - Niet **-geautoriseerde PLC Programming/firmware-updates blok keren**: niet-geautoriseerde PLC-wijzigingen.
     
     - **Niet-geautoriseerde PLC-stop blok keren**: PLC stoppen (downtime).
     
     - **Waarschuwingen met betrekking tot malware blok keren**: blok keren van industriële malware-pogingen (Triton, NotPetya, enzovoort). U kunt de optie voor **automatisch blok keren** selecteren. In dat geval wordt de blok kering automatisch en onmiddellijk uitgevoerd.
     
     - Niet- **geautoriseerd scannen blok keren**: niet-geautoriseerd scannen (mogelijke Reconnaissance).
     
1. Selecteer **Indienen**.

### <a name="to-block-the-suspicious-source"></a>De verdachte bron blok keren

1. Selecteer in het deel venster **waarschuwingen** de waarschuwing die betrekking heeft op de Palo Alto-integratie. Het dialoog venster **Details van de waarschuwing** wordt weer gegeven.

   :::image type="content" source="media/integration-paloalto/unauthorized.png" alt-text="Waarschuwings Details":::        

1. Selecteer **blok bron** om de verdachte bron automatisch te blok keren.

1. Selecteer OK in het dialoog venster **bevestigen** **.**

   :::image type="content" source="media/integration-paloalto/please.png" alt-text="Werkelijk":::

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [door sturen van waarschuwings gegevens](how-to-forward-alert-information-to-partners.md).
