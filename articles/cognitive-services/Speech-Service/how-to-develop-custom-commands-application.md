---
title: 'Instructies: aangepaste opdrachten ontwikkelen toepassingen-Speech Service'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het ontwikkelen en aanpassen van toepassingen voor aangepaste opdrachten. Deze Voice Command-apps zijn het meest geschikt voor het volt ooien van taken of voor scenario's met opdrachten en besturings elementen.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: trbye
ms.openlocfilehash: 1a002b6efbe2603ae254c19f9e3cc7377198cea2
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935815"
---
# <a name="develop-custom-commands-applications"></a>Toepassingen voor aangepaste opdrachten ontwikkelen

In dit artikel leert u hoe u toepassingen voor aangepaste opdrachten ontwikkelt en configureert. De functie aangepaste opdrachten helpt u bij het bouwen van geavanceerde stem opdrachten-apps die zijn geoptimaliseerd voor de ervaring van spraak-eerste interactie. De functie is het meest geschikt voor het volt ooien van taken of voor scenario's met opdrachten en besturings elementen. Het is bijzonder goed geschikt voor IoT-apparaten (Internet of Things) en voor omgevings-en headless apparaten.

In dit artikel maakt u een toepassing waarmee u een TV kunt in-en uitschakelen, de Tempe ratuur instelt en een alarm instelt. Nadat u deze basis opdrachten hebt gemaakt, krijgt u meer informatie over de volgende opties voor het aanpassen van opdrachten:

* Para meters aan opdrachten toevoegen
* Configuraties toevoegen aan opdracht parameters
* Interactie regels maken
* Sjablonen voor het genereren van talen maken voor spraak reacties
* Aangepaste spraak hulpprogramma's gebruiken

## <a name="create-an-application-by-using-simple-commands"></a>Een toepassing maken met behulp van eenvoudige opdrachten

Maak eerst een lege toepassing voor aangepaste opdrachten. Raadpleeg de [Snelstartgids](quickstart-custom-commands-application.md)voor meer informatie. In deze toepassing maakt u in plaats van een project te importeren een leeg project.

1. Voer in het vak **naam** de project naam *Smart-room-Lite* (of een andere naam van uw keuze) in.
1. Selecteer in de lijst **taal** de optie **Engels (Verenigde Staten)**.
1. Selecteer of maak een LUIS-resource.

   > [!div class="mx-imgBorder"]
   > ![Scherm afbeelding van het venster New project.](media/custom-commands/create-new-project.png)

### <a name="update-luis-resources-optional"></a>LUIS-resources bijwerken (optioneel)

U kunt de ontwerp bron die u hebt geselecteerd in het venster **Nieuw project** bijwerken. U kunt ook een Voorspellings bron instellen. 

Er wordt een Voorspellings resource gebruikt voor herkenning wanneer uw toepassing aangepaste opdrachten wordt gepubliceerd. U hebt geen Voorspellings resource nodig tijdens de ontwikkelings-en test fasen.

### <a name="add-a-turnon-command"></a>Een TurnOn-opdracht toevoegen

Voeg in de lege Smart-room-Lite-toepassing voor aangepaste opdrachten die u hebt gemaakt, een opdracht toe. Met de opdracht wordt een utterance verwerkt `Turn on the tv` . Deze reageert met het bericht `Ok, turning the tv on` .

1. Maak een nieuwe opdracht door **nieuwe opdracht** boven aan het linkerdeel venster te selecteren. Het **nieuwe opdracht** venster wordt geopend.
1. Geef voor het veld **naam** de waarde op `TurnOn` .
1. Selecteer **Maken**.

In het middelste deel venster worden de eigenschappen van de opdracht weer gegeven. 

De volgende tabel bevat informatie over de configuratie-eigenschappen van de opdracht. Zie voor meer informatie [aangepaste opdrachten concepten en definities](./custom-commands-references.md).

| Configuratie            | Beschrijving                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Voorbeeld zinnen | Voor beeld uitingen de gebruiker kan zeggen dat deze opdracht wordt geactiveerd.                                                                 |
| Parameters       | Informatie die is vereist om de opdracht te volt ooien.                                                                                |
| Voltooiings regels | Acties die moeten worden uitgevoerd om te voldoen aan de opdracht. Voor beelden: reageren op de gebruiker of communiceren met een webservice. |
| Interactie regels   | Andere regels voor het afhandelen van specifieke of complexe situaties.                                                              |


> [!div class="mx-imgBorder"]
> ![Scherm afbeelding die laat zien waar een opdracht wordt gemaakt.](media/custom-commands/add-new-command.png)

#### <a name="add-example-sentences"></a>Voorbeeld zinnen toevoegen

In het gedeelte **voor beelden van zinnen** geeft u een voor beeld van wat de gebruiker kan zeggen.

1. Selecteer **voorbeeld zinnen** in het middelste deel venster.
1. Voeg in het deel venster aan de rechter kant voor beelden toe:

    ```
    Turn on the tv
    ```

1.  Selecteer boven in het deel venster **Opslaan**.

U hebt nog geen para meters, dus u kunt door gaan naar de sectie **voltooiings regels** .

