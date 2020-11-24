---
title: Aanvullende scenario's voor licentie verlening op basis van een groep-Azure AD | Microsoft Docs
description: Meer scenario's voor het Azure Active Directory van groeps licenties
services: active-directory
keywords: Azure AD-licenties
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba21230f99b9f185335dfce4652a461a0a41ab3c
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95503141"
---
# <a name="scenarios-limitations-and-known-issues-using-groups-to-manage-licensing-in-azure-active-directory"></a>Scenario's, beperkingen en bekende problemen met behulp van groepen voor het beheren van licenties in Azure Active Directory

Gebruik de volgende informatie en voor beelden om een beter inzicht te krijgen in de licentie verlening op basis van Azure Active Directory (Azure AD).

## <a name="usage-location"></a>Gebruiks locatie

Sommige services van Microsoft zijn niet op alle locaties beschikbaar. Voordat een licentie aan een gebruiker kan worden toegewezen, moet de beheerder de eigenschap **gebruiks locatie** opgeven voor de gebruiker. In [de Azure Portal](https://portal.azure.com)kunt u gebruiks locatie opgeven in **gebruikers** &gt; **profiel** &gt; **instellingen**.

Voor het toewijzen van een groeps licentie neemt de locatie van de map over van alle gebruikers waarvoor een gebruiks locatie is opgegeven. Als u gebruikers op meerdere locaties hebt, moet u ervoor zorgen dat deze correct in uw gebruikers bronnen worden weer gegeven voordat u gebruikers toevoegt aan groepen met licenties.

> [!NOTE]
> Met de toewijzing van de groeps licentie wordt nooit een bestaande gebruiks locatie waarde voor een gebruiker gewijzigd. We raden u aan om de gebruiks locatie altijd in te stellen als onderdeel van de stroom voor het maken van een gebruiker in azure AD (bijvoorbeeld via AAD Connect-configuratie). Hiermee zorgt u ervoor dat het resultaat van de licentie toewijzing altijd juist is en dat gebruikers geen services ontvangen op locaties die niet zijn toegestaan.

## <a name="use-group-based-licensing-with-dynamic-groups"></a>Op groep gebaseerde licentie verlening met dynamische groepen gebruiken

U kunt op groepen gebaseerde licentie verlening met elke beveiligings groep gebruiken, wat betekent dat deze kan worden gecombineerd met dynamische Azure AD-groepen. Dynamische groepen voeren regels uit op bron kenmerken van de gebruiker om gebruikers automatisch toe te voegen en te verwijderen uit groepen.

U kunt bijvoorbeeld een dynamische groep maken voor een set producten die u aan gebruikers wilt toewijzen. Elke groep wordt gevuld met een regel waarmee gebruikers worden toegevoegd op basis van hun kenmerken, en aan elke groep worden de licenties toegewezen die u wilt ontvangen. U kunt het kenmerk on-premises toewijzen en dit synchroniseren met Azure AD of u kunt het kenmerk rechtstreeks in de Cloud beheren.

Er worden licenties toegewezen aan de gebruiker kort nadat ze aan de groep zijn toegevoegd. Wanneer het kenmerk wordt gewijzigd, de gebruiker de groepen verlaat en de licenties worden verwijderd.

### <a name="example"></a>Voorbeeld

Bekijk het voor beeld van een on-premises identiteits beheer oplossing die bepaalt welke gebruikers toegang moeten hebben tot micro soft-webservices. **ExtensionAttribute1** wordt gebruikt om een teken reeks waarde op te slaan voor de licenties die de gebruiker moet hebben. Azure AD Connect synchroniseert dit met Azure AD.

Gebruikers hebben mogelijk één licentie nodig, maar niet een andere, of kunnen beide. Hier volgt een voor beeld waarin u de licenties voor Office 365 Enter prise E5 en Enterprise Mobility + Security (EMS) distribueert voor gebruikers in groepen:

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 Enter prise E5: basis Services

![Scherm afbeelding van de basis services van Office 365 Enter prise E5](./media/licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Security: gelicentieerde gebruikers

![Scherm afbeelding van Enterprise Mobility + Security gelicentieerde gebruikers](./media/licensing-group-advanced/o365-e5-licensed-users.png)

Voor dit voor beeld wijzigt u één gebruiker en stelt u de extensionAttribute1 in op de waarde `EMS;E5_baseservices;` Als u wilt dat de gebruiker beide licenties moet hebben. U kunt deze wijziging on-premises aanbrengen. Nadat de wijziging is gesynchroniseerd met de Cloud, wordt de gebruiker automatisch toegevoegd aan beide groepen en worden er licenties toegewezen.

![Scherm afbeelding die laat zien hoe de extensionAttribute1 van de gebruiker kan worden ingesteld](./media/licensing-group-advanced/user-set-extensionAttribute1.png)

> [!WARNING]
> Wees voorzichtig met het wijzigen van de lidmaatschaps regel van een bestaande groep. Wanneer een regel wordt gewijzigd, wordt het lidmaatschap van de groep opnieuw geëvalueerd en worden gebruikers die niet meer overeenkomen met de nieuwe regel, verwijderd (gebruikers die nog steeds overeenkomen met de nieuwe regel, worden niet beïnvloed tijdens dit proces). De licenties van deze gebruikers worden tijdens het proces verwijderd, wat kan leiden tot verlies van de service, of in sommige gevallen gegevens verlies.
> 
> Als u een grote dynamische groep hebt die afhankelijk is van de licentie toewijzing, kunt u overwegen om eventuele belang rijke wijzigingen in een kleinere test groep te valideren voordat u deze toepast op de hoofd groep.

## <a name="multiple-groups-and-multiple-licenses"></a>Meerdere groepen en meerdere licenties

Een gebruiker kan lid zijn van meerdere groepen met licenties. Hier volgen enkele dingen die u moet overwegen:

- Meerdere licenties voor hetzelfde product kunnen elkaar overlappen, waardoor alle ingeschakelde services worden toegepast op de gebruiker. In het volgende voor beeld ziet u twee licentie groepen: *E3 basis Services* bevat de Foundation-Services die eerst moeten worden geïmplementeerd, voor alle gebruikers. En *E3 Extended Services* bevat extra services (Sway en planner) om alleen voor bepaalde gebruikers te implementeren. In dit voor beeld is de gebruiker toegevoegd aan beide groepen:

  ![Scherm opname van ingeschakelde Services](./media/licensing-group-advanced/view-enabled-services.png)

  Als gevolg hiervan heeft de gebruiker 7 van de 12 Services in het product ingeschakeld, terwijl er slechts één licentie voor dit product wordt gebruikt.

- Als u de *E3* -licentie selecteert, worden meer details weer gegeven, inclusief informatie over welke services voor de gebruiker zijn ingeschakeld door de toewijzing van de groeps licentie.

## <a name="direct-licenses-coexist-with-group-licenses"></a>Directe licenties naast groeps licenties

Wanneer een gebruiker een licentie van een groep overneemt, kunt u deze licentie toewijzing niet rechtstreeks verwijderen of wijzigen in de eigenschappen van de gebruiker. Wijzigingen moeten worden aangebracht in de groep en vervolgens door gegeven aan alle gebruikers.

Het is echter wel mogelijk om dezelfde product licentie rechtstreeks toe te wijzen aan de gebruiker, naast de overgenomen licentie. U kunt voor één gebruiker extra services van het product inschakelen, zonder dat dit van invloed is op andere gebruikers.

Rechtstreeks toegewezen licenties kunnen worden verwijderd en heeft geen invloed op overgenomen licenties. Denk na over de gebruiker die een Office 365 Enter prise E3-licentie van een groep overneemt.

In eerste instantie neemt de gebruiker de licentie alleen over van de *E3 Basic Services* -groep, waardoor vier service plannen worden ingeschakeld.

1. Selecteer **toewijzen** om een E3-licentie rechtstreeks toe te wijzen aan de gebruiker. In dit geval gaat u alle service abonnementen uitschakelen, behalve Yammer Enter prise.

    Als gevolg hiervan gebruikt de gebruiker nog steeds slechts één licentie van het E3-product. Maar de directe toewijzing maakt de Yammer Enter prise-service alleen voor die gebruiker. U kunt zien welke services worden ingeschakeld door het groepslid maatschap en de directe toewijzing.

1. Wanneer u een directe toewijzing gebruikt, zijn de volgende bewerkingen toegestaan:

   - Yammer Enter prise kan rechtstreeks worden uitgeschakeld voor de gebruikers resource. De **in-** /uitschakelen in de afbeelding is ingeschakeld voor deze service, in tegens telling tot de andere service. Omdat de service rechtstreeks op de gebruiker is ingeschakeld, kan deze worden gewijzigd.
   - Extra services kunnen ook worden ingeschakeld als onderdeel van de rechtstreeks toegewezen licentie.
   - De knop **verwijderen** kan worden gebruikt om de rechtstreekse licentie van de gebruiker te verwijderen. U kunt zien dat de gebruiker nu alleen de overgenomen groeps licentie heeft en dat alleen de oorspronkelijke Services ingeschakeld blijven:

## <a name="managing-new-services-added-to-products"></a>Nieuwe services die zijn toegevoegd aan producten beheren

Wanneer micro soft een nieuwe service toevoegt aan een licentie plan voor een product, wordt deze standaard ingeschakeld in alle groepen waaraan u de product licentie hebt toegewezen. Gebruikers in uw organisatie die zich hebben geabonneerd op meldingen over product wijzigingen, ontvangen e-mails van tevoren een melding over de aanstaande service toevoegingen.

Als beheerder kunt u alle groepen bekijken die worden beïnvloed door de wijziging en actie ondernemen, zoals het uitschakelen van de nieuwe service in elke groep. Als u bijvoorbeeld groepen hebt gemaakt met als doel alleen specifieke services voor implementatie, kunt u deze groepen opnieuw bezoeken en ervoor zorgen dat toegevoegde services worden uitgeschakeld.

Hier volgt een voor beeld van hoe dit proces eruit kan zien:

1. Oorspronkelijk hebt u het *Office 365 Enter prise E5* -product toegewezen aan verschillende groepen. Een van deze groepen, met de naam *O365 E5-Exchange is alleen* ontworpen om alleen de *Exchange Online-service (abonnement 2)* voor de leden in te scha kelen.

2. U hebt een melding ontvangen van micro soft dat het E5-product wordt uitgebreid met een nieuwe service- *Microsoft stream*. Wanneer de service beschikbaar wordt in uw organisatie, kunt u het volgende doen:

3. Ga naar de [**Azure Active Directory > licenties > alle producten**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) Blade en selecteer *Office 365 Enter prise E5* en selecteer vervolgens **gelicentieerde groepen** om een lijst weer te geven van alle groepen met dat product.

4. Klik op de groep die u wilt controleren (in dit geval *O365 E5-alleen Exchange*). Hiermee opent u het tabblad **licenties** . Als u op de E5-licentie klikt, wordt een Blade met alle ingeschakelde Services geopend.
   > [!NOTE]
   > De *Microsoft stream* -service is naast de *Exchange Online* -service automatisch toegevoegd en ingeschakeld in deze groep:

   ![Scherm opname van nieuwe service toegevoegd aan een groeps licentie](./media/licensing-group-advanced/manage-new-services.png)

5. Als u de nieuwe service in deze groep wilt uitschakelen, klikt u op de **aan/uit-** weer geven naast de service en klikt u op de knop **Opslaan** om de wijziging te bevestigen. Alle gebruikers in de groep worden nu door Azure AD verwerkt om de wijziging toe te passen. nieuwe gebruikers die zijn toegevoegd aan de groep, hebben geen *Microsoft stream* -service ingeschakeld.

   > [!NOTE]
   > Gebruikers hebben mogelijk de service nog steeds ingeschakeld via een andere licentie toewijzing (een andere groep waarvan ze lid zijn of een directe licentie toewijzing).

6. Voer, indien nodig, dezelfde stappen uit voor andere groepen waaraan dit product is toegewezen.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>Power shell gebruiken om te zien wie en welke licenties zijn overgenomen
U kunt een Power shell-script gebruiken om te controleren of gebruikers een licentie rechtstreeks of overgenomen van een groep hebben toegewezen.

1. Voer de `connect-msolservice` cmdlet uit om uw organisatie te verifiëren en er verbinding mee te maken.

2. `Get-MsolAccountSku` kan worden gebruikt voor het detecteren van alle ingerichte product licenties in de Azure AD-organisatie.

   ![Scherm afbeelding van de cmdlet Get-Msolaccountsku](./media/licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. Gebruik [Dit Power shell-script](licensing-ps-examples.md#check-if-user-license-is-assigned-directly-or-inherited-from-a-group)om de *AccountSkuId* -waarde voor de licentie die u wilt gebruiken. Hiermee wordt een lijst met gebruikers met deze licentie gemaakt met de informatie over de manier waarop de licentie is toegewezen.

## <a name="use-audit-logs-to-monitor-group-based-licensing-activity"></a>Controle Logboeken gebruiken voor het bewaken van op groepen gebaseerde licentie activiteiten

U kunt [Azure AD-controle logboeken](../reports-monitoring/concept-audit-logs.md#audit-logs) gebruiken om alle activiteiten te zien die betrekking hebben op op groepen gebaseerde licentie verlening, waaronder:
- Wie heeft de licenties op groepen gewijzigd
- Wanneer het systeem is begonnen met het verwerken van een wijziging in een groeps licentie en wanneer deze is voltooid
- welke licentie wijzigingen zijn aangebracht aan een gebruiker als gevolg van een toewijzing van een groeps licentie.

>[!NOTE]
> Audit logboeken zijn beschikbaar op de meeste blades in het gedeelte Azure Active Directory van de portal. Afhankelijk van waar u ze opent, kunnen filters vooraf worden toegepast om alleen activiteiten weer te geven die relevant zijn voor de context van de Blade. Als u niet de verwachte resultaten ziet, bekijkt u [de filter opties](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs) of opent u de niet-gefilterde audit Logboeken onder [**Azure Active Directory > activiteit > audit logboeken**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Audit).

### <a name="find-out-who-modified-a-group-license"></a>Ontdekken wie een groeps licentie heeft gewijzigd

1. Stel het **activiteiten** filter in op het instellen van de *groeps licentie* en klik op **Toep assen**.
2. De resultaten omvatten alle gevallen van licenties die worden ingesteld of gewijzigd op groepen.
   >[!TIP]
   > U kunt ook de naam van de groep in het *doel* filter typen om de resultaten te bereiken.

3. Selecteer een item in de lijst om de details te bekijken van wat er is gewijzigd. Onder *gewijzigde eigenschappen* worden zowel oude als nieuwe waarden voor de licentie toewijzing weer gegeven.

Hier volgt een voor beeld van recente wijzigingen in groeps licenties, met Details:

![Scherm opname van de pagina controle logboeken waarvoor een lijst item is geselecteerd en het deel venster Details van het controle logboek voor activiteiten geopend.](./media/licensing-group-advanced/audit-group-license-change.png)

### <a name="find-out-when-group-changes-started-and-finished-processing"></a>Nagaan wanneer de groeps wijzigingen zijn gestart en de verwerking is voltooid

Wanneer een licentie wordt gewijzigd voor een groep, gaat Azure AD de wijzigingen Toep assen op alle gebruikers.

1. Als u wilt zien wanneer groepen zijn begonnen met de verwerking, stelt u het **activiteit** filter in op het *Toep assen van groeps licentie op gebruikers*. Houd er rekening mee dat de actor voor de bewerking is *Microsoft Azure AD Group-Based-licentie verlening* : een systeem account dat wordt gebruikt voor het uitvoeren van alle groeps licentie wijzigingen.
   >[!TIP]
   > Klik op een item in de lijst om het veld *gewijzigde eigenschappen* weer te geven. hier worden de licentie wijzigingen weer gegeven die zijn opgenomen voor verwerking. Dit is handig als u meerdere wijzigingen in een groep hebt aangebracht en u niet zeker weet welk item is verwerkt.

2. Als u wilt zien wanneer de verwerking van groepen is voltooid, gebruikt u de filter waarde *Toep assen op groep gebaseerd op gebruikers*.
   > [!TIP]
   > In dit geval bevat het veld *gewijzigde eigenschappen* een samen vatting van de resultaten. Dit is handig om snel te controleren of er fouten zijn opgetreden in de verwerking. Voorbeelduitvoer:
   > ```
   > Modified Properties
   > ...
   > Name : Result
   > Old Value : []
   > New Value : [Users successfully assigned licenses: 6, Users for whom license assignment failed: 0.];
   > ```

3. Stel de volgende filters in om het volledige logboek voor de verwerking van een groep te bekijken, inclusief alle gebruikers wijzigingen:
   - **Gestart door (actor)**: "Microsoft Azure AD Group-Based-licentiëring"
   - **Datum bereik** (optioneel): aangepast bereik voor wanneer u een specifieke groep hebt gestart en de verwerking hebt voltooid

In deze voorbeeld uitvoer ziet u het begin van de verwerking, alle resulterende gebruikers wijzigingen en de eind datum van de verwerking.

![Licentie wijzigingen van scherm opname groep](./media/licensing-group-advanced/audit-group-processing-log.png)

>[!TIP]
> Als u op items met betrekking tot *wijzigings gebruikers licentie* klikt, worden details weer gegeven voor licentie wijzigingen die zijn toegepast op elke afzonderlijke gebruiker.

## <a name="deleting-a-group-with-an-assigned-license"></a>Een groep met een toegewezen licentie verwijderen

Het is niet mogelijk om een groep te verwijderen waaraan een actieve licentie is toegewezen. Een beheerder kan een groep verwijderen die niet kan leiden tot het feit dat er licenties worden verwijderd voor gebruikers. Daarom moeten de licenties eerst uit de groep worden verwijderd voordat deze kunnen worden verwijderd.

Wanneer u probeert een groep te verwijderen in de Azure Portal ziet u mogelijk een fout melding zoals deze: verwijderen van de ![ scherm opname is mislukt](./media/licensing-group-advanced/groupdeletionfailed.png)

Ga naar het tabblad **licenties** van de groep en controleer of er licenties zijn toegewezen. Zo ja, verwijdert u deze licenties en probeert u de groep opnieuw te verwijderen.

Er kunnen vergelijk bare fouten worden weer gegeven wanneer u probeert de groep te verwijderen via Power shell of Graph API. Als u een groep gebruikt die vanaf on-premises is gesynchroniseerd, worden in Azure AD Connect ook fouten gerapporteerd als de groep in azure AD niet kan worden verwijderd. In al deze gevallen moet u controleren of er licenties zijn toegewezen aan de groep en deze eerst verwijderen.

## <a name="limitations-and-known-issues"></a>Beperkingen en bekende problemen

Als u gebruikmaakt van op groepen gebaseerde licentie verlening, is het een goed idee om vertrouwd te raken met de volgende lijst met beperkingen en bekende problemen.

- Groepslicenties bieden op dit moment geen ondersteuning voor groepen die andere groepen bevatten (geneste groepen). Als u een licentie toepast op een geneste groep, wordt die licentie alleen toegepast op de gebruikers die lid direct zijn van de groep.

- De functie kan alleen worden gebruikt met beveiligings groepen en Microsoft 365 groepen met securityEnabled = TRUE.

- Het [Microsoft 365-beheer centrum](https://admin.microsoft.com) biedt momenteel geen ondersteuning voor op groepen gebaseerde licentie verlening. Als een gebruiker een licentie van een groep overneemt, wordt deze licentie weer gegeven in de Office-beheer portal als een reguliere gebruikers licentie. Als u de licentie probeert te wijzigen of de licentie probeert te verwijderen, wordt een fout bericht door de portal weer gegeven. Overgenomen groeps licenties kunnen niet rechtstreeks op een gebruiker worden gewijzigd.

- Wanneer licenties zijn toegewezen of gewijzigd voor een grote groep (bijvoorbeeld 100.000 gebruikers), kan dit van invloed zijn op de prestaties. Met name het aantal wijzigingen dat door Azure AD Automation wordt gegenereerd, kan de prestaties van uw adreslijst synchronisatie tussen Azure AD en on-premises systemen negatief beïnvloeden.

- Als u gebruikmaakt van dynamische groepen om het lidmaatschap van de gebruiker te beheren, controleert u of de gebruiker deel uitmaakt van de groep. Dit is noodzakelijk voor het toewijzen van licenties. Als dit niet het geval is, [controleert u de verwerkingsstatus voor de lidmaatschapsregel](groups-create-rule.md) van de dynamische groep.

- Bij bepaalde hoge belasting situaties kan het enige tijd duren om licentie wijzigingen voor groepen of lidmaatschaps wijzigingen te verwerken in groepen met bestaande licenties. Als u ziet dat uw wijzigingen meer dan 24 uur duren om de groeps grootte van 60K gebruikers of minder te verwerken, [opent u een ondersteunings ticket](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/supportRequest) om ons te laten onderzoeken. 

- Met Automation van licentie beheer wordt niet automatisch gereageerd op alle typen wijzigingen in de omgeving. Zo hebt u mogelijk geen licenties meer, waardoor sommige gebruikers een fout status hebben. Als u het aantal beschik bare stoelen wilt vrijmaken, kunt u een aantal rechtstreeks toegewezen licenties verwijderen van andere gebruikers. Het systeem reageert echter niet automatisch op deze wijziging en herstelt gebruikers met deze fout status.

  Als tijdelijke oplossing voor deze typen beperkingen gaat u naar de Blade **groep** in azure AD en klikt u op opnieuw **verwerken**. Met deze opdracht worden alle gebruikers in die groep verwerkt en worden de fout status, indien mogelijk, opgelost.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over scenario’s voor licentiebeheer via licenties op basis van groepen, raadpleegt u:

* [Wat is op een groep gebaseerde licentie verlening in Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Licenties toewijzen aan een groep in Azure Active Directory](licensing-groups-assign.md)
* [Licentieproblemen voor een groep vaststellen en oplossen in Azure Active Directory](licensing-groups-resolve-problems.md)
* [Gebruikers met een afzonderlijke licentie migreren naar licenties op basis van groepen in Azure Active Directory](licensing-groups-migrate-users.md)
* [Gebruikers tussen product licenties migreren met op groepen gebaseerde licentie verlening in Azure Active Directory](licensing-groups-change-licenses.md)
