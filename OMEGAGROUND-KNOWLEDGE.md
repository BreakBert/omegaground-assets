# OMEGAGROUND – Claude Modding Knowledge Base

> Diese Datei enthält das komplette Modding-Wissen für den OMEGAGROUND 7DTD Server.
> Bei Bedarf einfach in eine Claude-Session einfügen.

---

## Server & Infrastruktur

- **Spiel-Version:** 7 Days to Die V2.6 (b14)
- **Server-Typ:** PVP/EU | 300XP/300Loot
- **Welt:** Hecaxi County (6144×6144)
- **Mod-Verzeichnis:** `~/serverfiles/Mods/`
- **Saves:** `/home/sdtdserver/.local/share/7DaysToDie/Saves`
- **Console Logs:** `/home/sdtdserver/log/console/`
- **Live + Testserver** vorhanden
- **Allocs:** 29.0.0 / 37.0.0 / 51.0.0
- **Extras:** Multi_Trade PVE Zone, 24 RegionResets 35%
- **Discord:** discord.gg/fzbJyGpvBj
- **GitHub Assets:** github.com/BreakBert/omegaground-assets
  - Raw URL: `raw.githubusercontent.com/BreakBert/omegaground-assets/main/DATEINAME`
  - Enthält: OMEGAGROUND-BANNER-2304.png (2304×342px), OMEGAGROUND-MAP.png, spd_discord.png, linkURL_1.xml

---

## Wichtigste Modding-Regeln

1. **VOR dem Schreiben von XML-Dateien IMMER erst die relevanten Vanilla-Dateien auf dem Server abfragen lassen. Nie raten.**
2. Jeder Fehler = Server-Restart + Rejoin + Retest — das kostet viel Zeit.
3. Erst alle nötigen Infos sammeln, dann einmal korrekt schreiben.
4. Christoph ersetzt Dateiinhalte **immer komplett via nano** — immer die vollständige Datei liefern, nie nur Diffs oder Ausschnitte.
5. Mod deaktivieren: Ordner **komplett aus dem Mods-Verzeichnis herausbewegen**. Der `_DISABLED`-Suffix verhindert das Laden NICHT.
6. Linux ist case-sensitive: Dateinamen müssen exakt wie Vanilla sein (z.B. `progression.xml` nicht `Progression.xml`).

---

## XML-Grundwissen

### Root-Tags
- Bundle-Mods, Blöcke, Items, Recipes: `<configs>` (NICHT `<config>`)
- loot.xml: Root muss `<config>` sein (NICHT `<configs>`)

### ModInfo.xml
- **Name UND DisplayName** müssen gesetzt sein, sonst wird der Mod ignoriert (aber XML-Dateien werden trotzdem geladen!)

### Allgemeine xpath-Tipps
- `<set>` funktioniert nur wenn die Property bereits existiert
- `<append>` fügt neue Elemente hinzu
- `<insertAfter>` fügt nach einem bestimmten Element ein
- Bei Vanilla-Dateien immer zuerst per `grep` oder `sed` prüfen wie die Struktur exakt aussieht

---

## Bundle-Mods (Ressourcen-Stapel)

### Struktur eines neuen Bundle-Items
```xml
<configs>
    <append xpath="/items">
        <item name="resourceClayLumpBundle">
            <property name="Extends" value="resourceRockSmallBundle"/>
            <property name="CustomIcon" value="resourceClayLump"/>
            <property name="SoundPickup" value="clay_grab"/>
            <property name="SoundPlace" value="clay_place"/>
            <property class="Action0">
                <property name="Create_item" value="resourceClayLump"/>
                <property name="Create_item_count" value="10000"/>
            </property>
        </item>
    </append>
</configs>
```

### Rezept für Bundle
```xml
<configs>
    <append xpath="/recipes">
        <recipe name="resourceClayLumpBundle" count="1" craft_time="10" craft_exp_gain="0" tags="learnable,packMuleCrafting">
            <ingredient name="resourceClayLump" count="10000"/>
        </recipe>
    </append>
</configs>
```

### Freischaltung via Art of Mining Vol. 5 (perkArtOfMiningPallets)
```xml
<configs>
    <append xpath="/progression/perks/book[@name='perkArtOfMiningPallets']/effect_group/passive_effect/@tags">,resourceClayLumpBundle</append>
</configs>
```
> **Kritisch:** xpath ist `/progression/perks/book` — das `/perks/` ist zwingend nötig!
> `RecipeTagUnlocked` matcht auf Tags die im Rezept stehen UND auf den Rezeptnamen.

### Aktive Bundles (OGClayAndSandBundle)
- `resourceClayLumpBundle` — 10.000× Clay → 1 Bundle
- `resourceCrushedSandBundle` — 10.000× Sand → 1 Bundle

---

## OGRAIDPVP Mod

