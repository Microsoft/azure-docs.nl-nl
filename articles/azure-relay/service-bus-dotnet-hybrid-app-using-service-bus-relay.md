---
title: Azure Windows Communication Foundation (WCF) Relay hybride on-premises/Cloud toepassing (.NET) | Microsoft Docs
description: Ontdek hoe u een on-premises WCF-service zichtbaar maakt voor een webtoepassing in de cloud met behulp van Azure Relay
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 06/23/2020
ms.openlocfilehash: 22b582634b623b39545eca225c8df0130606c2bb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90983891"
---
# <a name="expose-an-on-premises-wcf-service-to-a-web-application-in-the-cloud-by-using-azure-relay"></a>Een on-premises WCF-service zichtbaar maken voor een webtoepassing in de cloud met behulp van Azure Relay

In dit artikel wordt beschreven hoe u een hybride cloudtoepassing opbouwt met Microsoft Azure en Visual Studio. U maakt een toepassing die gebruikmaakt van meerdere Azure-resources in de Cloud. In deze zelf studie leert u het volgende:

* Een bestaande webservice voor verbruik door een weboplossing maken of aanpassen.
* De Azure Windows Communication Foundation (WCF) Relay-service gebruiken voor het delen van gegevens tussen een Azure-toepassing en een webservice die ergens anders wordt gehost.

In deze zelfstudie gaat u de volgende taken uitvoeren:

> [!div class="checklist"]
>
> * Voldoen aan de vereisten voor deze zelfstudie.
> * Het scenario lezen.
> * Een naamruimte maken.
> * Een on-premises server maken.
> * Een ASP .NET-toepassing maken.
> * Voer de app lokaal uit.
> * De web-app implementeren in Azure.
> * Voer de app uit op Azure.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie moet aan de volgende vereisten worden voldaan:

