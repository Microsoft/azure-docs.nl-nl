---
title: Meerdere domeinen Azure AD Connect
description: Dit document bevat informatie over het instellen en configureren van meerdere domeinen op het hoogste niveau met Microsoft 365 en Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 5595fb2f-2131-4304-8a31-c52559128ea4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/31/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53a0da5b5db21c9a543d39d1b252b0b4c64e2a56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91306358"
---
# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Ondersteuning voor meerdere domeinen voor federatie met Azure AD
De volgende documentatie bevat richt lijnen voor het gebruik van meerdere domeinen op het hoogste niveau en subdomeinen wanneer federeren met Microsoft 365 of Azure AD-domeinen.

## <a name="multiple-top-level-domain-support"></a>Ondersteuning voor meerdere domeinen op het hoogste niveau
Federeren meerdere domeinen op het hoogste niveau met Azure AD vereisen enige aanvullende configuratie die niet vereist is wanneer federeren met één domein op het hoogste niveau.

Wanneer een domein Federated is met Azure AD, worden verschillende eigenschappen ingesteld voor het domein in Azure.  Een belang rijk item is IssuerUri.  Deze eigenschap is een URI die door Azure AD wordt gebruikt om het domein te identificeren waaraan het token is gekoppeld.  De URI hoeft niet naar iets te worden omgezet, maar moet een geldige URI zijn.  Standaard stelt Azure AD de URI in op de waarde van de Federation service-id in uw on-premises AD FS configuratie.

> [!NOTE]
> De Federation service-id is een URI waarmee een Federatie service op unieke wijze wordt geïdentificeerd.  De Federation-service is een instantie van AD FS die fungeert als de beveiligings token service.
>
>

U kunt de IssuerUri weer geven met behulp van de Power shell-opdracht `Get-MsolDomainFederationSettings -DomainName <your domain>` .

