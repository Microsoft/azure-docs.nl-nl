---
title: Populatie van Azure AD UserPrincipalName
description: In het volgende document wordt beschreven hoe het kenmerk UserPrincipalName wordt ingevuld.
author: billmath
ms.subservice: hybrid
ms.author: billmath
ms.date: 06/26/2018
ms.topic: conceptual
ms.workload: identity
ms.service: active-directory
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58b4bbcac110398ee4ff132b76ce8c4868ee17f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91317588"
---
# <a name="azure-ad-userprincipalname-population"></a>Populatie van Azure AD UserPrincipalName

In dit artikel wordt beschreven hoe het kenmerk UserPrincipalName wordt ingevuld in Azure Active Directory (Azure AD).
De waarde van het kenmerk UserPrincipalName is de Azure AD-gebruikers naam voor de gebruikers accounts.

## <a name="upn-terminology"></a>UPN-terminologie
In dit artikel wordt de volgende terminologie gebruikt:

|Termijn|Beschrijving|
|-----|-----|
|Eerste domein|Het standaard domein (onmicrosoft.com) in de Azure AD-Tenant. Bijvoorbeeld contoso.onmicrosoft.com.|
|Micro soft online e-mail routering adres (MOERA)|Azure AD berekent de MOERA van het Azure AD mailnickname-kenmerk en het oorspronkelijke Azure AD-domein als &lt; mailNickname &gt;&#64;&lt; eerste domein &gt; .|
|On-premises mailnickname-kenmerk|Een kenmerk in Active Directory, waarvan de waarde de alias vertegenwoordigt van een gebruiker in een Exchange-organisatie.|
|Kenmerk on-premises mail|Een kenmerk in Active Directory, waarvan de waarde het e-mail adres van een gebruiker vertegenwoordigt|
|Primair SMTP-adres|Het primaire e-mail adres van een object voor een Exchange-geadresseerde. Bijvoorbeeld SMTP: gebruiker \@ contoso.com.|
|Alternatieve aanmeldings-ID|Een on-premises kenmerk, met uitzonde ring van UserPrincipalName, zoals e-mail kenmerk, dat wordt gebruikt voor aanmelding.|

