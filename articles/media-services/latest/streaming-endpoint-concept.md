---
# <a name="mandatory-fields-see-more-on-akamsskyeyemeta"></a>Verplichte velden. Meer informatie over aka.ms/skyeye/meta.
Titel: streaming-eind punten (oorsprong): Azure Media Services beschrijving: meer informatie over streaming-eind punten (oorsprong), een dynamische verpakkings-en streaming-service die inhoud rechtstreeks levert aan een client speler of een Content Delivery Network (CDN). Services: Media Services documentationcenter: ' ' Auteur: IngridAtMicrosoft Manager: femila editor: ' '

MS. service: Media-Services MS. workload: MS. topic: conceptuele MS. date: 02/13/2020 MS. Author: inhenkel
---

# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Streaming-eind punten (oorsprong) in Azure Media Services

In Microsoft Azure Media Services vertegenwoordigt een [streaming-eind punt](/rest/api/media/streamingendpoints) een dynamische (just-in-time) verpakkings-en bron service die uw Live-en on-demand-inhoud rechtstreeks aan een client speler kan leveren met behulp van een van de algemene protocollen voor streaming media (HLS of Dash). Daarnaast biedt het **streaming-eind punt** dynamische code ring (just-in-time) voor toonaangevende DRMs. 

Wanneer u een Media Services account maakt, wordt er een **standaard** streaming-eind punt voor u gemaakt met de status gestopt. Er kunnen meer streaming-eind punten worden gemaakt onder het account (Zie [quota's en limieten](limits-quotas-constraints-reference.md)).

> [!NOTE]
> Als u Video's wilt streamen, moet u het **streaming-eind punt** starten van waaruit u de video wilt streamen.
>
> U wordt alleen gefactureerd wanneer uw streaming-eind punt de status actief heeft.

Controleer ook het onderwerp [dynamisch pakket](encode-dynamic-packaging-concept.md) . 

## <a name="naming-convention"></a>Naamgevings Conventie

De indeling van de hostnaam van de streaming-URL is: `{servicename}-{accountname}-{regionname}.streaming.media.azure.net` , waarbij `servicename` = de naam van het streaming-eind punt of de naam van de live-gebeurtenis.

Wanneer het standaard streaming-eind punt `servicename` wordt gebruikt, wordt wegge laten, zodat de URL: `{accountname}-{regionname}.streaming.azure.net` .

### <a name="limitations"></a>Beperkingen

* De naam van het streaming-eind punt heeft een maximale waarde van 24 tekens.
* De naam moet volgen op dit [regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) -patroon: `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$` .

## <a name="types"></a>Typen

Er zijn twee typen **streaming-eind punten** : **Standard** (preview) en **Premium**. Het type wordt gedefinieerd door het aantal schaal eenheden ( `scaleUnits` ) dat u toewijst voor het streaming-eind punt.

De maximale limiet voor de streaming-eenheid is doorgaans 10. Neem [hier](https://azure.microsoft.com/support/create-ticket/) contact met ons op om de limiet voor uw account te verhogen.

In de tabel worden de typen beschreven:

|Type|Schaaleenheden|Description|
|--------|--------|--------|  
|**Standard**|0|Het standaard streaming-eind punt is een **standaard** type. Dit kan worden gewijzigd in het Premium-type door de aanpassing `scaleUnits` .|
|**Premium**|>0|**Premium** Streaming-eind punten zijn geschikt voor geavanceerde workloads en bieden specifieke en schaal bare bandbreedte capaciteit. U gaat naar een **Premium** -type door aanpassing `scaleUnits` (streaming units). `scaleUnits` Geef een specifieke uitvoerige capaciteit die kan worden aangeschaft in stappen van 200 Mbps. Wanneer u het **Premium** -type gebruikt, biedt elke ingeschakelde eenheid extra bandbreedte capaciteit voor de app. |

> [!NOTE]
> Voor klanten die inhoud willen leveren aan grote Internet doelgroepen, raden we u aan CDN op het streaming-eind punt in te scha kelen.

Zie [prijzen en sla](https://azure.microsoft.com/pricing/details/media-services/)voor informatie over sla.

## <a name="comparing-streaming-types"></a>Streaming-typen vergelijken

Functie|Standard|Premium
---|---|---
Doorvoer |Maxi maal 600 Mbps en kan een veel hogere efficiënte door Voer bieden wanneer een CDN wordt gebruikt.|200 Mbps per streaming-eenheid (SU). Kan een veel hogere efficiënte door Voer bieden wanneer een CDN wordt gebruikt.
CDN|Azure CDN, CDN van derden of geen CDN.|Azure CDN, CDN van derden of geen CDN.
Facturering is naar verhouding| Dagelijks|Dagelijks
Dynamische versleuteling|Ja|Ja
Dynamische verpakking|Ja|Ja
Schalen|Automatisch geschaald naar de beoogde door voer.|Extra SUs
IP-filtering/G20/aangepaste host <sup>1</sup>|Ja|Ja
Progressieve down load|Ja|Ja
Aanbevolen gebruik |Aanbevolen voor de meeste streaming-scenario's.|Professioneel gebruik.

<sup>1</sup> wordt alleen rechtstreeks op het streaming-eind punt gebruikt wanneer CDN niet is ingeschakeld op het eind punt.<br/>

### <a name="versions"></a>Versies

|Type|StreamingEndpointVersion|ScaleUnits|CDN|Billing|
|--------------|----------|-----------------|-----------------|-----------------|
|Klassiek|1.0|0|NA|Gratis|
|Standard streaming-eind punt (preview-versie)|2,0|0|Yes|Betaald|
|Premium-streaming-eenheden|1.0|>0|Yes|Betaald|
|Premium-streaming-eenheden|2,0|>0|Yes|Betaald|

### <a name="features"></a>Functies

Functie|Standard|Premium
---|---|---
Doorvoer |Maxi maal 600 Mbps en kan een veel hogere efficiënte door Voer bieden wanneer een CDN wordt gebruikt.|200 Mbps per streaming-eenheid (SU). Kan een veel hogere efficiënte door Voer bieden wanneer een CDN wordt gebruikt.
CDN|Azure CDN, CDN van derden of geen CDN.|Azure CDN, CDN van derden of geen CDN.
Facturering is naar verhouding| Dagelijks|Dagelijks
Dynamische versleuteling|Ja|Ja
Dynamische verpakking|Ja|Ja
Schalen|Automatisch geschaald naar de beoogde door voer.|Extra streaming-eenheden.
IP-filtering/G20/aangepaste host <sup>1</sup>|Ja|Ja
Progressieve down load|Ja|Ja
Aanbevolen gebruik |Aanbevolen voor de meeste streaming-scenario's.|Professioneel gebruik. 

<sup>1</sup> alleen rechtstreeks op het streaming-eind punt gebruikt wanneer CDN niet is ingeschakeld op het eind punt.<br/>

Zie [prijzen en sla](https://azure.microsoft.com/pricing/details/media-services/)voor informatie over sla.

## <a name="migration-between-types"></a>Migratie tussen typen

Van | Tot | Bewerking
---|---|---
Klassiek|Standard|Moet u zich aanmelden
Klassiek|Premium| Schalen (extra streaming-eenheden)
Standard/Premium|Klassiek|Niet beschikbaar (als de versie van het streaming-eind punt 1,0 is. Het is toegestaan te wijzigen in klassiek met instelling scaleunits in ' 0 ')
Standard (met/zonder CDN)|Premium met dezelfde configuraties|Toegestaan in de status **gestart** . (via Azure Portal)
Premium (met/zonder CDN)|Standaard met dezelfde configuraties|Toegestaan in de status **gestart** (via Azure Portal)
Standard (met/zonder CDN)|Premium met andere configuratie|Toegestaan in de status **gestopt** (via Azure Portal). Niet toegestaan bij de status wordt uitgevoerd.
Premium (met/zonder CDN)|Standaard met andere configuratie|Toegestaan in de status **gestopt** (via Azure Portal). Niet toegestaan bij de status wordt uitgevoerd.
Versie 1,0 met SU >= 1 met CDN|Standard/Premium zonder CDN|Toegestaan in de status **gestopt** . Niet toegestaan in de status **gestart** .
Versie 1,0 met SU >= 1 met CDN|Standaard met/zonder CDN|Toegestaan in de status **gestopt** . Niet toegestaan in de status **gestart** . Versie 1,0 CDN wordt verwijderd en er wordt een nieuwe gemaakt en gestart.
Versie 1,0 met SU >= 1 met CDN|Premium met/zonder CDN|Toegestaan in de status **gestopt** . Niet toegestaan in de status **gestart** . Klassiek CDN wordt verwijderd en er wordt een nieuwe gemaakt en gestart.







## <a name="streaming-endpoint-properties"></a>Eigenschappen van streaming-eind punt

Deze sectie bevat informatie over een aantal eigenschappen van het streaming-eind punt. Zie [streaming-eind punt](/rest/api/media/streamingendpoints/create)voor voor beelden van het maken van een nieuw streaming-eind punt en beschrijvingen van alle eigenschappen.

- `accessControl`: Wordt gebruikt voor het configureren van de volgende beveiligings instellingen voor dit streaming-eind punt: Akamai-handtekening header verificatie sleutels en IP-adressen die verbinding mogen maken met dit eind punt. Deze eigenschap kan alleen worden ingesteld als deze `cdnEnabled` is ingesteld op false.

- `cdnEnabled`: Hiermee geeft u op of de Azure CDN integratie voor dit streaming-eind punt is ingeschakeld (standaard uitgeschakeld). Als u instelt `cdnEnabled` op True, worden de volgende configuraties uitgeschakeld: `customHostNames` en `accessControl` .

    Niet alle data centers bieden ondersteuning voor de integratie van Azure CDN. Voer de volgende stappen uit om te controleren of de Azure CDN integratie beschikbaar is voor uw Data Center:

  - Probeer de waarde `cdnEnabled` in te stellen op True.
  - Het geretourneerde resultaat voor een `HTTP Error Code 412` (PreconditionFailed) controleren met een bericht van de eigenschap ' streaming-eind punt CdnEnabled kan niet worden ingesteld op True omdat de CDN-mogelijkheid niet beschikbaar is in de huidige regio. '

    Als deze fout wordt weer gegeven, ondersteunt het data centrum het niet. Probeer een ander Data Center.

- `cdnProfile`: Wanneer `cdnEnabled` is ingesteld op waar, kunt u ook waarden door geven `cdnProfile` . `cdnProfile` is de naam van het CDN-profiel waar het CDN-eind punt wordt gemaakt. U kunt een bestaande cdnProfile opgeven of een nieuwe maken. Als de waarde NULL is en `cdnEnabled` waar is, wordt de standaard waarde ' AzureMediaStreamingPlatformCdnProfile ' gebruikt. Als de gegeven `cdnProfile` al bestaat, wordt er een eind punt gemaakt. Als het profiel niet bestaat, wordt er automatisch een nieuw profiel gemaakt.
- `cdnProvider`: Wanneer CDN is ingeschakeld, kunt u ook waarden door geven `cdnProvider` . `cdnProvider` Hiermee wordt bepaald welke provider wordt gebruikt. Op dit moment worden drie waarden ondersteund: "StandardVerizon", "PremiumVerizon" en "StandardAkamai". Als er geen waarde wordt gegeven en `cdnEnabled` waar is, wordt "StandardVerizon" gebruikt (dat is de standaard waarde).
- `crossSiteAccessPolicies`: Wordt gebruikt om beleid voor meerdere sites op te geven voor verschillende clients. Zie voor meer informatie [beleid voor meerdere domein beleids regels](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) en [het beschikbaar maken van een service over domein grenzen](/previous-versions/azure/azure-services/gg185950(v=azure.100)). De instellingen zijn alleen van toepassing op Smooth Streaming.
- `customHostNames`: Wordt gebruikt om een streaming-eind punt te configureren voor het accepteren van verkeer dat naar een aangepaste hostnaam wordt gestuurd. Deze eigenschap is geldig voor standaard-en Premium-streaming-eind punten en kan worden ingesteld als `cdnEnabled` : False.

    Het eigendom van de domein naam moet worden bevestigd door Media Services. Media Services controleert het eigendom van de domein naam door een `CName` record te vereisen met de Media Services-account-id als onderdeel dat moet worden toegevoegd aan het domein dat in gebruik is. Een voor beeld: voor ' sports.contoso.com ' die moet worden gebruikt als aangepaste hostnaam voor het streaming-eind punt, moet een record voor `<accountId>.contoso.com` worden geconfigureerd om te verwijzen naar een van Media Services-namen van verificatie-hosts. De naam van de verificatie-host bestaat uit verifydns. `\<mediaservices-dns-zone>` .

    Hieronder ziet u de verwachte DNS-zones die moeten worden gebruikt in de record controleren voor verschillende Azure-regio's.
  
  - Noord-Amerika, Europa, Singapore, Hong Kong SAR, Japan:

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - China

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    Bijvoorbeeld, een `CName` record waarbij "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" aan "verifydns.media.Azure.net" wordt toegewezen, bewijst dat de Media Services id 945a4c4e-28ea-45cd-8ccb-a519f6b700ad het eigendom van het contoso.com domein heeft, waardoor elke naam onder contoso.com kan worden gebruikt als een aangepaste hostnaam voor een streaming-eind punt onder dat account. Als u de waarde voor de media service-ID wilt vinden, gaat u naar de [Azure Portal](https://portal.azure.com/) en selecteert u uw media service-account. De **account-id** wordt weer gegeven in de rechter bovenhoek van de pagina.

    Als er een poging wordt gedaan om een aangepaste hostnaam in te stellen zonder de juiste verificatie van de `CName` record, mislukt het DNS-antwoord en wordt het enige tijd in de cache opgeslagen. Zodra de juiste record is geplaatst, kan het enige tijd duren voordat het antwoord in de cache opnieuw is gevalideerd. Afhankelijk van de DNS-provider voor het aangepaste domein, duurt het enkele minuten tot een uur om de record opnieuw te valideren.

    Naast de `CName` toewijzing `<accountId>.<parent domain>` aan `verifydns.<mediaservices-dns-zone>` , moet u een andere maken `CName` die de naam van de aangepaste host (bijvoorbeeld) toewijst `sports.contoso.com` aan de hostnaam van het Media Services streaming-eind punt (bijvoorbeeld `amstest-usea.streaming.media.azure.net` ).

    > [!NOTE]
    > Streaming-eind punten die zich in hetzelfde Data Center bevinden, kunnen niet dezelfde aangepaste hostnaam delen.

    Media Services ondersteunt momenteel geen TLS met aangepaste domeinen.

- `maxCacheAge` -Overschrijft de standaard instelling voor de HTTP-cache van de maximale leeftijds waarde voor het streamen van het streaming-eind punt op media fragmenten en on-demand manifesten. De waarde wordt ingesteld in seconden.
- `resourceState` -

    - Gestopt: de initiële status van een streaming-eind punt nadat deze is gemaakt
    - Starten: wordt overgezet naar de actieve status
    - Uitvoeren: kan inhoud streamen naar clients
    - Schalen: de schaal eenheden worden verg root of verkleind
    - Stoppen: wordt overgezet naar de status gestopt
    - Verwijderen: wordt verwijderd

- `scaleUnits`: Bieden specifieke uitvoerige capaciteit die kan worden aangeschaft in stappen van 200 Mbps. Als u naar een **Premium** -type moet gaan, past u aan `scaleUnits` .

## <a name="why-use-multiple-streaming-endpoints"></a>Waarom meerdere streaming-eind punten gebruiken?

Eén streaming-eind punt kan zowel live als on-demand Video's streamen en de meeste klanten gebruiken slechts één streaming-eind punt. In deze sectie vindt u enkele voor beelden van waarom u meerdere streaming-eind punten moet gebruiken.

* Elke gereserveerde eenheid biedt een band breedte van 200 Mbps. Als u meer dan 2.000 Mbps (2 Gbps) band breedte nodig hebt, kunt u het tweede streaming-eind punt en taak verdeling gebruiken om extra band breedte te bieden.

    CDN is echter de beste manier om de inhoud van streaming te verg Roten, maar als u zoveel inhoud levert als de CDN meer dan 2 Gbps haalt, kunt u extra streaming-eind punten (oorsprong) toevoegen. In dit geval moet u inhouds-Url's leveren die in de twee streaming-eind punten evenwichtig zijn. Deze benadering biedt een betere caching dan het wille keurig verzenden van aanvragen naar elke oorsprong (bijvoorbeeld via Traffic Manager). 
    
    > [!TIP]
    > Als de CDN meer dan 2 Gbps haalt, kan er meestal iets onjuist worden geconfigureerd (bijvoorbeeld zonder het afschermen van oorsprong).
    
* Taak verdeling verschillende CDN-providers. U kunt bijvoorbeeld het standaard streaming-eind punt instellen op het gebruik van het Verizon CDN en een tweede maken om Akamai te gebruiken. Voeg vervolgens een deel van de taak verdeling toe tussen de twee om multi-CDN-verdeling te belasten. 

    De klant houdt vaak taak verdeling over meerdere CDN-providers met behulp van één oorsprong.
* Gemengde inhoud streaming: Live en video op aanvraag. 

    De toegangs patronen voor Live en on-demand inhoud zijn zeer verschillend. De live-inhoud wordt in één keer veel vraag voor dezelfde inhoud ontvangen. De inhoud op aanvraag van de video (inhoud van een lang einde van het Archief voor exemplaar) heeft een laag gebruik op dezelfde inhoud. Het opslaan in cache werkt heel goed met de live-inhoud, maar ook op de lange staart inhoud.

    Houd rekening met een scenario waarbij uw klanten hoofd zakelijk live-inhoud volgen, maar alleen af en toe inhoud op aanvraag kunnen bekijken en wordt geleverd vanuit hetzelfde streaming-eind punt. Het lage gebruik van on-demand inhoud zou cache ruimte innemen die beter zou kunnen worden opgeslagen voor de live-inhoud. In dit scenario raden we u aan om de live-inhoud van één streaming-eind punt en de lange-staart inhoud van een ander streaming-eind punt te leveren. Hierdoor worden de prestaties van de inhoud van de live-gebeurtenis verbeterd.
    
## <a name="scaling-streaming-with-cdn"></a>Streaming schalen met CDN

Zie de volgende artikelen:

- [Overzicht van CDN](../../cdn/cdn-overview.md)
- [Streaming schalen met CDN](scale-streaming-cdn.md)

## <a name="ask-questions-and--get-updates"></a>Vragen stellen en updates ophalen

Ga naar het artikel van de [Azure Media Services-community](media-services-community.md) voor verschillende manieren om vragen te stellen, feedback te geven en updates voor Media Services op te halen.

## <a name="see-also"></a>Zie ook

[Dynamische verpakking](encode-dynamic-packaging-concept.md)

## <a name="next-steps"></a>Volgende stappen

[Streaming-eindpunten beheren](manage-streaming-endpoints-howto.md)
