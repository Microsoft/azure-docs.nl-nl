---
title: 'Azure AD Connect: ontwerp concepten | Microsoft Docs'
description: In dit onderwerp vindt u informatie over bepaalde implementatie gebieden
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 4114a6c0-f96a-493c-be74-1153666ce6c9
ms.service: active-directory
ms.custom: azure-ad-connect
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: baa03499cc11bda24ead986dd64621572484cbb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89279649"
---
# <a name="azure-ad-connect-design-concepts"></a>Azure AD Connect: ontwerp concepten
Het doel van dit document is het beschrijven van gebieden die tijdens het implementatie ontwerp van Azure AD Connect moeten worden beschouwd. Dit document is een grondige kennis van bepaalde gebieden en deze concepten worden ook kort beschreven in andere documenten.

## <a name="sourceanchor"></a>Source anchor
Het kenmerk source anchor wordt gedefinieerd als *een kenmerk dat tijdens de levens duur van een object onveranderbaar* is. Het identificeert een object op unieke wijze als hetzelfde object op locatie en in azure AD. Het kenmerk wordt ook wel **immutableId** genoemd en de twee namen worden uitgewisseld gebruikt.

Het woord onveranderbaar, dat wil zeggen ' kan niet worden gewijzigd ', is belang rijk voor dit document. Omdat de waarde van dit kenmerk niet kan worden gewijzigd nadat deze is ingesteld, is het belang rijk dat u een ontwerp kiest dat uw scenario ondersteunt.

Het kenmerk wordt gebruikt voor de volgende scenario's:

* Wanneer een nieuwe sync engine-server is gebouwd of opnieuw is opgebouwd na een nood herstel scenario, koppelt dit kenmerk bestaande objecten in azure AD met objecten on-premises.
* Als u overstapt van een Cloud-only-identiteit naar een gesynchroniseerd identiteits model, kunt u met dit kenmerk objecten ' hard matchen ' bestaande objecten in azure AD met on-premises objecten.
* Als u Federatie gebruikt, wordt dit kenmerk in combi natie met de **userPrincipalName** in de claim gebruikt om een gebruiker uniek te identificeren.

Dit onderwerp heeft alleen betrekking op source Anchor, aangezien deze betrekking hebben op gebruikers. Dezelfde regels zijn van toepassing op alle object typen, maar alleen voor gebruikers die dit probleem doorgaans is.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Een goed source Anchor kenmerk selecteren
De kenmerk waarde moet voldoen aan de volgende regels:

* Minder dan 60 tekens lang
  * Tekens die geen a-z, A-Z of 0-9 zijn, worden gecodeerd en geteld als drie tekens
* Geen speciaal teken: &#92;! # $% & * +/=? ^ &#96; {} | ~ < >  () '; : , [ ] " \@ _
* Moet globaal uniek zijn
* Moet een teken reeks, een geheel getal of een binaire waarde zijn
* Mag niet worden gebaseerd op de naam van de gebruiker omdat deze kunnen veranderen
* Mag niet hoofdletter gevoelig zijn en waarden voor komen die per geval kunnen verschillen
* Moet worden toegewezen wanneer het object wordt gemaakt

Als de geselecteerde source Anchor niet van het type teken reeks is, wordt de kenmerk waarde door Azure AD Connect Base64Encode om ervoor te zorgen dat er geen speciale tekens worden weer gegeven. Als u een andere Federatie server dan ADFS gebruikt, moet u ervoor zorgen dat uw server ook het kenmerk kan Base64Encode.

Het kenmerk source Anchor is hoofdletter gevoelig. De waarde ' JANJANSEN ' is niet hetzelfde als ' JANJANSEN '. Maar u hebt geen twee verschillende objecten met slechts een verschil in het geval.

Als u één forest on-premises hebt, is het kenmerk dat u moet gebruiken **ObjectGUID**. Dit is ook het kenmerk dat wordt gebruikt wanneer u snelle instellingen in Azure AD Connect gebruikt en ook het kenmerk dat wordt gebruikt door DirSync.

Als u meerdere forests hebt en gebruikers niet tussen forests en domeinen verplaatst, is **ObjectGUID** een goed kenmerk dat u ook in dit geval kunt gebruiken.

Als u gebruikers tussen forests en domeinen verplaatst, moet u een kenmerk vinden dat niet verandert of kan worden verplaatst met de gebruikers tijdens de verplaatsing. Een aanbevolen benadering is het introduceren van een synthetisch kenmerk. Een kenmerk dat iets zou kunnen bevatten dat lijkt op een GUID, zou geschikt zijn. Tijdens het maken van het object wordt een nieuwe GUID gemaakt en gestempeld op de gebruiker. Een aangepaste synchronisatie regel kan worden gemaakt op de server van de synchronisatie-engine om deze waarde te maken op basis van de **ObjectGUID** en het geselecteerde kenmerk in toevoegen bij te werken. Wanneer u het object verplaatst, moet u ook de inhoud van deze waarde kopiëren.

