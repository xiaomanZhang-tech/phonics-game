# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A single-file HTML phonics learning game for kids (3-8 years old). Covers 6 core phonics knowledge areas across 16+ game sub-modes within 4 categories. No build tools, no dependencies — just open `phonics-game.html` in a browser.

## How to Run

Open `phonics-game.html` in any modern browser (Chrome recommended for best speech synthesis support). No server needed.

## Code Architecture

Everything is in one file (`phonics-game.html`) with these layers:

### Screens
- **Home screen** — difficulty selector (3 levels) + 4 category cards
- **Sub-mode screen** — dynamically populated sub-mode list for selected category
- **Game screen** — question area, 4-choice grid (or spelling tiles), progress bar, score display
- **Result screen** — star rating (1-3), accuracy stats, mode label, retry/home buttons

### Navigation Flow
Home → click category → Sub-mode screen → click sub-mode → Game → Result → Home

### 4 Categories & Sub-Modes

| Category | Sub-Modes | Covers |
|----------|-----------|--------|
| 🎯 听音辨音 | 首音/尾音/中间音识别、长短元音、清浊辅音 | Phonemic Awareness |
| 🔤 拼读拼写 | CVC拼读、多音节拼读、听音拼词 | Blending & Spelling |
| 🔊 辅音元音 | 单辅音、辅音组合、短元音、长元音、元音组合 | Consonants & Vowels |
| ⚡ 特殊规则 | 魔法e、软音c/g、特殊组合、高频词 | Special Rules |

### Key Data Structures (all keyed by level 1-3)
- `letterGroups` — single letters (L1), digraphs/vowel teams (L2), blends (L3)
- `blendWords` — CVC words (L1), digraph words (L2), blend words (L3)
- `phonemeWords` — beginning/ending/middle sound words
- `vowelShortLongPairs` — short vs long vowel minimal pairs
- `voicedUnvoicedPairs` — minimal pairs for consonant voicing
- `syllableWords` — multi-syllable words with syllable breakdowns
- `spellingWords` — words for dictation mode
- `magicEWords` — magic e minimal word pairs
- `softCSGWords` — hard vs soft c/g word pairs
- `specialCombos` — tion/sion/igh/ture words
- `sightWords` — high-frequency irregular words

### Core Functions

| Function | Purpose |
|----------|---------|
| `startGame(subMode)` | Initializes state, generates questions, renders first question |
| `generateQuestions()` | Dispatches to mode-specific generator based on `currentSubMode` |
| `renderQuestion()` | Dispatches to type-specific renderer based on `q.type` |
| `playCurrentSound()` | Triggers speech synthesis appropriate for question type |
| `checkAnswer(value, btn)` | Validates multiple-choice answer, applies visual feedback |
| `renderSpellingQuestion()` | Renders interactive letter tiles + slots for spelling mode |
| `spellingSubmit()` | Validates spelling mode answer |
| `showResult()` | Calculates stars, displays stats, triggers confetti if 3 stars |
| `showSubModes(category)` | Populates and shows the sub-mode selection screen |

### Question Types
- `'letter'` / `'phoneme'` / `'shortvowel'` — hear a sound, pick a letter from 4 choices
- `'blend'` — see word blending display, pick matching emoji from 4
- `'shortlong'` / `'magice'` / `'softcg'` / `'voiced'` — hear a word, pick matching emoji/word from 4
- `'syllable'` — see word, hear syllable breakdown, pick syllable count from 4
- `'spelling'` — hear word, tap letter tiles to build the word
- `'specialcombo'` — hear word, see the combo highlighted, pick matching word from 4
- `'sightwords'` — hear word, pick correct written form from 4

### Speech Engine (Web Speech API)
- `speakLetterSound(letter)` — says a word starting with the target letter sound
- `speakLetterName(letter)` — says the letter name (e.g., "bee", "cee")
- `speakBlendWord(word)` — sounds out letter by letter, then says the full word
- `speakSyllables(word, syllables)` — says each syllable, then the full word
- `speakWordSlow(word)` — ultra-slow pronunciation for spelling mode
- `speak(text, rate, callback)` — low-level speech wrapper

### Key State Variables
- `currentLevel` (1-3), `currentCategory`, `currentSubMode`
- `questions[]`, `currentQuestionIndex`
- `score`, `correctCount`, `wrongCount`
- `isAnswering` — prevents double-clicking
- `spellingSelected[]` — tracks tiles tapped in spelling mode

## Modifying Game Content

- Add words to any data object (keyed by level 1-3).
- All text is bilingual (Chinese + English) — update labels in both languages.
- Questions-per-game constant is `QUESTIONS_PER_GAME` (default 10).
- To add a new sub-mode: add entry to `subModeInfo`, add generator case in `generateQuestions`, add renderer case in `renderQuestion`, add sound case in `playCurrentSound`.
