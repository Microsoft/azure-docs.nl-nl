---
title: Zelfstudie- Een update van een register met geo-replicatie pushen
description: Push een bijgewerkte Docker-installatiekopie naar uw Azure-containerregister met geo-replicatie. Vervolgens worden de wijzigingen automatisch geïmplementeerd in web-apps die in meerdere regio's worden uitgevoerd. Deel drie van een serie van drie.
ms.topic: tutorial
ms.date: 04/30/2018
ms.custom: seodec18, mvc
ms.openlocfilehash: 9222ac31e067cba6a0ffa71143c90f906ba6ff7f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74454687"
---
# <a name="tutorial-push-an-updated-container-image-to-a-geo-replicated-container-registry-for-regional-web-app-deployments"></a>Zelfstudie: een bijgewerkte containerinstallatiekopie naar een containerregister met geo-replicatie voor regionale web-app-implementaties pushen

Dit is deel drie van een serie met drie zelfstudies. In de [vorige zelfstudie](container-registry-tutorial-deploy-app.md) is geo-replicatie geconfigureerd voor twee verschillende regionale web-app-implementaties. In deze zelfstudie past u eerst de app aan, maakt u daarna een nieuwe containerinstallatiekopie en pusht u deze vervolgens naar het geo-gerepliceerde register. Ten slotte kunt u deze wijziging, die automatisch wordt geïmplementeerd door Azure Container Registry-webhooks, in beide web-app-instanties weergeven.

Deze zelfstudie, het laatste deel in de serie, gaat over het volgende:

> [!div class="checklist"]
> * De web-app-HTML wijzigen
> * De Docker-installatiekopie maken en taggen
> * De wijziging naar Azure Container Registry pushen
> * De bijgewerkte app in twee verschillende regio's weergeven

Als u de twee regionale *Web App for Containers*-implementaties nog niet hebt geconfigureerd, gaat u naar de vorige zelfstudie in de serie: [Web-app implementeren vanuit Azure Container Registry](container-registry-tutorial-deploy-app.md).

## <a name="modify-the-web-application"></a>De webtoepassing wijzigen

In deze stap brengt u een wijziging aan in de web-app die zeer zichtbaar is wanneer u de bijgewerkte containerinstallatiekopie naar Azure Container Registry pusht.

