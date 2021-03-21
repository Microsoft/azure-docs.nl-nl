---
title: Zelf studie voor de Windows Store-app Smooth Streaming | Microsoft Docs
description: Meer informatie over het gebruik van Azure Media Services voor het maken van een C# Windows Store-toepassing met een XML-media element-besturings element voor het afspelen van een Smoothe stream-inhoud.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 0fa5d8c5-3d5f-4886-ae55-fb6de4f5256d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 20c7068fa0cb56699fdbc6d75b279abadd583832
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103017115"
---
# <a name="how-to-build-a-smooth-streaming-windows-store-application"></a>Een Smooth Streaming Windows Store-toepassing bouwen

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

Met de Smooth Streaming client-SDK voor Windows 8 kunnen ontwikkel aars Windows Store-toepassingen bouwen die on-demand en live Smooth Streaming inhoud kunnen afspelen. Naast het eenvoudig afspelen van Smooth Streaming inhoud, biedt de SDK ook uitgebreide functies zoals micro soft PlayReady Protection, beperking van kwaliteits niveau, Live DVR, audio stream switches, Luis teren naar status updates (zoals wijzigingen op kwaliteits niveau) en fout gebeurtenissen, enzovoort. Zie de [release opmerkingen](https://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes)voor meer informatie over de ondersteunde functies. Zie [Player Framework voor Windows 8](https://playerframework.codeplex.com/)(Engelstalig) voor meer informatie. 

Deze zelf studie bevat vier lessen:

1. Een Basic Smooth Streaming Store-toepassing maken
2. Een schuif regelaar toevoegen voor het beheren van de voortgang van media
3. Smooth Streaming streams selecteren
4. Smooth Streaming-tracks selecteren

## <a name="prerequisites"></a>Vereisten
> [!NOTE]
> Windows Store-projecten versie 8,1 en lager worden niet ondersteund in Visual Studio 2017.  Zie [Geschikte platforms voor Visual Studio 2017 en compatibiliteit](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs) voor meer informatie.

* Windows 8 32-bits of 64-bits.
* Visual Studio versie 2012 tot en met 2015.
* [Micro soft Smooth streaming client-SDK voor Windows 8](https://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home).

De voltooide oplossing voor elke les kan worden gedownload van MSDN Developer code samples (code galerie): 

* [Les 1](https://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f) : een eenvoudige Windows 8-Smooth Streaming Media Player, 
* [Les 2](https://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a) : een eenvoudige Windows 8-Smooth Streaming Media Player met een schuif balk besturings element, 
* [Les 3](https://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44) : een Windows 8-Smooth Streaming Media Player met stroom selectie,  
* [Les 4](https://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907)  : een Windows 8-Smooth Streaming Media Player met de optie bijhouden.

## <a name="lesson-1-create-a-basic-smooth-streaming-store-application"></a>Les 1: een basis Smooth Streaming Store-toepassing maken

In deze les maakt u een Windows Store-toepassing met een media element-besturings element voor het afspelen van de inhoud van een gladde stroom.  De toepassing die wordt uitgevoerd, ziet er als volgt uit:

![Smooth Streaming voor beeld van Windows Store-toepassing][PlayerApplication]

Zie voor meer informatie over het ontwikkelen van Windows Store-toepassing [fantastische apps ontwikkelen voor Windows 8](https://developer.microsoft.com/en-us/windows/). Deze les bevat de volgende procedures:

1. Een Windows Store-project maken
2. De gebruikers interface (XAML) ontwerpen
3. De code achter het bestand wijzigen
4. De toepassing compileren en testen

### <a name="to-create-a-windows-store-project"></a>Een Windows Store-project maken

1. Visual Studio uitvoeren; versies 2012 tot en met 2015 worden ondersteund.
1. Klik in het menu **bestand** op **Nieuw** en klik vervolgens op **project**.
1. Typ of Selecteer in het dialoog venster Nieuw project de volgende waarden:

    | Name | Waarde |
    | --- | --- |
    | Sjabloon groep |Geïnstalleerd/templates/Visual C#/Windows Store |
    | Sjabloon |Lege app (XAML) |
    | Name |SSPlayer |
    | Locatie |C:\SSTutorials |
    | Naam van de oplossing |SSPlayer |
    | Map maken voor oplossing |(geselecteerd) |

1. Klik op **OK**.

### <a name="to-add-a-reference-to-the-smooth-streaming-client-sdk"></a>Een verwijzing naar de Smooth Streaming client-SDK toevoegen

1. Klik in Solution Explorer met de rechter muisknop op **SSPlayer** en klik vervolgens op **verwijzing toevoegen**.
1. Typ of selecteer de volgende waarden:

    | Name | Waarde |
    | --- | --- |
    | Verwijzings groep |Windows/uitbrei dingen |
    | Naslaginformatie |Selecteer micro soft Smooth Streaming client SDK voor Windows 8 en micro soft Visual C++ runtime-pakket |

1. Klik op **OK**. 

Nadat u de verwijzingen hebt toegevoegd, moet u het doel platform (x64 of x86) selecteren. het toevoegen van verwijzingen zal niet werken voor configuratie van een CPU-platform.  In Solution Explorer ziet u een geel waarschuwings teken voor deze toegevoegde verwijzingen.

### <a name="to-design-the-player-user-interface"></a>De gebruikers interface van de speler ontwerpen

1. Dubbel klik in Solution Explorer op **MainPage. xaml** om deze te openen in de ontwerp weergave.
2. Zoek het **&lt; raster &gt;** en **&lt; /grid &gt;** labels het XAML-bestand en plak de volgende code tussen de twee Tags:

   ```xml
         <Grid.RowDefinitions>

            <RowDefinition Height="20"/>    <!-- spacer -->
            <RowDefinition Height="50"/>    <!-- media controls -->
            <RowDefinition Height="100*"/>  <!-- media element -->
            <RowDefinition Height="80*"/>   <!-- media stream and track selection -->
            <RowDefinition Height="50"/>    <!-- status bar -->
         </Grid.RowDefinitions>

         <StackPanel Name="spMediaControl" Grid.Row="1" Orientation="Horizontal">
            <TextBlock x:Name="tbSource" Text="Source :  " FontSize="16" FontWeight="Bold" VerticalAlignment="Center" />
            <TextBox x:Name="txtMediaSource" Text="https://ecn.channel9.msdn.com/o9/content/smf/smoothcontent/elephantsdream/Elephants_Dream_1024-h264-st-aac.ism/manifest" FontSize="10" Width="700" Margin="0,4,0,10" />
            <Button x:Name="btnSetSource" Content="Set Source" Width="111" Height="43" Click="btnSetSource_Click"/>
            <Button x:Name="btnPlay" Content="Play" Width="111" Height="43" Click="btnPlay_Click"/>
            <Button x:Name="btnPause" Content="Pause"  Width="111" Height="43" Click="btnPause_Click"/>
            <Button x:Name="btnStop" Content="Stop"  Width="111" Height="43" Click="btnStop_Click"/>
            <CheckBox x:Name="chkAutoPlay" Content="Auto Play" Height="55" Width="Auto" IsChecked="{Binding AutoPlay, ElementName=mediaElement, Mode=TwoWay}"/>
            <CheckBox x:Name="chkMute" Content="Mute" Height="55" Width="67" IsChecked="{Binding IsMuted, ElementName=mediaElement, Mode=TwoWay}"/>
         </StackPanel>

         <StackPanel Name="spMediaElement" Grid.Row="2" Height="435" Width="1072"
                    HorizontalAlignment="Center" VerticalAlignment="Center">
            <MediaElement x:Name="mediaElement" Height="356" Width="924" MinHeight="225"
                          HorizontalAlignment="Center" VerticalAlignment="Center" 
                          AudioCategory="BackgroundCapableMedia" />
            <StackPanel Orientation="Horizontal">
                <Slider x:Name="sliderProgress" Width="924" Height="44"
                        HorizontalAlignment="Center" VerticalAlignment="Center"
                        PointerPressed="sliderProgress_PointerPressed"/>
                <Slider x:Name="sliderVolume" 
                        HorizontalAlignment="Right" VerticalAlignment="Center" Orientation="Vertical" 
                        Height="79" Width="148" Minimum="0" Maximum="1" StepFrequency="0.1" 
                        Value="{Binding Volume, ElementName=mediaElement, Mode=TwoWay}" 
                        ToolTipService.ToolTip="{Binding Value, RelativeSource={RelativeSource Mode=Self}}"/>
            </StackPanel>
         </StackPanel>

         <StackPanel Name="spStatus" Grid.Row="4" Orientation="Horizontal">
            <TextBlock x:Name="tbStatus" Text="Status :  " 
               FontSize="16" FontWeight="Bold" VerticalAlignment="Center" HorizontalAlignment="Center" />
            <TextBox x:Name="txtStatus" FontSize="10" Width="700" VerticalAlignment="Center"/>
         </StackPanel>
   ```
   Het besturings element media element wordt gebruikt voor het afspelen van media. Het besturings element schuif regelaar met de naam sliderProgress wordt gebruikt in de volgende les om de voortgang van de media te bepalen.
3. Druk op **CTRL + S** om het bestand op te slaan.

Het media element-besturings element biedt geen ondersteuning voor Smooth Streaming-inhoud out-of-Box. Als u de ondersteuning voor Smooth Streaming wilt inschakelen, moet u de Smooth Streaming byte-stream-handler registreren op basis van de bestandsnaam extensie en het MIME-type.  Als u zich wilt registreren, gebruikt u de methode MediaExtensionManager. RegisterByteStreamHandler van de Windows. Media-naam ruimte.

Sommige gebeurtenis-handlers zijn in dit XAML-bestand gekoppeld aan de besturings elementen.  U moet deze gebeurtenis-handlers definiëren.

### <a name="to-modify-the-code-behind-file"></a>De code achter het bestand wijzigen

1. Klik in Solution Explorer met de rechter muisknop op **MainPage. xaml** en klik vervolgens op **code weer geven**.
2. Voeg boven aan het bestand de volgende instructie toe met behulp van:

    ```csharp
        using Windows.Media;
    ```

3. Voeg aan het begin van de klasse **Mainpage** het volgende gegevenslid toe:

    ```csharp
        private MediaExtensionManager extensions = new MediaExtensionManager();
    ```

4. Voeg aan het einde van de **Mainpage** -constructor de volgende twee regels toe:

    ```csharp
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");
    ```

5. Plak de volgende code aan het einde van de **Mainpage** -klasse:
   ```csharp
         # region UI Button Click Events
         private void btnPlay_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Play();
         txtStatus.Text = "MediaElement is playing ...";
         }
         private void btnPause_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Pause();
         txtStatus.Text = "MediaElement is paused";
         }
         private void btnSetSource_Click(object sender, RoutedEventArgs e)
         {

         sliderProgress.Value = 0;
         mediaElement.Source = new Uri(txtMediaSource.Text);

         if (chkAutoPlay.IsChecked == true)
         {
             txtStatus.Text = "MediaElement is playing ...";
         }
         else
         {
             txtStatus.Text = "Click the Play button to play the media source.";
         }
         }
         private void btnStop_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Stop();
         txtStatus.Text = "MediaElement is stopped";
         }
         private void sliderProgress_PointerPressed(object sender, PointerRoutedEventArgs e)
         {

         txtStatus.Text = "Seek to position " + sliderProgress.Value;
         mediaElement.Position = new TimeSpan(0, 0, (int)(sliderProgress.Value));
         }
         # endregion
   ```
   De gebeurtenis-handler sliderProgress_PointerPressed wordt hier gedefinieerd.  Er zijn nog meer werk taken die u kunt uitvoeren. dit wordt behandeld in de volgende les van deze zelf studie.
6. Druk op **CTRL + S** om het bestand op te slaan.

De voltooide code achter het bestand ziet er als volgt uit:

![Code View in Visual Studio van Smooth Streaming Windows Store-toepassing][CodeViewPic]

### <a name="to-compile-and-test-the-application"></a>De toepassing compileren en testen

1. Klik in het menu **bouwen** op **Configuration Manager**.
2. Wijzig het **actieve oplossings platform** zodat dit overeenkomt met uw ontwikkel platform.
3. Druk op **F6** om het project te compileren. 
4. Druk op **F5** om de toepassing uit te voeren.
5. Boven aan de toepassing kunt u de standaard-Smooth Streaming URL gebruiken of een andere naam invoeren. 
6. Klik op **bron instellen**. Omdat **automatisch afspelen** standaard is ingeschakeld, moet de media automatisch worden afgespeeld.  U kunt de media beheren met de knoppen **afspelen**, **onderbreken** en **stoppen** .  U kunt het Media volume best uren met behulp van de verticale schuif regelaar.  De horizontale schuif regelaar voor het beheren van de media voortgang is echter nog niet volledig geïmplementeerd. 

U hebt lesson1 voltooid.  In deze les gebruikt u een media element-besturings element om Smooth Streaming inhoud af te spelen.  In de volgende les gaat u een schuif regelaar toevoegen om de voortgang van de Smooth Streaming inhoud te bepalen.

## <a name="lesson-2-add-a-slider-bar-to-control-the-media-progress"></a>Les 2: een schuif regelaar toevoegen om de voortgang van media te bepalen

In Les 1 hebt u een Windows Store-toepassing gemaakt met een media element XAML-besturings element voor het afspelen van Smooth Streaming media-inhoud.  Het bevat een aantal basis media functies als starten, stoppen en onderbreken.  In deze les gaat u een besturings element schuif regelaar toevoegen aan de toepassing.

In deze zelf studie gebruiken we een timer om de positie van de schuif regelaar bij te werken op basis van de huidige positie van het besturings element media element.  De start-en eind tijd van de schuif regelaar moeten ook worden bijgewerkt in het geval van live-inhoud.  Dit kan beter worden afgehandeld in de gebeurtenis adaptieve bron update.

Media bronnen zijn objecten die media gegevens genereren.  De bron resolver gebruikt een URL of byte stroom en maakt de juiste media bron voor die inhoud.  De bron conflict Oplosser is de standaard methode voor de toepassingen om media bronnen te maken. 

Deze les bevat de volgende procedures:

1. De Smooth Streaming-handler registreren 
2. De gebeurtenis-handlers voor het adaptieve bron beheer niveau toevoegen
3. De gebeurtenis-handlers voor het adaptieve bron niveau toevoegen
4. Media element-gebeurtenis-handlers toevoegen
5. Aan de schuif regelaar gerelateerde code toevoegen
6. De toepassing compileren en testen

### <a name="to-register-the-smooth-streaming-byte-stream-handler-and-pass-the-propertyset"></a>De Smooth Streaming byte-stream-handler registreren en de eigenschappenset door geven

1. Klik in Solution Explorer met de rechter muisknop op **MainPage. xaml** en klik vervolgens op **code weer geven**.
2. Voeg aan het begin van het bestand de volgende instructie toe met behulp van:

   ```csharp
        using Microsoft.Media.AdaptiveStreaming;
   ```
3. Voeg aan het begin van de klasse MainPage de volgende gegevens leden toe:

   ```csharp
         private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
         private IAdaptiveSourceManager adaptiveSourceManager;
   ```
4. Voeg in de **Mainpage** -constructor de volgende code toe na de **this.Initialize-onderdelen ();** en de registratie code regels die in de vorige les zijn geschreven:

   ```csharp
        // Gets the default instance of AdaptiveSourceManager which manages Smooth 
        //Streaming media sources.
        adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
        // Sets property key value to AdaptiveSourceManager default instance.
        // {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
        propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;
   ```
5. In de **Mainpage** -constructor wijzigt u de twee RegisterByteStreamHandler-methoden om de para meters toe te voegen:

   ```csharp
         // Registers Smooth Streaming byte-stream handler for ".ism" extension and, 
         // "text/xml" and "application/vnd.ms-ss" mime-types and pass the propertyset. 
         // http://*.ism/manifest URI resources will be resolved by Byte-stream handler.
         extensions.RegisterByteStreamHandler(

            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "text/xml", 
            propertySet );
         extensions.RegisterByteStreamHandler(

            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "application/vnd.ms-sstr+xml", 
         propertySet);
   ```
6. Druk op **CTRL + S** om het bestand op te slaan.

### <a name="to-add-the-adaptive-source-manager-level-event-handler"></a>De gebeurtenis afhandeling voor het adaptieve bron beheer niveau toevoegen

1. Klik in Solution Explorer met de rechter muisknop op **MainPage. xaml** en klik vervolgens op **code weer geven**.
2. Voeg in de klasse **Mainpage** het volgende gegevenslid toe:

   ```csharp
     private AdaptiveSource adaptiveSource = null;
   ```
3. Voeg aan het einde van de **Mainpage** -klasse de volgende gebeurtenis-handler toe:

   ```csharp
         # region Adaptive Source Manager Level Events
         private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
         }

         # endregion Adaptive Source Manager Level Events
   ```
4. Voeg aan het einde van de **Mainpage** -constructor de volgende regel toe om u te abonneren op de gebeurtenis voor het openen van een adaptieve Bron:

   ```csharp
         adaptiveSourceManager.AdaptiveSourceOpenedEvent += 
           new AdaptiveSourceOpenedEventHandler(mediaElement_AdaptiveSourceOpened);
   ```
5. Druk op **CTRL + S** om het bestand op te slaan.

### <a name="to-add-adaptive-source-level-event-handlers"></a>Voor het toevoegen van een adaptieve bron niveau gebeurtenis-handlers

1. Klik in Solution Explorer met de rechter muisknop op **MainPage. xaml** en klik vervolgens op **code weer geven**.
2. Voeg in de klasse **Mainpage** het volgende gegevenslid toe:

   ```csharp
     private AdaptiveSourceStatusUpdatedEventArgs adaptiveSourceStatusUpdate; 
     private Manifest manifestObject;
   ```
3. Voeg aan het einde van de **Mainpage** -klasse de volgende gebeurtenis-handlers toe:

   ```csharp
         # region Adaptive Source Level Events
         private void mediaElement_ManifestReady(AdaptiveSource sender, ManifestReadyEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
            manifestObject = args.AdaptiveSource.Manifest;
         }

         private void mediaElement_AdaptiveSourceStatusUpdated(AdaptiveSource sender, AdaptiveSourceStatusUpdatedEventArgs args)
         {

            adaptiveSourceStatusUpdate = args;
         }

         private void mediaElement_AdaptiveSourceFailed(AdaptiveSource sender, AdaptiveSourceFailedEventArgs args)
         {

            txtStatus.Text = "Error: " + args.HttpResponse;
         }

         # endregion Adaptive Source Level Events
   ```
4. Voeg aan het einde van de **media element AdaptiveSourceOpened** -methode de volgende code toe om u te abonneren op de gebeurtenissen:

   ```csharp
         adaptiveSource.ManifestReadyEvent +=

                    mediaElement_ManifestReady;
         adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 

            mediaElement_AdaptiveSourceStatusUpdated;
         adaptiveSource.AdaptiveSourceFailedEvent += 

            mediaElement_AdaptiveSourceFailed;
   ```
5. Druk op **CTRL + S** om het bestand op te slaan.

Dezelfde gebeurtenissen zijn ook beschikbaar op het niveau van het adaptieve bron beheer, dat kan worden gebruikt voor de verwerking van de algemene functionaliteit voor alle media-elementen in de app. Elke AdaptiveSource bevat eigen gebeurtenissen en alle AdaptiveSource-gebeurtenissen worden trapsgewijs weer AdaptiveSourceManager.

### <a name="to-add-media-element-event-handlers"></a>Gebeurtenis-handlers voor media elementen toevoegen

1. Klik in Solution Explorer met de rechter muisknop op **MainPage. xaml** en klik vervolgens op **code weer geven**.
2. Voeg aan het einde van de **Mainpage** -klasse de volgende gebeurtenis-handlers toe:

   ```csharp
         # region Media Element Event Handlers
         private void MediaOpened(object sender, RoutedEventArgs e)
         {

            txtStatus.Text = "MediaElement opened";
         }

         private void MediaFailed(object sender, ExceptionRoutedEventArgs e)
         {

            txtStatus.Text= "MediaElement failed: " + e.ErrorMessage;
         }

         private void MediaEnded(object sender, RoutedEventArgs e)
         {

            txtStatus.Text ="MediaElement ended.";
         }

         # endregion Media Element Event Handlers
   ```
3. Voeg aan het einde van de **Mainpage** -constructor de volgende code toe aan subscript voor de gebeurtenissen:

   ```csharp
         mediaElement.MediaOpened += MediaOpened;
         mediaElement.MediaEnded += MediaEnded;
         mediaElement.MediaFailed += MediaFailed;
   ```
4. Druk op **CTRL + S** om het bestand op te slaan.

### <a name="to-add-slider-bar-related-code"></a>Aan de schuif balk gerelateerde code toevoegen

1. Klik in Solution Explorer met de rechter muisknop op **MainPage. xaml** en klik vervolgens op **code weer geven**.
2. Voeg aan het begin van het bestand de volgende instructie toe met behulp van:

   ```csharp
        using Windows.UI.Core;
   ```
3. Voeg in de klasse **Mainpage** de volgende gegevens leden toe:

   ```csharp
         public static CoreDispatcher _dispatcher;
         private DispatcherTimer sliderPositionUpdateDispatcher;
   ```
4. Voeg aan het einde van de **Mainpage** -constructor de volgende code toe:

   ```csharp
         _dispatcher = Window.Current.Dispatcher;
         PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
         sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    
   ```
5. Voeg aan het einde van de **Mainpage** -klasse de volgende code toe:

   ```csharp
         # region sliderMediaPlayer
         private double SliderFrequency(TimeSpan timevalue)
         {

            long absvalue = 0;
            double stepfrequency = -1;

            if (manifestObject != null)
            {
                absvalue = manifestObject.Duration - (long)manifestObject.StartTime;
            }
            else
            {
                absvalue = mediaElement.NaturalDuration.TimeSpan.Ticks;
            }

            TimeSpan totalDVRDuration = new TimeSpan(absvalue);

            if (totalDVRDuration.TotalMinutes >= 10 && totalDVRDuration.TotalMinutes < 30)
            {
               stepfrequency = 10;
            }
            else if (totalDVRDuration.TotalMinutes >= 30 
                     && totalDVRDuration.TotalMinutes < 60)
            {
                stepfrequency = 30;
            }
            else if (totalDVRDuration.TotalHours >= 1)
            {
                stepfrequency = 60;
            }

            return stepfrequency;
         }

         void updateSliderPositionoNTicks(object sender, object e)
         {

            sliderProgress.Value = mediaElement.Position.TotalSeconds;
         }

         public void setupTimer()
         {

            sliderPositionUpdateDispatcher = new DispatcherTimer();
            sliderPositionUpdateDispatcher.Interval = new TimeSpan(0, 0, 0, 0, 300);
            startTimer();
         }

         public void startTimer()
         {

            sliderPositionUpdateDispatcher.Tick += updateSliderPositionoNTicks;
            sliderPositionUpdateDispatcher.Start();
         }

         // Slider start and end time must be updated in case of live content
         public async void setSliderStartTime(long startTime)
         {

            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.StartTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Minimum = absvalue;
            });
         }

         // Slider start and end time must be updated in case of live content
         public async void setSliderEndTime(long startTime)
         {

            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Maximum = absvalue;
            });
         }

         # endregion sliderMediaPlayer
   ```

   > [!NOTE]
   > CoreDispatcher wordt gebruikt om wijzigingen aan te brengen in de UI-thread vanuit een niet-UI-thread. In het geval van een knel punt in de dispatcher-thread, kan de ontwikkelaar kiezen om dispatcher te gebruiken die wordt verschaft door de gebruikers interface-element die ze willen bijwerken.  Bijvoorbeeld:

   ```csharp
         await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 

         timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
         double absvalue  = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 

         sliderProgress.Maximum = absvalue; }); 
   ```
6. Voeg aan het einde van de **mediaElement_AdaptiveSourceStatusUpdated** -methode de volgende code toe:

   ```csharp
         setSliderStartTime(args.StartTime);
         setSliderEndTime(args.EndTime);
   ```
7. Voeg aan het einde van de methode **MediaOpened** de volgende code toe:

   ```csharp
         sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan);
         sliderProgress.Width = mediaElement.Width;
         setupTimer();
   ```
8. Druk op **CTRL + S** om het bestand op te slaan.

### <a name="to-compile-and-test-the-application"></a>De toepassing compileren en testen

1. Druk op **F6** om het project te compileren. 
2. Druk op **F5** om de toepassing uit te voeren.
3. Boven aan de toepassing kunt u de standaard-Smooth Streaming URL gebruiken of een andere naam invoeren. 
4. Klik op **bron instellen**. 
5. Test de schuif regelaar.

U hebt Les 2 voltooid.  In deze les hebt u een schuif regelaar toegevoegd aan de toepassing. 

## <a name="lesson-3-select-smooth-streaming-streams"></a>Les 3: Smooth Streaming streams selecteren
Smooth Streaming is in staat om inhoud te streamen met meerdere audio sporen in de taal die kunnen worden geselecteerd door de viewers.  In deze les schakelt u kijkers in om stromen te selecteren. Deze les bevat de volgende procedures:

1. Het XAML-bestand wijzigen
2. De code achter het bestand wijzigen
3. De toepassing compileren en testen

### <a name="to-modify-the-xaml-file"></a>Het XAML-bestand wijzigen

1. Klik vanuit Solution Explorer met de rechter muisknop op **MainPage. xaml** en klik vervolgens op **Designer weer geven**.
2. Zoek &lt; grid. RowDefinitions &gt; en wijzig de RowDefinitions zodat ze er als volgt uitzien:

   ```xml
         <Grid.RowDefinitions>            
            <RowDefinition Height="20"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="100"/>
            <RowDefinition Height="80"/>
            <RowDefinition Height="50"/>
         </Grid.RowDefinitions>
   ```
3. Voeg in het &lt; raster &gt; &lt; /grid &gt; Tags de volgende code toe om een besturings element keuze lijst te definiëren, zodat gebruikers de lijst met beschik bare streams kunnen zien en stromen selecteren:

   ```xml
         <Grid Name="gridStreamAndBitrateSelection" Grid.Row="3">
            <Grid.RowDefinitions>
                <RowDefinition Height="300"/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="250*"/>
                <ColumnDefinition Width="250*"/>
            </Grid.ColumnDefinitions>
            <StackPanel Name="spStreamSelection" Grid.Row="1" Grid.Column="0">
                <StackPanel Orientation="Horizontal">
                    <TextBlock Name="tbAvailableStreams" Text="Available Streams:" FontSize="16" VerticalAlignment="Center"></TextBlock>
                    <Button Name="btnChangeStreams" Content="Submit" Click="btnChangeStream_Click"/>
                </StackPanel>
                <ListBox x:Name="lbAvailableStreams" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                    ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
                    <ListBox.ItemTemplate>
                        <DataTemplate>
                            <CheckBox Content="{Binding Name}" IsChecked="{Binding isChecked, Mode=TwoWay}" />
                        </DataTemplate>
                    </ListBox.ItemTemplate>
                </ListBox>
            </StackPanel>
         </Grid>
   ```
4. Druk op **CTRL + S** om de wijzigingen op te slaan.

### <a name="to-modify-the-code-behind-file"></a>De code achter het bestand wijzigen

1. Klik in Solution Explorer met de rechter muisknop op **MainPage. xaml** en klik vervolgens op **code weer geven**.
2. Voeg in de naam ruimte SSPlayer een nieuwe klasse toe:

   ```csharp
        #region class Stream
   
        public class Stream
        {
            private IManifestStream stream;
            public bool isCheckedValue;
            public string name;
   
            public string Name
            {
                get { return name; }
                set { name = value; }
            }
   
            public IManifestStream ManifestStream
            {
                get { return stream; }
                set { stream = value; }
            }
   
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    // mMke the video stream always checked.
                    if (stream.Type == MediaStreamType.Video)
                    {
                        isCheckedValue = true;
                    }
                    else
                    {
                        isCheckedValue = value;
                    }
                }
            }
   
            public Stream(IManifestStream streamIn)
            {
                stream = streamIn;
                name = stream.Name;
            }
        }
        #endregion class Stream
   ```
3. Voeg aan het begin van de klasse MainPage de volgende variabele definities toe:

   ```csharp
         private List<Stream> availableStreams;
         private List<Stream> availableAudioStreams;
         private List<Stream> availableTextStreams;
         private List<Stream> availableVideoStreams;
   ```
4. Voeg in de klasse MainPage de volgende regio toe:
   ```csharp
        #region stream selection
        ///<summary>
        ///Functionality to select streams from IManifestStream available streams
        /// </summary>
   
        // This function is called from the mediaElement_ManifestReady event handler 
        // to retrieve the streams and populate them to the local data members.
        public void getStreams(Manifest manifestObject)
        {
            availableStreams = new List<Stream>();
            availableVideoStreams = new List<Stream>();
            availableAudioStreams = new List<Stream>();
            availableTextStreams = new List<Stream>();
   
            try
            {
                for (int i = 0; i<manifestObject.AvailableStreams.Count; i++)
                {
                    Stream newStream = new Stream(manifestObject.AvailableStreams[i]);
                    newStream.isChecked = false;
   
                    //populate the stream lists based on the types
                    availableStreams.Add(newStream);
   
                    switch (newStream.ManifestStream.Type)
                    {
                        case MediaStreamType.Video:
                            availableVideoStreams.Add(newStream);
                            break;
                        case MediaStreamType.Audio:
                            availableAudioStreams.Add(newStream);
                            break;
                        case MediaStreamType.Text:
                            availableTextStreams.Add(newStream);
                            break;
                    }
   
                    // Select the default selected streams from the manifest.
                    for (int j = 0; j<manifestObject.SelectedStreams.Count; j++)
                    {
                        string selectedStreamName = manifestObject.SelectedStreams[j].Name;
                        if (selectedStreamName.Equals(newStream.Name))
                        {
                            newStream.isChecked = true;
                            break;
                        }
                    }
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
   
        // This function set the list box ItemSource
        private async void refreshAvailableStreamsListBoxItemSource()
        {
            try
            {
                //update the stream check box list on the UI
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableStreams.ItemsSource = availableStreams; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
   
        // This function creates a selected streams list
        private void createSelectedStreamsList(List<IManifestStream> selectedStreams)
        {
            bool isOneVideoSelected = false;
            bool isOneAudioSelected = false;
   
            // Only one video stream can be selected
            for (int j = 0; j<availableVideoStreams.Count; j++)
            {
                if (availableVideoStreams[j].isChecked && (!isOneVideoSelected))
                {
                    selectedStreams.Add(availableVideoStreams[j].ManifestStream);
                    isOneVideoSelected = true;
                }
            }
   
            // Select the first video stream from the list if no video stream is selected
            if (!isOneVideoSelected)
            {
                availableVideoStreams[0].isChecked = true;
                selectedStreams.Add(availableVideoStreams[0].ManifestStream);
            }
   
            // Only one audio stream can be selected
            for (int j = 0; j<availableAudioStreams.Count; j++)
            {
                if (availableAudioStreams[j].isChecked && (!isOneAudioSelected))
                {
                    selectedStreams.Add(availableAudioStreams[j].ManifestStream);
                    isOneAudioSelected = true;
                    txtStatus.Text = "The audio stream is changed to " + availableAudioStreams[j].ManifestStream.Name;
                }
            }
   
            // Select the first audio stream from the list if no audio steam is selected.
            if (!isOneAudioSelected)
            {
                availableAudioStreams[0].isChecked = true;
                selectedStreams.Add(availableAudioStreams[0].ManifestStream);
            }
   
            // Multiple text streams are supported.
            for (int j = 0; j < availableTextStreams.Count; j++)
            {
                if (availableTextStreams[j].isChecked)
                {
                    selectedStreams.Add(availableTextStreams[j].ManifestStream);
                }
            }
        }
   
        // Change streams on a smooth streaming presentation with multiple video streams.
        private async void changeStreams(List<IManifestStream> selectStreams)
        {
            try
            {
                IReadOnlyList<IStreamChangedResult> returnArgs =
                    await manifestObject.SelectStreamsAsync(selectStreams);
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        #endregion stream selection
   ```
5. Ga naar de mediaElement_ManifestReady methode en voeg de volgende code toe aan het einde van de functie:
   ```csharp
        getStreams(manifestObject);
        refreshAvailableStreamsListBoxItemSource();
   ```
    Wanneer media element-manifest klaar is, haalt de code een lijst van de beschik bare streams en vult de keuze lijst met gebruikers interface met de lijst.
6. Zoek in de klasse MainPage de gebruikers interface knoppen op gebeurtenissen regio en voeg de volgende functie definitie toe:
   ```csharp
        private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();
   
            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);
   
            // Change streams on the presentation
            changeStreams(selectedStreams);
        }
   ```

### <a name="to-compile-and-test-the-application"></a>De toepassing compileren en testen

1. Druk op **F6** om het project te compileren. 
2. Druk op **F5** om de toepassing uit te voeren.
3. Boven aan de toepassing kunt u de standaard-Smooth Streaming URL gebruiken of een andere naam invoeren. 
4. Klik op **bron instellen**. 
5. De standaard taal is audio_eng. Probeer te scha kelen tussen audio_eng en audio_es. Telkens wanneer u een nieuwe stream selecteert, moet u op de knop verzenden klikken.

U hebt Les 3 voltooid.  In deze les voegt u de functionaliteit toe om stromen te kiezen.

## <a name="lesson-4-select-smooth-streaming-tracks"></a>Les 4: Smooth Streaming-tracks selecteren

Een Smooth Streaming presentatie kan meerdere video bestanden bevatten die zijn gecodeerd met verschillende kwaliteits niveaus (bitsnelheden) en resoluties. In deze les stelt u gebruikers in staat om sporen te selecteren. Deze les bevat de volgende procedures:

1. Het XAML-bestand wijzigen
2. De code achter het bestand wijzigen
3. De toepassing compileren en testen

### <a name="to-modify-the-xaml-file"></a>Het XAML-bestand wijzigen

1. Klik vanuit Solution Explorer met de rechter muisknop op **MainPage. xaml** en klik vervolgens op **Designer weer geven**.
2. Zoek de &lt; grid &gt; -tag met de naam **gridStreamAndBitrateSelection**, voeg de volgende code toe aan het einde van de tag:
   ```xml
         <StackPanel Name="spBitRateSelection" Grid.Row="1" Grid.Column="1">
         <StackPanel Orientation="Horizontal">
             <TextBlock Name="tbBitRate" Text="Available Bitrates:" FontSize="16" VerticalAlignment="Center"/>
             <Button Name="btnChangeTracks" Content="Submit" Click="btnChangeTrack_Click" />
         </StackPanel>
         <ListBox x:Name="lbAvailableVideoTracks" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                  ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
             <ListBox.ItemTemplate>
                 <DataTemplate>
                     <CheckBox Content="{Binding Bitrate}" IsChecked="{Binding isChecked, Mode=TwoWay}"/>
                 </DataTemplate>
             </ListBox.ItemTemplate>
         </ListBox>
         </StackPanel>
   ```
3. Druk op **CTRL + S** om de wijzigingen op te slaan

### <a name="to-modify-the-code-behind-file"></a>De code achter het bestand wijzigen

1. Klik in Solution Explorer met de rechter muisknop op **MainPage. xaml** en klik vervolgens op **code weer geven**.
2. Voeg in de naam ruimte SSPlayer een nieuwe klasse toe:
   ```csharp
        #region class Track
        public class Track
        {
            private IManifestTrack trackInfo;
            public string _bitrate;
            public bool isCheckedValue;
   
            public IManifestTrack TrackInfo
            {
                get { return trackInfo; }
                set { trackInfo = value; }
            }
   
            public string Bitrate
            {
                get { return _bitrate; }
                set { _bitrate = value; }
            }
   
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    isCheckedValue = value;
                }
            }
   
            public Track(IManifestTrack trackInfoIn)
            {
                trackInfo = trackInfoIn;
                _bitrate = trackInfoIn.Bitrate.ToString();
            }
            //public Track() { }
        }
        #endregion class Track
   ```
3. Voeg aan het begin van de klasse MainPage de volgende variabele definities toe:
   ```csharp
        private List<Track> availableTracks;
   ```
4. Voeg in de klasse MainPage de volgende regio toe:
   ```csharp
        #region track selection
        /// <summary>
        /// Functionality to select video streams
        /// </summary>
   
        /// This Function gets the tracks for the selected video stream
        public void getTracks(Manifest manifestObject)
        {
            availableTracks = new List<Track>();
   
            IManifestStream videoStream = getVideoStream();
            IReadOnlyList<IManifestTrack> availableTracksLocal = videoStream.AvailableTracks;
            IReadOnlyList<IManifestTrack> selectedTracksLocal = videoStream.SelectedTracks;
   
            try
            {
                for (int i = 0; i < availableTracksLocal.Count; i++)
                {
                    Track thisTrack = new Track(availableTracksLocal[i]);
                    thisTrack.isChecked = true;
   
                    for (int j = 0; j < selectedTracksLocal.Count; j++)
                    {
                        string selectedTrackName = selectedTracksLocal[j].Bitrate.ToString();
                        if (selectedTrackName.Equals(thisTrack.Bitrate))
                        {
                            thisTrack.isChecked = true;
                            break;
                        }
                    }
                    availableTracks.Add(thisTrack);
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = e.Message;
            }
        }
   
        // This function gets the video stream that is playing
        private IManifestStream getVideoStream()
        {
            IManifestStream videoStream = null;
            for (int i = 0; i < manifestObject.SelectedStreams.Count; i++)
            {
                if (manifestObject.SelectedStreams[i].Type == MediaStreamType.Video)
                {
                    videoStream = manifestObject.SelectedStreams[i];
                    break;
                }
            }
            return videoStream;
        }
   
        // This function set the UI list box control ItemSource
        private async void refreshAvailableTracksListBoxItemSource()
        {
            try
            {
                // Update the track check box list on the UI 
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableVideoTracks.ItemsSource = availableTracks; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }        
        }
   
        // This function creates a list of the selected tracks.
        private void createSelectedTracksList(List<IManifestTrack> selectedTracks)
        {
            // Create a list of selected tracks
            for (int j = 0; j < availableTracks.Count; j++)
            {
                if (availableTracks[j].isCheckedValue == true)
                {
                    selectedTracks.Add(availableTracks[j].TrackInfo);
                }
            }
        }
   
        // This function selects the tracks based on user selection 
        private void changeTracks(List<IManifestTrack> selectedTracks)
        {
            IManifestStream videoStream = getVideoStream();
            try
            {
                videoStream.SelectTracks(selectedTracks);
            }
            catch (Exception ex)
            {
                txtStatus.Text = ex.Message;
            }
        }
        #endregion track selection
   ```
5. Ga naar de mediaElement_ManifestReady methode en voeg de volgende code toe aan het einde van de functie:
   ```csharp
         getTracks(manifestObject);
         refreshAvailableTracksListBoxItemSource();
   ```
6. Zoek in de klasse MainPage de gebruikers interface knoppen op gebeurtenissen regio en voeg de volgende functie definitie toe:
   ```csharp
         private void btnChangeStream_Click(object sender, RoutedEventArgs e)
         {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
         }
   ```
   
### <a name="to-compile-and-test-the-application"></a>De toepassing compileren en testen

1. Druk op **F6** om het project te compileren. 
2. Druk op **F5** om de toepassing uit te voeren.
3. Boven aan de toepassing kunt u de standaard-Smooth Streaming URL gebruiken of een andere naam invoeren. 
4. Klik op **bron instellen**. 
5. Standaard zijn alle sporen van de video stroom geselecteerd. Als u de wijzigingen in de bitsnelheid wilt experimenteren, kunt u de laagste bitsnelheid selecteren die beschikbaar is en selecteert u vervolgens de hoogste bitsnelheid beschikbaar. U moet na elke wijziging op verzenden klikken.  U kunt de wijzigingen in de video kwaliteit bekijken.

U hebt Les 4 voltooid.  In deze les voegt u de functionaliteit toe om sporen te kiezen.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="other-resources"></a>Andere bronnen:
* [Een Smooth Streaming Windows 8 java script-toepassing bouwen met geavanceerde functies](https://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
* [Technisch overzicht van Smooth Streaming](https://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

[PlayerApplication]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png
[CodeViewPic]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png
