---
title: Azure Key Vault integreren met Azure Policy
description: Informatie over het integreren van Azure Key Vault met Azure Policy
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 10/15/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 6ac4d0e0744bfc82a686671234e013b2dd717146
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92927750"
---
# <a name="integrate-azure-key-vault-with-azure-policy"></a>Azure Key Vault integreren met Azure Policy

[Azure Policy](../../governance/policy/index.yml) is een governance-programma waarmee gebruikers hun Azure-omgeving op schaal kunnen controleren en beheren. Azure Policy biedt de mogelijkheid om beschermingsmaatregelen te koppelen aan Azure-resources om ervoor te zorgen dat deze voldoen aan de toegewezen beleidsregels. Gebruikers kunnen zo audits uitvoeren, in realtime bepaalde zaken afdwingen en indien nodig hun Azure-omgeving herstellen. De resultaten van audits op basis van beleidsregels zijn beschikbaar voor gebruikers in een nalevingsdashboard. Hier kunnen ze inzoomen op de resources en onderdelen die wel of niet voldoen aan de regels.  Zie [Wat is Azure Policy?](../../governance/policy/overview.md) voor meer informatie.

Enkele voorbeeldscenario's:

- U wilt de beveiligingspostuur van uw bedrijf verbeteren door vereisten te implementeren met betrekking tot de minimale grootte van sleutels en de maximale geldigheidsduur van certificaten in de sleutelkluizen van uw bedrijf, maar u weet niet welke van uw teams hieraan voldoen en welke niet.
- U hebt momenteel geen oplossing voor het uitvoeren van een bedrijfsbrede audit, of u voert handmatige audits uit van uw omgeving door afzonderlijke teams binnen uw organisatie te vragen hun naleving te rapporteren. U bent op zoekt naar een manier om deze taak te automatiseren, audits in realtime uit te voeren en de nauwkeurigheid van deze audits te garanderen.
- U wilt het beveiligingsbeleid van uw bedrijf afdwingen en u wilt voorkomen dat individuen zelfondertekende certificaten maken, maar u beschikt niet over een geautomatiseerde manier om dit tegen te gaan. 
- U wilt sommige vereisten afzwakken voor uw testteams, maar u wilt strikte controle houden over uw productieomgeving. U hebt een eenvoudige, geautomatiseerde manier nodig om verschillende niveaus van afdwinging te implementeren voor uw resources.
- U wilt er zeker van zijn dat u het afdwingen van nieuwe beleidsregels kunt terugdraaien in het geval van een probleem met een live-site. U hebt een one-click oplossing nodig om afdwinging van het beleid te kunnen uitschakelen. 
- U bent afhankelijk van een oplossing van derden voor het controleren van uw omgeving en u wilt een intern Microsoft-oplossing gebruiken.

## <a name="types-of-policy-effects-and-guidance"></a>Soorten beleidseffecten en -richtlijnen

**Audit**: Wanneer het effect van een beleid is ingesteld op 'audit', resulteert het beleid niet in wijzigingen die fouten veroorzaken. Er wordt dan alleen een waarschuwing gegeven voor onderdelen zoals certificaten die niet voldoen aan de beleidsdefinities binnen een opgegeven bereik. Deze onderdelen worden dan als 'Niet compatibel' gemarkeerd in het dashboard voor beleidsnaleving. 'Audit' is de standaardinstelling als er geen beleidseffect is geselecteerd.

**Deny**: Wanneer het effect van een beleid is ingesteld op 'deny', blokkeert het beleid het maken van nieuwe onderdelen zoals certificaten, evenals het maken van nieuwe versies van bestaande onderdelen die niet voldoen aan de beleidsdefinitie. Dit effect heeft geen invloed op bestaande niet-compatibele resources in een sleutelkluis. De 'audit'-mogelijkheden blijven van kracht.

## <a name="available-built-in-policy-definitions"></a>Beschikbare 'ingebouwde' beleidsdefinities

In Key Vault is een set beleidsregels gemaakt die kunnen worden gebruikt om sleutel-, certificaat- en geheime objecten te beheren. Deze beleidsregels zijn 'ingebouwd', wat betekent dat u geen aangepaste JSON hoeft te schrijven om ze in te schakelen en dat ze standaard beschikbaar zijn in Azure Portal om toe te wijzen. U kunt wel bepaalde parameters aanpassen aan de behoeften van uw organisatie.

# <a name="certificate-policies"></a>[Certificaatbeleid](#tab/certificates)