#### <a name="add-a-completion-rule"></a>Een voltooiings regel toevoegen

Vervolgens moet de opdracht een voltooiings regel hebben. Deze regel geeft aan de gebruiker door dat er een uitvoerings actie wordt ondernomen. 

Zie voor meer informatie over regels en voltooiings regels [aangepaste opdrachten, concepten en definities](./custom-commands-references.md).

1. Selecteer de standaard voltooiings **regel is voltooid.** Bewerk de sjabloon als volgt:

    
    | Instelling    | Voorgestelde waarde                          | Beschrijving                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **Naam**       | `ConfirmationResponse`                  | Een naam die het doel van de regel beschrijft          |
    | **Voorwaarden** | Geen                                     | Voor waarden die bepalen wanneer de regel kan worden uitgevoerd    |
    | **Acties**    | **Spraak antwoord verzenden**  >  **Eenvoudige editor**  >  **Eerste variatie** > `Ok, turning the tv on` | De actie die moet worden uitgevoerd wanneer de regel voorwaarde waar is |

   > [!div class="mx-imgBorder"]
   > ![Scherm afbeelding die laat zien waar een spraak antwoord wordt gemaakt.](media/custom-commands/create-speech-response-action.png)

1. Selecteer **Opslaan** om de actie op te slaan.
1. Selecteer in de sectie **voltooiings regels** de optie **Opslaan** om alle wijzigingen op te slaan. 

    > [!NOTE]
    > U hoeft niet de standaard voltooiings regel te gebruiken die bij de opdracht wordt geleverd. U kunt de standaard voltooiings regel verwijderen en uw eigen regel toevoegen.

### <a name="add-a-settemperature-command"></a>Een SetTemperature-opdracht toevoegen

Voeg nu nog één opdracht toe `SetTemperature` . Met deze opdracht wordt één utterance uitgevoerd `Set the temperature to 40 degrees` en wordt met het bericht gereageerd `Ok, setting temperature to 40 degrees` .

Als u de nieuwe opdracht wilt maken, volgt u de stappen die u hebt gebruikt voor de `TurnOn` opdracht, maar gebruikt u de voorbeeld zin `Set the temperature to 40 degrees` .

Bewerk vervolgens **de bestaande regels** voor het volt ooien van voltooiing als volgt:

| Instelling    | Voorgestelde waarde                          |
| ---------- | ---------------------------------------- |
| **Naam**  | `ConfirmationResponse`                  |
| **Voorwaarden** | Geen                                     |
| **Acties**    | **Spraak antwoord verzenden**  >  **Eenvoudige editor**  >  **Eerste variatie** > `Ok, setting temperature to 40 degrees` |

Selecteer **Opslaan** om alle wijzigingen in de opdracht op te slaan.

### <a name="add-a-setalarm-command"></a>Een SetAlarm-opdracht toevoegen

Maak een nieuwe `SetAlarm` opdracht. Gebruik de voorbeeld zin `Set an alarm for 9 am tomorrow` . Bewerk vervolgens **de bestaande regels** voor het volt ooien van voltooiing als volgt:

| Instelling    | Voorgestelde waarde                          |
| ---------- | ---------------------------------------- |
| **Naam**  | `ConfirmationResponse`                  |
| **Voorwaarden** | Geen                                     |
| **Acties**    | **Spraak antwoord verzenden**  >  **Eenvoudige editor**  >  **Eerste variatie** > `Ok, setting an alarm for 9 am tomorrow` |

Selecteer **Opslaan** om alle wijzigingen in de opdracht op te slaan.

### <a name="try-it-out"></a>Probeer het eens

Test het gedrag van de toepassing met behulp van het test venster: 

1. Selecteer in de rechter bovenhoek van het deel venster het pictogram **trein** . 
1. Wanneer de training is voltooid, selecteert u **testen**. 

Probeer de volgende utterance-voor beelden uit met behulp van spraak of tekst:

- U typt: *Stel de Tempe ratuur in op 40 graden*
- Verwachte reactie: OK, Tempe ratuur instellen op 40 graden
- U typt: *de TV inschakelen*
- Verwachte reactie: OK, de TV inschakelen
- U typt: *een alarm instellen voor 9 am morgen*
- Verwachte reactie: OK, instellen van een alarm voor 9 am morgen

