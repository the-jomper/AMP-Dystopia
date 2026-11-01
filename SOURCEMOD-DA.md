# SourceMod og respawn (skabelonversion 5)

## Installation

Når version 5 er anvendt på instansen, findes fanerne **SourceMod** og **Respawn** under **Configuration > Dystopia**.

1. Stop spilserveren.
2. Vælg **Metamod / SourceMod Management > Install / update and activate**. Begge branches står som standard på 1.12.
3. Kør spilserverens **Update** i AMP. At gemme indstillingen alene installerer ikke noget.
4. Start spilserveren og skriv `meta version`, `meta list` og `sm version` i AMP-konsollen.
5. I spillet åbnes adminmenuen med `sm_admin` i konsollen, når din Steam-konto er tilføjet.

Pakkerne hentes fra Metamod/SourceMods officielle hjemmesider. Installation af Metamod sker før SourceMod. SourceMod-konfiguration og eksisterende pluginvalg bevares; installerede standardplugins og binære filer opdateres. En ny standardplugin, der ikke findes i en eksisterende installation, placeres deaktiveret.

**Keep existing / do nothing** stopper automatisk installation/opdatering; det deaktiverer ikke allerede installerede mods. Vælg **Deactivate (keep files)** og kør Update, hvis du vil deaktivere dem. Genstart derefter spillet. Filer og admins bevares, så de kan bruges igen.

Installerens AMP-format følger CubeCoders' officielle Source-skabelon, men Dystopia med disse konkrete mod-builds er ikke testet på en kørende server. Hvis indlæsningen fejler, send resultatet af `meta version`, `meta list`, `sm version` og relevante fejl fra opstartsloggen.

## Admins

Slå **Manage SourceMod Admin List** til og skriv en linje pr. person i **SourceMod Admins**. Find SteamID med `status` i spilkonsollen, mens personen er tilsluttet.

Eksempler (erstat ID'erne):

```text
"STEAM_0:1:123456" "99:z"
"STEAM_0:0:654321" "bcdfgjk"
```

`99:z` giver fuld adgang. `bcdfgjk` giver adminmenu, kick, ban, slay, mapskift, adminchat og afstemninger.

Stop spillet, kør Update og start det igen for at anvende listen. AMP erstatter `dystopia/addons/sourcemod/configs/admins_simple.ini` med feltets indhold. Før hver erstatning gemmes den tidligere fil som `admins_simple.ini.amp-backup`; ældre backups nummereres. En tom liste fjerner entries i denne fil. Rettigheder fra `admins.cfg` eller SQL påvirkes ikke. Når administrering slås fra, bliver den eksisterende liste liggende.

Der tildeles ingen admins som standard, og SteamID'er skal kun indtastes i dit eget AMP-panel, ikke i GitHub-skabelonen.

## Respawn

**Scale Attacker Respawn Time (Legacy)** styrer `mp_scalespawntime` med 0/1. Det er ikke en fast respawn-tid i sekunder. Der findes en udviklerkommentar om, at den muligvis ikke længere virker efter ændringer i Dystopia 1.4.

**Population-based Respawn Scaling** styrer den nyere `dys_spawntime_per_player`, som udgivelsesnoterne beskriver som spillerantalbaserede respawn-bølger. Standard er 0 (deaktiveret). Den præcise værdi, enhed og effekt skal kontrolleres mod din serverbuild; feltet lover ikke en fast respawn-tid. Skriv følgende uden en værdi i AMP-konsollen for at se serverens egen beskrivelse:

```text
mp_scalespawntime
dys_spawntime_per_player
```

Ældre builds kan svare Unknown command på den nyere indstilling. Begge værdier skrives til `dystopia/cfg/amp-server.cfg`, fordi skabelonen starter med `+servercfgfile amp-server.cfg`. En ændring i `server.cfg` er derfor ikke nok i denne opsætning. Genstart spillet efter ændringer. Fjern dublerede kommandoer fra Additional Server Settings, hvis de tilsidesætter felterne.

## Kendt AMP-opgraderingsproblem

Hvis opdatering af selve instansens skabelon fejler med `--main/dystopia.kvp`, bliver de nye felter ikke installeret. Version 5 løser ikke denne særskilte AMP-fejl. Kontrollér at den anvendte instans har `Meta.ConfigVersion=5`; ændr ikke versionsnummeret manuelt. En ny instans kan bruges til afprøvning, mens den eksisterende instans bevares.

## Kilder

- https://github.com/CubeCoders/AMPTemplates/blob/main/half-life-deathmatch-sourceupdates.json
- https://wiki.alliedmods.net/Adding_Admins_(SourceMod)
- https://sourcemod.net/downloads.php
- https://metamodsource.net/downloads.php
- https://steamcommunity.com/app/17580/allnews/
- https://steamcommunity.com/app/17580/discussions/0/152390014785349924/
