---
title: Zelf studie-een Azure Service Fabric mesh-toepassing upgraden
description: Deze zelf studie is deel vier van een reeks en laat zien hoe u een Azure Service Fabric-mesh-toepassing rechtstreeks vanuit Visual Studio bijwerkt.
author: georgewallace
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-csharp
ms.openlocfilehash: 1020613eb43177ba159601f253848f8d03f385a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625429"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>Zelf studie: meer informatie over het bijwerken van een Service Fabric-toepassing met Visual Studio

> [!IMPORTANT]
> De preview-versie van Azure Service Fabric Mesh is buiten gebruik gesteld. Nieuwe implementaties zijn niet langer toegestaan via de API van Service Fabric net. Ondersteuning voor bestaande implementaties gaat door tot 28 april 2021.
> 
> Zie [Azure service Fabric Netpreview buiten](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)gebruik stellen voor meer informatie.

Deze zelf studie is deel vier van een reeks en laat zien hoe u een Azure Service Fabric-mesh-toepassing rechtstreeks vanuit Visual Studio bijwerkt. De upgrade bevat een code-update en een configuratie-update. U ziet dat de stappen voor het uitvoeren van een upgrade en publicatie in Visual Studio hetzelfde zijn.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een Service Fabric mesh-service bijwerken met behulp van Visual Studio