![Scherm opname van de resultaten nadat u de opdracht ' Get-MsolDomainFederationSettings ' in Power shell hebt ingevoerd.](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

Er treedt een probleem op wanneer u meer dan één domein op het hoogste niveau toevoegt.  Stel bijvoorbeeld dat u Federatie hebt ingesteld tussen Azure AD en uw on-premises omgeving.  Voor dit document wordt het domein bmcontoso.com gebruikt.  Er is nu een tweede domein op het hoogste niveau bmfabrikam.com toegevoegd.

![Een scherm opname met meerdere domeinen op het hoogste niveau](./media/how-to-connect-install-multiple-domains/domains.png)

Wanneer u probeert het domein bmfabrikam.com te converteren naar Federated, treedt er een fout op.  De reden hiervoor is dat Azure AD een beperking heeft die de eigenschap IssuerUri niet toestaat dezelfde waarde voor meer dan één domein te hebben.  

![Scherm opname waarin een Federatie fout in Power shell wordt weer gegeven.](./media/how-to-connect-install-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>SupportMultipleDomain-para meter
Als u deze beperking wilt omzeilen, moet u een andere IssuerUri toevoegen. Dit kan worden gedaan met behulp van de `-SupportMultipleDomain` para meter.  Deze para meter wordt gebruikt met de volgende cmdlets:

* `New-MsolFederatedDomain`
* `Convert-MsolDomaintoFederated`
* `Update-MsolFederatedDomain`

Met deze para meter wordt de IssuerUri door Azure AD geconfigureerd zodat deze is gebaseerd op de naam van het domein.  De IssuerUri is uniek voor alle directory's in azure AD.  Als u de para meter gebruikt, kan de Power shell-opdracht zijn voltooid.

![Scherm afbeelding met een geslaagde voltooiing van de Power shell-opdracht.](./media/how-to-connect-install-multiple-domains/convert.png)

Als u de instellingen voor het bmfabrikam.com-domein bekijkt, ziet u het volgende:

![Scherm afbeelding met de instellingen voor het domein ' bmfabrikam.com '.](./media/how-to-connect-install-multiple-domains/settings.png)

`-SupportMultipleDomain` wijzigt niet de andere eind punten die nog steeds zijn geconfigureerd om te verwijzen naar de Federation-service op adfs.bmcontoso.com.

Een ander `-SupportMultipleDomain` zou dat ervoor zorgt dat het AD FS systeem de juiste waarde voor de verlener bevat in de tokens die zijn uitgegeven voor Azure AD. Deze waarde wordt ingesteld door het domein gedeelte van de UPN van de gebruiker te nemen en in te stellen als het domein in de IssuerUri, d.w.z. https://{UPN-achtervoegsel}/ADFS/Services/Trust.

Tijdens de verificatie bij Azure AD of Microsoft 365, wordt het IssuerUri-element in het token van de gebruiker gebruikt om het domein te vinden in azure AD. Als er geen overeenkomst wordt gevonden, mislukt de verificatie.

Als de UPN van een gebruiker bijvoorbeeld is bsimon@bmcontoso.com , wordt het IssuerUri-element in het token, AD FS-problemen, ingesteld op `http://bmcontoso.com/adfs/services/trust` . Dit element komt overeen met de Azure AD-configuratie en de verificatie slaagt.

Hier volgt de aangepaste claim regel die deze logica implementeert:

```
c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));
```


> [!IMPORTANT]
> Als u de schakel optie-SupportMultipleDomain wilt gebruiken bij het toevoegen van nieuwe of geconverteerde al bestaande domeinen, moet uw federatieve vertrouwens relatie al zijn ingesteld om ze te ondersteunen.
>
>

## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>De vertrouwens relatie tussen AD FS en Azure AD bijwerken
Als u de federatieve vertrouwens relatie tussen AD FS en uw exemplaar van Azure AD niet hebt ingesteld, moet u deze vertrouwens relatie mogelijk opnieuw maken.  De reden hiervoor is, wanneer deze oorspronkelijk is ingesteld zonder de `-SupportMultipleDomain` para meter, wordt de IssuerUri ingesteld met de standaard waarde.  In de onderstaande scherm afbeelding ziet u dat de IssuerUri is ingesteld op `https://adfs.bmcontoso.com/adfs/services/trust` .

Als u een nieuw domein hebt toegevoegd in de Azure AD-Portal en vervolgens probeert te converteren met `Convert-MsolDomaintoFederated -DomainName <your domain>` , wordt de volgende fout weer geven.

![Scherm opname waarin een Federatie fout in Power shell wordt weer gegeven na een poging een nieuw domein te converteren met de opdracht ' Convert-MsolDomaintoFederated '.](./media/how-to-connect-install-multiple-domains/trust1.png)

Als u de switch probeert toe te voegen `-SupportMultipleDomain` , wordt de volgende fout weer gegeven:

![Scherm opname van een Federatie fout na het toevoegen van de schakel optie '-SupportMultipleDomain '.](./media/how-to-connect-install-multiple-domains/trust2.png)

Als u gewoon probeert uit te voeren `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` op het oorspronkelijke domein, resulteert dit in een fout.

![Federatie fout](./media/how-to-connect-install-multiple-domains/trust3.png)

Gebruik de volgende stappen om een extra domein op het hoogste niveau toe te voegen.  Als u al een domein hebt toegevoegd en de para meter niet hebt gebruikt `-SupportMultipleDomain` , begint u met de stappen voor het verwijderen en bijwerken van het oorspronkelijke domein.  Als u nog geen domein op het hoogste niveau hebt toegevoegd, kunt u beginnen met de stappen voor het toevoegen van een domein met Power shell van Azure AD Connect.

Gebruik de volgende stappen om de micro soft online-vertrouwens relatie te verwijderen en uw oorspronkelijke domein bij te werken.

1. Open **AD FS beheer** op uw AD FS federatieve server.
2. Vouw aan de linkerkant **vertrouwens relaties** en **vertrouwens relaties met relying party's** uit
3. Verwijder aan de rechter kant de vermelding van het **Microsoft Office 365-identiteits platform** .
   ![Micro soft online verwijderen](./media/how-to-connect-install-multiple-domains/trust4.png)
4. Voer de volgende handelingen uit op een computer waarop [Azure Active Directory-module voor Windows Power shell](/previous-versions/azure/jj151815(v=azure.100)) is geïnstalleerd: `$cred=Get-Credential` .  
5. Voer de gebruikers naam en het wacht woord in van een globale beheerder voor het Azure AD-domein waarmee u federeren.
6. Voer in Power shell `Connect-MsolService -Credential $cred`
7. Voer in Power shell in `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain` .  Deze update is voor het oorspronkelijke domein.  Als u de bovenstaande domeinen gebruikt, zou dit het volgende zijn:  `Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`

Gebruik de volgende stappen om het nieuwe domein op het hoogste niveau toe te voegen met behulp van Power shell

1. Voer de volgende handelingen uit op een computer waarop [Azure Active Directory-module voor Windows Power shell](/previous-versions/azure/jj151815(v=azure.100)) is geïnstalleerd: `$cred=Get-Credential` .  
2. Voer de gebruikers naam en het wacht woord in van een globale beheerder voor het Azure AD-domein waarmee u federeren hebt
3. Voer in Power shell `Connect-MsolService -Credential $cred`
4. Voer in Power shell `New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

Gebruik de volgende stappen om het nieuwe domein op het hoogste niveau toe te voegen met behulp van Azure AD Connect.

1. Azure AD Connect starten vanaf het bureau blad of in het menu Start
2. Kies de scherm afbeelding ' een extra Azure AD-domein toevoegen ' ![ waarin de pagina extra taken wordt weer gegeven met de optie een extra Azure AD-domein toevoegen is geselecteerd.](./media/how-to-connect-install-multiple-domains/add1.png)
3. Voer uw Azure AD-en Active Directory-referenties in
4. Selecteer het tweede domein dat u wilt configureren voor Federatie.
   ![Een extra Azure AD-domein toevoegen](./media/how-to-connect-install-multiple-domains/add2.png)
5. Klik op Installeren

### <a name="verify-the-new-top-level-domain"></a>Het nieuwe domein op het hoogste niveau controleren
Met behulp van de Power shell-opdracht `Get-MsolDomainFederationSettings -DomainName <your domain>` kunt u de bijgewerkte IssuerUri bekijken.  In de onderstaande scherm afbeelding ziet u dat de Federatie-instellingen zijn bijgewerkt op het oorspronkelijke domein `http://bmcontoso.com/adfs/services/trust`

![Scherm opname van de Federatie-instellingen die in het oorspronkelijke domein zijn bijgewerkt.](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

En de IssuerUri voor het nieuwe domein is ingesteld op `https://bmfabrikam.com/adfs/services/trust`

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/settings2.png)

## <a name="support-for-subdomains"></a>Ondersteuning voor subdomeinen
Wanneer u een subdomein toevoegt vanwege de manier waarop Azure AD domeinen heeft verwerkt, neemt het de instellingen van het bovenliggende knoop punt over.  De IssuerUri moet dus overeenkomen met de bovenliggende items.

Zo kunt u bijvoorbeeld een bmcontoso.com hebben en vervolgens corp.bmcontoso.com toevoegen.  De IssuerUri voor een gebruiker uit corp.bmcontoso.com moet zijn **`http://bmcontoso.com/adfs/services/trust`** .  De standaard regel die hierboven is geïmplementeerd voor Azure AD, genereert echter een token met een certificaat verlener als **`http://corp.bmcontoso.com/adfs/services/trust`** . Dit komt niet overeen met de vereiste waarde van het domein en de verificatie mislukt.

### <a name="how-to-enable-support-for-subdomains"></a>Ondersteuning voor subdomeinen inschakelen
Om dit gedrag te omzeilen, moet de AD FS Relying Party Trust voor micro soft online worden bijgewerkt.  Hiervoor moet u een aangepaste claim regel configureren zodat deze alle subdomeinen uit het UPN-achtervoegsel van de gebruiker verwijdert bij het samen stellen van de waarde van de aangepaste verlener.

Deze claim gaat als volgt:

```    
c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));
```

[!NOTE]
Het laatste nummer in de reguliere expressie set is het aantal bovenliggende domeinen in het hoofd domein. Hier wordt bmcontoso.com gebruikt, zodat er twee bovenliggende domeinen nodig zijn. Als er drie bovenliggende domeinen moeten worden bewaard (bijvoorbeeld: corp.bmcontoso.com), is het aantal drie. Uiteindelijk kan een bereik worden aangegeven, de overeenkomst wordt altijd gemaakt met het maximum aantal domeinen. " {2,3} " komt overeen met twee tot drie domeinen (dat wil zeggen: bmfabrikam.com en Corp.bmcontoso.com).

Gebruik de volgende stappen om een aangepaste claim toe te voegen ter ondersteuning van subdomeinen.

1. AD FS beheer openen
2. Klik met de rechter muisknop op de micro soft online RP-vertrouwens relatie en kies claim regels bewerken
3. Selecteer de derde claim regel en vervang ![ claim bewerken](./media/how-to-connect-install-multiple-domains/sub1.png)
4. De huidige claim vervangen:

   ```
   c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));
   ```
    wordt uitgevoerd met

   ```
   c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));
   ```

    ![Claim vervangen](./media/how-to-connect-install-multiple-domains/sub2.png)

5. Klik op OK.  Klik op Toepassen.  Klik op OK.  Sluit AD FS-beheer.

## <a name="next-steps"></a>Volgende stappen
Nu u Azure AD Connect geïnstalleerd hebt, kunt u [de installatie verifiëren en licenties toewijzen](how-to-connect-post-installation.md).

Lees meer over deze functies, die tijdens de installatie zijn ingeschakeld: [Automatische upgrade](how-to-connect-install-automatic-upgrade.md), [Onopzettelijk verwijderen voorkomen](how-to-connect-sync-feature-prevent-accidental-deletes.md) en [Azure AD Connect Health](how-to-connect-health-sync.md).

Lees meer over deze veelvoorkomende onderwerpen: [Scheduler en het activeren van de synchronisatie](how-to-connect-sync-feature-scheduler.md).

Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).