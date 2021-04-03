---
title: 'Azure AD Connect Sync: informatie over de standaard configuratie | Microsoft Docs'
description: In dit artikel wordt de standaard configuratie in Azure AD Connect Sync beschreven.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ed876f22-6892-4b9d-acbe-6a2d112f1cd1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e55526e0a63a0c603e2b62ccb3ac0efed911cff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996624"
---
# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Azure AD Connect-synchronisatie: inzicht in de standaardconfiguratie
In dit artikel worden de out-of-box-configuratie regels uitgelegd. De regels worden gedocumenteerd en hoe deze regels van invloed zijn op de configuratie. U wordt ook begeleid bij de standaard configuratie van Azure AD Connect Sync. Het doel is dat de lezer begrijpt hoe het configuratie model, met de naam declaratief inrichten, in een echt wereld voorbeeld werkt. In dit artikel wordt ervan uitgegaan dat u Azure AD Connect synchronisatie al hebt geïnstalleerd en geconfigureerd met behulp van de installatie wizard.

Lees [inzicht in declaratieve inrichting](concept-azure-ad-connect-sync-declarative-provisioning.md)voor meer informatie over de details van het configuratie model.

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Out-of-Box-regels van on-premises naar Azure AD
De volgende expressies vindt u in de out-of-box-configuratie.

### <a name="user-out-of-box-rules"></a>Out-of-Box-regels voor gebruikers
Deze regels zijn ook van toepassing op het object type iNetOrgPerson.

Een gebruikers object moet voldoen aan het volgende om te worden gesynchroniseerd:

* Moet een source Anchor hebben.
* Nadat het object is gemaakt in azure AD, kan source Anchor niet meer worden gewijzigd. Als de waarde voor on-premises wordt gewijzigd, wordt het object niet meer gesynchroniseerd totdat de vorige waarde van de source anchor wordt gewijzigd.
* Het kenmerk accountEnabled (userAccountControl) moet zijn ingevuld. Met een on-premises Active Directory, is dit kenmerk altijd aanwezig en gevuld.

De volgende gebruikers objecten zijn **niet** gesynchroniseerd met Azure AD:

* `IsPresent([isCriticalSystemObject])`. Zorg ervoor dat veel out-of-Box-objecten in Active Directory, zoals het ingebouwde Administrator account, niet zijn gesynchroniseerd.
* `IsPresent([sAMAccountName]) = False`. Zorg ervoor dat gebruikers objecten zonder het kenmerk sAMAccountName niet zijn gesynchroniseerd. Dit geldt alleen voor het uitvoeren van een domein dat is bijgewerkt van NT4.
* `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Synchroniseer niet het service account dat wordt gebruikt door Azure AD Connect Sync en de eerdere versies.
* Synchroniseer geen Exchange-accounts die niet werken in Exchange Online.
  * `[sAMAccountName] = "SUPPORT_388945a0"`
  * `Left([mailNickname], 14) = "SystemMailbox{"`
  * `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
  * `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
* Synchroniseer geen objecten die niet werken in Exchange Online.
  `CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
  Dit bitmasker (&H21C07000) zou de volgende objecten kunnen filteren:
  * Open bare map met e-mail functionaliteit (in preview versie 1.1.524.0)
  * System Attendant-Postvak
  * Postvak database postvak (systeem postvak)
  * Universele beveiligings groep (is niet van toepassing voor een gebruiker, maar is wel aanwezig voor verouderde redenen)
  * Niet-universele groep (is niet van toepassing voor een gebruiker, maar is wel aanwezig voor verouderde redenen)
  * Postvak abonnement
  * Detectie Postvak
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Synchroniseer geen objecten voor het slacht offer van de replicatie.

De volgende kenmerk regels zijn van toepassing:

