---
title: Controleren welk verkeer wordt bewaakt
description: Sens oren voeren automatisch grondige pakket detectie uit voor IT en verkeer en kunnen informatie over netwerk apparaten, zoals kenmerken van het apparaat en het netwerk gedrag, oplossen. Er zijn verschillende hulpprogram ma's beschikbaar om het type verkeer te bepalen dat elke sensor detecteert.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: bfe3e00c4930ba57c930eb1bc2f2dd4ed11886e0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100522390"
---
# <a name="control-what-traffic-is-monitored"></a>Controleren welk verkeer wordt bewaakt

Sens oren voeren automatisch grondige pakket detectie uit voor IT en verkeer en kunnen informatie over netwerk apparaten, zoals kenmerken en gedrag van apparaten, oplossen. Er zijn verschillende hulpprogram ma's beschikbaar om het type verkeer te bepalen dat elke sensor detecteert.

## <a name="learning-and-smart-it-learning-modes"></a>Leer-en slimme IT-leer modi

De leer modus geeft uw sensor de gebruikelijke activiteiten van uw netwerk over. Voor beelden zijn apparaten die zijn gedetecteerd in uw netwerk, protocollen die zijn gedetecteerd in het netwerk, bestands overdrachten tussen specifieke apparaten en meer. Deze activiteit wordt uw netwerk basislijn.

De leer modus wordt automatisch ingeschakeld na de installatie en blijft ingeschakeld totdat deze is uitgeschakeld. De geschatte periode van de leer modus ligt tussen twee en zes weken, afhankelijk van de grootte en complexiteit van het netwerk. Wanneer de leer modus is uitgeschakeld, worden er na deze periode waarschuwingen geactiveerd. Waarschuwingen worden geactiveerd wanneer de beleids engine afwijkingen van uw geleerde basis lijn detecteert.

Nadat de leer periode is voltooid en de leer modus is uitgeschakeld, kan de sensor een ongebruikelijk hoog niveau van basislijn wijzigingen detecteren die het resultaat zijn van de normale IT-activiteit, zoals DNS-en HTTP-aanvragen. De activiteit wordt een niet-deterministisch IT-gedrag genoemd. Het gedrag kan ook onnodige waarschuwingen voor beleids schendingen en systeem meldingen activeren. Als u de hoeveelheid van deze waarschuwingen en meldingen wilt beperken, kunt u de functie voor **slimme IT-training** inschakelen.

Wanneer Slim IT-learning is ingeschakeld, houdt de sensor netwerk verkeer bij dat niet-deterministische IT-gedrag genereert op basis van specifieke waarschuwings scenario's.

De sensor bewaakt dit verkeer gedurende zeven dagen. Als het hetzelfde niet-deterministische IT-verkeer binnen zeven dagen detecteert, blijft het verkeer voor een andere periode van zeven dagen in bewaken. Wanneer het verkeer voor een volledige zeven dagen niet wordt gedetecteerd, is slim IT-Learning uitgeschakeld voor dat scenario. Nieuw verkeer dat voor dit scenario wordt gedetecteerd, genereert alleen waarschuwingen en meldingen.

Als u werkt met Smart IT Learning, kunt u het aantal onnodige waarschuwingen en meldingen dat wordt veroorzaakt door ruisieve IT-scenario's, verminderen.

Als uw sensor wordt beheerd door de on-premises beheer console, kunt u de leer modi niet uitschakelen. In dergelijke gevallen kan de leer modus alleen worden uitgeschakeld vanuit de beheer console.

De leer mogelijkheden (learning en Smart IT learning) zijn standaard ingeschakeld.

Leren in-of uitschakelen:

- Selecteer **systeem instellingen** en schakel het **leer** -en **slimme it-leer** opties in.

:::image type="content" source="media/concept-learning-modes/toggle-options-for-learning-and-smart-it-learning.png" alt-text="Systeem instellingen wissel knop scherm.":::

## <a name="configure-subnets"></a>Subnetten configureren

Subnet-configuraties bepalen hoe u apparaten in de apparaattoewijzing ziet.

De sensor detecteert standaard de instellingen van uw subnet en vult het dialoog venster **subnet configuratie** met deze informatie.

Voor het inschakelen van focus op het OT, worden de IT-apparaten automatisch geaggregeerd door het subnet in de apparaattoewijzing. Elk subnet wordt weer gegeven als één entiteit op de kaart, inclusief een interactieve samen vouwen/uitbreid bare mogelijkheid om in te zoomen naar een IT-subnet en weer terug.