- **Materialkette:** Carbon → Graphene → Titansteel
- **Werkstationen:** OGTechstation, OGChemicalProcessor, OGMaterialPrinter, OGCarbonProcessor, OGTitanForge
- **OGStainlessSteelBlock:** TextureId 446, 15.000 HP
- **OGGrapheneSteelfoamBlock:** TextureId 267, 20.000 HP
- **TitansteelRaidAuger:** Kerosin, random Stats, 30min Craft
- **Trader:** Acid 600–1200, Steel Polish + Titanfragment
- **Perk:** `perkOGAdvancedMaterials` (General Perks Tab, 5 Stufen, Kosten 1/2/3/4/5) via `RecipeTagUnlocked`
- Perk benutzt `effect_description` statt `display_entry` (`display_entry` nur für `crafting_skill`)

### Upgrade-Kette
`steelShapes` + 10× `resourceOGSteelPolish` = `OGStainlessSteelBlock` (nur Mason Nailgun!)
→ + 10× `resourceOGGrapheneSteelfoam` = `OGGrapheneSteelfoamBlock`

### Titanfragment
- Droppt 10% Chance bei `oreIronBoulder` und `oreLeadBoulder`

---

## OGMasterMason Mod

- **Mining Auger:** doppelter Tank + HarvestCount, GasCan
- **Nailgun:** 514 RPM, InstantUpgrade, InstantRepair
  - `allowed_upgrade_items` inkl. `resourceOGSteelPolish` + `resourceOGGrapheneSteelfoam`
- **Brush:** Range 60
- **Diamond Repair Kit:** 5× ForgedIron + 5× DuctTape + 1× Diamond
- Alle Tint: `#2c9efb`
- Reparatur **nur** mit Diamond Repair Kit

---

## OGStarterBundle Mod

Gibt Spielern beim Login:
Bicycle, 5× FirstAidKit, Splint, HuntingKnife, IronPickaxe, IronFireaxe, 2× IronShovel, PureMineralWater, SteakAndPotato, Pistol, 500× 9mmAmmo

- Patch: `entityclasses.xml` → `playerMale` `ItemsOnEnterGame` (SP+MP)

---

## OGDeerHunt Mod (aktiv, funktioniert)

Quest-Flow:
1. Phase 1: `RandomPOIGoto`
2. Phase 2: `RallyPoint`
3. GameEvent `og_spawn_deer` spawnt 3 Rehe (`animalStag` / `animalDoe`)
4. Phase 3: `FetchKeep` (`foodRawMeat` value=10)
5. Phase 4: `ReturnToNPC` + `InteractWithNPC`

- `gameevents.xml` mit `<configs>` / `<append>`
- Localization in allen 19 Sprachen vorhanden

---

## OGServerPanel

| Tab | Inhalt |
|-----|--------|
| 1 | Karte (kein Titel) |
| 2 | Rules (9 Zeilen) |
| 3 | Commands |
| 4 | Mods (11 Zeilen) |
| 5 | Support (Discord-Button) |

- Map: `pos="384,-383"` `width="660"` `height="660"`
- Banner: `pos="384,-57"` `width="768"` `height="114"`
- Max. 80 Zeichen pro Zeile, max. 10–11 Zeilen pro Tab
- Keine Leerzeilen zwischen Einträgen
- Schriftgröße Default (nicht ändern)
- Localization: English + German Spalte identisch

---

## Shapes="All" Blöcke (kritisch)

1. Brauchen `CustomIcon` auf Vanilla `shapes="All"` Block (z.B. `steelShapes`)
2. Texture muss `TextureId` aus `painting.xml` sein (nicht Paint-ID!)
   - Paint-ID 77 = TextureId 267
   - Paint-ID 144 = TextureId 446
3. Upgrade von `shapes="All"` braucht `remove` + `append`, nicht nur `append`
4. `allowed_upgrade_items` in Nailgun bestimmt welche Items für Upgrades verwendet werden

---

## Trader-Wissen

- `buy_markup=3` → EconomicValue × 3 = Preis
- **Trader IDs:** Joel=1, Jen=2, Bob=6, Rekt=7, Hugh=8, id=9
- Eigene Gruppe: `append` auf `/traders/trader_item_groups`
- Dann bei Trader: `append` auf `/traders/trader_info[@id='X']/trader_items[last()]`
- `traders.xml` xpath: `/traders/trader_item_groups/trader_item_group[@name='generalResources']`
- `count=` setzt Anzahl der angezeigten Items

---

## Quest-Wissen V2.5 / V2.6

### V2.5
- Nur `TurnIn` als `completiontype`
- Gültige Objectives: `RandomPOIGoto`, `RandomGotoNPC`, `RallyPoint`, `ClearSleepers`, `FetchKeep`, `ReturnToNPC`, `InteractWithNPC`, `StayWithin`
- Gültige Actions: `GameEvent`, `SpawnGSEnemy`, `UnlockPOI`
- **Kein** `EntityKill` / `ZombieKill` / `SpawnEnemy` in V2.5
- `append` auf `quest_list[@id='trader_X_quests']` crasht `PopulateQuestList` (ungeklärt)

