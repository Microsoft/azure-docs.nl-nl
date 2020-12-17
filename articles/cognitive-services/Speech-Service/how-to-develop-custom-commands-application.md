---
title: 'Instructies: aangepaste opdrachten ontwikkelen toepassingen-Speech Service'
titleSuffix: Azure Cognitive Services
description: In deze procedure leert u hoe u aangepaste opdrachten toepassingen kunt ontwikkelen en aanpassen. Met aangepaste opdrachten kunt u eenvoudig geavanceerde spraak opdrachten bouwen die zijn geoptimaliseerd voor de functie voor het maken van spraak, en die het beste geschikt is voor het volt ooien van taken of het beheren van opdrachten en besturings elementen, met name voor Internet of Things IoT-apparaten (well-to-match), omgevings-en headless apparaten.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: trbye
ms.openlocfilehash: 98c0459e0b67102458169147b1d39e98e2b3e2b1
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632749"
---
# <a name="develop-custom-commands-applications"></a>Toepassingen voor aangepaste opdrachten ontwikkelen

In deze procedure leert u hoe u aangepaste opdrachten toepassingen ontwikkelt en configureert. Met aangepaste opdrachten kunt u eenvoudig geavanceerde spraak opdrachten bouwen die zijn geoptimaliseerd voor de functie voor het maken van spraak, en die het beste geschikt is voor het volt ooien van taken of het beheren van opdrachten en besturings elementen, met name voor Internet of Things IoT-apparaten (well-to-match), omgevings-en headless apparaten.

In dit artikel maakt u een toepassing waarmee u een TV kunt in-en uitschakelen, de Tempe ratuur instelt en een alarm instelt. Nadat u deze basis opdrachten hebt gemaakt, worden de volgende opties voor het aanpassen van opdrachten behandeld:

* Para meters aan opdrachten toevoegen
* Configuraties toevoegen aan opdracht parameters
* Interactie regels maken
* Sjablonen voor het genereren van talen maken voor spraak reacties
* Aangepaste spraak gebruiken 

## <a name="create-application-with-simple-commands"></a>Toepassing met eenvoudige opdrachten maken

Begin eerst door een lege toepassing voor aangepaste opdrachten te maken. Raadpleeg de [Snelstartgids](quickstart-custom-commands-application.md)voor meer informatie. Deze keer, in plaats van een project te importeren, maakt u een leeg project.

1. Voer in het vak **naam** de project naam in `Smart-Room-Lite` (of iets anders van uw keuze).
1. Selecteer in de lijst **taal** de optie **Engels (Verenigde Staten)**.
1. Selecteer of maak een LUIS-resource van uw keuze.

   > [!div class="mx-imgBorder"]
   > ![Een project maken](media/custom-commands/create-new-project.png)

### <a name="update-luis-resources-optional"></a>LUIS-resources bijwerken (optioneel)

U kunt de ontwerp bron die u hebt geselecteerd in het venster **Nieuw project** bijwerken en een Voorspellings bron instellen. De Voorspellings resource wordt gebruikt voor herkenning wanneer uw toepassing aangepaste opdrachten wordt gepubliceerd. U hebt geen Voorspellings resource nodig tijdens de ontwikkelings-en test fasen.

### <a name="add-turnon-command"></a>Opdracht TurnOn toevoegen

In de lege aangepaste opdrachten voor het maken van **Smart room-Lite-** toepassingen die u zojuist hebt gemaakt, voegt u een eenvoudige opdracht toe waarmee een utterance wordt verwerkt, `turn on the tv` en reageert u met het bericht `Ok, turning the tv on` .

1. Maak een nieuwe opdracht door **nieuwe opdracht** boven aan het linkerdeel venster te selecteren. Het **nieuwe opdracht** venster wordt geopend.
1. Geef een waarde voor het veld **naam** op als **TurnOn**.
1. Selecteer **Maken**.

In het middelste deel venster worden de verschillende eigenschappen van de opdracht weer gegeven. U configureert de volgende eigenschappen van de opdracht. Ga naar [verwijzingen](./custom-commands-references.md)voor uitleg van alle configuratie-eigenschappen van een opdracht.

| Configuratie            | Beschrijving                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Voorbeeld zinnen** | Voor beeld van uitingen de gebruiker kan zeggen dat deze opdracht wordt geactiveerd                                                                 |
| **Parameters**       | Gegevens die nodig zijn om de opdracht te volt ooien                                                                                |
| **Voltooiings regels** | De acties die moeten worden uitgevoerd om te voldoen aan de opdracht. Bijvoorbeeld om te reageren op de gebruiker of te communiceren met een andere webservice. |
| **Interactie regels**   | Aanvullende regels voor het afhandelen van specifieke of complexe situaties                                                              |


> [!div class="mx-imgBorder"]
> ![Een opdracht maken](media/custom-commands/add-new-command.png)

#### <a name="add-example-sentences"></a>Voorbeeld zinnen toevoegen