Wanneer u met subnetten werkt, selecteert u de ICS-subnetten om de subnetten te identificeren. U kunt de kaart weergave vervolgens richten op de netwerken en ICS en samen vouwen tot een minimum aan de presentatie van de IT-netwerk elementen. Deze inspanning vermindert het totale aantal apparaten dat op de kaart wordt weer gegeven en biedt een duidelijke afbeelding van de netwerk elementen OT en ICS.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/expand-network-option.png" alt-text="Scherm opname van het filteren op een netwerk.":::

U kunt de configuratie wijzigen of de gegevens van het subnet hand matig wijzigen door de gedetecteerde gegevens te exporteren, deze hand matig te wijzigen en vervolgens de lijst met subnetten die u hand matig hebt gedefinieerd, vervolgens weer te importeren. Zie informatie over het [importeren van apparaten](how-to-import-device-information.md)voor meer informatie over exporteren en importeren.

In sommige gevallen, zoals omgevingen die gebruikmaken van open bare bereiken als interne bereiken, kunt u de sensor de opdracht geven alle subnetten als interne subnetten op te lossen door de optie **Internet activiteit niet detecteren** te selecteren. Wanneer u deze optie selecteert:

- Open bare IP-adressen worden behandeld als lokale adressen.

- Er worden geen waarschuwingen verzonden over niet-geautoriseerde Internet activiteit, waardoor meldingen en waarschuwingen op externe adressen worden verminderd.

Subnetten configureren:

1. Selecteer **systeem instellingen** in het menu aan de zijkant.

2. Selecteer **subnetten** in het venster **systeem instelling** .

   :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-subnets-configuration-screen.png" alt-text="Scherm opname van het scherm voor het configureren van het subnet."::: 

3. Als u wilt dat subnetten automatisch worden toegevoegd wanneer er nieuwe apparaten worden gedetecteerd, moet u **automatisch geleerde subnetten** selecteren.

4. Als u alle subnetten wilt omzetten als interne subnetten, selecteert u **Internet activiteit niet detecteren**.

5. Selecteer **netwerk toevoegen** en definieer de volgende para meters voor elk subnet:

    - Het IP-adres van het subnet.
    - Het subnetmasker adres.
    - De naam van het subnet. U wordt aangeraden elk subnet een duidelijke naam te geven dat u eenvoudig kunt identificeren, zodat u kunt onderscheiden van het netwerk en de netwerken. De naam mag Maxi maal 60 tekens lang zijn.

6. Selecteer het **subnet ICS** als u dit subnet als een OT wilt markeren.

7. Als u het subnet afzonderlijk wilt presen teren wanneer u de kaart volgens het niveau Purdue rangschikt, selecteert u **gescheiden**.

8. Als u een subnet wilt verwijderen, selecteert u :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/delete-icon.png" border="false"::: .

9. Als u alle subnetten wilt verwijderen, selecteert u **Alles wissen**.

10. Selecteer **exporteren** als u geconfigureerde subnetten wilt exporteren. De tabel subnet wordt gedownload naar uw werk station.

11. Selecteer **Opslaan**.

### <a name="importing-information"></a>Gegevens importeren 

Als u subnetgegevens wilt importeren, selecteert u **importeren** en selecteert u een CSV-bestand dat u wilt importeren. De gegevens van het subnet worden bijgewerkt met de informatie die u hebt geïmporteerd. Als u een leeg veld belang rijk vindt, gaan uw gegevens verloren.

## <a name="detection-engines"></a>Detectie-engines

Zelf studie-analyse engines elimineren de nood zaak voor het bijwerken van hand tekeningen of het definiëren van regels. De engines gebruiken ICS-specifieke gedrags analyse en data Science om voortdurend het netwerk verkeer voor afwijkingen, malware, operationele problemen, schendingen van het protocol en afwijkingen van de basislijn netwerk activiteit te analyseren.

> [!NOTE]
> U wordt aangeraden alle beveiligings engines in te scha kelen.

Wanneer een engine een afwijking detecteert, wordt er een waarschuwing geactiveerd. U kunt waarschuwingen weer geven en beheren via het scherm waarschuwing of vanuit een partner systeem.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/deviation-alert-screen.png" alt-text="Scherm opname van de detectie van afwijkingen.":::

De naam van de engine die de waarschuwing heeft geactiveerd, wordt weer gegeven onder de titel van de waarschuwing.

### <a name="protocol-violation-engine"></a>Engine voor protocol overtreding 