### V2.6
- `EntityKill` ist valid
- `RallyPoint` mit `start_mode="Create"` valid, aber nur wenn Phase 1 (`RandomGotoNPC`) wirklich abgelaufen ist
- Wenn Quest per `GameEvent`/`AddQuest` gestartet wird während Spieler beim Trader → Phase 1 übersprungen → RallyPoint zeigt "---"
- Fix: Quest direkt in Phase 2 starten
- `activate_event` im RallyPoint = nur Kosmetik, kein Positionsmarker
- Buch-Quest-Flow: Buch lesen → GameEvent → AddItems(Block) → Block setzen → Buff → CallGameEvent → AddQuest

---

## loot.xml

- Root muss `<config>` sein (NICHT `<configs>`)
- Neue lootcontainer brauchen numerische id, z.B. `id="1900"`
- Kisten-Mechanismus: `DowngradeBlock` — verschlossene Kiste aufschlagen → geöffnete Version mit `Class=Loot` + `LootList`

---

## Werkstationen

- `Class=Workstation`, `CraftingAreaRecipes=StationsName`, `WorkstationWindow=workstation_workbench`
- **Modelle:**
  - Techstation = `utilityCartFullPrefab`
  - ChemProcessor = `controlPanelTop_01Prefab`
  - MaterialPrinter = `controlPanelBase_01Prefab`
  - CarbonProcessor = `utilityCartFullPrefab`
  - TitanForge = `woodBurningStovePrefab`
- `CustomIcon` ohne Tint = weißer Würfel mit Textur im Hintergrund

---

## Localization

- Alle Mods auf **Vanilla 19-Sprachen-Format**
- Pfad: `~/serverfiles/Mods/[ModName]/Config/Localization.txt`
- Übersetzte Mods: OGEmberStorm, OGGlassCandle, TMO Return of Log Spikes, Ultimate_Turrets_Pack, donovan-betterblades, MDAdvancedGenerator, Medical_conditions, TMO Lobby Ores, OGClayAndSandBundle

---

## Aktive OG-Mods (Stoffel)

OGAK47SabotAPCR, OGBiomeBlocks, OGChristmasStick, OGCombatKnife, OGCombatSilencer, OGDeerHunt, OGDesertVultureMod, OGEliteShotgunBarrel, OGEliteSniperBarrel, OGEmberStorm, OGGlassCandle, OGHighImpactMagazine, OGHighPerformanceRunningShoes, OGLandClaimBlocks, OGMarksmanRifleMod, OGMasterMason, OGMountainRangerMod, OGNerfExplosives, OGPistol47MagMod, OGPistolSpringFirerate, OGQuickHandsMod, OGRAIDPVP, OGServerPanel, OGServerPanelCustom, OGShockwaveBoots, OGSMG5Mod57AP, OGSniperEnduranceForegrip, OGSportsForegrip, OGStarterBundle, OGTalisman, OGWeaponToolsMods (vereint: OGGamerHivePatch, OGSuperWeapons, OGNerfSTWeapons, zzz_Glasma_BlessedMetal + weitere), OGx16Scope

**Dauerhaft entfernt:** OGTraderChests (obsolet)

---

## Aktive Drittanbieter-Mods

AEC-AIO BOSS, Bandits LootChest Expansion, Better Farming, BFT2020_BiomeStages, Bloque Electrico, CPPS-fixed, craftablePerkBooks, donovan-betterblades, donovan-craftableparts, GG_BunkerBusterIronBreakerCombined, GNS_BeautifulBases, Hydroponic Farming, Jakmeister999 HUD+LargeStacks V2.5, KHV2-3SlotForge, KHV2-96BBM, khzmusik_Trader_Progression, lootableEverything, Max Level Progression, MDAdvancedGenerator, Medical_conditions, MicksSpecialZombiesV1, More_General_Skills, More_SharedTrapXP, NamedDyes 2.3.9, NWG_DewCollectorMods, OcbStopFuelWaste, OGClayAndSandBundle, PaLoALo V1+V2, RoboticInbox, SCarRespawner, SKarmahRemains, SM_FasterSmelting, SM_MoreSmeltedResources, SNoAdminItems, Snufkin_Autominer, ST7D_Drones_Removed, TechFreqsFasterSmelting, TMO-Core+Mods, Ultimate_Turrets_Pack, V2-Fratracide Mushrooms, V2-SolarPowerCrafting, Wyr3d-NotJustForTheNight, zCloud HealingBeds+GlassJars, zLamsincreasedMODslot, ZZZ_CK_StickyNade, zzPaLoALo_Resize, Palladium_Glass-Jacked, PGz_UIAtlas_Icons, zzzScomar82_Hobacs, Xample_MarkersMod

**Dauerhaft entfernt (V2.6 inkompatibel / Community-Entscheid):** ChatLvl, AcceleratedAnnihilation, AidTheAfflicted, ContrabandCollection, DefendTheSafehouse, ExpeditedEradication, FeedTheFamished, QuestBalancer, Remove Vultures

---

## Geplante Mods (~/Dokumente/OMEGAGROUND/)

- ImprovedHordes
- Alle Waffen auf bessere Version + Ammo
- Epic-Chests + Lockpick
- Gold Repair Kit
- Lockpick System
- Titansteel Turrets
- Prestige Pickaxe
