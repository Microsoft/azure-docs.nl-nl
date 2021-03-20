---
title: Host load balanced Azure web apps op de zone Apex
description: Een Azure DNS alias record gebruiken om de web-apps met taak verdeling te hosten op de zone Apex
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 08/10/2019
ms.author: rohink
ms.openlocfilehash: 72adb2732eb0832589cbc25fb7e4288eb1899214
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94954508"
---
# <a name="host-load-balanced-azure-web-apps-at-the-zone-apex"></a>Host load balanced Azure web apps op de zone Apex

Het DNS-protocol voor komt dat de toewijzing van iets anders dan een A-of AAAA-record bij de zone Apex. Een voor beeld van een zone Apex is contoso.com. Deze beperking geeft een probleem met de eigen aren van toepassingen die achter Traffic Manager toepassingen met taak verdeling hebben. Het is niet mogelijk om te verwijzen naar het Traffic Manager profiel in de zone Apex-record. Als gevolg hiervan moeten toepassings eigenaren gebruikmaken van een tijdelijke oplossing. Een omleiding op de toepassingslaag moet worden omgeleid van de zone naar een ander domein. Een voor beeld is een omleiding van contoso.com naar www \. contoso.com. Deze indeling bevat een Single Point of Failure voor de omleidings functie.

Met alias records bestaat dit probleem niet meer. Toepassings eigenaren kunnen nu de Apex-record van de zone naar een Traffic Manager profiel met externe eind punten aanwijzen. Toepassings eigenaren kunnen verwijzen naar hetzelfde Traffic Manager-profiel dat wordt gebruikt voor elk ander domein binnen hun DNS-zone.

Contoso.com en www \. contoso.com kunnen bijvoorbeeld verwijzen naar hetzelfde Traffic Manager-profiel. Dit is het geval zolang het Traffic Manager profiel alleen externe eind punten heeft geconfigureerd.

In dit artikel leert u hoe u een alias record kunt maken voor uw domein Apex en hoe u de eind punten van uw Traffic Manager-profiel kunt configureren voor uw web-apps.

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

U moet een beschikbare domeinnaam hebben die u in Azure DNS kunt hosten om te testen. U moet het volledige beheer over dit domein hebben. Volledig beheer betekent ook de mogelijkheid om naamserverrecords (NS) voor het domein in te stellen.

Zie voor instructies voor het hosten van uw domein in Azure DNS [Zelfstudie: Uw domein in Azure DNS hosten](dns-delegate-domain-azure-dns.md).

Het voorbeelddomein dat wordt gebruikt voor deze zelfstudie is contoso.com, maar u moet uw eigen domeinnaam gebruiken.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resource groep maken voor het opslaan van alle resources die in dit artikel worden gebruikt.

## <a name="create-app-service-plans"></a>App Service-abonnementen maken

Maak twee Web App Service-abonnementen in uw resource groep met behulp van de volgende tabel voor configuratie-informatie. Zie [een app service-abonnement beheren in azure](../app-service/app-service-plan-manage.md)voor meer informatie over het maken van een app service-abonnement.


|Name  |Besturingssysteem  |Locatie  |Prijscategorie  |
|---------|---------|---------|---------|
|ASP-01     |Windows|VS - oost|Dev/test-D1-Shared|
|ASP-02     |Windows|VS - centraal|Dev/test-D1-Shared|

## <a name="create-app-services"></a>App Services maken

Maak twee web-apps, één in elk App Service-abonnement.

1. Selecteer in de linkerbovenhoek van de pagina Azure Portal **een resource maken**.
2. Typ **Web-app** in de zoek balk en druk op ENTER.
3. Selecteer **Web-app**.
4. Selecteer **Maken**.
5. Accepteer de standaard waarden en gebruik de volgende tabel om de twee web-apps te configureren:

   |Name<br>(moet uniek zijn binnen. azurewebsites.net)|Resourcegroep |Runtimestack|Region|Abonnement/locatie App Service
   |---------|---------|-|-|-------|
   |App-01|Bestaande gebruiken<br>Uw resourcegroep selecteren|.NET Core 2.2|VS - oost|ASP-01 (D1)|
   |App-02|Bestaande gebruiken<br>Uw resourcegroep selecteren|.NET Core 2.2|VS - centraal|ASP-02 (D1)|

### <a name="gather-some-details"></a>Enkele details verzamelen

Nu moet u het IP-adres en de hostnaam voor de web-apps noteren.

1. Open de resource groep en selecteer uw eerste web-app (**app-01** in dit voor beeld).
2. Selecteer in de linkerkolom **Eigenschappen**.
3. Noteer het adres onder **URL** en noteer het eerste IP-adres in de lijst onder **uitgaande IP-adressen** . U gebruikt deze informatie later wanneer u uw Traffic Manager-eind punten configureert.
4. Herhaal dit voor **app-02**.

## <a name="create-a-traffic-manager-profile"></a>Een Traffic Manager-profiel maken

Maak een Traffic Manager-profiel in de resource groep. Gebruik de standaard waarden en typ een unieke naam binnen de naam ruimte trafficmanager.net.

