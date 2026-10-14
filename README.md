<!-- solve-problem:32585ad6-c1c9-436a-9e79-47daa884810b -->
# Repeated base-fee manipulation with endogenous coalitions

A [Tangle Solve problem](https://solve.tangle.tools/problems/32585ad6-c1c9-436a-9e79-47daa884810b).

## What is the problem?

EIP-1559 is Ethereum's transaction fee mechanism. Each block has a protocol-computed base fee $b_t$ updated by a simple rule: $b_{t+1} = b_t(1 + d\cdot(g_t - g^{target})/g^{target})$, where $g_t$ is the gas used in block $t$ and $d \approx 0.125$ is a damping factor. A block producer (validator) chooses which transactions to include, hence effectively chooses $g_t$ within limits, and so can steer the base fee over time — e.g., keeping blocks underfull to drive the base fee down, then extracting surplus when demand is high.

The census problem: characterize the **long-run (repeated-game) equilibria** when producers can (i) trade block-production rights (e.g., via proposers selling block construction to builders, or rotating slots), and (ii) form or dissolve coalitions endogenously — no fixed cartel membership. The formal setting: an infinitely repeated game among a set $N$ of producers, stage game = EIP-1559 fee mechanism plus inclusion choice; players control less than 50% of stake individually but coalitions may form via transferable utility (side payments) and endogenous membership. Goals: (a) characterize the set of sustainable manipulation strategies and their profit as a function of coalition size and discount factor $\delta$; (b) design protocol modifications (change $d$, target adjustment, commitment rules) that are robust — i.e., make manipulation unprofitable or coalitions unstable. Ambiguity note: "trade block rights" could mean builder markets (mev-boost) or inter-validator slot sales; the most reasonable reading is both, modeled as transferable control of the stage-game action.

## Why does it matter?

EIP-1559 secures hundreds of millions of dollars in daily fees; if a minority coalition can profitably steer base fees, transaction-fee predictability — the mechanism's main promise — fails. A characterization would tell protocol designers whether the damping parameter or update rule needs changing and would inform Ethereum's builder/relay ecosystem, where block rights are already concentrated. It also connects to MEV mitigation and to the general theory of whether "incentive-compatible per-block" mechanisms survive repeated play.

## What's already known?

- Azouvi et al. (2023) showed single-producer and small-coalition base-fee manipulation is profitable with well under majority control: over multiple consecutive blocks a producer can lower the base fee and then extract higher priority fees / include withheld demand. They compute profit and time-to-profitability as functions of $d$ and demand elasticity.
- Prior theory (Roughgarden's transaction fee mechanism framework, 2020–21) established (approximate) incentive compatibility of EIP-1559 **myopically / one-block**, and strictly speaking only under exogenous inclusion; this is exactly the gap: one-block honesty does not preclude multi-block manipulation.
- Repeated-game tools (folk theorems, cartel sustainability, endogenous membership à la "coalition-proof" equilibria) exist in the classical literature but have not been applied with the specific EIP-1559 state variable and builder-market control structure.
- No published characterization of equilibrium manipulation with transferable block rights and endogenous coalitions; no stable mitigation proven. (Verify status — this is the open target.)

## What would count as solving it?

1. **Theorem (characterization):** For the repeated game with transferable block rights and endogenous coalition formation, characterize the set of equilibria / maximal sustainable manipulation profit $V^*(k, \delta, d)$ for coalition size $k < n/2$, including conditions under which the "honest" equilibrium is the unique one or manipulation is unsustainable.
2. **Theorem (lower bound / attack):** An explicit strategy profile showing manipulation profit bounded away from zero for specific $k, \delta$, with endogenous coalition stability (no profitable deviation by members or entrants).
3. **Construction (mitigation):** A modified update rule or commitment mechanism (e.g., different $d$, multi-block averaging, inclusion-commitment rules) with a proof that it makes manipulation unprofitable or the manipulating coalition unstable, at acceptable cost to fee responsiveness.
A full solution gives (1)+(3), or (2) matching (1).

## How to start

1. Read Azouvi et al. (2023) fully — their two-block/"split" manipulation strategies and profit formulas are the stage-game primitives; then Roughgarden's TFM chapter on EIP-1559 for the formal mechanism and the exact myopic-IC statement being generalized.
2. Model block-right trading: simplest formalization is transferable control — a coalition with expected share $\alpha$ of slots over a horizon $T$ (with $T$ needed since the base fee moves slowly, $d=1/8$). Derive optimal manipulation for a fixed coalition first; this largely re-derives/extends Azouvi with dynamic programming.
3. Layer on endogenous membership: use repeated cartel / endogenous coalition formation theory (e.g., dynamic coalition formation literature) to ask when the manipulating coalition is internally and externally stable; the key obstacle is that punishment requires non-members to fill blocks fully, which is individually rational for them, so deviation incentives may be weak.
4. For mitigation, analyze $d \to 1$ (faster adjustment) and note the known tradeoff: faster adjustment improves manipulation resistance but worsens fee volatility; quantify the frontier.

## Key references

- M. Azouvi, A. Owusu, L. Zamyatin, P. McCorry, W. J. Knottenbelt, *Base Fee Manipulation in Ethereum's EIP-1559 Transaction Fee Mechanism*, arXiv:2304.11478 (2023). https://arxiv.org/abs/2304.11478 — Quantifies profitable multi-block base-fee manipulation by producers (or small coalitions) holding far less than majority control; the direct precursor to this problem.
- T. Roughgarden, *Transaction Fee Mechanism Design* / *Transaction Fee Mechanism Design for the Ethereum Blockchain: An Economic Analysis of EIP-1559* (2020/2021, arXiv:2012.00854). https://arxiv.org/abs/2012.00854 — Defines the TFM solution concept (myopic, exogenous inclusion) and proves EIP-1559's approximate incentive compatibility within it; establishes the one-block horizon that this problem breaks.
- (verify) Literature on repeated games with endogenous coalition formation and cartel stability — no single canonical blockchain paper; classical references in industrial-organization cartel theory are the natural toolkit.

## Warnings

- The hardest modeling choice is what "trading block rights" means: mev-boost-style builder delegation vs. proposers acting in sequence without any trade. Results may differ qualitatively; state your formalization explicitly.
- Naive folk-theorem application fails: the base fee is a shared state variable, so deviations affect future payoffs of all players, not just stage payoffs — the game is not a standard repeated identical-interest-stage game.
- Do not assume manipulation requires majority; the 2023 attacks and the census explicitly note profitability below majority control — a "majority needed" claim would contradict known results.
- Watch for confusion between "incentive compatible" in Roughgarden's sense (myopic, exogenous inclusion) and dynamic collusion resistance; they are different properties and papers use the term loosely.

---
*`BCGT-000284` · flagged for review*

## Acceptance guidance

1. **Theorem (characterization):** For the repeated game with transferable block rights and endogenous coalition formation, characterize the set of equilibria / maximal sustainable manipulation profit $V^*(k, \delta, d)$ for coalition size $k < n/2$, including conditions under which the "honest" equilibrium is the unique one or manipulation is unsustainable.
2. **Theorem (lower bound / attack):** An explicit strategy profile showing manipulation profit bounded away from zero for specific $k, \delta$, with endogenous coalition stability (no profitable deviation by members or entrants).
3. **Construction (mitigation):** A modified update rule or commitment mechanism (e.g., different $d$, multi-block averaging, inclusion-commitment rules) with a proof that it makes manipulation unprofitable or the manipulating coalition unstable, at acceptable cost to fee responsiveness.
A full solution gives (1)+(3), or (2) matching (1).

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/32585ad6-c1c9-436a-9e79-47daa884810b?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: BCGT-000284.

- [Public source catalog](https://drewstone.github.io/problems/#/p/BCGT-000284)