## <a name="what-is-userprincipalname"></a>Wat is UserPrincipalName?
UserPrincipalName is een kenmerk dat een aanmeldings naam op internet is voor een gebruiker op basis van Internet Standard [RFC 822](https://www.ietf.org/rfc/rfc0822.txt). 

### <a name="upn-format"></a>UPN-indeling
Een UPN bestaat uit een UPN-voor voegsel (de naam van het gebruikers account) en een UPN-achtervoegsel (een DNS-domein naam). Het voor voegsel wordt toegevoegd aan het achtervoegsel met het \@ symbool "". Bijvoorbeeld ' iemand \@ example.com '. Een UPN moet uniek zijn voor alle SPN-objecten in een Directory-forest. 

## <a name="upn-in-azure-ad"></a>UPN in azure AD 
De UPN wordt door Azure AD gebruikt om gebruikers toe te staan zich aan te melden.  De UPN die een gebruiker kan gebruiken, is afhankelijk van het feit of het domein is geverifieerd.  Als het domein is geverifieerd, kan een gebruiker met dat achtervoegsel zich aanmelden bij Azure AD.  

Het kenmerk wordt gesynchroniseerd door Azure AD Connect.  Tijdens de installatie kunt u de geverifieerde domeinen en de niet-bewaarde weer geven.

   ![Niet-geverifieerde domeinen](./media/plan-connect-userprincipalname/unverifieddomain.png) 

## <a name="alternate-login-id"></a>Alternatieve aanmeldings-ID
In sommige omgevingen is het mogelijk dat eind gebruikers alleen op de hoogte zijn van hun e-mail adres en niet met hun UPN.  Het gebruik van een e-mail adres kan worden veroorzaakt door een bedrijfs beleid of een on-premises line-of-Business-toepassings afhankelijkheid.

Met de alternatieve aanmeldings-ID kunt u een aanmeldings ervaring configureren waarbij gebruikers zich kunnen aanmelden met een ander kenmerk dan hun UPN, zoals e-mail.

Als u een alternatieve aanmeldings-ID wilt inschakelen met Azure AD, zijn er geen aanvullende configuraties nodig om Azure AD Connect te gebruiken. Alternatieve ID kan rechtstreeks vanuit de wizard worden geconfigureerd. Zie configuratie van Azure AD-aanmelding voor uw gebruikers in de sectie synchronisatie. Selecteer onder de vervolg keuzelijst **User Principal name** het kenmerk voor de alternatieve aanmeldings-id.

![Scherm afbeelding van de lijst met gebruikers-principal-namen waar u het kenmerk alternatieve aanmeldings-ID selecteert.](./media/plan-connect-userprincipalname/altloginid.png)  

Zie [Configure alternatieve aanmeldings-id](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) en [configuratie van Azure AD-aanmelding](how-to-connect-install-custom.md#azure-ad-sign-in-configuration) voor meer informatie.

## <a name="non-verified-upn-suffix"></a>Niet-geverifieerd UPN-achtervoegsel
Als het achtervoegsel voor on-premises UserPrincipalName kenmerk/alternatieve aanmeldings-ID niet is geverifieerd met de Azure AD-Tenant, wordt de waarde van het kenmerk Azure AD UserPrincipalName ingesteld op MOERA. Azure AD berekent de MOERA van het Azure AD mailnickname-kenmerk en het oorspronkelijke Azure AD-domein als &lt; mailNickname &gt;&#64;&lt; eerste &gt; domein.

## <a name="verified-upn-suffix"></a>Geverifieerde UPN-achtervoegsel
Als het achtervoegsel voor on-premises UserPrincipalName kenmerk/alternatieve aanmeldings-ID wordt gecontroleerd met de Azure AD-Tenant, wordt de waarde van het Azure AD UserPrincipalName-kenmerk gelijk aan de waarde on-premises UserPrincipalName kenmerk/alternatieve aanmeldings-ID.

## <a name="azure-ad-mailnickname-attribute-value-calculation"></a>Berekening van kenmerk waarde voor Azure AD mailNickname
Omdat de Azure AD UserPrincipalName-kenmerk waarde kan worden ingesteld op MOERA, is het belang rijk om te begrijpen hoe de naam van het Azure AD mailnickname-kenmerk, het voor voegsel MOERA, wordt berekend.

Wanneer een gebruikers object voor de eerste keer wordt gesynchroniseerd met een Azure AD-Tenant, controleert Azure AD de volgende items in de opgegeven volg orde en wordt de kenmerk waarde van mailnickname ingesteld op de eerste bestaande:

- On-premises mailnickname-kenmerk
- Voor voegsel van primair SMTP-adres
- Voor voegsel van on-premises e-mail kenmerk
- Voor voegsel van on-premises userPrincipalName-kenmerk/alternatieve aanmeldings-ID
- Voor voegsel van secundair SMTP-adres

Wanneer de updates voor een gebruikers object worden gesynchroniseerd met de Azure AD-Tenant, wordt de waarde van het kenmerk mailnickname alleen door Azure AD bijgewerkt als er een update voor de kenmerk waarde on-premises mailnickname is.

>[!IMPORTANT]
>Azure AD herberekent de waarde van het kenmerk UserPrincipalName alleen in het geval een update van de on-premises UserPrincipalName kenmerk/alternatieve aanmeldings-ID-waarde wordt gesynchroniseerd met de Azure AD-Tenant. 
>
>Wanneer Azure AD het kenmerk UserPrincipalName opnieuw berekent, berekent dit ook de MOERA opnieuw. 

## <a name="upn-scenarios"></a>UPN-scenario's
Hieronder ziet u een voor beeld van hoe de UPN wordt berekend op basis van het gegeven scenario.

### <a name="scenario-1-non-verified-upn-suffix--initial-synchronization"></a>Scenario 1: niet-geverifieerd UPN-achtervoegsel – initiële synchronisatie

![Scenario1](./media/plan-connect-userprincipalname/example1.png)

On-premises gebruikers object:
- mailnickname: &lt; niet ingesteld&gt;
- proxyAddresses: { SMTP:us1@contoso.com }
- verzenden us2@contoso.com
- userPrincipalName us3@contoso.com

Het gebruikers object voor de eerste keer is gesynchroniseerd met Azure AD-Tenant
- Stel het kenmerk Azure AD mailnickname in op het primaire SMTP-adres voorvoegsel.
- Stel MOERA in op  &lt; mailNickname &gt;&#64;&lt; eerste domein &gt; .
- Stel het kenmerk Azure AD UserPrincipalName in op MOERA.

Gebruikers object van de Azure AD-Tenant:
- Mailnickname: us1           
- UserPrincipalName us1@contoso.onmicrosoft.com


### <a name="scenario-2-non-verified-upn-suffix--set-on-premises-mailnickname-attribute"></a>Scenario 2: niet-geverifieerde UPN-achtervoegsel – set on-premises mailnickname kenmerk

![Scenario2](./media/plan-connect-userprincipalname/example2.png)

On-premises gebruikers object:
- mailnickname: US4
- proxyAddresses: { SMTP:us1@contoso.com }
- verzenden us2@contoso.com
- userPrincipalName us3@contoso.com

Update op een on-premises mailnickname-kenmerk synchroniseren met Azure AD-Tenant
- Werk het kenmerk van de Azure AD-mailnickname bij met een on-premises mailnickname-kenmerk.
- Omdat er geen update is naar het kenmerk on-premises userPrincipalName, is er geen wijziging in het kenmerk Azure AD UserPrincipalName.

Gebruikers object van de Azure AD-Tenant:
- Mailnickname: US4
- UserPrincipalName us1@contoso.onmicrosoft.com

### <a name="scenario-3-non-verified-upn-suffix--update-on-premises-userprincipalname-attribute"></a>Scenario 3: niet-geverifieerd UPN-achtervoegsel – on-premises userPrincipalName kenmerk bijwerken

![Scenario3](./media/plan-connect-userprincipalname/example3.png)

On-premises gebruikers object:
- mailnickname: US4
- proxyAddresses: { SMTP:us1@contoso.com }
- verzenden us2@contoso.com
- userPrincipalName us5@contoso.com

Update op een on-premises userPrincipalName-kenmerk synchroniseren met de Azure AD-Tenant
- Update on-premises userPrincipalName kenmerk activeert herberekening van het kenmerk MOERA en Azure AD UserPrincipalName.
- Stel MOERA in op &lt; mailNickname &gt;&#64;&lt; eerste domein &gt; .
- Stel het kenmerk Azure AD UserPrincipalName in op MOERA.

Gebruikers object van de Azure AD-Tenant:
- Mailnickname: US4
- UserPrincipalName us4@contoso.onmicrosoft.com

### <a name="scenario-4-non-verified-upn-suffix--update-primary-smtp-address-and-on-premises-mail-attribute"></a>Scenario 4: niet-geverifieerd UPN-achtervoegsel – primair SMTP-adres en on-premises e-mail kenmerk bijwerken

![Scenario4](./media/plan-connect-userprincipalname/example4.png)

On-premises gebruikers object:
- mailnickname: US4
- proxyAddresses: { SMTP:us6@contoso.com }
- verzenden us7@contoso.com
- userPrincipalName us5@contoso.com

Update voor on-premises e-mail kenmerk en primair SMTP-adres synchroniseren met Azure AD-Tenant
- Na de initiële synchronisatie van het gebruikers object worden updates voor het kenmerk on-premises mail en het primaire SMTP-adres niet beïnvloed door de Azure AD mailnickname of het kenmerk UserPrincipalName.

Gebruikers object van de Azure AD-Tenant:
- Mailnickname: US4
- UserPrincipalName us4@contoso.onmicrosoft.com

### <a name="scenario-5-verified-upn-suffix--update-on-premises-userprincipalname-attribute-suffix"></a>Scenario 5: UPN-achtervoegsel geverifieerd: on-premises userPrincipalName kenmerk achtervoegsel bijwerken

![Scenario5](./media/plan-connect-userprincipalname/example5.png)

On-premises gebruikers object:
- mailnickname: US4
- proxyAddresses: { SMTP:us6@contoso.com }
- verzenden us7@contoso.com
- userPrincipalName us5@verified.contoso.com

Update op een on-premises userPrincipalName-kenmerk synchroniseren met de Azure AD-Tenant
- Bijwerken bij on-premises userPrincipalName kenmerk activeert herberekening van het kenmerk Azure AD UserPrincipalName.
- Stel het kenmerk Azure AD UserPrincipalName in op on-premises userPrincipalName kenmerk als het UPN-achtervoegsel wordt geverifieerd met de Azure AD-Tenant.

Gebruikers object van de Azure AD-Tenant:
- Mailnickname: US4     
- UserPrincipalName us5@verified.contoso.com

## <a name="next-steps"></a>Volgende stappen
- [Uw on-premises directory's integreren met Azure Active Directory](whatis-hybrid-identity.md)
- [Aangepaste installatie van Azure AD Connect](how-to-connect-install-custom.md)