Laten we beginnen met de sectie **voor beelden van zinnen** en een voor beeld geven van wat de gebruiker kan zeggen.

1. Selecteer de sectie **voor beelden van zinnen** in het middelste deel venster.
1. Voeg in het rechterdeel venster voor beelden toe:

    ```
    turn on the tv
    ```

1.  Selecteer boven aan het deel venster **Opslaan** .

We hebben nu geen para meters, dus we kunnen door gaan naar de sectie **voltooiings regels** .

#### <a name="add-a-completion-rule"></a>Een voltooiings regel toevoegen

Vervolgens moet de opdracht een voltooiings regel hebben. Deze regel geeft aan de gebruiker door dat er een uitvoerings actie wordt ondernomen. Ga naar [verwijzingen](./custom-commands-references.md)voor meer informatie over regels en voltooiings regels.

1. Selecteer de standaard voltooiings regel die u **hebt** geselecteerd en bewerk deze als volgt:

    
    | Instelling    | Voorgestelde waarde                          | Beschrijving                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **Naam**       | ConfirmationResponse                  | Een naam die het doel van de regel beschrijft          |
    | **Voorwaarden** | Geen                                     | Voor waarden die bepalen wanneer de regel kan worden uitgevoerd    |
    | **Acties**    | Spraak antwoord verzenden > eenvoudige editor > eerste variatie > `Ok, turning the tv on` | De actie die moet worden uitgevoerd wanneer de regel voorwaarde waar is |

   > [!div class="mx-imgBorder"]
   > ![Een spraak antwoord maken](media/custom-commands/create-speech-response-action.png)

1. Selecteer **Opslaan** om de actie op te slaan.
1. Selecteer in de sectie **voltooiings regels** de optie **Opslaan** om alle wijzigingen op te slaan. 

    > [!NOTE]
    > Het is niet nodig om de standaard voltooiings regel te gebruiken die bij de opdracht wordt geleverd. Indien nodig kunt u de bestaande standaard voltooiings regel verwijderen en uw eigen regel toevoegen.

### <a name="add-settemperature-command"></a>Opdracht SetTemperature toevoegen

Voeg nu nog één opdracht **SetTemperature** toe die één utterance gaat maken, `set the temperature to 40 degrees` en reageer met het bericht `Ok, setting temperature to 40 degrees` .

Volg de stappen in de **TurnOn** -opdracht om een nieuwe opdracht te maken met behulp van de voorbeeld zin '**de Tempe ratuur instellen op 40 graden**'.

Bewerk vervolgens **de bestaande regels** voor het volt ooien van voltooiing als volgt:

| Instelling    | Voorgestelde waarde                          |
| ---------- | ---------------------------------------- |
| Naam  | ConfirmationResponse                  |
| Voorwaarden | Geen                                     |
| Acties    | Spraak antwoord verzenden > eenvoudige editor > eerste variatie > `Ok, setting temperature to 40 degrees` |

Selecteer **Opslaan** om alle wijzigingen in de opdracht op te slaan.

### <a name="add-setalarm-command"></a>Opdracht SetAlarm toevoegen

Maak een nieuwe opdracht **SetAlarm** met behulp van de voorbeeld zin '**een wekker instellen voor 9 am morgen**'. Bewerk vervolgens **de bestaande regels** voor het volt ooien van voltooiing als volgt:

| Instelling    | Voorgestelde waarde                          |
| ---------- | ---------------------------------------- |
| Regelnaam  | ConfirmationResponse                  |
| Voorwaarden | Geen                                     |
| Acties    | Spraak antwoord verzenden > eenvoudige editor > eerste variatie >`Ok, setting an alarm for 9 am tomorrow` |

Selecteer **Opslaan** om alle wijzigingen in de opdracht op te slaan.

### <a name="try-it-out"></a>Probeer het eens

Test het gedrag met behulp van het deel venster testen. Selecteer een **trein** pictogram boven in het rechterdeel venster. Wanneer de training is voltooid, selecteert u **testen**. Probeer de volgende utterance-voor beelden uit via spraak of tekst:

- U typt: Stel de Tempe ratuur in op 40 graden
- Verwachte reactie: OK, Tempe ratuur instellen op 40 graden
- U typt: de TV inschakelen
- Verwachte reactie: OK, de TV inschakelen
- U typt: een alarm instellen voor 9 am morgen
- Verwachte reactie: OK, instellen van een alarm voor 9 am morgen

