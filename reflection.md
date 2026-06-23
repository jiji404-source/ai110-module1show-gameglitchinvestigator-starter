# 💭 Reflection: Game Glitch Investigator

Answer each question in 3 to 5 sentences. Be specific and honest about what actually happened while you worked. This is about your process, not trying to sound perfect.

## 1. What was broken when you started?

- What did the game look like the first time you ran it?

The first time I ran the game, it launched but the hints were immediately wrong. When I guessed 100 (the maximum possible number), the game told me to "Go Higher," which is impossible. The score also behaved strangely, wrong guesses sometimes added points instead of deducting them, and a new game didn't fully reset the state so the score carried over from the previous round. The show hint checkbox also didn't always behave predictably because the outcome itself could be wrong due to a string comparison bug on even-numbered attempts.

- List at least two concrete bugs you noticed at the start  
  (for example: "the hints were backwards").
1. Game told me "Go Higher" when I guessed 100
2. The "New Game" button didn't work

**Bug Reproduction Log**

Document at least 3 bugs you found. Add rows as needed.

| Input | Expected Behavior | Actual Behavior | Console Output / Error |
|-------|-------------------|-----------------|------------------------|
| Guess: 100, Secret: 50 | "Too High — Go Lower" | "Too High — Go HIGHER!" | none |
| Wrong guess on even attempt (e.g., attempt 2) | Score -5 | Score +5 (rewarded for wrong guess) | none |
| Click "New Game" after losing | Fresh game, score resets to 0 | Score and status carry over from previous game | none |

---

## 2. How did you use AI as a teammate?

- Which AI tools did you use on this project (for example: ChatGPT, Gemini, Copilot)?

Claude

- Give one example of an AI suggestion that was correct (including what the AI suggested and how you verified the result).

A correct suggestion: when I described the "Go Higher" hint bug, the AI immediately identified that the hint messages in check_guess were swapped, "Go HIGHER!" was returned when guess > secret (too high), when it should say "Go LOWER!". I verified this by reading lines 37–40 in app.py and confirming the logic matched the bug I was seeing.

- Give one example of an AI suggestion that was incorrect or misleading (including what the AI suggested and how you verified the result).

A misleading moment: the AI's initial explanation of the score bug focused only on the +5 reward on even attempts, but didn't immediately surface the deeper issue, that the secret was being converted to a string on even attempts, causing alphabetical comparisons like "100" < "50" to return wrong outcomes entirely. I had to keep playing the game and notice the score was still off before that second bug was caught.

---

## 3. Debugging and testing your fixes

- How did you decide whether a bug was really fixed?

I verified each fix by running streamlit run app.py and manually testing the exact input that triggered the bug.

- Describe at least one test you ran (manual or using pytest)  
  and what it showed you about your code.

For the hint bug, I guessed 100 when the secret was a low number and confirmed "Go LOWER!" appeared. For the score bug, I tracked the score across 3 guesses and confirmed it went 0 → -5 → -10 → -15 consistently. I also ran pytest to check the existing tests in tests/test_game_logic.py, which test check_guess directly against known inputs.

- Did AI help you design or understand any tests? How?

The AI helped me understand what a targeted test should look like, for example, asserting that check_guess(60, 50) returns "Too High" directly tests the hint logic without needing to run the full UI.

---

## 4. What did you learn about Streamlit and state?

- How would you explain Streamlit "reruns" and session state to a friend who has never used Streamlit?

Streamlit "reruns" the entire Python script from top to bottom every time a user interacts with anything, clicks a button, types in a field, changes a dropdown. This means you can't store values in regular Python variables between interactions; they'd reset every time. st.session_state solves this by acting like a dictionary that persists across reruns. I learned this matters a lot for game logic: when the "New Game" button only reset attempts and secret but not status or score, those old values survived the rerun and broke the next game.

---

## 5. Looking ahead: your developer habits

- What is one habit or strategy from this project that you want to reuse in future labs or projects?
  - This could be a testing habit, a prompting strategy, or a way you used Git.

One habit I want to keep is testing the exact broken input that exposed a bug before declaring it fixed, not just running the app and assuming. The "Go Higher" bug was easy to verify that way.

- What is one thing you would do differently next time you work with AI on a coding task?

One thing I'd do differently next time is ask the AI to explain all the places a value is used before fixing just one, the string comparison bug affected both the hint and the score but I only caught it because the score was still wrong after the first fix.

- In one or two sentences, describe how this project changed the way you think about AI generated code.

This project changed how I think about AI-generated code: it can look correct at a glance and even pass basic logic checks, but the bugs tend to hide in edge cases and interactions between parts of the code that the AI didn't think about together.