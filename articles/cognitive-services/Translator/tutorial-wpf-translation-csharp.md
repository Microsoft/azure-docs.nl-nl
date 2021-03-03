---
title: 'Zelfstudie: Een vertaal-app maken met WPF, C# - Translator'
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie maakt u een WPF-app voor tekstvertaling, taaldetectie en spellingscontrole. Hierbij wordt één abonnementssleutel gebruikt.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 05/26/2020
ms.author: lajanuar
ms.custom: devx-track-csharp
ms.openlocfilehash: 0665dcbc8de518c5759c52a8fc3aec26859566d6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101728005"
---
# <a name="tutorial-create-a-translation-app-with-wpf"></a>Zelfstudie: Een vertaal-app maken met WPF

In deze zelfstudie bouwt u een [Windows Presentation Foundation-app (WPF)](/visualstudio/designers/getting-started-with-wpf) die gebruikmaakt van Azure Cognitive Service voor tekstvertaling, taaldetectie en spellingscontrole. Hierbij wordt één abonnementssleutel gebruikt. Met de app worden API's aangeroepen vanuit Translator en [Bing Spellingcontrole](https://azure.microsoft.com/services/cognitive-services/spell-check/).

Wat is WPF? Dit is een UI-framework waarmee u apps voor computers maakt. Het WPF-ontwikkelingsplatform biedt ondersteuning voor een breed scala aan app-ontwikkelingsfuncties, waaronder een app-model, resources, besturingselementen, afbeeldingen, lay-out, gegevensbinding, documenten en beveiliging. Dit is een subset van het .NET Framework, dus als u al eerder apps hebt gemaakt met het .NET Framework met behulp van ASP.NET of Windows Forms, is de programmeerervaring vergelijkbaar. In WPF wordt gebruikgemaakt van de Extensible Application Markup Language (XAML) om een declaratief model te creëren voor app-programmering. Dit model wordt in de volgende secties besproken.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een WPF-project maken in Visual Studio
> * Assembly's en NuGet-pakketten toevoegen aan uw project
> * De gebruikersinterface van uw app maken met XAML
> * Translator gebruiken om talen op te halen, tekst te vertalen en brontaal te detecteren
> * De Bing Spellingcontrole-API gebruiken om uw invoer te valideren en de nauwkeurigheid van de vertaling te verbeteren
> * De WPF-app uitvoeren

### <a name="cognitive-services-used-in-this-tutorial"></a>Cognitive Services die in deze zelfstudie worden gebruikt

Deze lijst bevat de Cognitive Services in deze zelfstudie worden gebruikt. Klik op de koppeling om de API-naslaginformatie van elke functie te bekijken.

| Service | Functie | Beschrijving |
|---------|---------|-------------|
| Translator | [Talen ophalen](./reference/v3-0-languages.md) | Hiermee haalt u een volledige lijst ondersteunde talen op voor het vertalen van tekst. |
| Translator | [Vertalen](./reference/v3-0-translate.md) | Vertaal tekst in 90 talen en dialecten. |
| Translator | [Detecteren](./reference/v3-0-detect.md) | Hiermee detecteert u de taal van ingevoerde tekst. Biedt ook een betrouwbaarheidsscore voor de detectie. |
| Bing Spellingcontrole | [Spellingscontrole](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference) | Hiermee corrigeert u spelfouten om de nauwkeurigheid van de vertaling te vergroten. |

## <a name="prerequisites"></a>Vereisten

Voordat u doorgaat, zorgt u voor de volgende zaken:

