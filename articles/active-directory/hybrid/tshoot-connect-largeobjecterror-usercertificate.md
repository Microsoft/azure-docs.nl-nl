---
title: Azure AD Connect-LargeObject-fouten veroorzaakt door het kenmerk userCertificate | Microsoft Docs
description: Dit onderwerp bevat de herstels tappen voor LargeObject-fouten die worden veroorzaakt door het kenmerk userCertificate.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 146ad5b3-74d9-4a83-b9e8-0973a19828d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: d33b419e0f24201d661ad0f5f1373022ea6e9e9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861745"
---
# <a name="azure-ad-connect-sync-handling-largeobject-errors-caused-by-usercertificate-attribute"></a>Azure AD Connect Sync: afhandelings LargeObject-fouten veroorzaakt door userCertificate-kenmerk

Azure AD dwingt een maximum limiet van **15** certificaat waarden af voor het kenmerk **userCertificate** . Als Azure AD Connect een object met meer dan 15 waarden exporteert naar Azure AD, retourneert Azure AD een **LargeObject** -fout met het bericht:

>*' Het ingerichte object is te groot. Snijd het aantal kenmerk waarden voor dit object bij. De bewerking wordt opnieuw uitgevoerd tijdens de volgende synchronisatie cyclus... "*

De LargeObject-fout kan worden veroorzaakt door andere AD-kenmerken. Om te bevestigen dat dit inderdaad wordt veroorzaakt door het kenmerk userCertificate, moet u controleren of het object zich in een on-premises AD bevindt of in de [Synchronization Service Manager omgekeerde zoek opdracht](./how-to-connect-sync-service-manager-ui-mvsearch.md).

Gebruik een van de volgende methoden om de lijst met objecten in uw Tenant te verkrijgen met LargeObject-fouten:

 * Als uw Tenant is ingeschakeld voor Azure AD Connect Health synchronisatie, kunt u het [rapport synchronisatie fout](./how-to-connect-health-sync.md) raadplegen.
 
 * De e-mail melding voor Directory synchronisatie fouten die aan het einde van elke synchronisatie cyclus wordt verzonden, bevat de lijst met objecten met LargeObject-fouten. 
 * Op het [tabblad Synchronization Service Manager bewerkingen](./how-to-connect-sync-service-manager-ui-operations.md) wordt de lijst met objecten met LargeObject-fouten weer gegeven als u op de laatste export naar Azure AD-bewerking klikt.
 