Een andere oplossing is het kiezen van een bestaand kenmerk dat u niet wijzigt. Veelgebruikte kenmerken zijn onder andere werk **nemers**. Als u overweegt een kenmerk dat letters bevat, moet u ervoor zorgen dat er geen kans is (hoofd letters en kleine letters) kan worden gewijzigd voor de waarde van het kenmerk. Ongeldige kenmerken die niet moeten worden gebruikt, zijn onder andere die kenmerken met de naam van de gebruiker. In een huwelijk of echtheid wordt de naam naar verwachting gewijzigd. Dit is niet toegestaan voor dit kenmerk. Dit is ook een van de redenen waarom kenmerken zoals **userPrincipalName**, **mail** en **targetAddress** niet zelfs kunnen worden geselecteerd in de installatie wizard van Azure AD Connect. Deze kenmerken bevatten ook het \@ teken ' ', wat niet is toegestaan in de source Anchor.

### <a name="changing-the-sourceanchor-attribute"></a>Het kenmerk source Anchor wijzigen
De waarde van het kenmerk source Anchor kan niet worden gewijzigd nadat het object is gemaakt in azure AD en de identiteit is gesynchroniseerd.

Daarom zijn de volgende beperkingen van toepassing op Azure AD Connect:

* Het kenmerk source Anchor kan alleen worden ingesteld tijdens de eerste installatie. Als u de installatie wizard opnieuw uitvoeren, is deze optie alleen-lezen. Als u deze instelling wilt wijzigen, moet u deze verwijderen en opnieuw installeren.
* Als u een andere Azure AD Connect-server installeert, moet u hetzelfde source Anchor-kenmerk selecteren als dat eerder is gebruikt. Als u DirSync eerder hebt gebruikt en naar Azure AD Connect hebt verplaatst, moet u **ObjectGUID** gebruiken, want dit is het kenmerk dat wordt gebruikt door DirSync.
* Als de waarde voor source anchor wordt gewijzigd nadat het object is geëxporteerd naar Azure AD, wordt door Azure AD Connect sync een fout gegenereerd en worden er geen wijzigingen meer aangebracht in dat object voordat het probleem is opgelost en wordt de source Anchor weer gewijzigd in de bron directory.