Voor informatie over het maken van een Traffic Manager profiel raadpleegt u [Quick Start: een Traffic Manager profiel maken voor een Maxi maal beschik bare webtoepassing](../traffic-manager/quickstart-create-traffic-manager-profile.md).

### <a name="create-endpoints"></a>Eindpunten maken

U kunt nu de eind punten voor de twee web-apps maken.

1. Open de resource groep en selecteer uw Traffic Manager profiel.
2. Selecteer **eind punten** in de linkerkolom.
3. Selecteer **Toevoegen**.
4. Gebruik de volgende tabel om de eind punten te configureren:

   |Type  |Naam  |Doel  |Locatie  |Aangepaste headerinstellingen|
   |---------|---------|---------|---------|---------|
   |Extern eind punt     |End-01|IP-adres dat u hebt genoteerd voor app-01|VS - oost|hostsite\<the URL you recorded for App-01\><br>Voor beeld: **host: app-01.azurewebsites.net**|
   |Extern eind punt     |End-02|IP-adres dat u hebt vastgelegd voor app-02|VS - centraal|hostsite\<the URL you recorded for App-02\><br>Voor beeld: **host: app-02.azurewebsites.net**

## <a name="create-dns-zone"></a>DNS-zone maken

U kunt een bestaande DNS-zone gebruiken om te testen of u kunt een nieuwe zone maken. Als u een nieuwe DNS-zone wilt maken en delegeren in azure, raadpleegt u [zelf studie: host uw domein in azure DNS](dns-delegate-domain-azure-dns.md).

## <a name="add-a-txt-record-for-custom-domain-validation"></a>Een TXT-record toevoegen voor het valideren van aangepaste domeinen

Wanneer u een aangepaste hostnaam aan uw web-apps toevoegt, zoekt deze naar een specifieke TXT-record om uw domein te valideren.

1. Open de resource groep en selecteer de DNS-zone.
2. Selecteer **Recordset**.
3. Voeg de recordset toe met behulp van de volgende tabel. Voor de waarde gebruikt u de daad werkelijke web-app-URL die u eerder hebt vastgelegd:

   |Naam  |Type  |Waarde|
   |---------|---------|-|
   |@     |TXT|App-01.azurewebsites.net|


## <a name="add-a-custom-domain"></a>Een aangepast domein toevoegen

Voeg een aangepast domein toe voor beide web-apps.

1. Open de resource groep en selecteer uw eerste web-app.
2. Selecteer in de linkerkolom **aangepaste domeinen**.
3. Onder **aangepaste domeinen** selecteert u **aangepast domein toevoegen**.
4. Onder **aangepast domein** typt u uw aangepaste domein naam. Bijvoorbeeld contoso.com.
5. Selecteer **Valideren**.

   Uw domein moet validatie door geven en groene vinkjes weer geven naast **hostnamen Beschik baarheid** en **domein eigendom**.
5. Selecteer **Aangepast domein toevoegen**.
6. Vernieuw uw browser om de nieuwe hostnaam te bekijken onder **hostnamen die zijn toegewezen aan de site**. Bij het vernieuwen op de pagina worden de wijzigingen niet altijd meteen weer gegeven.
7. Herhaal deze procedure voor uw tweede web-app.

## <a name="add-the-alias-record-set"></a>De set met alias records toevoegen

Voeg nu een alias record toe voor de zone Apex.

1. Open de resource groep en selecteer de DNS-zone.
2. Selecteer **Recordset**.
3. Voeg de recordset toe met behulp van de volgende tabel:

   |Naam  |Type  |Recordset van alias  |Aliastype  |Azure-resource|
   |---------|---------|---------|---------|-----|
   |@     |A|Ja|Azure-resource|Traffic Manager-uw profiel|


## <a name="test-your-web-apps"></a>Uw Web-Apps testen

U kunt nu testen om er zeker van te zijn dat u uw web-app kunt bereiken en dat de taak verdeling wordt gebalanceerd.

1. Open een webbrowser en blader naar uw domein. Bijvoorbeeld contoso.com. De pagina standaard web-app wordt weer geven.
2. Stop uw eerste web-app.
3. Sluit de webbrowser en wacht enkele minuten.
4. Start uw webbrowser en blader naar uw domein. De standaard pagina voor web-apps moet nog steeds worden weer geven.
5. Stop uw tweede web-app.
6. Sluit de webbrowser en wacht enkele minuten.
7. Start uw webbrowser en blader naar uw domein. U ziet fout 403 om aan te geven dat de web-app is gestopt.
8. Start uw tweede web-app.
9. Sluit de webbrowser en wacht enkele minuten.
10. Start uw webbrowser en blader naar uw domein. De pagina standaard web-app wordt opnieuw weer geven.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over alias records:

- [Zelfstudie: Een aliasrecord zo configureren dat deze naar een openbaar Azure-IP-adres verwijst](tutorial-alias-pip.md)
- [Zelfstudie: een Azure DNS-aliasrecord configureren om het gebruik van hoofddomeinnaam met Traffic Manager te ondersteunen](tutorial-alias-tm.md)
- [Veelgestelde vragen over DNS](./dns-faq.md#alias-records)

Zie [een actieve DNS-naam migreren naar Azure app service](../app-service/manage-custom-dns-migrate-domain.md)voor meer informatie over het migreren van een actieve DNS-naam.