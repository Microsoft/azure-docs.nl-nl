---
title: Azure AD Connect-AD FS vertrouwen met Azure AD beheren met behulp van Azure AD Connect | Microsoft Docs
description: Operationele details van de verwerking van Azure AD-vertrouwens relaties door Azure AD Connect.
keywords: AD FS, ADFS, AD FS beheer, AAD Connect, Connect, Azure AD, Trust, AAD, claim, claim, claim regels, uitgifte, trans formatie, regels, back-up, herstel
services: active-directory
documentationcenter: ''
ms.reviewer: anandyadavmsft
manager: daveba
ms.subservice: hybrid
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/28/2018
ms.author: billmath
author: billmath
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13d56ec321cd257412c2b0abbe0be655c6cb4dbf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85360092"
---
# <a name="manage-ad-fs-trust-with-azure-ad-using-azure-ad-connect"></a>AD FS-vertrouwensrelatie met Azure AD beheren met behulp van Azure AD Connect

## <a name="overview"></a>Overzicht

Azure AD Connect kunt Federatie beheren tussen on-premises Active Directory Federation Service (AD FS) en Azure AD. Dit artikel bevat een overzicht van:

* De verschillende instellingen die zijn geconfigureerd op de vertrouwens relatie door Azure AD Connect
* De regels voor uitgifte transformatie (claim regels) die zijn ingesteld door Azure AD Connect
* Het maken van back-ups en herstellen van uw claim regels tussen upgrades en configuratie-updates. 

## <a name="settings-controlled-by-azure-ad-connect"></a>Instellingen die worden beheerd door Azure AD Connect

Azure AD Connect beheert **alleen** instellingen met betrekking tot de vertrouwens relatie van Azure AD. Azure AD Connect wijzigt geen instellingen op andere Relying Party vertrouwens relaties in AD FS. De volgende tabel geeft de instellingen aan die worden beheerd door Azure AD Connect.

| Instelling | Beschrijving |
| :--- | :--- |
| Certificaat voor token-ondertekening | Azure AD Connect kunnen worden gebruikt om de vertrouwens relatie met Azure AD opnieuw in te stellen en opnieuw te maken. Azure AD Connect voert een eenmalige onmiddellijke rollover van certificaten voor token-ondertekening voor AD FS en werkt de Federatie-instellingen van het Azure AD-domein bij.|
| Algoritme voor token ondertekening | Micro soft raadt aan om SHA-256 te gebruiken als het token handtekening algoritme. Azure AD Connect kunt detecteren of het token handtekening algoritme is ingesteld op een waarde die minder veilig is dan SHA-256. De instelling wordt bij de volgende mogelijke configuratie bewerking bijgewerkt naar SHA-256. Andere Relying Party-vertrouwens relatie moet worden bijgewerkt om het nieuwe certificaat voor token-ondertekening te gebruiken. |
| Azure AD-vertrouwens-id | Azure AD Connect stelt de juiste id-waarde voor de Azure AD-vertrouwens relatie in. AD FS unieke identificatie van de Azure AD-vertrouwens relatie met de id-waarde. |
| Azure AD-eind punten | Azure AD Connect zorgt ervoor dat de eind punten die zijn geconfigureerd voor de Azure AD-vertrouwens relatie altijd volgens de meest recente aanbevolen waarden voor tolerantie en prestaties. |
| Transformatie regels voor uitgifte | Er zijn een aantal claim regels die nodig zijn voor optimale prestaties van de functies van Azure AD in een federatieve instelling. Azure AD Connect zorgt ervoor dat de vertrouwens relatie van Azure AD altijd is geconfigureerd met de juiste set aanbevolen claim regels. |
| Alternatief-id | Als synchronisatie is geconfigureerd voor het gebruik van alternatieve-id, Azure AD Connect configureert AD FS om verificatie met behulp van alternatieve-id uit te voeren. |
| Automatische meta gegevens bijwerken | Trust with Azure AD is geconfigureerd voor automatische updates van meta gegevens. AD FS controleert regel matig de meta gegevens van de Azure AD-vertrouwens relatie en houdt deze up-to-date als de wijzigingen aan de Azure AD-kant worden aangebracht. |
| Geïntegreerde Windows-verificatie | Tijdens de hybride Azure AD-deelname bewerking is IWA ingeschakeld voor apparaatregistratie om hybride Azure AD-deelname te vergemakkelijken voor down level-apparaten |

## <a name="execution-flows-and-federation-settings-configured-by-azure-ad-connect"></a>Uitvoerings stromen en Federatie-instellingen die zijn geconfigureerd door Azure AD Connect