### <a name="certificates-should-have-the-specified-maximum-validity-period-preview"></a>Certificaten moeten de opgegeven maximale geldigheidsperiode hebben (preview)

Met dit beleid kunt u de maximale geldigheidsduur beheren van certificaten die zijn opgeslagen in de sleutelkluis. Het wordt aanbevolen om een maximale geldigheidsduur in te stellen voor uw certificaten. Als een persoonlijke sleutel van uw certificaat zou worden onderschept door een kwaadwillende persoon, zorgt een korte levensduur ervoor dat het tijdsbestek voor schade aan uw organisatie wordt geminimaliseerd en is het certificaat dus minder waardevol voor een aanvaller.

### <a name="certificates-should-use-allowed-key-types-preview"></a>Certificaten moeten toegestane sleuteltypen gebruiken (preview)

Met dit beleid kunt u het type certificaat beperken dat in de sleutelkluis kan worden opgeslagen. U kunt dit beleid gebruiken om ervoor te zorgen dat de persoonlijke sleutels voor certificaten zijn beveiligd met RSA, ECC of HSM-back-up. U kunt kiezen uit de volgende lijst welke certificaattypen zijn toegestaan.

- RSA
- RSA - HSM
- ECC
- ECC - HSM

### <a name="certificates-should-have-the-specified-lifetime-action-triggers-preview"></a>Voor certificaten moeten de opgegeven triggers voor levensduuractie zijn ingevoerd (preview)

Met dit beleid kunt u de levensduuractie beheren die is opgegeven voor certificaten die binnen een bepaald aantal dagen verlopen of die een bepaald percentage van hun gebruiksduur hebben bereikt.

### <a name="certificates-should-be-issued-by-the-specified-integrated-certificate-authority-preview"></a>Certificaten moeten worden uitgegeven door de opgegeven geïntegreerde certificeringsinstantie (preview)

Als u gebruikmaakt van een geïntegreerde certificeringsinstantie voor Key Vault (Digicert of GlobalSign) en u wilt dat gebruikers een van deze twee providers gebruiken, kunt u dit beleid gebruiken om uw selectie te controleren of af te dwingen. Dit beleid kan ook worden gebruikt om het maken van zelfondertekende certificaten in de sleutelkluis te controleren of te weigeren.

### <a name="certificates-should-be-issued-by-the-specified-non-integrated-certificate-authority-preview"></a>Certificaten moeten worden uitgegeven door de opgegeven niet-geïntegreerde certificeringsinstantie (preview)

Als u een interne certificeringsinstantie gebruikt of een certificeringsinstantie die niet is geïntegreerd met Key Vault en u wilt dat gebruikers een certificeringsinstantie gebruiken uit een lijst die u opgeeft, kunt u dit beleid gebruiken om een lijst met toegestane certificeringsinstanties te maken op basis van de naam van de uitgever. Dit beleid kan ook worden gebruikt om het maken van zelfondertekende certificaten in de sleutelkluis te controleren of te weigeren.

### <a name="certificates-using-elliptic-curve-cryptography-should-have-allowed-curve-names-preview"></a>Certificaten die gebruikmaken van elliptische-curvecryptografie moeten toegestane curvenamen hebben (preview)

Als u ECC-certificaten (elliptische curvecryptografie) gebruikt, kunt u een lijst met toegestane curvenamen aanpassen op basis van de onderstaande lijst. Met de standaardinstelling worden alle volgende curvenamen toegestaan.

- P-256
- P-256K
- P-384
- P-521

## <a name="certificates-using-rsa-cryptography-manage-minimum-key-size-for-rsa-certificates-preview"></a>Certificaten die gebruikmaken van RSA-cryptografie moeten de gespecificeerde minimale sleutelgrootte hebben (preview)

Als u RSA-certificaten gebruikt, kunt u een minimale sleutelgrootte kiezen die uw certificaten moeten hebben. U kunt een optie selecteren in de onderstaande lijst.

- 2048 bits
- 3072 bits
- 4096 bits

## <a name="manage-certificates-that-are-within-a-specified-number-of-days-of-expiration-preview"></a>[Voorbeeld]: Certificaten beheren die binnen het opgegeven aantal dagen zullen verlopen

Uw service kan worden onderbroken als een certificaat dat niet op de juiste manier wordt bewaakt, niet wordt geroteerd voordat het verloopt. Dit beleid is essentieel om ervoor te zorgen dat uw certificaten die zijn opgeslagen in Key Vault, worden bewaakt. Het wordt aangeraden dit beleid meerdere keren toe te passen met verschillende vervaldrempels, bijvoorbeeld van 180, 90, 60 en 30 dagen. Dit beleid kan worden gebruikt om het verlopen van certificaten in uw organisatie te controleren en er triage op uit te voeren.

