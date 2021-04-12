---
title: Een app voor meerdere service maken en implementeren naar Service Fabric Mesh
description: In deze zelfstudie maakt u een Azure Service Fabric Mesh-toepassing voor meerdere services, bestaande uit een ASP.NET Core-website die communiceert met een back-endwebservice. U spoort de fouten in de toepassing lokaal op en publiceert de toepassing in Azure.
author: georgewallace
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-csharp
ms.openlocfilehash: b0bdb3c09aead812e1c16f4d0d17aae58e141809
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99626790"
---
# <a name="tutorial-create-debug-deploy-and-upgrade-a-multi-service-service-fabric-mesh-app"></a>Zelfstudie: Een Service Fabric Mesh-toepassing met meerdere services bouwen, foutvrij maken, implementeren en upgraden

> [!IMPORTANT]
> De preview-versie van Azure Service Fabric Mesh is buiten gebruik gesteld. Nieuwe implementaties zijn niet langer toegestaan via de API van Service Fabric net. Ondersteuning voor bestaande implementaties gaat door tot 28 april 2021.
> 
> Zie [Azure service Fabric Netpreview buiten](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)gebruik stellen voor meer informatie.

Deze zelfstudie is deel één van een serie. U leert hoe u met Visual Studio een Azure Service Fabric Mesh-app maakt die een ASP.NET-webfront-end- en een ASP.NET Core Web API-back-endservice bevat. Vervolgens spoort u de fouten in de app op in uw lokale ontwikkelcluster. Daarna publiceert u de app naar Azure, brengt u wijzigingen aan in de configuratie en de code en werkt u de app bij. Als laatste gaat u ongebruikte Azure-resources opschonen zodat u niet hoeft te betalen voor wat u niet gebruikt.

Wanneer u klaar bent, hebt u stapsgewijs de meeste van de fasen van het levenscyclusbeheer van de app doorlopen en een app gebouwd die een service-naar-serviceaanroep in een Service Fabric Mesh-app demonstreert.