Zoek het bestand `AcrHelloworld/Views/Home/Index.cshtml` in de app-bron die u in een vorige zelfstudie hebt [gekloond vanuit GitHub](container-registry-tutorial-prepare-registry.md#get-application-code) en open het in uw favoriete teksteditor. Voeg de volgende regel toe onder de bestaande regel `<h1>`:

```html
<h1>MODIFIED</h1>
```

De aangepaste `Index.cshtml` moet er ongeveer zo uitzien:

```html
@{
    ViewData["Title"] = "Azure Container Registry :: Geo-replication";
}
<style>
    body {
        background-image: url('images/azure-regions.png');
        background-size: cover;
    }
    .footer {
        position: fixed;
        bottom: 0px;
        width: 100%;
    }
</style>

<h1 style="text-align:center;color:blue">Hello World from:  @ViewData["REGION"]</h1>
<h1>MODIFIED</h1>
<div class="footer">
    <ul>
        <li>Registry URL: @ViewData["REGISTRYURL"]</li>
        <li>Registry IP: @ViewData["REGISTRYIP"]</li>
        <li>Registry Region: @ViewData["REGION"]</li>
    </ul>
</div>
```

## <a name="rebuild-the-image"></a>De installatiekopie herbouwen

Nu u de web-app hebt bijgewerkt, moet u de bijbehorende containerinstallatiekopie herbouwen. Gebruik net als eerder de volledig gekwalificeerde installatiekopienaam, met inbegrip van de FQDN (Fully Qualified Domain Name) voor de aanmeldingsserver, voor de tag:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

## <a name="push-image-to-azure-container-registry"></a>Installatiekopie pushen naar Azure Container Registry

Push hierna de bijgewerkte containerinstallatiekopie *acr-helloworld* naar het geo-gerepliceerde register. Hier voert u een enkele `docker push`-opdracht uit om de bijgewerkte installatiekopie naar de registerreplica's in de regio's *VS - west* en *VS - oost* te implementeren.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

De `docker push`-uitvoer moet er ongeveer als volgt uitzien:

```console
$ docker push uniqueregistryname.azurecr.io/acr-helloworld:v1
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
5b9454e91555: Pushed
d6803756744a: Layer already exists
b7b1f3a15779: Layer already exists
a89567dff12d: Layer already exists
59c7b561ff56: Layer already exists
9a2f9413d9e4: Layer already exists
a75caa09eb1f: Layer already exists
v1: digest: sha256:4c3f2211569346fbe2d1006c18cbea2a4a9dcc1eb3a078608cef70d3a186ec7a size: 1792
```

## <a name="view-the-webhook-logs"></a>Logboeken van webhooks bekijken

Terwijl de installatiekopie wordt gerepliceerd, worden de Azure Container Registry-webhooks geactiveerd.

Als u de regionale webhooks wilt bekijken die zijn gemaakt tijdens de implementatie van de container naar *Web App for Containers* in een vorige zelfstudie, gaat u naar het containerregister in Azure Portal en selecteert u onder **SERVICES** de optie **Webhooks**.

![Webhooks van containerregisters in Azure Portal][tutorial-portal-01]

Selecteer elke webhook om de geschiedenis van de bijbehorende aanroepen en antwoorden te bekijken. U zou een rij moeten zien voor de actie **push** in de logboeken van beide webhooks. Hier toont het logboek voor de webhook in de regio *VS - west* de actie **push** die is geactiveerd door de `docker push` in de vorige stap:

![Logboek voor containerregister-webhook in Azure Portal (VS - west)][tutorial-portal-02]

## <a name="view-the-updated-web-app"></a>Bijgewerkte web-app weergeven

De webhooks geven door aan de web-apps dat een nieuwe installatiekopie naar het register is gepusht, en hierdoor wordt automatisch de bijgewerkte container naar de twee regionale web-apps geïmplementeerd.

Controleer of de app in beide implementaties is bijgewerkt door naar beide regionale web-app-implementaties te navigeren in uw webbrowser. U kunt de URL voor de geïmplementeerde web-app vinden in de rechterbovenhoek van elk App Service-overzichtstabblad.

![App Service-overzicht in Azure Portal][tutorial-portal-03]

Selecteer de link in het App Service-overzicht om de bijgewerkte app te bekijken. Hier volgt een voorbeeldweergave van de app die wordt uitgevoerd in *VS - west*:

![Browserweergave van gewijzigde web-app die wordt uitgevoerd in de regio VS - west][deployed-app-westus-modified]

Controleer of de bijgewerkte containerinstallatiekopie ook is geïmplementeerd naar *VS - oost* door deze in uw browser weer te geven.

![Browserweergave van gewijzigde web-app die wordt uitgevoerd in de regio VS - oost][deployed-app-eastus-modified]

Met één `docker push` hebt u automatisch de webtoepassing bijgewerkt die wordt uitgevoerd in beide regionale web-app-implementaties. En Azure Container Registry leverde de containerinstallatiekopieën uit de opslagplaatsen het dichtst bij elke implementatie.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een nieuwe versie van de web-app-container bijgewerkt en naar het geo-gerepliceerde register gepusht. Webhooks in Azure Container Registry gaven de update door aan Web App for Containers, waardoor een lokale pull vanuit de dichtstbijzijnde registerreplica's werd geactiveerd.

### <a name="acr-build-automated-image-build-and-patch"></a>ACR Build: geautomatiseerde build en patch voor de installatiekopie

Naast geo-replicatie is ACR Build nog een functie van Azure Container Registry waarmee u de implementatiepijplijn van uw container kunt optimaliseren. Bekijk eerst het ACR Build-overzicht om een idee te krijgen van de mogelijkheden:

[OS- en frameworkpatching automatiseren met ACR Build](container-registry-tasks-overview.md)

<!-- IMAGES -->
[deployed-app-eastus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-eastus-modified.png
[deployed-app-westus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-westus-modified.png
[local-container-01]: ./media/container-registry-tutorial-deploy-update/local-container-01.png
[tutorial-portal-01]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-01.png
[tutorial-portal-02]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-02.png
[tutorial-portal-03]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-03.png