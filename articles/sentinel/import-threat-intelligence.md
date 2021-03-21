---
title: Bedreigings informatie importeren in azure Sentinel | Microsoft Docs
description: Gebruik Threat Intelligence-feeds in azure Sentinel voor het analyseren van gegevens, het detecteren van bedreigingen en het genereren van waarschuwingen en incidenten. Informatie over Threat Intelligence met werkmappen visualiseren.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2020
ms.author: yelevin
ms.openlocfilehash: 0b2a6088effc735076d56ba83dd85135392147b8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574986"
---
# <a name="import-threat-intelligence-into-azure-sentinel"></a>Bedreigingsinformatie importeren in Azure Sentinel

## <a name="introduction-to-threat-intelligence"></a>Inleiding tot bedreigings informatie

CTI (Cyber Threat Intelligence) is informatie die bekende bestaande of mogelijke dreigingen voor systemen en gebruikers beschrijft. Dit type informatie neemt veel formulieren in beslag op basis van geschreven rapporten, van de motivaties, infra structuur en technieken van een bepaalde bedreigings actor, tot specifieke waarnemingen van IP-adressen, domeinen en bestands-hashes die zijn gekoppeld aan Cyber bedreigingen. CTI wordt door organisaties gebruikt voor het leveren van essentiële context voor ongebruikelijke activiteiten, zodat beveiligings personeel snel actie kan ondernemen om hun mensen en activa te beveiligen. CTI kan worden gebrond op vele locaties, zoals open source-gegevensfeeds, organisaties voor het delen van bedreigingen, commerciële informatie-feeds en lokale intelligentie die zijn verzameld in de loop van beveiligings onderzoek binnen een organisatie.

Binnen een SIEM-oplossing (Security Information and Event Management) zoals Azure Sentinel is de meest gebruikte vorm van CTI risico indicatoren, vaak aangeduid als Indica tors van inbreuk of IoCs. Bedreigings indicatoren zijn gegevens die waarnemingen koppelen, zoals Url's, bestands-hashes of IP-adressen met bekende bedreigings activiteiten, zoals phishing, botnets of malware. Deze vorm van bedreigings informatie wordt vaak tactische Threat Intelligence genoemd, omdat deze kan worden toegepast op beveiligings producten en automatisering op grote schaal om mogelijke dreigingen voor een organisatie te beveiligen en te detecteren. In azure Sentinel kunt u bedreigings indicatoren gebruiken om te helpen schadelijke activiteiten te detecteren die in uw omgeving worden waargenomen en context te bieden aan beveiligings onderzoekers om antwoord beslissingen te informeren.

U kunt Threat Intelligence (TI) integreren in azure Sentinel via de volgende activiteiten:

- Gebruik **Data connectors** voor verschillende Ti-platforms om [bedreigings informatie te importeren](./connect-threat-intelligence.md) in azure Sentinel.
- Bekijk en beheer de geïmporteerde bedreigings informatie in **Logboeken** en in het nieuwe gebied **bedreigings informatie** van Azure Sentinel.
- Gebruik de ingebouwde **analyse** regel sjablonen om beveiligings waarschuwingen en incidenten te genereren met behulp van uw geïmporteerde bedreigings informatie.
- Visualiseren van belang rijke gegevens over uw bedreigings informatie in azure Sentinel met de **Threat Intelligence-werkmap**.

Threat Intelligence biedt ook nuttige context binnen andere onderverklikker ervaringen van Azure, zoals **jacht** en **notebooks**, en hoewel dit niet in dit artikel wordt behandeld, worden deze ervaringen behandeld in [dit fantastische blog bericht door Ian hellen op Jupyter-notebooks in azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/using-threat-intelligence-in-your-jupyter-notebooks/ba-p/860239), dat betrekking heeft op het gebruik van CTI in notitie blokken.

## <a name="azure-sentinel-data-connectors-for-threat-intelligence"></a>Azure Sentinel data connectors voor bedreigings informatie

Net als alle andere gebeurtenis gegevens in de Azure-Sentinel worden risico indicatoren geïmporteerd met behulp van gegevens connectors. Er zijn twee gegevens connectors in azure Sentinel die specifiek worden gegeven voor bedreigings indicatoren, **Threat Intelligence-TAXII** en **Threat Intelligence-platforms**. U kunt alleen de gegevens connector gebruiken, of beide connectors samen, afhankelijk van de locatie van de bedreigings indicatoren van uw organisatie. Laten we praten over elk van de gegevens connectors.

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence-platforms-data-connector"></a>Risico indicatoren toevoegen aan Azure Sentinel met de gegevens connector van het bedreigings informatie platform