# <a name="key-policies"></a>[Sleutelbeleid](#tab/keys)

### <a name="keys-should-not-be-active-for-longer-than-the-specified-number-of-days-preview"></a>Sleutels mogen niet langer actief zijn dan het opgegeven aantal dagen (preview)

Als u er zeker van wilt zijn dat uw sleutels niet langer actief zijn dan een opgegeven aantal dagen, kunt u dit beleid gebruiken om te controleren hoe lang uw sleutel actief is geweest.

**Als voor uw sleutel een activeringsdatum is ingesteld**, wordt met dit beleid het aantal dagen berekend dat is verstreken van de **activeringsdatum** van de sleutel tot de huidige datum. Als het aantal dagen de drempelwaarde overschrijdt die u hebt ingesteld, wordt de sleutel gemarkeerd als niet-compatibel met het beleid.

**Als voor uw sleutel geen activeringsdatum is ingesteld**, wordt met dit beleid het aantal dagen berekend dat is verstreken van de **productiedatum** van de sleutel tot de huidige datum. Als het aantal dagen de drempelwaarde overschrijdt die u hebt ingesteld, wordt de sleutel gemarkeerd als niet-compatibel met het beleid.

### <a name="keys-should-be-the-specified-cryptographic-type-rsa-or-ec-preview"></a>Sleutels moeten het opgegeven cryptografische type RSA of EC hebben (preview)

Met dit beleid kunt u het type sleutel beperken dat in de sleutelkluis kan worden opgeslagen. U kunt dit beleid gebruiken om ervoor te zorgen dat de sleutels zijn beveiligd met RSA, ECC of HSM-back-up. U kunt kiezen uit de volgende lijst welke certificaattypen zijn toegestaan.

- RSA
- RSA - HSM
- ECC
- ECC - HSM

### <a name="keys-using-elliptic-curve-cryptography-should-have-the-specified-curve-names-preview"></a>Sleutels die gebruikmaken van elliptische-curvecryptografie moeten de opgegeven curvenamen hebben (preview)

Als u elliptische-curvecryptografie of ECC-sleutels gebruikt, kunt u een lijst met toegestane curvenamen aanpassen op basis van de onderstaande lijst. Met de standaardinstelling worden alle volgende curvenamen toegestaan.

- P-256
- P-256K
- P-384
- P-521

### <a name="keys-should-have-expirations-dates-set-preview"></a>Voor sleutels moet een vervaldatum zijn ingesteld (preview)

Met dit beleid worden alle sleutels in de sleutelkluizen gecontroleerd en worden sleutels waarvoor geen vervaldatum is ingesteld, als niet-compatibel gemarkeerd. U kunt dit beleid ook gebruiken om te voorkomen dat sleutels worden gemaakt waarvoor geen vervaldatum is ingesteld.

### <a name="keys-should-have-more-than-the-specified-number-of-days-before-expiration-preview"></a>Sleutels moeten meer dan het opgegeven aantal dagen hebben voor ze vervallen (preview)

Als een sleutel bijna is vervallen, kan een organisatorische vertraging in het roteren van de sleutel tot uitval leiden. Sleutels moeten na een bepaald aantal dagen vóór de vervaldatum worden geroteerd om te zorgen dat er voldoende tijd is om op een fout te kunnen reageren. Met dit beleid worden sleutels gecontroleerd die bijna vervallen. U kunt er ook de drempelwaarde in dagen voor instellen. U kunt dit beleid ook gebruiken om te voorkomen dat nieuwe sleutels worden gemaakt die bijna vervallen.

### <a name="keys-should-be-backed-by-a-hardware-security-module-preview"></a>Sleutels moeten worden ondersteund door een hardwarebeveiligingsmodule (preview)

Een HSM is een hardwarebeveiligingsmodule waarin sleutels worden opgeslagen. Een HSM biedt een fysieke beveiligingslaag voor cryptografische sleutels. De cryptografische sleutel kan geen fysieke HSM verlaten die een grotere mate van beveiliging biedt dan een softwaresleutel. Sommige organisaties kennen nalevingsvereisten die het gebruik van HSM-sleutels verplicht stellen. Gebruik dit beleid om sleutels te controleren die zijn opgeslagen in de sleutelkluis die niet door HSM wordt ondersteund. U kunt dit beleid ook gebruiken om te voorkomen dat nieuwe sleutels worden gemaakt die niet door HSM worden ondersteund. Dit beleid is van toepassing op alle sleuteltypen, RSA en ECC.

