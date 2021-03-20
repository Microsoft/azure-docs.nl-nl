---
title: .NET-toepassing met meerdere lagen die Azure Service Bus gebruikt | Microsoft Docs
description: Een .NET-zelfstudie waarmee u in Azure een app met meerdere lagen kunt ontwikkelen die Service Bus-wachtrijen gebruikt voor communicatie tussen lagen.
ms.devlang: dotnet
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 40529df5195a29fbf2ff4887311932c2ffbf471d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96029892"
---
# <a name="net-multi-tier-application-using-azure-service-bus-queues"></a>.NET-toepassing met meerdere lagen die Azure Service Bus-wachtrijen gebruikt

Ontwikkelen voor Microsoft Azure is eenvoudig met Visual Studio en de gratis Azure SDK voor .NET. In deze zelfstudie doorloopt u de stappen voor het maken van een toepassing die meerdere Azure-resources in uw lokale omgeving gebruikt.

U leert het volgende:

* De computer met een enkele download en installatie instellen voor het ontwikkelen voor Azure.
* Visual Studio gebruiken om te ontwikkelen voor Azure.
* Een toepassing met meerdere lagen maken in Azure met de web- en werkrollen.
* Communiceren tussen lagen met Service Bus-wachtrijen.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

In deze zelfstudie zult u de toepassing met meerdere lagen ontwikkelen en uitvoeren in een cloudservice van Azure. De front-end is een ASP.NET MVC-webrol en de back-end is een werkrol die gebruikmaakt van een Service Bus-wachtrij. U kunt dezelfde toepassing met meerdere lagen maken met de front-end als een webproject dat wordt geïmplementeerd op een Azure-website in plaats van een cloudservice. U kunt ook de zelfstudie [.NET on-premises/hybride cloud-toepassing](../azure-relay/service-bus-dotnet-hybrid-app-using-service-bus-relay.md) gebruiken.

In de volgende scherm afbeelding wordt de voltooide toepassing weer gegeven.

![Scherm afbeelding van de verzend pagina van de toepassing.][0]

## <a name="scenario-overview-inter-role-communication"></a>Scenario-overzicht: communicatie tussen rollen
Als u een order wilt indienen voor verwerking, moet het front-end UI-onderdeel, dat wordt uitgevoerd in de webrol, communiceren met de logica van de middelste laag, die wordt uitgevoerd in de werkrol. In dit voorbeeld wordt Service Bus Messaging gebruikt voor communicatie tussen de lagen.

Met behulp van Service Bus Messaging tussen de weblaag en de middelste laag worden de twee onderdelen losgekoppeld. In tegenstelling tot Direct Messaging (dat wil zeggen, TCP of HTTP) hoeft de weblaag niet rechtstreeks verbinding te maken met de middelste laag. In plaats daarvan worden werkeenheden, als berichten, naar Service Bus gepusht. Daar worden ze veilig bewaard totdat de middelste laag gereed is om ze te ontvangen en te verwerken.

Service Bus biedt twee entiteiten ter ondersteuning van Brokered Messaging: wachtrijen en onderwerpen. Met wachtrijen wordt elk bericht dat naar de wachtrij wordt verzonden, verbruikt door een enkele ontvanger. Onderwerpen ondersteunen het patroon voor publiceren/abonneren waarin elk gepubliceerde bericht beschikbaar wordt gesteld aan een abonnement dat bij het onderwerp is geregistreerd. Elk abonnement onderhoudt logisch gezien zijn eigen wachtrij met berichten. Abonnementen kunnen ook worden geconfigureerd met filterregels die de set berichten die wordt doorgegeven aan de abonnementenwachtrij beperken tot berichten die overeenkomen met het filter. In het volgende voorbeeld wordt gebruikgemaakt van Service Bus-wachtrijen.

![Diagram van de communicatie tussen de webrole, het Service Bus en de rol van de werk nemer.][1]

Dit communicatiemechanisme heeft verschillende voordelen ten opzichte van Direct Messaging:

