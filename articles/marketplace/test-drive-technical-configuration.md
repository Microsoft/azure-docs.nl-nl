---
title: Technische configuratie van test drive, micro soft Commercial Marketplace
description: Meer informatie over test stations. Met test stations kunnen nieuwe klanten uw aanbieding test drive voordat ze de aankoop door voeren.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 08/13/2019
author: trkeya
ms.author: trkeya
ms.openlocfilehash: 553eba3898a5ea42e5d478603e35e82c68abcab1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462891"
---
# <a name="test-drive-technical-configuration"></a>Technische configuratie van test drive

Met de optie test drive in micro soft Commercial Marketplace kunt u een zelf-doorgeleide rond leiding door de belangrijkste functies van uw product configureren. Met een test drive kunnen nieuwe klanten uw aanbieding uitproberen voordat ze zich vastleggen om deze aan te schaffen. Zie [Wat is een test station?](what-is-test-drive.md) voor meer informatie.

Als u een test drive voor uw aanbieding niet meer wilt opgeven, gaat u terug naar de pagina **aanbieding instellen** en schakelt u het selectie vakje **Test Drive inschakelen** uit. Niet voor alle aanbiedings typen is een test drive beschikbaar.

## <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager test drive

Dit is de enige test drive optie voor de aanbiedingen van virtuele machines of Azure-apps en vereist tamelijk gedetailleerde installatie. Lees de onderstaande secties voor [Details van het implementatie abonnement](#deployment-subscription-details) en [test stations](#test-drive-listings), en ga vervolgens door met het afzonderlijke onderwerp voor [Azure Resource Manager test drive-configuratie](azure-resource-manager-test-drive.md).

## <a name="hosted-test-drive"></a>Gehoste test drive

Micro soft kan de complexiteit van het instellen van een test drive verwijderen door het inrichten en onderhouden van de inrichting van de service te hosten. De configuratie voor dit type test drive is hetzelfde, ongeacht of de test drive is gericht op een Dynamics 365-klant afspraak of een Dynamics 365-bewerkings publiek.

- **Maxi maal aantal gelijktijdige test schijven** (vereist): Stel het maximum in voor de klanten die uw test drive tegelijk kunnen gebruiken. Elke gelijktijdige gebruiker gebruikt een Dynamics 365-licentie terwijl de test drive actief is. Zorg er dus voor dat er voldoende licenties beschikbaar zijn voor de ondersteuning van de maximale limiet. De aanbevolen waarde is 3-5.

- **Duur van test drive** (vereist): Voer het aantal uren in dat het test drive actief blijft voor elke klant. Na deze periode wordt de sessie beëindigd en wordt een van uw licenties niet meer gebruikt. We raden een waarde van 2-24 uur aan, afhankelijk van de complexiteit van uw aanbieding. Deze duur mag alleen in hele uren worden ingesteld (bijvoorbeeld ' 2 uur ' is geldig; "1,5 uur" is niet). De gebruiker kan een nieuwe sessie aanvragen als deze niet langer actief zijn en de test drive opnieuw willen gebruiken.

- **URL van exemplaar** (vereist): de URL waar de klant de test drive begint. Doorgaans de URL van uw Dynamics 365-exemplaar waarop uw app wordt uitgevoerd, waarbij voorbeeld gegevens zijn geïnstalleerd (bijvoorbeeld `https://testdrive.crm.dynamics.com` ).

- **URL** van de Web-API voor het exemplaar (vereist): Haal de Web-API-URL voor uw Dynamics 365-exemplaar op door u aan te melden bij uw Microsoft 365-account en te navigeren naar **instellingen**  >  **aanpassing**  >  **ontwikkelaars resources**  >  **instance Web API (Service root URL)**, kopieer de URL die u hier kunt vinden (bijvoorbeeld `https://testdrive.crm.dynamics.com/api/data/v9.0` ).

- **Rolnaam** (vereist): Geef de beveiligingsrol naam op die u hebt gedefinieerd in uw aangepaste Dynamics 365 test drive, die wordt toegewezen aan de gebruiker tijdens hun test drive (bijvoorbeeld test station-Role).

Volg [deze instructies](./test-drive-azure-subscription-setup.md)voor meer informatie over het instellen van uw Dynamics 365-omgeving voor test drive en het verlenen van AppSource-machtiging voor het inrichten en ongedaan maken van test drive gebruikers in uw Tenant.

Voor stapsgewijze instructies voor het weer geven en configureren van uw gehoste test drive gaat u naar de pagina [gedetailleerde configuratie voor gehoste test drive](./test-drive-hosted-detailed-config.md) .

## <a name="logic-app-test-drive"></a>test drive van logische app

Dit type test drive wordt niet gehost door micro soft. Gebruik het om verbinding te maken met een Dynamics 365-aanbieding of een andere aangepaste resource, die een verscheidenheid aan complexe oplossings architecturen omvat. Ga voor meer informatie over het instellen van de test schijven voor logische apps naar [activiteiten](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) en [klanten betrokkenheid](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) op github.

- **Regio** (vereist, vervolg keuzelijst met één selectie): er zijn momenteel 26 door Azure ondersteunde regio's waar uw test drive beschikbaar kan worden gemaakt. De resources voor uw logische app worden geïmplementeerd in de regio die u selecteert. Als uw logische app aangepaste resources heeft die zijn opgeslagen in een bepaalde regio, moet u ervoor zorgen dat de regio hier wordt geselecteerd. De beste manier is om uw logische app lokaal te implementeren in uw Azure-abonnement in de portal en te controleren of deze correct werkt voordat u deze selectie maakt.

- **Maxi maal aantal gelijktijdige test schijven** (vereist): Stel het maximum in voor de klanten die uw test drive tegelijk kunnen gebruiken. Deze test stations zijn al geïmplementeerd, zodat klanten deze direct kunnen openen zonder te hoeven wachten op een implementatie.

- **Test drive** (vereist): Voer de tijds duur in die het test station actief blijft, in aantal uren. De test drive wordt automatisch beëindigd nadat deze periode is verstreken.

- **Naam van de Azure-resource groep** (vereist): Voer de naam van de [Azure-resource groep](../azure-resource-manager/management/overview.md#resource-groups)in waar uw logische app-test drive wordt opgeslagen.

- **Naam van de Azure Logic-app** (vereist): Voer de naam in van de logische app die de test drive aan de gebruiker toewijst. Deze logische app moet worden opgeslagen in de bovenstaande Azure-resource groep.

- De naam van de logische app voor het ongedaan maken van de **inrichting** (vereist): Voer de naam in van de logische app die de test drive nadat de klant is voltooid. Deze logische app moet worden opgeslagen in de bovenstaande Azure-resource groep.

## <a name="power-bi-test-drive"></a>Power BI test drive

Producten die een interactief Power BI visueel element willen demonstreren, kunnen een Inge sloten koppeling gebruiken om een aangepast dash board te delen als hun test drive, maar er is geen verdere technische configuratie vereist. U hoeft dit alleen te doen als u uw Inge sloten Power BI-URL uploadt.

Zie [Wat zijn Power bi apps?](/power-bi/service-template-apps-overview) voor meer informatie over het instellen van Power bi-apps.

## <a name="deployment-subscription-details"></a>Details van implementatie abonnement

Als u wilt dat micro soft de test drive namens u kan implementeren, maakt en verstrekt u een afzonderlijk, uniek Azure-abonnement (niet vereist voor Power BI test stations).

- **Azure-abonnements-id** (vereist voor Azure Resource Manager en Logic apps): Voer de abonnements-id in om toegang te verlenen tot uw Azure-account services voor rapportage en facturering van resource gebruik. Het is raadzaam om [een afzonderlijk Azure-abonnement te maken](../cost-management-billing/manage/create-subscription.md) dat u voor test stations kunt gebruiken als u er nog geen hebt. U kunt uw Azure-abonnements-ID vinden door u aan te melden bij de [Azure Portal](https://portal.azure.com/) en te navigeren naar het tabblad **abonnementen** van het menu aan de linkerkant. Als u het tabblad selecteert, wordt uw abonnements-ID weer gegeven (bijvoorbeeld "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **Azure AD-Tenant-id** (vereist): voer uw Azure Active Directory (AD) [Tenant-id](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)) in. Als u deze ID wilt vinden, meldt u zich aan bij de [Azure Portal](https://portal.azure.com/), selecteert u het tabblad Active Directory in het menu links, selecteert u **Eigenschappen** en zoekt u naar de weer gegeven **Directory-id** (bijvoorbeeld 50c464d3-4930-494c-963c-1e951d15360e). U kunt ook de Tenant-ID van uw organisatie opzoeken met uw domein naam adres op [https://www.whatismytenantid.com](https://www.whatismytenantid.com) .

- **Naam van Azure AD-Tenant** (vereist voor dynamische 365): Voer de naam van uw Azure Active Directory (AD) in. Als u deze naam wilt vinden, meldt u zich aan bij de [Azure Portal](https://portal.azure.com/). in de rechter bovenhoek wordt de naam van de Tenant vermeld onder uw account naam.

- **Azure AD-App-ID** (vereist): voer uw Azure Active Directory (AD) [-toepassings-id](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)) in. Als u deze ID wilt vinden, meldt u zich aan bij de [Azure Portal](https://portal.azure.com/), selecteert u het tabblad Active Directory in het menu aan de linkerkant, selecteert u **app-registraties** en zoekt u naar de weer gegeven **toepassings-id** (zoals `50c464d3-4930-494c-963c-1e951d15360e` ).

- **Azure AD-App-client geheim** (vereist): Voer uw Azure AD-toepassings [client geheim](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)in. Als u deze waarde wilt vinden, meldt u zich aan bij de [Azure Portal](https://portal.azure.com/). Selecteer in het linkermenu het tabblad **Azure Active Directory** , selecteer **app-registraties** en selecteer uw test drive-app. Selecteer vervolgens **certificaten en geheimen**, selecteer **Nieuw client geheim**, voer een beschrijving in, selecteer **nooit** onder **verloopt** en kies **toevoegen**. Zorg ervoor dat u de waarde kopieert. Navigeer niet weg van de pagina voordat u de waarde kopieert.

## <a name="test-drive-listings"></a>Test stations

De optie voor het **testen van stations** vindt u op het tabblad **test station** in het partner centrum om de talen (en markten) weer te geven waar uw test drive beschikbaar is. momenteel is Engels (Verenigde Staten) de enige locatie beschikbaar. Daarnaast wordt op deze pagina de status weer gegeven van de taalspecifieke vermelding en de datum/tijd waarop deze is toegevoegd. U moet de test drive Details (beschrijving, gebruikers handleiding, Video's, enzovoort) definiëren voor elke taal/markt.

- **Beschrijving** (vereist): Geef een beschrijving van uw test drive, wat wordt aangetoond, wat de gebruiker kan doen om te experimenteren met, functies om te verkennen en alle relevante informatie om de gebruiker te helpen bepalen of u uw aanbieding moet verkrijgen. In dit veld kunnen Maxi maal 3.000 tekens tekst worden ingevoerd.

- **Toegangs gegevens** (vereist voor Azure Resource Manager en logische test stations): Leg uit wat een klant moet weten om deze test drive te kunnen openen en gebruiken. Door loop een scenario voor het gebruik van uw aanbieding en precies wat de klant moet weten voor toegang tot de functies in de test drive. In dit veld kunnen Maxi maal 10.000 tekens tekst worden ingevoerd.

- **Gebruikers handleiding** (vereist): een diep gaande uitleg van uw test drive-ervaring. De gebruikers handleiding moet betrekking hebben op precies wat u wilt dat de klant het test drive verkrijgt en zich als referentie voor eventuele vragen bevindt. Het bestand moet een PDF-indeling hebben en een naam hebben (255 tekens Max) na het uploaden.

- **Video's: Video's toevoegen** (optioneel): Video's die elders worden gehost, kunnen hier worden verwezen met een koppeling en een miniatuur afbeelding (533 x 324 pixels) zodat een klant een overzicht van de informatie kan bekijken om hen meer inzicht te geven in de test drive, met inbegrip van de manier waarop u de functies van uw aanbieding kunt gebruiken en inzicht krijgt in scenario's die hun voor delen accentueren.
  - **Naam** (vereist)
  - **URL** (alleen YouTube of Vimeo; vereist)
  - **Miniatuur** (533 x 324 pixels): de afbeelding moet de PNG-indeling hebben.

Als u uw test drive momenteel in partner centrum maakt, selecteert u **concept opslaan** voordat u doorgaat.

Voor stapsgewijze instructies voor het weer geven en configureren van uw gehoste test drive gaat u naar de pagina [gedetailleerde configuratie voor gehoste test drive](./test-drive-hosted-detailed-config.md) .

## <a name="additional-resources"></a>Aanvullende resources

## <a name="next-steps"></a>Volgende stappen

- [Aanbevolen procedures testen](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Overzicht](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF; Controleer of de pop-upblokkering is uitgeschakeld)
- [Een bestaande aanbieding bijwerken in de commerciële Marketplace](partner-center-portal/update-existing-offer.md)