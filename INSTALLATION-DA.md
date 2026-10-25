# Dystopia til AMP – Linux

Denne pakke indeholder en lokal Generic Module-skabelon til Linux x86_64. Dystopias server er en 32-bit applikation. Windows og ARM er ikke understøttet af denne udgave. AMP 2.6.0.0 eller nyere er angivet som minimum, svarende til den Source-skabelon pakken er baseret på.

Status: Filformat, referencer og indstillingsmapping er kontrolleret. Installation, start, RCON, konfigurationsændringer og stop er IKKE testet i en kørende AMP-installation. Behandl pakken som en første testversion.

## Installation via GitHub

Tilføj `the-jomper/AMP-Dystopia:main` under Configuration → Instance Deployment → Configuration Repositories. Behold CubeCoders-kilden, klik Fetch Latest, og genindlæs browseren. Opret derefter en instans med Dystopia-skabelonen.

## Alternativ: lokal installation

1. Find din ADS-instans på AMP-værten (ofte ADS01). Det er administrationsinstansen, ikke en spilinstans.
2. Opret undermappen `Plugins/ADSModule/DeploymentTemplates/LOCAL-Dystopia-main` i ADS-instansens mappe.
3. Kopiér de fem filer `dystopia.kvp`, `dystopiaconfig.json`, `dystopiametaconfig.json`, `dystopiaports.json` og `dystopiaupdates.json` direkte ind i den nye mappe. Bevar ejerskab og læserettigheder for AMP-brugeren.
4. Genindlæs AMP i browseren og opret en ny instans med den lokale Dystopia-skabelon. Hvis skabelonen ikke vises, genstart ADS-instansen og genindlæs browseren.
5. Åbn spilinstansen. Vælg den relevante IP-binding og tryk Update for at installere serveren via SteamCMD.
6. Indstil servernavn, adgangskode, map og spillerantal. Standard er `dys_broadcast` og 16 spillere; skabelonen begrænser indstillingen til 32.
7. Tryk Start. Kontrollér at AMP bliver Ready, og forbind fra Dystopia-klienten.

ZIP-filen skal pakkes ud; den er ikke en AMP-backup, som kan gendannes. Skabelonen er ikke publiceret til CubeCoders' repository.

## Porte

| Standardport | Protokol | Brug |
| --- | --- | --- |
| 27015 | UDP | Spil og serverforespørgsler |
| 27015 | TCP | RCON til AMP |
| 27020 | UDP | SourceTV, hvis det anvendes |
| 27005 | UDP | Source-klientport |

AMP kan tildele andre porte ved konflikter. Brug de faktisk tildelte porte i firewall og eventuel port forwarding. RCON skal kunne nås fra AMP, men behøver ikke åbnes til hele internettet.

## Konfiguration og teknik

- SteamCMD installerer server-app `17585` anonymt; spillets klient-app er `17580`.
- Serveren installeres under `dystopia/17585/` relativt til AMP-spilinstansen.
- Linux-startfilen er sat til `17585/bin/linux32/srcds`. Arbejdsmappen er `17585`, og biblioteksstien inkluderer `bin/linux32`. Den direkte eksekverbare fil bruges i stedet for at starte et shellscript.
- Startstien er baseret på den dokumenterede depotstruktur. Den aktuelt installerede Steam-build skal verificeres ved første installation, da serverfiler kan ændre struktur.
- AMP skriver sine indstillinger til `dystopia/17585/dystopia/cfg/amp-server.cfg`. Parameteren `+servercfgfile amp-server.cfg` vælger denne fil. Redigér de administrerede indstillinger i AMP; filen kan blive genskrevet.
- AMP genererer og gemmer RCON-adgangskoden. Ready-status afhænger af en vellykket RCON-forbindelse; stop sendes som `quit` via RCON.
- På en Linux-vært uden container skal de nødvendige 32-bit systembiblioteker være installeret. Ved manglende biblioteker kan `ldd bin/linux32/srcds`, kørt i serverens installationsmappe, hjælpe med at identificere dem.

## Første driftstest

Kontrollér Update, Start → Ready, forbindelse fra en spiller, ændring af servernavn og map efter genstart samt Stop → Stopped. Hvis start fejler, gem AMP-loggen og kontrollér først, at `bin/linux32/srcds` findes og kan eksekveres. Send gerne fejlteksten sammen med AMP-version og Linux-distribution, så skabelonen kan tilpasses den installerede build.

## Kilder og licens

Tilpasset fra Greelans Half-Life Deathmatch: Source-skabelon i CubeCoders/AMPTemplates (MIT; licens vedlagt), læst ved commit `aa716232ba4f1718043b8373bb6b6338b4cff3f0`.

- [AMPTemplates](https://github.com/CubeCoders/AMPTemplates)
- [AMP-dokumentation og lokal installation](https://github.com/CubeCoders/AMP/wiki/Configuring-the-%27Generic%27-AMP-module)
- [LinuxGSM Dystopia-konfiguration: app-ID, map og startparametre](https://github.com/GameServerManagers/LinuxGSM/blob/master/lgsm/config-default/config-lgsm/dysserver/_default.cfg)
- [Historisk Linux-depotstruktur; skal verificeres mod installeret build](https://steamdb.info/depot/806641/history/)
- [Dystopia på Steam](https://store.steampowered.com/app/17580/Dystopia/)