Bij Azure AD Connect worden niet alle instellingen voor Azure AD-vertrouwens relatie bijgewerkt tijdens configuratie stromen. De instellingen die zijn gewijzigd, zijn afhankelijk van welke taak of uitvoerings stroom wordt uitgevoerd. De volgende tabel bevat de instellingen die van invloed zijn op de verschillende uitvoerings stromen.

| Uitvoerings stroom | Beïnvloede instellingen |
| :--- | :--- |
| Eerste geslaagde installatie (Express) | Geen |
| Eerste fase van de installatie (nieuwe AD FS Farm) | Er wordt een nieuwe AD FS-farm gemaakt en een vertrouwens relatie met Azure AD is volledig gemaakt. |
| Eerste geslaagde installatie (bestaande AD FS Farm, bestaande Azure AD-vertrouwens relatie) | Azure AD-vertrouwens-id, uitgifte transformatie regels, Azure AD-eind punten, alternatieve-id (indien nodig), automatische update van meta gegevens |
| Azure AD-vertrouwens relatie opnieuw instellen | Certificaat voor token-ondertekening, algoritme voor token-ondertekening, Azure AD-vertrouwens-id, uitgifte transformatie regels, Azure AD-eind punten, alternatieve-id (indien nodig), automatische update van meta gegevens |
| Federatie server toevoegen | Geen |
| WAP-server toevoegen | Geen |
| Apparaatopties | Regels voor uitgifte transformatie, IWA voor apparaatregistratie |
| Federatief domein toevoegen | Als het domein voor het eerst wordt toegevoegd, dat wil zeggen, wordt de installatie van één domein Federatie gewijzigd in een Federatie met meerdere domeinen. Azure AD Connect wordt de vertrouwens relatie opnieuw gemaakt. Als de vertrouwens relatie met Azure AD al is geconfigureerd voor meerdere domeinen, worden alleen transformatie regels voor uitgifte gewijzigd |
| TLS bijwerken | Geen |

Bij alle bewerkingen, waarbij elke instelling wordt gewijzigd, maakt Azure AD Connect een back-up van de huidige vertrouwens instellingen op **%ProgramData%\AADConnect\ADFS**

![Azure AD Connect pagina met bericht over bestaande Azure AD-vertrouwens relatie](./media/how-to-connect-azure-ad-trust/backup2.png)

> [!NOTE]
> Vóór versie 1.1.873.0 is de back-up alleen uit regels voor uitgifte transformatie en zijn er back-ups gemaakt in het tracerings logboek bestand van de wizard.

## <a name="issuance-transform-rules-set-by-azure-ad-connect"></a>Regels voor uitgifte transformatie die zijn ingesteld door Azure AD Connect

Azure AD Connect zorgt ervoor dat de vertrouwens relatie van Azure AD altijd is geconfigureerd met de juiste set aanbevolen claim regels. Micro soft raadt u aan Azure AD Connect te gebruiken voor het beheren van uw Azure AD-vertrouwens relatie. In deze sectie vindt u de set met regels voor uitgifte transformatie en de bijbehorende beschrijvingen.