## <a name="mitigation-options"></a>Opties voor risico beperking
Totdat de LargeObject-fout is opgelost, kunnen andere kenmerk wijzigingen naar hetzelfde object niet worden geëxporteerd naar Azure AD. U kunt de volgende opties overwegen om de fout op te lossen:

 * Upgrade Azure AD Connect om 1.1.524.0 of After te bouwen. In Azure AD Connect build 1.1.524.0 zijn de out-of-Box-synchronisatie regels bijgewerkt om geen kenmerken userCertificate en userSMIMECertificate te exporteren als de kenmerken meer dan 15 waarden hebben. Raadpleeg voor meer informatie over het upgraden van Azure AD Connect artikel [Azure AD Connect: een upgrade van een eerdere versie naar de laatste](./how-to-upgrade-previous-version.md).

 * Implementeer een **uitgaande synchronisatie regel** in azure AD Connect die een **Null-waarde exporteert in plaats van de werkelijke waarden voor objecten met meer dan 15 certificaat waarden**. Deze optie is geschikt als u geen van de certificaat waarden nodig hebt om te exporteren naar Azure AD voor objecten met meer dan 15 waarden. Voor meer informatie over het implementeren van deze synchronisatie regel raadpleegt u de volgende sectie [implementeren van de synchronisatie regel voor het beperken van het exporteren van het kenmerk userCertificate](#implementing-sync-rule-to-limit-export-of-usercertificate-attribute).

 * Verminder het aantal certificaat waarden voor het on-premises AD-object (15 of minder) door waarden te verwijderen die niet meer in gebruik zijn door uw organisatie. Dit is geschikt als de kenmerken aanzienlijk worden veroorzaakt door verlopen of ongebruikte certificaten. U kunt het [Power shell-script dat hier beschikbaar is](https://gallery.technet.microsoft.com/Remove-Expired-Certificates-0517e34f) , gebruiken om verlopen certificaten in uw on-PREMISes ad te vinden, te maken en te verwijderen. Voordat u de certificaten verwijdert, is het raadzaam om te controleren of de Administrators van de open bare-sleutel infrastructuur in uw organisatie.

 * Configureer Azure AD Connect om het kenmerk userCertificate uit te sluiten van export naar Azure AD. Over het algemeen wordt deze optie niet aanbevolen omdat het kenmerk door micro soft Online Services kan worden gebruikt om specifieke scenario's mogelijk te maken. Met name:
    * Het kenmerk userCertificate voor het gebruikers object wordt gebruikt door Exchange Online-en Outlook-clients voor het ondertekenen van berichten en versleuteling. Raadpleeg artikel [S/MIME voor bericht ondertekening en versleuteling voor](/microsoft-365/security/office-365-security/s-mime-for-message-signing-and-encryption)meer informatie over deze functie.

    * Het kenmerk userCertificate van het computer object wordt door Azure AD gebruikt om Windows 10 on-premises domein apparaten toe te staan om verbinding te maken met Azure AD. Voor meer informatie over deze functie raadpleegt u het artikel [verbinding maken tussen domein apparaten en Azure AD voor Windows 10-ervaringen](../devices/hybrid-azuread-join-plan.md).

## <a name="implementing-sync-rule-to-limit-export-of-usercertificate-attribute"></a>De implementatie regel voor het beperken van het exporteren van het kenmerk userCertificate wordt geïmplementeerd
Als u de LargeObject-fout wilt oplossen die wordt veroorzaakt door het kenmerk userCertificate, kunt u een uitgaande synchronisatie regel implementeren in Azure AD Connect die een null-waarde exporteert in **plaats van de werkelijke waarden voor objecten met meer dan 15 certificaat waarden**. In deze sectie worden de stappen beschreven die nodig zijn voor het implementeren van de synchronisatie regel voor **gebruikers** objecten. De stappen kunnen worden aangepast voor **contact** -en **computer** objecten.

> [!IMPORTANT]
> Als u een null-waarde exporteert, worden de certificaat waarden die eerder zijn geëxporteerd naar Azure AD verwijderd.

De stappen kunnen als volgt worden samenvatten:

1. Schakel de synchronisatie planner uit en controleer of er geen synchronisatie wordt uitgevoerd.
3. Zoek de bestaande regel voor uitgaande synchronisatie voor het kenmerk userCertificate.
4. Maak een vereiste voor de uitgaande synchronisatie regel.
5. Controleer de nieuwe synchronisatie regel op een bestaand object met LargeObject-fout.
6. De nieuwe synchronisatie regel Toep assen op de resterende objecten met LargeObject-fout.
7. Controleer of er geen onverwachte wijzigingen zijn die nog moeten worden geëxporteerd naar Azure AD.
8. Exporteer de wijzigingen naar Azure AD.
9. Synchronisatie planner opnieuw inschakelen.

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Stap 1. Synchronisatie planner uitschakelen en controleren of er geen synchronisatie wordt uitgevoerd
Zorg ervoor dat er geen synchronisatie plaatsvindt terwijl u zich in het midden van de implementatie van een nieuwe synchronisatie regel bevindt om te voor komen dat onbedoelde wijzigingen worden geëxporteerd naar Azure AD. De ingebouwde synchronisatie planner uitschakelen:
1. Start Power shell-sessie op de Azure AD Connect-server.

2. Geplande synchronisatie uitschakelen door cmdlet uit te voeren: `Set-ADSyncScheduler -SyncCycleEnabled $false`

> [!Note]
> De bovenstaande stappen zijn alleen van toepassing op nieuwere versies (1.1. xxx. x) van Azure AD Connect met de ingebouwde scheduler. Als u oudere versies (1.0. xxx. x) gebruikt van Azure AD Connect die gebruikmaken van Windows taak planner, of als u uw eigen aangepaste scheduler (niet common) gebruikt om periodieke synchronisatie te activeren, moet u ze dienovereenkomstig uitschakelen.

1. Start de **Synchronization Service Manager** door naar Start → synchronisatie service te gaan.

1. Ga naar het tabblad **bewerkingen** en controleer of er geen bewerking is waarvan de status wordt *uitgevoerd.*

### <a name="step-2-find-the-existing-outbound-sync-rule-for-usercertificate-attribute"></a>Stap 2. De bestaande regel voor uitgaande synchronisatie voor het kenmerk userCertificate zoeken
Er moet een bestaande synchronisatie regel zijn ingeschakeld en geconfigureerd voor het exporteren van het kenmerk userCertificate voor gebruikers objecten naar Azure AD. Zoek deze synchronisatie regel om de **prioriteit** op te sporen en **filter** configuratie te bepalen:

1. Start de **Editor voor synchronisatie regels** door naar Start → synchronisatie regels editor te gaan.

2. Configureer de zoek filters met de volgende waarden:

    | Kenmerk | Waarde |
    | --- | --- |
    | Richting |**Uitgaand** |
    | MV-object type |**Person** |
    | Connector |*de naam van uw Azure AD-connector* |
    | Type connector-object |**gebruiker** |
    | MV-kenmerk |**userCertificate** |

3. Als u gebruikmaakt van OOB (out-of-box) synchronisatie regels naar Azure AD connector als u het kenmerk userCertficiate voor gebruikers objecten wilt exporteren, moet u de regel *' out to Aad – User ExchangeOnline '* terughalen.
4. Noteer de **prioriteits** waarde van deze synchronisatie regel.
5. Selecteer de synchronisatie regel en klik op **bewerken**.
6. Klik in het pop-upvenster *"gereserveerde regel bewerken"* op **Nee**. (U hoeft geen wijzigingen aan te brengen in deze synchronisatie regel).
7. Selecteer in het scherm bewerken het tabblad **filter bereik** .
8. Noteer de filter configuratie voor bereik. Als u de OOB-synchronisatie regel gebruikt, moet er precies **één bereik filter groep zijn met twee componenten**, waaronder:

    | Kenmerk | Operator | Waarde |
    | --- | --- | --- |
    | sourceObjectType | WAARD | Gebruiker |
    | cloudMastered | NOTEQUAL | Waar |

### <a name="step-3-create-the-outbound-sync-rule-required"></a>Stap 3. De uitgaande synchronisatie regel vereist maken
De nieuwe synchronisatie regel moet hetzelfde **bereik filter** hebben en een **hogere prioriteit** hebben dan de bestaande synchronisatie regel. Dit zorgt ervoor dat de nieuwe synchronisatie regel van toepassing is op dezelfde set objecten als de bestaande synchronisatie regel en overschrijft de bestaande synchronisatie regel voor het kenmerk userCertificate. De synchronisatie regel maken:
1. Klik in de editor voor synchronisatie regels op de knop **nieuwe regel toevoegen** .
2. Geef onder het **tabblad Beschrijving** de volgende configuratie op:

    | Kenmerk | Waarde | Details |
    | --- | --- | --- |
    | Name | *Geef een naam op* | Bijvoorbeeld *' uit tot Aad – Custom override voor userCertificate '* |
    | Description | *Geef een beschrijving op* | Bijvoorbeeld, *als het kenmerk userCertificate meer dan 15 waarden heeft, wordt Null geëxporteerd. "* |
    | Verbonden systeem | *De Azure AD-connector selecteren* |
    | Type verbonden systeem object | **gebruiker** | |
    | Omgekeerd object type | **gelaedeerde** | |
    | Koppelings type | **Join** | |
    | Prioriteit | *Kies een getal tussen 1-99* | Het gekozen nummer mag niet worden gebruikt door een bestaande synchronisatie regel en heeft een lagere waarde (en dus hogere prioriteit) dan de bestaande synchronisatie regel. |

3. Ga naar het tabblad **bereik filteren** en implementeer hetzelfde bereik filter de bestaande synchronisatie regel gebruikt.
4. Sla het tabblad **regels voor samen voegen** over.
5. Ga naar het tabblad **transforms** om een nieuwe trans formatie toe te voegen met behulp van de volgende configuratie:

    | Kenmerk | Waarde |
    | --- | --- |
    | Stroomtype |**Expression** |
    | Doel kenmerk |**userCertificate** |
    | Bronkenmerk |*Gebruik de volgende expressie*: `IIF(IsNullOrEmpty([userCertificate]), NULL, IIF((Count([userCertificate])> 15),AuthoritativeNull,[userCertificate]))` |
    
6. Klik op de knop **toevoegen** om de synchronisatie regel te maken.

### <a name="step-4-verify-the-new-sync-rule-on-an-existing-object-with-largeobject-error"></a>Stap 4. De nieuwe synchronisatie regel voor een bestaand object controleren met LargeObject-fout
Dit is om te controleren of de gemaakte synchronisatie regel correct werkt op een bestaand AD-object met LargeObject-fout voordat u het toepast op andere objecten:
1. Ga naar het tabblad **bewerkingen** in het Synchronization Service Manager.
2. Selecteer de meest recente bewerking voor het exporteren naar Azure AD en klik op een van de objecten met LargeObject-fouten.
3.  Klik in het pop-upvenster eigenschappen van connector Space-object op de knop **Preview** .
4. Selecteer in het pop-upvenster voor beeld de optie **volledige synchronisatie** en klik op **Doorvoervoorbeeld**.
5. Sluit het scherm preview en het scherm Eigenschappen van connector ruimte-object.
6. Ga naar het tabblad **connectors** in de Synchronization Service Manager.
7. Klik met de rechter muisknop op de **Azure AD** -connector en selecteer **uitvoeren...**
8. Selecteer in het pop-upvenster run connector stap **exporteren** en klik op **OK**.
9. Wacht totdat de export naar Azure AD is voltooid en controleer of er geen LargeObject-fout meer is op dit specifieke object.

### <a name="step-5-apply-the-new-sync-rule-to-remaining-objects-with-largeobject-error"></a>Stap 5. De nieuwe synchronisatie regel Toep assen op de resterende objecten met de fout LargeObject
Zodra de synchronisatie regel is toegevoegd, moet u een volledige synchronisatie stap uitvoeren op de AD-connector:
1. Ga naar het tabblad **connectors** in de Synchronization Service Manager.
2. Klik met de rechter muisknop op de **ad** -connector en selecteer **uitvoeren...**
3. Selecteer in het pop-upvenster run connector de stap **volledige synchronisatie** en klik op **OK**.
4. Wacht totdat de volledige synchronisatie stap is voltooid.
5. Herhaal de bovenstaande stappen voor de resterende AD-connectors als u meer dan één AD-connector hebt. Normaal gesp roken zijn meerdere connectors vereist als u meerdere on-premises directory's hebt.

### <a name="step-6-verify-there-are-no-unexpected-changes-waiting-to-be-exported-to-azure-ad"></a>Stap 6. Controleren of er geen onverwachte wijzigingen zijn die nog moeten worden geëxporteerd naar Azure AD
1. Ga naar het tabblad **connectors** in de Synchronization Service Manager.
2. Klik met de rechter muisknop op de **Azure AD** -connector en selecteer **ruimte voor Zoek connector**.
3. In het pop-upvenster Space zoeken in Zoek connector:
    1. Stel het bereik in op **in behandeling zijnde export**.
    2. Schakel alle drie de selectie vakjes in, waaronder **toevoegen**, **wijzigen** en **verwijderen**.
    3. Klik op de knop **zoeken** om alle objecten te retour neren die wachten op exporteren naar Azure AD.
    4. Controleer of er geen onverwachte wijzigingen zijn. Als u de wijzigingen voor een bepaald object wilt onderzoeken, dubbelklikt u op het object.

### <a name="step-7-export-the-changes-to-azure-ad"></a>Stap 7. De wijzigingen exporteren naar Azure AD
De wijzigingen exporteren naar Azure AD:
1. Ga naar het tabblad **connectors** in de Synchronization Service Manager.
2. Klik met de rechter muisknop op de **Azure AD** -connector en selecteer **uitvoeren...**
4. Selecteer in het pop-upvenster run connector stap **exporteren** en klik op **OK**.
5. Wacht totdat de export naar Azure AD is voltooid en controleer of er geen LargeObject-fouten meer zijn.

### <a name="step-8-re-enable-sync-scheduler"></a>Stap 8. Synchronisatie planner opnieuw inschakelen
Nu het probleem is opgelost, moet u de ingebouwde synchronisatie planner opnieuw inschakelen:
1. Power shell-sessie starten.
2. Geplande synchronisatie opnieuw inschakelen door cmdlet uit te voeren: `Set-ADSyncScheduler -SyncCycleEnabled $true`

> [!Note]
> De bovenstaande stappen zijn alleen van toepassing op nieuwere versies (1.1. xxx. x) van Azure AD Connect met de ingebouwde scheduler. Als u oudere versies (1.0. xxx. x) gebruikt van Azure AD Connect die gebruikmaken van Windows taak planner, of als u uw eigen aangepaste scheduler (niet common) gebruikt om periodieke synchronisatie te activeren, moet u ze dienovereenkomstig uitschakelen.

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
