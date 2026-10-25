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


## Opdatering til version 2: RCON og egne kommandoer

Stop spilserveren. Vælg Fetch Latest under Configuration Repositories i ADS.
Højreklik derefter på den eksisterende instans og vælg Refresh Configuration.
Kør Update inde i spilinstansen, så den nye AMP_server.cfg-skabelon bliver hentet.
Start serveren igen og prøv kommandoen status i AMP-konsollen.

Under Configuration → Dystopia findes nu Additional Server Settings.
Skriv én kommando pr. linje, for eksempel:

    sv_gravity 600
    mp_allowvoting_map 1
    mp_allowvoting_kick 1

Kommandoerne køres ved start og mapskift. Indstillinger, som findes som særskilte
felter i AMP, bør ændres i de felter. Angiv ikke rcon_password i egne kommandoer;
den sættes automatisk til AMP's adgangskode efter de øvrige indstillinger.
Brug ikke exec amp-server.cfg i feltet, da det giver rekursion.

settings.scr beskriver opsætningsmenuen i spillet. server.cfg.example er kun et
eksempel; den køres ikke automatisk under dette navn. Skabelonen bruger
amp-server.cfg som den aktive serverkonfiguration.

Bad Password betyder, at RCON-forbindelsen når frem, men adgangskoden afvises.
Version 2 erstatter den oprindelige KVP-mapping med samme template/AutoMap-metode
som CubeCoders' Source-skabeloner. Den præcise årsag på din server kan først
bekræftes ved kontrol af den genererede konfiguration og en ny RCON-test.
Hvis fejlen fortsætter: Kontrollér at amp-server.cfg indeholder en ikke-tom
rcon_password, at ingen andre configs/plugins ændrer den, og at AMP peger på den
korrekte spilport. Del ikke selve adgangskoden. Retningen på IP-adressen i loggen
viser RCON-klienten; loglinjen alene beviser ikke, at klienten er AMP.

Rettelsen er statisk kontrolleret, men endnu ikke driftstestet mod din instans.

## Version 3: mapvalg

Initial Map er nu en dropdown med de 19 BSP-maps fra din installation:
dys_assemble, dys_broadcast, dys_coast, dys_cybernetic, dys_detonate,
dys_exodus, dys_fortress, dys_fortress_classic, dys_fusion, dys_injection,
dys_lobby, dys_silo, dys_undermine, dys_vaccine, pb_dojo, pb_rooftop,
pb_round, pb_urbandome og varena.

Use Custom Map er slået fra som standard: Initial Map bruges.
Slå kontakten til for at bruge Custom Map Name. Indtast navnet uden .bsp.
Kortet skal allerede findes i dystopia/maps. Feltet downloader ikke maps.
Custom Map Name har dys_broadcast som et gyldigt udgangspunkt; erstat det med
dit eget mapnavn før aktivering. De to felter står efter Initial Map;
placeringen side om side afhænger af AMP's responsive layout.

Hent ændringen med Fetch Latest og Refresh Configuration på den eksisterende
instans, og genindlæs webinterfacet. Genstart spilserveren efter ændring af map.
Maplisten ændres ikke automatisk, når filer uploades eller slettes.
Ændringen er statisk kontrolleret; live-validering i AMP mangler.

## Version 4: navn og flere indstillinger

Repository-prefix er fjernet, så applikationslisten viser Dystopia.
Der er nu 51 konfigurationsposter (inklusive skjult RCON-felt), med faner for
Server, Gameplay, Bots, Voting, Network og Experimental.

33 nye felter dækker bots, tom-server-dvale, legacy spawn-skalering, stopwatch,
chatpause efter runder, ceasefire, gravity, cheats, individuelle afstemninger,
stemmekrav og bantider, netværksrater samt 1.5.8's tre eksperimentelle tilvalg.
De eksisterende felter og mapvalg bevares.

Bot Count=0 slår bots fra. Test først med Initial Map=dys_detonate og Bot Count=8.
Til bots på en tom server: slå Allow Bots Without Players til og Hibernate When
Empty fra. Allowed Bot Maps accepterer en kommasepareret liste eller *.
Bots kræver serverversion 1.5.8. bot_status i konsollen hjælper ved fejlsøgning.

Scale Attacker Spawn Time (Legacy) bruger mp_scalespawntime. Kommandoen er
dokumenteret, men har været rapporteret uden effekt siden 1.4. Der er ikke
tilføjet en opdigtet indstilling til fast respawn-tid i sekunder.
Round Start Delay er ventetid ved rundestart, ikke respawn efter dødsfald.

Additional Server Settings udføres efter de dedikerede felter og kan derfor
overskrive dem. Fjern eksempelvis gamle bot_quota-linjer derfra, hvis du vil
styre bots med det nye felt. RCON-adgangskoden sættes fortsat til sidst af AMP.

Installation: Fetch Latest i ADS, Refresh Configuration på instansen,
Update inde i spilinstansen for at hente den nye AMP_server.cfg, genindlæs
webinterfacet og genstart spilserveren. Geninstallation er ikke et krav.
Hvis --main-fejlen vender tilbage, er opgraderingen ikke gennemført.

Standardværdier for nye felter kommer fra din server.cfg.example, 1.5.8-noterne
og serverguiden nedenfor. Opdatering introducerer dermed eksplicitte værdier
for indstillinger, der tidligere blev overladt til spillet. Gennemse dem før start.
Netværkets minimumsværdier bør ikke overstige de tilsvarende maksimumsværdier.

Kilder:
- Din server.cfg.example fra Dystopia-installationen.
- https://steamcommunity.com/games/17580/announcements/detail/670631830781695620
- https://github.com/dysemjay/dys-server-guide/blob/master/guide.md
- https://steamcommunity.com/app/17580/discussions/0/152390014785349924/

JSON, unikke feltnavne og skabelonmapping er kontrolleret lokalt.
Den udvidede konfiguration er endnu ikke driftstestet i AMP/Dystopia.