Als u de taken-app niet handmatig wilt maken, kunt u [de broncode downloaden](https://github.com/azure-samples/service-fabric-mesh) voor de voltooide toepassing en verdergaan met [Fouten in de app lokaal opsporen](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md).

In deel 1 van de reeks leert u het volgende:

> [!div class="checklist"]
> * Visual Studio gebruiken om een Service Fabric Mesh-app te maken bestaande uit een ASP.NET-webfront-end.
> * Een model maken die de taakitems representeren.
> * Een back-endservice maken en er gegevens uithalen.
> * Een controller en DataContext toevoegen als deel van dat Model View Controller-patroon voor de back-endservice.
> * Een webpagina maken om taakitems weer te geven.
> * Omgevingsvariabelen maken waarmee de back-endservice worden geïdentificeerd

In deze zelfstudiereeks leert u het volgende:
> [!div class="checklist"]
> * Een Service Fabric Mesh-app maken in Visual Studio
> * [Fouten opsporen in een Service Fabric Mesh-app die wordt uitgevoerd in uw lokale ontwikkelcluster](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Een Service Fabric Mesh-app implementeren](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Een Service Fabric Mesh-app bijwerken](service-fabric-mesh-tutorial-upgrade.md)
> * [Service Fabric Mesh-bronnen opschonen](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Vereisten

Voor u met deze zelfstudie begint:

* Als u nog geen abonnement op Azure hebt, kunt u een [gratis account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

* Controleer of u [uw ontwikkelomgeving hebt ingesteld](service-fabric-mesh-howto-setup-developer-environment-sdk.md). Hieronder valt het installeren van de Service Fabric-runtime, SDK, Docker en Visual Studio 2017.

## <a name="create-a-service-fabric-mesh-project-in-visual-studio"></a>Een Service Fabric Mesh-project maken in Visual Studio

Voer Visual Studio uit en selecteer **File** > **New** > **Project...**

In het dialoogvenster **New Project** typt u `mesh` in het vak **Search**. Selecteer de sjabloon **Service Fabric Mesh Application**. (Als u de sjabloon niet ziet, controleert u of u de Mesh-SDK en de previewversie van de hulpmiddelen van Visual Studio hebt geïnstalleerd, zoals beschreven in [set up your development environment](service-fabric-mesh-howto-setup-developer-environment-sdk.md) (uw ontwikkelomgeving instellen).)  

Typ `todolistapp` in het vak **Name** en stel in het vak **Location** het mappad in naar de plaats waar u de bestanden voor het project wilt opslaan.

Controleer of het selectievakje **Create directory for solution** is ingeschakeld en klik op **OK** om het Service Fabric Mesh-project te maken.

![Schermopname van het maken van een Service Fabric Mesh-project.](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-project.png)

Vervolgens ziet u het dialoogvenster **New Service Fabric Service**.

### <a name="create-the-web-front-end-service"></a>De front-endservice voor het web maken

Selecteer in het dialoogvenster **New Service Fabric Service** het projecttype **ASP.NET Core** en controleer of **Container OS** is ingesteld op **Windows**.

Stel **Service Name** in op **WebFrontEnd**. Druk op **OK** om de ASP.NET Core-service te maken.

![Dialoogvenster Nieuw project van Service Fabric Mesh in Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-service-fabric-service.png)

Vervolgens ziet u het dialoogvenster ASP.NET Core Web Application. Selecteer **Webtoepassing** en klik op **OK**.

![Schermafbeelding met de sjabloon Webtoepassing.](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-aspnetcore-app.png)

U hebt nu een Service Fabric Mesh-toepassing gemaakt. Vervolgens maakt u het model voor taakinformatie.

## <a name="create-the-to-do-items-model"></a>Taakitemsmodel maken

Ter vereenvoudiging worden de taakitems in-memory in een lijst opgeslagen. Maak een klassebibliotheek voor de taakitems en een lijst om ze in te bewaren. In Visual Studio, waarin momenteel de oplossing **todolistapp** is geladen, selecteert u **File** > **Add** > **New Project**.

In het dialoogvenster **Add New Project** typt u `C# .net core class` in het vak **Search**. Selecteer de sjabloon **Class Library (.NET Core)**.

Typ `Model` in het vak **Name**. Klik op **OK** om de klassebibliotheek te maken.

Klik in Solution Explorer, onder **Model**, met de rechtermuisknop op **Class1.cs** en kies **Rename**. Geef de klasse de nieuwe naam **ToDoItem.cs**. Als u wordt gevraagd alle verwijzingen een andere naam te geven, klikt u op **Yes**.

Vervang de inhoud van de lege `class ToDoItem` door:

```csharp
public class ToDoItem
{
    public string Description { get; set; }
    public int Index { get; set; }
    public bool Completed { get; set; }

    public ToDoItem(string description)
    {
        Description = description;
        Index = 0;
    }

    public static ToDoItem Load(string description, int index, bool completed)
    {
        ToDoItem newItem = new ToDoItem(description)
        {
            Index = index,
            Completed = completed
        };

        return newItem;
    }
}
```

Deze klasse staat voor taakitems.

Klik in Visual Studio met de rechtermuisknop op de klassebibliotheek **Model** en selecteer **Add** > **Class...** om een lijst te maken voor de taakitems. Het dialoogvenster **Add New Item** wordt weergegeven. Stel **Name** in op `ToDoList.cs` en klik op **Add**.

Vervang in **ToDoList.cs** de lege `class ToDoList` door:

```csharp
public class ToDoList
{
    private List<ToDoItem> _items;

    public string Name { get; set; }
    public IEnumerable<ToDoItem> Items { get => _items; }

    public ToDoList(string name)
    {
        Name = name;
        _items = new List<ToDoItem>();
    }

    public ToDoItem Add(string description)
    {
        var item = new ToDoItem(description);
        _items.Add(item);
        item.Index = _items.IndexOf(item);
        return item;
    }
    public void Add(ToDoItem item)
    {
        _items.Add(item);
        item.Index = _items.Count - 1;
    }

    public ToDoItem RemoveAt(int index)
    {
        if (index >= 0 && index < _items.Count)
        {
            var result = _items[index];
            _items.RemoveAt(index);

            // Reorder items
            for (int i = index; i < _items.Count; i++)
            {
                _items[i].Index = i;
            }

            return result;
        }
        else
        {
            throw new IndexOutOfRangeException();
        }
    }
}
```

Maak vervolgens de Service Fabric-service waarmee de taakitems worden gevolgd.

## <a name="create-the-back-end-service"></a>Back-endservice maken

Klik in het Visual Studio-venster **Solution Explorer** met de rechtermuisknop op **todolistapp** en klik op **Add** > **New Service Fabric Service...**

Het dialoogvenster **New Service Fabric Service** wordt weergegeven. Selecteer het projecttype **ASP.NET Core** en controleer of **Container OS** is ingesteld op **Windows**. Stel **Service Name** in op **ToDoService**. Klik op **OK** om de ASP.NET Core-service te maken.

Vervolgens wordt het dialoogvenster **New ASP.NET Core Web Application** weergegeven. In dit dialoogvenster selecteert u **API** en vervolgens **OK**. Er wordt een project voor de service aan de oplossing toegevoegd.

![Nieuwe ASP.NET Core-toepassing in Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-webapi.png)

Omdat de back-endservice geen gebruikersinterface heeft, schakelt u het starten van de browser uit als de service wordt gestart. Klik in **Solution Explorer** met de rechtermuisknop op **ToDoService** en selecteer **Properties**. Selecteer in het eigenschappenvenster aan de linkerkant het tabblad **Debug** en schakel het selectievakje **Launch browser** in. Druk op **Ctrl+S** om de wijziging op te slaan.

Omdat deze service de taakinformatie onderhoudt, voegt u een verwijzing aan de klassenbibliotheek Model toe. Klik in Solution Explorer met de rechtermuisknop op **ToDoService** en selecteer **Add** > **Reference...** . Het dialoogvenster **Reference Manager** wordt weergegeven.

Schakel in **Reference Manager** het selectievakje in voor **Model** en klik op **OK**.

### <a name="add-a-data-context&quot;></a>Gegevenscontext toevoegen

Maak vervolgens een gegevenscontext waarmee het gebruik van gegevens uit het gegevensmodel wordt gecoördineerd.

U voegt de gegevenscontextklasse toe door in Solution Explorer met de rechtermuisknop op **ToDoService** te klikken en vervolgens **Add** > **Class** te selecteren.
Controleer in het dialoogvenster **Add New Item** of **Class** is geselecteerd, stel **Name** in op `DataContext.cs` en klik op **Add**.

Vervang in **DataContext.cs** de inhoud van de lege `class DataContext` door:

```csharp
public static class DataContext
{
    public static Model.ToDoList ToDoList { get; } = new Model.ToDoList(&quot;Azure learning List");

    static DataContext()
    {
        // Seed to-do list
        ToDoList.Add(Model.ToDoItem.Load("Learn about microservices", 0, true));
        ToDoList.Add(Model.ToDoItem.Load("Learn about Service Fabric", 1, true));
        ToDoList.Add(Model.ToDoItem.Load("Learn about Service Fabric Mesh", 2, false));
    }
}
```

Deze minimale gegevenscontext vult enkele voorbeelden van taakitems en biedt er toegang toe.

### <a name="add-a-controller"></a>Een controller toevoegen

Toen het project **ToDoService** werd gemaakt, werd er door de sjabloon een standaardcontroller opgegeven die de HTTP-aanvragen afhandelt en het HTTP-antwoord maakt. Open in **Solution Explorer**, onder **ToDoService**, de map **Controllers** om het bestand **ValuesController.cs** te bekijken. 

Klik met de rechtermuisknop op **ValuesController.cs** en vervolgens op **Rename**. Geef het bestand de naam `ToDoController.cs`. Als u wordt gevraagd alle verwijzingen een andere naam te geven, klikt u op **Yes**.

Open het bestand **ToDoController.cs** en vervang de inhoud van `class ToDoController` door:

```csharp
[Route("api/[controller]")]
public class ToDoController : Controller
{
    // GET api/todo
    [HttpGet]
    public IEnumerable<Model.ToDoItem> Get()
    {
        return DataContext.ToDoList.Items;
    }

    // GET api/todo/5
    [HttpGet("{index}")]
    public Model.ToDoItem Get(int index)
    {
        return DataContext.ToDoList.Items.ElementAt(index);
    }

    //// POST api/values
    //[HttpPost]
    //public void Post([FromBody]string value)
    //{
    //}

    //// PUT api/values/5
    //[HttpPut("{id}")]
    //public void Put(int id, [FromBody]string value)
    //{
    //}

    // DELETE api/values/5
    [HttpDelete("{index}")]
    public void Delete(int index)
    {
    }
}
```

In deze zelfstudie wordt het toevoegen, verwijderen en dergelijke niet behandelt, zodat u zich alleen hoeft te concentreren op het communiceren met een andere service.

## <a name="create-the-web-page-that-displays-to-do-items"></a>De webpagina maken waarmee de taakitems worden weergegeven

Nu de back-endservice is geïmplementeerd, codeert u de website waarop de bijbehorende taakitems worden weergegeven. De volgende stappen vinden plaats binnen het project **WebFrontEnd**.

De webpagina waarop de taakitems worden weergegeven, heeft toegang nodig tot de klasse **ToDoItem** en de lijst.
Voeg in **Solution Explorer** een verwijzing toe naar het modelproject door met de rechtermuisknop op **WebFrontEnd** te klikken en **Add** > **Reference...** te selecteren Het dialoogvenster **Reference Manager** wordt weergegeven.

Schakel in **Reference Manager** het selectievakje in voor **Model** en klik op **OK**.

Open in **Solution Explorer** de pagina Index.cshtml door naar **WebFrontEnd** > **Pages** > **Index.cshtml** te gaan. Open **Index.cshtml**.

Vervang de inhoud van het hele bestand door de volgende HTML, waarmee een eenvoudige tabel voor het weergeven van taakitems wordt gedefinieerd:

```HTML
@page
@model IndexModel
@{
    ViewData["Title"] = "Home page";
}

<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

Klik op het pictogram van de vervolgkeuzelijst van het bestand **Index.cshtml** in de **Solution Explorer** en open vervolgens **Index.cshtml.cs**.

Boven aan **Index.cshtml.cs** voegt u `using System.Net.Http;` toe

Vervang de inhoud van `public class IndexModel` door:

```csharp
public class IndexModel : PageModel
{
    public Model.ToDoItem[] Items = new Model.ToDoItem[] { };

    public void OnGet()
    {
        HttpClient client = new HttpClient();

        using (HttpResponseMessage response = client.GetAsync(backendUrl).GetAwaiter().GetResult())
        {
            if (response.StatusCode == System.Net.HttpStatusCode.OK)
            {
                Items = Newtonsoft.Json.JsonConvert.DeserializeObject<Model.ToDoItem[]>(response.Content.ReadAsStringAsync().Result);
            }
        }
    }

    private static string backendDNSName = $"{Environment.GetEnvironmentVariable("ToDoServiceName")}";
    private static Uri backendUrl = new Uri($"http://{backendDNSName}:{Environment.GetEnvironmentVariable("ApiHostPort")}/api/todo");
}
```

### <a name="create-environment-variables"></a>Omgevingsvariabelen maken

De URL voor de back-endservice is vereist om met die service te communiceren. In deze zelfstudie leest het volgende codefragment (dat hierboven is gedefinieerd als onderdeel van IndexModel) omgevingsvariabelen om de URL te kunnen opstellen:

```csharp
private static string backendDNSName = $"{Environment.GetEnvironmentVariable("ToDoServiceName")}";
private static Uri backendUrl = new Uri($"http://{backendDNSName}:{Environment.GetEnvironmentVariable("ApiHostPort")}/api/todo");
```

De URL bestaat uit de servicenaam en de poort. Al deze informatie is in het project **ToDoService** aanwezig in het bestand service.yaml.

> [!IMPORTANT]
> In de volgende stappen worden YAML-bestanden gewijzigd.
> Gebruik spaties, geen tabs, om de variabelen in te springen in het bestand service.yaml. Doet u dit niet, dan wordt het bestand niet gecompileerd. Tijdens het maken van de omgevingsvariabelen kunnen tabs worden ingevoegd. Vervang de tabs door spaties. Hoewel u fouten zult zien in de uitvoer van de foutopsporing voor de **compilatie**, wordt de app nog steeds gestart, maar niet totdat u de tabs naar spaties hebt geconverteerd en de compilatie opnieuw hebt uitgevoerd. Om er zeker van te zijn dat er geen tabs meer in het bestand service.yaml aanwezig zijn, kunt u witruimtes zichtbaar maken in de editor van Visual Studio via **Edit**  > **Advanced**  > **View White Space**.
> Houd er rekening mee dat service.yaml-bestanden worden verwerkt met de Engelse landinstellingen. Dus als u een decimaal scheidingsteken nodig hebt, moet u bijvoorbeeld een punt in plaats van een komma gebruiken.

Ga in **Solution Explorer** naar het project **ToDoService** en open **Service Resources** > **service.yaml**.

![Afbeelding 1: het ToDoService-bestand service.yaml](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-serviceyaml-port.png)

 De servicenaam `ToDoService` bevindt zich onder `services:`. Zie (1) in de bovenstaande afbeelding.

* De poort `80` bevindt zich onder `endpoints:`. Zie (2) in de bovenstaande afbeelding. Het poortnummer van uw project wijkt hier mogelijk van af.

Vervolgens moet we omgevingsvariabelen definiëren die de servicenaam en het poortnummer in het project WebFrontEnd representeren zodat dit de back-endservice kan aanroepen.

Ga in **Solution Explorer** naar **WebFrontEnd** > **Service Resources** > **service.yaml** om de variabelen te definiëren waarmee het adres van de back-endservice wordt opgegeven.

Voeg in het bestand service.yam de volgende variabelen toe onder `environmentVariables:`. (U moet eerst de `#` verwijderen om de opmerkingen bij `environmentVariables:` te verwijderen). Het gebruik van spaties is belangrijk, dus zorg dat u de variabelen die u toevoegt uitlijnt met de andere variabelen onder `environmentVariables:`. Het is heel belangrijk dat de waarde voor ApiHostPort overeenkomt met de waarde van de poort voor ToDoServiceListener die eerder is aangetroffen in het bestand service.yaml van ToDoService.

```yaml
- name: ApiHostPort
  value: 
- name: ToDoServiceName
  value: ToDoService
```

> [!Tip]
> Er zijn twee manieren om de waarde op te geven voor `ToDoServiceName`: 
> - Alleen de servicenaam, die zowel in een scenario voor het opsporen van fouten in Windows 10 als tijdens het implementeren van de service in Azure Service Fabric Mesh wordt omgezet.
> - Volledig gekwalificeerde naam als servicename.appname. Dit werkt alleen als u fouten opspoort in Windows 10.
> Het is raadzaam om alleen de naam van de service te gebruiken voor het omzetten van de service.

Het bestand **service.yaml** van het project **WebFrontEnd** moet er zoals dit uitzien, hoewel uw `ApiHostPort`-waarde waarschijnlijk anders is:

![Service.yaml in het project WebFrontEnd](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-serviceyaml-envvars.png)


U bent nu klaar om de installatiekopie van de Service Fabric Mesh-toepassing in uw lokale cluster te bouwen en implementeren, in combinatie met de back-endwebservice.

## <a name="next-steps"></a>Volgende stappen

In dit deel van de zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een Service Fabric Mesh-app maken bestaande uit een ASP.NET-webfront-end.
> * Een model maken die de taakitems representeren.
> * Een back-endservice maken en er gegevens uithalen.
> * Een controller en DataContext toevoegen als deel van dat Model View Controller-patroon voor de back-endservice.
> * Een webpagina maken om taakitems weer te geven.
> * Omgevingsvariabelen maken waarmee de back-endservice worden geïdentificeerd

Ga door naar de volgende zelfstudie:
> [!div class="nextstepaction"]
> [Fouten opsporen in een Service Fabric Mesh-toepassing die in uw lokale ontwikkelcluster wordt uitgevoerd](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)