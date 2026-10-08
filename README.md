# Rain Target

Educational Standard Edition calculator for a rain-affected limited-overs match. It is not the official ICC Duckworth-Lewis-Stern target. International matches use licensed DLS software. High-scoring games can differ by several runs, because the Professional Edition scales the resource curves with a match factor and always uses the proportional method.

## Run

No install and no server. Open `index.html` in a browser. Calculation stays on the device.

The page asks what the rain did: no rain, they came back with fewer overs, or the game ended. “They came back” uses overs left at the stop and at the restart, and the chase length becomes overs already bowled plus overs left when they came back. “Game ended” does not show a target still to chase. The big number is the par, and green or red says who won.

```bash
# from this folder, optional local preview
python3 -m http.server 8765
# then open http://localhost:8765
```

## What it uses

Resources are overs remaining and wickets lost together. A full 50-over innings with 10 wickets in hand is 100 percent. The app embeds the published Duckworth/Lewis Standard Edition over-by-over table (overs left 0 to 50, wickets lost 0 to 9). A part-over is linear between the two whole-over cells. Type 47.5 for 47 overs and 5 balls, which is 287 balls. 47.6 is rejected, because six balls is the next over. At 10 wickets lost, resources remaining are 0.

For a shortened match both sides face in full from the start, the start resource is the table value for that many overs and 0 wickets down. A 20-over start is 56.6, not 100.

## Formula

For each innings, resources available equal resources at the start minus resources lost in every interruption. An interruption’s loss is resources remaining at the stop minus resources remaining at the restart, using wickets lost at the stoppage. A delay before an innings starts counts. Several breaks add up. Resources are kept between 0 and the format start value.

S is team 1’s completed score. G50 defaults to 245 and is editable. Decimals are truncated, not rounded.

- If R2 < R1: target = floor(S × R2 / R1) + 1. Par, the tie score, is target − 1.
- If R2 = R1: target = S + 1. Par = S. The allocation is unchanged.
- If R2 > R1: target = S + floor((R2 − R1) × G50 / 100) + 1. Par = target − 1.

Live par if the match ended now: S × (resources already used by team 2) / R1. Resources used = R2 − resources still remaining. The tie score shown is that figure truncated. Par at the current overs is also listed for 0 to 9 wickets down.

If a minimum-overs figure is set and the chase allocation is shorter, and team 2 are not all out, the app says no result and does not name a winner.

## Checked examples

1. Team 1 made 250 in 50. Team 2 were 40 for 1 after 12 overs. Rain costs 10 overs. Restart with 28 overs and 1 wicket down.
   - Stop: 38 overs, 1 down = 82.0. Restart: 28 overs, 1 down = 68.8. Lost 13.2.
   - R1 = 100, R2 = 86.8. Target = floor(250 × 86.8 / 100) + 1 = 218. Par = 217, in 40 overs.
   - Resources used so far = 18. Live par = 45. 40 is behind par by 5.

2. Team 1 made 300 in 50. Chase reduced to 20 overs before it starts.
   - R1 = 100, R2 = 56.6. Lost 43.4. Target = floor(300 × 56.6 / 100) + 1 = 170. Par = 169.

A result is named only when the chase is more than 15 runs from the tie. Inside that, the card stays yellow and says too close. The Professional Edition can move a tie by about that much on a high score, enough to change the winner, as in the India 328 and Lord's 280 checks.

3. India 328 all out, Pakistan innings cut to 47 and finished at 311/7. Official target 305, Pakistan won by 7. Standard tie is 318.
4. India 280/5, England finished a 48.5-over chase at 270/8. Official target 271, a tie. Standard tie is 277. A tie on this page is yellow.
5. Sri Lanka 173/7, Zimbabwe 29/1 after 5 overs of 20, 1 wicket down. Official par 43. Standard par is 38. Rain had already revised the chase once, after 1 over, so one stop cannot reproduce that card.
