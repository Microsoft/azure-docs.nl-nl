---
title: Opmerkingen bij de release Microsoft Azure Storage Explorer
description: Lees de opmerkingen bij de release voor de nieuwste versie van Microsoft Azure Storage Explorer. De release opmerkingen voor eerdere versies worden ook weer gegeven.
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/12/2018
ms.author: cawa
ms.openlocfilehash: 56654f09d3a92eec5b86d8c8e4cd76fec0639394
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97653041"
---
# <a name="microsoft-azure-storage-explorer-release-notes"></a>Opmerkingen bij de release Microsoft Azure Storage Explorer

Dit artikel bevat de nieuwste opmerkingen bij de release voor Azure Storage Explorer, evenals release opmerkingen voor eerdere versies. 

[Microsoft Azure Storage Explorer](./vs-azure-tools-storage-manage-with-storage-explorer.md) is een zelfstandige app waarmee u eenvoudig met Azure Storage gegevens kunt werken in Windows, MacOS en Linux.

Als u vorige versies van Storage Explorer wilt downloaden, gaat u naar de [pagina met releases](https://github.com/microsoft/AzureStorageExplorer/releases) van onze github opslag plaats.

## <a name="version-1110"></a>Versie 1.11.0
11/4/2019

### <a name="new"></a>Nieuw
* Bewerkingen voor blobs, ADLS Gen2 en Managed Disks gebruiken de geïntegreerde AzCopy. Meer in het bijzonder worden de volgende bewerkingen uitgevoerd met behulp van AzCopy:
   * Blobs
      * Openen voor bewerken en uploaden
      * Uploaden, inclusief slepen & neerzetten
      * Downloaden
      * & plakken #1249 kopiëren
      * Verwijderen
   * ADLS Gen2 blobs
      * Uploaden, inclusief slepen & neerzetten
      * Downloaden
      * Kopiëren & plakken
      * Verwijderen, inclusief map verwijderen
   * Managed Disks
      * Uploaden
      * Downloaden
      * Kopiëren & plakken

   Daarnaast zijn er verschillende vaak aangevraagde functies toegevoegd aan de geïntegreerde AzCopy-ervaring:
   * Conflict oplossingen: u wordt gevraagd tijdens overdracht om conflicten op te lossen. #1455
   * Uploaden als pagina-blobs: u kunt kiezen of u AzCopy uploadt. VHD-en. vhdx-bestanden als pagina-blobs. #1164 en #1601
   * Configureer bare AzCopy-para meters: er zijn diverse instellingen toegevoegd voor het afstemmen van de prestaties en het resource gebruik van AzCopy. Zie hieronder voor meer informatie.

* We hebben de volgende functies voor de ADLS Gen2 accounts toegevoegd om ADLS Gen2 en blobs toegang tot meerdere protocollen in te scha kelen en ADLS Gen2 ervaringen verder te verbeteren:
   * Zoeken met beschrijvende namen om ACL-machtigingen in te stellen
   * Verborgen containers weer geven, zoals $logs en $web
   * Container lease ophalen en verbreekt
   * #848 voor BLOB-lease ophalen en verstoren
   * Toegangs beleid voor container beheren
   * BLOB-toegangs lagen configureren
   * & plakken-blobs kopiëren

* In deze release bekijken we een voor beeld van 17 extra talen. U kunt overschakelen naar een taal van uw keuze op de pagina instellingen onder "toepassing" → "land instellingen" → "taal (preview)". We werken nog hard op het vertalen van extra teken reeksen en het verbeteren van de Vertaal kwaliteit. Als u feedback hebt over een vertaling of als u een teken reeks ziet die nog niet is omgezet, opent u [een probleem op github](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%F0%9F%8C%90%20localization&template=bug-report.md&title=).
* In elke release proberen we een paar instellingen uit te voeren om Storage Explorer in te scha kelen. In deze release hebben we instellingen toegevoegd voor het verder configureren van AzCopy en het verbergen van service knooppunten:
   * Limiet voor AzCopy-band breedte: Hiermee kunt u bepalen hoeveel netwerk AzCopy worden gebruikt. U kunt deze instelling vinden op "transfers" → "AzCopy" → "maximale overdrachts snelheid". #1099
   * AzCopy MD5-controle: Hiermee kunt u configureren of en hoe strikt AzCopy controleert op MD5-hashes bij het downloaden. U kunt deze instelling vinden op "transfers" → "AzCopy" → "MD5 controleren".
   * AzCopy-gelijktijdigheid en buffer grootte van geheugen: standaard wordt door AzCopy de computer geanalyseerd om redelijke standaard waarden voor deze instellingen te bepalen. Maar als u prestatie problemen ondervindt, kunt u deze geavanceerde instellingen gebruiken om verder te aanpassen hoe AzCopy op uw computer wordt uitgevoerd. U kunt deze instellingen vinden onder "transfers" → "AzCopy". #994
   * Service knooppunten weer geven en verbergen: deze instellingen bieden u de opties voor het weer geven of verbergen van de Azure-Services die Storage Explorer ondersteunt. U vindt deze instellingen in de sectie ' Services '. #1877

* Bij het maken van een moment opname van een beheerde schijf, wordt nu een standaard naam gegeven. #1847
* Als u koppelt aan Azure AD en u een ADLS Gen2 BLOB-container koppelt, wordt ' (ADLS Gen2) ' weer gegeven naast het knoop punt. #1861

### <a name="fixes"></a>Oplossingen
* Wanneer u grote schijven kopieert, uploadt of downloadt, kan Storage Explorer soms geen toegang intrekken voor de schijven die zijn betrokken bij de bewerking. Dit probleem is opgelost. #2048
* De tabel statistieken zijn mislukt bij het weer geven van een partitie sleutel query. Dit probleem is opgelost. #1886

### <a name="known-issues"></a>Bekende problemen
* Storage Explorer 1.11.0 hebt nu een DFS-eind punt (zoals ' myaccount.dfs.core.windows.net ') nodig om aan ADLS Gen2 containers te koppelen. In vorige versies van Storage Explorer hebt u een BLOB-eind punt kunnen gebruiken. Deze bijlagen werken mogelijk niet meer na een upgrade naar 1.11.0. Als dit probleem zich voordoet, koppelt u het opnieuw met het DFS-eind punt.
* Numerieke instellingen worden niet gecontroleerd of ze zich in een geldig bereik bevinden. #2140
* Het kopiëren van BLOB-containers van het ene opslag account naar het andere in de structuur weergave kan mislukken. Het probleem wordt onderzocht. #2124
* De instelling voor automatisch vernieuwen heeft nog geen invloed op alle bewerkingen in de BLOB Explorer.
* Functies voor beheerde schijven worden niet ondersteund in Azure Stack.
* Als het uploaden of plakken van de schijf mislukt en er een nieuwe schijf is gemaakt vóór de fout, wordt de schijf niet door Storage Explorer verwijderd.
* Afhankelijk van het annuleren van het uploaden of plakken van een schijf, is het mogelijk om de nieuwe schijf beschadigd te laten. Als dit het geval is, moet u de nieuwe schijf verwijderen of hand matig de schijf-Api's aanroepen om de inhoud van de schijf te vervangen, zodat deze niet meer beschadigd is.
* Wanneer u RBAC gebruikt, heeft Storage Explorer bepaalde beheer laag machtigingen nodig om toegang te krijgen tot uw opslag resources. Raadpleeg de [hand leiding](./storage/common/storage-explorer-troubleshooting.md) voor het oplossen van problemen voor meer informatie.
* Het ontkoppelen van een resource die is gekoppeld via SAS URI, zoals een BLOB-container, kan een fout veroorzaken die voor komt dat andere bijlagen correct worden weer gegeven. U kunt dit probleem omzeilen door het groeps knooppunt te vernieuwen. Zie #537 voor meer informatie.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, kunt u zich mogelijk niet aanmelden. U kunt het probleem omzeilen door de inhoud van ~/te verwijderen. IdentityService/AadConfigurations. Als u dit doet, neemt u een opmerking over dit probleem op.
* Azurite heeft alle opslag-Api's nog niet volledig geïmplementeerd. Daarom kunnen er onverwachte fouten of gedrag zijn bij het gebruik van Azurite voor de ontwikkelings opslag.
* In zeldzame gevallen kan de structuur focus vastlopen op snelle toegang. Als u de focus wilt ontsteken, kunt u alle vernieuwen.
* Het uploaden van uw OneDrive-map werkt niet als gevolg van een fout in NodeJS. De fout is opgelost, maar is nog niet geïntegreerd in elektron. U kunt dit probleem omzeilen bij het uploaden naar of downloaden van een BLOB-container met de experimentele AzCopy-functie.
* Als Azure Stack, kan het uploaden van bepaalde bestanden als toevoeg-blobs mislukken.
* Nadat u op Annuleren hebt geklikt, kan het even duren voordat de taak is geannuleerd. Dit komt doordat we het werkings filter voor annuleren gebruiken dat hier wordt beschreven.
* Als u het verkeerde pincode/smartcard certificaat kiest, moet u de computer opnieuw opstarten om deze beslissing te Storage Explorer vergren delen.
* Bij het wijzigen van de naam van blobs (afzonderlijk of binnen een nieuwe BLOB-container) worden geen moment opnamen bewaard. Alle andere eigenschappen en meta gegevens voor blobs, bestanden en entiteiten blijven behouden tijdens het wijzigen van de naam.
* Azure Stack biedt geen ondersteuning voor de volgende functies. Bij het werken met Azure Stack resources kunnen onverwachte fouten optreden.
   * Bestandsshares
   * Toegangslagen
   * Voorlopig verwijderen
   * ADLS Gen2
   * Managed Disks
* De elektron-Shell die door Storage Explorer wordt gebruikt, heeft moeite met het versnellen van een GPU (graphics processing unit). Als Storage Explorer een leeg (leeg) hoofd venster wordt weer gegeven, kunt u proberen om Storage Explorer te starten vanaf de opdracht regel en de GPU-versnelling uit te scha kelen door de switch toe te voegen `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor het uitvoeren van Storage Explorer op Linux moeten bepaalde afhankelijkheden eerst worden geïnstalleerd. Raadpleeg de [hand leiding voor het oplossen van problemen met](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) Storage Explorer voor meer informatie.

## <a name="previous-releases"></a>Vorige versies

* [Versie 1.10.1](#version-1101)
* [Versie 1.10.0](#version-1100)
* [Versie 1.9.0](#version-190)
* [Versie 1.8.1](#version-181)
* [Versie 1.8.0](#version-180)
* [Versie 1.7.0](#version-170)
* [Versie 1.6.2](#version-162)
* [Versie 1.6.1](#version-161)
* [Versie 1.6.0](#version-160)
* [Versie 1.5.0](#version-150)
* [Versie 1.4.4](#version-144)
* [Versie 1.4.3](#version-143)
* [Versie 1.4.2](#version-142)
* [Versie 1.4.1](#version-141)
* [Versie 1.3.0](#version-130)
* [Versie 1.2.0](#version-120)
* [Versie 1.1.0](#version-110)
* [Versie 1.0.0](#version-100)
* [Versie 0.9.6](#version-096)
* [Versie 0.9.5](#version-095)
* [Versie 0.9.4 en 0.9.3](#version-094-and-093)
* [Versie 0.9.2](#version-092)
* [Versie 0.9.1 tot en en 0.9.0](#version-091-and-090)
* [Versie 0.8.16](#version-0816)
* [Versie 0.8.14](#version-0814)
* [Versie 0.8.13](#version-0813)
* [Versie 0.8.12 en 0.8.11 en 0.8.10](#version-0812-and-0811-and-0810)
* [Versie 0.8.9 en 0.8.8](#version-089-and-088)
* [Versie 0.8.7](#version-087)
* [Versie 0.8.6](#version-086)
* [Versie 0.8.5](#version-085)
* [Versie 0.8.4](#version-084)
* [Versie 0.8.3](#version-083)
* [Versie 0.8.2](#version-082)
* [Versie 0.8.0](#version-080)
* [Versie 0.7.20160509.0](#version-07201605090)
* [Versie 0.7.20160325.0](#version-07201603250)
* [Versie 0.7.20160129.1](#version-07201601291)
* [Versie 0.7.20160105.0](#version-07201601050)
* [Versie 0.7.20151116.0](#version-07201511160)

## <a name="version-1101"></a>Versie 1.10.1
9/19/2019

### <a name="hotfix"></a>Hotfix
* Sommige gebruikers hebben een fout aangetroffen in 1.10.0 tijdens het weer geven van hun gegevens in hun ADLS gen 1-accounts. Vanwege deze fout is het deel venster Verkenner niet correct weer gegeven. Dit probleem is opgelost. #1853 #1865

### <a name="new"></a>Nieuw
* Storage Explorer heeft nu een eigen instellingen voor de gebruikers interface. U hebt toegang tot het bestand via bewerken → instellingen of door te klikken op het pictogram instellingen (het vistuig) op de verticale werk balk aan de linkerkant. Deze functie is de eerste stap die wordt gebruikt om verschillende door de [gebruiker aangevraagde instellingen](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate)te bieden. Vanaf deze release worden de volgende instellingen ondersteund:
  * Thema
  * Proxy
  * Afmelden bij afsluiten #6
  * Aanmelden met Device code flow inschakelen
  * #1526 automatisch vernieuwen
  * AzCopy inschakelen
  * AzCopy SAS-duur als er andere instellingen zijn die u wilt toevoegen, opent u [een probleem op github](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=) met een beschrijving van de instelling die u wilt weer geven.
* Storage Explorer ondersteunt nu Managed Disks. U kunt het volgende doen:
  * Een on-premises VHD uploaden naar een nieuwe schijf
  * Een schijf downloaden
  * Schijven kopiëren en plakken in resource groepen en regio's
  * Schijven verwijderen
  * Een moment opname van een schijf maken

Het uploaden, downloaden en cross-Region kopiëren van schijven wordt mogelijk gemaakt door AzCopy V10 toevoegen.
* Storage Explorer kan nu worden geïnstalleerd via de module archief op Linux. Wanneer u via de snap Store installeert, worden alle afhankelijkheden voor u geïnstalleerd, inclusief .NET core! Op dit moment hebben we gecontroleerd of Storage Explorer goed werkt op Ubuntu en CentOS. Als u problemen ondervindt tijdens de installatie vanuit de snap Store op andere Linux distributies, [opent u een probleem op github](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Zie de [aan de slag-hand leiding](./vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=linux)voor meer informatie over het installeren vanuit de snap Store. #68
* Er zijn twee belang rijke wijzigingen aangebracht in de Azure Active Directory (Azure AD) die zijn bedoeld om de functie nuttiger te maken voor ADLS Gen2 gebruikers:
  * U selecteert nu de Tenant waaraan de resource die u wilt koppelen, zich bevindt. Dit betekent dat u geen RBAC-toegang meer nodig hebt tot het abonnement van de resource.
  * Als u een ADLS Gen2 BLOB-container koppelt, kunt u nu koppelen aan een specifiek pad in de container.
* Wanneer u Acl's voor ADLS Gen2-bestanden en-mappen beheert, worden in Storage Explorer nu de beschrijvende namen voor entiteiten in de ACL weer gegeven. #957
* Wanneer u via OID aan een ADLS Gen2-ACL toevoegt, wordt door Storage Explorer nu gevalideerd of de OID deel uitmaakt van een geldige entiteit in uw Tenant. #1603
* De sneltoetsen voor het navigeren tussen tabbladen gebruiken nu meer standaard toetsen combinaties. #1018
* Wanneer u op een tabblad klikt, wordt dit nu gesloten. #1348
* Als een AzCopy-overdracht geen fouten bevat, wordt er in Storage Explorer nu een waarschuwings pictogram weer gegeven om te markeren dat er overs laan is opgetreden. #1490
* De geïntegreerde AzCopy is bijgewerkt naar versie 10.2.1. Daarnaast kunt u nu de versie van AzCopy weer geven die is geïnstalleerd in het dialoog venster Info. #1343

### <a name="fixes"></a>Oplossingen
* Veel gebruikers hebben uitgevoerd in verschillende ' kan de versie niet lezen van niet-gedefinieerd ' of ' kan geen verbinding met niet-gedefinieerde ' fouten lezen bij het werken met gekoppelde opslag accounts. Hoewel we nog steeds verder gaan met het onderzoeken van de hoofd oorzaak van dit probleem, hebben we in 1.10.0 de fout afhandeling verbeterd rondom het laden van gekoppelde opslag accounts. #1626, #985 en #1532
* Het was mogelijk dat de Verkenner-structuur (aan de linkerkant) een status krijgt waarin de focus herhaaldelijk naar het bovenste knoop punt springt. Dit probleem is opgelost. #1596
* Wanneer u de moment opnamen van een BLOB beheert, lezen scherm lezers de tijds tempel die is gekoppeld aan de moment opname niet. Dit probleem is opgelost. #1202
* De proxy-instelling voor macOS is niet ingesteld op het moment dat het verificatie proces ze gebruikt. Dit probleem is opgelost. #1567
* Als een opslag account in een soevereine Cloud is gekoppeld met behulp van de naam en sleutel, werkt AzCopy niet. Dit probleem is opgelost. #1544
* Wanneer u verbinding maakt via een connection string, verwijdert Storage Explorer nu de Volg spaties. #1387

### <a name="known-issues"></a>Bekende problemen
* De instelling voor automatisch vernieuwen heeft nog geen invloed op alle bewerkingen in de BLOB Explorer.
* Functies voor beheerde schijven worden niet ondersteund in Azure Stack.
* Als het uploaden of plakken van de schijf mislukt en er een nieuwe schijf is gemaakt vóór de fout, wordt de schijf niet door Storage Explorer verwijderd.
* Afhankelijk van het annuleren van het uploaden of plakken van een schijf, is het mogelijk om de nieuwe schijf beschadigd te laten. Als dit het geval is, moet u de nieuwe schijf verwijderen of hand matig de schijf-Api's aanroepen om de inhoud van de schijf te vervangen, zodat deze niet meer beschadigd is.
* Afhankelijk van het annuleren van het uploaden of plakken van een schijf, is het mogelijk om de nieuwe schijf beschadigd te laten. Als dit het geval is, moet u de nieuwe schijf verwijderen of hand matig de schijf-Api's aanroepen om de inhoud van de schijf te vervangen, zodat deze niet meer beschadigd is.
* Wanneer u een niet-AzCopy-BLOB downloadt, wordt de MD5 voor grote bestanden niet gecontroleerd. Dit wordt veroorzaakt door een fout in de opslag-SDK. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Wanneer u RBAC gebruikt, heeft Storage Explorer bepaalde beheer laag machtigingen nodig om toegang te krijgen tot uw opslag resources. Raadpleeg de [hand leiding](./storage/common/storage-explorer-troubleshooting.md) voor het oplossen van problemen voor meer informatie.
* Het ontkoppelen van een resource die is gekoppeld via SAS URI, zoals een BLOB-container, kan een fout veroorzaken die voor komt dat andere bijlagen correct worden weer gegeven. U kunt dit probleem omzeilen door het groeps knooppunt te vernieuwen. Zie #537 voor meer informatie.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, kunt u zich mogelijk niet aanmelden. U kunt het probleem omzeilen door de inhoud van ~/te verwijderen. IdentityService/AadConfigurations. Als u dit doet, neemt u een opmerking over dit probleem op.
* Azurite heeft alle opslag-Api's nog niet volledig geïmplementeerd. Daarom kunnen er onverwachte fouten of gedrag zijn bij het gebruik van Azurite voor de ontwikkelings opslag.
* In zeldzame gevallen kan de structuur focus vastlopen op snelle toegang. Als u de focus wilt ontsteken, kunt u alle vernieuwen.
* Het uploaden van uw OneDrive-map werkt niet als gevolg van een fout in NodeJS. De fout is opgelost, maar is nog niet geïntegreerd in elektron. U kunt dit probleem omzeilen bij het uploaden naar of downloaden van een BLOB-container met de experimentele AzCopy-functie.
* Als Azure Stack, kan het uploaden van bepaalde bestanden als toevoeg-blobs mislukken.
* Nadat u op Annuleren hebt geklikt, kan het even duren voordat de taak is geannuleerd. Dit komt doordat we het werkings filter voor annuleren gebruiken dat hier wordt beschreven.
* Als u het verkeerde pincode/smartcard certificaat kiest, moet u de computer opnieuw opstarten om deze beslissing te Storage Explorer vergren delen.
* Bij het wijzigen van de naam van blobs (afzonderlijk of binnen een nieuwe BLOB-container) worden geen moment opnamen bewaard. Alle andere eigenschappen en meta gegevens voor blobs, bestanden en entiteiten blijven behouden tijdens het wijzigen van de naam.
* Azure Stack biedt geen ondersteuning voor de volgende functies. Bij het werken met Azure Stack resources kunnen onverwachte fouten optreden.
   * Bestandsshares
   * Toegangslagen
   * Voorlopig verwijderen
   * ADLS Gen2
   * Managed Disks
* De elektron-Shell die door Storage Explorer wordt gebruikt, heeft moeite met het versnellen van een GPU (graphics processing unit). Als Storage Explorer een leeg (leeg) hoofd venster wordt weer gegeven, kunt u proberen om Storage Explorer te starten vanaf de opdracht regel en de GPU-versnelling uit te scha kelen door de switch toe te voegen `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor het uitvoeren van Storage Explorer op Linux moeten bepaalde afhankelijkheden eerst worden geïnstalleerd. Raadpleeg de [hand leiding voor het oplossen van problemen met](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) Storage Explorer voor meer informatie.


## <a name="version-1100"></a>Versie 1.10.0
9/12/2019

### <a name="new"></a>Nieuw

* Storage Explorer heeft nu een eigen instellingen voor de gebruikers interface. U hebt toegang tot het bestand via bewerken → instellingen of door te klikken op het pictogram instellingen (het vistuig) op de verticale werk balk aan de linkerkant. Deze functie is de eerste stap die wordt gebruikt om verschillende door de [gebruiker aangevraagde instellingen](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate)te bieden. Vanaf deze release worden de volgende instellingen ondersteund:
    * Thema
    * Proxy
    * Afmelden bij afsluiten [#6](https://www.github.com/Microsoft/AzureStorageExplorer/issues/6)
    * Aanmelden met Device code flow inschakelen
    * [#1526](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1526) automatisch vernieuwen
    * AzCopy inschakelen
    * SAS-duur AzCopy

    Als er andere instellingen zijn die u wilt toevoegen, [opent u een probleem op github met een beschrijving van de instelling die u wilt zien](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=).
* Storage Explorer ondersteunt nu Managed Disks. U kunt het volgende doen:
    * Een on-premises VHD uploaden naar een nieuwe schijf
    * Een schijf downloaden
    * Schijven kopiëren en plakken in resource groepen en regio's
    * Schijven verwijderen
    * Een moment opname van een schijf maken

    Het uploaden, downloaden en cross-Region kopiëren van schijven wordt mogelijk gemaakt door AzCopy V10 toevoegen.
* Storage Explorer kan nu worden geïnstalleerd via de module archief op Linux. Wanneer u via de snap Store installeert, worden alle afhankelijkheden voor u geïnstalleerd, inclusief .NET core! Op dit moment hebben we gecontroleerd of Storage Explorer goed werkt op Ubuntu en CentOS. Als u problemen ondervindt tijdens de installatie vanuit de snap Store op andere Linux distributies, [opent u een probleem op github](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Zie de [aan de slag-hand leiding](./vs-azure-tools-storage-manage-with-storage-explorer.md)voor meer informatie over het installeren vanuit de snap Store. [#68](https://www.github.com/Microsoft/AzureStorageExplorer/issues/68)
* Er zijn twee belang rijke wijzigingen aangebracht in de Azure Active Directory (Azure AD) die zijn bedoeld om de functie nuttiger te maken voor ADLS Gen2 gebruikers: * u selecteert nu de Tenant waaraan de resource die u wilt koppelen, zich bevindt. Dit betekent dat u geen RBAC-toegang meer nodig hebt tot het abonnement van de resource.
        * Als u een ADLS Gen2 BLOB-container koppelt, kunt u nu koppelen aan een specifiek pad in de container.
* Wanneer u Acl's voor ADLS Gen2-bestanden en-mappen beheert, worden in Storage Explorer nu de beschrijvende namen voor entiteiten in de ACL weer gegeven. [#957](https://www.github.com/Microsoft/AzureStorageExplorer/issues/957)
* Wanneer u via OID aan een ADLS Gen2-ACL toevoegt, wordt door Storage Explorer nu gevalideerd of de OID deel uitmaakt van een geldige entiteit in uw Tenant. [#1603](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1603)
* De sneltoetsen voor het navigeren tussen tabbladen gebruiken nu meer standaard toetsen combinaties. [#1018](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1018)
* Wanneer u op een tabblad klikt, wordt dit nu gesloten. [#1348](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1348)
* Als een AzCopy-overdracht geen fouten bevat, wordt er in Storage Explorer nu een waarschuwings pictogram weer gegeven om te markeren dat er overs laan is opgetreden. [#1490](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1490)
* De geïntegreerde AzCopy is bijgewerkt naar versie 10.2.1. Daarnaast kunt u nu de versie van AzCopy weer geven die is geïnstalleerd in het dialoog venster Info. [#1343](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1343)

### <a name="fixes"></a>Oplossingen

* Veel gebruikers hebben uitgevoerd in verschillende ' kan de versie niet lezen van niet-gedefinieerd ' of ' kan geen verbinding met niet-gedefinieerde ' fouten lezen bij het werken met gekoppelde opslag accounts. Hoewel we nog steeds verder gaan met het onderzoeken van de hoofd oorzaak van dit probleem, hebben we in 1.10.0 de fout afhandeling verbeterd rondom het laden van gekoppelde opslag accounts. [#1626](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1626), [#985](https://www.github.com/Microsoft/AzureStorageExplorer/issues/985)en [#1532](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1532)
* Het was mogelijk dat de Verkenner-structuur (aan de linkerkant) een status krijgt waarin de focus herhaaldelijk naar het bovenste knoop punt springt. Dit probleem is opgelost. [#1596](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1596)
* Wanneer u de moment opnamen van een BLOB beheert, lezen scherm lezers de tijds tempel die is gekoppeld aan de moment opname niet. Dit probleem is opgelost. [#1202](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1202)
* De proxy-instelling voor macOS is niet ingesteld op het moment dat het verificatie proces ze gebruikt. Dit probleem is opgelost. [#1567](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1567)
* Als een opslag account in een soevereine Cloud is gekoppeld met behulp van de naam en sleutel, werkt AzCopy niet. Dit probleem is opgelost. [#1544](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1544)
* Wanneer u verbinding maakt via een connection string, verwijdert Storage Explorer nu de Volg spaties. [#1387](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1387)

### <a name="known-issues"></a>Bekende problemen

* De instelling voor automatisch vernieuwen heeft nog geen invloed op alle bewerkingen in de BLOB Explorer.
* Functies voor beheerde schijven worden niet ondersteund in Azure Stack.
* Als het uploaden of plakken van de schijf mislukt en er een nieuwe schijf is gemaakt vóór de fout, wordt de schijf niet door Storage Explorer verwijderd.
* Afhankelijk van het annuleren van het uploaden of plakken van een schijf, is het mogelijk om de nieuwe schijf beschadigd te laten. Als dit het geval is, moet u de nieuwe schijf verwijderen of hand matig de schijf-Api's aanroepen om de inhoud van de schijf te vervangen, zodat deze niet meer beschadigd is.
* Wanneer u een niet-AzCopy-BLOB downloadt, wordt de MD5 voor grote bestanden niet gecontroleerd. Dit wordt veroorzaakt door een fout in de opslag-SDK. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Wanneer u RBAC gebruikt, heeft Storage Explorer bepaalde beheer laag machtigingen nodig om toegang te krijgen tot uw opslag resources. Raadpleeg de [hand leiding](./storage/common/storage-explorer-troubleshooting.md) voor het oplossen van problemen voor meer informatie.
* Het ontkoppelen van een resource die is gekoppeld via SAS URI, zoals een BLOB-container, kan een fout veroorzaken die voor komt dat andere bijlagen correct worden weer gegeven. U kunt dit probleem omzeilen door het groeps knooppunt te vernieuwen. Zie #537 voor meer informatie.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, kunt u zich mogelijk niet aanmelden. U kunt het probleem omzeilen door de inhoud van ~/te verwijderen. IdentityService/AadConfigurations. Als u dit doet, neemt u een opmerking over dit probleem op.
* Azurite heeft alle opslag-Api's nog niet volledig geïmplementeerd. Daarom kunnen er onverwachte fouten of gedrag zijn bij het gebruik van Azurite voor de ontwikkelings opslag.
* In zeldzame gevallen kan de structuur focus vastlopen op snelle toegang. Als u de focus wilt ontsteken, kunt u alle vernieuwen.
* Het uploaden van uw OneDrive-map werkt niet als gevolg van een fout in NodeJS. De fout is opgelost, maar is nog niet geïntegreerd in elektron. U kunt dit probleem omzeilen bij het uploaden naar of downloaden van een BLOB-container met de experimentele AzCopy-functie.
* Als Azure Stack, kan het uploaden van bepaalde bestanden als toevoeg-blobs mislukken.
* Nadat u op Annuleren hebt geklikt, kan het even duren voordat de taak is geannuleerd. Dit komt doordat we het werkings filter voor annuleren gebruiken dat hier wordt beschreven.
* Als u het verkeerde pincode/smartcard certificaat kiest, moet u de computer opnieuw opstarten om deze beslissing te Storage Explorer vergren delen.
* Bij het wijzigen van de naam van blobs (afzonderlijk of binnen een nieuwe BLOB-container) worden geen moment opnamen bewaard. Alle andere eigenschappen en meta gegevens voor blobs, bestanden en entiteiten blijven behouden tijdens het wijzigen van de naam.
* Azure Stack biedt geen ondersteuning voor de volgende functies. Bij het werken met Azure Stack resources kunnen onverwachte fouten optreden.
   * Bestandsshares
   * Toegangslagen
   * Voorlopig verwijderen
   * ADLS Gen2
   * Managed Disks
* De elektron-Shell die door Storage Explorer wordt gebruikt, heeft moeite met het versnellen van een GPU (graphics processing unit). Als Storage Explorer een leeg (leeg) hoofd venster wordt weer gegeven, kunt u proberen om Storage Explorer te starten vanaf de opdracht regel en de GPU-versnelling uit te scha kelen door de switch toe te voegen `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor het uitvoeren van Storage Explorer op Linux moeten bepaalde afhankelijkheden eerst worden geïnstalleerd. Raadpleeg de [hand leiding voor het oplossen van problemen met](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) Storage Explorer voor meer informatie.

## <a name="version-190"></a>Versie 1.9.0
7/1/2019

### <a name="download-azure-storage-explorer-190"></a>Azure Storage Explorer 1.9.0 downloaden
- [Azure Storage Explorer 1.9.0 voor Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Azure Storage Explorer 1.9.0 voor Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Azure Storage Explorer 1.9.0 voor Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Nieuw

* U kunt nu BLOB-containers koppelen via Azure AD (RBAC-of ACL-machtigingen). Deze functie is bedoeld voor gebruikers die toegang hebben tot containers, maar niet de opslag accounts waarin de containers zich bevinden. Raadpleeg de introductie handleiding voor meer informatie over deze functie.
* Verwervings-en verbreekt lease nu met RBAC. [#1354](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1354)
* Het beheren van toegangs beleid en het instellen van open bare toegangs niveau werken nu met RBAC. [#1355](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1355)
* Het verwijderen van BLOB-mappen werkt nu met RBAC. [#1450](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1450)
* Het wijzigen van de BLOB-toegangs laag werkt nu met RBAC. [#1446](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1446)
* U kunt de snelle toegang nu snel opnieuw instellen via "Help" → "opnieuw instellen". [#1327](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1327)

### <a name="preview-features"></a>Preview-functies

* U kunt zich nu aanmelden met een apparaatcode stroom. Als u deze functie wilt inschakelen, gaat u naar ' Preview "→ ' apparaat code flow-aanmelding gebruiken '. We raden aan dat gebruikers problemen ondervinden met lege aanmeld Vensters om deze functie uit te proberen, omdat dit een betrouwbaardere vorm van aanmelding kan zijn.
* Storage Explorer geïntegreerd met AzCopy is momenteel beschikbaar als preview-versie. Als u dit wilt inschakelen, gaat u naar ' Preview "→" AzCopy gebruiken voor verbeterde BLOB upload en down load ". BLOB-overdrachten die zijn voltooid met AzCopy moeten sneller en beter worden uitgevoerd.

### <a name="fixes"></a>Oplossingen

* Er kunnen niet meer dan 50 abonnementen voor één account worden geladen. [#1416](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1416)
* De knop ' Aanmelden ' is niet actief op de Info balk die wordt weer gegeven wanneer een directe koppeling mislukt. [#1358](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1358)
* Er worden geen app-bestanden geüpload naar macOS. [#1119](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1119)
* ' Alles opnieuw proberen ' is niet actief voor het wijzigen van de naam van een mislukte blob. [#992](https://www.github.com/Microsoft/AzureStorageExplorer/issues/992)
* ' Annuleren ' is niet actief tijdens het openen van een blob. [#1464](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1464)
* Er zijn meerdere spel-en knop info-problemen in het product opgelost. Veel dank voor alle personen die deze problemen hebben gerapporteerd! [#1303](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1303), [#1328](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1328), [#1329](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1329), [#1331](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1331), [#1336](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1336), [#1352](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1352), [#1368](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1368), [#1395](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1395)

### <a name="known-issues"></a>Bekende problemen

* Wanneer u een niet-AzCopy-BLOB downloadt, wordt de MD5 voor grote bestanden niet gecontroleerd. Dit wordt veroorzaakt door een fout in de opslag-SDK. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Wanneer u RBAC gebruikt, heeft Storage Explorer bepaalde beheer laag machtigingen nodig om toegang te krijgen tot uw opslag resources. Raadpleeg de [hand leiding](./storage/common/storage-explorer-troubleshooting.md) voor het oplossen van problemen voor meer informatie.
* Bij een poging om toegang te krijgen tot ADLS Gen2 blobs na een proxy kan mislukken.
* Het ontkoppelen van een resource die is gekoppeld via SAS URI, zoals een BLOB-container, kan een fout veroorzaken die voor komt dat andere bijlagen correct worden weer gegeven. U kunt dit probleem omzeilen door het groeps knooppunt te vernieuwen. Zie #537 voor meer informatie.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, kunt u zich mogelijk niet aanmelden. U kunt het probleem omzeilen door de inhoud van ~/te verwijderen. IdentityService/AadConfigurations. Als u dit doet, neemt u een opmerking over dit probleem op.
* Azurite heeft alle opslag-Api's nog niet volledig geïmplementeerd. Daarom kunnen er onverwachte fouten of gedrag zijn bij het gebruik van Azurite voor de ontwikkelings opslag.
* In zeldzame gevallen kan de structuur focus vastlopen op snelle toegang. Als u de focus wilt ontsteken, kunt u alle vernieuwen.
* Het uploaden van uw OneDrive-map werkt niet als gevolg van een fout in NodeJS. De fout is opgelost, maar is nog niet geïntegreerd in elektron. U kunt dit probleem omzeilen bij het uploaden naar of downloaden van een BLOB-container met de experimentele AzCopy-functie.
* Als Azure Stack, kan het uploaden van bepaalde bestanden als toevoeg-blobs mislukken.
* Nadat u op Annuleren hebt geklikt, kan het even duren voordat de taak is geannuleerd. Dit komt doordat we het werkings filter voor annuleren gebruiken dat hier wordt beschreven.
* Als u het verkeerde pincode/smartcard certificaat kiest, moet u de computer opnieuw opstarten om deze beslissing te Storage Explorer vergren delen.
* Bij het wijzigen van de naam van blobs (afzonderlijk of binnen een nieuwe BLOB-container) worden geen moment opnamen bewaard. Alle andere eigenschappen en meta gegevens voor blobs, bestanden en entiteiten blijven behouden tijdens het wijzigen van de naam.
* Azure Stack biedt geen ondersteuning voor de volgende functies. Bij het werken met Azure Stack resources kunnen onverwachte fouten optreden.
   * Bestandsshares
   * Toegangslagen
   * Voorlopig verwijderen
   * ADLS Gen2
* De elektron-Shell die door Storage Explorer wordt gebruikt, heeft moeite met het versnellen van een GPU (graphics processing unit). Als Storage Explorer een leeg (leeg) hoofd venster wordt weer gegeven, kunt u proberen om Storage Explorer te starten vanaf de opdracht regel en de GPU-versnelling uit te scha kelen door de switch toe te voegen `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor het uitvoeren van Storage Explorer op Linux moeten bepaalde afhankelijkheden eerst worden geïnstalleerd. Raadpleeg de [hand leiding voor het oplossen van problemen met](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) Storage Explorer voor meer informatie.

## <a name="version-181"></a>Versie 1.8.1
5/13/2019

### <a name="hotfixes"></a>Hotfixes
* In sommige gevallen wordt de volgende pagina van resources niet weer gegeven als u op resource niveau op ' meer laden ' klikt. Dit probleem is opgelost. [#1359](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1359)
* In Windows zouden AzCopy-down loads mislukken als één bestand of map werd gedownload en de naam van het bestand of de map een teken had dat ongeldig was voor een Windows-pad. Dit probleem is opgelost. [#1350](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1350)
* In extreem zeldzame gevallen moet u tijdens het uitvoeren van een bestands share of een naam wijziging in een bestands share, als de kopieën voor de naamswijziging zijn mislukt, of als opslag verkennen het succes van de kopieën met Azure niet kan bevestigen, is het mogelijk dat Storage Explorer de oorspronkelijke bestanden te verwijderen voordat de kopie is voltooid. Dit probleem is opgelost.

### <a name="new"></a>Nieuw

* De geïntegreerde AzCopy-versie is bijgewerkt naar versie 10.1.0.
* Ctrl/Cmd + R kan nu worden gebruikt om de huidige focus editor te vernieuwen. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* De versie van de Azure Stack Storage-API is gewijzigd in 2017-04-17.
* In het dialoog venster toegang beheren voor ADLS Gen2 wordt het masker nu op dezelfde manier gesynchroniseerd als andere hulpprogram ma's voor POSIX-machtigingen. De gebruikers interface waarschuwt u ook als er een wijziging wordt aangebracht, waardoor de machtigingen van een gebruiker of groep de grenzen van het masker overschrijden. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* Voor AzCopy uploads is de vlag voor het berekenen en instellen van de MD5-hash nu ingeschakeld. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Preview-functies

* U kunt zich nu aanmelden met een apparaatcode stroom. Als u deze functie wilt inschakelen, gaat u naar ' Preview "→ ' apparaat code flow-aanmelding gebruiken '. We raden aan dat gebruikers problemen ondervinden met lege aanmeld Vensters om deze functie uit te proberen, omdat dit een betrouwbaardere vorm van aanmelding kan zijn.
* Storage Explorer geïntegreerd met AzCopy is momenteel beschikbaar als preview-versie. Als u dit wilt inschakelen, gaat u naar ' Preview "→" AzCopy gebruiken voor verbeterde BLOB upload en down load ". BLOB-overdrachten die zijn voltooid met AzCopy moeten sneller en beter worden uitgevoerd.

### <a name="fixes"></a>Oplossingen

* Het dialoog venster toegangs beleid heeft geen verval datum meer ingesteld voor het toegangs beleid voor opslag dat geen verloop tijd heeft. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Er zijn enkele wijzigingen aangebracht in het dialoog venster SAS genereren om er zeker van te zijn dat opgeslagen toegangs beleid correct wordt gebruikt bij het genereren van een SAS. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* Wanneer u probeert een niet-512 byte uitgelijnd bestand te uploaden naar een pagina-blob, wordt in Storage Explorer nu een meer relevante fout weer gegeven. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* Het kopiëren van een BLOB-container die een weergave naam heeft gebruikt, mislukt. Nu wordt de daad werkelijke naam van de BLOB-container gebruikt. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Als u probeert bepaalde acties uit te voeren op een ADLS Gen2 map met de naam Unicode-tekens, mislukt dit. Alle acties moeten nu werken. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Bekende problemen

* Wanneer u een niet-AzCopy-BLOB downloadt, wordt de MD5 voor grote bestanden niet gecontroleerd. Dit wordt veroorzaakt door een fout in de opslag-SDK. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Wanneer u RBAC gebruikt, heeft Storage Explorer bepaalde beheer laag machtigingen nodig om toegang te krijgen tot uw opslag resources. Raadpleeg de [hand leiding](./storage/common/storage-explorer-troubleshooting.md) voor het oplossen van problemen voor meer informatie.
* Bij een poging om toegang te krijgen tot ADLS Gen2 blobs na een proxy kan mislukken.
* Het ontkoppelen van een resource die is gekoppeld via SAS URI, zoals een BLOB-container, kan een fout veroorzaken die voor komt dat andere bijlagen correct worden weer gegeven. U kunt dit probleem omzeilen door het groeps knooppunt te vernieuwen. Zie #537 voor meer informatie.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, kunt u zich mogelijk niet aanmelden. U kunt het probleem omzeilen door de inhoud van ~/te verwijderen. IdentityService/AadConfigurations. Als u dit doet, neemt u een opmerking over dit probleem op.
* Azurite heeft alle opslag-Api's nog niet volledig geïmplementeerd. Daarom kunnen er onverwachte fouten of gedrag zijn bij het gebruik van Azurite voor de ontwikkelings opslag.
* In zeldzame gevallen kan de structuur focus vastlopen op snelle toegang. Als u de focus wilt ontsteken, kunt u alle vernieuwen.
* Het uploaden van uw OneDrive-map werkt niet als gevolg van een fout in NodeJS. De fout is opgelost, maar is nog niet geïntegreerd in elektron. U kunt dit probleem omzeilen bij het uploaden naar of downloaden van een BLOB-container met de experimentele AzCopy-functie.
* Als Azure Stack, kan het uploaden van bepaalde bestanden als toevoeg-blobs mislukken.
* Nadat u op Annuleren hebt geklikt, kan het even duren voordat de taak is geannuleerd. Dit komt doordat we het werkings filter voor annuleren gebruiken dat hier wordt beschreven.
* Als u het verkeerde pincode/smartcard certificaat kiest, moet u de computer opnieuw opstarten om deze beslissing te Storage Explorer vergren delen.
* Bij het wijzigen van de naam van blobs (afzonderlijk of binnen een nieuwe BLOB-container) worden geen moment opnamen bewaard. Alle andere eigenschappen en meta gegevens voor blobs, bestanden en entiteiten blijven behouden tijdens het wijzigen van de naam.
* Azure Stack biedt geen ondersteuning voor de volgende functies. Bij het werken met Azure Stack resources kunnen onverwachte fouten optreden.
   * Bestandsshares
   * Toegangslagen
   * Voorlopig verwijderen
   * ADLS Gen2
* De elektron-Shell die door Storage Explorer wordt gebruikt, heeft moeite met het versnellen van een GPU (graphics processing unit). Als Storage Explorer een leeg (leeg) hoofd venster wordt weer gegeven, kunt u proberen om Storage Explorer te starten vanaf de opdracht regel en de GPU-versnelling uit te scha kelen door de switch toe te voegen `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor het uitvoeren van Storage Explorer op Linux moeten bepaalde afhankelijkheden eerst worden geïnstalleerd. Raadpleeg de [hand leiding voor het oplossen van problemen met](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) Storage Explorer voor meer informatie.

## <a name="version-180"></a>Versie 1.8.0
1/5/2019

### <a name="new"></a>Nieuw

* De geïntegreerde AzCopy-versie is bijgewerkt naar versie 10.1.0.
* Ctrl/Cmd + R kan nu worden gebruikt om de huidige focus editor te vernieuwen. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* De versie van de Azure Stack Storage-API is gewijzigd in 2017-04-17.
* In het dialoog venster toegang beheren voor ADLS Gen2 wordt het masker nu op dezelfde manier gesynchroniseerd als andere hulpprogram ma's voor POSIX-machtigingen. De gebruikers interface waarschuwt u ook als er een wijziging wordt aangebracht, waardoor de machtigingen van een gebruiker of groep de grenzen van het masker overschrijden. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* Voor AzCopy uploads is de vlag voor het berekenen en instellen van de MD5-hash nu ingeschakeld. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Preview-functies

* U kunt zich nu aanmelden met een apparaatcode stroom. Als u deze functie wilt inschakelen, gaat u naar ' Preview "→ ' apparaat code flow-aanmelding gebruiken '. We raden aan dat gebruikers problemen ondervinden met lege aanmeld Vensters om deze functie uit te proberen, omdat dit een betrouwbaardere vorm van aanmelding kan zijn.
* Storage Explorer geïntegreerd met AzCopy is momenteel beschikbaar als preview-versie. Als u dit wilt inschakelen, gaat u naar ' Preview "→" AzCopy gebruiken voor verbeterde BLOB upload en down load ". BLOB-overdrachten die zijn voltooid met AzCopy moeten sneller en beter worden uitgevoerd.

### <a name="fixes"></a>Oplossingen

* Het dialoog venster toegangs beleid heeft geen verval datum meer ingesteld voor het toegangs beleid voor opslag dat geen verloop tijd heeft. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Er zijn enkele wijzigingen aangebracht in het dialoog venster SAS genereren om er zeker van te zijn dat opgeslagen toegangs beleid correct wordt gebruikt bij het genereren van een SAS. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* Wanneer u probeert een niet-512 byte uitgelijnd bestand te uploaden naar een pagina-blob, wordt in Storage Explorer nu een meer relevante fout weer gegeven. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* Het kopiëren van een BLOB-container die een weergave naam heeft gebruikt, mislukt. Nu wordt de daad werkelijke naam van de BLOB-container gebruikt. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Als u probeert bepaalde acties uit te voeren op een ADLS Gen2 map met de naam Unicode-tekens, mislukt dit. Alle acties moeten nu werken. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Bekende problemen

* Wanneer u een niet-AzCopy-BLOB downloadt, wordt de MD5 voor grote bestanden niet gecontroleerd. Dit wordt veroorzaakt door een fout in de opslag-SDK. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Wanneer u RBAC gebruikt, heeft Storage Explorer bepaalde beheer laag machtigingen nodig om toegang te krijgen tot uw opslag resources. Raadpleeg de [hand leiding](./storage/common/storage-explorer-troubleshooting.md) voor het oplossen van problemen voor meer informatie.
* Bij een poging om toegang te krijgen tot ADLS Gen2 blobs na een proxy kan mislukken.
* Het ontkoppelen van een resource die is gekoppeld via SAS URI, zoals een BLOB-container, kan een fout veroorzaken die voor komt dat andere bijlagen correct worden weer gegeven. U kunt dit probleem omzeilen door het groeps knooppunt te vernieuwen. Zie #537 voor meer informatie.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, kunt u zich mogelijk niet aanmelden. U kunt het probleem omzeilen door de inhoud van ~/te verwijderen. IdentityService/AadConfigurations. Als u dit doet, neemt u een opmerking over dit probleem op.
* Azurite heeft alle opslag-Api's nog niet volledig geïmplementeerd. Daarom kunnen er onverwachte fouten of gedrag zijn bij het gebruik van Azurite voor de ontwikkelings opslag.
* In zeldzame gevallen kan de structuur focus vastlopen op snelle toegang. Als u de focus wilt ontsteken, kunt u alle vernieuwen.
* Het uploaden van uw OneDrive-map werkt niet als gevolg van een fout in NodeJS. De fout is opgelost, maar is nog niet geïntegreerd in elektron. U kunt dit probleem omzeilen bij het uploaden naar of downloaden van een BLOB-container met de experimentele AzCopy-functie.
* Als Azure Stack, kan het uploaden van bepaalde bestanden als toevoeg-blobs mislukken.
* Nadat u op Annuleren hebt geklikt, kan het even duren voordat de taak is geannuleerd. Dit komt doordat we het werkings filter voor annuleren gebruiken dat hier wordt beschreven.
* Als u het verkeerde pincode/smartcard certificaat kiest, moet u de computer opnieuw opstarten om deze beslissing te Storage Explorer vergren delen.
* Bij het wijzigen van de naam van blobs (afzonderlijk of binnen een nieuwe BLOB-container) worden geen moment opnamen bewaard. Alle andere eigenschappen en meta gegevens voor blobs, bestanden en entiteiten blijven behouden tijdens het wijzigen van de naam.
* Azure Stack biedt geen ondersteuning voor de volgende functies. Bij het werken met Azure Stack resources kunnen onverwachte fouten optreden.
   * Bestandsshares
   * Toegangslagen
   * Voorlopig verwijderen
   * ADLS Gen2
* De elektron-Shell die door Storage Explorer wordt gebruikt, heeft moeite met het versnellen van een GPU (graphics processing unit). Als Storage Explorer een leeg (leeg) hoofd venster wordt weer gegeven, kunt u proberen om Storage Explorer te starten vanaf de opdracht regel en de GPU-versnelling uit te scha kelen door de switch toe te voegen `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor het uitvoeren van Storage Explorer op Linux moeten bepaalde afhankelijkheden eerst worden geïnstalleerd. Raadpleeg de [hand leiding voor het oplossen van problemen met](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) Storage Explorer voor meer informatie.

## <a name="version-170"></a>Versie 1.7.0
3/5/2019

### <a name="download-azure-storage-explorer-170"></a>Azure Storage Explorer 1.7.0 downloaden
- [Azure Storage Explorer 1.7.0 voor Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Azure Storage Explorer 1.7.0 voor Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Azure Storage Explorer 1.7.0 voor Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Nieuw

* U kunt nu de eigenaar en de groep die eigenaar is wijzigen voor het beheren van de toegang voor een ADLS Gen2-container,-bestand of-map.
* In Windows is het bijwerken van Storage Explorer van binnen het product nu een incrementele installatie. Dit moet resulteren in een snellere update-ervaring. Als u liever een schone installatie wilt, kunt u het [installatie programma](https://azure.microsoft.com/features/storage-explorer/) zelf downloaden en vervolgens hand matig installeren. #1089

### <a name="preview-features"></a>Preview-functies

* U kunt zich nu aanmelden met een apparaatcode stroom. Als u deze functie wilt inschakelen, gaat u naar ' Preview "→ ' apparaat code flow-aanmelding gebruiken '. We raden aan dat gebruikers problemen ondervinden met lege aanmeld Vensters om deze functie uit te proberen, omdat dit een betrouwbaardere vorm van aanmelding kan zijn. #938
* Storage Explorer geïntegreerd met AzCopy is momenteel beschikbaar als preview-versie. Als u dit wilt inschakelen, gaat u naar ' Preview "→" AzCopy gebruiken voor verbeterde BLOB upload en down load ". BLOB-overdrachten die zijn voltooid met AzCopy moeten sneller en beter worden uitgevoerd.

### <a name="fixes"></a>Oplossingen

* U kunt nu het BLOB-type kiezen dat u wilt uploaden als AzCopy is ingeschakeld. #1111
* Als u voorheen statische websites voor een ADLS Gen2-opslag account hebt ingeschakeld en deze vervolgens aan de naam en sleutel hebt gekoppeld, heeft Storage Explorer niet gedetecteerd dat de hiërarchische naam ruimte werd ingeschakeld. Dit probleem is opgelost. #1081
* In de BLOB-editor sorteren op resterende Bewaar dagen of de status is verbroken. Dit probleem is opgelost. #1106
* Na 1.5.0 is Storage Explorer niet langer gewacht op het volt ooien van kopieën van de server voordat het succes wordt gerapporteerd tijdens een naamswijziging of kopiëren & plakken. Dit probleem is opgelost. #976
* Wanneer u de functie experimentele AzCopy gebruikt, is de opdracht gekopieerd nadat u op ' opdracht kopiëren naar klem bord ' was niet altijd zelf uitvoer bare. Nu worden alle opdrachten die nodig zijn om de overdracht hand matig uit te voeren, gekopieerd. #1079
* Voorheen werden ADLS Gen2 blobs niet toegankelijk als u zich achter een proxy bevond. Dit werd veroorzaakt door een fout in een nieuwe netwerk bibliotheek die wordt gebruikt door de opslag-SDK. In 1.7.0 is een poging gedaan om dit probleem te verhelpen, maar sommige personen kunnen toch problemen blijven zien. Een volledige oplossing wordt uitgebracht in een toekomstige update. #1090
* In 1.7.0 maakt het dialoog venster bestand opslaan nu goed plaats op de laatste locatie waar u een bestand hebt opgeslagen. #16
* In het deel venster Eigenschappen wordt de SKU-laag van een opslag account weer gegeven als het soort account. Dit probleem is opgelost. #654
* Soms was het onmogelijk om de lease van een BLOB te kraken, zelfs als u de naam van de BLOB correct hebt ingevoerd. Dit probleem is opgelost. #1070

### <a name="known-issues"></a>Bekende problemen

* Wanneer u RBAC gebruikt, heeft Storage Explorer bepaalde beheer laag machtigingen nodig om toegang te krijgen tot uw opslag resources. Raadpleeg de [hand leiding](./storage/common/storage-explorer-troubleshooting.md) voor het oplossen van problemen voor meer informatie.
* Bij een poging om toegang te krijgen tot ADLS Gen2 blobs na een proxy kan mislukken.
* Het ontkoppelen van een resource die is gekoppeld via SAS URI, zoals een BLOB-container, kan een fout veroorzaken die voor komt dat andere bijlagen correct worden weer gegeven. U kunt dit probleem omzeilen door het groeps knooppunt te vernieuwen. Zie #537 voor meer informatie.
* Het ontkoppelen van een resource die is gekoppeld via SAS URI, zoals een BLOB-container, kan een fout veroorzaken die voor komt dat andere bijlagen correct worden weer gegeven. U kunt dit probleem omzeilen door het groeps knooppunt te vernieuwen. Zie #537 voor meer informatie.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, kunt u zich mogelijk niet aanmelden. U kunt het probleem omzeilen door de inhoud van ~/te verwijderen. IdentityService/AadConfigurations. Als u dit doet, neemt u een opmerking over dit probleem op.
* Azurite heeft alle opslag-Api's nog niet volledig geïmplementeerd. Daarom kunnen er onverwachte fouten of gedrag zijn bij het gebruik van Azurite voor de ontwikkelings opslag.
* In zeldzame gevallen kan de structuur focus vastlopen op snelle toegang. Als u de focus wilt ontsteken, kunt u alle vernieuwen.
* Het uploaden van uw OneDrive-map werkt niet als gevolg van een fout in NodeJS. De fout is opgelost, maar is nog niet geïntegreerd in elektron. U kunt dit probleem omzeilen bij het uploaden naar of downloaden van een BLOB-container met de experimentele AzCopy-functie.
* Als Azure Stack, kan het uploaden van bepaalde bestanden als toevoeg-blobs mislukken.
* Nadat u op Annuleren hebt geklikt, kan het even duren voordat de taak is geannuleerd. Dit komt doordat we het werkings filter voor annuleren gebruiken dat hier wordt beschreven.
* Als u het verkeerde pincode/smartcard certificaat kiest, moet u de computer opnieuw opstarten om deze beslissing te Storage Explorer vergren delen.
* Bij het wijzigen van de naam van blobs (afzonderlijk of binnen een nieuwe BLOB-container) worden geen moment opnamen bewaard. Alle andere eigenschappen en meta gegevens voor blobs, bestanden en entiteiten blijven behouden tijdens het wijzigen van de naam.
* Azure Stack biedt geen ondersteuning voor de volgende functies. Bij het werken met Azure Stack resources kunnen onverwachte fouten optreden.
   * Bestandsshares
   * Toegangslagen
   * Voorlopig verwijderen
* De elektron-Shell die door Storage Explorer wordt gebruikt, heeft moeite met het versnellen van een GPU (graphics processing unit). Als Storage Explorer een leeg (leeg) hoofd venster wordt weer gegeven, kunt u proberen om Storage Explorer te starten vanaf de opdracht regel en de GPU-versnelling uit te scha kelen door de switch toe te voegen `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor Linux-gebruikers moet u [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0)installeren.
* Voor gebruikers op Ubuntu 14,04, moet u ervoor zorgen dat GCC up-to-date is. Dit kan worden gedaan door de volgende opdrachten uit te voeren en de computer vervolgens opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17,04 moet u GConf installeren. dit kunt u doen door de volgende opdrachten uit te voeren en vervolgens de computer opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-162"></a>Versie 1.6.2
1/9/2019

### <a name="hotfixes"></a>Hotfixes
* In 1.6.1 zijn entiteiten die zijn toegevoegd aan ADLS Gen2 Acl's door ObjectId die niet waren gebruikers, altijd toegevoegd als groepen. Nu worden alleen groepen toegevoegd als groepen, en entiteiten zoals zakelijke toepassingen die andService-principals zijn toegevoegd als gebruikers. [#1049](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1049)
* Als een ADLS Gen2 Storage-account geen containers heeft en is gekoppeld aan de naam en de sleutel, dan detecteert Storage Explorer niet dat het opslag account is ADLS Gen2. Dit probleem is opgelost. [#1048](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1048)
* In 1.6.0 worden tijdens het kopiëren en plakken conflicten niet gevraagd naar een oplossing. In plaats daarvan mislukt de conflicterende kopie gewoon. Op het eerste conflict wordt u gevraagd hoe u deze oplossing wilt laten oplossen. [#1014](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1014)
* Vanwege API-beperkingen is alle validatie van Objectid's in het dialoog venster toegang beheren uitgeschakeld. Validatie vindt nu alleen plaats voor gebruikers-Upn's. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* De machtigingen voor een groep kunnen niet worden gewijzigd in het dialoog venster ADLS Gen2 toegang beheren. Dit probleem is opgelost. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* De ondersteuning voor het uploaden van slepen en neerzetten is toegevoegd aan de ADLS Gen2 editor. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* De eigenschap URL in het dialoog venster Eigenschappen voor ADLS Gen2-bestanden en-mappen missen soms een '/'. Dit probleem is opgelost. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Als het ophalen van de huidige machtigingen voor een ADLS Gen2 container, bestand of map mislukt, wordt de fout nu in het activiteiten logboek weer gegeven. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* Het tijdelijke pad dat is gemaakt voor het openen van bestanden is inge kort om de kans op het maken van een pad dat langer is dan MAX_PATH in Windows te verminderen. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* Het dialoog venster verbinding maken wordt nu correct weer gegeven wanneer er geen aangemelde gebruikers zijn en er geen resources zijn gekoppeld. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* In 1.6.0 wordt met het opslaan van eigenschappen voor niet-HNS-blobs en-bestanden de waarde van elke eigenschap gecodeerd. Dit resulteerde in overbodige code ring van waarden die alleen ASCII-tekens bevatten. De waarden worden nu alleen gecodeerd als ze niet-ASCII-tekens bevatten. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Het uploaden van een map naar een niet-HNS BLOB-container mislukt als er een SAS werd gebruikt en de SA'S geen lees machtigingen hebben. Dit probleem is opgelost. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Het annuleren van een AzCopy-overdracht werkt niet. Dit probleem is opgelost. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* AzCopy mislukt bij het downloaden van een map van een ADLS Gen2 BLOB-container als de map spaties bevat. Dit probleem is opgelost. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* De CosmosDB-editor is gebroken in 1.6.0. Het is nu opgelost. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Nieuw

* U kunt nu Storage Explorer gebruiken om toegang te krijgen tot uw BLOB-gegevens via [RBAC](./storage/common/storage-auth-aad-rbac-portal.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Als u bent aangemeld en Storage Explorer de sleutels voor uw opslag account niet kan ophalen, wordt een OAuth-token gebruikt voor verificatie bij interactie met uw gegevens.
* Storage Explorer ondersteunt nu ADLS Gen2-opslag accounts. Als Storage Explorer detecteert dat hiërarchische naam ruimte is ingeschakeld voor een opslag account, wordt ' (ADLS Gen2 preview) ' weer gegeven naast de naam van uw opslag account. Storage Explorer kan detecteren of hiërarchische naam ruimte is ingeschakeld wanneer u bent aangemeld, of als u uw opslag account hebt gekoppeld aan de naam en sleutel. Voor ADLS Gen2 opslag accounts kunt u Storage Explorer gebruiken voor het volgende:
  * Containers maken en verwijderen
  * Container eigenschappen en-machtigingen beheren (aan de linkerkant)
  * Gegevens binnen containers weer geven en ernaar navigeren
  * Nieuwe mappen maken
  * Bestanden en mappen uploaden, downloaden, verwijderen en de naam ervan wijzigen
  * Eigenschappen en machtigingen voor bestanden en mappen beheren (aan de rechter kant).

    Andere typische BLOB-functies, zoals zacht verwijderen en moment opnamen, zijn momenteel niet beschikbaar. Het beheren van machtigingen is ook alleen beschikbaar wanneer u bent aangemeld. Wanneer u in een ADLS Gen2 Storage-account werkt Storage Explorer, wordt er bovendien AzCopy gebruikt voor alle uploads en down loads, en wordt de standaard instelling gebruikt voor het gebruik van naam-en sleutel referenties voor alle bewerkingen, indien beschikbaar.
* Na sterke feedback van de gebruiker kan de afbreek lease opnieuw worden gebruikt om leases op meerdere blobs tegelijk te verstoren.

### <a name="known-issues"></a>Bekende problemen

* Bij het downloaden van een ADLS Gen2 Storage-account wordt een van de bestanden die worden overgedragen al bestaan, AzCopy soms vastlopen. Dit wordt opgelost in een toekomstige hotfix.
* Het ontkoppelen van een resource die is gekoppeld via SAS URI, zoals een BLOB-container, kan een fout veroorzaken die voor komt dat andere bijlagen correct worden weer gegeven. U kunt dit probleem omzeilen door het groeps knooppunt te vernieuwen. Zie #537 voor meer informatie.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, kunt u zich mogelijk niet aanmelden. U kunt het probleem omzeilen door de inhoud van ~/te verwijderen. IdentityService/AadConfigurations. Als u dit doet, neemt u een opmerking over dit probleem op.
* Azurite heeft alle opslag-Api's nog niet volledig geïmplementeerd. Daarom kunnen er onverwachte fouten of gedrag zijn bij het gebruik van Azurite voor de ontwikkelings opslag.
* In zeldzame gevallen kan de structuur focus vastlopen op snelle toegang. Als u de focus wilt ontsteken, kunt u alle vernieuwen.
* Het uploaden van uw OneDrive-map werkt niet als gevolg van een fout in NodeJS. De fout is opgelost, maar is nog niet geïntegreerd in elektron. U kunt dit probleem omzeilen bij het uploaden naar of downloaden van een BLOB-container met de experimentele AzCopy-functie.
* Als Azure Stack, kan het uploaden van bepaalde bestanden als toevoeg-blobs mislukken.
* Nadat u op Annuleren hebt geklikt, kan het even duren voordat de taak is geannuleerd. Dit komt doordat we het werkings filter voor annuleren gebruiken dat hier wordt beschreven.
* Als u het verkeerde pincode/smartcard certificaat kiest, moet u de computer opnieuw opstarten om deze beslissing te Storage Explorer vergren delen.
* Bij het wijzigen van de naam van blobs (afzonderlijk of binnen een nieuwe BLOB-container) worden geen moment opnamen bewaard. Alle andere eigenschappen en meta gegevens voor blobs, bestanden en entiteiten blijven behouden tijdens het wijzigen van de naam.
* Azure Stack biedt geen ondersteuning voor de volgende functies. Bij het werken met Azure Stack resources kunnen onverwachte fouten optreden.
   * Bestandsshares
   * Toegangslagen
   * Voorlopig verwijderen
* De elektron-Shell die door Storage Explorer wordt gebruikt, heeft moeite met het versnellen van een GPU (graphics processing unit). Als Storage Explorer een leeg (leeg) hoofd venster wordt weer gegeven, kunt u proberen om Storage Explorer te starten vanaf de opdracht regel en de GPU-versnelling uit te scha kelen door de switch toe te voegen `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor Linux-gebruikers moet u [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0)installeren.
* Voor gebruikers op Ubuntu 14,04, moet u ervoor zorgen dat GCC up-to-date is. Dit kan worden gedaan door de volgende opdrachten uit te voeren en de computer vervolgens opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17,04 moet u GConf installeren. dit kunt u doen door de volgende opdrachten uit te voeren en vervolgens de computer opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-161"></a>Versie 1.6.1
12/18/2018

### <a name="hotfixes"></a>Hotfixes
* Vanwege API-beperkingen is alle validatie van Objectid's in het dialoog venster toegang beheren uitgeschakeld. Validatie vindt nu alleen plaats voor gebruikers-Upn's. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* De machtigingen voor een groep kunnen niet worden gewijzigd in het dialoog venster ADLS Gen2 toegang beheren. Dit probleem is opgelost. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* De ondersteuning voor het uploaden van slepen en neerzetten is toegevoegd aan de ADLS Gen2 editor. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* De eigenschap URL in het dialoog venster Eigenschappen voor ADLS Gen2-bestanden en-mappen missen soms een '/'. Dit probleem is opgelost. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Als het ophalen van de huidige machtigingen voor een ADLS Gen2 container, bestand of map mislukt, wordt de fout nu in het activiteiten logboek weer gegeven. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* Het tijdelijke pad dat is gemaakt voor het openen van bestanden is inge kort om de kans op het maken van een pad dat langer is dan MAX_PATH in Windows te verminderen. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* Het dialoog venster verbinding maken wordt nu correct weer gegeven wanneer er geen aangemelde gebruikers zijn en er geen resources zijn gekoppeld. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* In 1.6.0 wordt met het opslaan van eigenschappen voor niet-HNS-blobs en-bestanden de waarde van elke eigenschap gecodeerd. Dit resulteerde in overbodige code ring van waarden die alleen ASCII-tekens bevatten. De waarden worden nu alleen gecodeerd als ze niet-ASCII-tekens bevatten. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Het uploaden van een map naar een niet-HNS BLOB-container mislukt als er een SAS werd gebruikt en de SA'S geen lees machtigingen hebben. Dit probleem is opgelost. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Het annuleren van een AzCopy-overdracht werkt niet. Dit probleem is opgelost. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* AzCopy mislukt bij het downloaden van een map van een ADLS Gen2 BLOB-container als de map spaties bevat. Dit probleem is opgelost. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* De CosmosDB-editor is gebroken in 1.6.0. Het is nu opgelost. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Nieuw

* U kunt nu Storage Explorer gebruiken om toegang te krijgen tot uw BLOB-gegevens via [RBAC](./storage/common/storage-auth-aad-rbac-portal.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Als u bent aangemeld en Storage Explorer de sleutels voor uw opslag account niet kan ophalen, wordt een OAuth-token gebruikt voor verificatie bij interactie met uw gegevens.
* Storage Explorer ondersteunt nu ADLS Gen2-opslag accounts. Als Storage Explorer detecteert dat hiërarchische naam ruimte is ingeschakeld voor een opslag account, wordt ' (ADLS Gen2 preview) ' weer gegeven naast de naam van uw opslag account. Storage Explorer kan detecteren of hiërarchische naam ruimte is ingeschakeld wanneer u bent aangemeld, of als u uw opslag account hebt gekoppeld aan de naam en sleutel. Voor ADLS Gen2 opslag accounts kunt u Storage Explorer gebruiken voor het volgende:
  * Containers maken en verwijderen
  * Container eigenschappen en-machtigingen beheren (aan de linkerkant)
  * Gegevens binnen containers weer geven en ernaar navigeren
  * Nieuwe mappen maken
  * Bestanden en mappen uploaden, downloaden, verwijderen en de naam ervan wijzigen
  * Eigenschappen en machtigingen voor bestanden en mappen beheren (aan de rechter kant).
    
    Andere typische BLOB-functies, zoals zacht verwijderen en moment opnamen, zijn momenteel niet beschikbaar. Het beheren van machtigingen is ook alleen beschikbaar wanneer u bent aangemeld. Wanneer u in een ADLS Gen2 Storage-account werkt Storage Explorer, wordt er bovendien AzCopy gebruikt voor alle uploads en down loads, en wordt de standaard instelling gebruikt voor het gebruik van naam-en sleutel referenties voor alle bewerkingen, indien beschikbaar.
* Na sterke feedback van de gebruiker kan de afbreek lease opnieuw worden gebruikt om leases op meerdere blobs tegelijk te verstoren.

### <a name="known-issues"></a>Bekende problemen

* Bij het downloaden van een ADLS Gen2 Storage-account wordt een van de bestanden die worden overgedragen al bestaan, AzCopy soms vastlopen. Dit wordt opgelost in een toekomstige hotfix.
* Het ontkoppelen van een resource die is gekoppeld via SAS URI, zoals een BLOB-container, kan een fout veroorzaken die voor komt dat andere bijlagen correct worden weer gegeven. U kunt dit probleem omzeilen door het groeps knooppunt te vernieuwen. Zie #537 voor meer informatie.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, kunt u zich mogelijk niet aanmelden. U kunt het probleem omzeilen door de inhoud van ~/te verwijderen. IdentityService/AadConfigurations. Als u dit doet, neemt u een opmerking over dit probleem op.
* Azurite heeft alle opslag-Api's nog niet volledig geïmplementeerd. Daarom kunnen er onverwachte fouten of gedrag zijn bij het gebruik van Azurite voor de ontwikkelings opslag.
* In zeldzame gevallen kan de structuur focus vastlopen op snelle toegang. Als u de focus wilt ontsteken, kunt u alle vernieuwen.
* Het uploaden van uw OneDrive-map werkt niet als gevolg van een fout in NodeJS. De fout is opgelost, maar is nog niet geïntegreerd in elektron. U kunt dit probleem omzeilen bij het uploaden naar of downloaden van een BLOB-container met de experimentele AzCopy-functie.
* Als Azure Stack, kan het uploaden van bepaalde bestanden als toevoeg-blobs mislukken.
* Nadat u op Annuleren hebt geklikt, kan het even duren voordat de taak is geannuleerd. Dit komt doordat we het werkings filter voor annuleren gebruiken dat hier wordt beschreven.
* Als u het verkeerde pincode/smartcard certificaat kiest, moet u de computer opnieuw opstarten om deze beslissing te Storage Explorer vergren delen.
* Bij het wijzigen van de naam van blobs (afzonderlijk of binnen een nieuwe BLOB-container) worden geen moment opnamen bewaard. Alle andere eigenschappen en meta gegevens voor blobs, bestanden en entiteiten blijven behouden tijdens het wijzigen van de naam.
* Azure Stack biedt geen ondersteuning voor de volgende functies. Bij het werken met Azure Stack resources kunnen onverwachte fouten optreden.
   * Bestandsshares
   * Toegangslagen
   * Voorlopig verwijderen
* De elektron-Shell die door Storage Explorer wordt gebruikt, heeft moeite met het versnellen van een GPU (graphics processing unit). Als Storage Explorer een leeg (leeg) hoofd venster wordt weer gegeven, kunt u proberen om Storage Explorer te starten vanaf de opdracht regel en de GPU-versnelling uit te scha kelen door de switch toe te voegen `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor Linux-gebruikers moet u [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0)installeren.
* Voor gebruikers op Ubuntu 14,04, moet u ervoor zorgen dat GCC up-to-date is. Dit kan worden gedaan door de volgende opdrachten uit te voeren en de computer vervolgens opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17,04 moet u GConf installeren. dit kunt u doen door de volgende opdrachten uit te voeren en vervolgens de computer opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-160"></a>Versie 1.6.0
5-12-2018

### <a name="new"></a>Nieuw

* U kunt nu Storage Explorer gebruiken om toegang te krijgen tot uw BLOB-gegevens via [RBAC](./storage/common/storage-auth-aad-rbac-portal.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Als u bent aangemeld en Storage Explorer de sleutels voor uw opslag account niet kan ophalen, wordt een OAuth-token gebruikt voor verificatie bij interactie met uw gegevens.
* Storage Explorer ondersteunt nu ADLS Gen2-opslag accounts. Als Storage Explorer detecteert dat hiërarchische naam ruimte is ingeschakeld voor een opslag account, wordt ' (ADLS Gen2 preview) ' weer gegeven naast de naam van uw opslag account. Storage Explorer kan detecteren of hiërarchische naam ruimte is ingeschakeld wanneer u bent aangemeld, of als u uw opslag account hebt gekoppeld aan de naam en sleutel. Voor ADLS Gen2 opslag accounts kunt u Storage Explorer gebruiken voor het volgende:
  * Containers maken en verwijderen
  * Container eigenschappen en-machtigingen beheren (aan de linkerkant)
  * Gegevens binnen containers weer geven en ernaar navigeren
  * Nieuwe mappen maken
  * Bestanden en mappen uploaden, downloaden, verwijderen en de naam ervan wijzigen
  * Eigenschappen en machtigingen voor bestanden en mappen beheren (aan de rechter kant).
    
    Andere typische BLOB-functies, zoals zacht verwijderen en moment opnamen, zijn momenteel niet beschikbaar. Het beheren van machtigingen is ook alleen beschikbaar wanneer u bent aangemeld. Wanneer u in een ADLS Gen2 Storage-account werkt Storage Explorer, wordt er bovendien AzCopy gebruikt voor alle uploads en down loads, en wordt de standaard instelling gebruikt voor het gebruik van naam-en sleutel referenties voor alle bewerkingen, indien beschikbaar.
* Na sterke feedback van de gebruiker kan de afbreek lease opnieuw worden gebruikt om leases op meerdere blobs tegelijk te verstoren.

### <a name="known-issues"></a>Bekende problemen

* Bij het downloaden van een ADLS Gen2 Storage-account wordt een van de bestanden die worden overgedragen al bestaan, AzCopy soms vastlopen. Dit wordt opgelost in een toekomstige hotfix.
* Het ontkoppelen van een resource die is gekoppeld via SAS URI, zoals een BLOB-container, kan een fout veroorzaken die voor komt dat andere bijlagen correct worden weer gegeven. U kunt dit probleem omzeilen door het groeps knooppunt te vernieuwen. Zie #537 voor meer informatie.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, kunt u zich mogelijk niet aanmelden. U kunt het probleem omzeilen door de inhoud van ~/te verwijderen. IdentityService/AadConfigurations. Als u dit doet, neemt u een opmerking over dit probleem op.
* Azurite heeft alle opslag-Api's nog niet volledig geïmplementeerd. Daarom kunnen er onverwachte fouten of gedrag zijn bij het gebruik van Azurite voor de ontwikkelings opslag.
* In zeldzame gevallen kan de structuur focus vastlopen op snelle toegang. Als u de focus wilt ontsteken, kunt u alle vernieuwen.
* Het uploaden van uw OneDrive-map werkt niet als gevolg van een fout in NodeJS. De fout is opgelost, maar is nog niet geïntegreerd in elektron. U kunt dit probleem omzeilen bij het uploaden naar of downloaden van een BLOB-container met de experimentele AzCopy-functie.
* Als Azure Stack, kan het uploaden van bepaalde bestanden als toevoeg-blobs mislukken.
* Nadat u op Annuleren hebt geklikt, kan het even duren voordat de taak is geannuleerd. Dit komt doordat we het werkings filter voor annuleren gebruiken dat hier wordt beschreven.
* Als u het verkeerde pincode/smartcard certificaat kiest, moet u de computer opnieuw opstarten om deze beslissing te Storage Explorer vergren delen.
* Bij het wijzigen van de naam van blobs (afzonderlijk of binnen een nieuwe BLOB-container) worden geen moment opnamen bewaard. Alle andere eigenschappen en meta gegevens voor blobs, bestanden en entiteiten blijven behouden tijdens het wijzigen van de naam.
* Azure Stack biedt geen ondersteuning voor de volgende functies. Bij het werken met Azure Stack resources kunnen onverwachte fouten optreden.
   * Bestandsshares
   * Toegangslagen
   * Voorlopig verwijderen
* De elektron-Shell die door Storage Explorer wordt gebruikt, heeft moeite met het versnellen van een GPU (graphics processing unit). Als Storage Explorer een leeg (leeg) hoofd venster wordt weer gegeven, kunt u proberen om Storage Explorer te starten vanaf de opdracht regel en de GPU-versnelling uit te scha kelen door de switch toe te voegen `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor Linux-gebruikers moet u [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0)installeren.
* Voor gebruikers op Ubuntu 14,04, moet u ervoor zorgen dat GCC up-to-date is. Dit kan worden gedaan door de volgende opdrachten uit te voeren en de computer vervolgens opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17,04 moet u GConf installeren. dit kunt u doen door de volgende opdrachten uit te voeren en vervolgens de computer opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-150"></a>Versie 1.5.0
10/29/2018

### <a name="new"></a>Nieuw

* U kunt nu [AzCopy V10 toevoegen (preview)](https://github.com/Azure/azure-storage-azcopy) gebruiken voor het uploaden en downloaden van blobs. Als u deze functie wilt inschakelen, gaat u naar het menu experimenteren en klikt u op AzCopy gebruiken voor verbeterde BLOB uploaden en downloaden. Als deze functie is ingeschakeld, wordt AzCopy gebruikt in de volgende scenario's:
   * Het uploaden van mappen en bestanden naar BLOB-containers, hetzij via de werk balk, hetzij slepen en neerzetten.
   * Het downloaden van mappen en bestanden via de werk balk of in het context menu.

* Daarnaast kunt u bij het gebruik van AzCopy:
   * U kunt de AzCopy-opdracht die wordt gebruikt voor het uitvoeren van de overdracht naar uw klem bord kopiëren. Klik in het activiteiten logboek op de opdracht AzCopy kopiëren naar het klem bord.
   * U moet de BLOB-editor hand matig vernieuwen na het uploaden.
   * Het uploaden van bestanden naar toevoeg-blobs wordt niet ondersteund en VHD-bestanden worden geüpload als pagina-blobs en alle andere bestanden worden geüpload als blok-blobs.
   * Fouten en conflicten die zich voordoen tijdens het uploaden of downloaden, worden pas afgevoerd nadat het uploaden of downloaden is voltooid.

Ten slotte wordt de ondersteuning voor het gebruik van AzCopy met bestands shares in de toekomst beschikbaar.
* Storage Explorer maakt nu gebruik van elektronen versie 2.0.11.
* Het verbreken van leases kan nu alleen op één BLOB tegelijk worden uitgevoerd. Daarnaast moet u de naam van de BLOB invoeren waarvan u de lease wilt opdelen. Deze wijziging is doorgevoerd om de kans te verminderen dat een lease per ongeluk wordt verbroken, met name voor Vm's. #394
* Als u ooit aanmeldings problemen ondervindt, kunt u de verificatie nu opnieuw instellen. Ga naar het menu Help en klik op opnieuw instellen om toegang te krijgen tot deze functie. #419

### <a name="fix"></a>Herstellen

* Na sterke feedback van gebruikers is het knoop punt standaard emulator weer ingeschakeld. U kunt nog steeds extra emulator-verbindingen toevoegen via het dialoog venster verbinding maken, maar als uw emulator is geconfigureerd voor het gebruik van de standaard poorten, kunt u ook het knoop punt emulator * standaard poorten onder lokale & gekoppelde/opslag accounts gebruiken. #669
* Met Storage Explorer kunt u geen waarden voor BLOB-meta gegevens instellen die voor loop-of volg spaties hebben. #760
* De knop Aanmelden is altijd ingeschakeld op dezelfde pagina's van het dialoog venster verbinden. Het is nu uitgeschakeld wanneer dat nodig is. #761
* Snelle toegang genereert geen fout meer in de console wanneer er geen snelle toegangs items zijn toegevoegd.

### <a name="known-issues"></a>Bekende problemen

* Het ontkoppelen van een resource die is gekoppeld via SAS URI, zoals een BLOB-container, kan een fout veroorzaken die voor komt dat andere bijlagen correct worden weer gegeven. U kunt dit probleem omzeilen door het groeps knooppunt te vernieuwen. Zie #537 voor meer informatie.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, kunt u zich mogelijk niet aanmelden. U kunt het probleem omzeilen door de inhoud van ~/te verwijderen. IdentityService/AadConfigurations. Als u dit doet, neemt u een opmerking over dit probleem op.
* Azurite heeft alle opslag-Api's nog niet volledig geïmplementeerd. Daarom kunnen er onverwachte fouten of gedrag zijn bij het gebruik van Azurite voor de ontwikkelings opslag.
* In zeldzame gevallen kan de structuur focus vastlopen op snelle toegang. Als u de focus wilt ontsteken, kunt u alle vernieuwen.
* Het uploaden van uw OneDrive-map werkt niet als gevolg van een fout in NodeJS. De fout is opgelost, maar is nog niet geïntegreerd in elektron. U kunt dit probleem omzeilen bij het uploaden naar of downloaden van een BLOB-container met de experimentele AzCopy-functie.
* Als Azure Stack, kan het uploaden van bepaalde bestanden als toevoeg-blobs mislukken.
* Nadat u op Annuleren hebt geklikt, kan het even duren voordat de taak is geannuleerd. Dit komt doordat we het werkings filter voor annuleren gebruiken dat hier wordt beschreven.
* Als u het verkeerde pincode/smartcard certificaat kiest, moet u de computer opnieuw opstarten om deze beslissing te Storage Explorer vergren delen.
* Bij het wijzigen van de naam van blobs (afzonderlijk of binnen een nieuwe BLOB-container) worden geen moment opnamen bewaard. Alle andere eigenschappen en meta gegevens voor blobs, bestanden en entiteiten blijven behouden tijdens het wijzigen van de naam.
* Azure Stack biedt geen ondersteuning voor de volgende functies. Bij het werken met Azure Stack resources kunnen onverwachte fouten optreden.
   * Bestandsshares
   * Toegangslagen
   * Voorlopig verwijderen
* De elektron-Shell die door Storage Explorer wordt gebruikt, heeft moeite met het versnellen van een GPU (graphics processing unit). Als Storage Explorer een leeg (leeg) hoofd venster wordt weer gegeven, kunt u proberen om Storage Explorer te starten vanaf de opdracht regel en de GPU-versnelling uit te scha kelen door de switch toe te voegen `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor Linux-gebruikers moet u [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0)installeren.
* Voor gebruikers op Ubuntu 14,04, moet u ervoor zorgen dat GCC up-to-date is. Dit kan worden gedaan door de volgende opdrachten uit te voeren en de computer vervolgens opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17,04 moet u GConf installeren. dit kunt u doen door de volgende opdrachten uit te voeren en vervolgens de computer opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-144"></a>Versie 1.4.4
10/15/2018

### <a name="hotfixes"></a>Hotfixes
* De Azure Resource Management API-versie is teruggedraaid om de blok kering van Azure-gebruikers van de Amerikaanse overheid ongedaan te maken. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Het laden van kring velden maakt nu gebruik van CSS-animaties om de hoeveelheid GPU te verminderen die door Storage Explorer wordt gebruikt. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Nieuw
* Externe bron bijlagen, zoals voor SAS-verbindingen en-emulators, zijn aanzienlijk verbeterd. U kunt nu:
   * Pas de weergave naam aan van de resource die u wilt koppelen. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Verbinden met meerdere lokale emulators met behulp van verschillende poorten. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Voeg gekoppelde resources toe aan snelle toegang. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Storage Explorer ondersteunt nu het voorlopig verwijderen. U kunt het volgende doen:
   * Configureer een beleid voor voorlopig verwijderen door met de rechter muisknop te klikken op het knoop punt BLOB containers voor uw opslag account.
   * Bekijk de voorlopig verwijderde blobs in de BLOB-editor door actieve en verwijderde blobs te selecteren in de vervolg keuzelijst naast de navigatie balk.
   * Verwijder tijdelijke verwijderde blobs.

### <a name="fixes"></a>Oplossingen
* De actie CORS-instellingen configureren is niet meer beschikbaar op Premium Storage accounts omdat Premium Storage accounts geen CORS ondersteunen. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Er is nu een Shared Access Signature-eigenschap voor aan SAS gekoppelde services. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* De actie ' standaard Access-laag instellen ' is nu beschikbaar voor Blob-en GPV2-opslag accounts die zijn vastgemaakt aan snelle toegang. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Soms worden klassieke opslag accounts in Storage Explorer niet weer gegeven. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Bekende problemen
* Wanneer u emulators gebruikt, zoals Azure Storage emulator of Azurite, moet u ze Luis teren naar verbindingen op hun standaard poorten. Als dat niet het geval is, kan Storage Explorer geen verbinding maken.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, kunt u zich mogelijk niet aanmelden. U kunt het probleem omzeilen door de inhoud van ~/te verwijderen. IdentityService/AadConfigurations. Als u dat wel doet, kunt u een opmerking over [Dit probleem](https://github.com/Microsoft/AzureStorageExplorer/issues/97)melden.
* Azurite heeft alle opslag-Api's nog niet volledig geïmplementeerd. Daarom kunnen er onverwachte fouten of gedrag zijn bij het gebruik van Azurite voor de ontwikkelings opslag.
* In zeldzame gevallen kan de structuur focus vastlopen op snelle toegang. Als u de focus wilt ontsteken, kunt u alle vernieuwen.
* Het uploaden van uw OneDrive-map werkt niet als gevolg van een fout in NodeJS. De fout is opgelost, maar is nog niet geïntegreerd in elektron.
* Als Azure Stack, kan het uploaden van bepaalde bestanden als toevoeg-blobs mislukken.
* Nadat u op Annuleren hebt geklikt, kan het even duren voordat de taak is geannuleerd. Dit komt doordat we het werkings filter voor annuleren gebruiken dat [hier](https://github.com/Azure/azure-storage-node/issues/317)wordt beschreven.
* Als u het verkeerde pincode/smartcard certificaat kiest, moet u de computer opnieuw opstarten om deze beslissing te Storage Explorer vergren delen.
* Bij het wijzigen van de naam van blobs (afzonderlijk of binnen een nieuwe BLOB-container) worden geen moment opnamen bewaard. Alle andere eigenschappen en meta gegevens voor blobs, bestanden en entiteiten blijven behouden tijdens het wijzigen van de naam.
* Hoewel Azure Stack momenteel geen bestanden ondersteunt, wordt het knoop punt bestands shares nog steeds weer gegeven onder een Azure Stack opslag account dat is gekoppeld.
* De elektron-Shell die door Storage Explorer wordt gebruikt, heeft moeite met het versnellen van een GPU (graphics processing unit). Als Storage Explorer een leeg (leeg) hoofd venster wordt weer gegeven, kunt u proberen om Storage Explorer te starten vanaf de opdracht regel en de GPU-versnelling uit te scha kelen door de switch toe te voegen `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor Linux-gebruikers moet u [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0)installeren.
* Voor gebruikers op Ubuntu 14,04, moet u ervoor zorgen dat GCC up-to-date is. Dit kan worden gedaan door de volgende opdrachten uit te voeren en de computer vervolgens opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17,04 moet u GConf installeren. dit kunt u doen door de volgende opdrachten uit te voeren en vervolgens de computer opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-143"></a>Versie 1.4.3
10/11/2018

### <a name="hotfixes"></a>Hotfixes
* De Azure Resource Management API-versie is teruggedraaid om de blok kering van Azure-gebruikers van de Amerikaanse overheid ongedaan te maken. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Het laden van kring velden maakt nu gebruik van CSS-animaties om de hoeveelheid GPU te verminderen die door Storage Explorer wordt gebruikt. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Nieuw
* Externe bron bijlagen, zoals voor SAS-verbindingen en-emulators, zijn aanzienlijk verbeterd. U kunt nu:
   * Pas de weergave naam aan van de resource die u wilt koppelen. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Verbinden met meerdere lokale emulators met behulp van verschillende poorten. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Voeg gekoppelde resources toe aan snelle toegang. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Storage Explorer ondersteunt nu het voorlopig verwijderen. U kunt het volgende doen:
   * Configureer een beleid voor voorlopig verwijderen door met de rechter muisknop te klikken op het knoop punt BLOB containers voor uw opslag account.
   * Bekijk de voorlopig verwijderde blobs in de BLOB-editor door actieve en verwijderde blobs te selecteren in de vervolg keuzelijst naast de navigatie balk.
   * Verwijder tijdelijke verwijderde blobs.

### <a name="fixes"></a>Oplossingen
* De actie CORS-instellingen configureren is niet meer beschikbaar op Premium Storage accounts omdat Premium Storage accounts geen CORS ondersteunen. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Er is nu een Shared Access Signature-eigenschap voor aan SAS gekoppelde services. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* De actie ' standaard Access-laag instellen ' is nu beschikbaar voor Blob-en GPV2-opslag accounts die zijn vastgemaakt aan snelle toegang. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Soms worden klassieke opslag accounts in Storage Explorer niet weer gegeven. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Bekende problemen
* Wanneer u emulators gebruikt, zoals Azure Storage emulator of Azurite, moet u ze Luis teren naar verbindingen op hun standaard poorten. Als dat niet het geval is, kan Storage Explorer geen verbinding maken.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, kunt u zich mogelijk niet aanmelden. U kunt het probleem omzeilen door de inhoud van ~/te verwijderen. IdentityService/AadConfigurations. Als u dat wel doet, kunt u een opmerking over [Dit probleem](https://github.com/Microsoft/AzureStorageExplorer/issues/97)melden.
* Azurite heeft alle opslag-Api's nog niet volledig geïmplementeerd. Daarom kunnen er onverwachte fouten of gedrag zijn bij het gebruik van Azurite voor de ontwikkelings opslag.
* In zeldzame gevallen kan de structuur focus vastlopen op snelle toegang. Als u de focus wilt ontsteken, kunt u alle vernieuwen.
* Het uploaden van uw OneDrive-map werkt niet als gevolg van een fout in NodeJS. De fout is opgelost, maar is nog niet geïntegreerd in elektron.
* Als Azure Stack, kan het uploaden van bepaalde bestanden als toevoeg-blobs mislukken.
* Nadat u op Annuleren hebt geklikt, kan het even duren voordat de taak is geannuleerd. Dit komt doordat we het werkings filter voor annuleren gebruiken dat [hier](https://github.com/Azure/azure-storage-node/issues/317)wordt beschreven.
* Als u het verkeerde pincode/smartcard certificaat kiest, moet u de computer opnieuw opstarten om deze beslissing te Storage Explorer vergren delen.
* Bij het wijzigen van de naam van blobs (afzonderlijk of binnen een nieuwe BLOB-container) worden geen moment opnamen bewaard. Alle andere eigenschappen en meta gegevens voor blobs, bestanden en entiteiten blijven behouden tijdens het wijzigen van de naam.
* Hoewel Azure Stack momenteel geen bestanden ondersteunt, wordt het knoop punt bestands shares nog steeds weer gegeven onder een Azure Stack opslag account dat is gekoppeld.
* De elektron-Shell die door Storage Explorer wordt gebruikt, heeft moeite met het versnellen van een GPU (graphics processing unit). Als Storage Explorer een leeg (leeg) hoofd venster wordt weer gegeven, kunt u proberen om Storage Explorer te starten vanaf de opdracht regel en de GPU-versnelling uit te scha kelen door de switch toe te voegen `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor Linux-gebruikers moet u [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0)installeren.
* Voor gebruikers op Ubuntu 14,04, moet u ervoor zorgen dat GCC up-to-date is. Dit kan worden gedaan door de volgende opdrachten uit te voeren en de computer vervolgens opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17,04 moet u GConf installeren. dit kunt u doen door de volgende opdrachten uit te voeren en vervolgens de computer opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-142"></a>Versie 1.4.2
09/24/2018

### <a name="hotfixes"></a>Hotfixes
* Werk de API-versie van Azure resource management bij naar 2018-07-01 om ondersteuning toe te voegen voor nieuwe Azure Storage-account typen. [#652](https://github.com/Microsoft/AzureStorageExplorer/issues/652)

### <a name="new"></a>Nieuw
* Externe bron bijlagen, zoals voor SAS-verbindingen en-emulators, zijn aanzienlijk verbeterd. U kunt nu:
   * Pas de weergave naam aan van de resource die u wilt koppelen. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Verbinden met meerdere lokale emulators met behulp van verschillende poorten. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Voeg gekoppelde resources toe aan snelle toegang. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Storage Explorer ondersteunt nu het voorlopig verwijderen. U kunt het volgende doen:
   * Configureer een beleid voor voorlopig verwijderen door met de rechter muisknop te klikken op het knoop punt BLOB containers voor uw opslag account.
   * Bekijk de voorlopig verwijderde blobs in de BLOB-editor door actieve en verwijderde blobs te selecteren in de vervolg keuzelijst naast de navigatie balk.
   * Verwijder tijdelijke verwijderde blobs.

### <a name="fixes"></a>Oplossingen
* De actie CORS-instellingen configureren is niet meer beschikbaar op Premium Storage accounts omdat Premium Storage accounts geen CORS ondersteunen. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Er is nu een Shared Access Signature-eigenschap voor aan SAS gekoppelde services. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* De actie ' standaard Access-laag instellen ' is nu beschikbaar voor Blob-en GPV2-opslag accounts die zijn vastgemaakt aan snelle toegang. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Soms worden klassieke opslag accounts in Storage Explorer niet weer gegeven. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Bekende problemen
* Wanneer u emulators gebruikt, zoals Azure Storage emulator of Azurite, moet u ze Luis teren naar verbindingen op hun standaard poorten. Als dat niet het geval is, kan Storage Explorer geen verbinding maken.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, kunt u zich mogelijk niet aanmelden. U kunt het probleem omzeilen door de inhoud van ~/te verwijderen. IdentityService/AadConfigurations. Als u dat wel doet, kunt u een opmerking over [Dit probleem](https://github.com/Microsoft/AzureStorageExplorer/issues/97)melden.
* Azurite heeft alle opslag-Api's nog niet volledig geïmplementeerd. Daarom kunnen er onverwachte fouten of gedrag zijn bij het gebruik van Azurite voor de ontwikkelings opslag.
* In zeldzame gevallen kan de structuur focus vastlopen op snelle toegang. Als u de focus wilt ontsteken, kunt u alle vernieuwen.
* Het uploaden van uw OneDrive-map werkt niet als gevolg van een fout in NodeJS. De fout is opgelost, maar is nog niet geïntegreerd in elektron.
* Als Azure Stack, kan het uploaden van bepaalde bestanden als toevoeg-blobs mislukken.
* Nadat u op Annuleren hebt geklikt, kan het even duren voordat de taak is geannuleerd. Dit komt doordat we het werkings filter voor annuleren gebruiken dat [hier](https://github.com/Azure/azure-storage-node/issues/317)wordt beschreven.
* Als u het verkeerde pincode/smartcard certificaat kiest, moet u de computer opnieuw opstarten om deze beslissing te Storage Explorer vergren delen.
* Bij het wijzigen van de naam van blobs (afzonderlijk of binnen een nieuwe BLOB-container) worden geen moment opnamen bewaard. Alle andere eigenschappen en meta gegevens voor blobs, bestanden en entiteiten blijven behouden tijdens het wijzigen van de naam.
* Hoewel Azure Stack momenteel geen bestanden ondersteunt, wordt het knoop punt bestands shares nog steeds weer gegeven onder een Azure Stack opslag account dat is gekoppeld.
* De elektron-Shell die door Storage Explorer wordt gebruikt, heeft moeite met het versnellen van een GPU (graphics processing unit). Als Storage Explorer een leeg (leeg) hoofd venster wordt weer gegeven, kunt u proberen om Storage Explorer te starten vanaf de opdracht regel en de GPU-versnelling uit te scha kelen door de switch toe te voegen `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor Linux-gebruikers moet u [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0)installeren.
* Voor gebruikers op Ubuntu 14,04, moet u ervoor zorgen dat GCC up-to-date is. Dit kan worden gedaan door de volgende opdrachten uit te voeren en de computer vervolgens opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17,04 moet u GConf installeren. dit kunt u doen door de volgende opdrachten uit te voeren en vervolgens de computer opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-141"></a>Versie 1.4.1
08/28/2018

### <a name="hotfixes"></a>Hotfixes
* Bij de eerste keer starten kan Storage Explorer geen sleutel genereren die wordt gebruikt voor het versleutelen van gevoelige gegevens. Dit kan problemen veroorzaken bij het gebruik van snelle toegang en het koppelen van resources. [#535](https://github.com/Microsoft/AzureStorageExplorer/issues/535)
* Als uw account geen MFA vereist voor de thuis Tenant, maar wel voor andere tenants, zou Storage Explorer geen abonnementen kunnen weer geven. Nadat u zich hebt aangemeld met een dergelijk account, wordt u door Storage Explorer gevraagd uw referenties opnieuw in te voeren en MFA uit te voeren. [#74](https://github.com/Microsoft/AzureStorageExplorer/issues/74)
* Storage Explorer kon geen resources koppelen van Azure Duitsland en de Amerikaanse overheid van Azure. [#572](https://github.com/Microsoft/AzureStorageExplorer/issues/572)
* Als u zich met hetzelfde e-mail adres hebt aangemeld bij twee accounts, worden uw resources in Storage Explorer soms niet weer gegeven in de structuur weergave. [#580](https://github.com/Microsoft/AzureStorageExplorer/issues/580)
* Op tragere Windows-computers zal het begin scherm soms veel tijd in beslag nemen. [#586](https://github.com/Microsoft/AzureStorageExplorer/issues/586)
* Het dialoog venster verbinding maken wordt weer gegeven, zelfs als er gekoppelde accounts of services zijn. [#588](https://github.com/Microsoft/AzureStorageExplorer/issues/588)

### <a name="new"></a>Nieuw
* Externe bron bijlagen, zoals voor SAS-verbindingen en-emulators, zijn aanzienlijk verbeterd. U kunt nu:
   * Pas de weergave naam aan van de resource die u wilt koppelen. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Verbinden met meerdere lokale emulators met behulp van verschillende poorten. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Voeg gekoppelde resources toe aan snelle toegang. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Storage Explorer ondersteunt nu het voorlopig verwijderen. U kunt het volgende doen:
   * Configureer een beleid voor voorlopig verwijderen door met de rechter muisknop te klikken op het knoop punt BLOB containers voor uw opslag account.
   * Bekijk de voorlopig verwijderde blobs in de BLOB-editor door actieve en verwijderde blobs te selecteren in de vervolg keuzelijst naast de navigatie balk.
   * Verwijder tijdelijke verwijderde blobs.

### <a name="fixes"></a>Oplossingen
* De actie CORS-instellingen configureren is niet meer beschikbaar op Premium Storage accounts omdat Premium Storage accounts geen CORS ondersteunen. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Er is nu een Shared Access Signature-eigenschap voor aan SAS gekoppelde services. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* De actie ' standaard Access-laag instellen ' is nu beschikbaar voor Blob-en GPV2-opslag accounts die zijn vastgemaakt aan snelle toegang. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Soms worden klassieke opslag accounts in Storage Explorer niet weer gegeven. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Bekende problemen
* Wanneer u emulators gebruikt, zoals Azure Storage emulator of Azurite, moet u ze Luis teren naar verbindingen op hun standaard poorten. Als dat niet het geval is, kan Storage Explorer geen verbinding maken.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, kunt u zich mogelijk niet aanmelden. U kunt het probleem omzeilen door de inhoud van ~/te verwijderen. IdentityService/AadConfigurations. Als u dat wel doet, kunt u een opmerking over [Dit probleem](https://github.com/Microsoft/AzureStorageExplorer/issues/97)melden.
* Azurite heeft alle opslag-Api's nog niet volledig geïmplementeerd. Daarom kunnen er onverwachte fouten of gedrag zijn bij het gebruik van Azurite voor de ontwikkelings opslag.
* In zeldzame gevallen kan de structuur focus vastlopen op snelle toegang. Als u de focus wilt ontsteken, kunt u alle vernieuwen.
* Het uploaden van uw OneDrive-map werkt niet als gevolg van een fout in NodeJS. De fout is opgelost, maar is nog niet geïntegreerd in elektron.
* Als Azure Stack, kan het uploaden van bepaalde bestanden als toevoeg-blobs mislukken.
* Nadat u op Annuleren hebt geklikt, kan het even duren voordat de taak is geannuleerd. Dit komt doordat we het werkings filter voor annuleren gebruiken dat [hier](https://github.com/Azure/azure-storage-node/issues/317)wordt beschreven.
* Als u het verkeerde pincode/smartcard certificaat kiest, moet u de computer opnieuw opstarten om deze beslissing te Storage Explorer vergren delen.
* Bij het wijzigen van de naam van blobs (afzonderlijk of binnen een nieuwe BLOB-container) worden geen moment opnamen bewaard. Alle andere eigenschappen en meta gegevens voor blobs, bestanden en entiteiten blijven behouden tijdens het wijzigen van de naam.
* Hoewel Azure Stack momenteel geen bestanden ondersteunt, wordt het knoop punt bestands shares nog steeds weer gegeven onder een Azure Stack opslag account dat is gekoppeld.
* De elektron-Shell die door Storage Explorer wordt gebruikt, heeft moeite met het versnellen van een GPU (graphics processing unit). Als Storage Explorer een leeg (leeg) hoofd venster wordt weer gegeven, kunt u proberen om Storage Explorer te starten vanaf de opdracht regel en de GPU-versnelling uit te scha kelen door de switch toe te voegen `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor Linux-gebruikers moet u [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0)installeren.
* Voor gebruikers op Ubuntu 14,04, moet u ervoor zorgen dat GCC up-to-date is. Dit kan worden gedaan door de volgende opdrachten uit te voeren en de computer vervolgens opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17,04 moet u GConf installeren. dit kunt u doen door de volgende opdrachten uit te voeren en vervolgens de computer opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-130"></a>Versie 1.3.0
07/09/2018

### <a name="new"></a>Nieuw
* Het is nu mogelijk om toegang te krijgen tot de $web containers die worden gebruikt door statische websites. Zo kunt u eenvoudig bestanden en mappen uploaden en beheren die worden gebruikt door uw website. [#223](https://github.com/Microsoft/AzureStorageExplorer/issues/223)
* De app-balk op macOS is opnieuw georganiseerd. Wijzigingen bevatten een menu bestand, wijzigingen in sneltoetsen en verschillende nieuwe opdrachten onder het app-menu. [#99](https://github.com/Microsoft/AzureStorageExplorer/issues/99)
* Het instantie-eind punt voor het aanmelden bij Azure de Amerikaanse overheid is gewijzigd in https://login.microsoftonline.us/
* Toegankelijkheid: wanneer een scherm lezer actief is, werkt de toetsenbord navigatie nu met de tabellen die worden gebruikt voor het weer geven van items aan de rechter kant. U kunt de pijl toetsen gebruiken om te navigeren in rijen en kolommen, op te geven om standaard acties aan te roepen, de context menu toets om het context menu voor een item te openen, en SHIFT of Control to MultiSelect. [#103](https://github.com/Microsoft/AzureStorageExplorer/issues/103)

### <a name="fixes"></a>Oplossingen
*  Op sommige computers nemen onderliggende processen veel tijd in beslag. Als dit gebeurt, wordt de fout ' er is een onderliggend proces niet op de juiste manier gestart ' weer gegeven. De tijd die is toegewezen voor een onderliggend proces om te beginnen, is nu verhoogd van 20 tot 90 seconden. Als dit probleem nog steeds van toepassing is, kunt u een opmerking over het probleem met de gekoppelde GitHub maken. [#281](https://github.com/Microsoft/AzureStorageExplorer/issues/281)
* Wanneer u een SAS gebruikt die geen lees machtigingen heeft, was het niet mogelijk om een grote BLOB te uploaden. De logica voor het uploaden is gewijzigd om in dit scenario te werken. [#305](https://github.com/Microsoft/AzureStorageExplorer/issues/305)
* Als u het niveau van open bare toegang instelt voor een container, worden alle toegangs beleid verwijderd en vice versa. Het niveau van open bare toegang en het toegangs beleid blijven behouden bij het instellen van een van de twee. [#197](https://github.com/Microsoft/AzureStorageExplorer/issues/197)
* ' AccessTierChangeTime ' is afgekapt in het dialoog venster Eigenschappen. Dit probleem is opgelost. [#145](https://github.com/Microsoft/AzureStorageExplorer/issues/145)
* Het voor voegsel Microsoft Azure Storage Explorer ontbreekt in het dialoog venster nieuwe map maken. Dit probleem is opgelost. [#299](https://github.com/Microsoft/AzureStorageExplorer/issues/299)
* Toegankelijkheid: het dialoog venster entiteit toevoegen is moeilijk te navigeren wanneer u Voice gebruikt. Er zijn verbeteringen aangebracht. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Toegankelijkheid: de achtergrond kleur van de knop samen vouwen/uitvouwen in het deel venster acties en eigenschappen is inconsistent met vergelijk bare UI-besturings elementen in hoog contrast Black-thema. De kleur is gewijzigd. [#123](https://github.com/Microsoft/AzureStorageExplorer/issues/123)
* Toegankelijkheid: in hoog contrast zwart thema is de focus stijl voor de knop X in het dialoog venster Eigenschappen niet zichtbaar. Dit probleem is opgelost. [#243](https://github.com/Microsoft/AzureStorageExplorer/issues/243)
* Toegankelijkheid: in de tabbladen acties en eigenschappen ontbreken verschillende aria-waarden die zijn opgetreden in een subpar van de scherm lezer. De ontbrekende Aria-waarden zijn nu toegevoegd. [#316](https://github.com/Microsoft/AzureStorageExplorer/issues/316)
* Toegankelijkheid: aan de linkerkant is geen aria met de waarde False opgegeven voor de samengevouwen structuur knooppunten. Dit probleem is opgelost. [#352](https://github.com/Microsoft/AzureStorageExplorer/issues/352)

### <a name="known-issues"></a>Bekende problemen
* Het ontkoppelen van een resource die is gekoppeld via SAS URI, zoals een BLOB-container, kan een fout veroorzaken die voor komt dat andere bijlagen correct worden weer gegeven. U kunt dit probleem omzeilen door het groeps knooppunt te vernieuwen. Raadpleeg [Dit probleem](https://github.com/Microsoft/AzureStorageExplorer/issues/537) voor meer informatie.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, kunt u zich mogelijk niet aanmelden. U kunt het probleem omzeilen door de inhoud van ~/te verwijderen. IdentityService/AadConfigurations. Als u dat wel doet, kunt u een opmerking over [Dit probleem](https://github.com/Microsoft/AzureStorageExplorer/issues/97)melden.
* Azurite heeft alle opslag-Api's nog niet volledig geïmplementeerd. Daarom kunnen er onverwachte fouten of gedrag zijn bij het gebruik van Azurite voor de ontwikkelings opslag.
* In zeldzame gevallen kan de structuur focus vastlopen op snelle toegang. Als u de focus wilt ontsteken, kunt u alle vernieuwen.
* Het uploaden van uw OneDrive-map werkt niet als gevolg van een fout in NodeJS. De fout is opgelost, maar is nog niet geïntegreerd in elektron.
* Als Azure Stack, kan het uploaden van bepaalde bestanden als toevoeg-blobs mislukken.
* Nadat u op Annuleren hebt geklikt, kan het even duren voordat de taak is geannuleerd. Dit komt doordat we het werkings filter voor annuleren gebruiken dat [hier](https://github.com/Azure/azure-storage-node/issues/317)wordt beschreven.
* Als u het verkeerde pincode/smartcard certificaat kiest, moet u de computer opnieuw opstarten om deze beslissing te Storage Explorer vergren delen.
* Bij het wijzigen van de naam van blobs (afzonderlijk of binnen een nieuwe BLOB-container) worden geen moment opnamen bewaard. Alle andere eigenschappen en meta gegevens voor blobs, bestanden en entiteiten blijven behouden tijdens het wijzigen van de naam.
* Azure Stack biedt geen ondersteuning voor de volgende functies en tijdens het werken met Azure Stack kunnen onverwachte fouten optreden:
   * Bestandsshares
   * Toegangslagen
   * Voorlopig verwijderen
* De elektron-Shell die door Storage Explorer wordt gebruikt, heeft moeite met het versnellen van een GPU (graphics processing unit). Als Storage Explorer een leeg (leeg) hoofd venster wordt weer gegeven, kunt u proberen om Storage Explorer te starten vanaf de opdracht regel en de GPU-versnelling uit te scha kelen door de switch toe te voegen `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor Linux-gebruikers moet u [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0)installeren.
* Voor gebruikers op Ubuntu 14,04, moet u ervoor zorgen dat GCC up-to-date is. Dit kan worden gedaan door de volgende opdrachten uit te voeren en de computer vervolgens opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17,04 moet u GConf installeren. dit kunt u doen door de volgende opdrachten uit te voeren en vervolgens de computer opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-120"></a>Versie 1.2.0
06/12/2018

### <a name="new"></a>Nieuw
* Als Storage Explorer geen abonnementen kunt laden vanuit een subset van uw tenants, worden alle geladen abonnementen weer gegeven samen met een fout bericht dat specifiek is voor de tenants die zijn mislukt. [#159](https://github.com/Microsoft/AzureStorageExplorer/issues/159)
* Als er in Windows een update beschikbaar is, kunt u nu ' bijwerken bij sluiten ' kiezen. Wanneer deze optie wordt gekozen, wordt het installatie programma voor de update uitgevoerd nadat u Storage Explorer hebt gesloten. [#21](https://github.com/Microsoft/AzureStorageExplorer/issues/21)
* De moment opname van de herstel bewerking is toegevoegd aan het context menu van de bestands share-editor bij het weer geven van een moment opname van een bestands share. [#131](https://github.com/Microsoft/AzureStorageExplorer/issues/131)
* De knop wachtrij wissen is nu altijd ingeschakeld. [#135](https://github.com/Microsoft/AzureStorageExplorer/issues/135)
* Ondersteuning voor aanmelding bij ADFS Azure Stack is weer ingeschakeld. Azure Stack versie 1804 of hoger is vereist. [#150](https://github.com/Microsoft/AzureStorageExplorer/issues/150)

### <a name="fixes"></a>Oplossingen
* Als u moment opnamen hebt bekeken voor een bestands share waarvan de naam een voor voegsel van een andere bestands share in hetzelfde opslag account was, worden de moment opnamen voor de andere bestands share ook weer gegeven. Dit probleem is opgelost. [#255](https://github.com/Microsoft/AzureStorageExplorer/issues/255)
* Wanneer een bestand wordt gekoppeld via SAS, zou de moment opname van de bestands share een fout veroorzaken. Dit probleem is opgelost. [#211](https://github.com/Microsoft/AzureStorageExplorer/issues/211)
* Bij het weer geven van moment opnamen voor een blob is de actie voor het promo veren van de moment opname ingeschakeld toen de basis-Blob en één moment opname werden geselecteerd. De actie is nu alleen ingeschakeld als er één moment opname is geselecteerd. [#230](https://github.com/Microsoft/AzureStorageExplorer/issues/230)
* Als één taak (zoals het downloaden van een blob) is gestart en later is mislukt, wordt deze niet automatisch opnieuw geprobeerd totdat u een andere taak van hetzelfde type hebt gestart. Alle taken moeten nu automatisch opnieuw worden uitgevoerd, ongeacht het aantal taken in de wachtrij.
* Editors die zijn geopend voor zojuist gemaakte BLOB-containers in GPV2-en Blob Storage-accounts hebben geen kolom met de Access-laag. Dit probleem is opgelost. [#109](https://github.com/Microsoft/AzureStorageExplorer/issues/109)
* Soms wordt een kolom met een Access-laag niet weer gegeven wanneer een opslag account of BLOB-container is gekoppeld via SAS. De kolom wordt nu altijd weer gegeven, maar met een lege waarde als er geen toegangs laag is ingesteld. [#160](https://github.com/Microsoft/AzureStorageExplorer/issues/160)
* Het instellen van de toegangs laag van een zojuist geüploade blok-blob is uitgeschakeld. Dit probleem is opgelost. [#171](https://github.com/Microsoft/AzureStorageExplorer/issues/171)
* Als de knop "tabblad openen" is aangeroepen met behulp van het toetsen bord, gaat de focus van het toetsen bord verloren. Nu wordt de focus verplaatst naar het tabblad dat is geopend. [#163](https://github.com/Microsoft/AzureStorageExplorer/issues/163)
* Voor een query in de Opbouwfunctie voor query's is Voice geen bruikbare beschrijving van de huidige operator geven. Het is nu meer beschrijvend. [#207](https://github.com/Microsoft/AzureStorageExplorer/issues/207)
* De paginerings koppelingen voor de verschillende editors zijn niet beschrijvend. Deze zijn zo gewijzigd dat ze meer beschrijvend zijn. [#205](https://github.com/Microsoft/AzureStorageExplorer/issues/205)
* In het dialoog venster entiteit toevoegen heeft Voice niet gekondigt van welke kolom een INPUT-element deel uitmaakt. De naam van de huidige kolom is nu opgenomen in de beschrijving van het element. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Keuze rondjes en selectie vakjes hebben geen zicht bare rand wanneer ze zijn gericht. Dit probleem is opgelost. [#237](https://github.com/Microsoft/AzureStorageExplorer/issues/237)

### <a name="known-issues"></a>Bekende problemen
* Wanneer u emulators gebruikt, zoals Azure Storage emulator of Azurite, moet u ze Luis teren naar verbindingen op hun standaard poorten. Als dat niet het geval is, kan Storage Explorer geen verbinding maken.
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, kunt u zich mogelijk niet aanmelden. U kunt het probleem omzeilen door de inhoud van ~/te verwijderen. IdentityService/AadConfigurations. Als u dat wel doet, kunt u een opmerking over [Dit probleem](https://github.com/Microsoft/AzureStorageExplorer/issues/97)melden.
* Azurite heeft alle opslag-Api's nog niet volledig geïmplementeerd. Daarom kunnen er onverwachte fouten of gedrag zijn bij het gebruik van Azurite voor de ontwikkelings opslag.
* In zeldzame gevallen kan de structuur focus vastlopen op snelle toegang. Als u de focus wilt ontsteken, kunt u alle vernieuwen.
* Het uploaden van uw OneDrive-map werkt niet als gevolg van een fout in NodeJS. De fout is opgelost, maar is nog niet geïntegreerd in elektron.
* Als Azure Stack, kan het uploaden van bepaalde bestanden als toevoeg-blobs mislukken.
* Nadat u op Annuleren hebt geklikt, kan het even duren voordat de taak is geannuleerd. Dit komt doordat we het werkings filter voor annuleren gebruiken dat [hier](https://github.com/Azure/azure-storage-node/issues/317)wordt beschreven.
* Als u het verkeerde pincode/smartcard certificaat kiest, moet u de computer opnieuw opstarten om deze beslissing te Storage Explorer vergren delen.
* Bij het wijzigen van de naam van blobs (afzonderlijk of binnen een nieuwe BLOB-container) worden geen moment opnamen bewaard. Alle andere eigenschappen en meta gegevens voor blobs, bestanden en entiteiten blijven behouden tijdens het wijzigen van de naam.
* Hoewel Azure Stack momenteel geen bestanden ondersteunt, wordt het knoop punt bestands shares nog steeds weer gegeven onder een Azure Stack opslag account dat is gekoppeld.
* De elektron-Shell die door Storage Explorer wordt gebruikt, heeft moeite met het versnellen van een GPU (graphics processing unit). Als Storage Explorer een leeg (leeg) hoofd venster wordt weer gegeven, kunt u proberen om Storage Explorer te starten vanaf de opdracht regel en de GPU-versnelling uit te scha kelen door de switch toe te voegen `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor Linux-gebruikers moet u [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0)installeren.
* Voor gebruikers op Ubuntu 14,04, moet u ervoor zorgen dat GCC up-to-date is. Dit kan worden gedaan door de volgende opdrachten uit te voeren en de computer vervolgens opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17,04 moet u GConf installeren. dit kunt u doen door de volgende opdrachten uit te voeren en vervolgens de computer opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-110"></a>Versie 1.1.0
05/09/2018

### <a name="new"></a>Nieuw
* Storage Explorer ondersteunt nu het gebruik van Azurite. Opmerking: de verbinding met Azurite wordt vastgelegd voor de standaard ontwikkelings eindpunten.
* Storage Explorer ondersteunt nu alleen toegangs lagen voor Blob-en GPV2-opslag accounts. Lees [hier](./storage/blobs/storage-blob-storage-tiers.md)meer over toegangs lagen.
* Een begin tijd is niet langer vereist bij het genereren van een SAS.

### <a name="fixes"></a>Oplossingen
* Het ophalen van abonnementen voor Amerikaanse overheids accounts is verbroken. Dit probleem is opgelost. [#61](https://github.com/Microsoft/AzureStorageExplorer/issues/61)
* De verloop tijd voor het toegangs beleid is onjuist opgeslagen. Dit probleem is opgelost. [#50](https://github.com/Microsoft/AzureStorageExplorer/issues/50)
* Bij het genereren van een SAS-URL voor een item in een container, wordt de naam van het item niet toegevoegd aan de URL. Dit probleem is opgelost. [#44](https://github.com/Microsoft/AzureStorageExplorer/issues/44)
* Wanneer u een SAS maakt, zijn de verloop tijden die zich in het verleden bevinden, soms de standaard waarde. Dit was het gevolg van Storage Explorer van de laatst gebruikte begin-en verloop tijd als standaard waarden. Telkens wanneer u het dialoog venster SAS opent, wordt er een nieuwe set standaard waarden gegenereerd. [#35](https://github.com/Microsoft/AzureStorageExplorer/issues/35)
* Bij het kopiëren tussen opslag accounts wordt een 24-uurs SAS gegenereerd. Als het kopiëren meer dan 24 uur duurt, mislukt de Kopieer bewerking. We hebben de SA'S voor de laatste week verhoogd, zodat de kans op het mislukken van een kopie door een verlopen SAS wordt verminderd. [#62](https://github.com/Microsoft/AzureStorageExplorer/issues/62)
* Voor sommige activiteiten is het klikken op annuleren niet altijd goed. Dit probleem is opgelost. [#125](https://github.com/Microsoft/AzureStorageExplorer/issues/125)
* Voor sommige activiteiten is de overdrachts snelheid onjuist. Dit probleem is opgelost. [#124](https://github.com/Microsoft/AzureStorageExplorer/issues/124)
* De spelling van ' Previous ' in het menu View is onjuist. Het is nu goed gespeld. [#71](https://github.com/Microsoft/AzureStorageExplorer/issues/71)
* De laatste pagina van het Windows-installatie programma had een knop Volgende. Het is gewijzigd in de knop volt ooien. [#70](https://github.com/Microsoft/AzureStorageExplorer/issues/70)
* De tab-focus is niet zichtbaar voor knoppen in dialoog vensters wanneer u het thema HC Black gebruikt. Het is nu zichtbaar. [#64](https://github.com/Microsoft/AzureStorageExplorer/issues/64)
* De behuizing van ' automatisch oplossen ' voor acties in het activiteiten logboek is onjuist. Het is nu correct. [#51](https://github.com/Microsoft/AzureStorageExplorer/issues/51)
* Wanneer u een entiteit uit een tabel verwijdert, wordt het dialoog venster met de vraag om bevestiging een fout pictogram weer gegeven. In het dialoog venster wordt nu een waarschuwings pictogram gebruikt. [#148](https://github.com/Microsoft/AzureStorageExplorer/issues/148)

### <a name="known-issues"></a>Bekende problemen
* Als u VS voor Mac gebruikt en ooit een aangepaste AAD-configuratie hebt gemaakt, kunt u zich mogelijk niet aanmelden. U kunt het probleem omzeilen door de inhoud van ~/te verwijderen. IdentityService/AadConfigurations. Als u dat wel doet, kunt u een opmerking over [Dit probleem](https://github.com/Microsoft/AzureStorageExplorer/issues/97)melden.
* Azurite heeft alle opslag-Api's nog niet volledig geïmplementeerd. Daarom kunnen er onverwachte fouten of gedrag zijn bij het gebruik van Azurite voor de ontwikkelings opslag.
* In zeldzame gevallen kan de structuur focus vastlopen op snelle toegang. Als u de focus wilt ontsteken, kunt u alle vernieuwen.
* Het uploaden van uw OneDrive-map werkt niet als gevolg van een fout in NodeJS. De fout is opgelost, maar is nog niet geïntegreerd in elektron.
* Als Azure Stack, kan het uploaden van bepaalde bestanden als toevoeg-blobs mislukken.
* Nadat u op Annuleren hebt geklikt, kan het even duren voordat de taak is geannuleerd. Dit komt doordat we het werkings filter voor annuleren gebruiken dat [hier](https://github.com/Azure/azure-storage-node/issues/317)wordt beschreven.
* Als u het verkeerde pincode/smartcard certificaat kiest, moet u de computer opnieuw opstarten om deze beslissing te Storage Explorer vergren delen.
* Bij het wijzigen van de naam van blobs (afzonderlijk of binnen een nieuwe BLOB-container) worden geen moment opnamen bewaard. Alle andere eigenschappen en meta gegevens voor blobs, bestanden en entiteiten blijven behouden tijdens het wijzigen van de naam.
* Hoewel Azure Stack momenteel geen bestanden ondersteunt, wordt het knoop punt bestands shares nog steeds weer gegeven onder een Azure Stack opslag account dat is gekoppeld.
* De elektron-Shell die door Storage Explorer wordt gebruikt, heeft moeite met het versnellen van een GPU (graphics processing unit). Als Storage Explorer een leeg (leeg) hoofd venster wordt weer gegeven, kunt u proberen om Storage Explorer te starten vanaf de opdracht regel en de GPU-versnelling uit te scha kelen door de switch toe te voegen `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor Linux-gebruikers moet u [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0)installeren.
* Voor gebruikers op Ubuntu 14,04, moet u ervoor zorgen dat GCC up-to-date is. Dit kan worden gedaan door de volgende opdrachten uit te voeren en de computer vervolgens opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17,04 moet u GConf installeren. dit kunt u doen door de volgende opdrachten uit te voeren en vervolgens de computer opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-100"></a>Versie 1.0.0
04/16/2018

### <a name="new"></a>Nieuw
* Verbeterde verificatie waarmee Storage Explorer hetzelfde account archief kunt gebruiken als Visual Studio 2017. Als u deze functie wilt gebruiken, moet u zich opnieuw aanmelden bij uw accounts en uw gefilterde abonnementen opnieuw instellen.
* Voor Azure Stack-accounts die door AAD worden ondersteund, haalt Storage Explorer nu Azure Stack-abonnementen op wanneer target Azure Stack is ingeschakeld. U hoeft geen aangepaste aanmeldings omgeving meer te maken.
* Er zijn enkele snelkoppelingen toegevoegd om sneller te kunnen navigeren. Dit omvat het wisselen van verschillende deel Vensters en het verplaatsen tussen editors. Zie het menu weer geven voor meer informatie.
* Storage Explorer feedback bevindt zich nu op GitHub. U kunt onze pagina met problemen bereiken door te klikken op de knop feedback linksonder of door naar te gaan [https://github.com/Microsoft/AzureStorageExplorer/issues](https://github.com/Microsoft/AzureStorageExplorer/issues) . U kunt suggesties doen, problemen melden, vragen stellen of een andere vorm van feedback geven.
* Als u problemen ondervindt met TLS/SSL-certificaten en niet het foutieve certificaat kan vinden, kunt u Storage Explorer nu starten vanaf de opdracht regel met de `--ignore-certificate-errors` vlag. Wanneer met deze markering wordt gestart, negeert Storage Explorer TLS/SSL-certificaat fouten.
* Er is nu een download optie in het context menu voor Blob-en file-items.
* Verbeterde ondersteuning voor toegankelijkheid en scherm lezers. Als u afhankelijk bent van toegankelijkheids functies, raadpleegt u de [toegankelijkheids documentatie](./vs-azure-tools-storage-explorer-accessibility.md) voor meer informatie.
* Storage Explorer maakt nu gebruik van elektroden 1.8.3

### <a name="breaking-changes"></a>Wijzigingen die fouten veroorzaken
* Storage Explorer is overgeschakeld naar een nieuwe verificatie bibliotheek. Als onderdeel van de switch naar de bibliotheek moet u zich opnieuw aanmelden bij uw accounts en uw gefilterde abonnementen opnieuw instellen
* De methode die wordt gebruikt voor het versleutelen van gevoelige gegevens is gewijzigd. Dit kan leiden tot een aantal van uw snelle toegangs items die opnieuw moeten worden toegevoegd en/of een aantal van de resources die moeten worden gekoppeld.

### <a name="fixes"></a>Oplossingen
* Bij sommige gebruikers achter proxy's zouden groepslid BLOB-uploads of down loads worden onderbroken door het fout bericht ' kan niet worden opgelost '. Dit probleem is opgelost.
* Als er tijdens het gebruik van een directe koppeling een aanmelding is vereist, klikt u op de prompt aanmelden om een leeg dialoog venster te openen. Dit probleem is opgelost.
* Als Storage Explorer in Linux niet kan worden gestart vanwege een crash van het GPU-proces, wordt u nu op de hoogte gesteld van de crash, wordt u geadviseerd de schakel optie--uitschakelen-GPU te gebruiken en wordt Storage Explorer vervolgens automatisch opnieuw opgestart terwijl de switch is ingeschakeld.
* Ongeldige beleids regels voor toegang tot de identiteit in het dialoog venster toegangs beleid. Ongeldige toegangs beleid-Id's worden nu in het rood beschreven om meer inzicht te krijgen.
* Het activiteiten logboek zou soms grote hoeveel heden witruimte hebben tussen de verschillende onderdelen van een activiteit. Dit probleem is opgelost.
* Als u in de query-editor van de tabel een time stamp-component in een ongeldige status hebt gelaten en vervolgens probeert een andere component te wijzigen, wordt de editor geblokkeerd. Met de editor wordt de time stamp-component nu teruggezet naar de laatste geldige status wanneer een wijziging in een andere component wordt gedetecteerd.
* Als u de zoek opdracht hebt gepauzeerd terwijl u in de structuur weergave typt, wordt de zoek opdracht gestart en wordt de focus vanuit het tekstvak gestolen. U moet de zoek opdracht expliciet starten door op de Enter-toets te drukken of door te klikken op de knop Zoeken starten.
* De opdracht ' Get Shared Access Signature ' wordt soms uitgeschakeld wanneer met de rechter muisknop op een bestand in een bestands share wordt geklikt. Dit probleem is opgelost.
* Als het resource structuur knooppunt met de focus tijdens de zoek opdracht is gefilterd, kunt u niet naar de resource structuur gaan en de pijl toetsen gebruiken om door de resource structuur te navigeren. Als het knoop punt resource structuur met focus is verborgen, wordt het eerste knoop punt in de resource structuur automatisch gefocust.
* Soms is een extra scheidings teken zichtbaar in de editor-werk balk. Dit probleem is opgelost.
* Het tekstvak voor de breadcrumb wordt soms overgelopen. Dit probleem is opgelost.
* De BLOB-en bestands share-editors worden soms voortdurend vernieuwd bij het uploaden van veel bestanden tegelijk. Dit probleem is opgelost.
* De functie voor het beheren van de map heeft geen doel in de beheer weergave voor moment opnamen van bestands shares. Het is nu uitgeschakeld.
* In Linux werd het menu bestand niet weer gegeven. Dit probleem is opgelost.
* Wanneer u een map uploadt naar een bestands share, wordt standaard alleen de inhoud van de map geüpload. Nu is het standaard gedrag om de inhoud van de map te uploaden naar een overeenkomende map in de bestands share.
* De volg orde van knoppen in verschillende dialoog vensters is omgekeerd. Dit probleem is opgelost.
* Diverse beveiligings oplossingen.

### <a name="known-issues"></a>Bekende problemen
* In zeldzame gevallen kan de structuur focus vastlopen op snelle toegang. Als u de focus wilt ontsteken, kunt u alle vernieuwen.
* Als Azure Stack, kan het uploaden van bepaalde bestanden als toevoeg-blobs mislukken.
* Nadat u op Annuleren hebt geklikt, kan het even duren voordat de taak is geannuleerd. Dit komt doordat we het werkings filter voor annuleren gebruiken dat hier wordt beschreven.
* Als u het verkeerde pincode/smartcard certificaat kiest, moet u de computer opnieuw opstarten om deze beslissing te Storage Explorer vergren delen.
* Bij het wijzigen van de naam van blobs (afzonderlijk of binnen een nieuwe BLOB-container) worden geen moment opnamen bewaard. Alle andere eigenschappen en meta gegevens voor blobs, bestanden en entiteiten blijven behouden tijdens het wijzigen van de naam.
* Hoewel Azure Stack momenteel geen bestanden ondersteunt, wordt het knoop punt bestands shares nog steeds weer gegeven onder een Azure Stack opslag account dat is gekoppeld.
* De elektron-Shell die door Storage Explorer wordt gebruikt, heeft moeite met het versnellen van een GPU (graphics processing unit). Als Storage Explorer een leeg (leeg) hoofd venster wordt weer gegeven, kunt u proberen om Storage Explorer te starten vanaf de opdracht regel en de GPU-versnelling uit te scha kelen door de switch toe te voegen `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor Linux-gebruikers moet u [.net Core 2,0](https://dotnet.microsoft.com/download/dotnet-core/2.0)installeren.
* Voor gebruikers op Ubuntu 14,04, moet u ervoor zorgen dat GCC up-to-date is. Dit kan worden gedaan door de volgende opdrachten uit te voeren en de computer vervolgens opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17,04 moet u GConf installeren. dit kunt u doen door de volgende opdrachten uit te voeren en vervolgens de computer opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-096"></a>Versie 0.9.6
02/28/2018

### <a name="fixes"></a>Oplossingen
* Er is een probleem opgetreden waardoor verwachte blobs/bestanden niet worden weer gegeven in de editor. Dit probleem is opgelost.
* Er is een probleem opgetreden bij het overschakelen tussen momentopname weergaven om items onjuist weer te geven. Dit probleem is opgelost.

### <a name="known-issues"></a>Bekende problemen
* Storage Explorer geen ADFS-accounts ondersteunt.
* Als Azure Stack, kan het uploaden van bepaalde bestanden als toevoeg-blobs mislukken.
* Nadat u op Annuleren hebt geklikt, kan het even duren voordat de taak is geannuleerd. Dit komt doordat we het werkings filter voor annuleren gebruiken dat [hier](https://github.com/Azure/azure-storage-node/issues/317)wordt beschreven.
* Als u het verkeerde pincode/smartcard certificaat kiest, moet u de computer opnieuw opstarten om deze beslissing te Storage Explorer vergren delen.
* In het deel venster account instellingen kan worden aangegeven dat u de referenties opnieuw moet invoeren om abonnementen te filteren.
* Bij het wijzigen van de naam van blobs (afzonderlijk of binnen een nieuwe BLOB-container) worden geen moment opnamen bewaard. Alle andere eigenschappen en meta gegevens voor blobs, bestanden en entiteiten blijven behouden tijdens het wijzigen van de naam.
* Hoewel Azure Stack momenteel geen bestanden ondersteunt, wordt het knoop punt bestands shares nog steeds weer gegeven onder een Azure Stack opslag account dat is gekoppeld.
* De elektron-Shell die door Storage Explorer wordt gebruikt, heeft moeite met het versnellen van een GPU (graphics processing unit). Als Storage Explorer een leeg (leeg) hoofd venster wordt weer gegeven, kunt u proberen om Storage Explorer te starten vanaf de opdracht regel en de GPU-versnelling uit te scha kelen door de switch toe te voegen `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor gebruikers op Ubuntu 14,04, moet u ervoor zorgen dat GCC up-to-date is. Dit kan worden gedaan door de volgende opdrachten uit te voeren en de computer vervolgens opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17,04 moet u GConf installeren. dit kunt u doen door de volgende opdrachten uit te voeren en vervolgens de computer opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-095"></a>Versie 0.9.5
02/06/2018

### <a name="new"></a>Nieuw

* Ondersteuning voor moment opnamen van bestands shares:
    * Moment opnamen voor uw bestands shares maken en beheren.
    * U kunt gemakkelijk scha kelen tussen de weer gaven van moment opnamen van uw bestands shares tijdens het verkennen.
    * Herstel vorige versies van uw bestanden.
* Preview-ondersteuning voor Azure Data Lake Store:
    * Maak verbinding met uw ADLS-resources tussen meerdere accounts.
    * Maak verbinding met en deel ADLS-resources met behulp van ADL-Uri's.
    * Basis bewerkingen voor bestanden en mappen recursief uitvoeren.
    * Afzonderlijke mappen vastmaken aan snelle toegang.
    * Statistieken van mappen weer geven.

### <a name="fixes"></a>Oplossingen
* Verbeteringen in de prestaties van het opstarten.
* Diverse oplossingen voor problemen.

### <a name="known-issues"></a>Bekende problemen
* Storage Explorer geen ADFS-accounts ondersteunt.
* Als Azure Stack, kan het uploaden van bepaalde bestanden als toevoeg-blobs mislukken.
* Nadat u op Annuleren hebt geklikt, kan het even duren voordat de taak is geannuleerd. Dit komt doordat we het werkings filter voor annuleren gebruiken dat hier wordt beschreven.
* Als u het verkeerde pincode/smartcard certificaat kiest, moet u de computer opnieuw opstarten om deze beslissing te Storage Explorer vergren delen.
* In het deel venster account instellingen kan worden aangegeven dat u de referenties opnieuw moet invoeren om abonnementen te filteren.
* Bij het wijzigen van de naam van blobs (afzonderlijk of binnen een nieuwe BLOB-container) worden geen moment opnamen bewaard. Alle andere eigenschappen en meta gegevens voor blobs, bestanden en entiteiten blijven behouden tijdens het wijzigen van de naam.
* Hoewel Azure Stack momenteel geen bestanden ondersteunt, wordt het knoop punt bestands shares nog steeds weer gegeven onder een Azure Stack opslag account dat is gekoppeld.
* De elektron-Shell die door Storage Explorer wordt gebruikt, heeft moeite met het versnellen van een GPU (graphics processing unit). Als Storage Explorer een leeg (leeg) hoofd venster wordt weer gegeven, kunt u proberen om Storage Explorer te starten vanaf de opdracht regel en de GPU-versnelling uit te scha kelen door de switch toe te voegen `--disable-gpu` :

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Voor gebruikers op Ubuntu 14,04, moet u ervoor zorgen dat GCC up-to-date is. Dit kan worden gedaan door de volgende opdrachten uit te voeren en de computer vervolgens opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17,04 moet u GConf installeren. dit kunt u doen door de volgende opdrachten uit te voeren en vervolgens de computer opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-094-and-093"></a>Versie 0.9.4 en 0.9.3
01/21/2018

### <a name="new"></a>Nieuw
* Uw bestaande Storage Explorer-venster wordt opnieuw gebruikt wanneer:
    * Direct koppelingen worden geopend die zijn gegenereerd in Storage Explorer.
    * Storage Explorer openen vanuit de portal.
    * Storage Explorer openen vanuit Azure Storage VS code-extensie (binnenkort beschikbaar).
* De mogelijkheid om een nieuw Storage Explorer venster in Storage Explorer te openen, is toegevoegd.
    * Voor Windows is de optie Nieuw venster in het menu bestand en in het context menu van de taak balk.
    * Voor Mac is er een optie ' nieuw venster ' in het menu van de app.

### <a name="fixes"></a>Oplossingen
* Een beveiligings probleem opgelost. Voer zo snel mogelijk een upgrade naar 0.9.4 uit.
* Oude activiteiten zijn niet op de juiste wijze opgeruimd. Dit heeft gevolgen voor de prestaties van langlopende taken. Ze worden nu goed opgeruimd.
* Acties waarbij een groot aantal bestanden en mappen worden betrokken, zouden af en toe ertoe leiden dat Storage Explorer worden geblokkeerd. Aanvragen voor Azure voor bestands shares worden nu beperkt om het gebruik van de systeem resources te beperken.

### <a name="known-issues"></a>Bekende problemen
* Storage Explorer geen ADFS-accounts ondersteunt.
* Sneltoetsen voor ' View Explorer ' en ' account beheer weer geven ' moeten respectievelijk Ctrl/Cmd + Shift + E en Ctrl/Cmd + Shift + A zijn.
* Als Azure Stack, kan het uploaden van bepaalde bestanden als toevoeg-blobs mislukken.
* Nadat u op Annuleren hebt geklikt, kan het even duren voordat de taak is geannuleerd. Dit komt doordat we het werkings filter voor annuleren gebruiken dat hier wordt beschreven.
* Als u het verkeerde pincode/smartcard certificaat kiest, moet u de computer opnieuw opstarten om deze beslissing te Storage Explorer vergren delen.
* In het deel venster account instellingen kan worden aangegeven dat u de referenties opnieuw moet invoeren om abonnementen te filteren.
* Bij het wijzigen van de naam van blobs (afzonderlijk of binnen een nieuwe BLOB-container) worden geen moment opnamen bewaard. Alle andere eigenschappen en meta gegevens voor blobs, bestanden en entiteiten blijven behouden tijdens het wijzigen van de naam.
* Hoewel Azure Stack momenteel geen bestanden ondersteunt, wordt het knoop punt bestands shares nog steeds weer gegeven onder een Azure Stack opslag account dat is gekoppeld.
* De elektron-Shell die door Storage Explorer wordt gebruikt, heeft moeite met het versnellen van een GPU (graphics processing unit). Als Storage Explorer een leeg (leeg) hoofd venster wordt weer gegeven, kunt u proberen om Storage Explorer te starten vanaf de opdracht regel en de GPU-versnelling uit te scha kelen door de switch toe te voegen `--disable-gpu` :

    ```
    ./StorageExplorer --disable-gpu
    ```

* Voor gebruikers op Ubuntu 14,04, moet u ervoor zorgen dat GCC up-to-date is. Dit kan worden gedaan door de volgende opdrachten uit te voeren en de computer vervolgens opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17,04 moet u GConf installeren. dit kunt u doen door de volgende opdrachten uit te voeren en vervolgens de computer opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-092"></a>Versie 0.9.2
11/01/2017

### <a name="hotfixes"></a>Hotfixes
* Er zijn onverwachte gegevens wijzigingen mogelijk bij het bewerken van EDM. DateTime-waarden voor tabel entiteiten, afhankelijk van de lokale tijd zone. In de editor wordt nu gebruikgemaakt van een tekst zonder opmaak, waardoor de waarden van de EDM. DateTime nauw keuriger consistent zijn.
* Het uploaden/downloaden van een groep blobs wanneer deze is gekoppeld aan de naam en sleutel, wordt niet gestart. Dit probleem is opgelost.
* Voorheen Storage Explorer alleen u gevraagd om een verlopen account opnieuw te verifiëren als een of meer abonnementen van het account zijn geselecteerd. U wordt Storage Explorer nu gevraagd of het account volledig is gefilterd.
* Het domein met eind punten voor de Amerikaanse overheid van Azure is onjuist. Het is opgelost.
* De knop Toep assen in het deel venster accounts beheren is soms moeilijk te klikken. Dit mag niet langer gebeuren.

### <a name="new"></a>Nieuw
* Preview-ondersteuning voor Azure Cosmos DB:
    * [Online documentatie](./cosmos-db/storage-explorer.md)
    * Data bases en verzamelingen maken
    * Gegevens verwerken
    * Documenten opvragen, maken of verwijderen
    * Opgeslagen procedures, door de gebruiker gedefinieerde functies of triggers bijwerken
    * Verbindings reeksen gebruiken om verbinding te maken met uw data bases en deze te beheren
* Verbeterde prestaties van het uploaden/downloaden van veel kleine blobs.
* Er is een actie ' opnieuw uitvoeren ' toegevoegd als er fouten zijn in een BLOB-upload groep of BLOB-Download groep.
* Storage Explorer onderbreekt nu de herhaling tijdens het uploaden/downloaden van de BLOB als de netwerk verbinding wordt gedetecteerd. U kunt vervolgens de iteratie hervatten zodra de netwerk verbinding is hersteld.
* De mogelijkheid om de tabbladen ' Alles sluiten ', ' anderen sluiten ' en ' sluiten ' toe te voegen, is toegevoegd via het context menu.
* Storage Explorer maakt nu gebruik van systeem eigen dialoog vensters en systeem eigen context menu's.
* Storage Explorer is nu toegankelijker. De verbeteringen zijn onder andere:
    * Verbeterde ondersteuning voor scherm lezers, voor NVDA in Windows en voor Voice op Mac
    * Verbeterd hoog contrast
    * Problemen met toetsen bord en toetsenbord focus

### <a name="fixes"></a>Oplossingen
* Als u een blob met een ongeldige Windows-bestands naam hebt geopend of gedownload, mislukt de bewerking. Storage Explorer detecteert nu of de naam van een BLOB ongeldig is en vraagt u of u deze wilt coderen of de BLOB wilt overs Laan. Storage Explorer detecteert ook of een bestands naam moet worden gecodeerd en wordt u gevraagd of u deze wilt decoderen voordat u deze uploadt.
* Tijdens het uploaden van de BLOB wordt de editor voor de doel-BLOB-container soms niet goed vernieuwd. Dit probleem is opgelost.
* De ondersteuning voor verschillende vormen van verbindings reeksen en SAS Uri's teruggedraaide. We hebben alle bekende problemen behandeld, maar u kunt feedback verzenden als u verdere problemen ondervindt.
* De update melding is verbroken voor sommige gebruikers in 0.9.0. Dit probleem is opgelost en voor de fouten die worden beïnvloed door de fout, kunt u de meest recente versie van Storage Explorer [hier](https://azure.microsoft.com/features/storage-explorer/)hand matig downloaden.

### <a name="known-issues"></a>Bekende problemen
* Storage Explorer geen ADFS-accounts ondersteunt.
* Sneltoetsen voor ' View Explorer ' en ' account beheer weer geven ' moeten respectievelijk Ctrl/Cmd + Shift + E en Ctrl/Cmd + Shift + A zijn.
* Als Azure Stack, kan het uploaden van bepaalde bestanden als toevoeg-blobs mislukken.
* Nadat u op Annuleren hebt geklikt, kan het even duren voordat de taak is geannuleerd. Dit komt doordat we het werkings filter voor annuleren gebruiken dat hier wordt beschreven.
* Als u het verkeerde pincode/smartcard certificaat kiest, moet u de computer opnieuw opstarten om deze beslissing te Storage Explorer vergren delen.
* In het deel venster account instellingen kan worden aangegeven dat u de referenties opnieuw moet invoeren om abonnementen te filteren.
* Bij het wijzigen van de naam van blobs (afzonderlijk of binnen een nieuwe BLOB-container) worden geen moment opnamen bewaard. Alle andere eigenschappen en meta gegevens voor blobs, bestanden en entiteiten blijven behouden tijdens het wijzigen van de naam.
* Hoewel Azure Stack momenteel geen bestanden ondersteunt, wordt het knoop punt bestands shares nog steeds weer gegeven onder een Azure Stack opslag account dat is gekoppeld.
* De elektron-Shell die door Storage Explorer wordt gebruikt, heeft moeite met het versnellen van een GPU (graphics processing unit). Als Storage Explorer een leeg (leeg) hoofd venster wordt weer gegeven, kunt u proberen om Storage Explorer te starten vanaf de opdracht regel en de GPU-versnelling uit te scha kelen door de switch toe te voegen `--disable-gpu` :

    ```
    ./StorageExplorer --disable-gpu
    ```

* Voor gebruikers op Ubuntu 14,04, moet u ervoor zorgen dat GCC up-to-date is. Dit kan worden gedaan door de volgende opdrachten uit te voeren en de computer vervolgens opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17,04 moet u GConf installeren. dit kunt u doen door de volgende opdrachten uit te voeren en vervolgens de computer opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-091-and-090"></a>Versie 0.9.1 tot en en 0.9.0
10/20/2017
### <a name="new"></a>Nieuw
* Preview-ondersteuning voor Azure Cosmos DB:
    * [Online documentatie](./cosmos-db/storage-explorer.md)
    * Data bases en verzamelingen maken
    * Gegevens verwerken
    * Documenten opvragen, maken of verwijderen
    * Opgeslagen procedures, door de gebruiker gedefinieerde functies of triggers bijwerken
    * Verbindings reeksen gebruiken om verbinding te maken met uw data bases en deze te beheren
* Verbeterde prestaties van het uploaden/downloaden van veel kleine blobs.
* Er is een actie ' opnieuw uitvoeren ' toegevoegd als er fouten zijn in een BLOB-upload groep of BLOB-Download groep.
* Storage Explorer onderbreekt nu de herhaling tijdens het uploaden/downloaden van de BLOB als de netwerk verbinding wordt gedetecteerd. U kunt vervolgens de iteratie hervatten zodra de netwerk verbinding is hersteld.
* De mogelijkheid om de tabbladen ' Alles sluiten ', ' anderen sluiten ' en ' sluiten ' toe te voegen, is toegevoegd via het context menu.
* Storage Explorer maakt nu gebruik van systeem eigen dialoog vensters en systeem eigen context menu's.
* Storage Explorer is nu toegankelijker. De verbeteringen zijn onder andere:
    * Verbeterde ondersteuning voor scherm lezers, voor NVDA in Windows en voor Voice op Mac
    * Verbeterd hoog contrast
    * Problemen met toetsen bord en toetsenbord focus

### <a name="fixes"></a>Oplossingen
* Als u een blob met een ongeldige Windows-bestands naam hebt geopend of gedownload, mislukt de bewerking. Storage Explorer detecteert nu of de naam van een BLOB ongeldig is en vraagt u of u deze wilt coderen of de BLOB wilt overs Laan. Storage Explorer detecteert ook of een bestands naam moet worden gecodeerd en wordt u gevraagd of u deze wilt decoderen voordat u deze uploadt.
* Tijdens het uploaden van de BLOB wordt de editor voor de doel-BLOB-container soms niet goed vernieuwd. Dit probleem is opgelost.
* De ondersteuning voor verschillende vormen van verbindings reeksen en SAS Uri's teruggedraaide. We hebben alle bekende problemen behandeld, maar u kunt feedback verzenden als u verdere problemen ondervindt.
* De update melding is verbroken voor sommige gebruikers in 0.9.0. Dit probleem is opgelost en voor de fouten die worden beïnvloed door de fout, kunt u de meest recente versie van Storage Explorer [hier](https://azure.microsoft.com/features/storage-explorer/) hand matig downloaden

### <a name="known-issues"></a>Bekende problemen
* Storage Explorer geen ADFS-accounts ondersteunt.
* Sneltoetsen voor ' View Explorer ' en ' account beheer weer geven ' moeten respectievelijk Ctrl/Cmd + Shift + E en Ctrl/Cmd + Shift + A zijn.
* Als Azure Stack, kan het uploaden van bepaalde bestanden als toevoeg-blobs mislukken.
* Nadat u op Annuleren hebt geklikt, kan het even duren voordat de taak is geannuleerd. Dit komt doordat we het werkings filter voor annuleren gebruiken dat hier wordt beschreven.
* Als u het verkeerde pincode/smartcard certificaat kiest, moet u de computer opnieuw opstarten om deze beslissing te Storage Explorer vergren delen.
* In het deel venster account instellingen kan worden aangegeven dat u de referenties opnieuw moet invoeren om abonnementen te filteren.
* Bij het wijzigen van de naam van blobs (afzonderlijk of binnen een nieuwe BLOB-container) worden geen moment opnamen bewaard. Alle andere eigenschappen en meta gegevens voor blobs, bestanden en entiteiten blijven behouden tijdens het wijzigen van de naam.
* Hoewel Azure Stack momenteel geen bestanden ondersteunt, wordt het knoop punt bestands shares nog steeds weer gegeven onder een Azure Stack opslag account dat is gekoppeld.
* De elektron-Shell die door Storage Explorer wordt gebruikt, heeft moeite met het versnellen van een GPU (graphics processing unit). Als Storage Explorer een leeg (leeg) hoofd venster wordt weer gegeven, kunt u proberen om Storage Explorer te starten vanaf de opdracht regel en de GPU-versnelling uit te scha kelen door de switch toe te voegen `--disable-gpu` :

    ```
    ./StorageExplorer --disable-gpu
    ```

* Voor gebruikers op Ubuntu 14,04, moet u ervoor zorgen dat GCC up-to-date is. Dit kan worden gedaan door de volgende opdrachten uit te voeren en de computer vervolgens opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17,04 moet u GConf installeren. dit kunt u doen door de volgende opdrachten uit te voeren en vervolgens de computer opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-0816"></a>Versie 0.8.16
8/21/2017

### <a name="new"></a>Nieuw
* Wanneer u een BLOB opent, wordt u door Storage Explorer gevraagd het gedownloade bestand te uploaden als er een wijziging wordt gedetecteerd
* Verbeterde Azure Stack-aanmeldings ervaring
* Verbeterde prestaties van het uploaden/downloaden van veel kleine bestanden tegelijk


### <a name="fixes"></a>Oplossingen
* Voor sommige BLOB-typen wordt bij het uploaden van een upload conflict als gevolg hiervan dat de upload opnieuw wordt gestart.
* In versie 0.8.15 worden uploads soms gestopt om 99%.
* Als u bestanden uploadt naar een bestands share en u ervoor hebt gekozen om te uploaden naar een map die nog niet bestaat, mislukt de upload.
* Storage Explorer heeft onjuist tijds tempels gegenereerd voor hand tekeningen voor gedeelde toegang en tabel query's.


### <a name="known-issues"></a>Bekende problemen
* Het gebruik van een naam en Key-connection string werkt momenteel niet. Deze wordt in de volgende release opgelost. Tot slot kunt u de koppeling met de naam en de sleutel gebruiken.
* Als u een bestand met een ongeldige Windows-bestands naam probeert te openen, treedt er een fout op in een bestand niet gevonden.
* Nadat u op Annuleren hebt geklikt, kan het even duren voordat de taak is geannuleerd. Dit is een beperking van de Azure Storage knooppunt bibliotheek.
* Nadat het uploaden van een blob is voltooid, wordt het tabblad dat het uploaden heeft gestart, vernieuwd. Dit is een wijziging ten opzichte van het vorige gedrag en leidt er ook toe dat u terugkeert naar de hoofdmap van de container waarin u zich bevindt.
* Als u het verkeerde pincode/smartcard certificaat kiest, moet u de computer opnieuw opstarten om deze beslissing te Storage Explorer vergren delen.
* In het deel venster account instellingen kan worden aangegeven dat u de referenties opnieuw moet invoeren om abonnementen te filteren.
* Bij het wijzigen van de naam van blobs (afzonderlijk of binnen een nieuwe BLOB-container) worden geen moment opnamen bewaard. Alle andere eigenschappen en meta gegevens voor blobs, bestanden en entiteiten blijven behouden tijdens het wijzigen van de naam.
* Hoewel Azure Stack momenteel geen bestanden ondersteunt, wordt het knoop punt bestands shares nog steeds weer gegeven onder een Azure Stack opslag account dat is gekoppeld.
* Voor gebruikers op Ubuntu 14,04, moet u ervoor zorgen dat GCC up-to-date is. Dit kan worden gedaan door de volgende opdrachten uit te voeren en de computer vervolgens opnieuw op te starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Voor gebruikers op Ubuntu 17,04 moet u GConf installeren. dit kunt u doen door de volgende opdrachten uit te voeren en vervolgens de computer opnieuw op te starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

### <a name="version-0814"></a>Versie 0.8.14
06/22/2017

### <a name="new"></a>Nieuw

* Bijgewerkte elektro versie naar 1.7.2 om te kunnen profiteren van verschillende essentiële beveiligings updates
* U kunt nu snel toegang krijgen tot de online probleemoplossings gids vanuit het menu Help
* [Gids][2] voor het oplossen van problemen Storage Explorer
* [Instructies][3] voor het maken van verbinding met een Azure stack-abonnement

### <a name="known-issues"></a>Bekende problemen

* De knoppen in het dialoog venster map verwijderen worden niet geregistreerd bij de muis klikken op Linux. tijdelijke oplossing is om de Enter-toets te gebruiken
* Als u het verkeerde pincode/smartcard certificaat kiest, moet u de computer opnieuw opstarten om de beslissing Storage Explorer vergren delen
* Wanneer er meer dan drie groepen blobs of bestanden tegelijk worden geüpload, kunnen er fouten optreden
* In het deel venster account instellingen kan worden aangegeven dat u de referenties opnieuw moet invoeren om abonnementen te filteren
* Bij het wijzigen van de naam van blobs (afzonderlijk of binnen een nieuwe BLOB-container) worden geen moment opnamen bewaard. Alle andere eigenschappen en meta gegevens voor blobs, bestanden en entiteiten blijven behouden tijdens het wijzigen van de naam.
* Hoewel Azure Stack momenteel geen bestands shares ondersteunt, wordt het knoop punt bestands shares nog steeds weer gegeven onder een Azure Stack opslag account dat is gekoppeld.
* Ubuntu 14,04 installatie moet gcc-versie bijgewerkt of bijgewerkt zijn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>Versie 0.8.13
12/05/2017

#### <a name="new"></a>Nieuw

* [Gids][2] voor het oplossen van problemen Storage Explorer
* [Instructies][3] voor het maken van verbinding met een Azure stack-abonnement

#### <a name="fixes"></a>Oplossingen

* Opgelost: het uploaden van bestanden had een hoge kans om een geheugen fout te veroorzaken
* Opgelost: u kunt zich nu aanmelden met een pincode of Smart Card
* Opgelost: Open in portal werkt nu met Azure China 21Vianet, Azure Duitsland, Azure Amerikaanse overheid en Azure Stack
* Opgelost: tijdens het uploaden van een map naar een BLOB-container treedt er soms een ' ongeldige bewerking ' op
* Opgelost: Alles selecteren is uitgeschakeld tijdens het beheren van moment opnamen
* Opgelost: de meta gegevens van de basis-BLOB worden mogelijk overschreven nadat de eigenschappen van de moment opnamen zijn weer gegeven

#### <a name="known-issues"></a>Bekende problemen

* Als u het verkeerde pincode/smartcard certificaat kiest, moet u de computer opnieuw opstarten om de beslissing Storage Explorer vergren delen
* Tijdens het in-of uitzoomen kan het zoom niveau tijdelijk opnieuw worden ingesteld op het standaard niveau
* Wanneer er meer dan drie groepen blobs of bestanden tegelijk worden geüpload, kunnen er fouten optreden
* In het deel venster account instellingen kan worden aangegeven dat u de referenties opnieuw moet invoeren om abonnementen te filteren
* Bij het wijzigen van de naam van blobs (afzonderlijk of binnen een nieuwe BLOB-container) worden geen moment opnamen bewaard. Alle andere eigenschappen en meta gegevens voor blobs, bestanden en entiteiten blijven behouden tijdens het wijzigen van de naam.
* Hoewel Azure Stack momenteel geen bestanden ondersteunt, wordt het knoop punt bestands shares nog steeds weer gegeven onder een Azure Stack opslag account dat is gekoppeld.
* Ubuntu 14,04 installatie moet gcc-versie bijgewerkt of bijgewerkt zijn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812-and-0811-and-0810"></a>Versie 0.8.12 en 0.8.11 en 0.8.10
04/07/2017

#### <a name="new"></a>Nieuw

* Storage Explorer wordt nu automatisch gesloten wanneer u een update installeert vanuit de update melding
* In-place snelle toegang biedt een verbeterde ervaring voor het werken met uw veelgebruikte resources
* In de BLOB-container editor kunt u nu zien van welke virtuele machine een geleasde BLOB hoort
* U kunt nu het deel venster aan de linkerkant samen vouwen
* Detectie wordt nu op hetzelfde moment uitgevoerd als downloaden
* Statistieken in de BLOB-container, de bestands share en de tabel editors gebruiken om de grootte van uw resource of selectie te bekijken
* U kunt zich nu aanmelden bij Azure Active Directory (AAD) op basis Azure Stack accounts.
* U kunt nu archief bestanden uploaden naar een Premium-opslag account van 32 MB
* Verbeterde ondersteuning voor toegankelijkheid
* U kunt nu vertrouwde basis-64 gecodeerde X. 509 TLS/SSL-certificaten toevoegen door naar bewerken- &gt; SSL-certificaten te gaan- &gt; certificaten importeren

#### <a name="fixes"></a>Oplossingen

* Opgelost: nadat de referenties van een account zijn vernieuwd, wordt de structuur weergave soms niet automatisch vernieuwd
* Opgelost: als een SAS wordt gegenereerd voor emulator-wacht rijen en-tabellen, resulteert dit in een ongeldige URL
* Opgelost: Premium Storage-accounts kunnen nu worden uitgebreid terwijl een proxy is ingeschakeld
* Opgelost: de knop Toep assen op de pagina account beheer werkt niet als u hebt ingesteld dat er 1 of 0 accounts zijn geselecteerd
* Opgelost: het uploaden van blobs waarvoor conflict oplossingen zijn vereist, kan mislukken, opgelost in 0.8.11
* Opgelost: verzenden van feedback is afgebroken in 0.8.11-opgelost in 0.8.12

#### <a name="known-issues"></a>Bekende problemen

* Nadat u een upgrade hebt uitgevoerd naar 0.8.10, moet u al uw referenties vernieuwen.
* In-of uitzoomen kan het zoom niveau tijdelijk worden teruggezet op het standaard niveau.
* Wanneer er meer dan drie groepen blobs of bestanden tegelijk worden geüpload, kunnen er fouten optreden.
* In het deel venster account instellingen kan worden aangegeven dat u de referenties opnieuw moet invoeren om abonnementen te filteren.
* Bij het wijzigen van de naam van blobs (afzonderlijk of binnen een nieuwe BLOB-container) worden geen moment opnamen bewaard. Alle andere eigenschappen en meta gegevens voor blobs, bestanden en entiteiten blijven behouden tijdens het wijzigen van de naam.
* Hoewel Azure Stack momenteel geen bestanden ondersteunt, wordt het knoop punt bestands shares nog steeds weer gegeven onder een Azure Stack opslag account dat is gekoppeld.
* Ubuntu 14,04 installatie moet gcc-versie bijgewerkt of bijgewerkt zijn:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-089-and-088"></a>Versie 0.8.9 en 0.8.8
02/23/2017

>[!VIDEO https://www.youtube.com/embed/R6gonK3cYAc?ecver=1]

>[!VIDEO https://www.youtube.com/embed/SrRPCm94mfE?ecver=1]


#### <a name="new"></a>Nieuw

* Storage Explorer 0.8.9 wordt automatisch de meest recente versie gedownload voor updates.
* Hotfix: als u een door een portal gegenereerde SAS-URI gebruikt om een opslag account te koppelen, resulteert dit in een fout.
* U kunt nu BLOB-moment opnamen maken, beheren en promo veren.
* U kunt zich nu aanmelden bij Azure China 21Vianet, Azure Duitsland en Azure US Government-accounts.
* U kunt nu het zoom niveau wijzigen. Gebruik de opties in het menu weer gave om in te zoomen, uit te zoomen en zoomen opnieuw in te stellen.
* Unicode-tekens worden nu ondersteund in de meta gegevens van gebruikers voor blobs en bestanden.
* Verbeterde toegankelijkheid.
* De release opmerkingen van de volgende versie kunnen worden weer gegeven in de update melding. U kunt ook de huidige release opmerkingen weer geven in het menu Help.

#### <a name="fixes"></a>Oplossingen

* Opgelost: het versie nummer wordt nu correct weer gegeven in het configuratie scherm in Windows
* Opgelost: zoeken is niet langer beperkt tot 50.000 knoop punten
* Opgelost: upload naar een bestands share voor goed als de doelmap nog niet bestaat
* Opgelost: verbeterde stabiliteit voor lange uploads en down loads

#### <a name="known-issues"></a>Bekende problemen

* In-of uitzoomen kan het zoom niveau tijdelijk worden teruggezet op het standaard niveau.
* Snelle toegang werkt alleen met items op basis van een abonnement. Lokale resources of resources die zijn gekoppeld via sleutel-of SAS-token, worden niet ondersteund in deze release.
* Het kan een paar seconden duren voordat er naar de doel resource wordt genavigeerd, afhankelijk van het aantal resources dat u hebt.
* Wanneer er meer dan drie groepen blobs of bestanden tegelijk worden geüpload, kunnen er fouten optreden.

12/16/2016
### <a name="version-087"></a>Versie 0.8.7

>[!VIDEO https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1]

#### <a name="new"></a>Nieuw

* U kunt kiezen hoe conflicten aan het begin van een update worden opgelost, hoe u een sessie downloadt of kopieert in het venster activiteiten
* Beweeg de muis aanwijzer over een tabblad om het volledige pad naar de opslag resource weer te geven
* Wanneer u op een tabblad klikt, wordt dit gesynchroniseerd met de locatie in het navigatie deel venster aan de linkerkant

#### <a name="fixes"></a>Oplossingen

* Opgelost: Storage Explorer is nu een vertrouwde app op Mac
* Opgelost: Ubuntu 14,04 wordt opnieuw ondersteund
* Opgelost: soms knippert de gebruikers interface van het account toevoegen tijdens het laden van abonnementen
* Opgelost: soms zijn niet alle opslag resources opgenomen in het navigatie deel venster aan de linkerkant
* Opgelost: in het actie deel venster worden soms lege acties weer gegeven
* Opgelost: de venster grootte van de laatste gesloten sessie wordt nu bewaard
* Opgelost: u kunt meerdere tabbladen voor dezelfde resource openen met behulp van het context menu

#### <a name="known-issues"></a>Bekende problemen

* Snelle toegang werkt alleen met items op basis van een abonnement. Lokale resources of resources die zijn gekoppeld via sleutel-of SAS-token, worden niet ondersteund in deze release
* Het kan een paar seconden duren voordat er naar de doel resource wordt genavigeerd, afhankelijk van het aantal resources dat u hebt
* Wanneer er meer dan drie groepen blobs of bestanden tegelijk worden geüpload, kunnen er fouten optreden
* Met Zoek opdrachten zoekt u op ongeveer 50.000 knoop punten, waardoor de prestaties mogelijk worden beïnvloed of waardoor er een onverwerkte uitzonde ring kan optreden
* Voor de eerste keer dat u de Storage Explorer op macOS gebruikt, ziet u mogelijk meerdere vragen om toestemming van de gebruiker voor toegang tot sleutel hanger. U wordt aangeraden altijd toestaan te selecteren zodat de prompt niet meer wordt weer gegeven

11/18/2016
### <a name="version-086"></a>Versie 0.8.6

#### <a name="new"></a>Nieuw

* U kunt de meest gebruikte services nu vastmaken aan de snelle toegang voor eenvoudige navigatie
* U kunt nu meerdere editors openen op verschillende tabbladen. Eén Klik om een tijdelijk tabblad te openen. dubbel klik om een permanent tabblad te openen. U kunt ook klikken op het tijdelijke tabblad om het tabblad vast te maken
* We hebben merk bare verbeteringen aangebracht in de prestaties en stabiliteit voor uploads en down loads, met name voor grote bestanden op snelle computers
* Lege "virtuele" mappen kunnen nu worden gemaakt in BLOB-containers
* We hebben de scoped Search opnieuw geïntroduceerd met onze nieuwe zoek functie voor uitgebreide subtekenreeksen, zodat u nu twee opties kunt zoeken:
    * Algemene zoek opdracht: Voer alleen een zoek term in het tekstvak Zoeken in
    * Zoek opdracht in bereik: Klik op het pictogram met het vergroot glas naast een knoop punt en voeg vervolgens een zoek term toe aan het einde van het pad, of klik met de rechter muisknop en selecteer ' zoeken vanaf hier '.
* We hebben diverse Thema's toegevoegd: licht (standaard), donker, hoog contrast zwart en hoog contrast wit. Ga naar bewerken- &gt; Thema's om uw voor keuren te wijzigen
* U kunt de eigenschappen van blobs en bestanden wijzigen
* Er wordt nu ondersteuning geboden voor gecodeerde (base64) en niet-versleutelde wachtrij berichten
* Op Linux is een 64-bits besturings systeem nu vereist. Voor deze release ondersteunen we alleen 64-bits Ubuntu 16.04.1 LTS
* We hebben ons logo bijgewerkt.

#### <a name="fixes"></a>Oplossingen

* Opgelost: problemen met scherm bevriezen
* Opgelost: verbeterde beveiliging
* Opgelost: soms worden er dubbele gekoppelde accounts weer gegeven
* Opgelost: een blob met een niet-gedefinieerd inhouds type kan een uitzonde ring genereren
* Opgelost: het query paneel op een lege tabel kan niet worden geopend
* Opgelost: er is een probleem met de zoek functie
* Opgelost: het aantal resources dat is geladen van 50 tot 100 verg root wanneer u op extra laden klikt
* Opgelost: bij de eerste uitvoering, als een account is aangemeld, selecteren we nu standaard alle abonnementen voor dat account.

#### <a name="known-issues"></a>Bekende problemen

* Deze versie van de Storage Explorer wordt niet uitgevoerd op Ubuntu 14,04
* Als u meerdere tabbladen voor dezelfde resource wilt openen, klikt u niet voortdurend op dezelfde resource. Klik op een andere resource, ga terug en klik vervolgens op de oorspronkelijke resource om deze opnieuw te openen op een ander tabblad
* Snelle toegang werkt alleen met items op basis van een abonnement. Lokale resources of resources die zijn gekoppeld via sleutel-of SAS-token, worden niet ondersteund in deze release
* Het kan een paar seconden duren voordat er naar de doel resource wordt genavigeerd, afhankelijk van het aantal resources dat u hebt
* Wanneer er meer dan drie groepen blobs of bestanden tegelijk worden geüpload, kunnen er fouten optreden
* Met Zoek opdrachten zoekt u op ongeveer 50.000 knoop punten, waardoor de prestaties mogelijk worden beïnvloed of waardoor er een onverwerkte uitzonde ring kan optreden

03/10/2016
### <a name="version-085"></a>Versie 0.8.5

#### <a name="new"></a>Nieuw

* Kan nu door de portal gegenereerde SAS-sleutels worden gebruikt om te koppelen aan opslag accounts en bronnen

#### <a name="fixes"></a>Oplossingen

* Fixed: voor de zoek actie is het mogelijk dat knoop punten niet-uitbreidbaar kunnen worden
* Opgelost: ' HTTP gebruiken ' werkt niet wanneer u verbinding maakt met opslag accounts met de account naam en-sleutel
* Opgelost: SAS-sleutels (speciaal door de portal gegenereerde poorten) retour neren de fout ' afsluitende slash '
* Opgelost: problemen met het importeren van tabellen
    * Soms zijn de partitie sleutel en de rij-sleutel omgekeerd
    * Kan "null"-partitie sleutels niet lezen

#### <a name="known-issues"></a>Bekende problemen

* Zoek opdrachten worden doorzocht op ongeveer 50.000 knoop punten, waardoor de prestaties mogelijk worden beïnvloed
* Azure Stack biedt momenteel geen ondersteuning voor bestanden. Als u bestanden probeert te openen, wordt er een fout weer gegeven

09/12/2016
### <a name="version-084"></a>Versie 0.8.4

>[!VIDEO https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1]

#### <a name="new"></a>Nieuw

* Genereer directe koppelingen naar opslag accounts, containers, wacht rijen, tabellen of bestands shares voor delen en eenvoudige toegang tot uw resources-Windows en Mac OS ondersteuning
* Uw BLOB-containers, tabellen, wacht rijen, bestands shares of opslag accounts zoeken in het zoekvak
* U kunt nu componenten groeperen in de opbouw functie voor tabel query's
* Naam wijzigen en kopiëren/plakken van BLOB-containers, bestands shares, tabellen, blobs, Blob-mappen, bestanden en mappen in SAS-gekoppelde accounts en containers
* Het wijzigen van de naam en het kopiëren van BLOB-containers en bestands shares behouden nu eigenschappen en meta gegevens

#### <a name="fixes"></a>Oplossingen

* Opgelost: kan geen tabel entiteiten bewerken als deze Booleaanse of binaire eigenschappen bevatten

#### <a name="known-issues"></a>Bekende problemen

* Zoek opdrachten worden doorzocht op ongeveer 50.000 knoop punten, waardoor de prestaties mogelijk worden beïnvloed

08/03/2016
### <a name="version-083"></a>Versie 0.8.3

>[!VIDEO https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1]

#### <a name="new"></a>Nieuw

* Namen van containers, tabellen, bestands shares wijzigen
* Verbeterde ervaring voor de opbouw functie voor Query's
* Mogelijkheid om query's op te slaan en te laden
* Directe koppelingen naar opslag accounts of containers, wacht rijen, tabellen of bestands shares voor delen en eenvoudig toegang krijgen tot uw resources (alleen Windows-macOS-ondersteuning wordt binnenkort beschikbaar.)
* Mogelijkheid voor het beheren en configureren van CORS-regels

#### <a name="fixes"></a>Oplossingen

* Opgelost: micro soft-accounts moeten elke 8-12 uur opnieuw worden geverifieerd

#### <a name="known-issues"></a>Bekende problemen

* Soms kan de gebruikers interface worden weer gegeven met bevroren-maximaliseren van het venster, waardoor dit probleem wordt opgelost
* voor macOS-installatie zijn mogelijk verhoogde machtigingen vereist
* In het deel venster account instellingen kan worden aangegeven dat u de referenties opnieuw moet invoeren om abonnementen te filteren
* Het wijzigen van de naam van bestands shares, Blob-containers en tabellen behoudt geen meta gegevens of andere eigenschappen in de container, zoals het quotum voor bestands shares, het open bare toegangs niveau of het toegangs beleid
* Bij het wijzigen van de naam van blobs (afzonderlijk of binnen een nieuwe BLOB-container) worden geen moment opnamen bewaard. Alle andere eigenschappen en meta gegevens voor blobs, bestanden en entiteiten blijven behouden tijdens het wijzigen van de naam
* Het kopiëren of hernoemen van resources werkt niet binnen SAS-gekoppelde accounts

07/07/2016
### <a name="version-082"></a>Versie 0.8.2

>[!VIDEO https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1]

#### <a name="new"></a>Nieuw

* Opslag accounts worden gegroepeerd op abonnementen. Ontwikkel opslag en resources die zijn gekoppeld via sleutel of SA'S, worden weer gegeven onder (lokaal en gekoppeld) knoop punt
* Meld u af bij accounts in het deel venster Azure-account instellingen
* Proxy-instellingen configureren om aanmelden in te scha kelen en te beheren
* BLOB-leases maken en verstoren
* BLOB-containers, wacht rijen, tabellen en bestanden openen met één klik

#### <a name="fixes"></a>Oplossingen

* Opgelost: wachtrij berichten die zijn ingevoegd met .NET-of Java-bibliotheken worden niet op de juiste wijze gedecodeerd vanuit base64
* Opgelost: er worden geen $metrics-tabellen weer gegeven voor Blob Storage accounts
* Fixed: het knoop punt tabellen werkt niet voor lokale opslag (ontwikkeling)

#### <a name="known-issues"></a>Bekende problemen

* voor macOS-installatie zijn mogelijk verhoogde machtigingen vereist

06/15/2016
### <a name="version-080"></a>Versie 0.8.0

>[!VIDEO https://www.youtube.com/embed/ycfQhKztSIY?ecver=1]

>[!VIDEO https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1]

>[!VIDEO https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1]

#### <a name="new"></a>Nieuw

* Ondersteuning voor bestands shares: weer geven, uploaden, downloaden, kopiëren, bestanden en mappen, SAS-Uri's (maken en verbinden)
* Verbeterde gebruikers ervaring voor het maken van verbinding met opslag met SAS-Uri's of account sleutels
* Query resultaten van tabel exporteren
* Tabel kolommen opnieuw ordenen en aanpassen
* $logs BLOB-containers en $metrics tabellen weer geven voor opslag accounts met ingeschakelde metrische gegevens
* Verbeterd export-en import gedrag bevat nu het type eigenschaps waarde

#### <a name="fixes"></a>Oplossingen

* Opgelost: het uploaden of downloaden van grote blobs kan leiden tot onvolledige uploads/down loads
* Opgelost: als u een entiteit met een numerieke teken reeks waarde ("1") bewerkt, toevoegt of importeert, wordt deze geconverteerd naar Double
* Opgelost: kan het tabel knooppunt niet uitvouwen in de lokale ontwikkel omgeving

#### <a name="known-issues"></a>Bekende problemen

* $metrics tabellen zijn niet zichtbaar voor Blob Storage accounts
* Wachtrij berichten die via een programma zijn toegevoegd, worden mogelijk niet correct weer gegeven als de berichten worden gecodeerd met base64-code ring

05/17/2016
### <a name="version-07201605090"></a>Versie 0.7.20160509.0

#### <a name="new"></a>Nieuw

* Betere fout afhandeling voor app-crashes

#### <a name="fixes"></a>Oplossingen

* Er is een probleem opgelost waarbij de berichten van de Info balk soms niet worden weer gegeven wanneer aanmeldings referenties vereist zijn

#### <a name="known-issues"></a>Bekende problemen

* Tabellen: toevoegen, bewerken of importeren van een entiteit met een eigenschap met een niet-eenduidige numerieke waarde, zoals "1" of "1,0", en de gebruiker probeert deze te verzenden als een `Edm.String` EDM. Double-waarde voor de client-API

03/31/2016

### <a name="version-07201603250"></a>Versie 0.7.20160325.0

>[!VIDEO https://www.youtube.com/embed/imbgBRHX65A?ecver=1]

>[!VIDEO https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1]

#### <a name="new"></a>Nieuw

* Tabel ondersteuning: weer geven, query's uitvoeren, exporteren, importeren en ruwe bewerkingen voor entiteiten
* Ondersteuning voor de wachtrij: berichten weer geven, toevoegen, verwijderen uit de wachtrij
* SAS-Uri's genereren voor opslag accounts
* Verbinding maken met opslag accounts met SAS-Uri's
* Meldingen voor toekomstige updates voor Storage Explorer bijwerken
* Het uiterlijk is bijgewerkt

#### <a name="fixes"></a>Oplossingen

* Verbeteringen in prestaties en betrouw baarheid

### <a name="known-issues-amp-mitigations"></a>Oplossingen voor bekende problemen &amp;

* Het downloaden van grote BLOB-bestanden werkt niet goed-we raden u aan AzCopy te gebruiken terwijl dit probleem wordt opgelost
* Account referenties worden niet opgehaald of in de cache opgeslagen als de basismap niet kan worden gevonden of niet kan worden geschreven naar
* Als we een entiteit die een eigenschap bevat met een niet-eenduidige numerieke waarde, zoals "1" of "1,0", toevoegt, bewerkt of importeert, `Edm.String` wordt de waarde door de client-API teruggestuurd als een EDM. Double
* Bij het importeren van CSV-bestanden met meerdere records worden de gegevens mogelijk schokkerig of versleuteld

02/03/2016

### <a name="version-07201601291"></a>Versie 0.7.20160129.1

#### <a name="fixes"></a>Oplossingen

* Verbeterde algehele prestaties bij het uploaden, downloaden en kopiëren van blobs

01/14/2016

### <a name="version-07201601050"></a>Versie 0.7.20160105.0

#### <a name="new"></a>Nieuw

* Linux-ondersteuning (pariteits functies naar OSX)
* BLOB-containers toevoegen met SAS-sleutel (Shared Access signatures)
* Opslag accounts voor Azure China 21Vianet toevoegen
* Opslag accounts met aangepaste eind punten toevoegen
* De inhouds opgave en afbeeldings-blobs openen en weer geven
* BLOB-eigenschappen en meta gegevens weer geven en bewerken

#### <a name="fixes"></a>Oplossingen

* Opgelost: het uploaden of downloaden van een groot aantal blobs (500) kan er soms toe leiden dat de app een wit scherm heeft
* Opgelost: wanneer het open bare toegangs niveau van een BLOB-container wordt ingesteld, wordt de nieuwe waarde pas bijgewerkt als u de focus opnieuw instelt op de container. Het dialoog venster wordt altijd standaard ingesteld op geen open bare toegang en niet de huidige waarde.
* Betere algemene ondersteuning voor toetsen bord/toegankelijkheid en gebruikers interface
* De tekst van de brood kruimels wordt gewikkeld wanneer deze lang is en er witruimte is
* Het dialoog venster SAS ondersteunt invoer validatie
* Lokale opslag blijft beschikbaar, zelfs als de gebruikers referenties zijn verlopen
* Wanneer een geopende BLOB-container wordt verwijderd, wordt de BLOB-Verkenner aan de rechter kant gesloten.

#### <a name="known-issues"></a>Bekende problemen

* Linux-installatie vereist gcc-versie bijgewerkt of bijgewerkt – stappen voor een upgrade zijn lager dan:
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

11/18/2015
### <a name="version-07201511160"></a>Versie 0.7.20151116.0

#### <a name="new"></a>Nieuw

* macOS en Windows-versies
* Meld u aan om uw opslag accounts weer te geven: gebruik uw organisatie account, micro soft-account, twee ledige, enzovoort.
* Lokale ontwikkelings opslag (gebruik opslag emulator, alleen Windows)
* Ondersteuning voor Azure Resource Manager en klassieke bronnen
* Blobs, wacht rijen of tabellen maken en verwijderen
* Zoeken naar specifieke blobs, wacht rijen of tabellen
* De inhoud van BLOB-containers verkennen
* Mappen weer geven en navigeren
* Blobs en mappen uploaden, downloaden en verwijderen
* BLOB-eigenschappen en meta gegevens weer geven en bewerken
* SAS-sleutels genereren
* Opgeslagen toegangs beleid (SAP) beheren en maken
* Blobs zoeken op voor voegsel
* Sleep bestanden neerzetten om te uploaden of te downloaden

#### <a name="known-issues"></a>Bekende problemen

* Wanneer het open bare toegangs niveau van een BLOB-container wordt ingesteld, wordt de nieuwe waarde pas bijgewerkt als u de focus opnieuw instelt op de container
* Wanneer u het dialoog venster opent om het niveau van de open bare toegang in te stellen, wordt altijd ' geen open bare toegang ' weer gegeven als de standaard waarde en niet de werkelijke huidige waarden
* Kan de naam van gedownloade blobs niet wijzigen
* Vermeldingen in het activiteiten logboek worden soms ' vastgelopen ' in de status in uitvoering wanneer er een fout optreedt en de fout wordt niet weer gegeven
* Soms loopt de kleur af of wordt deze volledig wit wanneer u een groot aantal blobs wilt uploaden of downloaden
* Het annuleren van een Kopieer bewerking werkt soms niet
* Als u tijdens het maken van een container (BLOB/wachtrij/tabel) een ongeldige naam hebt ingevoerd en een andere container type wilt maken, kunt u de focus niet instellen op het nieuwe type
* Kan geen nieuwe map maken of map een andere naam geven




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md