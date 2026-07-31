# Taxonomy Patch for `index.html` TAXONOMY

**Additions only. Nothing is removed** — removing codes causes the history/redo-deck drift you were warned about. `SUP` and `URD` stay exactly as they are.

2 new subjects, 77 new topic codes. Covers all 183 Testbook chapters with no `MISC` fallbacks.

## Order of operations (this matters)

1. Apply the patch below to `TAXONOMY` in `index.html`.
2. Update the external authoring prompt (the comment at `index.html:629`).
3. **Merge `feature/cloud-sync-english-ui` → `main`.** The hosted app runs `main`; until it's merged, any set using a new code is rejected wholesale by `normalizeSet()`.
4. Only then generate practice sets.

## 1. New subjects

```js
TEACH: {
  name: 'शिक्षण कौशल',
  topics: {
    METH: 'शिक्षण विधियाँ एवं कौशल',
    PRIN: 'शिक्षण के सिद्धांत',
    SOC: 'समकालीन भारतीय समाज एवं प्रारंभिक शिक्षा',
    INC: 'समावेशी शिक्षा',
    EVAL: 'आकलन एवं मूल्यांकन',
    READ: 'प्रारंभिक पठन कौशल',
    SCHM: 'शैक्षिक प्रबंधन एवं प्रशासन',
    MISC: 'सामान्य / अन्य',
  }
},
```

```js
LIFE: {
  name: 'जीवन कौशल, प्रबंधन एवं अभिवृत्ति',
  topics: {
    ETH: 'व्यावसायिक आचरण एवं नैतिकता',
    MOT: 'प्रेरणा',
    ROLE: 'शिक्षक की भूमिका',
    CONST: 'संवैधानिक एवं मानवीय मूल्य',
    PUN: 'पुरस्कार एवं दंड का प्रभावी प्रयोग',
    MISC: 'सामान्य / अन्य',
  }
},
```

`TEACH` and `LIFE` are 20 marks between them and had no home before. Note the deliberate overlap with `SUP` — see the warning at the bottom.

## 2. New topics on existing subjects

### `HIN` — add 13 topics

```js
KVIS: 'क्रिया विशेषण',
AVY: 'अव्यय एवं निपात',
KRAM: 'क्रम व्यवस्थापन',
RIKT: 'रिक्त स्थानों की पूर्ति',
DESH: 'देशज एवं विदेशज शब्द',
RUDH: 'रूढ़, यौगिक एवं योगरूढ़ शब्द',
BHIN: 'भिन्नार्थक शब्द',
ANRTH: 'अनेकार्थक शब्द',
CHND: 'छंद',
VART: 'वर्तनी',
TAT: 'तत्सम एवं तद्भव शब्द',
KAVI: 'रचना एवं रचनाकार',
DEV: 'विराम चिह्न एवं देवनागरी लिपि',
```

### `ENG` — add 4 topics

```js
PROV: 'Proverbs',
VERB: 'Verb',
OWS: 'One Word Substitution',
POS: 'Parts of Speech',
```

### `SKT` — add 5 topics

```js
SAMAN: 'समानार्थी शब्द',
VIPR: 'विपरीतार्थक शब्द',
UPS: 'उपसर्ग',
AVY: 'अव्यय',
PAD: 'पद्यांश',
```

### `MATH` — add 4 topics

```js
OPS: 'गणितीय संक्रियाएँ',
FACT: 'गुणनखंड',
UNIT: 'ऐकिक नियम',
VOL: 'आयतन',
```

### `SCI` — add 5 topics

```js
DAILY: 'दैनिक जीवन में विज्ञान',
MOT: 'गति',
MEAS: 'दूरी एवं मापन',
RES: 'प्राकृतिक संसाधन',
STATE: 'पदार्थ की अवस्थाएँ',
```

### `SST` — add 7 topics

```js
EARTH: 'पृथ्वी की संरचना',
RIV: 'नदियाँ',
MTN: 'पर्वत',
CONT: 'महाद्वीप',
OCEAN: 'महासागर',
SOLAR: 'सौर मंडल एवं आकाशीय पिंड',
HERIT: 'सांस्कृतिक धरोहर',
```

### `EVS` — add 2 topics

```js
LIFE: 'सजीव जगत',
ROAD: 'सड़क सुरक्षा',
```

### `CDP` — add 2 topics

```js
NEEDS: 'अधिगम आवश्यकताओं की पहचान',
FACT: 'विकास को प्रभावित करने वाले कारक',
```

### `GK` — add 4 topics

```js
PLACE: 'महत्वपूर्ण स्थल',
PERS: 'महत्वपूर्ण व्यक्तित्व',
ART: 'भारतीय कला एवं संस्कृति',
UPCUR: 'उत्तर प्रदेश समसामयिक',
```

### `REAS` — add 10 topics

```js
ASSN: 'कथन एवं कारण',
BIN: 'बाइनरी लॉजिक',
INEQ: 'कूट असमिकाएँ',
CRIT: 'आलोचनात्मक तर्क',
PUZ: 'पहेलियाँ',
SYMB: 'संकेत एवं चिह्न',
DI: 'आँकड़ा विश्लेषण',
GRP: 'समूहन एवं चयन',
INF: 'निष्कर्ष',
LSER: 'अक्षर श्रृंखला',
```

### `COMP` — add 7 topics

```js
EDU: 'शिक्षा में सूचना प्रौद्योगिकी',
CLASS: 'कक्षा शिक्षण में IT',
SCHM: 'विद्यालय प्रबंधन में IT',
OER: 'ओपन एजुकेशनल रिसोर्सेज',
SMART: 'शैक्षिक ऐप एवं स्मार्टफोन',
DIGI: 'डिजिटल शिक्षण सामग्री',
ELRN: 'ऑनलाइन कक्षा एवं ई-लर्निंग',
```

## 3. `SUP` is now redundant — stop authoring to it

`SUP` was a catch-all: its `TEACH`, `CLASS`, `PLAN`, `EVAL`, `ICT`, `NEP` topics now duplicate the new `TEACH` subject and `COMP`. Leave the code in place so old history keeps resolving, but route all new questions to `TEACH` / `LIFE` / `COMP`. If both are used, weakness analysis splits the same topic across two buckets and under-reports it.

## 4. Authoring prompt change

The prompt must state:

- Use `subjectId` + `topicId` from the updated TAXONOMY only; never invent codes.
- `TEACH` and `LIFE` are valid subjects.
- Do **not** use `SUP` for new sets.
- Do **not** use `URD` — Urdu is not part of her paper.
- Target SUPERTET Primary: academic content at Class 12 level, pedagogy at D.El.Ed level. Harder than UPTET.
- No subject-wise pedagogy questions (`MPED`, `SPED`, `EPED`, `HPED`) — SUPERTET has no subject-pedagogy section.

## 5. Files

`chapter-topic-map.json` gives the chapter → `subjectId`/`topicId` pair for all 183, so every practice set can be tagged without guessing.
