# Accessible Colors: How Computers Measure Contrast

## Why?

Have you ever struggled to read light gray text on a white background, or squinted at a website with clashing colors? The difference between readable and unreadable often comes down to *contrast* — how much the text color differs in brightness from the background. In this activity, your team will discover how to measure contrast mathematically and determine whether a color combination meets accessibility standards.

## Prerequisites

- Python variables and arithmetic expressions (including `**` for exponentiation)
- Conditional statements (`if`/`else`)
- Calling Python functions

## Learning Objectives

**Content:**
- Describe how a color is represented as three numeric values (red, green, blue), each ranging from 0 to 255.
- Calculate the relative luminance of a color from its linearized RGB channel values.
- Calculate the contrast ratio between two colors and determine whether the pair meets WCAG AA guidelines for normal-sized text.

**Process:**
- Extract a mathematical relationship from patterns in tabular data (Information Processing).

&nbsp;

## Model 1: How Bright Is That Color?

Computers represent every color as three numbers — one each for the red, green, and blue channels. Each channel ranges from 0 (none of that color) to 255 (maximum intensity). For example, `(255, 0, 0)` is pure red: maximum red, no green, no blue.

To measure contrast, we first need a single number that captures how *bright* a color appears to the human eye. This requires two steps.

**Step 1 — Linearize each channel.** Raw channel values don't correspond directly to how much light your screen emits. The function below converts a single channel value (0–255) to a *linear* light value between 0.0 and 1.0:

```python
def linearize(channel):
    v = channel / 255
    if v <= 0.04045:
        return v / 12.92
    else:
        return ((v + 0.055) / 1.055) ** 2.4
```

**Step 2 — Combine into a single brightness value.** The three linearized channel values are combined into one number. The table below shows both steps for eight colors:

| Color   |   R |   G |   B | lin_r  | lin_g  | lin_b  | Brightness |
|:--------|----:|----:|----:|-------:|-------:|-------:|-----------:|
| Black   |   0 |   0 |   0 | 0.0000 | 0.0000 | 0.0000 |     0.0000 |
| White   | 255 | 255 | 255 | 1.0000 | 1.0000 | 1.0000 |     1.0000 |
| Red     | 255 |   0 |   0 | 1.0000 | 0.0000 | 0.0000 |     0.2126 |
| Green   |   0 | 255 |   0 | 0.0000 | 1.0000 | 0.0000 |     0.7152 |
| Blue    |   0 |   0 | 255 | 0.0000 | 0.0000 | 1.0000 |     0.0722 |
| Yellow  | 255 | 255 |   0 | 1.0000 | 1.0000 | 0.0000 |     0.9278 |
| Cyan    |   0 | 255 | 255 | 0.0000 | 1.0000 | 1.0000 |     0.7874 |
| Magenta | 255 |   0 | 255 | 1.0000 | 0.0000 | 1.0000 |     0.2848 |

&nbsp;

1. How many numbers does a computer use to represent a single color?
   > ***Sample:***<br>
   > Three — one for red, one for green, one for blue.

   &nbsp;

2. What RGB values would produce a color with maximum green, maximum blue, and no red? Find this color in the table.
   > ***Sample:***<br>
   > (0, 255, 255). That's Cyan in the table.

   &nbsp;

3. Look at the `linearize` function. It contains an `if`/`else` statement. In your own words, what determines which formula is applied to a channel value?
   > ***Sample:***<br>
   > It checks whether v (the channel divided by 255) is small or large. If v is 0.04045 or less, it just divides by 12.92. Otherwise it uses the bigger formula with the exponent.

   &nbsp;

4. Examine the Red, Green, and Blue rows. In each row, exactly one linearized channel is 1.0 and the others are 0.0. What Brightness value does each produce?

   a. Red (lin_r = 1.0): Brightness = ___
      > ***Sample:***<br>
      > 0.2126

      &nbsp;

   b. Green (lin_g = 1.0): Brightness = ___
      > ***Sample:***<br>
      > 0.7152

      &nbsp;

   c. Blue (lin_b = 1.0): Brightness = ___
      > ***Sample:***<br>
      > 0.0722

   &nbsp;