* Een Azure Cognitive Services-abonnement. [Haal een Cognitive Services-toegangssleutel op](../cognitive-services-apis-create-account.md#create-a-new-azure-cognitive-services-resource).
* Een Windows-machine
* [Visual Studio 2019](https://www.visualstudio.com/downloads/) - Community of Enterprise

> [!NOTE]
> Het wordt aanbevolen om het abonnement voor deze zelfstudie in de regio VS - west te maken. Als u dit niet doet, moet u de eindpunten en regio's in de code wijzigen naar mate u deze oefening doorloopt.  

## <a name="create-a-wpf-app-in-visual-studio"></a>Een WPF-app maken in Visual Studio

Om te beginnen moet u een project opzetten in Visual Studio.

1. Open Visual Studio. Selecteer **Een nieuw project maken**.
1. Selcteer in **Een nieuw project maken** de optie **WPF-app (.NET Framework)** . U kunt C# kiezen in **Taal** om de opties te verfijnen.
1. Selecteer **Volgende** en geef uw project de naam `MSTranslatorDemo`.
1. Stel de frameworkversie in op **.NET Framework 4.7.2** of hoger en selecteer **Maken**.
   ![De naam en de Framework-versie in Visual Studio invoeren](media/name-wpf-project-visual-studio.png)

Uw project is gemaakt. Er worden twee tabbladen geopend: `MainWindow.xaml` en `MainWindow.xaml.cs`. In deze zelfstudie wordt er code toegevoegd aan deze twee bestanden. We wijzigen `MainWindow.xaml` voor de gebruikersinterface van de app. We wijzigen `MainWindow.xaml.cs` voor onze aanroepen naar Translator en Bing Spellingcontrole.
   ![Uw omgeving controleren](media/blank-wpf-project.png)

In de volgende sectie gaan we assembly's en een NuGet-pakket toevoegen aan dit project. Dit is voor aanvullende functionaliteit, zoals JSON parseren.

## <a name="add-references-and-nuget-packages-to-your-project"></a>Verwijzingen en NuGet-pakketten toevoegen aan uw project

Voor dit project zijn een klein aantal .NET Framework-assembly's en is NewtonSoft.Json nodig; deze worden geïnstalleerd via NuGet Package Manager.

### <a name="add-net-framework-assemblies"></a>.NET Framework-assembly's toevoegen

We gaan assembly's toevoegen aan het project om objecten te serialiseren en deserialiseren en om HTTP-aanvragen en -antwoorden te beheren.

1. Zoek uw project in Solution Explorer van Visual Studio. Klik met de rechtermuisknop op uw project en selecteer vervolgens **Toevoegen > Verwijzing**. **Reference Manager** wordt dan geopend.
1. Op het tabblad **Assembly's** staan alle .NET Framework-assembly's die beschikbaar zijn voor verwijzing. Gebruik de zoekbalk rechtsboven om naar verwijzingen te zoeken.
   ![Assembly-verwijzingen toevoegen](media/add-assemblies-2019.png)
1. Selecteer de volgende verwijzingen voor uw project:
   * [System.Runtime.Serialization](/dotnet/api/system.runtime.serialization)
   * [System.Web](/dotnet/api/system.web)
   * System.Web.Extensions
   * [System.Windows](/dotnet/api/system.windows)
1. Wanneer u deze verwijzingen hebt toegevoegd aan uw project, klikt u op **OK** om **Reference Manager** te sluiten.

> [!NOTE]
> Als u graag meer wilt weten over assembly-verwijzingen, ziet u [How to: Add or remove reference using the Reference Manager](/visualstudio/ide/how-to-add-or-remove-references-by-using-the-reference-manager) (Instructies: verwijzingen toevoegen of verwijderen via Reference Manager).

### <a name="install-newtonsoftjson"></a>NewtonSoft.Json installeren

In onze app wordt NewtonSoft.Json gebruikt voor het deserialiseren van JSON-objecten. Volg deze instructies voor het installeren van het pakket.

1. Zoek uw project in Solution Explorer van Visual Studio en klik met de rechtermuisknop op uw project. Selecteer **NuGet-pakketten beheren**.
1. Zoek en selecteer het tabblad **Bladeren**.
1. Voer [NewtonSoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) in de zoekbalk in.

    ![NewtonSoft.Json zoeken en installeren](media/nuget-package-manager.png)

1. Selecteer het pakket en klik op **Installeren**.
1. Sluit nadat de installatie is voltooid het tabblad.

## <a name="create-a-wpf-form-using-xaml"></a>Een WPF-formulier maken met behulp van XAML

Als u uw app wilt gebruiken, hebt u een gebruikersinterface nodig. U maakt met XAML een formulier waarmee gebruikers invoer en de gewenste talen kunnen selecteren, waarmee ze tekst ter vertaling kunnen invoeren en waarmee ze de vertalingen kunnen bekijken.

Laten we eens bekijken wat we bouwen.

![WPF XAML-gebruikersinterface](media/translator-text-csharp-xaml.png)

De gebruikersinterface bevat de volgende onderdelen:

| Naam | Type | Beschrijving |
|------|------|-------------|
| `FromLanguageComboBox` | ComboBox | Hiermee wordt een lijst weergegeven met alle talen die door Microsoft Translator worden ondersteund voor het vertalen van tekst. De gebruiker selecteert de taal waaruit hij vertaalt. |
| `ToLanguageComboBox` | ComboBox | Hiermee wordt dezelfde lijst met talen weergegeven als met `FromComboBox`, maar dit element wordt gebruikt om de taal te selecteren waarin de gebruiker vertaalt. |
| `TextToTranslate` | Tekstvak | Hiermee kan de gebruiker tekst invoeren die moet worden vertaald. |
| `TranslateButton` | Knop | Gebruik deze knop om tekst te vertalen. |
| `TranslatedTextLabel` | Label | Hiermee wordt de vertaling weergegeven. |
| `DetectedLanguageLabel` | Label | Hiermee wordt weergegeven welke taal de tekst heeft die u wilt vertalen (`TextToTranslate`). |

> [!NOTE]
> U maakt dit formulier aan de hand van de XAML-broncode, maar u kunt het formulier zelf maken met de editor van Visual Studio.

We gaan de code toevoegen aan het project.

1. Selecteer in Visual Studio het tabblad `MainWindow.xaml`.
1. Kopieer deze code in uw project en selecteer vervolgens **Bestand > MainWindow.xaml opslaan** om uw wijzigingen op te slaan.
   ```xaml
   <Window x:Class="MSTranslatorDemo.MainWindow"
           xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
           xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
           xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
           xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
           xmlns:local="clr-namespace:MSTranslatorDemo"
           mc:Ignorable="d"
           Title="Microsoft Translator" Height="400" Width="700" BorderThickness="0">
       <Grid>
           <Label x:Name="label" Content="Microsoft Translator" HorizontalAlignment="Left" Margin="39,6,0,0" VerticalAlignment="Top" Height="49" FontSize="26.667"/>
           <TextBox x:Name="TextToTranslate" HorizontalAlignment="Left" Height="23" Margin="42,160,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Width="600" FontSize="14" TabIndex="3"/>
           <Label x:Name="EnterTextLabel" Content="Text to translate:" HorizontalAlignment="Left" Margin="40,129,0,0" VerticalAlignment="Top" FontSize="14"/>
           <Label x:Name="toLabel" Content="Translate to:" HorizontalAlignment="Left" Margin="304,58,0,0" VerticalAlignment="Top" FontSize="14"/>

           <Button x:Name="TranslateButton" Content="Translate" HorizontalAlignment="Left" Margin="39,206,0,0" VerticalAlignment="Top" Width="114" Height="31" Click="TranslateButton_Click" FontSize="14" TabIndex="4" IsDefault="True"/>
           <ComboBox x:Name="ToLanguageComboBox"
                   HorizontalAlignment="Left"
                   Margin="306,88,0,0"
                   VerticalAlignment="Top"
                   Width="175" FontSize="14" TabIndex="2">

           </ComboBox>
           <Label x:Name="fromLabel" Content="Translate from:" HorizontalAlignment="Left" Margin="40,58,0,0" VerticalAlignment="Top" FontSize="14"/>
           <ComboBox x:Name="FromLanguageComboBox"
               HorizontalAlignment="Left"
               Margin="42,88,0,0"
               VerticalAlignment="Top"
               Width="175" FontSize="14" TabIndex="1"/>
           <Label x:Name="TranslatedTextLabel" Content="Translation is displayed here." HorizontalAlignment="Left" Margin="39,255,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="85" BorderThickness="0"/>
           <Label x:Name="DetectedLanguageLabel" Content="Autodetected language is displayed here." HorizontalAlignment="Left" Margin="39,288,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="84" BorderThickness="0"/>
       </Grid>
   </Window>
   ```
U ziet nu een voorbeeld van de gebruikersinterface van de app in Visual Studio. Dit ziet er ongeveer uit zoals in de bovenstaande afbeelding.

Dat was het. Het formulier is klaar. Nu gaan we code schrijven voor het gebruik van Text Translation en Bing Spellingcontrole.

> [!NOTE]
> U kunt dit formulier nog aanpassen of zelf een formulier maken.

## <a name="create-your-app"></a>Uw app maken

`MainWindow.xaml.cs` bevat de code waarmee de app wordt bestuurd. In de volgende gedeelten gaat u code toevoegen om de vervolgkeuzelijsten te vullen en om een reeks API's voor Translator en Bing Spellingcontrole aan te roepen.

* Wanneer het programma wordt gestart en `MainWindow` wordt geopend, wordt de methode `Languages` van Translator aangeroepen voor het ophalen van de vervolgkeuzelijsten voor taalselectie en om deze te vullen. Dit gebeurt één keer, aan het begin van elke sessie.
* Wanneer er op de knop **Vertalen** wordt geklikt, worden de door de gebruiker geselecteerde taal en tekst opgehaald, wordt er een spellingscontrole uitgevoerd voor de invoer en worden de vertaling en gedetecteerde taal weergegeven aan de gebruiker.
  * De methode `Translate` van Translator wordt aangeroepen om de tekst uit `TextToTranslate` te vertalen. Deze aanroep bevat ook de talen `to` en `from` die zijn geselecteerd via de vervolgkeuzelijsten.
  * De methode `Detect` van Translator wordt aangeroepen om de taal van `TextToTranslate` te bepalen.
  * Bing Spellingcontrole wordt gebruikt om `TextToTranslate` te valideren en spellingsfouten te corrigeren.

Het volledige project bevindt zich in de klasse `MainWindow : Window`. We beginnen door code toe te voegen om uw abonnementssleutel in te stellen, eindpunten op te geven voor Translator en Bing Spellingcontrole en de app te initialiseren.

1. Selecteer in Visual Studio het tabblad `MainWindow.xaml.cs`.
1. Vervang de vooraf ingevulde `using`-instructies door het volgende.  
   ```csharp
   using System;
   using System.Windows;
   using System.Net;
   using System.Net.Http;
   using System.IO;
   using System.Collections.Generic;
   using System.Linq;
   using System.Text;
   using Newtonsoft.Json;
   ```
1. Zoek de klasse `MainWindow : Window` en vervang deze door deze code:
   ```csharp
   {
       // This sample uses the Cognitive Services subscription key for all services. To learn more about
       // authentication options, see: https://docs.microsoft.com/azure/cognitive-services/authentication.
       const string COGNITIVE_SERVICES_KEY = "YOUR_COG_SERVICES_KEY";
       // Endpoints for Translator and Bing Spell Check
       public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
       const string BING_SPELL_CHECK_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/bing/v7.0/spellcheck/";
       // An array of language codes
       private string[] languageCodes;

       // Dictionary to map language codes from friendly name (sorted case-insensitively on language name)
       private SortedDictionary<string, string> languageCodesAndTitles =
           new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

       // Global exception handler to display error message and exit
       private static void HandleExceptions(object sender, UnhandledExceptionEventArgs args)
       {
           Exception e = (Exception)args.ExceptionObject;
           MessageBox.Show("Caught " + e.Message, "Error", MessageBoxButton.OK, MessageBoxImage.Error);
           System.Windows.Application.Current.Shutdown();
       }
       // MainWindow constructor
       public MainWindow()
       {
           // Display a message if unexpected error is encountered
           AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

           if (COGNITIVE_SERVICES_KEY.Length != 32)
           {
               MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                   "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                   "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
               System.Windows.Application.Current.Shutdown();
           }
           else
           {
               // Start GUI
               InitializeComponent();
               // Get languages for drop-downs
               GetLanguagesForTranslate();
               // Populate drop-downs with values from GetLanguagesForTranslate
               PopulateLanguageMenus();
           }
       }
   // NOTE:
   // In the following sections, we'll add code below this.
   }
   ```
1. Voeg uw Cognitive Services-abonnementssleutel toe en sla de wijzigingen op.

In dit codeblok hebben we twee variabelen opgegeven die informatie bevatten over de talen die beschikbaar zijn voor vertaling:

| Variabele | Type | Beschrijving |
|----------|------|-------------|
|`languageCodes` | Matrix van tekenreeksen |Hiermee worden de taalcodes opgeslagen in de cache. De Translator-service gebruikt korte codes om talen te identificeren, bijvoorbeeld `en` voor Engels. |
|`languageCodesAndTitles` | Gesorteerde woordenlijst | Hiermee worden de beschrijvende namen in de gebruikersinterface terugverwezen naar de korte codes die in de API worden gebruikt. Ze worden op alfabetische volgorde gesorteerd, zonder rekening te houden met het gebruik van hoofdletters. |

In de `MainWindow`-constructor is foutafhandeling met `HandleExceptions` toegevoegd. Deze foutafhandeling zorgt ervoor dat er een waarschuwing wordt gegeven als een uitzondering niet is verwerkt. Vervolgens wordt er een controle uitgevoerd om te bevestigen dat de opgegeven abonnementssleutel 32 tekens lang is. Er wordt een fout gemeld als de sleutel korter of langer is dan 32 tekens.

Als er sleutels met minstens 32 tekens zijn, brengt de aanroep `InitializeComponent()` de gebruikersinterface op gang door de XAML-beschrijving van het hoofdtoepassingsvenster te zoeken, te laden en te instantiëren.

Tot slot is er code toegevoegd om methoden aan te roepen voor het ophalen van de talen voor vertaling en om de vervolgkeuzelijsten in de gebruikersinterface van de app te vullen. We bespreken straks de code achter deze aanroepen.

## <a name="get-supported-languages"></a>Ondersteunde talen ophalen

De vertaler ondersteunt momenteel 90 talen en dialecten. In de loop van de tijd wordt er ondersteuning voor verschillende talen toegevoegd; daarom wordt het aanbevolen de talenresource van Translator aan te roepen in plaats van de lijst talen vast te leggen in uw app.

In deze sectie maken we een `GET`-aanvraag voor de talenresource. Hierbij geven we op dat we een lijst willen zien van de talen die beschikbaar zijn voor vertaling.

> [!NOTE]
> Met de talenresource kunt u de ondersteunde talen filteren op basis van de volgende queryparameters: transliteratie, woordenlijst en vertaling. Zie de [API-naslaginformatie](./reference/v3-0-languages.md) voor meer informatie.

Voordat we verdergaan, bekijkt u de voorbeelduitvoer van een aanroep naar de talenresource:

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    }
    // Additional languages are provided in the full JSON output.
}
```

In deze uitvoer kunt u de taalcode en de `name` van een specifieke taal extraheren. In onze app wordt NewtonSoft.Json gebruikt voor het deserialiseren van het JSON-object ([`JsonConvert.DeserializeObject`](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm)).

Nu gaan we verder waar we in het vorige gedeelte zijn gebleven: u gaat een methode toevoegen om de ondersteunde talen aan de app toe te voegen.

1. Open in Visual Studio het tabblad `MainWindow.xaml.cs`.
2. Voeg deze code toe aan uw project:
   ```csharp
   // ***** GET TRANSLATABLE LANGUAGE CODES
   private void GetLanguagesForTranslate()
   {
       // Send request to get supported language codes
       string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
       WebRequest WebRequest = WebRequest.Create(uri);
       WebRequest.Headers.Add("Accept-Language", "en");
       WebResponse response = null;
       // Read and parse the JSON response
       response = WebRequest.GetResponse();
       using (var reader = new StreamReader(response.GetResponseStream(), UnicodeEncoding.UTF8))
       {
           var result = JsonConvert.DeserializeObject<Dictionary<string, Dictionary<string, Dictionary<string, string>>>>(reader.ReadToEnd());
           var languages = result["translation"];

           languageCodes = languages.Keys.ToArray();
           foreach (var kv in languages)
           {
               languageCodesAndTitles.Add(kv.Value["name"], kv.Key);
           }
       }
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

Met de methode `GetLanguagesForTranslate()` wordt een HTTP GET-aanvraag gemaakt en wordt de queryreeksparameter `scope=translation` gebruikt om het bereik van de aanvraag te beperken tot talen die beschikbaar zijn voor vertaling. De header `Accept-Language` met de waarde `en` wordt toegevoegd, zodat de ondersteunde talen worden geretourneerd in het Engels.

Het JSON-antwoord wordt geparseerd en omgezet in een woordenlijst. De taalcodes worden vervolgens toegevoegd aan de lidvariabele `languageCodes`. De sleutel-waardeparen die de taalcodes en de beschrijvende namen van de talen bevatten, worden doorgegeven en toegevoegd aan de lidvariabele `languageCodesAndTitles`. In de vervolgkeuzelijsten in het formulier worden de beschrijvende namen weergegeven, maar u hebt de codes wel nodig om de vertaling aan te vragen.

## <a name="populate-language-drop-down-menus"></a>De vervolgkeuzelijsten voor taal vullen

De gebruikersinterface wordt gedefinieerd met XAML, dus voor het instellen hoeft u niet veel te doen, behalve dat u `InitializeComponent()` moet aanroepen. Het enige wat u moet doen, is de beschrijvende namen van de talen toevoegen aan de vervolgkeuzelijsten **Vertalen van** en **Vertalen naar**. De methode `PopulateLanguageMenus()` voegt de namen toe.

1. Open in Visual Studio het tabblad `MainWindow.xaml.cs`.
2. Voeg deze code toe aan uw project, onder de methode `GetLanguagesForTranslate()`:
   ```csharp
   private void PopulateLanguageMenus()
   {
       // Add option to automatically detect the source language
       FromLanguageComboBox.Items.Add("Detect");

       int count = languageCodesAndTitles.Count;
       foreach (string menuItem in languageCodesAndTitles.Keys)
       {
           FromLanguageComboBox.Items.Add(menuItem);
           ToLanguageComboBox.Items.Add(menuItem);
       }

       // Set default languages
       FromLanguageComboBox.SelectedItem = "Detect";
       ToLanguageComboBox.SelectedItem = "English";
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

Met deze methode wordt de woordenlijst `languageCodesAndTitles` herhaald en wordt elke sleutel toegevoegd aan beide menu's. Nadat de menu's zijn gevuld, worden de standaardtalen 'van' en 'naar' respectievelijk ingesteld op **Detecteren** en **Engels**.

> [!TIP]
> Als er geen standaardselectie is voor de menu's, kan de gebruiker op **Vertalen** klikken, zonder dat hij eerst een 'naar'- of 'van'-taal hoeft te kiezen. De standaardinstellingen zorgen ervoor dat het niet meer nodig is om dit probleem op te lossen.

Nu `MainWindow` is geïnitialiseerd en de gebruikersinterface is gemaakt, wordt de code pas uitgevoerd wanneer er op de knop **Vertalen** wordt geklikt.

## <a name="detect-language-of-source-text"></a>De taal van brontekst detecteren

We gaan nu een methode maken voor het detecteren van de taal van een brontekst (tekst ingevoerd in het tekstgedeelte). Dit doen we met Translator. De waarde die door deze aanvraag wordt geretourneerd, wordt later in de vertaalaanvraag gebruikt.

1. Open in Visual Studio het tabblad `MainWindow.xaml.cs`.
2. Voeg deze code toe aan uw project, onder de methode `PopulateLanguageMenus()`:
   ```csharp
   // ***** DETECT LANGUAGE OF TEXT TO BE TRANSLATED
   private string DetectLanguage(string text)
   {
       string detectUri = string.Format(TEXT_TRANSLATION_API_ENDPOINT ,"detect");

       // Create request to Detect languages with Translator
       HttpWebRequest detectLanguageWebRequest = (HttpWebRequest)WebRequest.Create(detectUri);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Region", "westus");
       detectLanguageWebRequest.ContentType = "application/json; charset=utf-8";
       detectLanguageWebRequest.Method = "POST";

       // Send request
       var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
       string jsonText = serializer.Serialize(text);

       string body = "[{ \"Text\": " + jsonText + " }]";
       byte[] data = Encoding.UTF8.GetBytes(body);

       detectLanguageWebRequest.ContentLength = data.Length;

       using (var requestStream = detectLanguageWebRequest.GetRequestStream())
           requestStream.Write(data, 0, data.Length);

       HttpWebResponse response = (HttpWebResponse)detectLanguageWebRequest.GetResponse();

       // Read and parse JSON response
       var responseStream = response.GetResponseStream();
       var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
       dynamic jsonResponse = serializer.DeserializeObject(jsonString);

       // Fish out the detected language code
       var languageInfo = jsonResponse[0];
       if (languageInfo["score"] > (decimal)0.5)
       {
           DetectedLanguageLabel.Content = languageInfo["language"];
           return languageInfo["language"];
       }
       else
           return "Unable to confidently detect input language.";
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

Met deze methode wordt een `POST` HTTP-aanvraag gemaakt voor de detectieresource. Er is maar één argument, `text`, voor nodig, en dit wordt doorgegeven als de hoofdtekst van de aanvraag. Later, wanneer we de vertaalaanvraag maken, wordt de tekst die in de gebruikersinterface wordt ingevoerd, aan deze methode doorgegeven voor de taaldetectie.

Daarnaast wordt met deze methode de betrouwbaarheidsscore van het antwoord geëvalueerd. Als de score hoger is dan `0.5`, wordt de gedetecteerde taal weergegeven in de gebruikersinterface.

## <a name="spell-check-the-source-text"></a>De spelling van de brontekst controleren

We gaan nu een methode maken om de spelling van de brontekst te controleren met de Bing Spellingcontrole-API. Op deze manier weet u zeker dat er nauwkeurige vertalingen worden verkregen van Translator. Alle correcties in de brontekst worden doorgegeven in de vertaalaanvraag wanneer er op de knop **Vertalen** wordt geklikt.

1. Open in Visual Studio het tabblad `MainWindow.xaml.cs`.
2. Voeg deze code toe aan uw project, onder de methode `DetectLanguage()`:

```csharp
// ***** CORRECT SPELLING OF TEXT TO BE TRANSLATED
private string CorrectSpelling(string text)
{
    string uri = BING_SPELL_CHECK_API_ENDPOINT + "?mode=spell&mkt=en-US";

    // Create a request to Bing Spell Check API
    HttpWebRequest spellCheckWebRequest = (HttpWebRequest)WebRequest.Create(uri);
    spellCheckWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
    spellCheckWebRequest.Method = "POST";
    spellCheckWebRequest.ContentType = "application/x-www-form-urlencoded"; // doesn't work without this

    // Create and send the request
    string body = "text=" + System.Web.HttpUtility.UrlEncode(text);
    byte[] data = Encoding.UTF8.GetBytes(body);
    spellCheckWebRequest.ContentLength = data.Length;
    using (var requestStream = spellCheckWebRequest.GetRequestStream())
        requestStream.Write(data, 0, data.Length);
    HttpWebResponse response = (HttpWebResponse)spellCheckWebRequest.GetResponse();

    // Read and parse the JSON response; get spelling corrections
    var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
    var responseStream = response.GetResponseStream();
    var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
    dynamic jsonResponse = serializer.DeserializeObject(jsonString);
    var flaggedTokens = jsonResponse["flaggedTokens"];

    // Construct sorted dictionary of corrections in reverse order (right to left)
    // This ensures that changes don't impact later indexes
    var corrections = new SortedDictionary<int, string[]>(Comparer<int>.Create((a, b) => b.CompareTo(a)));
    for (int i = 0; i < flaggedTokens.Length; i++)
    {
        var correction = flaggedTokens[i];
        var suggestion = correction["suggestions"][0];  // Consider only first suggestion
        if (suggestion["score"] > (decimal)0.7)         // Take it only if highly confident
            corrections[(int)correction["offset"]] = new string[]   // dict key   = offset
                { correction["token"], suggestion["suggestion"] };  // dict value = {error, correction}
    }

    // Apply spelling corrections, in order, from right to left
    foreach (int i in corrections.Keys)
    {
        var oldtext = corrections[i][0];
        var newtext = corrections[i][1];

        // Apply capitalization from original text to correction - all caps or initial caps
        if (text.Substring(i, oldtext.Length).All(char.IsUpper)) newtext = newtext.ToUpper();
        else if (char.IsUpper(text[i])) newtext = newtext[0].ToString().ToUpper() + newtext.Substring(1);

        text = text.Substring(0, i) + newtext + text.Substring(i + oldtext.Length);
    }
    return text;
}
// NOTE:
// In the following sections, we'll add code below this.
```

## <a name="translate-text-on-click"></a>Tekst vertalen bij klikken

Het laatste dat u moet doen, is een methode maken die wordt aangeroepen wanneer op de knop **Vertalen** in de gebruikersinterface wordt geklikt.

1. Open in Visual Studio het tabblad `MainWindow.xaml.cs`.
1. Voeg deze code toe aan uw project, onder de methode `CorrectSpelling()`, en sla de wijzigingen op:  
   ```csharp
   // ***** PERFORM TRANSLATION ON BUTTON CLICK
   private async void TranslateButton_Click(object sender, EventArgs e)
   {
       string textToTranslate = TextToTranslate.Text.Trim();

       string fromLanguage = FromLanguageComboBox.SelectedValue.ToString();
       string fromLanguageCode;

       // auto-detect source language if requested
       if (fromLanguage == "Detect")
       {
           fromLanguageCode = DetectLanguage(textToTranslate);
           if (!languageCodes.Contains(fromLanguageCode))
           {
               MessageBox.Show("The source language could not be detected automatically " +
                   "or is not supported for translation.", "Language detection failed",
                   MessageBoxButton.OK, MessageBoxImage.Error);
               return;
           }
       }
       else
           fromLanguageCode = languageCodesAndTitles[fromLanguage];

       string toLanguageCode = languageCodesAndTitles[ToLanguageComboBox.SelectedValue.ToString()];

       // spell-check the source text if the source language is English
       if (fromLanguageCode == "en")
       {
           if (textToTranslate.StartsWith("-"))    // don't spell check in this case
               textToTranslate = textToTranslate.Substring(1);
           else
           {
               textToTranslate = CorrectSpelling(textToTranslate);
               TextToTranslate.Text = textToTranslate;     // put corrected text into input field
           }
       }
       // handle null operations: no text or same source/target languages
       if (textToTranslate == "" || fromLanguageCode == toLanguageCode)
       {
           TranslatedTextLabel.Content = textToTranslate;
           return;
       }

       // send HTTP request to perform the translation
       string endpoint = string.Format(TEXT_TRANSLATION_API_ENDPOINT, "translate");
       string uri = string.Format(endpoint + "&from={0}&to={1}", fromLanguageCode, toLanguageCode);

       System.Object[] body = new System.Object[] { new { Text = textToTranslate } };
       var requestBody = JsonConvert.SerializeObject(body);

       using (var client = new HttpClient())
       using (var request = new HttpRequestMessage())
       {
           request.Method = HttpMethod.Post;
           request.RequestUri = new Uri(uri);
           request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
           request.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
           request.Headers.Add("Ocp-Apim-Subscription-Region", "westus");
           request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

           var response = await client.SendAsync(request);
           var responseBody = await response.Content.ReadAsStringAsync();

           var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
           var translation = result[0]["translations"][0]["text"];

           // Update the translation field
           TranslatedTextLabel.Content = translation;
       }
   }
   ```

De eerste stap is om de talen 'van' en 'naar' op te halen, alsmede de tekst die de gebruiker heeft ingevoerd in het formulier. Als de brontaal is ingesteld op **Detecteren**, wordt `DetectLanguage()` aangeroepen om de taal van de brontekst te bepalen. De tekst kan een taal hebben die Translator niet ondersteunt. In dat geval geeft u een bericht weer om de gebruiker hiervan op de hoogte te stellen en gaat u terug zonder een vertaling van de tekst te geven.

Als de brontaal Engels is (ongeacht of deze is opgegeven of gedetecteerd), controleert u de tekst op spelfouten met `CorrectSpelling()` en voert u eventuele correcties door. De gecorrigeerde tekst wordt terug in het tekstvak geplaatst zodat de gebruiker ziet dat er een correctie is doorgevoerd.

De code voor het vertalen van tekst moet u bekend voorkomen: de URI bouwen, een aanvraag maken en verzenden en het antwoord parseren. De JSON-matrix bevat mogelijk meer dan één object voor vertaling, maar voor onze app is er slechts een nodig.

Na een geslaagde aanvraag wordt `TranslatedTextLabel.Content` vervangen door de `translation`, welke de gebruikersinterface bijwerkt zodat de vertaalde tekst wordt weergegeven.

## <a name="run-your-wpf-app"></a>De WPF-app uitvoeren

Dat was het. U hebt nu een werkende vertaal-app gebouwd met WPF. Als u de app wilt uitvoeren, klikt u op de knop **Starten** in Visual Studio.

## <a name="source-code"></a>Broncode

De broncode voor dit project is beschikbaar op GitHub.

* [Broncode verkennen](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-C-Sharp-Tutorial)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Microsoft Translator-verwijzing](./reference/v3-0-reference.md)