### <a name="keys-using-rsa-cryptography-should-have-a-specified-minimum-key-size-preview"></a>Sleutels met RSA-cryptografie moeten een opgegeven minimale sleutelgrootte hebben (preview)

Het gebruik van RSA-sleutels met kleinere sleutelgrootten is geen veilige ontwerppraktijk. U bent mogelijk onderhevig zijn aan controle- en certificeringsstandaarden die het gebruik van een minimale sleutelgrootte verplicht stellen. Met het volgende beleid kunt u een verplichte minimale sleutelgrootte instellen voor uw sleutelkluis. U kunt sleutels controleren die niet voldoen aan deze minimumvereiste. Dit beleid kan ook worden gebruikt om het maken van nieuwe sleutels te blokkeren die niet voldoen aan de vereiste van een minimale sleutelgrootte.

### <a name="keys-should-have-the-specified-maximum-validity-period-preview"></a>Sleutels moeten de opgegeven maximale geldigheidsperiode hebben (preview)

Beheer de nalevingsvereisten van uw organisatie door de maximale tijdsduur (in dagen) op te geven dat een sleutel binnen uw sleutelkluis geldig mag zijn. Sleutels die langer geldig zijn dan de drempelwaarde die u instelt, worden gemarkeerd als niet-compatibel. U kunt dit beleid ook gebruiken om te voorkomen dat nieuwe sleutels worden gemaakt waarvan de ingestelde vervaldatum langer is dan de maximale geldigheidsperiode die u opgeeft.

# <a name="secret-policies"></a>[Geheim beleid](#tab/secrets)

### <a name="secrets-should-not-be-active-for-longer-than-the-specified-number-of-days-preview"></a>Geheimen mogen niet langer actief zijn dan het opgegeven aantal dagen (preview)

Als u er zeker van wilt zijn dat uw geheimen niet langer actief zijn dan een opgegeven aantal dagen, kunt u dit beleid gebruiken om te controleren hoe lang uw geheim actief is geweest.

**Als voor uw geheim een activeringsdatum is ingesteld**, wordt met dit beleid het aantal dagen berekend dat is verstreken van de **activeringsdatum** van het geheim tot de huidige datum. Als het aantal dagen de drempelwaarde overschrijdt die u hebt ingesteld, wordt het geheim gemarkeerd als niet-compatibel met het beleid.

**Als voor uw geheim geen activeringsdatum is ingesteld**, wordt met dit beleid het aantal dagen berekend dat is verstreken van de **productiedatum** van het geheim tot de huidige datum. Als het aantal dagen de drempelwaarde overschrijdt die u hebt ingesteld, wordt het geheim gemarkeerd als niet-compatibel met het beleid.

### <a name="secrets-should-have-content-type-set-preview"></a>Voor geheimen moet het inhoudstype zijn ingesteld (preview)

Een bestand zonder opmaak of een gecodeerd bestand kan worden opgeslagen als een sleutelkluisgeheim. Uw organisatie kan echter een ander rotatiebeleid en beperkingen instellen voor wachtwoorden, verbindingsreeksen of certificaten die als sleutels zijn opgeslagen. Met een tag van het inhoudstype kan een gebruiker zien wat er in een geheim object is opgeslagen zonder de waarde van het geheim te lezen. U kunt dit beleid gebruiken om geheimen te controleren waarvoor geen tag van het inhoudstype is ingesteld. U kunt dit beleid ook gebruiken om te voorkomen dat nieuwe geheimen worden gemaakt als er geen tag van het inhoudstype voor is ingesteld.

### <a name="secrets-should-have-expiration-date-set-preview"></a>Voor geheimen moet een vervaldatum zijn ingesteld (preview)

Met dit beleid worden alle geheimen in de sleutelkluizen gecontroleerd en worden geheimen waarvoor geen vervaldatum is ingesteld, als niet-compatibel gemarkeerd. U kunt dit beleid ook gebruiken om te voorkomen dat geheimen worden gemaakt waarvoor geen vervaldatum is ingesteld.

### <a name="secrets-should-have-more-than-the-specified-number-of-days-before-expiration-preview"></a>Geheimen moeten meer dan het opgegeven aantal dagen hebben voor ze vervallen (preview)