5. Which color channel contributes the *most* to perceived brightness? Which contributes the *least*?
   > ***Sample:***<br>
   > Green contributes the most (0.7152) and blue the least (0.0722).

   &nbsp;

6. Yellow has lin_r = 1.0 and lin_g = 1.0 (with lin_b = 0.0). Using the per-channel brightness values you found in Question 4, predict Yellow's Brightness. Check the table — does your prediction match?
   > ***Sample:***<br>
   > 0.2126 + 0.7152 = 0.9278. Yes, it matches the table!

   &nbsp;

7. Verify your approach with one more color: Cyan has lin_g = 1.0 and lin_b = 1.0 (with lin_r = 0.0). Show your calculation and check it against the table.
   > ***Sample:***<br>
   > 0.7152 + 0.0722 = 0.7874. It matches.

   &nbsp;

8. Write a general formula for Brightness in terms of lin_r, lin_g, and lin_b.

   The official name for this value is **relative luminance**.
   > ***Sample:***<br>
   > (variation expected) Brightness = 0.2126 * lin_r + 0.7152 * lin_g + 0.0722 * lin_b

   &nbsp;

9. A color called "Orange" has RGB values `(255, 128, 0)`. Its linearized values are lin_r = 1.0000, lin_g = 0.2159, lin_b = 0.0000. Use your formula to calculate its relative luminance. Round to four decimal places.
   > ***Sample:***<br>
   > 0.2126 * 1.0 + 0.7152 * 0.2159 + 0.0722 * 0.0 = 0.2126 + 0.1544 + 0.0 = 0.3670

   &nbsp;

## Model 2: Can You Read This Text?

A large brightness difference between text and background makes text easier to read. The **contrast ratio** quantifies this using the relative luminance of each color:

```
contrast_ratio = (L_lighter + 0.05) / (L_darker + 0.05)
```

where `L_lighter` is the *larger* luminance and `L_darker` is the *smaller*. The result is always at least 1.

The Web Content Accessibility Guidelines (WCAG) set minimum contrast ratios so that text is readable by people with a wide range of visual abilities — including those with low vision, color vision deficiency, and aging eyes. The table below shows seven text-and-background combinations, their contrast ratios, and whether each meets the **WCAG AA** standard for normal-sized text.

| Pair | Text Color         | Background Color      | L_text | L_bg   | Contrast Ratio | WCAG AA |
|:----:|:-------------------|:----------------------|-------:|-------:|---------------:|:-------:|
| A    | Black (0, 0, 0)    | White (255, 255, 255) | 0.0000 | 1.0000 |          21.00 |  Pass   |
| B    | Green (0, 255, 0)  | Black (0, 0, 0)       | 0.7152 | 0.0000 |          15.30 |  Pass   |
| C    | Blue (0, 0, 255)   | White (255, 255, 255) | 0.0722 | 1.0000 |           8.59 |  Pass   |
| D    | Gray (118,118,118) | White (255, 255, 255) | 0.1813 | 1.0000 |           4.54 |  Pass   |
| E    | Gray (119,119,119) | White (255, 255, 255) | 0.1846 | 1.0000 |           4.48 |  Fail   |
| F    | Red (255, 0, 0)    | White (255, 255, 255) | 0.2126 | 1.0000 |           4.00 |  Fail   |
| G    | Yellow (255,255,0) | White (255, 255, 255) | 0.9278 | 1.0000 |           1.07 |  Fail   |

&nbsp;

10. Verify the contrast ratio for Pair A using the formula above. Show your work.
    > ***Sample:***<br>
    > L_lighter = 1.0 (white), L_darker = 0.0 (black). (1.0 + 0.05) / (0.0 + 0.05) = 1.05 / 0.05 = 21.0. Matches the table.

    &nbsp;

11. Verify the contrast ratio for Pair C. Which color is L_lighter and which is L_darker?
    > ***Sample:***<br>
    > White is lighter (L = 1.0), Blue is darker (L = 0.0722). (1.0 + 0.05) / (0.0722 + 0.05) = 1.05 / 0.1222 = 8.59. Matches.

    &nbsp;

12. The formula adds 0.05 to both luminance values instead of simply dividing L_lighter by L_darker. What would happen if you tried to compute the contrast ratio for Pair A *without* the 0.05?
    > ***Sample:***<br>
    > You'd divide by zero — L_darker for black is 0.0. Adding 0.05 prevents that.

    &nbsp;

