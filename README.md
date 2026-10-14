# BANGLAPLAN

**A Benchmark for Constraint-Based Planning and Decision-Making in Bangla**

BANGLAPLAN contains **1,000 planning and decision-making problems written directly in Bangla** and grounded in everyday life in Bangladesh. Each problem asks a model to build a plan (a schedule, route, allocation, ranking, and so on) that satisfies explicit constraints on time, cost, capacity, and order, or to recognize that **no feasible plan exists**.

Every problem has a single canonical gold answer in a shared JSON format, so it can be scored by deterministic exact match without an LLM judge.

---

## At a glance

| | |
|---|---|
| Problems | 1,000 |
| Task families | 10 (10 problem types each, 10 instances per type) |
| Feasible / infeasible | 744 / 256 |
| Difficulty | 300 easy, 300 medium, 400 hard |
| Language registers | everyday (500), formal (200), code-mixed Bangla–English (200), colloquial (100) |
| Answer types | 8 |
| Output format | One-element JSON array |

---

## Repository structure

```
benchmark-data/
├── part-1/
│   ├── F01.json
│   ├── F02.json
│   ├── ...
│   └── F10.json
├── part-2/
│   ├── F01.json
│   ├── ...
│   └── F10.json
└── part-3/
    ├── F01.json
    ├── ...
    └── F10.json
```

The three parts together form the full benchmark of 1,000 instances. Each part has one file per task family (`F01`–`F10`), and each file is a JSON array of instances. Every instance has a unique `instance_id`, so parts can be merged safely.

---

## Task families

| ID | Family | Example problem types |
|---|---|---|
| F01 | Eligibility | scholarships, exams, leave, mobile-money account rules |
| F02 | Procedures | NID correction, passport renewal, land mutation |
| F03 | Appointments | clinics, diagnostics, vaccination, biometrics, banks, legal aid |
| F04 | Journeys | Eid travel, launches and ferries, commutes, hospital referrals |
| F05 | Rosters | exams, invigilation, garment shifts, weddings |
| F06 | Sequencing | recipes, construction, crop calendars, document chains |
| F07 | Allocation | hospital beds, relief rice, irrigation, Qurbani meat, load-shedding |
| F08 | Procurement | haat shopping, Iftar supplies, tenders, courier assignment |
| F09 | Logistics | port unloading, ferry berths, waste collection, pumping |
| F10 | Emergency | cyclone evacuation, triage, ambulance dispatch |

## Answer types

| Answer type | Count | `verified_answer` content |
|---|---|---|
| schedule | 290 | `actions`: action, start, end, location |
| allocation | 160 | `allocations`: resource, recipient, amount |
| ordered steps | 140 | `steps`: action, start, end |
| selection | 110 | `items`: item, quantity, packs; `total_cost_bdt` |
| classification | 100 | one chosen option ID |
| route | 100 | `legs`: service, from, to, mode, dep, arr, cost_bdt; `total_cost_bdt` |
| matching | 60 | `pairs`: left, right |
| ranking | 40 | `ranking`: ordered list of IDs |

---

## Instance format

Each instance looks like this (prompt shortened):

```json
{
  "instance_id": "BP-F03-T021-001",
  "family_id": "F03",
  "type_id": "T021",
  "answer_kind": "schedule",
  "difficulty": "easy",
  "status": "FEASIBLE",
  "title_en": "Specialist appointment planning",
  "language_style": "everyday",
  "presentation_format": "prose",
  "reference_datetime": "2026-12-07T08:00:00+06:00",
  "timezone": "Asia/Dhaka",
  "gold_prompt": "রেফারেন্স সময়: ৭ ডিসেম্বর ২০২৬, সকাল ৮টা; ...",
  "gold_response": [
    {
      "is_feasible": true,
      "verified_answer": {
        "actions": [
          {"action": "referral_pickup", "start": "2026-12-07T09:00:00+06:00", "end": "2026-12-07T09:20:00+06:00", "location": "upazila_hc"},
          {"action": "cardio_slot",     "start": "2026-12-07T12:30:00+06:00", "end": "2026-12-07T13:10:00+06:00", "location": "cardio"}
        ]
      }
    }
  ]
}
```

### Fields

| Field | Description |
|---|---|
| `instance_id` | Unique ID: `BP-<family>-<type>-<instance>` |
| `family_id` | Task family, `F01`–`F10` |
| `type_id` | Problem type, `T001`–`T100` |
| `answer_kind` | Answer schema (see table above) |
| `difficulty` | `easy`, `medium`, or `hard` |
| `status` | `FEASIBLE` or `INFEASIBLE` |
| `title_en` | Short English title of the problem type |
| `language_style` | Register: `everyday`, `formal`, `colloquial`, or `codemix` |
| `presentation_format` | How facts are presented, e.g. `prose`, `bullets`, `message`, `notice` |
| `reference_datetime` | The "now" of the problem, ISO-8601 with `+06:00` |
| `timezone` | Always `Asia/Dhaka` |
| `gold_prompt` | The full Bangla prompt, including the required output format |
| `gold_response` | The canonical gold answer |

Infeasible instances have the gold answer:

```json
[{"is_feasible": false}]
```

---

## How problems are built

- **Explicit constraints.** Each prompt states all entities, quantities, time windows, and rules. Durations, travel times, and opening hours are given, not assumed.
- **Unique answers.** Objectives are applied in a stated lexicographic order, followed by an explicit tie-breaking rule, so each feasible problem has exactly one correct answer.
- **Near-boundary infeasibility.** Infeasible variants change one binding quantity, window, capacity, or rule just enough that no valid plan remains.
- **Distractors.** Some prompts contain irrelevant facts or non-binding preferences that do not affect the gold answer.
- **Linguistic variation.** Numbers appear as Bangla digits, English digits, or words. Outputs always use ASCII digits and ISO-8601 timestamps.
- **Difficulty.** Determined by the count *k* of binding constraints and supporting facts: easy (*k* ≤ 10), medium (10 < *k* ≤ 20), hard (*k* > 20).
- **Validation.** Problems were drafted by native Bangla speakers with language-model assistance, solved independently by a second annotator without seeing the gold answer, and then checked once more by a model, with every disagreement resolved by humans.

All names, places, offices, and rules in the prompts are **fictional**, and each prompt says so.

## Disclaimer

The rules and facts in each problem apply only to that fictional problem. They are not the current policies of any real institution.

## License

The BanglaPlan benchmark data is released under the
[Creative Commons Attribution 4.0 International License (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/).
You may share and adapt the data for any purpose, provided you give appropriate credit
by citing the paper.