Als een geheim bijna is vervallen, kan een organisatorische vertraging in het roteren van het geheim tot uitval leiden. Geheimen moeten na een bepaald aantal dagen vóór de vervaldatum worden geroteerd om te zorgen dat er voldoende tijd is om op een fout te kunnen reageren. Met dit beleid worden geheimen gecontroleerd die bijna vervallen. U kunt er ook de drempelwaarde in dagen voor instellen. U kunt dit beleid ook gebruiken om te voorkomen dat nieuwe geheimen worden gemaakt die bijna vervallen.

### <a name="secrets-should-have-the-specified-maximum-validity-period-preview"></a>Geheimen moeten de opgegeven maximale geldigheidsperiode hebben (preview)

Beheer de nalevingsvereisten van uw organisatie door de maximale tijdsduur (in dagen) op te geven dat een geheim binnen uw sleutelkluis geldig mag zijn. Geheimen die langer geldig zijn dan de drempelwaarde die u instelt, worden gemarkeerd als niet-compatibel. U kunt dit beleid ook gebruiken om te voorkomen dat nieuwe geheimen worden gemaakt waarvan de ingestelde vervaldatum langer is dan de maximale geldigheidsperiode die u opgeeft.

---

## <a name="example-scenario"></a>Voorbeeldscenario

U beheert een sleutelkluis met 100 certificaten die wordt gebruikt door meerdere teams, en u wilt er zeker van zijn dat geen van de certificaten in de sleutelkluis langer dan twee jaar geldig is.