13. Look at the Contrast Ratio and WCAG AA columns together. What is the minimum contrast ratio needed to pass?
    > ***Sample:***<br>
    > The cutoff is between 4.48 (Fail) and 4.54 (Pass) — somewhere around 4.5.

    &nbsp;

14. The WCAG AA standard requires a contrast ratio of at least **4.5 : 1** for normal-sized text. Pairs D and E differ by just one RGB step (118 vs. 119). In your own words, explain why the *darker* gray passes while the *lighter* gray fails.
    > ***Sample:***<br>
    > (variation expected) The darker gray (118) is further from white's brightness, so there's more contrast between them. Even one step lighter (119) brings it too close to white, dropping the ratio below 4.5.

    &nbsp;

15. Yellow text on a white background (Pair G) has a contrast ratio of only 1.07. Using what you learned in Model 1, explain *why* yellow has so little contrast against white.
    > ***Sample:***<br>
    > Yellow's luminance is 0.9278, which is very close to white's 1.0. They're almost equally bright because yellow maxes out both the red and green channels, and green contributes the most to luminance.

    &nbsp;

16. The "Orange" from Question 9 has a relative luminance of 0.3670. Calculate its contrast ratio against a white background. Does it meet WCAG AA for normal text?
    > ***Sample:***<br>
    > L_lighter = 1.0, L_darker = 0.3670. (1.0 + 0.05) / (0.3670 + 0.05) = 1.05 / 0.417 = 2.52. No, 2.52 < 4.5, so it fails.

    &nbsp;

17. Calculate that same Orange's contrast ratio against a black background. Does it pass?
    > ***Sample:***<br>
    > L_lighter = 0.3670, L_darker = 0.0. (0.3670 + 0.05) / (0.0 + 0.05) = 0.417 / 0.05 = 8.34. Yes, 8.34 >= 4.5, so it passes.

    &nbsp;

## Exercises

1. Complete the Python function below so that it returns the relative luminance of a color given its red, green, and blue channel values (each 0–255). Use the `linearize` function from Model 1.

   ```python
   def luminance(r, g, b):
       # Your code here
   ```

   > ***Sample:***<br>
   > ```python
   > def luminance(r, g, b):
   >     lr = linearize(r)
   >     lg = linearize(g)
   >     lb = linearize(b)
   >     return 0.2126 * lr + 0.7152 * lg + 0.0722 * lb
   > ```

   &nbsp;

2. Write a Python function `contrast_ratio(r1, g1, b1, r2, g2, b2)` that returns the contrast ratio between two colors. Make sure the larger luminance is in the numerator.

   > ***Sample:***<br>
   > ```python
   > def contrast_ratio(r1, g1, b1, r2, g2, b2):
   >     l1 = luminance(r1, g1, b1)
   >     l2 = luminance(r2, g2, b2)
   >     lighter = max(l1, l2)
   >     darker = min(l1, l2)
   >     return (lighter + 0.05) / (darker + 0.05)
   > ```

   &nbsp;

3. Write a Python function `meets_aa(r1, g1, b1, r2, g2, b2)` that returns `True` if the two colors meet WCAG AA for normal text, and `False` otherwise.

   > ***Sample:***<br>
   > ```python
   > def meets_aa(r1, g1, b1, r2, g2, b2):
   >     return contrast_ratio(r1, g1, b1, r2, g2, b2) >= 4.5
   > ```

   &nbsp;

4. Use your functions to test the following color pairs. For each, record the contrast ratio (rounded to two decimal places) and whether it passes WCAG AA.

   a. White `(255, 255, 255)` text on dark blue `(0, 0, 139)` background
      > ***Sample:***<br>
      > `contrast_ratio(255,255,255, 0,0,139)` returns approximately 15.30. Pass.

      &nbsp;

   b. Orange `(255, 165, 0)` text on white `(255, 255, 255)` background
      > ***Sample:***<br>
      > `contrast_ratio(255,165,0, 255,255,255)` returns approximately 1.97. Fail.

      &nbsp;

   c. Dark green `(0, 100, 0)` text on black `(0, 0, 0)` background
      > ***Sample:***<br>
      > `contrast_ratio(0,100,0, 0,0,0)` returns approximately 2.82. Fail.

   &nbsp;