* Een Azure-abonnement. Als u nog geen abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.
* [Visual Studio 2015 of hoger](https://www.visualstudio.com). In de voorbeelden in deze zelfstudie wordt Visual Studio 2019 gebruikt.
* Azure-SDK voor .NET. Installeer de SDK via de [SDK-downloadpagina](https://azure.microsoft.com/downloads/).

## <a name="how-azure-relay-helps-with-hybrid-solutions"></a>Hoe Azure hulp biedt bij hybride oplossingen

Bedrijfs oplossingen bestaan doorgaans uit een combi natie van aangepaste code en bestaande functionaliteit. Aangepaste code voldoet aan nieuwe en unieke zakelijke vereisten. Oplossingen en systemen die al aanwezig zijn, bieden bestaande functionaliteit.

Oplossingsarchitecten gaan nu vaak over op de cloud om gemakkelijk aan schaalvereisten te voldoen en operationele kosten te verlagen. Op die manier kunnen ze zien dat bestaande service assets die ze graag willen gebruiken als bouw stenen voor hun oplossingen zich in de bedrijfs firewall bevinden en niet gemakkelijk bereikbaar zijn door de Cloud oplossing. Veel interne services zijn niet gebouwd of worden gehost op een manier die eenvoudig kan worden blootgesteld aan de rand van het bedrijfs netwerk.

[Azure relay](https://azure.microsoft.com/services/service-bus/) maakt gebruik van bestaande WCF-webservices en maakt deze services veilig toegankelijk voor oplossingen die zich buiten het bedrijfs netwerk bevinden, zonder dat er onopvallende wijzigingen in de infra structuur van de bedrijfs netwerken zijn vereist. Dergelijke relayservices worden nog steeds gehost binnen hun bestaande omgeving, maar ze dragen het luisteren naar binnenkomende sessies en aanvragen over aan de relayservice in de cloud. Azure Relay beschermt ook deze services tegen onbevoegde toegang met behulp van [Shared Access Signature-verificatie (SAS)](../service-bus-messaging/service-bus-sas.md) .

## <a name="review-the-scenario"></a>Het scenario lezen

In deze zelfstudie maakt u een ASP.NET-website waarmee u een lijst met producten op de pagina met de productinventaris kunt bekijken.

![Scenario][0]

In de zelfstudie wordt ervan uitgegaan dat u productgegevens in een bestaand on-premises systeem hebt en wordt Azure Relay gebruikt om dat systeem te bereiken. Deze situatie wordt gesimuleerd door een webservice die wordt uitgevoerd in een eenvoudige console toepassing. Het bevat een set in het geheugen van producten. U kunt deze console toepassing op uw eigen computer uitvoeren en de webfunctie in azure implementeren. Als u dit doet, ziet u hoe de webfunctie die in het Azure Data Center wordt uitgevoerd, op uw computer aanroept. Deze aanroep gebeurt zelfs als uw computer bijna altijd achter ten minste één firewall en een Network Address Translation-laag (NAT) valt.

## <a name="set-up-the-development-environment"></a>De ontwikkelomgeving instellen

Voordat u Azure-toepassingen kunt ontwikkelen, dient u de hulpprogramma's te downloaden en uw ontwikkelomgeving in te stellen:

1. Installeer de Azure-SDK voor .NET via de [pagina met downloads](https://azure.microsoft.com/downloads/) voor SDK.
1. Kies in de kolom **.net** de versie van [Visual Studio](https://www.visualstudio.com) die u gebruikt. In deze zelf studie wordt Visual Studio 2019 gebruikt.
1. Als u wordt gevraagd of u het installatie programma wilt uitvoeren of opslaan, selecteert u **uitvoeren**.
1. In het dialoog venster **Web platform Installer** selecteert u **installeren** en gaat u door met de installatie.

Nadat de installatie is voltooid, hebt u alles wat u nodig hebt om te beginnen met het ontwikkelen van de app. De SDK bevat hulpprogramma's waarmee u eenvoudig Azure-toepassingen kunt ontwikkelen in Visual Studio.

## <a name="create-a-namespace"></a>Een naamruimte maken

De eerste stap is het maken van een naamruimte en ophalen van een SAS-sleutel ([Shared Access Signature](../service-bus-messaging/service-bus-sas.md)). Een naamruimte biedt een toepassingsbegrenzing voor elke toepassing die toegankelijk is via de relayservice. Een SAS-sleutel wordt automatisch door het systeem gegenereerd wanneer een servicenaamruimte wordt gemaakt. De combinatie van servicenaamruimte en SAS-sleutel biedt Service Bus de benodigde referenties voor het verifiëren van toegang tot een toepassing.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>Een on-premises server maken

U bouwt eerst een gesimuleerd on-premises systeem voor de productcatalogus op.  Dit project is een Visual Studio-consoletoepassing en gebruikt het [Azure Service Bus NuGet-pakket](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) om de Service Bus-bibliotheken en configuratie-instellingen op te nemen. <a name="create-the-project"></a>

1. Start Microsoft Visual Studio als beheerder. Klik hiervoor met de rechtermuisknop op het pictogram van het Visual Studio-programma en selecteer vervolgens **Als administrator uitvoeren**.
1. Selecteer **Een nieuw project maken** in Visual Studio.
1. Selecteer in **een nieuw project maken de** optie **console-app (.NET Framework)** voor C# en selecteer **volgende**.
1. Geef het project de naam *ProductsServer* en selecteer **maken**.

   ![Uw nieuwe project configureren][11]

1. Klik in **Solution Explorer** met de rechter muisknop op het project **ProductsServer** en selecteer vervolgens **NuGet-pakketten beheren**.
1. Selecteer **Bladeren**, en zoek en kies vervolgens **WindowsAzure.ServiceBus**. Selecteer **Installeren** en accepteer de gebruiksvoorwaarden.

   ![NuGet-pakket selecteren][13]

   Er wordt nu naar de vereiste clientassembly's verwezen.

1. Voeg een nieuwe klasse voor uw productcontract toe. Klik in **Solution Explorer** met de rechter muisknop op het project **ProductsServer** en selecteer   >  **klasse** toevoegen.
1. Voer bij **naam** de naam *ProductsContract. cs* in en selecteer **toevoegen**.

Maak de volgende code wijzigingen in uw oplossing:

1. Vervang in *ProductsContract.cs* de naamruimtedefinitie door de volgende code waarmee het contract voor de service wordt gedefinieerd.

    ```csharp
    namespace ProductsServer
    {
        using System.Collections.Generic;
        using System.Runtime.Serialization;
        using System.ServiceModel;

        // Define the data contract for the service
        [DataContract]
        // Declare the serializable properties.
        public class ProductData
        {
            [DataMember]
            public string Id { get; set; }
            [DataMember]
            public string Name { get; set; }
            [DataMember]
            public string Quantity { get; set; }
        }

        // Define the service contract.
        [ServiceContract]
        interface IProducts
        {
            [OperationContract]
            IList<ProductData> GetProducts();

        }

        interface IProductsChannel : IProducts, IClientChannel
        {
        }
    }
    ```

1. Vervang in *Program. cs* de naam ruimte definitie door de volgende code, waarmee de profiel service en de host voor het object worden toegevoegd.

    ```csharp
    namespace ProductsServer
    {
        using System;
        using System.Linq;
        using System.Collections.Generic;
        using System.ServiceModel;

        // Implement the IProducts interface.
        class ProductsService : IProducts
        {

            // Populate array of products for display on website
            ProductData[] products =
                new []
                    {
                        new ProductData{ Id = "1", Name = "Rock",
                                         Quantity = "1"},
                        new ProductData{ Id = "2", Name = "Paper",
                                         Quantity = "3"},
                        new ProductData{ Id = "3", Name = "Scissors",
                                         Quantity = "5"},
                        new ProductData{ Id = "4", Name = "Well",
                                         Quantity = "2500"},
                    };

            // Display a message in the service console application
            // when the list of products is retrieved.
            public IList<ProductData> GetProducts()
            {
                Console.WriteLine("GetProducts called.");
                return products;
            }

        }

        class Program
        {
            // Define the Main() function in the service application.
            static void Main(string[] args)
            {
                var sh = new ServiceHost(typeof(ProductsService));
                sh.Open();

                Console.WriteLine("Press ENTER to close");
                Console.ReadLine();

                sh.Close();
            }
        }
    }
    ```

1. Dubbelklik in **Solution Explorer** op **App.config** om het bestand te openen in de Visual Studio-editor. Voeg aan de onderkant van het `<system.ServiceModel>` element, maar nog steeds binnen `<system.ServiceModel>` , de volgende XML-code toe. Zorg ervoor dat u vervangt door `yourServiceNamespace` de naam van uw naam ruimte en `yourKey` met de SAS-sleutel die u eerder hebt opgehaald via de portal:

    ```xml
    <system.serviceModel>
    ...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```

    > [!NOTE]
    > De fout die wordt veroorzaakt door `transportClientEndpointBehavior` is slechts een waarschuwing en is geen blokkerend probleem voor dit voor beeld.

1. Vervang in *App.config* nog steeds `<appSettings>` de Connection String waarde door de Connection String die u eerder hebt verkregen via de portal in het element.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

1. Selecteer CTRL + SHIFT + B **of selecteer Build**  >  **Build Solution** om de toepassing te bouwen en de nauw keurigheid van uw werk tot nu toe te controleren.

## <a name="create-an-aspnet-application"></a>Een ASP.NET-toepassing maken

In deze sectie bouwt u een eenvoudige ASP.NET-toepassing op waarmee gegevens worden weergegeven die u uit uw productservice hebt opgehaald.

### <a name="create-the-project"></a>Het project maken

1. Zorg ervoor dat Visual Studio wordt uitgevoerd als beheerder.
1. Selecteer **Een nieuw project maken** in Visual Studio.
1. Selecteer in **een nieuw project maken de** optie **ASP.net Web Application (.NET Framework)** voor C# en selecteer **volgende**.
1. Geef het project de naam *ProductsPortal* en selecteer **maken**.
1. In **een nieuwe ASP.NET-webtoepassing maken** kiest u **MVC** en selecteert u **wijzigen** onder **verificatie**.

   ![ASP.NET-webtoepassing selecteren][16]

1. Kies bij **wijzigings verificatie** de optie **geen verificatie** en selecteer vervolgens **OK**. Voor deze zelf studie implementeert u een app waarvoor geen gebruiker nodig is om zich aan te melden.

    ![Verificatie opgeven][18]

1. Selecteer in **een nieuwe ASP.NET-webtoepassing** maken de optie **maken** om de MVC-app te maken.
1. Azure-resources configureren voor een nieuwe web-app. Volg de stappen in [uw web-app publiceren](../app-service/quickstart-dotnet-framework.md#launch-the-publish-wizard). Ga vervolgens terug naar deze zelf studie en ga verder met de volgende stap.
1. Klik in **Solution Explorer** met de rechter muisknop op **modellen** en Selecteer vervolgens  >  **klasse** toevoegen.
1. Geef de klasse *product. cs* een naam en selecteer vervolgens **toevoegen**.

    ![Productmodel maken][17]

### <a name="modify-the-web-application"></a>De webtoepassing wijzigen

1. Vervang in het bestand *product. cs* in Visual Studio de bestaande naam ruimte definitie door de volgende code:

   ```csharp
    // Declare properties for the products inventory.
    namespace ProductsWeb.Models
    {
       public class Product
       {
           public string Id { get; set; }
           public string Name { get; set; }
           public string Quantity { get; set; }
       }
    }
    ```

1. Vouw in **Solution Explorer** de optie **controllers** uit en dubbel klik vervolgens op **HomeController. cs** om het bestand te openen in Visual Studio.
1. In *HomeController.cs* vervangt u de bestaande naamruimtedefinitie door de volgende code:

    ```csharp
    namespace ProductsWeb.Controllers
    {
        using System.Collections.Generic;
        using System.Web.Mvc;
        using Models;

        public class HomeController : Controller
        {
            // Return a view of the products inventory.
            public ActionResult Index(string Identifier, string ProductName)
            {
                var products = new List<Product>
                    {new Product {Id = Identifier, Name = ProductName}};
                return View(products);
            }
         }
    }
    ```

1. Vouw in **Solution Explorer** **weer gaven**  >  **gedeeld** uit en dubbel klik vervolgens op **_Layout. cshtml** om het bestand te openen in de Visual Studio-editor.
1. Wijzig alle instanties van `My ASP.NET Application` voor *producten van North Wind BV*.
1. Verwijder de `Home` `About` koppelingen, en `Contact` . Verwijder de gemarkeerde code in het volgende voorbeeld.

    ![De gegenereerde lijst met items verwijderen][41]

1. Vouw in **Solution Explorer** **weer gaven**  >  **Start pagina** uit en dubbel klik op **index. cshtml** om het bestand te openen in de Visual Studio-editor. Vervang de volledige inhoud van het bestand door de volgende code:

   ```html
   @model IEnumerable<ProductsWeb.Models.Product>

   @{
            ViewBag.Title = "Index";
   }

   <h2>Prod Inventory</h2>

   <table>
             <tr>
                 <th>
                     @Html.DisplayNameFor(model => model.Name)
                 </th>
                 <th></th>
                 <th>
                     @Html.DisplayNameFor(model => model.Quantity)
                 </th>
             </tr>

   @foreach (var item in Model) {
             <tr>
                 <td>
                     @Html.DisplayFor(modelItem => item.Name)
                 </td>
                 <td>
                     @Html.DisplayFor(modelItem => item.Quantity)
                 </td>
             </tr>
   }

   </table>
   ```

1. Als u de nauw keurigheid van uw werk tot nu toe wilt controleren, kunt u CTRL + SHIFT + B selecteren om het project te bouwen.

### <a name="run-the-app-locally"></a>De app lokaal uitvoeren

Voer de toepassing uit om te controleren of deze werkt.

1. Zorg ervoor dat **ProductsPortal** het actieve project is. Klik met de rechter muisknop op de naam van het project in **Solution Explorer** en selecteer **instellen als opstart project**.
1. Selecteer F5 in Visual Studio.

Uw toepassing moet dan in een browser worden weergegeven.

![Scherm afbeelding toont een voor beeld van de toepassing die in een browser wordt uitgevoerd en de URL is gemarkeerd.][21]

## <a name="put-the-pieces-together"></a>De softwareonderdelen samenstellen

In de volgende stap koppelt u de on-premises productenserver aan de ASP.NET-toepassing.

1. Als deze nog niet is geopend, opent u in Visual Studio het **ProductsPortal** -project dat u hebt gemaakt in de sectie [een ASP.NET-toepassing maken](#create-an-aspnet-application) .
1. Vergelijkbaar met de stap in de sectie [een on-premises server maken](#create-an-on-premises-server) , voegt u het NuGet-pakket toe aan de project verwijzingen. Klik in **Solution Explorer** met de rechter muisknop op het project **ProductsPortal** en selecteer vervolgens **NuGet-pakketten beheren**.
1. Zoek naar *WindowsAzure.ServiceBus* en selecteer het item **WindowsAzure.ServiceBus**. Voltooi de installatie en sluit dit dialoog venster.
1. Klik in **Solution Explorer** met de rechter muisknop op het project **ProductsPortal** en selecteer vervolgens   >  **bestaand item** toevoegen.
1. Ga naar het bestand *ProductsContract.cs* vanuit het **ProductsServer**-consoleproject. Markeer *ProductsContract. cs*. Selecteer de pijl-omlaag naast **toevoegen** en kies vervolgens **toevoegen als koppeling**.

   ![Toevoegen als een koppeling][24]

1. Open nu het bestand *HomeController.cs* in de Visual Studio-editor en vervang de naamruimtedefinitie door de volgende code. Zorg ervoor dat u vervangt door `yourServiceNamespace` de naam van uw service naam ruimte en `yourKey` met uw SAS-sleutel. Deze code stelt de client in staat om de on-premises service aan te roepen en het resultaat van de aanroep te retour neren.

   ```csharp
   namespace ProductsWeb.Controllers
   {
       using System.Linq;
       using System.ServiceModel;
       using System.Web.Mvc;
       using Microsoft.ServiceBus;
       using Models;
       using ProductsServer;

       public class HomeController : Controller
       {
           // Declare the channel factory.
           static ChannelFactory<IProductsChannel> channelFactory;

           static HomeController()
           {
               // Create shared access signature token credentials for authentication.
               channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                   "sb://yourServiceNamespace.servicebus.windows.net/products");
               channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                   TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                       "RootManageSharedAccessKey", "yourKey") });
           }

           public ActionResult Index()
           {
               using (IProductsChannel channel = channelFactory.CreateChannel())
               {
                   // Return a view of the products inventory.
                   return this.View(from prod in channel.GetProducts()
                                    select
                                        new Product { Id = prod.Id, Name = prod.Name,
                                            Quantity = prod.Quantity });
               }
           }
       }
   }
   ```

1. Klik in **Solution Explorer** met de rechter muisknop op de **ProductsPortal** -oplossing. Zorg ervoor dat u met de rechter muisknop op de oplossing klikt, niet op het project. Selecteer   >  **bestaand project** toevoegen.
1. Ga naar het **ProductsServer**-project en dubbelklik op het *ProductsServer.csproj*-oplossingsbestand om het toe te voegen.
1. **ProductsServer** moet worden uitgevoerd om de gegevens op **ProductsPortal** weer te geven. Klik in **Solution Explorer** met de rechter muisknop op de oplossing **ProductsPortal** en selecteer **Eigenschappen** om eigenschappen **pagina's** weer te geven.
1. Selecteer **algemene eigenschappen**  >  **opstart project** en kies **meerdere opstart projecten**. Zorg ervoor dat **ProductsServer** en **ProductsPortal** in die volg orde worden weer gegeven en dat de **actie** voor beide **starten**.

      ![Meerdere opstartprojecten][25]

1. Selecteer **algemene eigenschappen**  >  **Project afhankelijkheden** aan de linkerkant.
1. Kies voor **projecten** **ProductsPortal**. Zorg ervoor dat **ProductsServer** is geselecteerd.

    ![Projectafhankelijkheden][26]

1. Kies voor **projecten** **ProductsServer**. Zorg ervoor dat **ProductsPortal** niet is geselecteerd en selecteer **OK** om uw wijzigingen op te slaan.

## <a name="run-the-project-locally"></a>Het project lokaal uitvoeren

Als u de toepassing lokaal wilt testen, selecteert u F5 in Visual Studio. De on-premises server, **ProductsServer**, moet eerst worden gestart. vervolgens moet de **ProductsPortal** -toepassing worden gestart in een browser venster. De productinventaris bevat nu de gegevens die zijn opgehaald uit het on-premises systeem van de productservice.

![Webtoepassing][10]

Selecteer **vernieuwen** op de pagina **ProductsPortal** . Elke keer dat u de pagina vernieuwt, wordt met de server-app een bericht weergegeven wanneer `GetProducts()` vanuit **ProductsServer** wordt aangeroepen.

Sluit beide toepassingen voordat u doorgaat naar de volgende sectie.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Het project ProductsPortal in een Azure-web-app implementeren

De volgende stap bestaat uit het opnieuw publiceren van de **ProductsPortal** front-end van Azure web app:

1. Klik in **Solution Explorer** met de rechter muisknop op het project **ProductsPortal** en selecteer **publiceren**. Selecteer op de pagina **Publiceren** de optie **Publiceren**.

   > [!NOTE]
   > Mogelijk wordt er een foutbericht weergegeven in het browservenster wanneer het **ProductsPortal**-webproject automatisch wordt gestart na de implementatie. Dit is normaal en doet zich voor omdat de **ProductsServer**-toepassing nog niet wordt uitgevoerd.
   >

1. Kopieer de URL van de geïmplementeerde web-app. U hebt de URL later nodig. U kunt deze URL ook ophalen via het venster **Azure app service activiteit** in Visual Studio:

   ![URL van de geïmplementeerde app][9]

1. Sluit het browservenster om het uitvoeren van de toepassing te stoppen.

<a name="set-productsportal-as-web-app"></a>Voordat u de toepassing uitvoert in de Cloud, moet u ervoor zorgen dat **ProductsPortal** in Visual Studio wordt gestart als een web-app.

1. Klik in Visual Studio met de rechter muisknop op het project **ProductsPortal** en selecteer **Eigenschappen**.
1. Selecteer **Web**. Kies onder **actie starten** de optie **Start URL**. Voer de URL in voor uw eerder geïmplementeerde web-app in dit voor beeld `https://productsportal20190906122808.azurewebsites.net/` .

    ![Start-URL][27]

1. Klik op **Bestand** > **Alles opslaan**.
1. Selecteer oplossing **bouwen opnieuw**  >  **samen stellen**.

## <a name="run-the-application"></a>De toepassing uitvoeren

Selecteer F5 om de toepassing te bouwen en uit te voeren. De on-premises server, die de **ProductsServer** -console toepassing is, moet eerst worden gestart. vervolgens moet de **ProductsPortal** -toepassing worden gestart in een browser venster, zoals hier wordt weer gegeven:

   ![De web-app in Azure uitvoeren][1]

De product inventarisatie vermeldt gegevens die zijn opgehaald uit het on-premises systeem van de product service en geeft die gegevens weer in de web-app. Controleer de URL om ervoor te zorgen dat **ProductsPortal** als een Azure-web-app wordt uitgevoerd in de cloud.

   > [!IMPORTANT]
   > De **ProductsServer**-consoletoepassing moet worden uitgevoerd en moet de gegevens aan de **ProductsPortal**-toepassing kunnen leveren. Als er een fout wordt weer gegeven in de browser, wacht u een paar seconden voordat **ProductsServer** wordt geladen en wordt het volgende bericht weer gegeven. Vernieuw vervolgens de browser.
   >

Vernieuw de pagina **ProductsPortal** in de browser. Elke keer dat u de pagina vernieuwt, wordt met de server-app een bericht weergegeven wanneer `GetProducts()` vanuit **ProductsServer** wordt aangeroepen.

![Bijgewerkte uitvoer][38]

## <a name="next-steps"></a>Volgende stappen

Ga naar de volgende zelfstudie:

> [!div class="nextstepaction"]
>[Een on-premises WCF-service zichtbaar maken voor een WCF-client buiten uw netwerk](service-bus-relay-tutorial.md)

[0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
[1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app.png
[NuGet]: https://nuget.org

[11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/configure-productsserver.png
[13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/install-nuget-service-bus-productsserver.png
[15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
[16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/choose-web-application-template.png
[17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-class-productsportal.png
[18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/change-authentication.png
[9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/web-publish-activity.png
[10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally.png

[21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally-no-content.png
[24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-existing-item-link.png
[25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
[26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
[27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/launch-app-as-web-app.png

[36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
[38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
[41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
[43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