* `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. Het kenmerk source Anchor is niet bijgedragen vanuit een gekoppeld postvak. Er wordt van uitgegaan dat als er een gekoppeld postvak is gevonden, het daad werkelijke account later wordt toegevoegd.
* Aan Exchange gerelateerde kenmerken worden alleen gesynchroniseerd als het kenmerk **mailNickname** een waarde heeft.
* Wanneer er meerdere forests zijn, worden de kenmerken in de volgende volg orde gebruikt:
  1. Kenmerken met betrekking tot aanmelden (bijvoorbeeld userPrincipalName) zijn bijgedragen vanuit het forest met een ingeschakeld account.
  2. Kenmerken die kunnen worden gevonden in een Exchange-GAL (algemene adres lijst), zijn bijgedragen vanuit het forest met een Exchange-postvak.
  3. Als er geen postvak kan worden gevonden, kunnen deze kenmerken afkomstig zijn uit elk forest.
  4. Aan Exchange gerelateerde kenmerken (technische kenmerken die niet zichtbaar zijn in de GAL) zijn van de forest bijgebracht `mailNickname ISNOTNULL` .
  5. Als er meerdere forests aan een van deze regels voldoen, wordt de aanmaak volgorde (datum/tijd) van de connectors (forests) gebruikt om te bepalen welk forest de kenmerken bijdraagt. Het eerste forest dat is verbonden, is het eerste forest dat wordt gesynchroniseerd. 

### <a name="contact-out-of-box-rules"></a>Contact opnemen met out-of-Box-regels
Een contact object moet voldoen aan het volgende om te worden gesynchroniseerd:

* De contact persoon moet zijn ingeschakeld voor e-mail. Deze wordt gecontroleerd met de volgende regels:
  * `IsPresent([proxyAddresses]) = True)`. Het kenmerk proxyAddresses moet worden ingevuld.
  * U vindt een primair e-mail adres in het kenmerk proxyAddresses of het kenmerk mail. De aanwezigheid van een \@ wordt gebruikt om te controleren of de inhoud een e-mail adres is. Een van deze twee regels moet worden geëvalueerd als waar.
    * `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. Is er een vermelding met ' SMTP: ' en als dat het geval is, kan \@ deze in de teken reeks worden gevonden?
    * `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. Is het e-mail kenmerk ingevuld en als dit zo is, kan \@ het worden gevonden in de teken reeks?

De volgende contact objecten zijn **niet** gesynchroniseerd met Azure AD:

* `IsPresent([isCriticalSystemObject])`. Zorg ervoor dat er geen contact objecten zijn die als kritiek zijn gemarkeerd, worden gesynchroniseerd. Dit is niet mogelijk met een standaard configuratie.
* `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
* `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Deze objecten werken niet in Exchange Online.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Synchroniseer geen objecten voor het slacht offer van de replicatie.

### <a name="group-out-of-box-rules"></a>Out-of-Box-regels groeperen
Een groeps object moet voldoen aan het volgende om te worden gesynchroniseerd:

* Moet minder dan 50.000 leden hebben. Dit aantal is het aantal leden in de on-premises groep.
  * Als er meer leden zijn voordat synchronisatie de eerste keer wordt gestart, wordt de groep niet gesynchroniseerd.
  * Als het aantal leden groeit vanaf het moment dat de eerste keer werd gemaakt, wordt de synchronisatie van 50.000 leden gestopt, totdat het aantal lidmaatschapen van 50.000 opnieuw is bereikt.
  * Opmerking: het 50.000-lidmaatschaps aantal wordt ook afgedwongen door Azure AD. Het is niet mogelijk om groepen met meer leden te synchroniseren, zelfs niet als u deze regel wijzigt of verwijdert.
* Als de groep een **distributie groep** is, moet het ook e-mail adres zijn ingeschakeld. Zie [out-of-Box-regels](#contact-out-of-box-rules) voor deze regel afdwingen.

De volgende groeps objecten zijn **niet** gesynchroniseerd met Azure AD:

* `IsPresent([isCriticalSystemObject])`. Zorg ervoor dat veel out-of-Box-objecten in Active Directory, zoals de ingebouwde groep Administrators, niet zijn gesynchroniseerd.
* `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. Verouderde groep die wordt gebruikt door DirSync.
* `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Functie groep.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Synchroniseer geen objecten voor het slacht offer van de replicatie.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>ForeignSecurityPrincipal out-of-Box-regels
FSPs worden toegevoegd aan het object any ( \* ) in de tekst. In werkelijkheid gebeurt deze samen voeging alleen voor gebruikers en beveiligings groepen. Deze configuratie zorgt ervoor dat meerdere forest-lidmaatschappen worden opgelost en correct worden weer gegeven in azure AD.

### <a name="computer-out-of-box-rules"></a>Out-of-Box-regels voor computer
Een computer object moet voldoen aan het volgende om te worden gesynchroniseerd:

* `userCertificate ISNOTNULL`. Alleen Windows 10-computers vullen dit kenmerk in. Alle computer objecten met een waarde in dit kenmerk worden gesynchroniseerd.

## <a name="understanding-the-out-of-box-rules-scenario"></a>Informatie over het scenario met de out-of-Box-regels
In dit voor beeld gebruiken we een implementatie met één account forest (A), één resource-forest (R) en één Azure AD-adres lijst.

![Afbeelding met scenario beschrijving](./media/concept-azure-ad-connect-sync-default-configuration/scenario.png)

In deze configuratie wordt ervan uitgegaan dat er een ingeschakeld account is in het account-forest en een uitgeschakeld account in de bron-forest met een gekoppeld postvak.

Ons doel met de standaard configuratie is:

* Kenmerken die betrekking hebben op aanmelden, worden gesynchroniseerd vanuit het forest met het ingeschakelde account.
* Kenmerken die kunnen worden gevonden in de GAL (algemene adres lijst), worden gesynchroniseerd vanuit het forest met het postvak. Als er geen postvak kan worden gevonden, wordt er een ander forest gebruikt.
* Als een gekoppeld postvak wordt gevonden, moet het gekoppelde ingeschakelde account worden gevonden voor het object dat naar Azure AD moet worden geëxporteerd.

### <a name="synchronization-rule-editor"></a>Editor voor synchronisatie regels
De configuratie kan worden weer gegeven en gewijzigd met de tool Synchronization Rules editor (SRE) en een snelkoppeling naar deze map vindt u in het menu Start.

![Pictogram voor de editor voor synchronisatie regels](./media/concept-azure-ad-connect-sync-default-configuration/sre.png)

De SRE is een Resource Kit en wordt geïnstalleerd met Azure AD Connect Sync. U moet lid zijn van de groep ADSyncAdmins om het te kunnen starten. Wanneer de app wordt gestart, ziet u er ongeveer als volgt uit:

![Synchronisatie regels binnenkomend](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

In dit deel venster ziet u alle synchronisatie regels die voor uw configuratie zijn gemaakt. Elke regel in de tabel is één synchronisatie regel. Aan de linkerkant onder regel typen, worden de twee verschillende typen weer gegeven: inkomend en uitgaand. Inkomend en uitgaand is afkomstig uit de weer gave van de tekst. U gaat voornamelijk richten op de regels voor binnenkomende verbindingen in dit overzicht. De werkelijke lijst met synchronisatie regels is afhankelijk van het gedetecteerde schema in AD. In de bovenstaande afbeelding heeft het account-forest (fabrikamonline.com) geen services, zoals Exchange en Lync, en zijn er geen synchronisatie regels voor deze services gemaakt. In het resource-forest (res.fabrikamonline.com) kunt u echter synchronisatie regels voor deze services vinden. De inhoud van de regels verschilt, afhankelijk van de gedetecteerde versie. In een implementatie met Exchange 2013 zijn bijvoorbeeld meer kenmerk stromen geconfigureerd dan in Exchange 2010/2007.

### <a name="synchronization-rule"></a>Synchronisatie regel
Een synchronisatie regel is een configuratie object met een set kenmerken die stromen wanneer aan een voor waarde wordt voldaan. Het wordt ook gebruikt om te beschrijven hoe een object in een connector ruimte is gerelateerd aan een object in de omgekeerde, ook wel **samen voegen** of **overeenkomst**. De synchronisatie regels hebben een prioriteits waarde die aangeeft hoe ze aan elkaar zijn gerelateerd. Een synchronisatie regel met een lagere numerieke waarde heeft een hogere prioriteit en een conflict met een kenmerk stroom heeft een hogere prioriteit dan WINS voor het oplossen van conflicten.

Bekijk een voor beeld van de synchronisatie regel **in vanuit AD: gebruiker AccountEnabled**. Markeer deze regel in de SRE en selecteer **bewerken**.

Omdat deze regel een out-of-Box-regel is, wordt er een waarschuwing weer gegeven wanneer u de regel opent. U moet geen wijzigingen aanbrengen [in out-of-Box-regels](how-to-connect-sync-best-practices-changing-default-configuration.md), zodat u wordt gevraagd wat uw bedoelingen zijn. In dit geval wilt u alleen de regel weer geven. Selecteer **Nee**.

![Waarschuwing voor synchronisatie regels](./media/concept-azure-ad-connect-sync-default-configuration/warningeditrule.png)

Een synchronisatie regel heeft vier configuratie secties: beschrijving, bereik filter, regels voor samen voegen en trans formaties.

#### <a name="description"></a>Description
De eerste sectie bevat basis informatie, zoals een naam en beschrijving.

![Tabblad Beschrijving in de editor voor synchronisatie regels](./media/concept-azure-ad-connect-sync-default-configuration/syncruledescription.png)

U vindt ook informatie over het verbonden systeem waaraan deze regel is gerelateerd, welk object type in het verbonden systeem van toepassing is en het object type van de tekst. Het object type van de tekst is altijd ongeacht of het type van het bron object een gebruiker, iNetOrgPerson of contact persoon is. Het type van het omgekeerde object mag niet worden gewijzigd, zodat het als een Gene riek type wordt gemaakt. Het type koppeling kan worden ingesteld op lid worden van, StickyJoin of inrichten. Deze instelling werkt samen met de sectie regels voor samen voegen en wordt later behandeld.

U kunt ook zien dat deze synchronisatie regel wordt gebruikt voor wachtwoord synchronisatie. Als een gebruiker zich in het bereik voor deze synchronisatie regel bevindt, wordt het wacht woord van on-premises naar de Cloud gesynchroniseerd (ervan uitgaande dat u de functie voor wachtwoord synchronisatie hebt ingeschakeld).

#### <a name="scoping-filter"></a>Bereik filter
De sectie Filter bereik wordt gebruikt om te configureren wanneer een synchronisatie regel moet worden toegepast. Omdat de naam van de synchronisatie regel aangeeft dat deze alleen moet worden toegepast voor ingeschakelde gebruikers, wordt het bereik geconfigureerd zodat het AD-kenmerk **userAccountControl** geen bit 2-set moet hebben. Wanneer de synchronisatie-engine een gebruiker in AD vindt, wordt deze synchronisatie regel toegepast wanneer **userAccountControl** is ingesteld op de decimale waarde 512 (ingeschakelde normale gebruiker). De regel wordt niet toegepast wanneer de gebruiker **userAccountControl** heeft ingesteld op 514 (uitgeschakelde normale gebruiker).

![Scherm afbeelding met de sectie ' filter bereik ' in het venster ' regel voor binnenkomende synchronisatie bewerken '.](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilter.png)

Het filter bereik heeft groepen en componenten die kunnen worden genest. Voor alle componenten in een groep moet worden voldaan aan een synchronisatie regel die moet worden toegepast. Wanneer er meerdere groepen zijn gedefinieerd, moet aan ten minste één groep worden voldaan om de regel toe te passen. Dat wil zeggen, een logische of wordt geëvalueerd tussen groepen en een logische en wordt geëvalueerd in een groep. Een voor beeld van deze configuratie vindt u in de regel voor uitgaande synchronisatie **naar Aad-Group-koppeling**. Er zijn verschillende synchronisatie filter groepen, bijvoorbeeld één voor beveiligings groepen ( `securityEnabled EQUAL True` ) en één voor distributie groepen ( `securityEnabled EQUAL False` ).

![Het tabblad bereik in de editor voor synchronisatie regels](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilterout.png)

Deze regel wordt gebruikt om te definiëren welke groepen moeten worden ingericht voor Azure AD. Voor distributie groepen moet e-mail zijn ingeschakeld om te worden gesynchroniseerd met Azure AD, maar voor beveiligings groepen is geen e-mail bericht vereist.

#### <a name="join-rules"></a>Regels voor samen voegen
De derde sectie wordt gebruikt om te configureren hoe objecten in de connector ruimte verband houden met objecten in de tekst. De regel die u eerder hebt bekeken, heeft geen configuratie voor regels voor samen voegen, dus in plaats daarvan gaat u naar **van AD – gebruiker toevoegen**.

![Het tabblad regels samen voegen in de editor voor synchronisatie regels](./media/concept-azure-ad-connect-sync-default-configuration/syncrulejoinrules.png)

De inhoud van de regel voor samen voeging is afhankelijk van de geselecteerde optie in de installatie wizard. Voor een regel voor binnenkomende verbindingen begint de evaluatie met een object in de bron connector ruimte en elke groep in de regels voor samen voegen wordt in de juiste volg orde geëvalueerd. Als een bron object wordt geëvalueerd om precies één object in de omgekeerde tekst te vergelijken met een van de join-regels, worden de objecten gekoppeld. Als alle regels zijn geëvalueerd en er geen overeenkomst is, wordt het koppelings type op de pagina beschrijving gebruikt. Als deze configuratie is ingesteld op **inrichten**, wordt een nieuw object gemaakt in het doel, het omgekeerde, als ten minste één kenmerk in de samenvoeg criteria aanwezig is (heeft een waarde). Om een nieuw object in te richten op de tekst is ook bekend als het **projecteren** van een object naar de tekst.

De regels voor samen voegen worden slechts eenmaal geëvalueerd. Wanneer een connector ruimte-object en een omgekeerd object worden toegevoegd, blijven ze gekoppeld zolang er nog steeds aan het bereik van de synchronisatie regel wordt voldaan.

Bij het evalueren van synchronisatie regels moet slechts één synchronisatie regel met gedefinieerde samenvoegings regels binnen het bereik vallen. Als er meerdere synchronisatie regels met regels voor samen voegen voor één object worden gevonden, wordt er een fout gegenereerd. Daarom is het best practice slechts één synchronisatie regel met samen voeging gedefinieerd wanneer meerdere synchronisatie regels binnen het bereik van een object vallen. In de out-of-box-configuratie voor Azure AD Connect synchronisatie, kunt u deze regels vinden door de naam te bekijken en deze te vinden met het woord **samen voegen** aan het einde van de naam. Een synchronisatie regel zonder gedefinieerde regels voor samen voegen past de kenmerk stromen toe wanneer een andere synchronisatie regel de objecten samenvoegde of een nieuw object in het doel heeft ingericht.

Als u de bovenstaande afbeelding bekijkt, kunt u zien dat de regel probeert **objectSID** toe te voegen met **msExchMasterAccountSID** (Exchange) en **msRTCSIP-OriginatorSid** (Lync). Dit is wat er wordt verwacht in een account-resource forest-topologie. U vindt dezelfde regel op alle forests. De veronderstelling is dat elke forest een account of een resource-forest zou kunnen zijn. Deze configuratie werkt ook als u accounts hebt die zich in één forest bevinden en niet moeten worden gekoppeld.

#### <a name="transformations"></a>Transformaties
De sectie trans formatie definieert alle kenmerk stromen die van toepassing zijn op het doel object wanneer de objecten zijn gekoppeld en waaraan het bereik filter is voldaan. Teruggaan naar de **in AD-** synchronisatie regel voor gebruikers AccountEnabled, vindt u de volgende trans formaties:

![Het tabblad trans formaties in de editor voor synchronisatie regels](./media/concept-azure-ad-connect-sync-default-configuration/syncruletransformations.png)

Als u deze configuratie in de context wilt opnemen in een Account-Resource-forest-implementatie, wordt verwacht dat deze een ingeschakeld account in het account-forest en een uitgeschakelde account in de bron-forest met de Exchange-en Lync-instellingen heeft gevonden. De synchronisatie regel die u bekijkt bevat de kenmerken die zijn vereist voor aanmelding. deze kenmerken moeten stromen van het forest met een ingeschakeld account. Al deze kenmerk stromen worden samen in één synchronisatie regel opgenomen.

Een trans formatie kan verschillende typen hebben: constante, direct en expressie.

* Met een constante stroom wordt altijd een hardcoded waarde stromen. In het bovenstaande voor beeld wordt altijd de waarde **True** ingesteld in het **accountEnabled** kenmerk met de naam.
* Met een directe stroom wordt altijd de waarde van het kenmerk in de bron naar het doel kenmerk gestroomd als-is.
* Het derde stroom type is expressie en biedt meer geavanceerde configuraties.

De expressie taal is VBA (Visual Basic for Applications), zodat gebruikers met de ervaring van Microsoft Office of VBScript de indeling kunnen herkennen. Kenmerken staan tussen vier Kante haken, [kenmerknaam]. Kenmerk namen en functie namen zijn hoofdletter gevoelig, maar in de editor voor synchronisatie regels worden de expressies geëvalueerd en wordt een waarschuwing gegeven als de expressie ongeldig is. Alle expressies worden weer gegeven op één regel met geneste functies. Om de kracht van de configuratie taal weer te geven, is dit de stroom voor pwdLastSet, maar er zijn aanvullende opmerkingen ingevoegd:

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .NET datetime, change it to the time format used by Azure AD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

Zie [uitleg over declaratieve inrichtings expressies](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) voor meer informatie over de expressie taal voor kenmerk stromen.

### <a name="precedence"></a>Prioriteit
U hebt nu een aantal afzonderlijke synchronisatie regels bekeken, maar de regels werken samen in de configuratie. In sommige gevallen wordt een kenmerk waarde bijgedragen van meerdere synchronisatie regels naar hetzelfde doel kenmerk. In dit geval wordt de kenmerk prioriteit gebruikt om te bepalen welk kenmerk WINS. Bekijk bijvoorbeeld het kenmerk source Anchor. Dit kenmerk is een belang rijk kenmerk waarmee u zich kunt aanmelden bij Azure AD. U kunt een kenmerk stroom voor dit kenmerk vinden in twee verschillende synchronisatie regels, **in vanuit AD: User AccountEnabled** en **in from AD: User common**. Als gevolg van de prioriteit van de synchronisatie regel, wordt het kenmerk source Anchor van de forest met een ingeschakeld account als eerste toegevoegd wanneer er meerdere objecten zijn gekoppeld aan het object van de tekst. Als er geen ingeschakelde accounts zijn, gebruikt de synchronisatie-engine de regel voor de volledige synchronisatie **in van AD: User common**. Deze configuratie zorgt ervoor dat zelfs voor accounts die zijn uitgeschakeld, nog steeds een source Anchor is.

![Synchronisatie regels binnenkomend](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

De prioriteit van synchronisatie regels wordt ingesteld in groepen door de installatie wizard. Alle regels in een groep hebben dezelfde naam, maar ze zijn verbonden met verschillende verbonden directory's. De-installatie wizard geeft de regel **in van AD: de gebruiker** neemt de hoogste prioriteit aan en doorloopt over alle verbonden ad-directory's. Vervolgens wordt de volgende groep regels in een vooraf gedefinieerde volg orde voortgezet. Binnen een groep worden de regels toegevoegd in de volg orde waarin de connectors zijn toegevoegd in de wizard. Als er via de wizard een andere connector wordt toegevoegd, worden de synchronisatie regels opnieuw gerangschikt en worden de regels van de nieuwe connector als laatste in elke groep ingevoegd.

### <a name="putting-it-all-together"></a>Alles samenvoegen
We weten nu voldoende over synchronisatie regels om te begrijpen hoe de configuratie werkt met de verschillende synchronisatie regels. Als u een gebruiker bekijkt en de kenmerken die aan de tekst zijn bijgedragen, worden de regels in de volgende volg orde toegepast:

| Name | Opmerking |
|:--- |:--- |
| Vanuit AD: gebruiker toevoegen |Regel voor het koppelen van connector ruimte-objecten met een omgekeerd. |
| In vanuit AD: User account ingeschakeld |Kenmerken die vereist zijn voor aanmelding bij Azure AD en Microsoft 365. We willen deze kenmerken van het ingeschakelde account. |
| In vanuit AD: gebruiker gemeen schappelijk van Exchange |Kenmerken gevonden in de globale adres lijst. We gaan ervan uit dat de kwaliteit van de gegevens het beste is in het forest waar het postvak van de gebruiker is gevonden. |
| In van AD: gebruiker algemeen |Kenmerken gevonden in de globale adres lijst. Als er geen postvak is gevonden, kan elk ander gekoppeld object de waarde van het kenmerk bijdragen. |
| In van AD: gebruikers uitwisseling |Bestaat alleen als Exchange is gedetecteerd. Hiermee worden alle kenmerken van infrastructuur uitwisseling stromen. |
| In vanuit AD: gebruikers Lync |Bestaat alleen als Lync is gedetecteerd. Hiermee worden alle Lync-kenmerken van de infra structuur stromen. |

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het configuratie model vindt u in [informatie over declaratieve inrichting](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Meer informatie over de expressie taal vindt u in [uitleg over declaratieve inrichtings expressies](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).
* Door gaan met het lezen van de out-of-box-configuratie voor [gebruikers en contact personen](concept-azure-ad-connect-sync-user-and-contacts.md)
* Zie een praktische wijziging aanbrengen met behulp van declaratieve inrichting in [hoe u een wijziging aanbrengt in de standaard configuratie](how-to-connect-sync-change-the-configuration.md).

**Overzichts onderwerpen**

* [Azure AD Connect synchroniseren: Synchronisatie begrijpen en aanpassen](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)