## Problem

A design team wants to use purple `(128, 0, 128)` as their text color. They are choosing between a white and a black background.

Using your functions (or by hand), determine the contrast ratio of purple against each background. Which background should they use? Could they use either one?

> ***Sample:***<br>
> Purple's luminance: `linearize(128)` gives approximately 0.2159 for both the red and blue channels, green is 0. So L = 0.2126 * 0.2159 + 0 + 0.0722 * 0.2159 = 0.0459 + 0.0156 = 0.0615.
>
> Against white: (1.0 + 0.05) / (0.0615 + 0.05) = 1.05 / 0.1115 = 9.42. Pass.
>
> Against black: (0.0615 + 0.05) / (0.0 + 0.05) = 0.1115 / 0.05 = 2.23. Fail.
>
> They should use white. Black doesn't meet the 4.5 threshold.

&nbsp;

# Facilitation Notes

## Overview
- **Suggested time:** Model 1: ~25 min; Model 2: ~20 min. Exercises are after-class work.
- **Suggested team roles:** Manager (keeps the team on pace), Recorder (writes the team's answers), Presenter (reports out), Reflector (monitors whether everyone is contributing).
- **What to watch for at the classroom level:** Teams getting bogged down in the `linearize` function details instead of treating it as a provided tool. Teams trying to average the three channels instead of looking for weights. In Model 2, teams who sort by contrast ratio but miss the threshold because they round differently.

## Per-model notes

### Model 1
- **Q3 — linearize function:** Some teams will want to fully understand the linearization math. Reassure them that they don't need to memorize or derive this function — it's a provided tool, like `math.sqrt`. The important thing is that it takes a 0–255 value and returns a 0.0–1.0 value. If a team is curious, the short explanation is that screens apply a curve (called gamma) to stored pixel values, and `linearize` undoes that curve.
- **Q4–Q7 — discovering weights:** This is the core invention sequence. If a team tries averaging (Brightness = (lin_r + lin_g + lin_b) / 3), point them to Red: average of (1, 0, 0) would be 0.3333, but the table says 0.2126. Ask: "What operation on 1.0 gives you 0.2126?"
- **Q8 — key question:** Teams should articulate the weighted sum. Accept any correct formulation. Introduce the term "relative luminance" only at this point.
- **Common misconception:** Students may think higher RGB values always mean brighter. Clarify that (0, 255, 0) appears much brighter to the human eye than (0, 0, 255) even though both have one channel at max — that's exactly what the weights capture.
- **Timing target:** 25 minutes. If teams finish early, ask: "Why do you think green gets the biggest weight? What would happen if all three weights were equal?"

### Model 2
- **Q10–Q11 — verifying the formula:** Make sure teams identify which luminance is `L_lighter` vs. `L_darker`. A common error is putting the text color in the numerator regardless of which is brighter.
- **Q12 — why +0.05:** Teams sometimes think the 0.05 is just to avoid division by zero. That's the main reason, but it also models the fact that even a "black" screen reflects a small amount of ambient light. Either answer is fine for this level.
- **Q13–Q14 — threshold discovery:** The boundary between Pairs D (4.54, Pass) and E (4.48, Fail) is intentionally tight. If teams say "about 4.5," that's exactly right. Confirm that the WCAG AA threshold is 4.5:1 for normal-sized text (and 3:1 for large text, if they ask).
- **Q15 — yellow on white:** This connects back to Model 1. If a team is stuck, ask: "What was Yellow's luminance? What was White's? Are they close or far apart?"
- **Q16–Q17 — application:** These two questions together show that the same color can pass against one background and fail against another. If a team finishes early, ask: "What's the lightest background Orange could pass against?"
- **Timing target:** 20 minutes.

## If a team finishes early
- Ask them to find the lightest gray that passes WCAG AA against white — by hand or by writing a short loop. (Answer: RGB 118,118,118 — also known as #767676, the famous "WCAG gray.")
- Ask: "WCAG AAA requires a contrast ratio of 7:1 instead of 4.5:1. Which pairs in Model 2 would pass AAA?" (Only A, B, and C.)