> [!div class="mx-imgBorder"]
> ![Testen met Web Chat](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> In het deel venster testen kunt u Details voor informatie **inschakelen** selecteren om te zien hoe deze spraak/tekst invoer is verwerkt.

## <a name="add-parameters-to-commands"></a>Parameters toevoegen aan opdrachten

In deze sectie leert u hoe u para meters kunt toevoegen aan uw-opdrachten. Para meters zijn de vereiste gegevens voor de opdrachten voor het volt ooien van een taak. In complexe scenario's kunnen para meters ook worden gebruikt om voor waarden te definiëren waarmee aangepaste acties worden geactiveerd.

### <a name="configure-parameters-for-turnon-command"></a>Para meters voor de opdracht TurnOn configureren

Begin met het bewerken van de bestaande **TurnOn** -opdracht om meerdere apparaten in te scha kelen en uit te scha kelen.

1. Nu de opdracht nu zowel in-als uit-scenario's wordt verwerkt, wijzigt u de naam van de opdracht in **TurnOnOff**.
   1. Selecteer de opdracht **TurnOn** in het linkerdeel venster en selecteer vervolgens de knop met het weglatings teken (...) naast **nieuwe opdracht** boven aan het deel venster.
   
   1. Selecteer **naam wijzigen**. Wijzig in het venster naam wijzigen de **optie** **naam** in **TurnOnOff**.

1. Vervolgens voegt u een nieuwe para meter toe aan deze opdracht die aangeeft of de gebruiker het apparaat wil in-of uitschakelen.
   1. Selecteer boven in het middelste deel venster de optie  **toevoegen** . Selecteer in de vervolg keuzelijst de **para meter**.
   1. In het rechterdeel venster, in het gedeelte **para meters** , voegt u waarde in het vak **naam** toe als **ONOFF**.
   1. Selecteer **vereist**. Selecteer in het venster **reactie voor een vereiste para meter toevoegen** de optie **eenvoudige editor**. In de **eerste variant** toevoegen
        ```
        On or Off?
        ```
   1. Selecteer **Bijwerken**.

       > [!div class="mx-imgBorder"]
       > ![Vereist parameter antwoord maken](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. Nu gaan we de eigenschappen van de para meters configureren. Ga naar [verwijzingen](./custom-commands-references.md)voor uitleg van alle configuratie-eigenschappen van een opdracht. Configureer de eigenschappen van de para meter als volgt:
      

       | Configuratie      | Voorgestelde waarde     | Beschrijving                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | Naam               | `OnOff`           | Een beschrijvende naam voor de para meter                                                                           |
       | Is wereld wijd          | uitgeschakeld       | Selectie vakje dat aangeeft of een waarde voor deze para meter globaal wordt toegepast op alle opdrachten in de toepassing|
       | Vereist           | wel         | Selectie vakje dat aangeeft of een waarde voor deze para meter vereist is voordat de opdracht wordt voltooid |
       | Antwoord voor vereiste para meter      |Eenvoudige editor > `On or Off?`      | Een prompt om te vragen naar de waarde van deze para meter als deze niet bekend is |
       | Type               | Tekenreeks          | Het type para meter, zoals getal, teken reeks, datum/tijd of geografie   |
       | Configuratie      | Vooraf gedefinieerde invoer waarden accepteren van interne catalogus | Voor teken reeksen beperkt de invoer tot een reeks mogelijke waarden |
       | Vooraf gedefinieerde invoer waarden     | `on`, `off`           | Set mogelijke waarden en hun aliassen         |
       
        
   1. Voor het toevoegen van vooraf gedefinieerde invoer waarden selecteert u **een vooraf gedefinieerde invoer toevoegen** en in het venster **Nieuw item**  typt u **naam** zoals opgegeven in de bovenstaande tabel. In dit geval gebruiken we geen aliassen, zodat u deze leeg kunt laten.
   
      > [!div class="mx-imgBorder"]
      > ![Para meter maken](media/custom-commands/create-on-off-parameter.png)

   1. Selecteer **Opslaan** om alle configuraties van de para meter op te slaan.
 
#### <a name="add-subjectdevice-parameter"></a>De para meter SubjectDevice toevoegen 

1. Selecteer vervolgens opnieuw **toevoegen** om een tweede para meter toe te voegen om de naam weer te geven van de apparaten die kunnen worden beheerd met behulp van deze opdracht. Gebruik de volgende configuratie.


    | Instelling            | Voorgestelde waarde       |
    | ------------------ | --------------------- |
    | Naam               | `SubjectDevice`         |
    | Is wereld wijd          | uitgeschakeld             |
    | Vereist           | wel               |
    | Antwoord voor vereiste para meter     | Eenvoudige editor > `Which device do you want to control?`    | 
    | Type               | Tekenreeks                |          |
    | Configuratie      | Vooraf gedefinieerde invoer waarden accepteren van interne catalogus | 
    | Vooraf gedefinieerde invoer waarden | `tv`, `fan`               |
    | Aliassen ( `tv` )      | `television`, `telly`     |

1. Selecteer **Opslaan**

#### <a name="modify-example-sentences"></a>Voorbeeld zinnen wijzigen

Voor opdrachten met para meters is het handig om voorbeeld zinnen toe te voegen die betrekking hebben op alle mogelijke combi Naties. Bijvoorbeeld:

* Volledige parameter informatie- `turn {OnOff} the {SubjectDevice}`
* Gedeeltelijke parameter informatie- `turn it {OnOff}`
* Geen parameter informatie- `turn something`

Met een voor beeld van zinnen met een andere mate van informatie kan de toepassing van de aangepaste opdrachten zowel oplossingen met één afronding als multi-turn oplossen met gedeeltelijke informatie.

Bewerk in dat geval de voorbeeld zinnen voor het gebruik van de para meters zoals hieronder wordt voorgesteld:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

Selecteer **Opslaan**.

> [!TIP]
> Gebruik accolades in de editor voor beelden om de para meters te raadplegen. - `turn {OnOff} the {SubjectDevice}` Tabblad gebruiken voor het automatisch volt ooien van de back-up door eerder gemaakte para meters.

#### <a name="modify-completion-rules-to-include-parameters"></a>Voltooiings regels voor het toevoegen van para meters wijzigen

Wijzig de bestaande **ConfirmationResponse** van de voltooiings regel.

1. Selecteer **een voor waarde toevoegen** in de sectie **voor waarden** .
1. Selecteer in het venster **nieuwe voor waarde** in de lijst **type** de **vereiste para meters**. Controleer in de onderstaande check list zowel **ONOFF** als **SubjectDevice**.
1. Zorg ervoor dat **IsGlobal** niet is ingeschakeld.
1. Selecteer **Maken**.
1. Bewerk de bestaande actie voor het verzenden van een **antwoord** in de sectie **acties** door over de actie te bewegen en de knop bewerken te selecteren. Gebruik de zojuist gemaakte para meters **ONOFF** en **SubjectDevice** om deze tijd te gebruiken

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. Selecteer **Opslaan**.

Probeer de wijzigingen door het pictogram **trein** boven in het rechterdeel venster te selecteren. Wanneer de training is voltooid, selecteert u **testen**. Er wordt een **test-app** -venster weer gegeven. Probeer de volgende interacties.

- Invoer: de TV uitschakelen
- Uitvoer: OK, de TV uitschakelen
- Invoer: de televisie uitschakelen
- Uitvoer: OK, de TV uitschakelen
- Invoer: uitschakelen
- Uitvoer: welk apparaat wilt u beheren?
- Invoer: de TV
- Uitvoer: OK, de TV uitschakelen

### <a name="configure-parameters-for-settemperature-command"></a>Para meters voor de opdracht SetTemperature configureren

Wijzig de **SetTemperature** -opdracht om deze in te scha kelen, zodat de Tempe ratuur kan worden ingesteld door de gebruiker.

Nieuwe parameter **temperatuur** toevoegen met de volgende configuratie

| Configuratie      | Voorgestelde waarde     |
| ------------------ | ----------------|
| Naam               | `Temperature`           |
| Vereist           | wel         |
| Antwoord voor vereiste para meter      | Eenvoudige editor > `What temperature would you like?`
| Type               | Getal          |


Bewerk het voor beeld uitingen in de volgende waarden.

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

Bewerk de bestaande voltooiings regels volgens de volgende configuratie.

| Configuratie      | Voorgestelde waarde     |
| ------------------ | ----------------|
| Voorwaarden         | Vereiste para meter > temperatuur           |
| Acties           | > spraak antwoord verzenden `Ok, setting temperature to {Temperature} degrees` |

### <a name="configure-parameters-to-the-setalarm-command"></a>Para meters configureren voor de SetAlarm-opdracht

Voeg een para meter met de naam **DateTime** toe met de volgende configuratie.

   | Instelling                           | Voorgestelde waarde                     | 
   | --------------------------------- | ----------------------------------------|
   | Naam                              | `DateTime`                               |
   | Vereist                          | wel                                 |
   | Antwoord voor vereiste para meter   | Eenvoudige editor > `For what time?`            | 
   | Type                              | DateTime                                |
   | Standaard waarden                     | Als datum ontbreekt, gebruikt u vandaag            |
   | Standaard waarden voor tijd                     | Gebruik begin van dag als tijd ontbreekt     |


> [!NOTE]
> In dit artikel hebben we voornamelijk gebruik gemaakt van teken reeks-, getal-en datum parameter typen. Ga naar [verwijzingen](./custom-commands-references.md)voor een lijst met alle ondersteunde parameter typen en de bijbehorende eigenschappen.


Bewerk voor beeld uitingen naar de volgende waarden.

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

Bewerk de bestaande voltooiings regels volgens de volgende configuratie.

   | Instelling    | Voorgestelde waarde                               |
   | ---------- | ------------------------------------------------------- |
   | Acties    | Spraak antwoord verzenden- `Ok, alarm set for {DateTime}`  |

U kunt alle drie de opdrachten tegelijk testen met uitingen die betrekking hebben op verschillende opdrachten. Houd er rekening mee dat u kunt scha kelen tussen de verschillende opdrachten.

- Invoer: een alarm instellen
- Uitvoer: voor welk tijdstip?
- Invoer: de TV inschakelen
- Uitvoer: OK, TV in-of uitschakelen
- Invoer: een alarm instellen
- Uitvoer: voor welk tijdstip?
- Invoer: tot 17:00 uur
- Uitvoer: OK, alarm ingesteld voor 2020-05-01 17:00:00

## <a name="add-configurations-to-commands-parameters"></a>Configuraties toevoegen aan opdrachtparameters

In deze sectie vindt u meer informatie over geavanceerde parameter configuratie, waaronder:

 - Hoe parameter waarden kunnen behoren tot een set die extern is gedefinieerd voor de toepassing Custom commands
 - Validatie componenten toevoegen aan de waarde van de para meters

### <a name="configure-parameter-as-external-catalog-entity"></a>Para meter als externe catalogus entiteit configureren

Met aangepaste opdrachten kunt u para meters van het type teken reeks configureren om te verwijzen naar externe catalogi die via een webeindpunt worden gehost. Hierdoor kunt u de externe catalogus onafhankelijk bijwerken zonder wijzigingen aan te brengen in de toepassing aangepaste opdrachten. Deze benadering is handig in gevallen waarin de catalogus vermeldingen groot kunnen zijn in een getal.

Gebruik de para meter **SubjectDevice** uit de opdracht **TurnOnOff** . De huidige configuratie voor deze para meter **accepteert vooraf gedefinieerde invoer van interne catalogus**. Dit verwijst naar een statische lijst met apparaten zoals gedefinieerd in de parameter configuratie. We willen deze inhoud verplaatsen naar een externe gegevens bron die onafhankelijk kan worden bijgewerkt.

Als u dit wilt doen, moet u eerst een nieuw webeind punt toevoegen. Ga naar de sectie **Web-eind punten** in het linkerdeel venster en voeg een nieuw webeind punt toe met de volgende configuratie.

| Instelling | Voorgestelde waarde |
|----|----|
| Naam | `getDevices` |
| URL | `https://aka.ms/speech/cc-sampledevices` |
| Methode | GET |


Als de voorgestelde waarde voor de URL niet voor u werkt, moet u een eenvoudig webeind punt configureren en hosten die een JSON retourneert dat bestaat uit een lijst met apparaten die kunnen worden beheerd. Het eind punt van het web moet een JSON retour neren met de volgende notatie:
    
```json
{
    "fan" : [],
    "refrigerator" : [
        "fridge"
    ],
    "lights" : [
        "bulb",
        "bulbs",
        "light"
        "light bulb"
    ],
    "tv" : [
        "telly",
        "television"
        ]
}

```

Ga vervolgens naar de pagina **SubjectDevice** para meter Settings en wijzig de eigenschappen in het volgende.

| Instelling | Voorgestelde waarde |
| ----| ---- |
| Configuratie | Vooraf gedefinieerde invoer accepteren van externe catalogus |                               
| Eind punt van catalogus | getDevices |
| Methode | GET |

Selecteer vervolgens **Opslaan**.

> [!IMPORTANT]
> U ziet geen optie voor het configureren van een para meter voor het accepteren van invoer uit een externe catalogus, tenzij u het webeindpunt hebt ingesteld in het gedeelte **Web endpoint** in het linkerdeel venster.

Probeer het uit door **Train** te selecteren en te wachten op voltooiing van de training. Zodra de training is voltooid, selecteert u **testen** en voert u enkele interacties uit.

* Invoer: inschakelen
* Uitvoer: welk apparaat wilt u beheren?
* Invoer: lichten
* Uitvoer: OK, de lampjes inschakelen

> [!NOTE]
> U kunt nu alle apparaten beheren die op het webeindpunt worden gehost. U moet de toepassing nog steeds trainen om de nieuwe wijzigingen te testen en de toepassing opnieuw te publiceren.

### <a name="add-validation-to-parameters"></a>Validatie toevoegen aan para meters

**Validaties** zijn constructies die van toepassing zijn op bepaalde parameter typen, waarmee u beperkingen kunt configureren voor de waarde van de para meter, en vragen om correctie als waarden niet binnen de beperkingen vallen. Ga naar [verwijzingen](./custom-commands-references.md) voor een volledige lijst met parameter typen die de validatie construct uitbreiden

Test de validaties met behulp van de **SetTemperature** -opdracht. Voer de volgende stappen uit om een validatie voor de para meter **Tempe ratuur** toe te voegen.

1. Selecteer de opdracht **SetTemperature** in het linkerdeel venster.
1. Selecteer  **Tempe ratuur** in het middelste deel venster.
1. Selecteer **een validatie toevoegen** die aanwezig is in het rechterdeel venster.
1. Configureer in het venster **nieuwe validatie** de optie validatie als volgt en selecteer **maken**.


    | Parameter configuratie | Voorgestelde waarde | Beschrijving |
    | ---- | ---- | ---- |
    | Minimumwaarde | `60` | Voor numerieke para meters kan de minimum waarde van deze para meter aannemen |
    | Maximumwaarde | `80` | Voor numerieke para meters kan de maximum waarde van deze para meter aannemen |
    | Fout bericht |  Eenvoudige editor > eerste variatie > `Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | Vragen om een nieuwe waarde te vragen als de validatie is mislukt |

    > [!div class="mx-imgBorder"]
    > ![Validatie van een bereik toevoegen](media/custom-commands/add-validations-temperature.png)

Probeer het uit door het **trein** pictogram boven in het rechterdeel venster te selecteren. Wanneer de training is voltooid, selecteert u **testen** en voert u enkele interacties uit:

- Invoer: Stel de Tempe ratuur in op 72 graden
- Uitvoer: OK, Tempe ratuur instellen op 72 graden
- Invoer: Stel de Tempe ratuur in op 45 graden
- Uitvoer: er kan alleen een Tempe ratuur tussen 60 en 80 graden worden ingesteld
- Invoer: Maak in plaats daarvan 72 graden
- Uitvoer: OK, Tempe ratuur instellen op 72 graden

## <a name="add-interaction-rules"></a>Interactieregels toevoegen

Interactie regels zijn *aanvullende regels* voor het verwerken van specifieke of complexe situaties. Hoewel u uw eigen aangepaste interactie regels wilt maken, kunt u in dit voor beeld gebruikmaken van interactie regels voor de volgende doel scenario's:

* Opdrachten bevestigen
* Een correctie met één stap toevoegen aan opdrachten

Ga voor meer informatie over interactie regels naar het gedeelte [verwijzingen](./custom-commands-references.md) .

### <a name="add-confirmations-to-a-command"></a>Bevestigingen toevoegen aan een opdracht

Als u een bevestiging wilt toevoegen, gebruikt u de opdracht **SetTemperature** . U kunt met behulp van de volgende stappen interactie regels maken om de bevestiging te krijgen.

1. Selecteer de opdracht **SetTemperature** in het linkerdeel venster.
1. Voeg interactie regels toe door **toevoegen** te selecteren in het middelste deel venster. Selecteer vervolgens de opdracht regels bevestigen van de **interactie**  >  .

    Met deze actie worden drie interactie regels toegevoegd waarmee de gebruiker wordt gevraagd om de datum en tijd van de waarschuwing te bevestigen en verwacht een bevestiging (Ja/Nee) voor de volgende beurt.

    1. Wijzig de interactie regel voor de **bevestigings opdracht** volgens de volgende configuratie:
        1. Wijzig **naam** om de **Tempe ratuur te bevestigen**.
        1. Voeg een nieuwe voor waarde toe als **vereiste para meters** voor de  >  **Tempe ratuur**.
        1. Een nieuwe actie toevoegen als het **type**  >  **spraak antwoord verzenden**  >  **weet u zeker dat u de Tempe ratuur wilt instellen als {Tempe ratuur} graden?**
        1. Behoud de standaard waarde van de **verwachte bevestiging van de gebruiker** in het gedeelte **verwachtingen** .
      
         > [!div class="mx-imgBorder"]
         > ![Vereist parameter antwoord maken](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Wijzig de interactie regel voor het bevestigen van de **bevestiging** om een geslaagde bevestiging te verwerken (gebruiker zei ja).
      
          1. Wijzig **naam** in **bevestigings temperatuur geslaagd**.
          1. De al bestaande **bevestiging is voltooid** .
          1. Voeg een nieuwe voor waarde toe als **type**  >  **vereiste para meters**  >  **temperatuur**.
          1. Wijzig de standaard waarde van de **status na uitvoering** als **regels** voor het uitvoeren van voltooiing.

    1. Wijzig de verwerkings regel voor **Geweigerde bevestiging** om scenario's te verwerken wanneer de bevestiging wordt geweigerd (gebruiker zei nee).

          1. Wijzig **naam** in **bevestigings temperatuur geweigerd**.
          1. De al bestaande **bevestiging is geweigerd** .
          1. Voeg een nieuwe voor waarde toe als **type**  >  **vereiste para meters**  >  **temperatuur**.
          1. Een nieuwe actie toevoegen als **type**  >  **verzenden spraak antwoord**  >  **geen probleem. Wat is de Tempe ratuur?**
          1. Wijzig de standaard waarde van de **status na uitvoering** als **wachten op de invoer van de gebruiker**.

> [!IMPORTANT]
> In dit artikel hebt u de ingebouwde bevestigings functie gebruikt. U kunt ook hand matig interactie regels één voor één toevoegen.
   
Probeer de wijzigingen door **Train** te selecteren, te wachten tot de training is voltooid en selecteer **testen**.

- **Invoer**: Stel de Tempe ratuur in op 80 graden
- **Uitvoer**: weet u zeker dat u de Tempe ratuur wilt instellen op 80 graden?
- **Invoer**: Nee
- **Uitvoer**: geen probleem. Wat is de Tempe ratuur?
- **Invoer**: 72 graden
- **Uitvoer**: weet u zeker dat u de Tempe ratuur wilt instellen op 72 graden?
- **Invoer**: Ja
- **Uitvoer**: OK, Tempe ratuur instellen op 83 graden

### <a name="implement-corrections-in-a-command"></a>Correcties implementeren in een opdracht

In deze sectie configureert u een correctie voor één stap, die wordt gebruikt nadat de uitvoering is al uitgevoerd. U ziet ook een voor beeld van hoe een correctie standaard is ingeschakeld voor het geval de opdracht nog niet is vervuld. Als u een correctie wilt toevoegen wanneer de opdracht niet is voltooid, voegt u de nieuwe para meter **AlarmTone** toe.

Selecteer de opdracht **SetAlarm** in het linkerdeel venster en voeg de nieuwe para meter **AlarmTone** toe.
        
- **Naam**  >  **AlarmTone**
- **Type**  >  **Teken reeks**
- **Standaard waarde**  >  **Klokken**
- **Configuratie**  >  **Vooraf gedefinieerde invoer waarden accepteren uit de interne catalogus**
- **Vooraf gedefinieerde invoer waarden**  >  **Klokken**, **Jingle** en **echo** als afzonderlijke vooraf gedefinieerde invoer


Vervolgens werkt u de reactie voor de para meter **DateTime** bij op **gereed om een waarschuwing in te stellen met een Toon als {AlarmTone}. Voor welke tijd?**. Wijzig vervolgens de voltooiings regel als volgt:

1. Selecteer de bestaande voltooiings regel **ConfirmationResponse**.
1. Beweeg de muis aanwijzer in het rechterdeel venster over de bestaande actie en selecteer **bewerken**.
1. Werk de spraak reactie op **OK, alarm ingesteld voor {DateTime}. Het alarm signaal is {AlarmTone}.**

> [!IMPORTANT]
> De alarm Toon kan worden gewijzigd zonder uitdrukkelijke configuratie in een doorlopende opdracht, bijvoorbeeld wanneer de opdracht nog niet is voltooid. *Een correctie is standaard ingeschakeld voor alle opdracht parameters, ongeacht het Schakel nummer als aan de opdracht nog moet worden voldaan.*

#### <a name="correction-when-command-is-completed"></a>Correctie wanneer opdracht is voltooid

Het aangepaste opdrachten platform biedt ook de mogelijkheid voor een correctie met één stap, zelfs wanneer de opdracht is voltooid. Deze functie is niet standaard ingeschakeld. Deze moet expliciet worden geconfigureerd. Voer de volgende stappen uit om een correctie met één stap te configureren.

1. Voeg in de opdracht **SetAlarm** een interactie regel van de opdracht type **Update Previous** toe om de eerder ingestelde waarschuwing bij te werken. Wijzig de naam van de standaard **naam** van de interactie regel om **vorige waarschuwing** bij te werken.
1. Behoud de standaard waarde **vorige opdracht moet worden bijgewerkt** als dat zo is.
1. Voeg een nieuwe voor waarde toe als vereist **type**  >  **para meter**  >  **DateTime**.
1. Een nieuwe actie toevoegen als **type**  >  **verzenden spraak antwoord**  >  **Simple editor** de  >  **vorige alarm tijd bijwerken naar {DateTime}.**
1. Wijzig de standaard waarde van de **status na uitvoering** als **opdracht is voltooid**.

Probeer de wijzigingen door **Train** te selecteren, te wachten tot de training is voltooid en selecteer **testen**.

- **Invoer**: Stel een alarm in.
- **Output**: gereed om alarm in te stellen met een Toon klok. Voor welke tijd?
- **Invoer**: Stel een alarm in met de Toon als jingle voor 9 am morgen.
- **Uitvoer**: OK, alarm ingesteld voor 2020-05-21 09:00:00. Het alarm signaal is jingle.
- **Invoer**: Nee, 8 uur.
- **Output**: de vorige alarm tijd wordt bijgewerkt naar 2020-05-29 08:00.

> [!NOTE]
> In een echte toepassing moet u in de sectie **acties** van deze correctie regel ook een activiteit naar de client terugsturen of een http-eind punt aanroepen om de alarm tijd in uw systeem bij te werken. Deze actie moet alleen verantwoordelijk zijn voor het bijwerken van de alarm tijd en niet van een ander kenmerk van de opdracht. In dit geval is dat de alarm Toon.

## <a name="add-language-generation-templates-for-speech-responses"></a>Taalgeneratiesjablonen toevoegen voor spraakreacties

Met sjablonen voor het maken van talen kunt u de antwoorden aanpassen die worden verzonden naar de client en worden er verschillen in de antwoorden ingevoerd. Aanpassing van de taal kan worden bereikt door:

* Gebruik van sjablonen voor het genereren van talen
* Gebruik van adaptieve expressies

Aangepaste opdrachten sjablonen zijn gebaseerd op de LG- [sjablonen](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)van BotFramework. Aangezien aangepaste opdrachten een nieuwe LG-sjabloon maken, indien nodig (dat wil zeggen, voor spraak reacties in para meters of acties), hoeft u de naam van de LG-sjabloon niet op te geven. In plaats van uw sjabloon te definiëren als:

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

U hoeft alleen de hoofd tekst van de sjabloon als volgt te definiëren zonder de naam.

> [!div class="mx-imgBorder"]
> ![voor beeld van sjabloon editor](./media/custom-commands/template-editor-example.png)


Met deze wijziging wordt het verschil in de spraak reacties naar de client verzonden. Voor dezelfde utterance zou de bijbehorende spraak reactie dus wille keurig worden gekozen uit de beschik bare opties.

Door gebruik te maken van LG sjablonen kunt u ook complexe spraak reacties voor opdrachten definiëren met behulp van adaptieve expressies. U kunt de indeling van de [LG-sjablonen](/azure/bot-service/file-format/bot-builder-lg-file-format#templates) raadplegen voor meer informatie. Aangepaste opdrachten bieden standaard ondersteuning voor alle mogelijkheden met de volgende kleine verschillen:

* In de entiteiten van LG-sjablonen worden weer gegeven als $ {EntityName}. In aangepaste opdrachten gebruiken we geen entiteiten, maar para meters kunnen worden gebruikt als variabelen met een van de volgende weer gaven $ {para meter naam} of {para meter naam}
* Sjabloon samenstelling en-uitbrei ding worden niet ondersteund in aangepaste opdrachten. Dit komt doordat u het bestand nooit `.lg` rechtstreeks bewerkt, maar alleen de antwoorden van automatisch gemaakte sjablonen.
* Aangepaste functies die worden geïnjecteerd door LG, worden niet ondersteund in aangepaste opdrachten. Vooraf gedefinieerde functies worden nog steeds ondersteund.
* Opties (strict, replaceNull & lineBreakStyle) worden niet ondersteund in aangepaste opdrachten.

### <a name="add-template-responses-to-turnonoff-command"></a>Sjabloon Reacties toevoegen aan de opdracht TurnOnOff

Wijzig de **TurnOnOff** -opdracht om een nieuwe para meter toe te voegen aan de volgende configuratie:

| Instelling            | Voorgestelde waarde       | 
| ------------------ | --------------------- | 
| Naam               | `SubjectContext`         | 
| Is wereld wijd          | uitgeschakeld             | 
| Vereist           | uitgeschakeld               | 
| Type               | Tekenreeks                |
| Standaardwaarde      | `all` |
| Configuratie      | Vooraf gedefinieerde invoer waarden accepteren van interne catalogus | 
| Vooraf gedefinieerde invoer waarden | `room`, `bathroom`, `all`|

#### <a name="modify-completion-rule"></a>Voltooiings regel wijzigen

Bewerk de sectie **acties** van bestaande voltooiings regel **ConfirmationResponse**. Schakel in het pop-upvenster **actie bewerken** over naar **sjabloon editor** en vervang de tekst door het volgende voor beeld.

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

**Train** en **test** uw toepassing als volgt. Let op de variatie van de antwoorden als gevolg van het gebruik van meerdere alternatieven van de sjabloon waarde, en ook voor het gebruik van adaptieve expressies.

* Invoer: de TV inschakelen
* Uitvoer: OK, TV in-of uitschakelen
* Invoer: de TV inschakelen
* Uitvoer: gereed, de TV ingeschakeld
* Invoer: de lichten uitschakelen
* Uitvoer: OK, alle lichten uitschakelen
* Invoer: kamer verlichting uitschakelen
* Uitvoer: OK, de kamer verlichting uitschakelen

## <a name="use-custom-voice"></a>Custom Voice gebruiken

Een andere manier om aangepaste opdrachten te beantwoorden, is door een aangepaste uitvoer stem te selecteren. Gebruik de volgende stappen om de standaard stem naar een aangepaste stem te scha kelen.

1. Selecteer in uw aangepaste opdrachten toepassing **instellingen** in het linkerdeel venster.
1. Selecteer **aangepaste spraak** in het middelste deel venster.
1. Selecteer de gewenste aangepaste of open bare stem in de tabel.
1. Selecteer **Opslaan**.

> [!div class="mx-imgBorder"]
> ![Voorbeeld zinnen met para meters](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - Voor **open bare stemmen** zijn **Neural typen** alleen beschikbaar voor bepaalde regio's. Zie [standaard en Neural stemmen per regio/eind punt](./regions.md#standard-and-neural-voices)om de beschik baarheid te controleren.
> - Voor **aangepaste stemmen** kunnen ze worden gemaakt op de pagina aangepast spraak project. Zie [aan de slag met aangepaste spraak](./how-to-custom-voice.md).

De toepassing reageert nu op de geselecteerde stem, in plaats van de standaard stem.

## <a name="next-steps"></a>Volgende stappen

* Leer hoe u [uw toepassing met aangepaste opdrachten integreert](how-to-custom-commands-setup-speech-sdk.md) met een client-app met behulp van de spraak-SDK.
* [Stel doorlopende implementatie](how-to-custom-commands-deploy-cicd.md) in voor uw toepassing voor aangepaste opdrachten met Azure DevOps. 
                      