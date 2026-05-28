# PlusTaal — Content Authoring Brief

You are helping write Dutch grammar exercise content for a personal learning app called PlusTaal. This prompt gives you everything you need. Read it carefully, then I will tell you which tile to write content for.

---

## The project

PlusTaal is a personal Dutch grammar app for a B2 learner. The wall of the app is 60 tiles, each tile a sub-theme within one of 13 grammar topics. Tapping a tile opens four sequential exercise levels of increasing difficulty:

- **Level 1: Recognition** — correct / incorrect judgement on Dutch sentences (7 items)
- **Level 2: Transformation** — rewrite a Dutch sentence applying the rule (7 items)
- **Level 3: Simple translation** — English to Dutch, one targeted construction (5 items)
- **Level 4: Complex translation** — English to Dutch, multiple constructions in one sentence (5 items)

Content is stored as JSON. Each tile = one JSON file in `/content/`.

---

## The user

- British, in her mid-fifties, has lived in Amsterdam for 30+ years
- Works as an air traffic controller — used to high-precision language
- Passive Dutch is strong (understands native speakers, reads fluently). Active production is weaker — she hesitates, makes specific recurring errors
- Goal: close the gap before a B2 conversation course
- Tone preference: warm but precise, no over-explaining, treat her as an intelligent adult who has thought hard about Dutch already

### Her recurring errors (target these subtly across many tiles, not just Foutjesgids)

- Inversion after time adverbs ("Gisteren ik ben naar..." → wrong, should be "Gisteren ben ik naar...")
- "Enige" spelling (often writes "eenige")
- "Worden" vs "zijn" in passive (uses "is" where the action emphasis needs "wordt", or vice versa)
- Voltooid deelwoord vs infinitief in dubbele infinitief (writes "ik heb het willen gewild" instead of "ik heb het willen")
- 't kofschip rule for past participles and imperfectum (wrong consonant choice)

---

## The JSON schema

Every tile file looks exactly like this:

```json
{
  "zone": "<topic_short_id>",
  "zoneName": "<Topic display name>",
  "tiles": [
    {
      "id": "<topic>-<n>-<subtheme>",
      "theme": "<Subtheme in Dutch>",
      "themeEnglish": "<Subtheme in English with brief explanation>",
      "recognitionQuestion": "<The question shown above the Correct/Niet correct buttons in Level 1, e.g. 'Is this use of the imperfectum correct?'>",
      "levels": {
        "1": [ /* 7 Level 1 items */ ],
        "2": [ /* 7 Level 2 items */ ],
        "3": [ /* 5 Level 3 items */ ],
        "4": [ /* 5 Level 4 items */ ]
      }
    }
  ]
}
```

---

## Reference example — the existing er-1-plaatsbijwoord tile

This is the canonical example. Match its style, length, and quality. Read it carefully to understand the bar.

```json
{
  "zone": "er",
  "zoneName": "Er",
  "tiles": [
    {
      "id": "er-1-plaatsbijwoord",
      "theme": "Er als plaatsbijwoord",
      "themeEnglish": "Er as locative adverb (referring to a place)",
      "recognitionQuestion": "Is this use of 'er' correct?",
      "levels": {
        "1": [
          {
            "type": "recognition",
            "prompt": "Ik ben gisteren naar Utrecht geweest. Ik ben er drie uur gebleven.",
            "correctUse": true,
            "explanation": "Correct. 'Er' here refers back to Utrecht — a clear locative use replacing 'daar' or 'in Utrecht'."
          },
          {
            "type": "recognition",
            "prompt": "We gaan naar het strand. Wil je daar mee?",
            "correctUse": false,
            "explanation": "Niet correct. With directional verbs like 'gaan' you need 'er…heen' or 'daarheen', not just 'daar mee'. Correct: 'Wil je er mee naartoe?' or 'Wil je mee daarheen?'"
          }
        ],
        "2": [
          {
            "type": "transformation",
            "prompt": "Ik heb tien jaar in Rotterdam gewoond.",
            "task": "Rewrite using 'er'.",
            "acceptedAnswers": [
              "Ik heb er tien jaar gewoond.",
              "Ik heb daar tien jaar gewoond."
            ],
            "preferred": "Ik heb er tien jaar gewoond.",
            "explanation": "'Er' replaces 'in Rotterdam' and sits right after the auxiliary verb 'heb'. The time phrase 'tien jaar' follows, then the participle 'gewoond' at the end. 'Daar' would also be acceptable, with slight emphasis."
          }
        ],
        "3": [
          {
            "type": "translation",
            "prompt": "I've been there twice.",
            "modelAnswer": "Ik ben er twee keer geweest.",
            "alternatives": ["Ik ben daar twee keer geweest."],
            "note": "Word order: 'twee keer' sits between 'er' and 'geweest'. 'Daar' is also acceptable with slight emphasis."
          }
        ],
        "4": [
          {
            "type": "translation",
            "prompt": "If I'd known you were coming, I would have stayed there longer.",
            "modelAnswer": "Als ik had geweten dat je kwam, was ik er langer gebleven.",
            "alternatives": [
              "Als ik had geweten dat je zou komen, was ik er langer gebleven.",
              "Als ik geweten had dat je kwam, was ik er langer gebleven."
            ],
            "note": "Plusquamperfectum in the 'als' clause ('had geweten'), conditional past in the main clause ('was…gebleven'). 'Er' refers back to the implied location. Word order: 'er langer' before 'gebleven'."
          }
        ]
      }
    }
  ]
}
```

