---
title: Azure AD Connect-AD FS beheer en aanpassing | Microsoft Docs
description: AD FS beheer met Azure AD Connect en aanpassing van de aanmeldings ervaring van gebruikers AD FS met Azure AD Connect en Power shell.
keywords: AD FS, ADFS, AD FS beheer, AAD Connect, Connect, aanmelden, AD FS aanpassing, herstel vertrouwen, M365, Federatie, Relying Party
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc0c8c40e370579100c562e0289c97e3f5ce4236
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91274109"
---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Active Directory Federation Services beheren en aanpassen met behulp van Azure AD Connect
In dit artikel wordt beschreven hoe u Active Directory Federation Services (AD FS) kunt beheren en aanpassen met behulp van Azure Active Directory (Azure AD) Connect. Het bevat ook andere veelvoorkomende AD FS taken die u mogelijk moet uitvoeren voor een volledige configuratie van een AD FS-farm.

| Onderwerp | Wat het betreft |
|:--- |:--- |
| **AD FS beheren** | |
| [De vertrouwens relatie herstellen](#repairthetrust) |De Federatie vertrouwensrelatie met Microsoft 365 herstellen. |
| [Communiceren met Azure AD met een alternatieve aanmeldings-ID](#alternateid) | Federatie configureren met alternatieve aanmeldings-ID  |
| [Een AD FS-server toevoegen](#addadfsserver) |Een AD FS-farm uitbreiden met een extra AD FS-server. |
| [Een AD FS Web Application proxy-server toevoegen](#addwapserver) |Een AD FS-farm uitbreiden met een extra WAP-server (Web Application proxy). |
| [Een federatief domein toevoegen](#addfeddomain) |Het toevoegen van een federatief domein. |
| [Het TLS/SSL-certificaat bijwerken](how-to-connect-fed-ssl-update.md)| Het TLS/SSL-certificaat voor een AD FS-farm bijwerken. |
| **AD FS aanpassen** | |
| [Een aangepast bedrijfs logo of een aangepaste afbeelding toevoegen](#customlogo) |Een AD FS-aanmeldings pagina aanpassen met een bedrijfs logo en een afbeelding. |
| [Een beschrijving van een aanmelding toevoegen](#addsignindescription) |Een beschrijving van een aanmeldings pagina toevoegen. |
| [AD FS claim regels wijzigen](#modclaims) |Het wijzigen van AD FS claims voor verschillende Federatie scenario's. |

## <a name="manage-ad-fs"></a>AD FS beheren
U kunt met behulp van de Azure AD Connect wizard diverse taken met betrekking tot AD FS uitvoeren in Azure AD Connect met minimale tussen komst van de gebruiker. Nadat u klaar bent met het installeren van Azure AD Connect door de wizard uit te voeren, kunt u de wizard opnieuw uitvoeren om aanvullende taken uit te voeren.

## <a name="repair-the-trust"></a><a name="repairthetrust"></a>De vertrouwens relatie herstellen 
U kunt Azure AD Connect gebruiken om de huidige status van de AD FS en Azure AD-vertrouwens relatie te controleren en de juiste maat regelen te nemen om de vertrouwens relatie te herstellen. Volg deze stappen om uw Azure AD-en AD FS-vertrouwens relatie te herstellen.

1. Selecteer **Aad en ADFS-vertrouwens relatie** in de lijst met extra taken herstellen.
   ![AAD-en ADFS-vertrouwens relatie herstellen](./media/how-to-connect-fed-management/RepairADTrust1.PNG)

2. Geef op de pagina **verbinding maken met Azure AD** uw globale beheerders referenties voor Azure AD op en klik op **volgende**.
   ![Scherm opname van de pagina ' verbinding maken met Azure AD ' met voor beelden van referenties die zijn ingevoerd.](./media/how-to-connect-fed-management/RepairADTrust2.PNG)

3. Voer op de pagina **referenties voor externe toegang** de referenties voor de domein beheerder in.

   ![Scherm opname van de pagina ' Remote Access credentials ' met voor beelden van referenties ingevoerd.](./media/how-to-connect-fed-management/RepairADTrust3.PNG)

    Nadat u op **volgende** hebt geklikt, controleert Azure AD Connect de status van het certificaat en worden eventuele problemen weer gegeven.

    ![Status van certificaten](./media/how-to-connect-fed-management/RepairADTrust4.PNG)

    De pagina **gereed voor configuratie** toont de lijst met acties die worden uitgevoerd om de vertrouwens relatie te herstellen.

    ![Scherm opname van de pagina gereed voor configuratie met een lijst met acties.](./media/how-to-connect-fed-management/RepairADTrust5.PNG)

4. Klik op **installeren** om de vertrouwens relatie te herstellen.

> [!NOTE]
> Azure AD Connect kunt alleen certificaten repareren of actie ondernemen die zelfondertekend zijn. Azure AD Connect kan geen certificaten van derden herstellen.

## <a name="federate-with-azure-ad-using-alternateid"></a><a name="alternateid"></a>Communiceren met Azure AD met behulp van AlternateID 
Het is raadzaam om de on-premises UPN (User Principal Name) en de principal-naam van de Cloud gebruiker hetzelfde te houden. Als de lokale UPN gebruikmaakt van een niet-routeerbare domein (ex. Contoso. local) of kan niet worden gewijzigd vanwege lokale toepassings afhankelijkheden, kunt u het beste een alternatieve aanmeldings-ID instellen. Met de alternatieve aanmeldings-ID kunt u een aanmeldings ervaring configureren waarbij gebruikers zich kunnen aanmelden met een ander kenmerk dan hun UPN, zoals e-mail. De keuze voor de UPN-naam in Azure AD Connect standaard ingesteld op het kenmerk userPrincipalName in Active Directory. Als u een ander kenmerk kiest voor User Principal name en federeren met behulp van AD FS, dan configureert Azure AD Connect AD FS voor de alternatieve aanmeldings-ID. Hieronder ziet u een voor beeld van het kiezen van een ander kenmerk voor User Principal name:

![Alternatieve ID-kenmerk selectie](./media/how-to-connect-fed-management/attributeselection.png)

Het configureren van de alternatieve aanmeldings-ID voor AD FS bestaat uit twee belang rijke stappen:
1. **De juiste set met uitgifte claims configureren**: de claim regels voor uitgifte in de Azure AD Relying Party-vertrouwens relatie zijn gewijzigd om het geselecteerde userPrincipalName-kenmerk te gebruiken als de alternatieve id van de gebruiker.
2. **Alternatieve aanmeldings-id inschakelen in de AD FS configuratie**: de AD FS configuratie wordt bijgewerkt, zodat AD FS gebruikers in de juiste forests kunt opzoeken met de alternatieve id. Deze configuratie wordt ondersteund voor AD FS op Windows Server 2012 R2 (met KB2919355) of hoger. Als de AD FS servers 2012 R2 zijn, Azure AD Connect de aanwezigheid van de vereiste KB controleren. Als de KB niet wordt gedetecteerd, wordt een waarschuwing weer gegeven nadat de configuratie is voltooid, zoals hieronder wordt weer gegeven:

    ![Waarschuwing voor ontbrekende KB op 2012R2](./media/how-to-connect-fed-management/kbwarning.png)

    Als u de configuratie wilt corrigeren in geval van ontbrekende KB, installeert u de vereiste [KB2919355](https://go.microsoft.com/fwlink/?LinkID=396590) en herstelt u de vertrouwens relatie met [Aad en AD FS-vertrouwens relatie](#repairthetrust).

> [!NOTE]
> Lees de [alternatieve aanmeldings-id configureren](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) voor meer informatie over alternateID en stappen om deze hand matig te configureren

## <a name="add-an-ad-fs-server"></a><a name="addadfsserver"></a>Een AD FS-server toevoegen 

> [!NOTE]
> Azure AD Connect vereist het PFX-certificaat om een AD FS server toe te voegen. Daarom kunt u deze bewerking alleen uitvoeren als u de AD FS farm hebt geconfigureerd met behulp van Azure AD Connect.

1. Selecteer **een extra Federatie server implementeren** en klik op **volgende**.

   ![Aanvullende Federatie server](./media/how-to-connect-fed-management/AddNewADFSServer1.PNG)

2. Voer op de pagina **verbinding maken met Azure AD** uw globale beheerders referenties in voor Azure AD en klik op **volgende**.

   ![Scherm afbeelding waarin de pagina ' verbinding maken met Azure AD ' wordt weer gegeven, waarbij de voorbeeld referenties zijn ingevoerd.](./media/how-to-connect-fed-management/AddNewADFSServer2.PNG)

3. Geef de referenties voor de domein beheerder op.

   ![Referenties voor domein beheerder](./media/how-to-connect-fed-management/AddNewADFSServer3.PNG)

4. Azure AD Connect vraagt naar het wacht woord van het PFX-bestand dat u hebt opgegeven bij het configureren van uw nieuwe AD FS farm met Azure AD Connect. Klik op **wacht woord invoeren** om het wacht woord voor het pfx-bestand op te geven.

   ![Scherm opname waarin de pagina SSL-certificaat opgeven wordt weer gegeven met het venster certificaat wachtwoord geopend.](./media/how-to-connect-fed-management/AddNewADFSServer4.PNG)

    ![Scherm afbeelding met de pagina SSL-certificaat opgeven nadat een wacht woord voor het PFX-bestand is ingevoerd.](./media/how-to-connect-fed-management/AddNewADFSServer5.PNG)

5. Voer op de pagina **AD FS servers** de server naam of het IP-adres in dat moet worden toegevoegd aan de AD FS-farm.

   ![AD FS-servers](./media/how-to-connect-fed-management/AddNewADFSServer6.PNG)

6. Klik op **volgende** en ga door de pagina definitieve **configuratie** . Nadat Azure AD Connect het toevoegen van de servers aan de AD FS-farm hebt voltooid, krijgt u de mogelijkheid om de verbinding te controleren.

   ![Scherm opname van de pagina gereed voor configuratie met een lijst met te volt ooien acties nadat u op installeren hebt geklikt.](./media/how-to-connect-fed-management/AddNewADFSServer7.PNG)

    ![Scherm opname van de pagina ' installatie voltooid ' met het bericht ' de intranet configuratie is gecontroleerd ' wordt weer gegeven. ](./media/how-to-connect-fed-management/AddNewADFSServer8.PNG)

## <a name="add-an-ad-fs-wap-server"></a><a name="addwapserver"></a>Een AD FS WAP-server toevoegen 

> [!NOTE]
> Als u een WAP-server wilt toevoegen, moet Azure AD Connect het PFX-certificaat hebben. Daarom kunt u deze bewerking alleen uitvoeren als u de AD FS farm hebt geconfigureerd met behulp van Azure AD Connect.

1. Selecteer **Web Application proxy implementeren** in de lijst met beschik bare taken.

   ![Web Application proxy implementeren](./media/how-to-connect-fed-management/WapServer1.PNG)

2. Geef de referenties van de globale Azure-beheerder op.

   ![Scherm opname van de pagina ' verbinding maken met Azure AD ' met een voor beeld van een gebruikers naam en wacht woord.](./media/how-to-connect-fed-management/wapserver2.PNG)

3. Geef op de pagina **SSL-certificaat opgeven** het wacht woord op voor het pfx-bestand dat u hebt opgegeven toen u de AD FS farm met Azure AD Connect hebt geconfigureerd.
   ![Certificaat wachtwoord](./media/how-to-connect-fed-management/WapServer3.PNG)

    ![TLS/SSL-certificaat opgeven](./media/how-to-connect-fed-management/WapServer4.PNG)

4. Voeg de server toe die moet worden toegevoegd als WAP-server. Omdat de WAP-server mogelijk niet is toegevoegd aan het domein, vraagt de wizard om beheerders referenties aan de server die wordt toegevoegd.

   ![Referenties voor beheerders server](./media/how-to-connect-fed-management/WapServer5.PNG)

5. Geef op de pagina **proxy Trust referenties** de beheerders referenties op voor het configureren van de proxy vertrouwensrelatie en toegang tot de primaire server in de AD FS-farm.

   ![Referenties voor proxy vertrouwen](./media/how-to-connect-fed-management/WapServer6.PNG)

6. Op de pagina **gereed voor configuratie** bevat de wizard de lijst met acties die worden uitgevoerd.

   ![Scherm opname van de pagina gereed om te configureren met een lijst met acties die moeten worden uitgevoerd.](./media/how-to-connect-fed-management/WapServer7.PNG)

7. Klik op **installeren** om de configuratie te volt ooien. Nadat de configuratie is voltooid, krijgt u de mogelijkheid om de verbinding met de servers te controleren. Klik op **controleren** om de verbinding te controleren.

   ![Installatie voltooid](./media/how-to-connect-fed-management/WapServer8.PNG)

## <a name="add-a-federated-domain"></a><a name="addfeddomain"></a>Een federatief domein toevoegen 

Het is eenvoudig om een domein toe te voegen voor federatief met Azure AD met behulp van Azure AD Connect. Azure AD Connect voegt het domein voor Federatie toe en wijzigt de claim regels zo dat de uitgever correct wordt weer gegeven wanneer u meerdere domeinen hebt die worden gefederatied met Azure AD.

1. Als u een federatief domein wilt toevoegen, selecteert u de taak **een extra Azure AD-domein toevoegen**.

   ![Extra Azure AD-domein](./media/how-to-connect-fed-management/AdditionalDomain1.PNG)

2. Geef op de volgende pagina van de wizard de globale beheerders referenties voor Azure AD op.

   ![Verbinding maken met Azure AD](./media/how-to-connect-fed-management/AdditionalDomain2.PNG)

3. Geef de referenties van de domein beheerder op de pagina **referenties voor externe toegang** op.

   ![Referenties voor externe toegang](./media/how-to-connect-fed-management/additionaldomain3.PNG)

4. Op de volgende pagina bevat de wizard een lijst met Azure AD-domeinen waarmee u uw on-premises Directory kunt vergeven met. Kies het domein in de lijst.

   ![Azure AD-domein](./media/how-to-connect-fed-management/AdditionalDomain4.PNG)

    Nadat u het domein hebt gekozen, biedt de wizard u de juiste informatie over verdere acties die de wizard zal ondernemen en de impact van de configuratie. Als u in sommige gevallen een domein selecteert dat nog niet is geverifieerd in azure AD, biedt de wizard u informatie om u te helpen bij het verifiëren van het domein. Zie [uw aangepaste domein naam toevoegen aan Azure Active Directory](../fundamentals/add-custom-domain.md) voor meer informatie.

5. Klik op **Volgende**. Op de pagina **gereed voor configuratie ziet u** de lijst met acties die Azure AD Connect worden uitgevoerd. Klik op **installeren** om de configuratie te volt ooien.

   ![Klaar om te configureren](./media/how-to-connect-fed-management/AdditionalDomain5.PNG)

> [!NOTE]
> Gebruikers van het toegevoegde federatieve domein moeten worden gesynchroniseerd voordat ze zich kunnen aanmelden bij Azure AD.

## <a name="ad-fs-customization"></a>AD FS-aanpassing
De volgende secties bevatten informatie over een aantal algemene taken die u mogelijk moet uitvoeren wanneer u uw AD FS-aanmeldings pagina aanpast.

## <a name="add-a-custom-company-logo-or-illustration"></a><a name="customlogo"></a>Een aangepast bedrijfs logo of een aangepaste afbeelding toevoegen 
Als u het logo van het bedrijf wilt wijzigen dat wordt weer gegeven op de **aanmeldings** pagina, gebruikt u de volgende Windows Power shell-cmdlet en syntaxis.

> [!NOTE]
> De aanbevolen afmetingen voor het logo zijn 260 x 35 \@ 96 dpi met een bestands grootte die niet groter is dan 10 KB.

```azurepowershell-interactive
Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}
```

> [!NOTE]
> De *TargetName* -para meter is vereist. Het standaard thema dat wordt uitgebracht met AD FS is standaard genoemd.

## <a name="add-a-sign-in-description"></a><a name="addsignindescription"></a>Een beschrijving van een aanmelding toevoegen 
Als u een beschrijving van de aanmeldings pagina wilt toevoegen aan de **aanmeldings pagina**, gebruikt u de volgende Windows Power shell-cmdlet en syntaxis.

```azurepowershell-interactive
Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"
```

## <a name="modify-ad-fs-claim-rules"></a><a name="modclaims"></a>AD FS claim regels wijzigen 
AD FS ondersteunt een uitgebreide claim taal die u kunt gebruiken om aangepaste claim regels te maken. Zie [de rol van de taal van de claim regel](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dd807118(v=ws.11))voor meer informatie.

In de volgende secties wordt beschreven hoe u aangepaste regels kunt schrijven voor bepaalde scenario's die betrekking hebben op Azure AD en AD FS federatie.

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>Onveranderbare ID die afhankelijk is van een waarde die in het kenmerk aanwezig is
Met Azure AD Connect kunt u een kenmerk opgeven dat moet worden gebruikt als een bron anker wanneer objecten worden gesynchroniseerd met Azure AD. Als de waarde in het aangepaste kenmerk niet leeg is, wilt u mogelijk een onveranderlijke ID-claim uitgeven.

U kunt bijvoorbeeld **MS-DS-consistencyguid** als het kenmerk voor het bron anker selecteren en **ImmutableID** als **MS-DS-consistencyguid** geven als het kenmerk een waarde heeft. Als er geen waarde is voor het kenmerk, geeft u **objectGuid** als de onveranderbare id. U kunt de set aangepaste claim regels maken, zoals wordt beschreven in de volgende sectie.

**Regel 1: query kenmerken**

```claim-rule-language
c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
=> add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);
```

In deze regel zoekt u een query uit op de waarden **MS-DS-consistencyguid** en **objectGuid** voor de gebruiker van Active Directory. Wijzig de archief naam in een geschikte archief naam in uw AD FS-implementatie. Wijzig ook het claim type in een juist claim type voor uw Federatie, zoals gedefinieerd voor **objectGuid** en **MS-DS-consistencyguid**.

Daarnaast kunt u met behulp van **toevoegen** en niet bij niet- **probleem** voor komen dat u een uitgaand probleem voor de entiteit toevoegt en de waarden als tussenliggende waarden gebruiken. U geeft de claim op in een latere regel nadat u hebt vastgesteld welke waarde moet worden gebruikt als de onveranderlijke ID.

**Regel 2: controleren of MS-DS-consistencyguid bestaat voor de gebruiker**

```claim-rule-language
NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"])
=> add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");
```

Deze regel definieert een tijdelijke vlag met de naam **idflag** die is ingesteld op **useguid** als er geen **MS-DS-consistencyguid** voor de gebruiker is ingevuld. De logica achter dit is het feit dat AD FS geen lege claims toestaat. Dus wanneer u claims `http://contoso.com/ws/2016/02/identity/claims/objectguid` en `http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid` in regel 1 toevoegt, wordt er alleen een **msdsconsistencyguid** claim gemaakt als de waarde voor de gebruiker wordt ingevuld. Als deze niet is ingevuld, ziet AD FS dat deze een lege waarde heeft en deze onmiddellijk wegvalt. Alle objecten hebben **objectGuid**, zodat de claim altijd aanwezig is nadat regel 1 is uitgevoerd.

**Regel 3: MS-DS-consistencyguid als onveranderbare ID uitgeven als deze aanwezig is**

```claim-rule-language
c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"]
=> issue(Type = "http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID", Value = c.Value);
```

Dit is **een impliciete** controle. Als de waarde voor de claim bestaat, moet u deze als onveranderlijke ID uitgeven. In het vorige voor beeld wordt de claim **nameidentifier** gebruikt. U moet dit wijzigen in het juiste claim type voor de onveranderbare ID in uw omgeving.

**Regel 4: objectGuid als onveranderlijke ID uitgeven als MS-DS-consistencyGuid niet aanwezig is**

```claim-rule-language
c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
&& c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
=> issue(Type = "http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID", Value = c2.Value);
```

In deze regel controleert u gewoon de tijdelijke vlag **idflag**. U bepaalt of de claim moet worden uitgegeven op basis van de waarde.

> [!NOTE]
> De volg orde van deze regels is belang rijk.

### <a name="sso-with-a-subdomain-upn"></a>Eenmalige aanmelding met een subdomein-UPN

U kunt meer dan één domein aan federatieve toevoegen met behulp van Azure AD Connect, zoals wordt beschreven in [een nieuw federatief domein toevoegen](how-to-connect-fed-management.md#addfeddomain). Azure AD Connect versie 1.1.553.0 en de laatste maakt de juiste claim regel voor issuerID automatisch. Als u Azure AD Connect versie 1.1.553.0 of de nieuwste niet kunt gebruiken, is het raadzaam om de [Azure AD rap-claim regels](https://aka.ms/aadrptclaimrules) voor het genereren en instellen van de juiste claim regels voor de vertrouwens relatie van azure AD Relying Party.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [Opties voor gebruikers aanmelding](plan-connect-user-signin.md).