---
title: Flaredle
date: 2024-01-30 21:49:00 -0
categories: [Reverse Engineering, FlareOn9]
tags: [reverse_engineering, js]
---

# Challenge 1 - Flaredle

## Start

The Prompt for this challenge:
```
Welcome to Flare-On 9!

You probably won't win. Maybe you're like us and spent the year playing Wordle.
We made our own version that is too hard to beat without cheating.
```

When we download the challenge we get those 4 files:

![Files](https://yoro3000.github.io/assets/img/flareon9/challenge_1_flaredle/files.png)

And we get a link for this webpage:

![Homepage](https://yoro3000.github.io/assets/img/flareon9/challenge_1_flaredle/flaredle_homepage.png)

So we know we are dealing with a clone of the game "Wordle", so the immediate assumption is that we need to find the correct word to solve the challenge.

By pressing F12 to see that the site runs "script.js", let's open it up.

![F12](https://yoro3000.github.io/assets/img/flareon9/challenge_1_flaredle/f12.png)

## Getting the Flag

By taking a quick glance we can see that at the start we import WORDS from "words.js".After that we set a const variable CORRECT_GUESS and set it to 57.
We then initiate a variable called "rightGuessString" with the CORRECT_GUESS (57) index in the WORDS array.

![script](https://yoro3000.github.io/assets/img/flareon9/challenge_1_flaredle/script.png)

Based on that, let's check what is in the array on the 57th index (line 58 because index starts at 0)

![words](https://yoro3000.github.io/assets/img/flareon9/challenge_1_flaredle/words.png)

So we can see the string "flareonisallaboutcats" is the value put in "rightGuessString", and if we try to put it in the flaredle we get:

![flag](https://yoro3000.github.io/assets/img/flareon9/challenge_1_flaredle/flag.png)

![flagbox](https://yoro3000.github.io/assets/img/flareon9/challenge_1_flaredle/flag_box.png)

And we got the flag: **flareonisallaboutcats@flare-on.com**

## For Fun - Looking into the code

If we want to check the code to see quickly what happens

We can see the function "insertLetter" which populates our "currentGuess" variable based on our input.

```javascript
function insertLetter (pressedKey) {
    if (nextLetter === WORD_LENGTH) {
        return
    }
    pressedKey = pressedKey.toLowerCase()

    let row = document.getElementsByClassName("letter-row")[NUMBER_OF_GUESSES - guessesRemaining]
    let box = row.children[nextLetter]
    animateCSS(box, "pulse")
    box.textContent = pressedKey
    box.classList.add("filled-box")
    currentGuess.push(pressedKey)
    nextLetter += 1
}
```

After that, in "checkGuess" we can see some checks on guessString (our user input) going through some checks, for example making sure it is 21 letters long and so on...

```javascript
function checkGuess () {
    let row = document.getElementsByClassName("letter-row")[NUMBER_OF_GUESSES - guessesRemaining]
    let guessString = ''
    let rightGuess = Array.from(rightGuessString)

    for (const val of currentGuess) {
        guessString += val
    }

    if (guessString.length != WORD_LENGTH) {
        toastr.error("Not enough letters!")
        return
    }

    if (!WORDS.includes(guessString)) {
        toastr.error("Word not in list!")
        return
    }
    ...
```

If we go a bit down, we can see a check if "guessString" is equal to "rightGuessString" and if so, we append the '@flare-on.com' onto the "rightGuessString" and set it as the flag.

```javascript
...
if (guessString === rightGuessString) {
    let flag = rightGuessString + '@flare-on.com';
    toastr.options.timeOut = 0;
    toastr.options.onclick = function() {alert(flag);}
    toastr.success('You guessed right! The flag is ' + flag);

    guessesRemaining = 0
    return
...
```

And as we can see the flag is indeed the string we got from the 57 index appended with '@flare-on.com'.