Veel organisaties gebruiken oplossingen voor het maken van bedreigings informatie (TIP) voor het verzamelen van de gegevens in het platform en vervolgens om te kiezen welke bedreigings indicatoren moeten worden toegepast op verschillende beveiligings oplossingen, zoals netwerk apparaten, oplossingen voor geavanceerde dreigingen of Siem's zoals Azure Sentinel. Als uw organisatie gebruikmaakt van een geïntegreerde TIP-oplossing, zoals MISP, Anomali ThreatStream, ThreatConnect, EclecticIQ platform, ThreatQ Threat Intelligence platform of Palo Alto Networks MineMeld, kunt u met de **Data Connector van de Threat Intelligence-platformen** uw tip gebruiken om bedreigings indicatoren te importeren in azure Sentinel. Omdat de connector samenwerkt met de [Microsoft Graph Security TIINDICATORS API](/graph/api/resources/tiindicator) om dit te bewerkstelligen, kan de connector ook worden gebruikt door een aangepast Threat Intelligence-platform om te profiteren van de TIINDICATORS-API om indica toren te verzenden naar Azure Sentinel (en naar andere micro soft-beveiligings oplossingen zoals Defender ATP).

:::image type="content" source="media/import-threat-intelligence/threat-intel-import-path.png" alt-text="Pad naar Threat Intelligence-import bewerking":::

Volg deze stappen om bedreigings indicatoren te importeren in azure Sentinel van uw geïntegreerde TIP of een aangepaste oplossing voor bedreigings informatie:

1. Een toepassings-ID en client geheim verkrijgen van uw Azure Active Directory

1. Deze informatie invoeren in uw TIP-oplossing of aangepaste toepassing

1. De gegevens connector voor bedreigings informatie platformen inschakelen in azure Sentinel

Hier volgt een gedetailleerde beschrijving van elk van deze stappen.

#### <a name="obtain-an-application-id-and-client-secret-from-your-azure-active-directory"></a>Een toepassings-ID en client geheim verkrijgen van uw Azure Active Directory

Of u nu werkt met een TIP of met een aangepaste oplossing, de tiIndicators-API vereist enkele basis informatie om verbinding te maken en bedreigings indicatoren te verzenden. De drie stukjes informatie die u moet verkrijgen, zijn:
- (Client-)id van de app
- (Tenant-)id van de map
- Clientgeheim

Deze informatie is altijd afkomstig van uw Azure Active Directory via een proces dat **app-registratie** wordt genoemd. Dit omvat de volgende drie stappen:
- Een app registreren bij Azure Active Directory
- Geef de machtigingen op die de app nodig heeft om verbinding te maken met de Microsoft Graph tiIndicators-API en bedreigings indicatoren te verzenden
- Vraag toestemming van uw organisatie om deze machtigingen te verlenen aan deze toepassing.  

**Een toepassing registreren met Azure Active Directory**

