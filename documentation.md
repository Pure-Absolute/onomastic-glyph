
# **Onomastic Glyph Framework**
> Manage name patterns and rules that would be used to generate a name

The **Onomastic Glyph Framework** is a symbolic language for constructing procedural name generators.  
It combines **Building Glyphs** (character pools) with **Special Glyphs** (modifiers and transformation rules) to generate names

# **1. Core Components**

The framework has **two major elements**:

## **1.1 Building Glyphs (0️⃣–9️⃣)**

Each building glyph represents a **set of strings** (syllables, phonemes, name fragments).  
Example:

```json
"1️⃣": ["ra", "rin", "lor", "sa"]

```

When the generator encounters **1️⃣**, it picks **one random element** from that list.

Building glyphs are the base material of your name.  
Everything else modifies, rearranges, or filters them.

----------

## **1.2 Special Glyphs**

Special glyphs do NOT output letters directly.  
They **modify the output of the glyph immediately to their left** — this is the most important rule in the whole system.

Example:

```
1️⃣🛸

```

Means:

-   generate 1️⃣
    
-   then 🛸 transforms the **result of 1️⃣** into uppercase
    

Special glyphs apply **only to the element directly left of them** (unless they explicitly duplicate multiple elements).

----------

# **2. Processing Order**

The framework reads the generator pattern **left → right**.

For each symbol:

1.  **If it's a building glyph:** output a string
    
2.  **If it's a special glyph:** modify the output of the previous glyph
    
3.  Continue until the pattern is complete
    
4.  Join all outputs into the final name
    

----------

# **3. All Special Glyphs Explained**

Here’s the definitive list, rewritten clearly and correctly:

----------

## **Duplication / Reflection**

Glyph

Meaning

**👣**

Duplicate the **last word** exactly and append it

**🪞**

Reverse the **last word** and append it

**🦜**

Repeat the glyph group **on the left twice**

----------

## **🎛 Randomness & Choice**

Glyph

Meaning

**🪙**

50% chance to activate the glyph **on the left**

**🎲**

Randomly inserts **a Building Glyph from group 1–6**

**🃏**

Inserts next **Building Glyph from 1–6** in sequence

**🔢**

Add a random digit (0–9)

----------

## **✍ Modification of Letters**

Glyph

Meaning

**💣**

Remove the **last letter** of the last word

**🥁**

Double a random **consonant** in the last word

**🔔**

Duplicate a random **vowel** in the last word

**🖌️**

Replace a random **vowel** with a **Building Glyph on the left**

**🔧**

Replace a random **consonant** with a **Building Glyph on the left**

**🪚**

Insert the **Building Glyph on the left** between **two random consonants**

**✂️**

Insert the **Building Glyph on the left** between **two random vowels**

----------

## **🧪 Conditional / Contextual Glyphs**

Glyph

Meaning

**🕯️**

Activate the glyph on the left **only if the last word ends in a vowel**

**🔨**

Activate the glyph on the left **only if the last word ends in a consonant**

----------

## **🧑‍🤝‍🧑 Gender Logic**

These do _not_ produce output themselves—they **restrict the glyph on their left**.

Glyph

Meaning

**🌵**

The glyph on the left applies **only to male names**

**🌸**

Applies **only to female names**

**Correct usage example:**

```
1️⃣🌵  → 1️⃣ only appears for male names  
1️⃣🌸 → 1️⃣ only appears for female names

```

**Wrong usage example:**

```
🌵1️⃣ → does nothing, because nothing is on 🌵’s left

```

----------

## **📏 Structural Glyphs**

Glyph

Meaning

**🌌**

Insert a space

**🌉**

Insert a hyphen

**👻**

Invisible boundary (splits word for mirror/duplicate logic but adds no character)

**🚧**

Divide different name sets (multiple branches in one generator)

----------


#  **7. Example: Simple Name Generator**
below is an example to generate Japanese name
```json
{
  "group": {
    "0️⃣": ["a", "i", "u", "e", "o"],

    "1️⃣": [
      "ka","ki","ku","ke","ko",
      "sa","shi","su","se","so",
      "ta","chi","tsu","te","to",
      "na","ni","nu","ne","no",
      "ha","hi","fu","he","ho",
      "ma","mi","mu","me","mo",
      "ya","yu","yo",
      "ra","ri","ru","re","ro",
      "wa","wo",
      "ga","gi","gu","ge","go",
      "za","ji","zu","ze","zo",
      "da","de","do",
      "ba","bi","bu","be","bo",
      "pa","pi","pu","pe","po"
    ],

    "2️⃣": ["ko","mi","na","to","ya","ri","ta","ki","sa","ru","no"],

    "3️⃣": ["maru", "hiro", "suke", "hiko", "kichi", "tarou"]
  },

  "generator": [
    "1️⃣", "🪙", "1️⃣", "🪙", "2️⃣", "🕯️", "3️⃣"
  ]
}


```

the Japanese generated names:
```
Kimiko, Haruto, Minarou, Renmaru, Sokichi, Yasuke, Tomihiko, Karino, Hirosuke, Kanato 
```
----------

#  **8. Example: Multi-branch Generator with 🚧**

Example to generate an Elven names with gender

```json
{
  "group": {
    "0️⃣": ["a", "ae", "ai", "e", "ea", "io", "y", "yl", "il", "el"],
    "1️⃣": ["l", "r", "n", "m", "v", "s", "th"],
    "2️⃣": ["a", "e", "i", "o", "u"],
    "3️⃣": ["Ara", "Eli", "Ira", "Lora", "Ner", "Ona", "Sael", "Tir"],
    "4️⃣": ["riel", "wyn", "nor", "thal", "wen"]
  },
  "generator": [
    // male branch (male-only markers follow the building glyph they restrict)
    "0️⃣", "1️⃣", "🌵", "2️⃣", "1️⃣", "🪙", "4️⃣",
    "🚧",
    // female branch
    "0️⃣", "2️⃣", "👣", "1️⃣", "🌸", "4️⃣",
    "🚧",
    // neutral suffix/mirror but with an invisible boundary to prevent runaway doubling
    "3️⃣", "👻", "🪞"
  ]
}


```
the generated result:
```
Male:
Althariel, Eryndalwyn, Ionaril, Velorian, Maerthal
Female:
Aeloriael, Ilywynriel, Elynael, Orianwen, Aesylia
Other:
AralAra, TirilTir
```
    

------