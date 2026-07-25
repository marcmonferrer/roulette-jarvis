# Algorithm and mathematical model

## 1. Sector analysis

For each of the 37 possible starting pockets, the engine evaluates a circular window of **12, 14, or 16 consecutive wheel pockets**.

The score combines direct hits, light recency weighting, and small neighbour influence. The highest-scoring window describes the supplied history; it does not change the probability of the next independent spin.

| Sector size | Physical-wheel coverage |
|---:|---:|
| 12 | 32.43% |
| 14 | 37.84% |
| 16 | 43.24% |

## 2. Legal bet candidates

The engine generates candidates allowed by the selected betting style:

- **Pleno:** one number.
- **Caballo:** two table-adjacent numbers.
- **Tercio:** one legal three-number row.
- **Cuarta:** one legal four-number corner.

Physical neighbours on the wheel are never assumed to be a legal caballo on the table.

## 3. Full-sector optimisation

There is no maximum-chip constraint. The optimiser adds legal bets until **every number in the selected wheel sector is covered**.

Priority order:

1. Generate only legal table bets.
2. Cover every target-sector pocket.
3. Maximise fresh target coverage per chip.
4. Minimise numbers covered outside the sector.
5. Penalise accidental overlaps.
6. Prefer central sector pockets as a tie-breaker.
7. Use plenos whenever necessary to guarantee completion.

Each generated bet uses one chip. The ticket’s chip count is therefore an output of the optimiser.

## 4. Probability

If a ticket covers `N` unique numbers:

```
P(any covered result) = N / 37
```

The unique set can be slightly larger than the selected physical sector when a legal table shape necessarily extends beyond it. This is coverage probability, not a prediction.

## 5. Cost and returns

The user selects a chip value of **€5 or €10**.

```
Ticket cost = chips required × chip value
```

Gross return per winning chip:

- pleno: `36 × chip value`;
- caballo: `18 × chip value`;
- tercio: `12 × chip value`;
- cuarta: `9 × chip value`.

For each possible winning number `n`:

```
Gross(n) = sum of returns for all winning bets containing n
Net(n)   = Gross(n) - total ticket cost
```

Because mixed tickets and overlaps can pay differently, the app reports the best net, lowest net among covered results, and the full loss when an uncovered number lands.
