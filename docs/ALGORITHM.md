# Algorithm and mathematical model

## 1. Sector analysis

For each of the 37 possible starting pockets, the engine evaluates a circular window of 12 or 13 consecutive wheel pockets.

A sector score may combine:

- direct hits inside the window;
- recency weights, with newer visible spins weighted slightly more;
- a small influence from neighbouring wheel pockets;
- stable tie-breaking based on ranked central pockets.

The winning window is the highest-scoring observed-history sector.

This score describes the supplied sample. It is not a claim that the sector has a greater probability on the next independent spin.

## 2. Candidate bets

Each candidate is represented as:

```ts
type RouletteBet = {
  id: string;
  type: "straight" | "split";
  numbers: number[];
  chips: number;
};
```

- A straight candidate contains one target number.
- A split candidate contains exactly two table-adjacent numbers.
- Illegal physical-neighbour pairs are discarded before optimisation.
- Configurable zero splits are loaded from casino rules.

## 3. Optimisation priorities

The optimiser uses a small exhaustive search because the candidate set is bounded.

Priority order:

1. Generate only legal bets.
2. Stay within the maximum chip count.
3. Respect the requested pleno/caballo composition where possible.
4. Maximise unique target-sector coverage.
5. Minimise coverage outside the sector.
6. Penalise accidental overlap.
7. Prefer central or higher-scoring pockets as a tie-breaker.
8. Explain impossible requests and return the closest valid option.

### Equal Coverage

Maximises unique target coverage while keeping effective stake per covered number as even as possible. Overlaps are penalised.

### Weighted Centre

Covers the broader sector but permits deliberate overlap or extra stake on central/high-scoring pockets. Deliberate weighting must be visible to the user.

## 4. Probability

A fair European wheel has 37 pockets. If a ticket covers `N` unique numbers:

```
P(any covered result) = N / 37
```

Examples:

| Unique pockets | Probability |
|---:|---:|
| 9 | 24.32% |
| 12 | 32.43% |
| 13 | 35.14% |

This is coverage probability, not a prediction derived from spin history.

## 5. Returns

For a bet with stake `s` chips:

- straight gross return on a hit: `36 × s` (35:1 plus returned stake);
- split gross return on a hit: `18 × s` (17:1 plus returned stake).

For each possible winning number `n`:

```
Gross(n) = sum of gross returns for all bets containing n
Net(n)   = Gross(n) - total ticket stake
```

The result can vary across covered numbers because mixed tickets and intentional overlaps do not pay uniformly.

The ticket should therefore report:

- probability of any covered result;
- best-case net;
- lowest net among covered results;
- average net conditional on a covered result;
- fixed loss when an uncovered number lands.

## 6. Session exposure

```
Maximum planned exposure = ticket cost × maximum planned spins
```

Confirmation is blocked when maximum planned exposure exceeds the entertainment budget. Limits cannot automatically increase after losses.