Een schending van het protocol treedt op wanneer de pakket structuur of veld waarden niet voldoen aan de protocol specificatie.

Voorbeeld scenario: de waarschuwing *Ongeldige Modbus-bewerking (functie code nul)* . Deze waarschuwing geeft aan dat een primair apparaat een aanvraag met functie code 0 naar een secundair apparaat heeft verzonden. Deze actie is niet toegestaan volgens de protocol specificatie en het secundaire apparaat kan de invoer mogelijk niet correct afhandelen.

### <a name="policy-violation-engine"></a>Engine voor beleids overtreding

Een beleids schending treedt op met een afwijking van het basislijn gedrag dat is gedefinieerd in geleerde of geconfigureerde instellingen.

Voorbeeld scenario: waarschuwing *' niet-geautoriseerde HTTP-gebruikers agent '* . Deze waarschuwing geeft aan dat een toepassing die niet is geleerd of goedgekeurd door beleid wordt gebruikt als een HTTP-client op een apparaat. Dit kan een nieuwe webbrowser of toepassing op dat apparaat zijn.

### <a name="malware-engine"></a>Malware-engine

De malware-engine detecteert schadelijke netwerk activiteit.

Voorbeeld scenario: waarschuwing voor *vermoeden van schadelijke activiteiten (Stuxnet)* . Deze waarschuwing geeft aan dat de sensor verdachte netwerk activiteit heeft gedetecteerd die is gekoppeld aan de Stuxnet malware. Deze malware is een geavanceerde permanente bedreiging die gericht is op industriële controle en SCADA netwerken.

### <a name="anomaly-engine"></a>Afwijkende engine

De afwijkende engine detecteert afwijkingen in het netwerk gedrag.

Voorbeeld scenario: waarschuwing *' periodiek gedrag in communicatie kanaal '* . Het onderdeel inspecteert netwerk verbindingen en vindt het periodieke en cyclische gedrag van gegevens overdracht. Dit gedrag is gebruikelijk in industriële netwerken.

### <a name="operational-engine"></a>Operationele engine

De operationele engine detecteert operationele incidenten of werkt niet goed.

Voorbeeld scenario: de waarschuwing *' het apparaat wordt verdacht om de verbinding te verbreken (reageert niet)* . Deze waarschuwing wordt gegenereerd wanneer een apparaat niet op een type aanvraag reageert voor een vooraf gedefinieerde periode. Deze waarschuwing kan duiden op een afsluiting van het apparaat, de verbinding of de storing.

### <a name="enable-and-disable-engines"></a>Engines in-en uitschakelen

Wanneer u een beleids engine uitschakelt, is de informatie die door de engine wordt gegenereerd niet beschikbaar voor de sensor. Als u bijvoorbeeld de afwijkende engine uitschakelt, ontvangt u geen waarschuwingen over netwerk afwijkingen. Als u een doorstuur regel hebt gemaakt, worden afwijkingen die de engine leert, niet verzonden. Selecteer **ingeschakeld** of **uitgeschakeld** voor de specifieke engine om een beleids engine in of uit te scha kelen.

De algemene score wordt weer gegeven in de rechter benedenhoek van het scherm **systeem instellingen** . De score geeft het percentage beschik bare beveiliging aan dat is ingeschakeld via de beveiligings engines voor bedreigingen. Elke engine is 20 procent van de beschik bare beveiliging.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/protection-score-screen.png" alt-text="Scherm opname waarin een score wordt weer gegeven.":::

## <a name="configure-dhcp-address-ranges"></a>DHCP-adresbereiken configureren

Uw netwerk kan bestaan uit zowel statische als dynamische IP-adressen. Statische adressen worden normaal gesp roken gevonden in netwerken via Historians, controllers en netwerk infrastructuur, zoals switches en routers. Dynamische IP-toewijzing wordt doorgaans geïmplementeerd op gast netwerken met laptops, Pc's, smartphones en andere draag bare apparaten (met behulp van Wi-Fi of fysieke LAN-verbindingen op verschillende locaties).

Als u met dynamische netwerken werkt, verwerkt u de IP-adres wijzigingen die zich voordoen wanneer er nieuwe IP-adressen worden toegewezen. U kunt dit doen door DHCP-adresbereiken te definiëren.

Er kunnen bijvoorbeeld wijzigingen optreden wanneer een DHCP-server IP-adressen toewijst.

Het definiëren van dynamische IP-adressen op elke sensor maakt uitgebreide, transparante ondersteuning mogelijk in exemplaren van IP-adres wijzigingen. Dit zorgt voor uitgebreide rapportage voor elk uniek apparaat.