## <a name="using-ms-ds-consistencyguid-as-sourceanchor"></a>MS-DS-ConsistencyGuid gebruiken als source anchor
Azure AD Connect (versie 1.1.486.0 en ouder) maakt standaard gebruik van objectGUID als het kenmerk source Anchor. ObjectGUID wordt door het systeem gegenereerd. U kunt de waarde niet opgeven bij het maken van on-premises AD-objecten. Zoals beschreven in sectie [Source Anchor](#sourceanchor)zijn er scenario's waarin u de source Anchor-waarde moet opgeven. Als de scenario's van toepassing zijn op u, moet u een configureerbaar AD-kenmerk (bijvoorbeeld MS-DS-ConsistencyGuid) gebruiken als het kenmerk source Anchor.

Azure AD Connect (versie 1.1.524.0 en After) vereenvoudigt nu het gebruik van het kenmerk source Anchor van MS-DS-ConsistencyGuid. Wanneer u deze functie gebruikt, worden de synchronisatie regels door Azure AD Connect automatisch geconfigureerd voor:

1. Gebruik MS-DS-ConsistencyGuid als het kenmerk source Anchor voor gebruikers objecten. ObjectGUID wordt gebruikt voor andere object typen.

2. Voor een gegeven on-premises AD-gebruikers object waarvan het MS-DS-ConsistencyGuid-kenmerk niet is ingevuld, schrijft Azure AD Connect de objectGUIDwaarde terug naar het kenmerk MS-DS-ConsistencyGuid in on-premises Active Directory. Nadat het kenmerk MS-DS-ConsistencyGuid is ingevuld, Azure AD Connect het object vervolgens naar Azure AD exporteert.

>[!NOTE]
> Wanneer een on-premises AD-object wordt geïmporteerd in Azure AD Connect (dat wil zeggen, geïmporteerd in de AD connector-ruimte en geprojecteerd in de tekst), kunt u de source Anchor waarde niet meer wijzigen. Als u de source Anchor-waarde voor een gegeven on-premises AD-object wilt opgeven, configureert u het MS-DS-ConsistencyGuid-kenmerk voordat het wordt geïmporteerd in Azure AD Connect.

### <a name="permission-required"></a>Machtiging vereist
Voor een goede werking van deze functie moet het AD DS-account dat wordt gebruikt om te synchroniseren met on-premises Active Directory, schrijf machtigingen krijgen voor het kenmerk MS-DS-ConsistencyGuid in on-premises Active Directory.

### <a name="how-to-enable-the-consistencyguid-feature---new-installation"></a>De ConsistencyGuid-functie inschakelen-nieuwe installatie
U kunt het gebruik van ConsistencyGuid als source Anchor inschakelen tijdens een nieuwe installatie. In deze sectie wordt beschreven hoe u een snelle en aangepaste installatie vindt in details.

  >[!NOTE]
  > Alleen nieuwere versies van Azure AD Connect (1.1.524.0 en After) ondersteunen het gebruik van ConsistencyGuid als source Anchor tijdens een nieuwe installatie.

### <a name="how-to-enable-the-consistencyguid-feature"></a>De functie ConsistencyGuid inschakelen

#### <a name="express-installation"></a>Snelle installatie
Wanneer u Azure AD Connect installeert met de Express-modus, bepaalt de wizard Azure AD Connect automatisch het meest geschikte AD-kenmerk dat wordt gebruikt als het kenmerk source Anchor met behulp van de volgende logica:

* Eerst vraagt de Azure AD Connect wizard uw Azure AD-Tenant op om het AD-kenmerk op te halen dat wordt gebruikt als het kenmerk source anchor in de vorige Azure AD Connect installatie (indien van toepassing). Als deze informatie beschikbaar is, gebruikt Azure AD Connect hetzelfde AD-kenmerk.

  >[!NOTE]
  > Alleen nieuwere versies van Azure AD Connect (1.1.524.0 en After) slaan informatie op in uw Azure AD-Tenant over het kenmerk source Anchor dat wordt gebruikt tijdens de installatie. Oudere versies van Azure AD Connect niet.

* Als informatie over het gebruikte kenmerk source Anchor niet beschikbaar is, controleert de wizard de status van het kenmerk MS-DS-ConsistencyGuid in uw on-premises Active Directory. Als het kenmerk niet is geconfigureerd voor een object in de Directory, gebruikt de wizard de MS-DS-ConsistencyGuid als het kenmerk source Anchor. Als het kenmerk is geconfigureerd voor een of meer objecten in de Directory, sluit de wizard het kenmerk dat door andere toepassingen wordt gebruikt en is het niet geschikt als source Anchor-kenmerk...

* In dat geval valt de wizard terug op het gebruik van objectGUID als het kenmerk source Anchor.

* Zodra het kenmerk source anchor wordt bepaald, slaat de wizard de informatie op in uw Azure AD-Tenant. De informatie wordt gebruikt door de volgende installatie van Azure AD Connect.

Zodra de snelle installatie is voltooid, wordt in de wizard gemeld welk kenmerk is verzameld als bron anker kenmerk.

![Wizard informeert het AD-kenmerk dat is verzameld voor source anchor](./media/plan-connect-design-concepts/consistencyGuid-01.png)

#### <a name="custom-installation"></a>Aangepaste installatie
Wanneer u Azure AD Connect installeert met aangepaste modus, biedt de wizard Azure AD Connect twee opties voor het configureren van het kenmerk source anchor:

![Aangepaste installatie-source Anchor-configuratie](./media/plan-connect-design-concepts/consistencyGuid-02.png)

| Instelling | Beschrijving |
| --- | --- |
| Azure het bronanker voor mij laten beheren | Selecteer deze optie als u wilt dat Azure AD het kenmerk voor u selecteert. Als u deze optie selecteert, past Azure AD Connect wizard dezelfde [Source Anchor-kenmerk selectie logica toe die wordt gebruikt tijdens de snelle installatie](#express-installation). Net als bij snelle installatie, informeert de wizard dat het kenmerk is verzameld als het kenmerk bron anker nadat de aangepaste installatie is voltooid. |
| Een specifiek kenmerk | Selecteer deze optie als u een bestaand AD-kenmerk opgeeft als het kenmerk sourceAnchor. |

### <a name="how-to-enable-the-consistencyguid-feature---existing-deployment"></a>De functie ConsistencyGuid inschakelen-bestaande implementatie
Als u een bestaande Azure AD Connect-implementatie hebt die objectGUID gebruikt als het kenmerk bron anker, kunt u deze overschakelen naar het gebruik van ConsistencyGuid in plaats daarvan.

>[!NOTE]
> Alleen nieuwere versies van Azure AD Connect (1.1.552.0 en After) ondersteunen de overstap van ObjectGuid naar ConsistencyGuid als bron anker kenmerk.

Overschakelen van objectGUID naar ConsistencyGuid als het kenmerk bron anker:

1. Start de wizard Azure AD Connect en klik op **configureren** om naar het scherm taken te gaan.

2. Selecteer de optie **bron anker taak configureren** en klik op **volgende**.

   ![ConsistencyGuid inschakelen voor bestaande implementatie-stap 2](./media/plan-connect-design-concepts/consistencyguidexistingdeployment01.png)

3. Voer uw Azure AD-beheerders referenties in en klik op **volgende**.

4. Azure AD Connect wizard analyseert de status van het kenmerk MS-DS-ConsistencyGuid in uw on-premises Active Directory. Als het kenmerk niet is geconfigureerd voor een object in de map, wordt door Azure AD Connect afgesloten dat er momenteel geen andere toepassing is die het kenmerk gebruikt en veilig is om het te gebruiken als bron anker kenmerk. Klik op **Volgende** om verder te gaan.

   ![ConsistencyGuid inschakelen voor bestaande implementatie-stap 4](./media/plan-connect-design-concepts/consistencyguidexistingdeployment02.png)

5. Klik in het scherm **gereed voor configuratie** op **configureren** om de configuratie wijziging door te voeren.

   ![ConsistencyGuid inschakelen voor bestaande implementatie-stap 5](./media/plan-connect-design-concepts/consistencyguidexistingdeployment03.png)

6. Zodra de configuratie is voltooid, geeft de wizard aan dat MS-DS-ConsistencyGuid nu wordt gebruikt als het kenmerk bron anker.

   ![ConsistencyGuid inschakelen voor bestaande implementatie-stap 6](./media/plan-connect-design-concepts/consistencyguidexistingdeployment04.png)

Als het kenmerk tijdens de analyse is geconfigureerd voor een of meer objecten in de Directory, wordt het kenmerk door de wizard afgesloten en wordt er een fout geretourneerd, zoals in het onderstaande diagram wordt weer gegeven. Deze fout kan ook optreden als u de ConsistencyGuid-functie op uw primaire Azure AD Connect server al hebt ingeschakeld en u hetzelfde op uw staging-server wilt doen.

![ConsistencyGuid inschakelen voor bestaande implementatie-fout](./media/plan-connect-design-concepts/consistencyguidexistingdeploymenterror.png)

 Als u zeker weet dat het kenmerk niet wordt gebruikt door andere bestaande toepassingen, kunt u de fout onderdrukken door de Azure AD Connect wizard opnieuw te starten met de opgegeven **/SkipLdapSearch** -switch. Voer hiervoor de volgende opdracht uit in de opdracht prompt:

```
"c:\Program Files\Microsoft Azure Active Directory Connect\AzureADConnect.exe" /SkipLdapSearch
```

### <a name="impact-on-ad-fs-or-third-party-federation-configuration"></a>Gevolgen voor AD FS of Federatie configuratie van derden
Als u Azure AD Connect gebruikt voor het beheren van on-premises AD FS-implementatie, werkt de Azure AD Connect de claim regels automatisch bij om hetzelfde AD-kenmerk als source Anchor te gebruiken. Dit zorgt ervoor dat de door ADFS gegenereerde ImmutableID-claim consistent is met de source Anchor-waarden die zijn geëxporteerd naar Azure AD.

Als u AD FS buiten Azure AD Connect beheert of als u Federatie servers van derden gebruikt voor verificatie, moet u de claim regels voor ImmutableID-claim hand matig bijwerken zodat deze consistent zijn met de source Anchor-waarden die zijn geëxporteerd naar Azure AD, zoals beschreven in de sectie artikel [wijzigen AD FS claim regels](./how-to-connect-fed-management.md#modclaims). De wizard retourneert de volgende waarschuwing nadat de installatie is voltooid:

![Federatie configuratie van derden](./media/plan-connect-design-concepts/consistencyGuid-03.png)

### <a name="adding-new-directories-to-existing-deployment"></a>Nieuwe directory's toevoegen aan bestaande implementatie
Stel dat u Azure AD Connect hebt geïmplementeerd met de functie ConsistencyGuid ingeschakeld en nu een andere map wilt toevoegen aan de implementatie. Wanneer u de map probeert toe te voegen, controleert Azure AD Connect wizard de status van het kenmerk MS-DS-ConsistencyGuid in de Directory. Als het kenmerk is geconfigureerd voor een of meer objecten in de Directory, sluit de wizard het kenmerk dat door andere toepassingen wordt gebruikt en wordt er een fout geretourneerd, zoals in het onderstaande diagram wordt weer gegeven. Als u zeker weet dat het kenmerk niet wordt gebruikt door bestaande toepassingen, kunt u de fout onderdrukken door de Azure AD Connect wizard opnieuw te starten met de **/SkipLdapSearch** -switch die is opgegeven zoals hierboven is beschreven, of moet u contact opnemen met de ondersteuning voor meer informatie.

![Nieuwe directory's toevoegen aan bestaande implementatie](./media/plan-connect-design-concepts/consistencyGuid-04.png)

## <a name="azure-ad-sign-in"></a>Aanmelden bij Azure AD
Tijdens de integratie van uw on-premises Directory met Azure AD is het belang rijk om te begrijpen hoe de synchronisatie-instellingen van invloed kunnen zijn op de manier waarop de gebruiker zich verifieert. In azure AD wordt userPrincipalName (UPN) gebruikt voor het verifiëren van de gebruiker. Wanneer u uw gebruikers synchroniseert, moet u echter het kenmerk dat moet worden gebruikt voor de waarde van userPrincipalName zorgvuldig kiezen.

### <a name="choosing-the-attribute-for-userprincipalname"></a>Het kenmerk voor userPrincipalName kiezen
Wanneer u het kenmerk selecteert voor het opgeven van de waarde van UPN die moet worden gebruikt in azure, moet ervoor worden gezorgd

* De kenmerk waarden voldoen aan de UPN-syntaxis (RFC 822), dat wil zeggen, het moet het domein notatie username zijn \@
* Het achtervoegsel in de waarden komt overeen met een van de geverifieerde aangepaste domeinen in azure AD

In snelle instellingen is de aangenomen keuze voor het kenmerk userPrincipalName. Als het kenmerk userPrincipalName niet de waarde bevat waarmee gebruikers zich moeten aanmelden bij Azure, moet u **aangepaste installatie** kiezen.

### <a name="custom-domain-state-and-upn"></a>Aangepaste domein status en UPN
Het is belang rijk om ervoor te zorgen dat er een geverifieerd domein is voor het UPN-achtervoegsel.

John is een gebruiker in contoso.com. U wilt dat John de on-premises UPN John \@ contoso.com gebruikt om u aan te melden bij Azure nadat u de gebruikers hebt gesynchroniseerd met uw Azure AD-contoso.onmicrosoft.com. Hiervoor moet u contoso.com toevoegen en verifiëren als aangepast domein in azure AD voordat u kunt beginnen met het synchroniseren van de gebruikers. Als het UPN-achtervoegsel van John, bijvoorbeeld contoso.com, niet overeenkomt met een geverifieerd domein in azure AD, wordt het UPN-achtervoegsel door Azure AD vervangen door contoso.onmicrosoft.com.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Niet-routeerbaar bare on-premises domeinen en UPN voor Azure AD
Sommige organisaties hebben niet-routeerbaare domeinen, zoals contoso. local of eenvoudige domeinen met één label, zoals contoso. U kunt een niet-routeerbaar domein niet verifiëren in azure AD. Azure AD Connect kan alleen worden gesynchroniseerd met een geverifieerd domein in azure AD. Wanneer u een Azure AD-Directory maakt, wordt er een routeerbaar domein gemaakt dat het standaard domein voor uw Azure AD wordt, bijvoorbeeld contoso.onmicrosoft.com. Daarom is het nood zakelijk om een ander routeerbaar domein te controleren in een scenario voor het geval dat u niet wilt synchroniseren met het standaard domein onmicrosoft.com.

Lees [uw aangepaste domein naam toevoegen aan Azure Active Directory](../fundamentals/add-custom-domain.md) voor meer informatie over het toevoegen en verifiëren van domeinen.

Azure AD Connect detecteert of u in een niet-routeerbaar domein omgeving werkt en vervolgens op de juiste wijze waarschuwt voor snelle instellingen. Als u in een niet-routeerbaar domein werkt, is het waarschijnlijk dat de UPN, van de gebruikers, niet-Routeer bare achtervoegsels bevat. Als u bijvoorbeeld onder contoso. local werkt Azure AD Connect, wordt u geadviseerd om aangepaste instellingen te gebruiken, in plaats van snelle instellingen te gebruiken. Met aangepaste instellingen kunt u het kenmerk opgeven dat moet worden gebruikt als UPN om zich aan te melden bij Azure nadat de gebruikers zijn gesynchroniseerd met Azure AD.

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).