The above shows abbreviated levels — your actual output needs 7 Level 1, 7 Level 2, 5 Level 3, 5 Level 4 items.

---

## Per-level writing guidelines

### Level 1 — Recognition (7 items)

Each item: a Dutch sentence using the target construction either correctly or incorrectly. User taps Correct / Niet correct.

- Mix: 4-5 correct uses, 2-3 incorrect uses
- Incorrect ones should reflect REAL learner errors (not random nonsense)
- Vary sentence type: statements, questions, with time adverbs, in perfectum, in present, etc.
- Vary length: some short, some longer
- Explanation should TEACH, not just say "wrong" — explain why, give the corrected form when relevant
- Keep explanations 30-60 words
- Set the tile-level `recognitionQuestion` field to name what's being judged — e.g. "Is this use of the imperfectum correct?", "Is this passive construction correct?". This focuses the learner on the specific feature. If omitted, the app falls back to a generic "Is this correct?"

### Level 2 — Transformation (7 items)

Each item: a Dutch sentence + an instruction to rewrite it applying the target construction. User types the rewrite.

- The `task` field is the per-question instruction (e.g. "Rewrite using passive voice", "Rewrite as a question", "Rewrite using a bijzin")
- `acceptedAnswers` should include 2-4 valid variants (word order alternatives, pronoun variants like 'we'/'wij', synonym variations)
- `preferred` is the most natural single answer (shown if user gets it wrong)
- CRITICAL: vary the tasks so the answer isn't always the same word. If every question's transformation produces the same target word, the exercise is broken. Mix tasks so the user has to think
- 30-60 word explanation

### Level 3 — Simple translation (5 items)

Each item: an English sentence to translate to Dutch. Tests ONE construction.

- English sentence: 6-12 words, natural English
- `modelAnswer`: the most natural Dutch
- `alternatives`: 1-3 acceptable variants
- `note`: brief teaching note about word order, alternative forms, or common pitfalls (40-80 words)

### Level 4 — Complex translation (5 items)

Like Level 3 but each sentence requires MULTIPLE constructions in one go: the tile's topic PLUS at least one other (different tense, conjunction, modal, word order rule, dubbele infinitief, etc.). This is the real B2 muscle.

- English sentence: 10-20 words
- Same format as Level 3
- The `note` should identify all the constructions present and explain how they interact
- These should genuinely stretch even a strong B2 learner

---

## General quality bar

- **Real Dutch as spoken/written by natives.** Amsterdam/standard Dutch, not textbook stilted, not Flemish. Read your sentences aloud — would a native say this?
- **Multiple acceptable answers always.** Translation has variation. List every reasonable variant.
- **Cultural relevance where natural.** Dutch contexts: bakkie koffie, naar de markt, Sinterklaas, terrasje pakken, fietsen langs de gracht. Don't force it but let it appear.
- **Avoid the trivial-answer trap.** If every Level 2 question's correct answer contains the exact same word, the exercise is broken (the user can just type that word every time). Mix tasks so the response varies.
- **Subtle injection of recurring errors.** Where it fits naturally, include Level 1 incorrect-use examples that mirror her specific mistakes (inversion after time adverb, worden/zijn passive confusion, etc.). Don't force it; let it appear authentically.
- **Explanations teach.** Every wrong answer is a teaching moment. Every right answer's explanation should also reinforce the rule. Don't be lecturing — be precise.