De sensor console bevat het meest actuele IP-adres dat is gekoppeld aan het apparaat en geeft aan welke apparaten dynamisch zijn. Bijvoorbeeld:

- Het rapport gegevens analyse rapport en inventarisatie van apparaten consolideren alle activiteiten die zijn geleerd van het apparaat als één entiteit, ongeacht het IP-adres verandert. Deze rapporten geven aan welke adressen zijn gedefinieerd als DHCP-adressen.

  :::image type="content" source="media/how-to-control-what-traffic-is-monitored/populated-device-inventory-screen-v2.png" alt-text="Scherm opname van de inventaris van het apparaat.":::

- Het venster **Eigenschappen van apparaat** geeft aan of het apparaat is gedefinieerd als een DHCP-apparaat.

Een DHCP-adres bereik instellen:

1.  Selecteer **DHCP-bereiken** in het venster **systeem instellingen** in het menu aan de zijkant.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/dhcp-address-range-screen.png" alt-text="Scherm opname waarin de selectie van DHCP-bereiken wordt weer gegeven.":::

2.  Definieer een nieuw bereik door in te stellen **van** en **tot** waarden.

3.  Optioneel: Definieer de bereik naam, Maxi maal 256 tekens.

4.  Als u de bereiken wilt exporteren naar een CSV-bestand, selecteert u **exporteren**.

5. Als u hand matig meerdere bereiken uit een CSV-bestand wilt toevoegen, selecteert u **importeren** en selecteert u vervolgens het bestand.

    > [!NOTE]
    > De bereiken die u uit een CSV-bestand importeert, overschrijven de bestaande bereik instellingen.

6. Selecteer **Opslaan**.

## <a name="configure-dns-servers-for-reverse-lookup-resolution"></a>DNS-servers configureren voor reverse lookup-omzetting

Om de verrijking van het apparaat te verbeteren, kunt u meerdere DNS-servers configureren voor het carryout van reverse lookups. U kunt de hostnamen of FQDN-namen die zijn gekoppeld aan de IP-adressen die zijn gedetecteerd in de subnetten van het netwerk omzetten. Als een sensor bijvoorbeeld een IP-adres detecteert, kan deze een query uitvoeren op meerdere DNS-servers om de hostnaam om te zetten.

Alle CIDR-indelingen worden ondersteund.

De hostnaam wordt weer gegeven in de inventaris van apparaten, de apparaattoewijzing en in rapporten.

U kunt planningen voor reverse lookup plannen voor specifieke uurtarieven, zoals elke 12 uur. U kunt ook een specifieke tijd plannen.

DNS-servers definiëren:

1. Selecteer **systeem instellingen** en selecteer vervolgens **DNS-instellingen**.

2. Selecteer **DNS-server toevoegen**.

    :::image type="content" source="media/how-to-enrich-asset-information/dns-reverse-lookup-configuration-screen.png" alt-text="Scherm opname van de selectie van de DNS-server toevoegen.":::

3. Kies in het veld **Achterwaartse DNS-zoek opdracht plannen** een van de volgende opties:

    - Intervallen (per uur).
  
    - Een specifieke tijd. Gebruik Europese notatie. Gebruik bijvoorbeeld **14:30** en niet **2:30 pm**.

4. Voer in het veld **DNS-server adres** het DNS-IP-adres in.

5. In het veld **DNS-server poort** voert u de DNS-poort in.

6. De IP-adressen van het netwerk omzetten naar FQDN-apparaten. Voeg in het veld **aantal labels** het aantal domein labels toe dat moet worden weer gegeven. Van links naar rechts worden Maxi maal 30 tekens weer gegeven.

7. Voer in het veld **subnetten** de subnetten in waarvan u wilt dat de DNS-server een query uitvoert.

8. Selecteer de **Schakel optie inschakelen** als u de reverse lookup wilt initiëren.

### <a name="test-the-dns-configuration"></a>De DNS-configuratie testen 

Als u een test apparaat gebruikt, controleert u of de instellingen die u hebt gedefinieerd, goed werken:

1. Schakel de optie **DNS-zoek opdracht** in.

2. Selecteer **Testen**.

3. Voer een adres in het **adres voor zoeken** in voor het dialoog venster **test voor achterwaartse DNS-zoek opdracht voor Server** .

    :::image type="content" source="media/how-to-enrich-asset-information/dns-reverse-lookup-test-screen.png" alt-text="Scherm opname van het adres gebied voor lookup.":::