1. U wijst het beleid **Certificaten moeten de opgegeven maximum geldigheidsduur hebben** toe, geeft op dat de maximale geldigheidsduur van een certificaat 24 maanden is en stelt het effect van het beleid in op 'controleren'. 
1. U bekijkt het [nalevingsrapport in Azure Portal](#view-compliance-results) en ontdekt dat twintig certificaten niet-compatibel zijn omdat ze langer dan twee jaar geldig zijn. De overige certificaten voldoen wel aan het beleid. 
1. U neemt contact op met de eigenaren van deze certificaten en licht ze in over de nieuwe beveiligingsvereiste, namelijk dat certificaten niet langer dan twee jaar geldig mogen zijn. Sommige teams reageren en vijftien van de certificaten zijn vernieuwd met een maximale geldigheidsduur van twee jaar of minder. Andere teams reageren echter niet en er zijn dan ook nog steeds vijf niet-compatibele certificaten aanwezig in uw sleutelkluis.
1. U wijzigt het effect van het beleid dat u hebt toegewezen in 'deny'. De vijf niet-compatibele certificaten worden niet ingetrokken en blijven functioneren. Ze kunnen echter niet worden verlengd met een geldigheidsduur die langer is dan twee jaar. 

## <a name="enabling-and-managing-a-key-vault-policy-through-the-azure-portal"></a>Een Key Vault-beleid inschakelen en beheren via Azure Portal

### <a name="select-a-policy-definition"></a>Een beleidsdefinitie selecteren

1. Meld u aan bij Azure Portal. 
1. Zoek 'beleid' in de zoek balk en selecteer **Beleid**.

    ![Schermopname met de zoekbalk.](../media/policy-img1.png)

1. Selecteer **Definities** in het venster Beleid.

    ![Schermopname waarop de optie Definities is gemarkeerd.](../media/policy-img2.png)

1. Schakel in de vervolgkeuzelijst Categorie het selectievakje **Alles selecteren** uit en selecteer **Key Vault**. 

    ![Schermopname van het filter Categorie en de geselecteerde categorie Key Vault.](../media/policy-img3.png)

1. U ziet nu alle beleidsregels die in openbare preview beschikbaar zijn voor Azure Key Vault. Zorg ervoor dat u de sectie met richtlijnen voor beleidsregels hierboven hebt gelezen en begrepen en selecteer een beleid dat u wilt toewijzen.  

    ![Schermopname van de beleidsregels die beschikbaar zijn als openbare preview-versie.](../media/policy-img4.png)

### <a name="assign-a-policy-to-a-scope"></a>Een beleid toewijzen 

1. Selecteer een beleid dat u wilt toepassen, in dit voorbeeld **Geldigheidsduur van certificaat beheren**. Klik linksboven op de knop Toewijzen.

    ![Schermopname van het beleid Geldigheidsduur van certificaat beheren.](../media/policy-img5.png)
  
1. Selecteer het abonnement waarop u het beleid wilt toepassen. U kunt ervoor kiezen om het bereik te beperken tot één resourcegroep binnen een abonnement. Als u het beleid wilt toepassen op het hele abonnement en bepaalde resourcegroepen wilt uitsluiten, kunt u ook een uitsluitingslijst configureren. Stel Beleidsafdwinging in op **Ingeschakeld** als u wilt dat het effect van het beleid (audit of deny) wordt toegepast. Selecteer **Uitgeschakeld** om het effect (audit of deny) uit te schakelen. 

    ![Schermopname die laat zien waar u ervoor kunt kiezen om het bereik te beperken tot één resourcegroep binnen een abonnement.](../media/policy-img6.png)

1. Klik boven aan het scherm op het tabblad Parameters om de maximale geldigheidsduur op te geven in maanden. Selecteer **audit** of **deny** voor het effect van het beleid volgens de richtlijnen in de bovenstaande secties. Selecteer ten slotte de knop Beoordelen en maken. 

    ![Schermopname met het tabblad Parameters, waar u de gewenste maximale geldigheidsduur kunt opgeven in maanden.](../media/policy-img7.png)

### <a name="view-compliance-results"></a>Nalevingsresultaten weergeven

1. Ga terug naar de blade Beleid en selecteer het tabblad Naleving. Klik op de beleidstoewijzing waarvoor u de nalevingsresultaten wilt bekijken.

    ![Schermopname van het tabblad Naleving, waar u de beleidstoewijzing kunt selecteren waarvoor u nalevingsresultaten wilt weergeven.](../media/policy-img8.png)

1. Op deze pagina kunt u de resultaten filteren op compatibele of niet-compatibele kluizen. Hier kunt u een lijst weergeven met niet-compatibele sleutelkluizen binnen het bereik van de beleidstoewijzing. Een kluis wordt als niet-compatibel beschouwd als een van de onderdelen (certificaten) in de kluis niet compatibel is. U kunt een specifieke kluis selecteren om de afzonderlijke niet-compatibele onderdelen (certificaten) weer te geven. 


    ![Schermopname van een lijst met niet-compatibele sleutelkluizen binnen het bereik van de beleidstoewijzing.](../media/policy-img9.png)

1. Bekijk de namen van de onderdelen in een kluis die niet-compatibel zijn.


    ![Schermopname die laat zien waar u de namen kunt bekijken van de onderdelen in een kluis die niet-compatibel zijn.](../media/policy-img10.png)

1. Als u wilt controleren of gebruikers niet de mogelijkheid hebben om resources te maken in de sleutelkluis, klikt u op het tabblad **Onderdeelgebeurtenissen (preview)** om een samenvatting te bekijken van geweigerde certificaatbewerkingen met de aanvrager en tijdstempels van aanvragen. 


    ![Overzicht van hoe Azure Key Vault werkt](../media/policy-img11.png)

## <a name="feature-limitations"></a>Functiebeperkingen

Als u een beleid met een 'deny'-effect toewijst, duurt het maximaal dertig minuten (gemiddeld) tot één uur (slechtste geval) voordat het maken van niet-compatibele resources wordt geweigerd. Bij een beleidsevaluatie van bestaande onderdelen in een kluis duurt het maximaal één uur (gemiddeld) tot twee uur (slechtste geval) voordat de compatibiliteitsresultaten zichtbaar zijn in de gebruikersinterface van de portal. Als de resultaten worden weergegeven als 'Niet gestart', kan dit de volgende oorzaken hebben:
- De beleidsevaluatie is nog niet voltooid. In het slechtste geval duurt het maximaal twee uur voordat de eerste evaluatie is voltooid. 
- Er bevinden zich geen sleutelkluizen binnen het bereik van de beleidstoewijzing.
- Er bevinden zich geen sleutelkluizen met certificaten binnen het bereik van de beleidstoewijzing.

> [!NOTE]
> De [resourceprovidermodi](../../governance/policy/concepts/definition-structure.md#resource-provider-modes) van Azure Policy, zoals die voor Azure Key Vault, bevatten informatie over naleving op de pagina [Compatibiliteit van onderdelen](../../governance/policy/how-to/get-compliance-data.md#component-compliance).

## <a name="next-steps"></a>Volgende stappen

- Lees meer over de [Azure Policy-service](../../governance/policy/overview.md)
- Bekijk voorbeelden van Key Vault: [Azure Policy built-in policy definitions](../../governance/policy/samples/built-in-policies.md#key-vault) (Ingebouwde beleidsdefinities voor Key Vault)