* **Tijdelijke ontkoppeling.** Met het asynchrone berichtenpatroon hoeven producenten en consumenten niet op hetzelfde moment online te zijn. Service Bus slaat de berichten veilig op totdat de verbruikende partij gereed is om de berichten te ontvangen. Hierdoor kunnen de onderdelen van de gedistribueerde toepassing worden losgekoppeld - hetzij vrijwillig, bijvoorbeeld voor onderhoud, hetzij vanwege het vastlopen van een onderdeel - zonder dat dit van invloed is op het systeem als geheel. Bovendien hoeft de betreffende toepassing mogelijk alleen online te komen op bepaalde tijdstippen gedurende de dag.
* **Herverdeling van taken.** In veel toepassingen varieert de systeembelasting gedurende de tijd, terwijl de benodigde verwerkingstijd voor elke werkeenheid doorgaans constant blijft. Door een wachtrij tussen producenten en consumenten van berichten te plaatsen, hoeft de verbruikende toepassing (de werkrol) alleen te worden ingericht voor het opvangen van een gemiddelde belasting in plaats van een piekbelasting. De lengte van de wachtrij neemt toe of af, al naargelang het binnenkomende verkeer. Dit betekent een rechtstreekse besparing op de kosten voor de benodigde infrastructuur om de toepassingsbelasting te verwerken.
* **Taak verdeling.** Naarmate het verkeer toeneemt, kunnen meer werkprocessen worden toegevoegd om uit de wachtrij te lezen. Elk bericht worden door slechts één van de werkprocessen verwerkt. Bovendien biedt deze pull-gebaseerde taakverdeling optimaal gebruik van de werkmachines, zelfs als de verwerkingskracht van de werkmachines verschilt en ze op eigen maximale snelheid berichten ophalen. Dit patroon wordt vaak aangeduid als het *concurrerend consumenten*-patroon.
  
  ![Diagram waarin de communicatie tussen de webfunctie, de Service Bus en twee werk rollen wordt weer gegeven.][2]

In de volgende gedeelten wordt de code besproken waarmee deze architectuur wordt geïmplementeerd.

## <a name="create-a-namespace"></a>Een naamruimte maken

De eerste stap is het maken van een *naam ruimte* en het verkrijgen van een [Shared Access Signature SAS-](service-bus-sas.md) sleutel voor die naam ruimte. Een naamruimte biedt een toepassingsbegrenzing voor elke toepassing die toegankelijk is via Service Bus. Er wordt automatisch een SAS-sleutel gegenereerd wanneer er een naamruimte wordt gemaakt. De combinatie van naamruimte en SAS-sleutel biedt Service Bus de benodigde referenties voor het verifiëren van toegang tot een toepassing.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-web-role"></a>Een webrol maken

In dit gedeelte maakt u de front-end van uw toepassing. U maakt eerst de pagina's die door uw toepassing worden weergegeven.
Vervolgens voegt u code toe waarmee items worden verzonden naar een Service Bus-wachtrij en waarmee statusinformatie over de wachtrij wordt weergegeven.

### <a name="create-the-project"></a>Het project maken

1. Visual Studio starten met administratorbevoegdheden: klik met de rechtermuisknop op het programmapictogram **Visual Studio** en klik vervolgens op **Als administrator uitvoeren**. De Azure Compute-emulator, die verderop in dit artikel wordt besproken, vereist dat Visual Studio met beheerders bevoegdheden wordt gestart.
   
   Klik in het menu **Bestand** van Visual Studio op **Nieuw** en klik vervolgens op **Project**.