---

## The 13 topics with suggested tile breakdown

The user can tweak names; these are starting points. ~60 tiles total.

### 1. Imperfectum vs perfectum (5 tiles)
- imperfectum-1-dagelijkse-routine
- imperfectum-2-reizen
- imperfectum-3-werk
- imperfectum-4-weekend
- imperfectum-5-eten-koken

### 2. Plusquamperfectum (4 tiles)
- plusquamperfectum-1-sequentieel
- plusquamperfectum-2-spijt-en-regret
- plusquamperfectum-3-indirecte-rede
- plusquamperfectum-4-conditioneel-verleden

### 3. Passief (5 tiles)
- passief-1-nieuwsberichten
- passief-2-recepten-en-processen
- passief-3-officieel-taalgebruik
- passief-4-worden-vs-zijn
- passief-5-passief-perfectum

### 4. Modale werkwoorden + dubbele infinitief (5 tiles)
- modaal-1-kunnen
- modaal-2-moeten
- modaal-3-mogen-en-willen
- modaal-4-dubbele-infinitief
- modaal-5-modaal-in-bijzin

### 5. Er (5 tiles — er-1 already done)
- er-1-plaatsbijwoord ✓ DONE — use as reference
- er-2-er-plus-voorzetsel (denk er aan, etc.)
- er-3-er-plus-getal (ik heb er drie)
- er-4-er-als-plaatshouder (er is, er zijn — dummy subject)
- er-5-er-gemengd-en-foutjes

### 6. Werkwoord + voorzetsel combinaties (5 tiles)
- vwvz-1-denken-aan-praten-over
- vwvz-2-houden-van-zorgen-voor
- vwvz-3-wachten-op-rekenen-op
- vwvz-4-zich-verheugen-op-zich-ergeren-aan
- vwvz-5-gemengd

### 7. Inversie (4 tiles)
- inversie-1-tijd-vooraan
- inversie-2-plaats-vooraan
- inversie-3-na-voegwoord
- inversie-4-in-vraag

### 8. Bijzinnen + werkwoord achteraan (5 tiles)
- bijzin-1-omdat-want
- bijzin-2-dat-of
- bijzin-3-hoewel-ondanks
- bijzin-4-terwijl-toen-voordat
- bijzin-5-bijzin-met-modaal

### 9. Om...te (4 tiles)
- omte-1-doel
- omte-2-na-werkwoorden
- omte-3-na-adjectieven
- omte-4-met-ontkenning

### 10. Zouden + conditional (4 tiles)
- zouden-1-hypothetisch
- zouden-2-beleefd-verzoek
- zouden-3-advies
- zouden-4-indirecte-rede

### 11. Reflexieve werkwoorden (4 tiles)
- reflexief-1-echt-reflexief
- reflexief-2-optioneel-reflexief
- reflexief-3-lichaamsverzorging
- reflexief-4-emoties

### 12. Scheidbare werkwoorden (5 tiles)
- scheidbaar-1-aankomen-vertrekken
- scheidbaar-2-opstaan-uitgaan
- scheidbaar-3-meegaan-meekomen
- scheidbaar-4-uitnodigen-uitzoeken
- scheidbaar-5-perfectum-scheidbaar

### 13. Foutjesgids (5 tiles — drilling recurring errors)
- foutjes-1-inversie-na-tijd
- foutjes-2-enige-spelling
- foutjes-3-worden-zijn-passief
- foutjes-4-voltooid-vs-infinitief-dubbel
- foutjes-5-kofschip

Total: ~60 tiles.

---

## How to use this prompt

Paste this entire brief at the start of a new conversation. Then for each tile, give a clear instruction:

> "Write the JSON content for `imperfectum-1-dagelijkse-routine`. The zoneName is 'Imperfectum vs Perfectum'. The theme is 'Dagelijkse routine'. The themeEnglish is 'Daily routines — choosing between imperfectum (was doing) and perfectum (have done)'."

You then output ONE complete JSON file matching the schema. Just the JSON in a code block, no surrounding chat.

I will save your output as `content/<id>.json` in the project.

If a topic doesn't quite work as I've specified it, push back — your judgement on Dutch is the priority, not these labels.

Take care with the Dutch. Native-feeling, varied, idiomatic. This is what I'll be practising with for months.
