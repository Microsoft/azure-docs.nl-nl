---
title: Server referenties opgeven voor het detecteren van software-inventaris, afhankelijkheden en SQL Server instanties en data bases
description: Meer informatie over het opgeven van Server referenties op het configuratie beheer van het apparaat
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: 990ca661eb6ec17c7f8aca246c15f89fcf8975a8
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785220"
---
# <a name="provide-server-credentials-to-discover-software-inventory-dependencies-and-sql-server-instances-and-databases"></a>Server referenties opgeven voor het detecteren van software-inventaris, afhankelijkheden en SQL Server instanties en data bases

In dit artikel vindt u informatie over het toevoegen van meerdere Server referenties op het configuratie beheer van het apparaat om software-inventarisatie uit te voeren (geïnstalleerde toepassingen te detecteren), afhankelijkheids analyse zonder agents en detectie van SQL Server instanties en data bases.

> [!Note]
> Detectie en evaluatie van SQL Server instanties en data bases die worden uitgevoerd in uw VMware-omgeving is nu beschikbaar als preview-versie. Als u deze functie wilt proberen, gebruikt u [**deze koppeling**](https://aka.ms/AzureMigrate/SQL) om een project te maken in de regio **Australië - oost**. Als u al een project in Australië-oost hebt en u deze functie wilt proberen, zorgt u ervoor dat u aan deze [**vereisten**](how-to-discover-sql-existing-project.md) voldoet in de portal.

Het [Azure migrate apparaat](migrate-appliance.md) is een licht gewicht dat door Azure migrate wordt gebruikt: detectie en evaluatie om on-premises servers in VMware-omgeving te detecteren en de meta gegevens van de server configuratie en-prestaties naar Azure te verzenden. Het apparaat kan ook worden gebruikt voor het uitvoeren van software-inventarisatie, afhankelijkheids analyse zonder agent en detectie van SQL Server instanties en data bases.

Als u deze functies wilt gebruiken, kunt u de referenties van de server opgeven door de volgende stappen uit te voeren. Het apparaat zal proberen de referenties automatisch toe te wijzen aan de servers om de detectie functies uit te voeren.

## <a name="add-credentials-for-servers-running-in-vmware-environment"></a>Referenties toevoegen voor servers die worden uitgevoerd in de VMware-omgeving

### <a name="types-of-server-credentials-supported"></a>Ondersteunde typen Server referenties

U kunt meerdere Server referenties toevoegen op de configuratie beheer van het apparaat, die domein, niet-domein (Windows of Linux) of SQL Server verificatie referenties kan zijn.

De typen Server referenties die worden ondersteund, worden weer gegeven in de onderstaande tabel:

Type referenties | Beschrijving
--- | ---
**Domein referenties** | U kunt **domein referenties** toevoegen door de optie te selecteren in de vervolg keuzelijst in het dialoog venster **referenties toevoegen** modaal. <br/><br/> Om domein referenties op te geven, moet u de **domein naam** opgeven die moet worden opgegeven in de FQDN-indeling (bijvoorbeeld Prod.Corp.contoso.com). <br/><br/> U moet ook een beschrijvende naam opgeven voor de referenties, de gebruikers naam en het wacht woord. <br/><br/> De domein referenties die worden toegevoegd, worden automatisch gevalideerd voor authenticiteit met de Active Directory van het domein. Dit is om te voor komen dat er account vergrendelingen worden uitgevoerd wanneer het apparaat de domein referenties probeert toe te wijzen aan gedetecteerde servers. <br/><br/> Het apparaat zal niet proberen om de domein referenties te koppelen waarvoor de validatie is mislukt. U moet ten minste één domein referentie hebben gevalideerd of ten minste één niet-domein referentie hebben om door te gaan met de software-inventarisatie.<br/><br/>De domein referenties die automatisch worden toegewezen aan de Windows-servers, worden gebruikt voor het uitvoeren van software-inventarisatie en kunnen ook worden gebruikt voor het detecteren van SQL Server instanties en data bases _(als u de Windows-verificatie modus hebt geconfigureerd op uw SQL-servers)_.<br/> Meer [informatie](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authentication-in-sql-server) over de typen verificatie modi die worden ondersteund op SQL-servers.
**Niet-domein referenties (Windows/Linux)** | U kunt **Windows (niet-domein)** of **Linux (niet-domein)** toevoegen door de optie vereist te selecteren in de vervolg keuzelijst van het dialoog venster **referenties toevoegen** modaal. <br/><br/> U moet een beschrijvende naam opgeven voor de referenties, de gebruikers naam en het wacht woord.
**Verificatie referenties SQL Server** | U kunt **SQL Server verificatie** referenties toevoegen door de optie te selecteren in de vervolg keuzelijst in het dialoog venster **referenties toevoegen** modaal. <br/><br/> U moet een beschrijvende naam opgeven voor de referenties, de gebruikers naam en het wacht woord. <br/><br/> U kunt dit type referenties toevoegen om SQL Server instanties en data bases te detecteren die in uw VMware-omgeving worden uitgevoerd, als u de SQL Server-verificatie modus hebt geconfigureerd op uw SQL-servers.<br/> Meer [informatie](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authentication-in-sql-server) over de typen verificatie modi die worden ondersteund op SQL-servers.<br/><br/> U moet ten minste één domein referentie hebben gevalideerd of ten minste één Windows-Referentie (niet-domein), zodat het apparaat de software-inventaris kan volt ooien om SQL te detecteren die op de servers is geïnstalleerd voordat de SQL Server verificatie referenties worden gebruikt om de SQL Server instanties en data bases te detecteren.

Controleer de vereiste machtigingen voor de Windows/Linux-referenties voor het uitvoeren van de software-inventaris, afhankelijkheids analyse zonder agent en detectie van SQL Server instanties en data bases.

### <a name="required-permissions"></a>Vereiste machtigingen

De volgende tabel bevat de machtigingen die vereist zijn voor de Server referenties die op het apparaat zijn opgegeven voor het uitvoeren van de desbetreffende functies:

Functie | Windows-referenties niet op te geven | Linux-referenties
---| ---| ---
**Software-inventaris** | Gast gebruikers account | Normaal/normaal gebruikers account (niet-sudo toegangs machtigingen)
**Detectie van SQL Server instanties en data bases** | Gebruikers account dat lid is van de serverrol sysadmin. | _Momenteel niet ondersteund_
**Analyse van afhankelijkheid zonder agent** | Een domein of een niet-domein account (lokaal) met beheerders machtigingen | Hoofd gebruikers account of <br/> een account met deze machtigingen voor/bin/netstat-en/bin/ls-bestanden: CAP_DAC_READ_SEARCH en CAP_SYS_PTRACE.<br/><br/> Stel deze mogelijkheden in met behulp van de volgende opdrachten: <br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/ls<br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/netstat

### <a name="recommended-practices-to-provide-credentials"></a>Aanbevolen procedures voor het opgeven van referenties

- Het is raadzaam om een toegewezen domein gebruikers account met de [vereiste machtigingen](add-server-credentials.md#required-permissions)te maken. Dit is de scope voor het uitvoeren van software-inventaris, afhankelijkheids analyse zonder agent en detectie van SQL Server instanties en data bases op de gewenste servers.
- Het wordt aanbevolen om ten minste één geslaagde domein referentie of ten minste één niet-domein referentie op te geven om software-inventaris te initiëren.
- Als u SQL Server instanties en data bases wilt detecteren, kunt u domein referenties opgeven als u de Windows-verificatie modus hebt geconfigureerd op uw SQL-servers.
- U kunt ook SQL Server verificatie referenties opgeven als u de SQL Server-verificatie modus hebt geconfigureerd op uw SQL-servers, maar u wordt aangeraden ten minste één geldige domein referentie of ten minste één Windows-Referentie (niet-domein) op te geven, zodat het apparaat de software-inventaris kan volt ooien.

## <a name="credentials-handling-on-appliance"></a>Afhandeling van referenties op apparaat

- Alle referenties die in de configuratie beheer van het apparaat worden gegeven, worden lokaal opgeslagen op de toestel server en worden niet naar Azure verzonden.
- De referenties die zijn opgeslagen op de toestel server, worden versleuteld met behulp van de Data Protection API (DPAPI).
- Nadat u referenties hebt toegevoegd, probeert het apparaat automatisch de referenties toe te wijzen om detectie op de desbetreffende servers uit te voeren.
- Het apparaat gebruikt de referenties die automatisch zijn toegewezen op een server voor alle volgende detectie cycli, en de referenties kunnen de vereiste detectie gegevens ophalen. Als de referenties niet meer werken, probeert het apparaat opnieuw toe te wijzen vanuit de lijst met toegevoegde referenties en de doorlopende detectie op de server voort te zetten.
- De domein referenties die worden toegevoegd, worden automatisch gevalideerd voor authenticiteit met de Active Directory van het domein. Dit is om te voor komen dat er account vergrendelingen worden uitgevoerd wanneer het apparaat de domein referenties probeert toe te wijzen aan gedetecteerde servers. Het apparaat zal niet proberen om de domein referenties te koppelen waarvoor de validatie is mislukt.
- Als het apparaat geen domein-of niet-domein referenties kan toewijzen aan een server, ziet u de status ' referenties niet beschikbaar ' voor de server in uw project.

## <a name="next-steps"></a>Volgende stappen

Bekijk de zelf studies voor [detectie van servers die in uw VMware-omgeving worden uitgevoerd](tutorial-discover-vmware.md)