# DRAGON BALL SUPER CARD GAME — FUSION WORLD (Bandai) — investigación y notas del bot

## El juego

- **Juego:** DRAGON BALL SUPER CARD GAME **FUSION WORLD**, de **Bandai Card Games** (misma
  división que One Piece Card Game, Naruto CG, Gundam CG, Digimon CG). Es el TCG de Dragon
  Ball **activo y mundial** desde 2023 (sustituyó al viejo "DBS Card Game" serie B01–B25).
- **Web oficial:** dbs-cardgame.com/fw/en
- Al ser Bandai, se distribuye por las **mismas tiendas que One Piece** → la lista de tiendas
  se basa en la del bot de OP (`/op/config.json`) + los feeds de preventa del de Naruto.

## Líneas de producto y códigos de set (jul-2026)

| Código | Línea | Ejemplos |
|--------|-------|----------|
| **SB** | **MANGA BOOSTER** (special, arte manga de Toriyama, 40 aniversario DB) | **SB01**, **SB02** (nov-2025), SB03 (en camino) |
| FB | Booster Pack normal | FB07 Wish for Shenron, FB08 Saiyan's Pride, FB09 Dual Evolution, FB10 **-Cross Force-** (jun-2026), FB11 -Brightness of Hope- (otoño-2026) |
| ST | Story Booster (línea nueva) | **ST01** (21-ago-2026) |
| FS | Starter Deck (EX) | FS08–FS12 |
| — | Promotion Pack / Tournament / Championship | Promotion Pack Vol.1–6+, promos de torneo (STPFW) |
| — | Special / Premium boxes | Special Booster Box 2026 Vol.1 (all-foil alt-art) |

- Una **"case"** = cartón de booster boxes (normalmente 12 boxes). Algunas tiendas la listan
  como "case", "booster case", "sealed case", "case box" (TCG Corner tiene una colección
  entera: `dragon-ball-case-box`).

## Prioridad de avisos (lo que más interesa al usuario)

1. **🔥 MANGA BOOSTER (sets SB)** — **MÁXIMA prioridad**, cases y booster boxes. Se marca 🔥 y
   se muestra el **primero**. Keywords: `manga booster`, `special manga`, `sb01`..`sb05`.
2. **🚨 CASE / BOOSTER BOX** de cualquier set (`case`, `booster box`, `display`, `caja sellada`...).
3. **🎁 PROMOS** — Promotion Pack / tournament / championship / promocional.

La prioridad de **SITIO** (HIGH cada 5 min / MEDIUM cada 15 min) solo reparte el chequeo entre
workflows. La prioridad de **PRODUCTO** (las 3 de arriba) decide el marcado y el orden dentro
del aviso. Un manga booster/case NUEVO se notifica siempre, aparezca en la tienda que aparezca.

## Estrategia del bot

- **Baseline SILENCIOSO en la 1ª pasada:** Fusion World ya tiene catálogo, así que la primera
  vez se absorbe TODO lo existente sin avisar. A partir de ahí solo se notifica lo **NUEVO**
  (preventa de SB03, una case nueva, un promo nuevo…) y los **RESTOCKS** (agotado → vuelve).
- **Filtro de keywords:** `required_keywords` = `dragon ball` / `fusion world` (+ JP ドラゴンボール).
  `exclude_keywords` descarta **merch** (figuras, funko, tazas, tomos de manga, ropa…) y otros
  juegos Dragon Ball que NO son Fusion World (Heroes, Carddass, IC, Super Battle).
  > ⚠️ **NO** meter `manga` a secas en exclude: mataría el MANGA BOOSTER. Por eso el merch de
  > tomos se filtra por otras vías (en WooCommerce se busca `fusion world`, no `dragon ball`).
- **3 vías de cobertura por tienda:**
  1. **Colecciones de MANGA BOOSTER / cases por set** (UniverseTCG `fusionworld-sb01/sb02/sb03`,
     Kantocards `dragon-ball-manga-booster-01` / `booster-box-dragon-ball`, TCG Corner
     `dragon-ball-case-box`). Es la red principal para lo prioritario. HIGH.
  2. **Feeds de preventa / reservas / novedades** multi-juego (Madrid Norte, Pokejotta,
     SparkLeaf, TodoHits, CardZone, TCG Level, AllInTCG…), filtrados por dragon ball. HIGH.
  3. **Colecciones `dragon-ball` reales** de cada tienda Bandai (handles verificados vía
     `/collections.json`, jun-2026) + búsqueda `fusion world` en WooCommerce ES/PT y
     `dragon ball fusion` en PrestaShop ES. Cobertura amplia. HIGH/MEDIUM.

## Handles verificados (jun-2026)

- FreakCorp `dragon-ball-super-card-game` · TodoHits `dragon-ball` · CardZone `dragon-ball-tcg`
- La Escotilla `dragon-ball-super-card-game` · TCG Level `dragon-ball` · JJ Collection `dragon-ball-tcg`
- Kantocards `dragon-ball`, `booster-box-dragon-ball`, `dragon-ball-manga-booster-01`,
  `productos-especiales-dragon-ball` · Pokemillon `dragon-ball` · Otakura `dragon-ball-super`
- UniverseTCG por set: `fusionworld-sb01/sb02`, `fusionworld-st01`, `cross-force-fusion-world`
  (y muchos `fusionworld-fbXX/fsXX`)
- TCG Corner `dragon-ball-case-box`, `dragon-ball-fusion-world`, `dragon-ball-fusion-world-pre-order`,
  `-fullset` · CardOtaku `dragon-ball-super-card-game-fusion-world`
- Pixel and Block `dragonball-card-game` · Evolution TCG `dragon-ball-super-card-game`
- `allintcg` y `europetcg` NO tienen handle `dragon-ball` → se cubren por sus feeds de
  reservas/novedades.

## TODO / mantenimiento
1. Cuando se anuncie **SB03** (Manga Booster 03), confirmar su código y que ya está en
   `top_priority_keywords` (SB03 ya incluido). Ídem SB04/SB05.
2. Si UniverseTCG crea la colección real de SB03, el handle adivinado `fusionworld-sb03` se
   auto-activa (hasta entonces da 404 → warning, es normal).
3. Revisar `high_value_keywords` si alguna tienda nombra las cases de otra forma.

## Uso
```bash
python3 setup_telegram.py            # crea/valida bot, detecta chat_id, manda test
                                     # y te imprime los valores para los Secrets
# Probar en local (el token NUNCA va en config.json — se pasa por env):
export TELEGRAM_BOT_TOKEN='...'; export TELEGRAM_CHAT_ID='...'
python3 monitor.py                   # 1 pasada, todas las tiendas
python3 monitor.py --priority high   # solo manga booster / preventas / cases
python3 monitor.py --loop            # bucle local cada 15 min
```
Despliegue = GitHub Actions (3 workflows: monitor 15 min, monitor-high 5 min, heartbeat 23:15 UTC).
Secrets del repo necesarios: `TELEGRAM_BOT_TOKEN`, `TELEGRAM_CHAT_ID`.

> **Primera pasada = baseline silencioso.** No esperes avisos en la primera ejecución: se
> absorbe el catálogo actual. A partir de ahí, cada listado nuevo (preventa de manga booster,
> case, promo) y cada restock te llega a Telegram.
