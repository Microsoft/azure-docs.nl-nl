---
title: Lokale cache
description: Meer informatie over hoe lokale cache werkt in Azure App Service en hoe u de status van de lokale cache van uw app kunt inschakelen, verg Roten of verkleinen.
tags: optional
ms.assetid: e34d405e-c5d4-46ad-9b26-2a1eda86ce80
ms.topic: article
ms.date: 03/04/2016
ms.custom: seodec18
ms.openlocfilehash: 81782f63199a9fe8f43f56aeefcd1c68951d57a4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96852249"
---
# <a name="azure-app-service-local-cache-overview"></a>Overzicht van lokale cache Azure App Service

> [!NOTE]
> De lokale cache wordt niet ondersteund in functie-apps of in containers App Service-apps, zoals in [Windows-containers](quickstart-custom-container.md?pivots=container-windows) of op [app service in Linux](overview.md#app-service-on-linux).


Azure App Service inhoud wordt opgeslagen op Azure Storage en wordt op een duurzame manier als een inhouds share geoppereerd. Dit ontwerp is bedoeld om te werken met verschillende apps en heeft de volgende kenmerken:  

* De inhoud wordt gedeeld door meerdere exemplaren van virtuele machines (VM) van de app.
* De inhoud is duurzaam en kan worden gewijzigd door apps uit te voeren.
* Logboek bestanden en diagnostische gegevens bestanden zijn beschikbaar in dezelfde gedeelde map met inhoud.
* Wanneer u nieuwe inhoud publiceert, wordt de inhoudsmap rechtstreeks bijgewerkt. U kunt dezelfde inhoud direct weer geven via de SCM-website en de app die wordt uitgevoerd (doorgaans sommige technologieën zoals ASP.NET doen de herstart van een app op een aantal wijzigingen in het bestand beginnen met het ophalen van de meest recente inhoud).

Hoewel veel apps een of meer van deze functies gebruiken, hebben sommige apps alleen een hoogwaardige, alleen-lezen inhouds opslag nodig die kan worden uitgevoerd met hoge Beschik baarheid. Deze apps kunnen profiteren van een VM-exemplaar van een specifieke lokale cache.

De functie Azure App Service lokale cache biedt een weer gave van webrollen van uw inhoud. Deze inhoud is een schrijf-maar-verwijder cache van uw opslag inhoud die asynchroon is gemaakt voor het opstarten van de site. Wanneer de cache gereed is, wordt de site overgeschakeld om te worden uitgevoerd op de inhoud in de cache. Voor apps die worden uitgevoerd op een lokale cache gelden de volgende voor delen:

* Ze zijn ongevoelig voor latenties die zich voordoen wanneer ze toegang hebben tot inhoud op Azure Storage.
* Ze zijn ongevoelig voor de geplande upgrades of ongeplande downtime en andere onderbrekingen met Azure Storage die zich voordoen op servers die de inhouds share leveren.
* Ze hebben minder apps opnieuw opgestart vanwege wijzigingen in de opslag share.

## <a name="how-the-local-cache-changes-the-behavior-of-app-service"></a>Hoe de lokale cache het gedrag van App Service wijzigt
* _D:\home_ verwijst naar de lokale cache, die op het VM-exemplaar wordt gemaakt wanneer de app wordt gestart. _D:\Local_ blijft verwijzen naar de tijdelijke VM-specifieke opslag.
* De lokale cache bevat een eenmalige kopie van de _/site_ -en _/siteextensions_ -mappen van het gedeelde inhouds archief, respectievelijk op _D:\home\site_ en _D:\home\siteextensions_. De bestanden worden gekopieerd naar de lokale cache wanneer de app wordt gestart. De grootte van de twee mappen voor elke app is standaard beperkt tot 1 GB, maar kan worden verhoogd tot 2 GB. Houd er rekening mee dat als de cache groter wordt, het langer duurt om de cache te laden. Als u de limiet voor de lokale cache hebt verhoogd naar 2 GB en de gekopieerde bestanden de maximum grootte van 2 GB overschrijden, App Service lokale cache en lees bewerkingen op de externe bestands share op de achtergrond genegeerd. Als er geen limiet is gedefinieerd of als de limiet is ingesteld op een lagere waarde dan 2 GB en de gekopieerde bestanden de limiet overschrijden, kan de implementatie of swap mislukken met een fout.
* De lokale cache is lezen/schrijven. Elke wijziging wordt echter verwijderd wanneer de app virtuele machines verplaatst of opnieuw wordt gestart. Gebruik niet de lokale cache voor apps die essentiële gegevens in het inhouds archief opslaan.
* _D:\home\LogFiles_ en _D:\home\Data_ bevatten logboek bestanden en app-gegevens. De twee submappen worden lokaal opgeslagen in het VM-exemplaar en worden periodiek naar het gedeelde inhouds archief gekopieerd. Apps kunnen logboek bestanden en gegevens persistent maken door deze te schrijven naar deze mappen. De kopie naar het gedeelde inhouds archief is echter het meest geschikt voor het geval dat logboek bestanden en gegevens verloren gaan als gevolg van een plotselinge crash van een VM-exemplaar.
* Het [streamen van Logboeken](troubleshoot-diagnostic-logs.md#stream-logs) wordt beïnvloed door de best mogelijke kopieer activiteit. U kunt in de gestreamde Logboeken een vertraging van één minuut opvolgen.
* In het gedeelde inhouds archief bevindt zich een wijziging in de mappen structuur van de _logboek bestanden_ en _gegevens_ mappen voor apps die gebruikmaken van de lokale cache. Er bevinden zich nu submappen die het naam patroon van ' unieke id ' + tijds tempel volgen. Elk van de submappen komt overeen met een VM-exemplaar waarop de app wordt uitgevoerd of die wordt uitgevoerd.
* Andere mappen in _D:\home_ blijven in de lokale cache en worden niet gekopieerd naar het gedeelde inhouds archief.
* App-implementatie via een ondersteunde methode publiceert rechtstreeks naar het archief met duurzame gedeelde inhoud. Als u de _D:\home\site_ -en _D:\home\siteextensions_ -mappen in de lokale cache wilt vernieuwen, moet u de app opnieuw starten. Zie de informatie verderop in dit artikel om de levens cyclus naadloos te maken.
* De standaard inhouds weergave van de SCM-site blijft die van het gedeelde inhouds archief.

## <a name="enable-local-cache-in-app-service"></a>Lokale cache inschakelen in App Service 

> [!NOTE]
> De lokale cache wordt niet ondersteund in de **F1** -of **D1** -laag. 

U kunt de lokale cache configureren met behulp van een combi natie van gereserveerde app-instellingen. U kunt deze app-instellingen configureren met behulp van de volgende methoden:

* [Azure-portal](#Configure-Local-Cache-Portal)
* [Azure Resource Manager](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Lokale cache configureren met behulp van de Azure Portal
<a name="Configure-Local-Cache-Portal"></a>

U schakelt lokale cache per web-app in met behulp van deze app-instelling: `WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![App-instellingen Azure Portal: lokale cache](media/app-service-local-cache-overview/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>Lokale cache configureren met behulp van Azure Resource Manager
<a name="Configure-Local-Cache-ARM"></a>

```json

...

{
    "apiVersion": "2015-08-01",
    "type": "config",
    "name": "appsettings",
    "dependsOn": [
        "[resourceId('Microsoft.Web/sites/', variables('siteName'))]"
    ],

    "properties": {
        "WEBSITE_LOCAL_CACHE_OPTION": "Always",
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "1000"
    }
}

...
```

## <a name="change-the-size-setting-in-local-cache"></a>De instelling grootte in lokale cache wijzigen
De grootte van de lokale cache is standaard **1 GB**. Dit geldt ook voor de mappen/site en/siteextensions die worden gekopieerd uit het inhouds archief, evenals alle lokaal gemaakte logboeken en gegevens mappen. Gebruik de app-instelling om deze limiet te verhogen `WEBSITE_LOCAL_CACHE_SIZEINMB` . U kunt de grootte verhogen tot **2 GB** (2000 MB) per app. Houd er rekening mee dat het langer duurt voordat de lokale cache wordt geladen naarmate de grootte toeneemt.

## <a name="best-practices-for-using-app-service-local-cache"></a>Aanbevolen procedures voor het gebruik van App Service lokale cache
U wordt aangeraden lokale cache te gebruiken in combi natie met de functie [staging Environment](../app-service/deploy-staging-slots.md) .

* Voeg de *plak* app-instelling `WEBSITE_LOCAL_CACHE_OPTION` met de waarde `Always` toe aan uw **productie** sleuf. Als u gebruikt `WEBSITE_LOCAL_CACHE_SIZEINMB` , voegt u deze ook als een plak instelling toe aan uw productie sleuf.
* Maak een **staging** -sleuf en publiceer deze naar uw staging-sleuf. Normaal gesp roken stelt u de staging-sleuf niet in voor het gebruik van een lokale cache om een naadloze implementatie van de levens cyclus voor fase ring mogelijk te maken als u de voor delen van de lokale cache voor de productie site ontvangt.
* Test uw site op basis van uw staging-sleuf.  
* Wanneer u klaar bent, kunt u een [swap bewerking](../app-service/deploy-staging-slots.md#Swap) uitgeven tussen uw staging-en productie-sleuven.  
* Plak instellingen bevatten naam en plak een sleuf. Als de Faserings sleuf wordt gewisseld naar productie, neemt deze de instellingen van de lokale cache-app over. De zojuist verwisselde productie sleuf wordt na een paar minuten uitgevoerd op de lokale cache en wordt na de wisseling opwarmen als onderdeel van de sleuf opwarm. Wanneer de wisseling van de sleuf is voltooid, wordt uw productie sleuf uitgevoerd op basis van de lokale cache.

## <a name="frequently-asked-questions-faq"></a>Veelgestelde vragen

### <a name="how-can-i-tell-if-local-cache-applies-to-my-app"></a>Hoe kan ik zien of de lokale cache van toepassing is op mijn app?
Als uw app een hoogwaardige, betrouw bare inhouds opslag nodig heeft, wordt het inhouds archief niet gebruikt voor het schrijven van kritieke gegevens tijdens runtime. Dit is minder dan 2 GB in totale grootte. het antwoord is "ja"! Als u de totale grootte van uw/site-en/siteextensions-mappen wilt ophalen, kunt u de site-extensie ' Azure Web Apps Disk Usage ' gebruiken.

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>Hoe kan ik zien of mijn site is overgeschakeld naar het gebruik van een lokale cache?
Als u de functie lokale cache gebruikt met Faserings omgevingen, wordt de wissel bewerking niet voltooid totdat de lokale cache wordt opgewarmd. Als u wilt controleren of uw site wordt uitgevoerd op de lokale cache, kunt u de variabele voor de werk proces omgeving controleren `WEBSITE_LOCALCACHE_READY` . Gebruik de instructies op de pagina [omgevings variabele van werk processen](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) voor toegang tot de omgevings variabele werk processen op meerdere exemplaren.  

### <a name="i-just-published-new-changes-but-my-app-does-not-seem-to-have-them-why"></a>Ik heb zojuist nieuwe wijzigingen gepubliceerd, maar mijn app lijkt deze niet te bevatten. Hoe komt dat?
Als uw app gebruikmaakt van lokale cache, moet u de site opnieuw opstarten om de laatste wijzigingen op te halen. Wilt u geen wijzigingen publiceren naar een productie site? Zie de sleuf opties in het gedeelte eerder best practices.

> [!NOTE]
> De optie [uitvoeren vanaf pakket](deploy-run-package.md) implementatie is niet compatibel met de lokale cache.

### <a name="where-are-my-logs"></a>Waar bevinden zich mijn logboeken?
In het geval van een lokale cache zien uw logboeken en gegevens mappen er iets anders uit. De structuur van uw submappen blijft echter hetzelfde, behalve dat de submappen worden Nestled onder een submap met de notatie ' unieke VM-id ' + tijds tempel.

### <a name="i-have-local-cache-enabled-but-my--app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>Er is een lokale cache ingeschakeld, maar mijn app wordt nog steeds opnieuw gestart. De reden hiervoor? Ik dacht dat lokale cache is geholpen bij het opnieuw starten van een app.
Lokale cache helpt te voor komen dat de app voor opslag is gestart. Uw app kan echter nog steeds opnieuw worden gestart tijdens de geplande infrastructuur upgrades van de virtuele machine. De algemene app wordt opnieuw gestart, zodat de lokale cache minder hoeft te worden gebruikt.

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>Sluit de lokale cache alle directory's uit die naar het snellere lokale station worden gekopieerd?
Als onderdeel van de stap waarmee de opslag inhoud wordt gekopieerd, wordt een map met de naam opslag plaats uitgesloten. Dit helpt bij scenario's waarbij uw site-inhoud mogelijk een broncode beheer opslagplaats bevat die mogelijk niet nodig is in de dag van de dag van de app. 

### <a name="how-to-flush-the-local-cache-logs-after-a-site-management-operation"></a>De logboeken van de lokale cache leegmaken na een site beheer bewerking?
Als u de logboeken van de lokale cache wilt leegmaken, stopt en start u de app opnieuw. Met deze actie wordt de oude cache gewist. 