1. Open de [Azure Portal](https://portal.azure.com/) en ga naar de **Azure Active Directory** -service.

1. Selecteer **app-registraties** in het menu en selecteer **nieuwe registratie**.

1. Kies een naam voor de registratie van uw toepassing, selecteer het keuze rondje **Eén Tenant** en selecteer **registreren**.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-register-application.png" alt-text="Een toepassing registreren":::

1. Kopieer vanuit het resulterende scherm de waarden van de **toepassing (client) ID** en **Directory (Tenant)** . Dit zijn de eerste twee stukjes informatie die u later nodig hebt om uw TIP of aangepaste oplossing te configureren voor het verzenden van bedreigings indicatoren naar Azure Sentinel.

**De vereiste machtigingen voor de toepassing opgeven**

1. Open de [Azure Portal](https://portal.azure.com/) en ga naar de **Azure Active Directory** -service.

1. Selecteer **app-registraties** in het menu en selecteer de zojuist geregistreerde app.

1. Selecteer **API-machtigingen** in het menu en klik op de knop **een machtiging toevoegen** .

1. Selecteer op de pagina **een API selecteren** **Microsoft Graph** om te kiezen uit een lijst met Microsoft Graph machtigingen.

1. Wanneer **wordt gevraagd welk type machtigingen uw toepassing vereist?** Selecteer **toepassings machtigingen**. Dit is het type machtigingen dat wordt gebruikt door toepassingen die worden geverifieerd met App-ID en app-geheimen (API-sleutels).

1. Selecteer **ThreatIndicators. readwrite. OwnedBy** en selecteer **machtigingen toevoegen** om deze machtiging toe te voegen aan de lijst met machtigingen van uw app.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-1.png" alt-text="Machtigingen opgeven":::

**Toestemming van uw organisatie verkrijgen om deze machtigingen te verlenen**

1. Als u toestemming wilt geven, moet u een Azure Active Directory globale beheerder zijn om de knop **toestemming geven voor het verlenen van de beheerder voor uw Tenant** te selecteren op de pagina API-machtigingen van uw app. Als u niet beschikt over de rol van globale beheerder voor uw account, is deze knop niet beschikbaar en moet u een globale beheerder van uw organisatie vragen om deze stap uit te voeren.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-2.png" alt-text="Toestemming verlenen":::

1. Zodra de toestemming voor uw app is verleend, ziet u een groen vinkje onder **status**.
 
Nu de app is geregistreerd en er machtigingen zijn verleend, kunt u het laatste van uw lijst ophalen: een client geheim voor uw app.

1. Open de [Azure Portal](https://portal.azure.com/) en ga naar de **Azure Active Directory** -service.

1. Selecteer **app-registraties** in het menu en selecteer de zojuist geregistreerde app.

1. Selecteer **certificaten & geheimen** in het menu en klik op de knop **Nieuw client geheim** om een geheim (API-sleutel) voor uw app te verkrijgen.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-client-secret.png" alt-text="Client geheim ophalen":::

1. Klik op de knop **toevoegen** en **Zorg ervoor dat u het client geheim kopieert**, omdat u dit geheim niet opnieuw kunt ophalen als u deze pagina verlaat. U hebt deze waarde nodig wanneer u uw TIP of een aangepaste oplossing configureert.

#### <a name="input-this-information-into-your-tip-solution-or-custom-application"></a>Deze informatie invoeren in uw TIP-oplossing of aangepaste toepassing

U hebt nu alle drie stukjes informatie die u nodig hebt om uw TIP of aangepaste oplossing te configureren voor het verzenden van bedreigings indicatoren naar Azure Sentinel. 
- (Client-)id van de app
- (Tenant-)id van de map
- Clientgeheim

Voer deze waarden in de configuratie van uw geïntegreerde TIP of aangepaste oplossing, waar nodig, en bedreigings indicatoren worden verzonden via de Microsoft Graph tiIndicators API gericht op Azure Sentinel.

#### <a name="enable-the-threat-intelligence-platforms-data-connector-in-azure-sentinel"></a>De gegevens connector voor bedreigings informatie platformen inschakelen in azure Sentinel

De laatste stap in het integratie proces is het inschakelen van de gegevens connector voor **bedreigings informatie platformen** in azure Sentinel. Dit is de stap waarmee de bedreigings indicatoren worden geïmporteerd die vanuit uw TIP of aangepaste oplossing via de Microsoft Graph tiIndicators API in azure Sentinel worden verzonden. Deze indica toren zijn beschikbaar voor alle Azure Sentinel-werk ruimten voor uw organisatie. Volg deze stappen voor het inschakelen van de gegevens connector voor bedreigings informatie platformen voor elke werk ruimte:

1. Open de [Azure Portal](https://portal.azure.com/) en navigeer naar de **Azure-Sentinel** -service.

1. Kies de **werk ruimte** waarnaar u de bedreigings indicatoren wilt importeren die worden verzonden via uw tip of een aangepaste oplossing.

1. Selecteer **gegevens connectors** in het menu, selecteer **bedreigingen van dreigingen** in de galerie connectors en klik op de knop **connector pagina openen** .

1. Wanneer u de app-registratie al hebt voltooid en uw TIP of aangepaste oplossing hebt geconfigureerd voor het verzenden van bedreigings indicatoren, kunt u ook op de knop **verbinden** klikken.

Binnen een paar minuten moeten bedreigings indicatoren stromen naar deze Azure Sentinel-werk ruimte.

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence---taxii-data-connector"></a>Bedreigings indicatoren toevoegen aan Azure Sentinel met de gegevens connector Threat Intelligence-TAXII

De meest wereld wijd gekozen industrie norm voor de verzen ding van bedreigings informatie is een [combi natie van de Stix-gegevens indeling en het TAXII-protocol](https://oasis-open.github.io/cti-documentation/). Als uw organisatie bedreigings indicatoren krijgt van oplossingen die de huidige STIX/TAXII-versie ondersteunen (2,0 of 2,1), kunt u de gegevens connector **Threat Intelligence-TAXII** gebruiken om uw bedreigings indicatoren in azure Sentinel te brengen. Met de TAXII-gegevens connector van Threat Intelligence kunt u een ingebouwde TAXII-client in azure Sentinel voor het importeren van bedreigings informatie van TAXII 2. x-servers.

:::image type="content" source="media/import-threat-intelligence/threat-intel-taxii-import-path.png" alt-text="Pad naar TAXII-import":::
 
Voer de volgende stappen uit om de STIX-opmaak bedreigingen te importeren in azure Sentinel van een TAXII-server:

1. De TAXII Server API root en de verzamelings-ID ophalen

1. De bedreigings informatie-TAXII Data Connector inschakelen in azure Sentinel

Laten we nu een gedetailleerde kijkje nemen bij elk van deze stappen.

#### <a name="obtain-the-taxii-server-api-root-and-collection-id"></a>De TAXII Server API root en de verzamelings-ID ophalen

TAXII 2. x-servers kondigen api's van de API. Dit zijn Url's die als host optreden voor verzamelingen van bedreigings informatie. De meeste API root kan worden verkregen via de documentatie pagina van de Threat Intelligence-provider die als host fungeert voor de TAXII-server. Soms is de enige geadverteerde informatie echter een URL die een detectie-eind punt wordt genoemd. Als dit het geval is, kunt u eenvoudig de API-hoofdmap vinden met het detectie-eind punt. Als u al bekend bent met de TAXII Server API root en verzameling-Id's waarmee u wilt werken, kunt u de volgende sectie overs Laan om **de gegevens connector Threat Intelligence-TAXII in te scha kelen in azure Sentinel**.

Laten we eens kijken naar een voor beeld van het gebruik van een eenvoudig opdracht regel programma met de naam [krul](https://en.wikipedia.org/wiki/CURL), dat is opgenomen in Windows en de meeste Linux-distributies, om de API-hoofdmap te ontdekken en te bladeren door de verzamelingen van een TAXII-server die alleen vanaf het detectie-eind punt wordt gestart. In dit voor beeld gebruiken we het detectie-eind punt van de [Anomali Limo](https://www.anomali.com/community/limo) ThreatStream TAXII 2,0-server.

1.  Ga vanuit een browser naar het [THREATSTREAM TAXII 2,0 server Discovery-eind punt](https://limo.anomali.com/taxii) om de API-hoofdmap op te halen. Verifieer met de gebruiker en het wacht woord `guest` .

    ```json
    {
        "api_roots":
        [
            "https://limo.anomali.com/api/v1/taxii2/feeds/",
            "https://limo.anomali.com/api/v1/taxii2/trusted_circles/",
            "https://limo.anomali.com/api/v1/taxii2/search_filters/"
        ],
        "contact": "info@anomali.com",
        "default": "https://limo.anomali.com/api/v1/taxii2/feeds/",
        "description": "TAXII 2.0 Server (guest)",
        "title": "ThreatStream Taxii 2.0 Server"
    }
    ```

2.  Gebruik het krul hulp programma en de API-hoofdmap ( https://limo.anomali.com/api/v1/taxii2/feeds/) in de vorige stap om te bladeren door de lijst met verzamelings-id's die worden gehost op de API-basis)

    ```json
    curl -u guest https://limo.anomali.com/api/v1/taxii2/feeds/collections/
    {
        "collections":
        [
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "107",
                "title": "Phish Tank"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "135",
                "title": "Abuse.ch Ransomware IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "136",
                "title": "Abuse.ch Ransomware Domains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "150",
                "title": "DShield Scanning IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "200",
                "title": "Malware Domain List - Hotlist"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "209",
                "title": "Blutmagie TOR Nodes"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "31",
                "title": "Emerging Threats C&C Server"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "33",
                "title": "Lehigh Malwaredomains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "41",
                "title": "CyberCrime"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "68",
                "title": "Emerging Threats - Compromised"
            }
        ]
    }
    ```

U hebt nu alle informatie die u nodig hebt om Azure Sentinel te verbinden met een of meer TAXII-server verzamelingen die worden verstrekt door Anomali Limo.

| **API-basis** (https://limo.anomali.com/api/v1/taxii2/feeds/) | Verzamelings-id |
| ------------------------------------------------------------ | ------------: |
| **Phishing tank**                                               | 107           |
| **Abuse.ch Ransomware IPs**                                  | 135           |
| **Abuse.ch Ransomware-domeinen**                              | 136           |
| **DShield scan IPs**                                     | 150           |
| **Lijst met schadelijke Domeins-hotlist**                            | 200           |
| **Blutmagie TOR-knoop punten**                                      | 209           |
| **Opkomende bedreigingen C&C server**                              |  31           |
| **Lehigh Malwaredomains**                                    |  33           |
| **Cyber Crime**                                               |  41           |
| **Opkomende bedreigingen-aangetast**                           |  68           |
|

#### <a name="enable-the-threat-intelligence---taxii-data-connector-in-azure-sentinel"></a>De bedreigings informatie-TAXII Data Connector inschakelen in azure Sentinel

Als u bedreigings indicatoren wilt importeren in azure Sentinel van een TAXII-server, voert u de volgende stappen uit:

1. Open de [Azure Portal](https://portal.azure.com/) en navigeer naar de **Azure-Sentinel** -service.

1. Kies de **werk ruimte** waarin u bedreigings indicatoren wilt importeren van de TAXII-server.

1. Selecteer **gegevens connectors** in het menu, selecteer **Threat Intelligence-TAXII** in de galerie connectors en klik op de knop **connector pagina openen** .

1. Typ een **naam** voor deze TAXII-server verzameling, URL van de **API-hoofdmap**, **verzamelings-id**, **gebruikers naam** (indien nodig) en **wacht woord** (indien nodig) en klik op de knop **toevoegen** .

    :::image type="content" source="media/import-threat-intelligence/threat-intel-configure-taxii-servers.png" alt-text="TAXII-servers configureren":::
 
U ontvangt een bevestiging dat er een verbinding met de TAXII-server is gemaakt en u kunt stap (4) zo vaak herhalen als gewenst om verbinding te maken met meerdere verzamelingen vanaf dezelfde of verschillende TAXII-servers.

## <a name="view-your-threat-indicators-in-azure-sentinel"></a>Bekijk uw bedreigings indicatoren in azure Sentinel

Nu u risico indicatoren hebt geïmporteerd in azure Sentinel met behulp van de **Threat Intelligence-platforms** en/of **de Threat Intelligence-TAXII** Data Connector, kunt u ze weer geven in de **ThreatIntelligenceIndicator** -tabel in **Logboeken** waarin al uw gebeurtenis gegevens van Azure-Sentinel worden opgeslagen. Deze tabel vormt de basis voor query's die worden uitgevoerd door andere Azure Sentinel-functies, zoals Analytics en Workbooks. U kunt als volgt uw bedreigings indicatoren vinden en weer geven in de **ThreatIntelligenceIndicator** -tabel.

1. Open de [Azure Portal](https://portal.azure.com/) en navigeer naar de **Azure-Sentinel** -service.

1. Kies de **werk ruimte** waarvoor u bedreigings indicatoren hebt geïmporteerd met behulp van de Threat Intelligence Data-Connector.

1. Selecteer **Logboeken** in de sectie **Algemeen** van het Sentinel-menu van Azure.

1. De **ThreatIntelligenceIndicator** -tabel bevindt zich in de **Azure-Sentinel** -groep.

1. Selecteer het pictogram **Preview-gegevens** (het oog) naast de tabel naam en selecteer de knop **weer geven in query-editor** om een query uit te voeren waarmee records uit deze tabel worden weer gegeven.

De resultaten moeten er ongeveer uitzien als de volgende voor beeld van bedreigings indicator:

:::image type="content" source="media/import-threat-intelligence/threat-intel-sample-query.png" alt-text="Voorbeeld query gegevens":::
 
## <a name="manage-your-threat-indicators-in-the-new-threat-intelligence-area-of-azure-sentinel"></a>Uw bedreigings indicatoren beheren in het nieuwe bedreigings informatie gebied van Azure Sentinel

Met het nieuwe gebied voor **bedreigings informatie** , dat toegankelijk is vanuit het menu voor Sentinel van Azure, kunt u ook uw geïmporteerde bedreigings indicatoren weer geven, sorteren, filteren en doorzoeken zonder een **Logboeken** -query te schrijven. Met dit nieuwe gebied kunt u ook op de Azure-Sentinel-interface bedreigings indicatoren maken en algemene Threat Intelligence-beheer taken uitvoeren, zoals het coderen van indica toren en het maken van nieuwe Indica tors met betrekking tot beveiligings onderzoek.
Laten we eens kijken naar twee van de meest voorkomende taken, het maken van nieuwe risico indicatoren en label indicatoren voor een eenvoudige groepering en verwijzing.

1. Open de [Azure Portal](https://portal.azure.com/) en navigeer naar de **Azure-Sentinel** -service.

1. Kies de **werk ruimte** waarvoor u bedreigings indicatoren hebt geïmporteerd met behulp van de Threat Intelligence Data-Connector.

1. Selecteer **Threat Intelligence** in het gedeelte Threat Management van het onderverklikker menu van Azure.

1. Selecteer de knop **Nieuw toevoegen** in het bovenste menu van de pagina.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-add-new-indicator.png" alt-text="Een nieuwe bedreigings indicator toevoegen" lightbox="media/import-threat-intelligence/threat-intel-add-new-indicator.png":::

1. Kies het indicator type en vul vervolgens de vereiste velden in die zijn gemarkeerd met een rood sterretje (*) in het deel venster **nieuwe indicator** .

1. Selecteer **Toepassen**. De indicator wordt toegevoegd aan het Indica tors raster en wordt ook verzonden naar de ThreatIntelligenceIndicator-tabel in **Logboeken**.

Het coderen van bedreigings indicatoren is een eenvoudige manier om ze samen te groeperen, zodat u ze eenvoudiger kunt vinden. Normaal gesp roken kunt u een tag Toep assen op Indica tors die betrekking hebben op een bepaald incident of op indica toren die bedreigingen vertegenwoordigen van een bepaalde bekende actor of een bekende aanvals campagne. U kunt risico indicatoren afzonderlijk labelen of meervoudige indica toren en ze allemaal tegelijk labelen. Hieronder ziet u een voor beeld van het coderen van meerdere indica toren met een incident-ID. Omdat labels gratis zijn, is het raadzaam om standaard naam conventies voor bedreigings indicator Tags te maken. U kunt meerdere labels Toep assen op elke indicator.

:::image type="content" source="media/import-threat-intelligence/threat-intel-tagging-indicators.png" alt-text="Tags Toep assen op bedreigings indicatoren" lightbox="media/import-threat-intelligence/threat-intel-tagging-indicators.png":::

## <a name="analytics-puts-your-threat-indicators-to-work-detecting-potential-threats"></a>Met Analytics worden uw bedreigings indicatoren gebruikt voor het detecteren van mogelijke dreigingen

U hebt uw bedreigings indicatoren ingevoerd in azure Sentinel; u hebt gezien hoe u ze kunt weer geven en beheren. Bekijk nu wat ze voor u kunnen doen. De belangrijkste gebruiks Case voor bedreigings indicatoren in SIEM-oplossingen zoals Azure Sentinel is voor Power Analytics-regels.  Deze regels op basis van een indicator vergelijken onbewerkte gebeurtenissen uit uw gegevens bronnen tegen uw bedreigings indicatoren om beveiligings dreigingen in uw organisatie te detecteren. In azure Sentinel **Analytics** maakt u Analytics-regels die volgens een planning worden uitgevoerd en beveiligings waarschuwingen genereren. De regels worden aangedreven door query's, samen met configuraties die bepalen hoe vaak de regel moet worden uitgevoerd, welke soort query resultaten beveiligings waarschuwingen moeten genereren en welke automatische antwoorden moeten worden geactiveerd wanneer er waarschuwingen worden gegenereerd.

Hoewel u altijd nieuwe analyse regels kunt maken, biedt Azure Sentinel een reeks ingebouwde regel sjablonen, gemaakt door micro soft-beveiligings technici, die u kunt gebruiken om te voldoen aan uw behoeften. U kunt de regel sjablonen die gebruikmaken van bedreigings indicatoren, gemakkelijk identificeren, aangezien ze allemaal beginnen met '**Ti map**... '. Al deze regel sjablonen worden op dezelfde manier uitgevoerd, met het enige verschil dat het type risico indicatoren wordt gebruikt (domein, e-mail, bestands-hash, IP-adres of URL) en welk gebeurtenis type moet overeenkomen. Elke sjabloon bevat een lijst met de vereiste gegevens bronnen die nodig zijn om de regel te kunnen gebruiken. u kunt in één oogopslag zien of u de benodigde gebeurtenissen al hebt geïmporteerd in azure Sentinel.

Laten we eens kijken naar een van deze regel sjablonen en instructies voor het inschakelen en configureren van de regel voor het genereren van beveiligings waarschuwingen met behulp van de bedreigings indicatoren die u in azure Sentinel hebt geïmporteerd. In dit voor beeld gebruiken we de regel sjabloon met de naam **Ti toewijzen IP-entiteit aan AzureActivity**. Deze regel komt overeen met een bedreigings indicator van het IP-adres type met al uw Azure-activiteiten gebeurtenissen. Als er een overeenkomst wordt gevonden, wordt er een **waarschuwing** gegenereerd, evenals een bijbehorend **incident** voor onderzoek door uw beveiligings team. Deze analyse regel werkt alleen als u een of beide van de **Threat Intelligence** -gegevens connectors (voor het importeren van bedreigings indicatoren) en de gegevens connector van **Azure activity** (voor het importeren van uw Azure-gebeurtenissen op abonnements niveau) hebt ingeschakeld.

1. Open de [Azure Portal](https://portal.azure.com/) en navigeer naar de **Azure-Sentinel** -service.

1. Kies de **werk ruimte** waarin u bedreigings indicatoren hebt geïmporteerd met behulp van de gegevens connectors voor **bedreigings informatie** en Azure-activiteit met behulp van de **Azure activity** data.

1. Selecteer **analyses** in de sectie **configuratie** van het Sentinel-menu van Azure.

1. Selecteer het tabblad **regel sjablonen** om de lijst met beschik bare analyse regel sjablonen te bekijken.

1. Navigeer naar de regel met de naam **map IP-entiteit toewijzen aan AzureActivity** en zorg ervoor dat u alle vereiste gegevens bronnen hebt verbonden, zoals hieronder wordt weer gegeven.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-required-data-sources.png" alt-text="Vereiste gegevens bronnen":::

1. Selecteer deze regel en selecteer de knop **regel maken** . Hiermee opent u een wizard om de regel te configureren. Vul de instellingen hier in en selecteer de **volgende: regel logica instellen >** knop.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-create-analytics-rule.png" alt-text="Analytics-regel maken":::

1. Het gedeelte regel logica van de wizard bevat:
    - De query die wordt gebruikt in de regel.

    - Entiteits toewijzingen, die de Azure-Sentinel vertellen hoe entiteiten zoals accounts, IP-adressen en Url's worden herkend, zodat **incidenten** en **onderzoek** inzicht krijgen in het werken met de gegevens in beveiligings waarschuwingen die door deze regel worden gegenereerd.

    - Het schema voor het uitvoeren van deze regel.

    - Het aantal query resultaten dat is vereist voordat een beveiligings waarschuwing wordt gegenereerd.

    De standaard instellingen in de sjabloon zijn:
    - Eén keer per uur uitvoeren.

    - Komen overeen met de risico indicatoren van IP-adressen uit de tabel **ThreatIntelligenceIndicator** , met elk IP-adres dat in de laatste één uur aan gebeurtenissen in de **AzureActivity** -tabel is gevonden.

    - Genereer een beveiligings waarschuwing als de query resultaten groter zijn dan nul, wat betekent dat er overeenkomsten worden gevonden.

U kunt de standaard instellingen laten staan of een van deze wijzigen om te voldoen aan uw vereisten. Wanneer u klaar bent, selecteert u de **volgende: knop automatisch antwoord >**

1. In deze stap van de wizard kunt u alle automatisering configureren die u wilt activeren wanneer er een beveiligings waarschuwing wordt gegenereerd op basis van deze analyse regel. Automatisering in azure Sentinel wordt uitgevoerd met behulp van **Playbooks**, aangedreven door Azure Logic apps. Meer informatie vindt u in deze [zelf studie: automatische antwoorden op dreigingen instellen in azure Sentinel](./tutorial-respond-threats-playbook.md). In dit voor beeld selecteren we alleen de knop **volgende: controleren >** om door te gaan.

1. Met deze laatste stap valideert u de instellingen in de regel. Wanneer u klaar bent om de regel in te scha kelen, selecteert u de knop **maken** en bent u klaar.

Nu u uw analyse regel hebt ingeschakeld, kunt u de ingeschakelde regel vinden op het tabblad **actieve regels** van het gedeelte **analyse** van Azure Sentinel. U kunt de actieve regel bewerken, inschakelen, uitschakelen, dupliceren of verwijderen. De nieuwe regel wordt direct na activering uitgevoerd en wordt vervolgens uitgevoerd volgens het gedefinieerde schema.

Op basis van de standaard instellingen, elke keer dat de regel volgens het schema wordt uitgevoerd, genereren alle gevonden resultaten een beveiligings waarschuwing. Beveiligings waarschuwingen in azure Sentinel kunnen worden weer gegeven in de sectie **logs** van Azure Sentinel, in de tabel **SecurityAlert** onder de groep **Azure-Sentinel** .

In azure Sentinel genereren de waarschuwingen die worden gegenereerd op basis van analyse regels ook beveiligings incidenten die kunnen worden gevonden in **incidenten** onder **Threat Management** in het Sentinel-menu van Azure. Incidenten zijn wat uw sorteren en onderzoek doen om de juiste reactie acties te bepalen. In deze zelf studie vindt u gedetailleerde informatie over het [onderzoeken van incidenten met Azure Sentinel](./tutorial-investigate-cases.md).

## <a name="workbooks-provide-insights-about-your-threat-intelligence"></a>Werkmappen bieden inzichten over uw bedreigings informatie

Ten slotte kunt u een Azure Sentinel-werkmap gebruiken om belang rijke informatie over uw bedreigings intelligentie in azure Sentinel te visualiseren, en u kunt de werkmappen eenvoudig aanpassen op basis van de behoeften van uw bedrijf.
Laten we eens kijken hoe u de Threat Intelligence-werkmap kunt vinden die is opgenomen in azure Sentinel, en we laten ook zien hoe u wijzigingen kunt aanbrengen in de werkmap om deze aan te passen.

1. Open de [Azure Portal](https://portal.azure.com/) en navigeer naar de **Azure-Sentinel** -service.

1. Kies de **werk ruimte** waarvoor u bedreigings indicatoren hebt geïmporteerd met behulp van de Threat Intelligence Data-Connector.

1. Selecteer **werkmappen** in het gedeelte **Threat Management** van het menu Sentinel van Azure.

1. Navigeer naar de werkmap met de titel **Threat Intelligence** en controleer of u gegevens hebt in de tabel **ThreatIntelligenceIndicator** , zoals hieronder wordt weer gegeven.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-verify-data.png" alt-text="Gegevens controleren":::
 
1. Selecteer de knop **Opslaan** en kies een Azure-locatie om de werkmap op te slaan. Deze stap is vereist als u de werkmap op een wille keurige manier wilt wijzigen en uw wijzigingen wilt opslaan.

1. Selecteer nu de knop **opgeslagen werkmap weer geven** om de werkmap te openen voor weer gave en bewerking.

1. Nu ziet u de standaard grafieken van de sjabloon. We gaan nu enkele wijzigingen aanbrengen in een van de grafieken. Selecteer de knop **bewerken** boven aan de pagina om de bewerkings modus voor de werkmap in te voeren.

1. Laten we een nieuw diagram van bedreigings indicatoren toevoegen op basis van het type bedreiging. Ga naar de onderkant van de pagina en selecteer query toevoegen.

1. Voeg de volgende tekst toe aan het tekstvak **log Analytics werkruimte logboek query** :
    ```kusto
    ThreatIntelligenceIndicator
    | summarize count() by ThreatType
    ```

1. Selecteer in de vervolg keuzelijst **visualisatie** de optie **staaf diagram**.

1. Selecteer de knop **bewerken is voltooid** . U hebt een nieuwe grafiek voor uw werkmap gemaakt.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-bar-chart.png" alt-text="Staaf diagram":::

Werkmappen bieden krachtige interactieve Dash boards die u inzicht geven in alle aspecten van Azure Sentinel. Er is een hele partij die u met werkmappen kunt doen, en terwijl de meegeleverde sjablonen een geweldig start punt zijn, wilt u waarschijnlijk de sjablonen in de hand brengen en aanpassen of nieuwe Dash boards maken die veel verschillende gegevens bronnen combi neren, zodat u uw gegevens op unieke wijze kunt visualiseren. Omdat Azure Sentinel-werkmappen zijn gebaseerd op Azure Monitor werkmappen, is er al uitgebreide documentatie beschikbaar en veel meer sjablonen. Een fantastische plek om te beginnen is dit artikel over het [maken van interactieve rapporten met Azure monitor-werkmappen](../azure-monitor/visualize/workbooks-overview.md). 

Er is ook een uitgebreide community van [Azure monitor werkmappen op github](https://github.com/microsoft/Application-Insights-Workbooks) waar u aanvullende sjablonen kunt downloaden en uw eigen sjablonen kunnen bijdragen.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd over de mogelijkheden van bedreigings informatie van Azure Sentinel en de nieuwe blade Threat Intelligence. Raadpleeg de volgende artikelen voor praktische richt lijnen voor het gebruik van de Threat Intelligence-mogelijkheden van Azure-Sentinel:

- [Gegevens van bedreigings informatie verbinden](./connect-threat-intelligence.md) met de Sentinel van Azure.
- Maak [ingebouwde](./tutorial-detect-threats-built-in.md) of [aangepaste](./tutorial-detect-threats-custom.md) waarschuwingen en [onderzoek](./tutorial-investigate-cases.md) incidenten in azure Sentinel.