> [!div class="mx-imgBorder"]
> ![Scherm opname van de test in een web-chat-interface.](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> In het test venster kunt u **Details inschakelen** selecteren voor informatie over hoe deze stem invoer of tekst invoer is verwerkt.

## <a name="add-parameters-to-commands"></a>Parameters toevoegen aan opdrachten

In deze sectie leert u hoe u para meters kunt toevoegen aan uw-opdrachten. Voor opdrachten zijn para meters vereist om een taak te volt ooien. In complexe scenario's kunnen para meters worden gebruikt om voor waarden te definiëren die aangepaste acties activeren.

### <a name="configure-parameters-for-a-turnon-command"></a>Para meters configureren voor een TurnOn-opdracht

Begin met het bewerken van de bestaande `TurnOn` opdracht om meerdere apparaten in te scha kelen en uit te scha kelen.

1. Nu de opdracht zowel in-als uit-scenario's wordt verwerkt, wijzigt u de naam van de opdracht als *TurnOnOff*.
   1. Selecteer de opdracht **TurnOn** in het deel venster aan de linkerkant. Selecteer vervolgens de knop met het weglatings teken (**...**) naast **nieuwe opdracht** bovenaan het deel venster.
   
   1. Selecteer **naam wijzigen**. Wijzig in het venster naam **wijzigen** de naam in *TurnOnOff*.

1. Voeg een nieuwe para meter toe aan de opdracht. De para meter geeft aan of de gebruiker het apparaat wil in-of uitschakelen.
   1. Selecteer aan de bovenkant van het middelste deel venster  **toevoegen**. Selecteer in de vervolg keuzelijst de optie **para meter**.
   1. In het deel venster aan de rechter kant in het gedeelte **para meters** , in het vak **naam** , toevoegen `OnOff` .
   1. Selecteer **vereist**. Selecteer in het venster **reactie voor een vereiste para meter toevoegen** de optie **eenvoudige editor**. In het **eerste variatie** veld voegt u in *of uit?*.
   1. Selecteer **Bijwerken**.

       > [!div class="mx-imgBorder"]
       > ![Scherm opname van de sectie ' reactie toevoegen voor een vereiste para meter ' op het tabblad eenvoudige editor geselecteerd.](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. Configureer de eigenschappen van de para meter met behulp van de volgende tabel. Zie voor meer informatie over alle configuratie-eigenschappen van een opdracht [aangepaste opdrachten concepten en definities](./custom-commands-references.md).
      

       | Configuratie      | Voorgestelde waarde     | Beschrijving                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | **Naam**               | `OnOff`           | Een beschrijvende naam voor de para meter                                                                           |
       | **Is wereld wijd**          | Geselecteerde       | Selectie vakje dat aangeeft of een waarde voor deze para meter globaal wordt toegepast op alle opdrachten in de toepassing.|
       | **Vereist**           | Geselecteerd         | Selectie vakje dat aangeeft of een waarde voor deze para meter vereist is voordat de opdracht is voltooid. |
       | **Antwoord voor vereiste para meter**      |**Eenvoudige editor** > `On or Off?`      | Er wordt gevraagd om de waarde van deze para meter wanneer deze niet bekend is. |
       | **Type**               | **Tekenreeks**          | Parameter type, zoals getal, teken reeks, datum/tijd of geografie.   |
       | **Configuratie**      | **Vooraf gedefinieerde invoer waarden van een interne catalogus accepteren** | Voor teken reeksen beperkt deze instelling invoer tot een reeks mogelijke waarden. |
       | **Vooraf gedefinieerde invoer waarden**     | `on`, `off`           | Set mogelijke waarden en aliassen.         |
       
        
   1. Selecteer **een vooraf gedefinieerde invoer toevoegen** om vooraf gedefinieerde invoer waarden toe te voegen. Typ in het venster **Nieuw item**  de *naam* zoals wordt weer gegeven in de voor gaande tabel. In dit geval gebruikt u geen aliassen, dus u kunt dit veld leeg laten.
   
      > [!div class="mx-imgBorder"]
      > ![Scherm afbeelding die laat zien hoe u een para meter maakt.](media/custom-commands/create-on-off-parameter.png)

   1. Selecteer **Opslaan** om alle configuraties van de para meter op te slaan.
 
#### <a name="add-a-subjectdevice-parameter"></a>Een SubjectDevice-para meter toevoegen

1. Selecteer **toevoegen** om een tweede para meter toe te voegen aan de naam van de apparaten die kunnen worden beheerd met behulp van deze opdracht. Gebruik de volgende configuratie.


    | Instelling            | Voorgestelde waarde       |
    | ------------------ | --------------------- |
    | **Naam**               | `SubjectDevice`         |
    | **Is wereld wijd**          | Geselecteerde             |
    | **Vereist**           | Geselecteerd               |
    | **Antwoord voor vereiste para meter**     | **Eenvoudige editor** > `Which device do you want to control?`    | 
    | **Type**               | **Tekenreeks**                |          |
    | **Configuratie**      | **Vooraf gedefinieerde invoer waarden van een interne catalogus accepteren** | 
    | **Vooraf gedefinieerde invoer waarden** | `tv`, `fan`               |
    | **Aliassen** ( `tv` )      | `television`, `telly`     |

1. Selecteer **Opslaan**.

#### <a name="modify-example-sentences"></a>Voorbeeld zinnen wijzigen

Voor opdrachten die para meters gebruiken, is het handig om voorbeeld zinnen toe te voegen die betrekking hebben op alle mogelijke combi Naties. Bijvoorbeeld:

* Volledige parameter informatie: `turn {OnOff} the {SubjectDevice}`
* Gedeeltelijke parameter informatie: `turn it {OnOff}`
* Geen parameter informatie: `turn something`

Met een voor beeld van zinnen die gebruikmaken van verschillende mate van informatie kan de toepassing voor aangepaste opdrachten zowel oplossingen met één afbeelding als meerdere resoluties oplossen door gedeeltelijke informatie te gebruiken.

Met deze informatie kunt u de voorbeeld zinnen bewerken voor het gebruik van de volgende voorgestelde para meters:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

Selecteer **Opslaan**.

> [!TIP]
> Gebruik accolades in de editor voor beelden om de para meters te raadplegen. Bijvoorbeeld `turn {OnOff} the {SubjectDevice}`.
> Een tabblad gebruiken voor het automatisch volt ooien van de back-up door eerder gemaakte para meters.

#### <a name="modify-completion-rules-to-include-parameters"></a>Voltooiings regels voor het toevoegen van para meters wijzigen

Wijzig de bestaande voltooiings regel `ConfirmationResponse` .

1. Selecteer **een voor waarde toevoegen** in de sectie **voor waarden** .
1. Selecteer in het venster **nieuwe voor waarde** in de lijst **type** de **vereiste para meters**. Selecteer in de onderstaande lijst zowel **ONOFF** als **SubjectDevice**.
1. **IsGlobal** niet selecteren.
1. Selecteer **Maken**.
1. Bewerk in de sectie **acties** de actie **spraak antwoord verzenden** door erop te wijzen en de knop bewerken te selecteren. Gebruik deze keer de zojuist gemaakte `OnOff` `SubjectDevice` para meters:

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. Selecteer **Opslaan**.

Probeer de wijzigingen door het pictogram **trein** boven aan het deel venster aan de rechter kant te selecteren. 

Wanneer de training is voltooid, selecteert u **testen**. Een **Test uw toepassings** venster wordt weer gegeven. Probeer de volgende interacties:

- Invoer: *de TV uitschakelen*
- Uitvoer: OK, de TV uitschakelen
- Invoer: *de televisie uitschakelen*
- Uitvoer: OK, de TV uitschakelen
- Invoer: *uitschakelen*
- Uitvoer: welk apparaat wilt u beheren?
- Invoer: *de TV*
- Uitvoer: OK, de TV uitschakelen

### <a name="configure-parameters-for-a-settemperature-command"></a>Para meters configureren voor een SetTemperature-opdracht

Wijzig de `SetTemperature` opdracht om de Tempe ratuur in te stellen als de gebruiker.

Voeg een `Temperature` para meter toe. Gebruik de volgende configuratie:

| Configuratie      | Voorgestelde waarde     |
| ------------------ | ----------------|
| **Naam**               | `Temperature`           |
| **Vereist**           | Geselecteerd         |
| **Antwoord voor vereiste para meter**      | **Eenvoudige editor** > `What temperature would you like?`
| **Type**               | `Number`          |


Bewerk het voor beeld uitingen om de volgende waarden te gebruiken.

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

Bewerk de bestaande voltooiings regels. Gebruik de volgende configuratie.

| Configuratie      | Voorgestelde waarde     |
| ------------------ | ----------------|
| **Voorwaarden**         | **Vereiste para meter**  >  **Temperatuur**           |
| **Acties**           | **Spraak antwoord verzenden** > `Ok, setting temperature to {Temperature} degrees` |

### <a name="configure-parameters-for-a-setalarm-command"></a>Para meters configureren voor een SetAlarm-opdracht

Voeg een para meter toe met de naam `DateTime` . Gebruik de volgende configuratie.

   | Instelling                           | Voorgestelde waarde                     | 
   | --------------------------------- | ----------------------------------------|
   | **Naam**                              | `DateTime`                               |
   | **Vereist**                          | Geselecteerd                                 |
   | **Antwoord voor vereiste para meter**   | **Eenvoudige editor** > `For what time?`            | 
   | **Type**                              | **Datum/tijd**                                |
   | **Standaard waarden**                     | Gebruik vandaag als de datum ontbreekt.            |
   | **Standaard waarden voor tijd**                     | Als de tijd ontbreekt, gebruikt u het begin van de dag.     |


> [!NOTE]
> In dit artikel worden meestal para meters voor teken reeksen, getallen en datum/tijd gebruikt. Zie voor een lijst met alle ondersteunde parameter typen en de bijbehorende eigenschappen [aangepaste opdrachten concepten en definities](./custom-commands-references.md).


Bewerk het voor beeld uitingen. Gebruik de volgende waarden.

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

Bewerk de bestaande voltooiings regels. Gebruik de volgende configuratie.

   | Instelling    | Voorgestelde waarde                               |
   | ---------- | ------------------------------------------------------- |
   | **Acties**    | **Spraak antwoord verzenden** > `Ok, alarm set for {DateTime}`  |

Test de drie opdrachten samen met behulp van uitingen die betrekking hebben op verschillende opdrachten. (U kunt scha kelen tussen de verschillende opdrachten.)

- Invoer: *een alarm instellen*
- Uitvoer: voor welk tijdstip?
- Invoer: *de TV inschakelen*
- Uitvoer: OK, TV in-of uitschakelen
- Invoer: *een alarm instellen*
- Uitvoer: voor welk tijdstip?
- Invoer: *5 pm*
- Uitvoer: OK, alarm ingesteld voor 2020-05-01 17:00:00

## <a name="add-configurations-to-command-parameters"></a>Configuraties toevoegen aan opdracht parameters

In deze sectie vindt u meer informatie over geavanceerde parameter configuratie, waaronder:

 - Hoe parameter waarden kunnen deel uitmaken van een set die buiten de toepassing voor aangepaste opdrachten is gedefinieerd.
 - Validatie componenten toevoegen aan de parameter waarden.

### <a name="configure-a-parameter-as-an-external-catalog-entity"></a>Een para meter als een externe catalogus entiteit configureren

Met de functie voor aangepaste opdrachten kunt u para meters van het type teken reeks configureren om te verwijzen naar externe catalogi die worden gehost via een webeindpunt. U kunt de externe catalogus dus onafhankelijk bijwerken zonder de toepassing voor aangepaste opdrachten te bewerken. Deze benadering is handig in gevallen waarin de catalogus items talrijke zijn.

Hergebruik de `SubjectDevice` para meter van de `TurnOnOff` opdracht. De huidige configuratie voor deze para meter **accepteert vooraf gedefinieerde invoer van interne catalogus**. Deze configuratie verwijst naar een statische lijst met apparaten in de parameter configuratie. Verplaats deze inhoud naar een externe gegevens bron die onafhankelijk kan worden bijgewerkt.

Als u de inhoud wilt verplaatsen, moet u eerst een nieuw webeind punt toevoegen. Ga in het deel venster aan de linkerkant naar de sectie **Web-eind punten** . Daar kunt u een nieuw webeindpunt toevoegen. Gebruik de volgende configuratie.

| Instelling | Voorgestelde waarde |
|----|----|
| **Naam** | `getDevices` |
| **URL** | `https://aka.ms/speech/cc-sampledevices` |
| **Methode** | **GET** |


Als de voorgestelde waarde voor de URL niet werkt voor u, configureert en host u een webeindpunt dat een JSON-bestand retourneert dat bestaat uit de lijst met apparaten die kunnen worden beheerd. Het eind punt van het web moet een JSON-bestand retour neren dat als volgt is opgemaakt:
    
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

Ga vervolgens naar de pagina **SubjectDevice** para meter Settings. Stel de volgende eigenschappen in.

| Instelling | Voorgestelde waarde |
| ----| ---- |
| **Configuratie** | **Vooraf gedefinieerde invoer accepteren van externe catalogus** |                               
| **Eind punt van catalogus** | `getDevices` |
| **Methode** | **GET** |

Selecteer vervolgens **Opslaan**.

> [!IMPORTANT]
> U ziet geen optie voor het configureren van een para meter voor het accepteren van invoer parameters van een externe catalogus, tenzij u het webeindpunt hebt ingesteld in het gedeelte **Web endpoint** in het deel venster aan de linkerkant.

Probeer het uit door **Train** te selecteren. Nadat de training is voltooid, selecteert u **testen** en voert u enkele interacties uit.

* Invoer: *inschakelen*
* Uitvoer: welk apparaat wilt u beheren?
* Invoer: *lichten*
* Uitvoer: OK, de lampjes inschakelen

> [!NOTE]
> U kunt nu alle apparaten beheren die worden gehost op het eind punt van de webserver. Maar u moet de toepassing nog steeds trainen om de nieuwe wijzigingen te testen en de toepassing vervolgens opnieuw te publiceren.

### <a name="add-validation-to-parameters"></a>Validatie toevoegen aan para meters

*Validaties* zijn constructies die van toepassing zijn op bepaalde parameter typen waarmee u beperkingen kunt configureren voor de waarde van de para meter. U wordt gevraagd om correcties als waarden niet binnen de beperkingen vallen. Zie voor een lijst met parameter typen die de validatie construct uitbreiden de [aangepaste opdrachten concepten en definities](./custom-commands-references.md).

Test de validaties met behulp van de `SetTemperature` opdracht. Gebruik de volgende stappen om een validatie voor de para meter toe te voegen `Temperature` .

1. Selecteer de opdracht **SetTemperature** in het deel venster aan de linkerkant.
1. Selecteer **Tempe ratuur** in het middelste deel venster.
1. Selecteer in het deel venster aan de rechter kant **een validatie toevoegen**.
1. Configureer in het venster **nieuwe validatie** de validatie zoals wordt weer gegeven in de volgende tabel. Selecteer vervolgens **Maken**.


    | Parameter configuratie | Voorgestelde waarde | Beschrijving |
    | ---- | ---- | ---- |
    | **Minimumwaarde** | `60` | Voor numerieke para meters kan de minimum waarde van deze para meter aannemen |
    | **Maximumwaarde** | `80` | Voor numerieke para meters kan de maximum waarde van deze para meter aannemen |
    | **Fout bericht** |  **Eenvoudige editor**  >  **Eerste variatie** > `Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | Een vraag om een nieuwe waarde te vragen als de validatie is mislukt |

    > [!div class="mx-imgBorder"]
    > ![Scherm opname waarin wordt getoond hoe een validatie van een bereik moet worden toegevoegd.](media/custom-commands/add-validations-temperature.png)

Probeer het uit door boven aan het deel venster aan de rechter kant het pictogram voor de **trein** te selecteren. Nadat de training is voltooid, selecteert u **testen**. Probeer enkele interacties:

- Invoer: *Stel de Tempe ratuur in op 72 graden*
- Uitvoer: OK, Tempe ratuur instellen op 72 graden
- Invoer: *Stel de Tempe ratuur in op 45 graden*
- Uitvoer: er kan alleen een Tempe ratuur tussen 60 en 80 graden worden ingesteld
- Invoer: *Maak in plaats daarvan 72 graden*
- Uitvoer: OK, Tempe ratuur instellen op 72 graden

## <a name="add-interaction-rules"></a>Interactieregels toevoegen

Interactie regels zijn *aanvullende* regels voor het verwerken van specifieke of complexe situaties. Hoewel u uw eigen interactie regels wilt maken, kunt u in dit voor beeld interactie regels gebruiken voor de volgende scenario's:

* Opdrachten bevestigen
* Een correctie met één stap toevoegen aan opdrachten

Zie voor meer informatie over interactie regels [aangepaste opdrachten concepten en definities](./custom-commands-references.md).

### <a name="add-confirmations-to-a-command"></a>Bevestigingen toevoegen aan een opdracht

Als u een bevestiging wilt toevoegen, gebruikt u de `SetTemperature` opdracht. Maak met behulp van de volgende stappen om een bevestiging te krijgen:

1. Selecteer de opdracht **SetTemperature** in het deel venster aan de linkerkant.
1. Voeg in het middelste deel venster interactie regels toe door **toevoegen** te selecteren. Selecteer vervolgens de opdracht regels bevestigen van de **interactie**  >  .

    Met deze actie worden drie interactie regels toegevoegd. De regels vragen de gebruiker om de datum en tijd van de wekker te bevestigen. Ze verwachten een bevestiging (ja of Nee) voor de volgende beurt.

    1. Wijzig de interactie regel voor de **Bevestig-opdracht** met behulp van de volgende configuratie:
        1. Wijzig de naam om de **Tempe ratuur te bevestigen**.
        1. Een nieuwe voor waarde toevoegen: **vereiste para meters**  >  **temperatuur**.
        1. Een nieuwe actie toevoegen: **type**  >  **verzenden spraak antwoord**  >  **weet u zeker dat u de Tempe ratuur wilt instellen als {Tempe ratuur} graden?**
        1. In het gedeelte **verwachtingen** moet u de standaard waarde van de **verwachte bevestiging van de gebruiker** behouden.
      
         > [!div class="mx-imgBorder"]
         > ![Scherm afbeelding die laat zien hoe het vereiste parameter antwoord wordt gemaakt.](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Wijzig de regel voor het bevestigen van de **bevestiging** om een geslaagde bevestiging te verwerken (de gebruiker zei ja).
      
          1. Wijzig de naam in **bevestigings temperatuur geslaagd**.
          1. De bestaande **bevestiging is geslaagd** .
          1. Een nieuwe voor waarde toevoegen: **Typ** de  >  **vereiste para meters** voor de  >  **Tempe ratuur**.
          1. Wijzig de standaard waarde voor de **status na de uitvoering** als regels voor het **uitvoeren van voltooiing**.

    1. Wijzig de afvraagde **interactie regel** voor het afhandelen van scenario's wanneer bevestiging wordt geweigerd (de gebruiker heeft nee).

          1. Wijzig de naam in **bevestigings temperatuur geweigerd**.
          1. De voor waarde voor de bestaande **bevestiging is geweigerd** .
          1. Een nieuwe voor waarde toevoegen: **Typ** de  >  **vereiste para meters** voor de  >  **Tempe ratuur**.
          1. Een nieuwe actie toevoegen: **type**  >  **verzenden spraak antwoord** is  >  **geen probleem. Wat is de Tempe ratuur?**.
          1. Wijzig de standaard waarde voor de **status na de uitvoering** om te **wachten op de invoer van de gebruiker**.

> [!IMPORTANT]
> In dit artikel gebruikt u de ingebouwde bevestigings mogelijkheid. U kunt ook hand matig interactie regels één voor één toevoegen.
   
Probeer de wijzigingen door **Train** te selecteren. Wanneer de training is voltooid, selecteert u **testen**.

- **Invoer**: *Stel de Tempe ratuur in op 80 graden*
- **Uitvoer**: weet u zeker dat u de Tempe ratuur wilt instellen op 80 graden?
- **Invoer**: *Nee*
- **Uitvoer**: geen probleem. Wat is de Tempe ratuur?
- **Invoer**: *72 graden*
- **Uitvoer**: weet u zeker dat u de Tempe ratuur wilt instellen op 72 graden?
- **Invoer**: *Ja*
- **Uitvoer**: OK, Tempe ratuur instellen op 72 graden

### <a name="implement-corrections-in-a-command"></a>Correcties implementeren in een opdracht

In deze sectie configureert u een correctie met één stap. Deze correctie wordt gebruikt nadat de uitvoerings actie is uitgevoerd. U ziet ook een voor beeld van hoe een correctie standaard is ingeschakeld als aan de opdracht nog niet is voldaan. Als u een correctie wilt toevoegen wanneer de opdracht niet is voltooid, voegt u de nieuwe para meter toe `AlarmTone` .

Selecteer de opdracht **SetAlarm** in het linkerdeel venster. Voeg vervolgens de nieuwe para meter **AlarmTone** toe.
        
- **Naam** > `AlarmTone`
- **Type**  >  **Teken reeks**
- **Standaard waarde**  >  **Klokken**
- **Configuratie**  >  **Vooraf gedefinieerde invoer waarden accepteren uit de interne catalogus**
- **Vooraf gedefinieerde invoer waarden**  >  **Klokken**, **Jingle** en **echo** (deze waarden zijn afzonderlijke vooraf gedefinieerde invoer.)


Vervolgens werkt u de reactie voor de para meter **DateTime** bij op **gereed om een waarschuwing in te stellen met een Toon als {AlarmTone}. Voor welke tijd?**. Wijzig vervolgens de voltooiings regel als volgt:

1. Selecteer de bestaande voltooiings regel **ConfirmationResponse**.
1. Beweeg in het deel venster aan de rechter kant de muis aanwijzer over de bestaande actie en selecteer **bewerken**.
1. Werk de spraak reactie bij naar `OK, alarm set for {DateTime}. The alarm tone is {AlarmTone}` .

> [!IMPORTANT]
> De alarm Toon kan worden gewijzigd zonder uitdrukkelijke configuratie in een doorlopende opdracht. Het kan bijvoorbeeld veranderen wanneer de opdracht nog niet is voltooid. Een correctie is *standaard* ingeschakeld voor alle opdracht parameters, ongeacht het Schakel nummer, als aan de opdracht nog moet worden voldaan.

#### <a name="implement-a-correction-when-a-command-is-finished"></a>Een correctie implementeren wanneer een opdracht is voltooid

Het aangepaste opdrachten platform maakt een correctie van één stap mogelijk, zelfs als de opdracht is voltooid. Deze functie is niet standaard ingeschakeld. Deze moet expliciet worden geconfigureerd. 

Voer de volgende stappen uit om een correctie met één stap te configureren:

1. Voeg in de opdracht **SetAlarm** een interactie regel van de opdracht type **Update Previous** toe om de eerder ingestelde waarschuwing bij te werken. Wijzig de naam van de interactie regel als **vorige waarschuwing bijwerken**.
1. De standaard waarde behouden: de **vorige opdracht moet worden bijgewerkt**.
1. Een nieuwe voor waarde toevoegen: **Typ** de  >  **vereiste para meter**  >  **DateTime**.
1. Een nieuwe actie toevoegen: **type**  >  **verzenden spraak antwoord**  >  **Simple editor**  >  **vorige alarm tijd bijwerken naar {datetime}**.
1. Wijzig de standaard waarde voor de **status na uitvoering** als **opdracht is voltooid**.

Probeer de wijzigingen door **Train** te selecteren. Wacht tot de training is voltooid en selecteer vervolgens **testen**.

- **Invoer**: *Stel een alarm in.*
- **Output**: gereed om alarm in te stellen met een Toon klok. Voor welke tijd?
- **Invoer**: *Stel een alarm in met de Toon als jingle voor 9 am morgen.*
- **Uitvoer**: OK, alarm ingesteld voor 2020-05-21 09:00:00. Het alarm signaal is jingle.
- **Invoer**: *Nee, 8 uur.*
- **Output**: de vorige alarm tijd wordt bijgewerkt naar 2020-05-29 08:00.

> [!NOTE]
> In een echte toepassing moet u in de sectie **acties** van deze correctie regel ook een activiteit naar de client terugsturen of een http-eind punt aanroepen om de alarm tijd in uw systeem bij te werken. Deze actie moet alleen verantwoordelijk zijn voor het bijwerken van de alarm tijd. Het moet niet verantwoordelijk zijn voor elk ander kenmerk van de opdracht. In dit geval zou dat kenmerk de alarm Toon zijn.

## <a name="add-language-generation-templates-for-speech-responses"></a>Sjablonen voor het genereren van talen toevoegen voor spraak reacties

Met de sjablonen voor taal generatie (LG) kunt u de antwoorden aanpassen die worden verzonden naar de client. Ze veroorzaken een afwijking in de reacties. U kunt de taal genereren met behulp van:

* Sjablonen voor het genereren van talen.
* Adaptieve expressies.

Aangepaste opdrachten sjablonen zijn gebaseerd op de [LG-sjablonen](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)van de bot Framework. Omdat de functie aangepaste opdrachten een nieuwe LG-sjabloon maakt wanneer vereist (voor spraak reacties in para meters of acties), hoeft u niet de naam van de LG-sjabloon op te geven. 

U hoeft uw sjabloon dus niet als volgt te definiëren:

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

In plaats daarvan kunt u de hoofd tekst van de sjabloon definiëren zonder de naam, bijvoorbeeld:

> [!div class="mx-imgBorder"]
> ![Scherm afbeelding van een voor beeld van een sjabloon editor.](./media/custom-commands/template-editor-example.png)


Met deze wijziging wordt het verschil in de spraak reacties die naar de client worden verzonden, gewijzigd. Voor een utterance wordt het bijbehorende spraak antwoord wille keurig uit de gegeven opties opgehaald.

Door gebruik te maken van LG-sjablonen, kunt u ook complexe spraak reacties voor opdrachten definiëren met behulp van adaptieve expressies. Zie de [indeling LG-sjablonen](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)voor meer informatie. 

Standaard ondersteunt de functie voor aangepaste opdrachten alle mogelijkheden, met de volgende kleine verschillen:

* In de LG-sjablonen worden entiteiten weer gegeven als `${entityName}` . De functie voor aangepaste opdrachten maakt geen gebruik van entiteiten. Maar u kunt para meters gebruiken als variabelen met de `${parameterName}` weer gave of de `{parameterName}` weer gave.
* De functie voor aangepaste opdrachten biedt geen ondersteuning voor sjabloon samenstelling en-uitbrei ding, omdat u het *. LG* -bestand nooit direct bewerkt. U bewerkt alleen de antwoorden van automatisch gemaakte sjablonen.
* De functie Custom commands biedt geen ondersteuning voor aangepaste functies die door LG injectes worden ingevoerd. Vooraf gedefinieerde functies worden ondersteund.
* De functie voor aangepaste opdrachten biedt geen ondersteuning voor opties, zoals `strict` , `replaceNull` en `lineBreakStyle` .

### <a name="add-template-responses-to-a-turnonoff-command"></a>Sjabloon Reacties toevoegen aan een TurnOnOff-opdracht

Wijzig de `TurnOnOff` opdracht om een nieuwe para meter toe te voegen. Gebruik de volgende configuratie.

| Instelling            | Voorgestelde waarde       | 
| ------------------ | --------------------- | 
| **Naam**               | `SubjectContext`         | 
| **Is wereld wijd**          | Geselecteerde             | 
| **Vereist**           | Geselecteerde               | 
| **Type**               | **Tekenreeks**                |
| **Standaardwaarde**      | `all` |
| **Configuratie**      | **Vooraf gedefinieerde invoer waarden accepteren van interne catalogus** | 
| **Vooraf gedefinieerde invoer waarden** | `room`, `bathroom`, `all`|

#### <a name="modify-a-completion-rule"></a>Een voltooiings regel wijzigen

Bewerk de sectie **acties** van de bestaande voltooiings regel **ConfirmationResponse**. Ga in het venster **actie bewerken** naar **sjabloon editor**. Vervang vervolgens de tekst door het volgende voor beeld.

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

Train en test uw toepassing met behulp van de volgende invoer en uitvoer. Let op de variatie van de antwoorden. De variatie wordt gemaakt door meerdere alternatieven van de sjabloon waarde en ook door het gebruik van adaptieve expressies.

* Invoer: *de TV inschakelen*
* Uitvoer: OK, TV in-of uitschakelen
* Invoer: *de TV inschakelen*
* Uitvoer: gereed, de TV ingeschakeld
* Invoer: *de lichten* uitschakelen
* Uitvoer: OK, alle lichten uitschakelen
* Invoer: *kamer verlichting uitschakelen*
* Uitvoer: OK, de kamer verlichting uitschakelen

## <a name="use-a-custom-voice"></a>Een aangepaste spraak gebruiken

Een andere manier om aangepaste opdrachten te beantwoorden, is door een uitvoer stem te selecteren. Gebruik de volgende stappen om de standaard stem naar een aangepaste stem te scha kelen:

1. Selecteer in de toepassing aangepaste opdrachten in het deel venster aan de linkerkant de optie **instellingen**.
1. Selecteer **aangepaste spraak** in het middelste deel venster.
1. Selecteer een aangepaste spraak of open bare stem in de tabel.
1. Selecteer **Opslaan**.

> [!div class="mx-imgBorder"]
> ![Scherm opname met voorbeeld zinnen en-para meters.](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> Voor open bare stemmen zijn Neural typen alleen beschikbaar voor bepaalde regio's. Zie [ondersteunde regio's voor spraak Services](./regions.md#standard-and-neural-voices)voor meer informatie.
>
> U kunt aangepaste stemmen maken op de pagina met het **aangepaste spraak** project. Zie [aan de slag met aangepaste spraak](./how-to-custom-voice.md)voor meer informatie.

De toepassing reageert nu op de geselecteerde stem, in plaats van de standaard stem.

## <a name="next-steps"></a>Volgende stappen

* Leer hoe u [uw toepassing met aangepaste opdrachten integreert](how-to-custom-commands-setup-speech-sdk.md) met een client-app met behulp van de spraak-SDK.
* [Stel doorlopende implementatie](how-to-custom-commands-deploy-cicd.md) in voor uw toepassing voor aangepaste opdrachten met behulp van Azure DevOps. 
                      
