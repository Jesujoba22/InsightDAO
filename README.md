# InsightDAO

## Overview

I have engineered **InsightDAO**, a state-of-the-art behavioral analytics engine and reputation framework specifically architected for the Stacks blockchain. In the current landscape of decentralized governance, "one token, one vote" often fails to account for the qualitative contributions of community members. I built InsightDAO to bridge this gap by transforming on-chain actions—such as voting and proposal creation—into a multidimensional **Behavioral Score**.

This contract does not merely count actions; it analyzes patterns. I have integrated a **temporal decay model** to ensure that reputation is a "perishable" asset that must be maintained through consistent effort. Furthermore, I developed a **streak-based multiplier system** that rewards reliability, ensuring that the most dedicated participants gain outsized influence within the ecosystem. InsightDAO serves as the definitive source of truth for user engagement, offering a robust API for frontend dashboards and a reliable integration point for other smart contracts seeking to implement reputation-gated logic.

---

## Technical Architecture

I designed InsightDAO using a modular approach to state management and mathematical synthesis. The contract maintains a clear separation between raw participation metrics (`participation-stats`) and processed reputation data (`behavioral-score`).

### Key Design Principles

* **Lazy Calculation:** Decay and scores are updated only when needed, minimizing gas costs for the DAO.
* **Deterministic Tiering:** I established five distinct tiers (**Bronze** through **Diamond**) using a rigorous threshold-based logic.
* **Administrative Resilience:** I included an owner-controlled pause mechanism to safeguard the DAO during protocol upgrades.
* **Analytical Depth:** The contract tracks "Lifetime Peaks," allowing the community to recognize historical significance even if a user's current score has decayed.

---

## Comprehensive Function Documentation

### 1. Private Functions

I utilize these internal helpers to maintain encapsulation and ensure that complex logic—like decay and tiering—is handled consistently across all entry points.

* **`get-or-default-stats`**: I designed this to ensure the contract never traps or fails when querying a new user. It returns a zeroed-out state if no data exists.
* **`get-or-default-score`**: Similar to the stats helper, this provides a "None" tier and zero score for uninitialized principals, facilitating smooth onboarding.
* **`get-tier-for-score`**: This function contains the tier-boundary logic. I used a nested conditional structure to map the  to  score range into the five prestige tiers.
* **`calculate-decay`**: This is the engine of the temporal model. I programmed it to calculate the number of blocks elapsed since the last update, divide by the `decay-interval` ( blocks), and multiply by the `decay-rate` (). It is capped at the user's current score to prevent negative integers.

### 2. Public Functions (State-Changing)

These functions constitute the primary interface for users and automated governance bots to interact with the reputation engine.

* **`log-vote`**: This records a voting action. I implemented logic here to check if the user's activity is "consistent" (within a 144-block window). If it is, the `consistency-streak` increments; otherwise, it resets to .
* **`log-proposal`**: I designated this as a high-value interaction. It increments the `proposals-created` count. Like `log-vote`, it also triggers the global `active-users-count` variable if the user is new to the system.
* **`update-behavioral-score`**: This is the most critical function in the contract. I built it to perform a full synthesis of user data:
1. It calculates the base score from votes () and proposals ().
2. It applies a  multiplier if the user’s streak .
3. It updates the `lifetime-score-peak` and re-evaluates the user's `tier`.


* **`set-paused`**: I restricted this to the `contract-owner`. It allows for the immediate suspension of all score-modifying activities in the event of a discovered edge case or emergency.

### 3. Read-Only Functions

I optimized these functions for gasless querying, making them ideal for frontend integration and external contract checks.

* **`get-score`**: A straightforward lookup that returns the user's current score, tier, and last updated block.
* **`get-comprehensive-user-report`**: I spent significant time perfecting this function to provide a "dashboard-ready" payload. It returns a complex tuple including:
* **Current Status**: Real-time tier and activity status.
* **Activity Metrics**: A summary of total votes and proposals.
* **Projections**: I included logic to tell the user exactly how many blocks remain until their next decay penalty and how many points they need for a tier upgrade.
* **Comparative Analysis**: It calculates the DAO's average interaction rate and labels the user as "Above Average" or "Average or Below" relative to the community.



---

## Mathematical Model

The reputation score  is derived through the following logic, which I have formalized in the contract code:

Where:

*  = Total Votes
*  = Total Proposals
*  =  (Vote Weight)
*  =  (Proposal Weight)
*  =  if Streak , else 

---

## Contribution & Governance

I welcome the community to contribute to the evolution of InsightDAO. To maintain the integrity of the reputation engine, I require the following:

1. **Code Consistency**: Ensure all new logic follows the existing Clarity  naming conventions.
2. **Test-Driven Development**: Any change to the scoring weights must be accompanied by updated unit tests showing the impact on the Diamond tier distribution.
3. **Documentation**: Update the technical specifications if any constants (like `decay-rate`) are modified via governance.

---

## License

### MIT License

Copyright (c) 2026 InsightDAO Contributors

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

---