| Regelnaam | Beschrijving |
| --- | --- |
| UPN uitgeven | Deze regel voert een query uit op de waarde van userPrincipalName vanaf het kenmerk dat is geconfigureerd in synchronisatie-instellingen voor userPrincipalName.|
| Query ObjectGUID en msdsconsistencyguid voor aangepaste ImmutableId-claim | Deze regel voegt een tijdelijke waarde toe aan de pijp lijn voor de waarde ObjectGUID en msdsconsistencyguid als deze bestaat. |
| Controleren op de aanwezigheid van msdsconsistencyguid | Op basis van het feit of de waarde voor msdsconsistencyguid bestaat, stellen we een tijdelijke vlag in om te bepalen wat moet worden gebruikt als ImmutableId |
| Msdsconsistencyguid uitgeven als onveranderbare ID als deze bestaat | Msdsconsistencyguid als ImmutableId geven als de waarde bestaat |
| ObjectGuidRule uitgeven als de msdsConsistencyGuid-regel niet bestaat | Als de waarde voor msdsconsistencyguid niet bestaat, wordt de waarde van ObjectGUID uitgegeven als ImmutableId |
| Nameidentifier uitgeven | Deze regel geeft een waarde voor de claim nameidentifier.|
| Account type uitgeven voor computers die lid zijn van een domein | Als de entiteit die wordt geverifieerd een apparaat is dat is gekoppeld aan een domein, wordt met deze regel het account type uitgegeven als DJ dat een aan een domein gekoppeld apparaat aanduidt |
| Account type uitgeven met de waarde gebruiker wanneer dit geen computer account is | Als de entiteit die wordt geverifieerd een gebruiker is, wordt met deze regel het account type als gebruiker uitgegeven |
| IssuerID uitgeven wanneer het geen computer account is | Met deze regel wordt de issuerId-waarde uitgegeven wanneer de verificatie-entiteit geen apparaat is. De waarde wordt gemaakt via een reguliere expressie, die wordt geconfigureerd door Azure AD Connect. De regex wordt gemaakt na het nemen van alle domeinen die worden gebruikt Azure AD Connect. |
| IssuerID uitgeven voor DJ computer auth | Met deze regel wordt de issuerId-waarde uitgegeven wanneer de verificatie-entiteit een apparaat is |
| Onpremobjectguid uitgeven voor computers die lid zijn van een domein | Als de entiteit die wordt geverifieerd een aan het domein gekoppelde apparaat is, wordt met deze regel de lokale ObjectGUID voor het apparaat uitgegeven |
| Primaire SID door geven | Met deze regel wordt de primaire SID van de verificatie-entiteit uitgegeven |
| Door geven via claim-insideCorporateNetwork | Deze regel geeft een claim waarmee Azure AD weet of de verificatie afkomstig is van binnen het bedrijfs netwerk of extern |
| Pass Through-claim – Psso |   |
| Claims voor verlopen van wacht woorden uitgeven | Met deze regel worden drie claims voor wachtwoord verloop tijd, het aantal dagen voor het verlopen van het wacht woord en de URL waarnaar wordt geauthenticeerd voor het wijzigen van het wacht woord uitgegeven.|
| Pass Through-claim – authnmethodsreferences | De waarde in de claim die is uitgegeven onder deze regel geeft aan welk type verificatie is uitgevoerd voor de entiteit |
| Door geven via claim-multifactorauthenticationinstant | De waarde van deze claim specificeert de tijd, in UTC, wanneer de gebruiker voor het laatst meerdere Factor Authentication heeft uitgevoerd. |
| Door geven via claim-AlternateLoginID | Met deze regel wordt de AlternateLoginID-claim uitgegeven als de verificatie is uitgevoerd met een alternatieve aanmeldings-ID. |

> [!NOTE]
> De claim regels voor de uitgifte-UPN en ImmutableId verschillen als u een niet-standaard keuze gebruikt tijdens Azure AD Connect configuratie

## <a name="restore-issuance-transform-rules"></a>Regels voor uitgifte transformatie herstellen

Azure AD Connect versie 1.1.873.0 of hoger maakt een back-up van de vertrouwens instellingen van Azure AD wanneer een update wordt uitgevoerd naar de vertrouwens instellingen van Azure AD. Er wordt een back-up van de Azure AD-vertrouwens instellingen gemaakt op **%ProgramData%\AADConnect\ADFS**. De bestands naam heeft de volgende indeling AadTrust: &lt; Date &gt; - &lt; time &gt; . txt, bijvoorbeeld-AadTrust-20180710-150216.txt

![Een scherm afbeelding van een voor beeld van een back-up van Azure AD-vertrouwens relatie](./media/how-to-connect-azure-ad-trust/backup.png)

U kunt de transformatie regels voor uitgifte herstellen met behulp van de onderstaande voorgestelde stappen

1. Open de gebruikers interface van AD FS beheer in Serverbeheer
2. Open de eigenschappen van de Azure AD-vertrouwens relatie door te gaan **AD FS &gt; Relying Party-vertrouwens relaties &gt; Microsoft Office 365-identiteits platform &gt; claim uitgifte beleid bewerken**
3. Klik op **regel toevoegen**
4. Selecteer claims verzenden met een aangepaste regel in de claim regel sjabloon en klik op **volgende**
5. Kopieer de naam van de claim regel uit het back-upbestand en plak deze in de naam van het veld **claim regel**
6. Kopieer de claim regel van het back-upbestand naar het tekst veld voor **aangepaste regel** en klik op **volt ooien**

> [!NOTE]
> Zorg ervoor dat de extra regels niet conflicteren met de regels die zijn geconfigureerd door Azure AD Connect.

## <a name="next-steps"></a>Volgende stappen
* [Active Directory Federation Services beheren en aanpassen met behulp van Azure AD Connect](how-to-connect-fed-management.md)
