# 🎮 Game Glitch Investigator: The Impossible Guesser

## 🚨 The Situation

You asked an AI to build a simple "Number Guessing Game" using Streamlit.
It wrote the code, ran away, and now the game is unplayable. 

- You can't win.
- The hints lie to you.
- The secret number seems to have commitment issues.

## 🛠️ Setup

1. Install dependencies: `pip install -r requirements.txt`
2. Run the broken app: `python -m streamlit run app.py`

## 🕵️‍♂️ Your Mission

1. **Play the game.** Open the "Developer Debug Info" tab in the app to see the secret number. Try to win.
2. **Find the State Bug.** Why does the secret number change every time you click "Submit"? Ask ChatGPT: *"How do I keep a variable from resetting in Streamlit when I click a button?"*
3. **Fix the Logic.** The hints ("Higher/Lower") are wrong. Fix them.
4. **Refactor & Test.** - Move the logic into `logic_utils.py`.
   - Run `pytest` in your terminal.
   - Keep fixing until all tests pass!

## 📝 Document Your Experience

- [x] Describe the game's purpose.

  A number guessing game where the player tries to guess a secret number within a limited number of attempts. The difficulty setting controls the number range and attempt limit. The score starts at 0, gains points on a win (more points for fewer attempts), and loses 5 points per wrong guess.

- [x] Detail which bugs you found.

  1. **Swapped hint messages** — `check_guess` returned "Go HIGHER!" when the guess was too high and "Go LOWER!" when too low. The messages were backwards.
  2. **Score rewarded wrong guesses** — on even-numbered attempts, a "Too High" guess added +5 to the score instead of deducting 5.
  3. **String comparison on even attempts** — the secret was converted to a string on every even attempt, causing alphabetical comparisons (e.g. `"100" < "50"`) that produced wrong outcomes and wrong score changes.
  4. **New Game button didn't reset state** — clicking New Game only reset `attempts` and `secret`, leaving `score` and `status` from the previous game intact.

- [x] Explain what fixes you applied.

  1. Swapped the hint message strings in `check_guess` so "Too High" → "Go LOWER!" and "Too Low" → "Go HIGHER!".
  2. Removed the `if attempt_number % 2 == 0: return current_score + 5` branch in `update_score` so wrong guesses always deduct 5.
  3. Removed the even/odd string conversion in `app.py` so the secret is always passed as an integer to `check_guess`.
  4. Refactored all four logic functions (`get_range_for_difficulty`, `parse_guess`, `check_guess`, `update_score`) out of `app.py` and into `logic_utils.py`.

## 📸 Demo Walkthrough

1. Run `streamlit run app.py`. The game opens with difficulty set to Normal (range 1–100, 8 attempts).
2. User enters a guess of 40 and clicks Submit. The secret is higher, so the game returns "📈 Go HIGHER!" and score drops to -5.
3. User enters a guess of 70. The secret is lower, so the game returns "📉 Go LOWER!" and score drops to -10.
4. User enters a guess of 55. Correct! The game shows "🎉 Correct!", launches balloons, and displays the final score.
5. Score updates correctly — points awarded based on how few attempts were used (100 minus 10 per attempt, minimum 10).
6. User clicks New Game. A fresh game starts with a new secret number.

## 🧪 Test Results

```
============================= test session starts ==============================
platform darwin -- Python 3.13.7, pytest-9.0.3
collected 3 items

tests/test_game_logic.py::test_winning_guess PASSED                      [ 33%]
tests/test_game_logic.py::test_guess_too_high PASSED                     [ 66%]
tests/test_game_logic.py::test_guess_too_low PASSED                      [100%]

============================== 3 passed in 0.01s ==============================
```

## 🚀 Stretch Features

- [ ] [If you choose to complete Challenge 4, describe the Enhanced UI changes here — a screenshot is optional]