In deze zelfstudiereeks leert u het volgende:
> [!div class="checklist"]
> * [Een Service Fabric Mesh-app maken in Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Fouten opsporen in een Service Fabric Mesh-app die wordt uitgevoerd in uw lokale ontwikkelcluster](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Een Service Fabric Mesh-app implementeren](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * Een Service Fabric Mesh-app bijwerken
> * [Service Fabric Mesh-bronnen opschonen](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Vereisten

Voor u met deze zelfstudie begint:

* Als u de to-do-app nog niet hebt geïmplementeerd, volgt u de instructies in [Zelfstudie: een Service Fabric Mesh-webtoepassing implementeren](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

## <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>Een Service Fabric mesh-service bijwerken met behulp van Visual Studio

In dit artikel wordt beschreven hoe u een micro service binnen een toepassing bijwerkt. In dit voor beeld wijzigen we de `WebFrontEnd` service om een taak categorie weer te geven en de hoeveelheid CPU die deze heeft gegeven, te verg Roten. Vervolgens voert u een upgrade uit van de geïmplementeerde service.

## <a name="modify-the-config"></a>De configuratie wijzigen

Wanneer u een Service Fabric mesh-app maakt, voegt Visual Studio een yaml-bestand voor **para meters** toe voor elke implementatie omgeving (Cloud en lokaal). In deze bestanden kunt u para meters en hun waarden definiëren waarnaar vervolgens kan worden verwezen vanuit uw net *. YAML-bestanden zoals service. yaml of Network. yaml.  Visual Studio biedt een aantal variabelen voor u, zoals hoeveel CPU de service kan gebruiken.

De `WebFrontEnd_cpu` para meter wordt bijgewerkt om de CPU-Resources bij te werken naar `1.5` in afwachting dat de Webfront- **End** -service intensief wordt gebruikt.

1. Open in het project **todolistapp** onder **environments**  >  **Cloud** het bestand **para meters. yaml** . Wijzig de `WebFrontEnd_cpu` waarde, in `1.5` . De parameter naam wordt voorafgegaan door de service naam `WebFrontEnd_` als een best practice om deze te onderscheiden van para meters met dezelfde naam die van toepassing zijn op verschillende services.

    ```xml
    WebFrontEnd_cpu: 1.5
    ```

2. Open het bestand **service. yaml** van het **webfrontend** onder **webfrontend**-  >  **service resources**.

    Houd er rekening mee dat de in `resources:` -sectie `cpu:` is ingesteld op `"[parameters('WebFrontEnd_cpu')]"` . Als het project wordt gebouwd voor de Cloud, wordt de waarde voor `'WebFrontEnd_cpu` opgehaald uit het   >    >  yaml-bestand Cloud **para meters.** `1.5` Als het project wordt gebouwd om lokaal te worden uitgevoerd, wordt de waarde opgehaald uit het yaml-bestand van de **omgeving**  >  **Local**  >  **para meters** en wordt ' 0,5 '.

> [!Tip]
> Standaard wordt het parameter bestand dat een peer is van het profiel. yaml-bestand gebruikt om de waarden voor dat profiel. yaml-bestand op te geven.
> Bijvoorbeeld: omgevingen > Cloud > para meters. yaml biedt de parameter waarden voor omgevingen > Cloud > profile. yaml.
>
> U kunt dit overschrijven door het volgende toe te voegen aan het profiel. yaml-bestand: `parametersFilePath=”relative or full path to the parameters file”` bijvoorbeeld `parametersFilePath=”C:\MeshParms\CustomParameters.yaml”` of `parametersFilePath=”..\CommonParameters.yaml”`

## <a name="modify-the-model"></a>Het model wijzigen

Als u een code wijziging wilt introduceren, voegt `Category` u een eigenschap toe aan de `ToDoItem` klasse in het `ToDoItem.cs` bestand.

```csharp
public class ToDoItem
{
    public string Category { get; set; }
    ...
}
```

Werk vervolgens de `Load()` -methode in hetzelfde bestand bij om de categorie in te stellen op een standaard teken reeks:

```csharp
public static ToDoItem Load(string description, int index, bool completed)
{
    ToDoItem newItem = new ToDoItem(description)
    {
        Index = index,
        Completed = completed, 
        Category = "none"    // <-- add this line
    };

    return newItem;
}
```

## <a name="modify-the-service"></a>De service wijzigen

Het `WebFrontEnd` project is een ASP.net core-toepassing met een webpagina waarop taken lijst items worden weer gegeven. Open in het `WebFrontEnd` project `Index.cshtml` en voeg de volgende twee regels toe, zoals hieronder aangegeven, om de categorie van de taak weer te geven:

```HTML
<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Category</th>           @*add this line*@
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Category</td>           @*add this line*@
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

Bouw en voer de app uit om te controleren of er een nieuwe categorie kolom wordt weer gegeven op de webpagina waarop de taken worden vermeld.

## <a name="upgrade-the-app-from-visual-studio"></a>De app bijwerken vanuit Visual Studio

Of u nu een code-upgrade of een configuratie-upgrade uitvoert (in dit geval gaan we beiden doen), werk uw Service Fabric mesh-app op Azure bij door met de rechter muisknop op **todolistapp** in Visual Studio te klikken en vervolgens publiceren te selecteren **...**

Vervolgens ziet u het dialoogvenster **Service Fabric-toepassing publiceren**.

Gebruik de vervolg keuzelijst **doel profiel** om het bestand profile. yaml te selecteren dat u voor deze implementatie wilt gebruiken. Er wordt een upgrade uitgevoerd van de app in de Cloud, dus we selecteren de **Cloud. yaml** in de vervolg keuzelijst, die de `WebFrontEnd_cpu` waarde 1,0 gebruikt die in dat bestand is gedefinieerd.

![Dialoogvenster in Visual Studio over Service Fabric Mesh-publicatie](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Selecteer uw Azure-account en -abonnement. Stel de **locatie** in op de locatie die u hebt gebruikt toen u de to-do-app voor het eerst naar Azure hebt gepubliceerd. In dit artikel wordt **VS-Oost** gebruikt.

Stel de **resource groep** in op de resource groep die u hebt gebruikt toen u de to-do-app voor het eerst naar Azure hebt gepubliceerd.

Stel **Azure container Registry** in op de naam van het Azure-container register dat u hebt gemaakt toen u de to-do oorspronkelijke app naar Azure hebt gepubliceerd.

Klik in het dialoog venster publiceren op de knop **publiceren** om de to-do-app in azure bij te werken.

Bewaak de voortgang van de upgrade door het deel venster **service Fabric tools** te selecteren in het venster Visual Studio **output** . 

Nadat de installatie kopie is gebouwd en gepusht naar de Azure Container Registry, wordt in de uitvoer een **voor status** koppeling weer gegeven waarop u kunt klikken om de implementatie in de Azure portal te controleren.

Zodra de upgrade is voltooid, worden in de uitvoer van de **service Fabric tools** het IP-adres en de poort van uw toepassing weer gegeven in de vorm van een URL.

```json
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Open een webbrowser en navigeer naar de URL om de website in Azure uitgevoerd te zien worden. U ziet nu een webpagina die een kolom Categorie bevat.

## <a name="next-steps"></a>Volgende stappen

In dit deel van de zelfstudie hebt u het volgende geleerd:
> [!div class="checklist"]
> * Een upgrade uitvoeren van een Service Fabric mesh-app met behulp van Visual Studio

Ga door naar de volgende zelfstudie:
> [!div class="nextstepaction"]
> [Service Fabric Mesh-bronnen opschonen](service-fabric-mesh-tutorial-cleanup-resources.md)