4. Selecteer **Testen**.

## <a name="configure-windows-endpoint-monitoring"></a>Windows-eindpunt bewaking configureren

Met de Windows endpoint-bewakings mogelijkheid kunt u Azure Defender voor IoT configureren om Windows-systemen selectief te testen. Dit biedt u meer gerichte en nauw keurige informatie over uw apparaten, zoals Service Pack-niveaus.

U kunt probing configureren met specifieke bereiken en hosts en zo configureren dat deze alleen zo vaak als gewenst kan worden uitgevoerd. U kunt selectief zoeken uitvoeren met behulp van de Windows Management Instrumentation (WMI), de standaard script taal van micro soft voor het beheren van Windows-systemen.

> [!NOTE]
> - U kunt slechts één scan tegelijk uitvoeren.
> - U krijgt de beste resultaten bij gebruikers met domein-of lokale beheerders bevoegdheden.
> - Voordat u de WMI-configuratie start, configureert u een firewall regel waarmee uitgaand verkeer van de sensor naar het gescande subnet wordt geopend met behulp van UDP-poort 135 en alle TCP-poorten hierboven 1024.

Wanneer de test is voltooid, is een logboek bestand met alle zoek pogingen beschikbaar via de optie om een logboek te exporteren. Het logboek bevat alle IP-adressen die zijn gecontroleerd. Voor elk IP-adres bevat het logboek informatie over geslaagde en mislukte pogingen. Er is ook een fout code, een gratis reeks die is afgeleid van de uitzonde ring. De controle van het laatste logboek wordt alleen in het systeem bewaard.

U kunt geplande scans of hand matige scans uitvoeren. Wanneer een scan is voltooid, kunt u de resultaten bekijken in een CSV-bestand.

**Vereisten**

Configureer een firewall regel waarmee uitgaand verkeer van de sensor naar het gescande subnet wordt geopend met behulp van UDP-poort 135 en alle TCP-poorten hierboven 1024.

Een automatische scan configureren:

1. Selecteer **systeem instellingen** in het menu aan de zijkant.

2. Selecteer **Windows endpoint-bewaking** :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false"::: .

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Scherm opname van de selectie van Windows-eindpunt bewaking.":::

3. Configureer de opties in het deel venster **scan schema** als volgt:

      - **Op vaste intervallen (in uren)**: Stel de scan planning in op basis van intervallen in uren.

      - **Op specifieke tijdstippen**: Stel de scan planning in volgens specifieke tijden en selecteer **Scan opslaan**.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/schedule-a-scan-screen-v2.png" alt-text="Scherm afbeelding met de knop voor het opslaan van de scan.":::

4. Als u het Scan bereik wilt definiëren, selecteert u **Scan bereik instellen**.

5. Stel het IP-adres bereik in en voeg uw gebruikers-en wacht woord toe.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-scan-range-screen.png" alt-text="Scherm opname van het toevoegen van een gebruiker en wacht woord.":::

6. Selecteer **uitschakelen** naast het bereik om een IP-bereik uit te sluiten van een scan.

7. Als u een bereik wilt verwijderen, selecteert :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/remove-scan-icon.png" border="false"::: u naast het bereik.

8. Selecteer **Opslaan**. Het dialoog venster **configuratie bereiken bewerken** wordt gesloten en het aantal bereiken wordt weer gegeven in het deel venster **bereik van scans** .

Een hand matige scan uitvoeren:

1. Selecteer **systeem instellingen** in het menu aan de zijkant.

2. Selecteer **Windows endpoint-bewaking** :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false"::: .

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Scherm afbeelding van het configuratie scherm voor Windows-eindpunt bewaking.":::

3. Selecteer in het deel venster **acties** de optie **scan starten**. Er wordt een status balk weer gegeven in het deel venster **acties** en toont de voortgang van het scan proces.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/started-scan-screen-v2.png" alt-text="Scherm afbeelding met de knop scan starten.":::

Scan resultaten weer geven:

1. Wanneer de scan is voltooid, selecteert u in het deel venster **acties** de optie **scan resultaten weer geven**. Het CSV-bestand met de scan resultaten wordt gedownload naar uw computer.

## <a name="see-also"></a>Zie ook

[Sensor detecties onderzoeken in een inventaris](how-to-investigate-sensor-detections-in-a-device-inventory.md) 
 van een apparaat [Detectie van sensors in de apparaattoewijzing onderzoeken](how-to-work-with-the-sensor-device-map.md)