2. Klik vanuit **Geïnstalleerde sjablonen** onder **Visual C#** op **Cloud** en klik vervolgens op **Azure Cloud Service**. Geef het project de naam **MultiTierApp**. Klik vervolgens op **OK**.
   
   ![Scherm afbeelding van het dialoog venster Nieuw project met geselecteerde Cloud en Azure Cloud service Visual C# gemarkeerd en in het rood beschreven.][9]
3. Dubbelklik in het deelvenster **Rollen** op **ASP.NET-webrol**.
   
   ![Scherm afbeelding van het dialoog venster nieuwe Microsoft Azure Cloud service met geselecteerde ASP.NET en WebRole1 ook geselecteerd.][10]
4. Beweeg de muisaanwijzer over **WebRole1** onder **Azure Cloud Service-oplossing**, klik op het potloodpictogram en wijzig de naam van de webrol in **FrontendWebRole**. Klik vervolgens op **OK**. (Zorg ervoor dat u 'Frontend' invoert met een kleine letter 'e', dus niet 'FrontEnd'.)
   
   ![Scherm afbeelding van het dialoog venster nieuwe Microsoft Azure Cloud service met de naam van de oplossing die is gewijzigd in FrontendWebRole.][11]
5. Klik in het dialoogvenster **Nieuw ASP.NET-project** in de lijst **Een sjabloon selecteren** op **MVC**.
   
   ![Screenshotof het nieuwe dialoog venster ASP.NET-project met MVC gemarkeerd en in rood en de optie voor het wijzigen van de verificatie rood.][12]
6. Klik nog steeds vanuit het dialoogvenster **Nieuw ASP.NET-project** op de knop **Verificatie wijzigen**. Zorg dat in het dialoogvenster **Verificatie wijzigen** de optie **Geen verificatie** is geselecteerd en klik vervolgens op **OK**. In deze zelfstudie implementeert u een app waarvoor geen gebruikersaanmelding nodig is.
   
    ![Scherm afbeelding van het dialoog venster verificatie wijzigen met de optie geen verificatie geselecteerd en in rood beschreven.][16]
7. Ga terug naar het dialoogvenster **Nieuw ASP.NET-project** en klik op **OK** om het project te maken.
8. Klik in **Solution Explorer** in het project **FrontendWebRole** met de rechtermuisknop op **Verwijzingen** en klik vervolgens op **NuGet-pakketten beheren**.
9. Klik op het tabblad **Bladeren** en zoek vervolgens naar **WindowsAzure.ServiceBus**. Selecteer het **WindowsAzure.ServiceBus**-pakket, klik op **Installeren** en accepteer de gebruiksvoorwaarden.
   
   ![Scherm afbeelding van het dialoog venster NuGet-pakketten beheren met de WindowsAzure. ServiceBus gemarkeerd en de installatie optie in een rood kader.][13]
   
   Na de installatie wordt verwezen naar de vereiste clientassembly's en zijn enkele nieuwe codebestanden toegevoegd.
10. Klik in **Solution Explorer** met de rechtermuisknop op **Modellen** en klik achtereenvolgens op **Toevoegen** en **Klasse**. Typ in het vak **Naam** de naam **OnlineOrder.cs**. Klik vervolgens op **Toevoegen**.

### <a name="write-the-code-for-your-web-role"></a>De code voor de webrol schrijven
In dit gedeelte maakt u de verschillende pagina's die door uw toepassing worden weergegeven.

1. In het bestand OnlineOrder.cs in Visual Studio vervangt u de bestaande naamruimtedefinitie door de volgende code:
   
   ```csharp
   namespace FrontendWebRole.Models
   {
       public class OnlineOrder
       {
           public string Customer { get; set; }
           public string Product { get; set; }
       }
   }
   ```
2. Dubbelklik in **Solution Explorer** op **Controllers\HomeController.cs**. Voeg de volgende **using**-instructies aan het begin van het bestand toe om de naamruimten op te nemen voor het model dat u zojuist hebt gemaakt, maar ook voor Service Bus.
   
   ```csharp
   using FrontendWebRole.Models;
   using Microsoft.ServiceBus.Messaging;
   using Microsoft.ServiceBus;
   ```
3. In het bestand HomeController.cs in Visual Studio vervangt u bovendien de bestaande naamruimtedefinitie door de volgende code. Deze code bevat methoden voor het afhandelen van de verzending van items naar de wachtrij.
   
   ```csharp
   namespace FrontendWebRole.Controllers
   {
       public class HomeController : Controller
       {
           public ActionResult Index()
           {
               // Simply redirect to Submit, since Submit will serve as the
               // front page of this application.
               return RedirectToAction("Submit");
           }
   
           public ActionResult About()
           {
               return View();
           }
   
           // GET: /Home/Submit.
           // Controller method for a view you will create for the submission
           // form.
           public ActionResult Submit()
           {
               // Will put code for displaying queue message count here.
   
               return View();
           }
   
           // POST: /Home/Submit.
           // Controller method for handling submissions from the submission
           // form.
           [HttpPost]
           // Attribute to help prevent cross-site scripting attacks and
           // cross-site request forgery.  
           [ValidateAntiForgeryToken]
           public ActionResult Submit(OnlineOrder order)
           {
               if (ModelState.IsValid)
               {
                   // Will put code for submitting to queue here.
   
                   return RedirectToAction("Submit");
               }
               else
               {
                   return View(order);
               }
           }
       }
   }
   ```
4. Klik in het menu **Bouwen** op **Oplossing opbouwen** om de juistheid van uw werk tot nu toe te controleren.
5. Maak nu de weergave voor de methode voor `Submit()` die u eerder hebt gemaakt. Klik met de rechtermuisknop in de methode voor `Submit()` (de overbelasting van `Submit()` waarvoor geen parameters zijn vereist) en kies vervolgens **Weergave toevoegen**.
   
   ![Scherm afbeelding van de code met de focus op de methode Submit en een vervolg keuzelijst met de optie weer gave toevoegen gemarkeerd.][14]
6. Een dialoogvenster voor het maken van de weergave wordt weergegeven. Kies **Maken** in de lijst **Sjabloon**. Selecteer in de lijst **Modelklasse** de klasse **OnlineOrder**.
   
   ![Een scherm afbeelding van het dialoog venster weer gave toevoegen met de vervolg keuzelijsten sjabloon en model klasse, die in het rood worden beschreven.][15]
7. Klik op **Add**.
8. Wijzig nu de weergegeven naam van uw toepassing. Dubbelklik in **Solution Explorer** op het bestand **Views\Shared\\_Layout.cshtml** om dit te openen in de Visual Studio-editor.
9. Vervang alle instanties van **Mijn ASP.NET-toepassing** door **Producten van Northwind Traders**.
10. Verwijder de koppelingen **Start**, **Info** en **Contact**. Verwijder de gemarkeerde code:
    
    ![Scherm afbeelding van de code met drie regels van de actie koppelings code voor H T/M L gemarkeerd.][28]
11. Wijzig tot slot de verzendpagina om enige informatie over de wachtrij op te nemen. Dubbelklik in **Solution Explorer** op het bestand **Views\Home\Submit.cshtml** om dit te openen in de Visual Studio-editor. Voeg de volgende regel toe na `<h2>Submit</h2>`. Op dit moment is `ViewBag.MessageCount` leeg. U vult dit later in.
    
    ```html
    <p>Current number of orders in queue waiting to be processed: @ViewBag.MessageCount</p>
    ```
12. U hebt nu de gebruikersinterface geïmplementeerd. Druk op **F5** om uw toepassing uit te voeren en te controleren of deze voldoet aan uw verwachting.
    
    ![Scherm afbeelding van de verzend pagina van de toepassing.][17]

### <a name="write-the-code-for-submitting-items-to-a-service-bus-queue"></a>De code schrijven voor het indienen van items aan een Service Bus-wachtrij
Voeg nu code toe voor het indienen van items aan een wachtrij. U maakt eerst een klasse die de verbindingsgegevens van de Service Bus-wachtrij bevat. Vervolgens initialiseert u de verbinding vanuit Global.aspx.cs. Tot slot werkt u de verzendcode bij die u eerder hebt gemaakt in HomeController.cs, zodat items daadwerkelijk naar een Service Bus-wachtrij worden verzonden.

1. Klik in **Solution Explorer** met de rechtermuisknop op **FrontendWebRole** (klik met de rechtermuisknop op het project, niet op de rol). Klik op **Toevoegen** en klik vervolgens op **Klasse**.
2. Geef de klasse de naam **QueueConnector.cs**. Klik op **Toevoegen** om de klasse te maken.
3. Voeg nu code toe waarin de verbindingsgegevens zijn opgenomen en waarmee de verbinding met een Service Bus-wachtrij wordt geïnitialiseerd. Vervang de volledige inhoud van QueueConnector.cs door de volgende code en voer waarden in voor `your Service Bus namespace` (de naam van uw naamruimte) en `yourKey`. Dit is de **primaire sleutel** die u eerder van Azure Portal hebt gekregen.
   
   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Linq;
   using System.Web;
   using Microsoft.ServiceBus.Messaging;
   using Microsoft.ServiceBus;
   
   namespace FrontendWebRole
   {
       public static class QueueConnector
       {
           // Thread-safe. Recommended that you cache rather than recreating it
           // on every request.
           public static QueueClient OrdersQueueClient;
   
           // Obtain these values from the portal.
           public const string Namespace = "your Service Bus namespace";
   
           // The name of your queue.
           public const string QueueName = "OrdersQueue";
   
           public static NamespaceManager CreateNamespaceManager()
           {
               // Create the namespace manager which gives you access to
               // management operations.
               var uri = ServiceBusEnvironment.CreateServiceUri(
                   "sb", Namespace, String.Empty);
               var tP = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                   "RootManageSharedAccessKey", "yourKey");
               return new NamespaceManager(uri, tP);
           }
   
           public static void Initialize()
           {
               // Using Http to be friendly with outbound firewalls.
               ServiceBusEnvironment.SystemConnectivity.Mode =
                   ConnectivityMode.Http;
   
               // Create the namespace manager which gives you access to
               // management operations.
               var namespaceManager = CreateNamespaceManager();
   
               // Create the queue if it does not exist already.
               if (!namespaceManager.QueueExists(QueueName))
               {
                   namespaceManager.CreateQueue(QueueName);
               }
   
               // Get a client to the queue.
               var messagingFactory = MessagingFactory.Create(
                   namespaceManager.Address,
                   namespaceManager.Settings.TokenProvider);
               OrdersQueueClient = messagingFactory.CreateQueueClient(
                   "OrdersQueue");
           }
       }
   }
   ```
4. Controleer nu of uw methode voor **Initialiseren** wordt aangeroepen. Dubbelklik in **Solution Explorer** op **Global.asax\Global.asax.cs**.
5. Voeg de volgende coderegel toe aan het einde van de methode **Application_Start**.
   
   ```csharp
   FrontendWebRole.QueueConnector.Initialize();
   ```
6. Werk tot slot de webcode die u eerder hebt gemaakt, bij om items naar de wachtrij te verzenden. Dubbelklik in **Solution Explorer** op **Controllers\HomeController.cs**.
7. Werk de methode `Submit()` (de overbelasting waarvoor geen parameters zijn vereist) als volgt bij om het aantal berichten voor de wachtrij te verkrijgen.
   
   ```csharp
   public ActionResult Submit()
   {
       // Get a NamespaceManager which allows you to perform management and
       // diagnostic operations on your Service Bus queues.
       var namespaceManager = QueueConnector.CreateNamespaceManager();
   
       // Get the queue, and obtain the message count.
       var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
       ViewBag.MessageCount = queue.MessageCount;
   
       return View();
   }
   ```
8. Werk de methode `Submit(OnlineOrder order)` (de overbelasting waarvoor één parameter moet worden gebruikt) als volgt bij om ordergegevens naar de wachtrij te verzenden.
   
   ```csharp
   public ActionResult Submit(OnlineOrder order)
   {
       if (ModelState.IsValid)
       {
           // Create a message from the order.
           var message = new BrokeredMessage(order);
   
           // Submit the order.
           QueueConnector.OrdersQueueClient.Send(message);
           return RedirectToAction("Submit");
       }
       else
       {
           return View(order); 
       }
   }
   ```
9. U kunt nu de toepassing opnieuw uitvoeren. Het aantal berichten neemt toe elke keer wanneer u een order verzendt.
   
   ![Scherm afbeelding van de verzend pagina van de toepassing met het aantal berichten dat wordt verhoogd naar 1.][18]

## <a name="create-the-worker-role"></a>De werkrol maken
U maakt nu de werkrol die de orderverzendingen verwerkt. In dit voorbeeld wordt de Visual Studio-projectsjabloon **Werkrol met Service Bus-wachtrij** gebruikt. U hebt al de vereiste referenties ontvangen van de portal.

1. Zorg ervoor dat u Visual Studio aan uw Azure-account hebt gekoppeld.
2. Klik in Visual Studio in **Solution Explorer** met de rechtermuisknop op de map **Rollen** onder het **MultiTierApp**-project.
3. Klik op **Toevoegen** en klik vervolgens op **Nieuw werkrolproject**. Het dialoogvenster **Nieuw rolproject toevoegen** wordt weergegeven.
   
   ![Scherm afbeelding van het deel venster Blade Explorer met de optie Nieuw werk rollen project en de optie toevoegen gemarkeerd.][26]
4. Klik in het dialoogvenster **Nieuw rolproject toevoegen** op **Werkrol met Service Bus-wachtrij**.
   
   ![Scherm afbeelding van het dialoog venster AD New Role project met de rol worker met Service Bus wachtrij selectie gemarkeerd en in rood beschreven.][23]
5. Voer in het vak **Naam** de naam **OrderProcessingRole** voor het project in. Klik vervolgens op **Toevoegen**.
6. Kopieer de verbindingsreeks die u hebt verkregen in stap 9 van het gedeelte 'Een Service Bus-naamruimte maken' naar het klembord.
7. Klik in **Solution Explorer** met de rechtermuisknop op de **OrderProcessingRole** die u in stap 5 hebt gemaakt (klik met de rechtermuisknop op **OrderProcessingRole** onder **Rollen** en niet onder de klasse). Klik vervolgens op **Eigenschappen**.
8. Klik op het tabblad **Instellingen** van het dialoogvenster **Eigenschappen** in het vak **Waarde** voor **Microsoft.ServiceBus.ConnectionString**. Plak vervolgens de eindpuntwaarde die u in stap 6 hebt gekopieerd.
   
   ![Scherm afbeelding van het dialoog venster Eigenschappen met het tabblad instellingen geselecteerd en de rij micro soft. ServiceBus. Connections Tring in een rood kader.][25]
9. Maak een klasse **OnlineOrder** die de orders vertegenwoordigt tijdens het verwerken van de wachtrij. U kunt een klasse die u al hebt gemaakt opnieuw gebruiken. Klik in **Solution Explorer** met de rechtermuisknop op de klasse **OrderProcessingRole** (klik met de rechtermuisknop op het klassepictogram en niet op de rol). Klik op **Toevoegen** en vervolgens op **Bestaand item**.
10. Blader naar de submap voor **FrontendWebRole\Models** en dubbelklik vervolgens op **OnlineOrder.cs** om het bestand toe te voegen aan dit project.
11. Wijzig in **WorkerRole.cs** de waarde van de variabele **QueueName** van `"ProcessingQueue"` in `"OrdersQueue"`, zoals weergegeven in de volgende code.
    
    ```csharp
    // The name of your queue.
    const string QueueName = "OrdersQueue";
    ```
12. Voeg de volgende instructie toe aan het begin van het bestand WorkerRole.cs.
    
    ```csharp
    using FrontendWebRole.Models;
    ```
13. In de functie `Run()` binnen de aanroep `OnMessage()` vervangt u de inhoud van de `try`-component door de volgende code.
    
    ```csharp
    Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
    // View the message as an OnlineOrder.
    OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
    Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
    receivedMessage.Complete();
    ```
14. U hebt de toepassing nu voltooid. U kunt de volledige toepassing testen door met de rechtermuisknop te klikken op het MultiTierApp-project in Solution Explorer, **Instellen als opstartproject** te selecteren en vervolgens op F5 te drukken. Houd er rekening mee dat het aantal berichten niet toeneemt, omdat de werkrol items uit de wachtrij verwerkt en als voltooid markeert. U ziet de trace-uitvoer van uw werkrol door de gebruikersinterface van de Azure-rekenemulator weer te geven. Klik hiervoor met de rechtermuisknop op het emulatorpictogram in het systeemvak van de taakbalk en selecteer **Gebruikersinterface van de rekenemulator weergeven**.
    
    ![Scherm opname van wat er wordt weer gegeven wanneer u op het Emulator pictogram klikt. Compute emulator weer geven gebruikers interface bevindt zich in de lijst met opties.][19]
    
    ![Scherm afbeelding van het dialoog venster Microsoft Azure Compute emulator (Express).][20]

## <a name="next-steps"></a>Volgende stappen
Zie de volgende resources voor meer informatie over Service Bus:  

* [Aan de slag met Service Bus-wachtrijen][sbacomqhowto]
* [Service Bus-servicepagina][sbacom]  

Zie voor meer informatie over scenario's voor meerdere lagen:  

* [.NET-toepassing met meerdere lagen met Table Storage, Queue Storage en Blob Storage][mutitierstorage]  

[0]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-app.png
[1]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
[2]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
[9]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.png
[10]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
[11]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
[12]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
[13]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
[14]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
[15]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
[16]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-14.png
[17]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-app.png
[18]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-app2.png

[19]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
[20]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
[23]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
[25]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
[26]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
[28]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png

[sbacom]: https://azure.microsoft.com/services/service-bus/  
[sbacomqhowto]: service-bus-dotnet-get-started-with-queues.md  
[mutitierstorage]: https://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
