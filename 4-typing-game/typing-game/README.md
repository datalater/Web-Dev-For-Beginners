# Creating a game using events

## Pre-Lecture Quiz

[Pre-lecture quiz](../.github/pre-lecture-quiz.md)

## Event driven programming

> The difference is **how** the functions will be called.

When creating a browser based application, we provide a graphical user interface (GUI) for the user to use when interacting with what we've built. The most common way to interact with the browser is through clicking and typing in various elements. The challenge we face as a developer is we don't know when they're going to perform these operations!

[Event-driven programming](https://en.wikipedia.org/wiki/Event-driven_programming) is the name for the type of programming we need to do to create our GUI. If we break this phrase down a little bit, we see the core word here is **event**. [Event](https://www.merriam-webster.com/dictionary/event), according to Merriam-Webster, is defined as "something which happens". This describes our situation perfectly. We know something is going to happen for which we want to execute some code in response, but we don't know when it will take place.

The way we mark a section of code we want to execute is by creating a function. When we think about [procedural programming](https://en.wikipedia.org/wiki/Procedural_programming), functions are called in a specific order. This same thing is going to be true with event driven programming. The difference is **how** the functions will be called.

To handle events (button clicking, typing, etc.), we register **event listeners**. An event listener is a function which listens for an event to occur and executes in response. Event listeners can update the UI, make calls to the server, or whatever else needs to be done in response to the user's action. We add an event listener by using [addEventListener](https://developer.mozilla.org/docs/Web/API/EventTarget/addEventListener), and providing a function to execute.

> **NOTE:** It's worth highlighting there are numerous ways to create event listeners. You can use anonymous functions, or create named ones. You can use various shortcuts, like setting the `click` property, or using `addEventListener`. In our exercise we are going to focus on `addEventLister` and anonymous functions, as it's probably the most common technique web developers use. It's also the most flexible, as `addEventListener` works for all events, and the event name can be provided as a parameter.

### Common events

There are [dozens of events](https://developer.mozilla.org/docs/Web/Events) available for you to listen to when creating an application. Basically anything a user does on a page raises an event, which gives you a lot of power to ensure they get the experience you desire. Fortunately, you'll normally only need a small handful of events. Here's a few common ones (including the two we'll use when creating our game):

- [click](https://developer.mozilla.org/en-US/docs/Web/API/Element/click_event): The user clicked on something, typically a button or hyperlink
- [contextmenu](https://developer.mozilla.org/en-US/docs/Web/API/Element/contextmenu_event): The user clicked the right mouse button
- [select](https://developer.mozilla.org/en-US/docs/Web/API/Element/select_event): The user highlighted some text
- [input](https://developer.mozilla.org/en-US/docs/Web/API/Element/input_event): The user input some text

## Creating the game

We are going to create a game to explore how events work in JavaScript. Our game is going to test a player's typing skill, which is one of the most underrated skills all developers should have. We should all be practicing our typing! The general flow of the game will look like this:

- Player clicks on start button and is presented with a quote to type
- Player types the quote as quickly as they can in a textbox
  - As each word is completed, the next one is highlighted
  - If the player has a typo, the textbox is updated to red
  - When the player completes the quote, a success message is displayed with the elapsed time

Let's build our game, and learn about events!

### File structure

We're going to need three total files: **index.html**, **script.js** and **style.css**. Let's start by setting those up to make life a little easier for us.

- Create a new folder for your work by opening a console or terminal window and issuing the following command:

```bash
# Linux or macOS
mkdir typing-game && cd typing-game

# Windows
md typing-game && cd typing game
```

- Open Visual Studio Code

```bash
code .
```

- Add three files to the folder in Visual Studio Code with the following names:
  - index.html
  - script.js
  - style.css

## Create the user interface

If we explore the requirements, we know we're going to need a handful of elements on our HTML page. This is sort of like a recipe, where we need some ingredients:

- Somewhere to display the quote for the user to type
- Somewhere to display any messages, like a success message
- A textbox for typing
- A start button

Each of those will need IDs so we can work with them in our JavaScript. We will also add references to the CSS and JavaScript files we're going to create.

Create a new file named **index.html**. Add the following HTML:

> **INSTRUCTION**
> 
> UI를 설계하고 구현하라.
> 1. `<h1>게임 제목</h1>`
> 2. `<p>게임 설명</p>`
> 3. `<h2 id="quote">제시 문장</h2>`
> 4. `<h2 id="message">게임 결과</h2>`
> 5. `<input type="text" id="typed-value"> <!-- 타이핑 텍스트 박스 -->`
> 6. `<button type="button" id="start>Start</button> <!-- 게임 시작 버튼 -->`

```html
<!-- inside index.html -->
<html>
<head>
  <title>Typing game</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <h1>Typing game!</h1>
  <p>Practice your typing skills with a quote from Sherlock Holmes. Click **start** to begin!</p>
  <h2 id="quote"></h2> <!-- This will display our quote -->
  <h2 id="message"></h2> <!-- This will display any status messages -->
  <div>
    <input type="text" id="typed-value" /> <!-- The textbox for typing -->
    <button type="button" id="start">Start</button> <!-- To start the game -->
  </div>
  <script src="script.js"></script>
</body>
</html>
```

### Launch the application

It's always best to develop iteratively to see how things look. Let's launch our application. There's a wonderful extension for Visual Studio Code called [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) which will both host your application locally and refresh the browser each time you save.

- Install [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) by following the link and clicking **Install**
  - You will be prompted by the browser to open Visual Studio Code, and then by Visual Studio Code to perform the installation
  - Restart Visual Studio Code if prompted
- Once installed, in Visual Studio Code, click Ctl-Shift-P (or Cmd-Shift-P) to open the command pallate
- Type **Live Server: Open with Live Server**
  - Live Server will start hosting your application
- Open a browser and navigate to **https://localhost:5500**
- You should now see the page you created!

Let's add some functionality.

## Add the CSS

With our HTML created, let's add the CSS for core styling. We need to highlight the word the player should be typing, and colorize the textbox if what they've typed is incorrect. We'll do this with two classes.

Create a new file named **style.css** and add the following syntax.

> **INSTRUCTION**
>
> 제시 문장에서 현재 타이핑할 단어를 하이라이트하고 오타가 났을 때도 하이라이트 한다.
> 1. 현재 타이핑할 단어를 `.highlight`로 셀렉트하고 배경색을 `yellow`로 설정한다.
> 2. 타이핑에 오타가 났을 경우 `.error`로 셀렉트하고 배경색을 `lightcoral`로, border를 `red`로 설정한다.

```css
/* inside style.css */
.highlight {
  background-color: yellow;
}

.error {
  background-color: lightcoral;
  border: red;
}
```

✅ When it comes to CSS you can layout your page however you might like. Take a little time and make the page look more appealing:

- Choose a different font
- Colorize the headers
- Resize items

## JavaScript

With our UI created, it's time to focus our attention on the JavaScript which will provide the logic. We're going to break this down into a handful of steps:

- [Create the constants](#add-the-constants)
- [Event listener to start the game](#add-start-logic)
- [Event listener to typing](#add-typing-logic)

But first, create a new file named **script.js**.

### Add the constants

We're going to need a few items to make our lives a little easier for programming. Again, similar to a recipe, here's what we'll need:

- Array with the list of all quotes
- Empty array to store all the words for the current quote
- Space to store the index of the word the player is currently typing
- The time the player clicked start

We're also going to want references to the UI elements:

- The textbox (**typed-value**)
- The quote display (**quote**)
- The message (**message**)

> **INSTRUCTIONS**
>
> 게임에서 필요한 변수를 설계하고 구현하라.
> 1. `quotes`: 제시 문장 목록을 담을 array || 문제집
> 2. `words`: 현재 제시된 문장의 단어 목록을 담을 array
> 3. `wordIndex`: 현재 타이핑 해야 할 단어를 추적하기 위한 array index number || highlight할 단어의 index
> 4. `startTime`: 타이핑 속도를 재기 위한 시간
> 5. `quoteElement`: 타이핑 해야 하는 제시 문장 element
> 6. `messageElement`: 게임 결과 메시지 element
> 7. `typedValueElement`: 플레이어가 타이핑 하는 input 박스 element

```javascript
// inside script.js
// all of our quotes
const quotes = [
    'When you have eliminated the impossible, whatever remains, however improbable, must be the truth.',
    'There is nothing more deceptive than an obvious fact.',
    'I ought to know by this time that when a fact appears to be opposed to a long train of deductions it invariably proves to be capable of bearing some other interpretation.',
    'I never make exceptions. An exception disproves the rule.',
    'What one man can invent another can discover.',
    'Nothing clears up a case so much as stating it to another person.',
    'Education never ends, Watson. It is a series of lessons, with the greatest for the last.',
];
// store the list of words and the index of the word the player is currently typing
let words = [];
let wordIndex = 0;
// the starting time
let startTime = Date.now();
// page elements
const quoteElement = document.getElementById('quote');
const messageElement = document.getElementById('message');
const typedValueElement = document.getElementById('typed-value');
```

✅ Go ahead and add more quotes to your game

> **NOTE:** We can retrieve the elements whenever we want in code by using `document.getElementById`. Because of the fact we're going to refer to these elements on a regular basis we're going to avoid typos with string literals by using constants. Frameworks such as [Vue.js](https://vuejs.org/) or [React](https://reactjs.org/) can help you better manage centralizing your code.

Take a minute to watch a video on using `const`, `let` and `var`

[![Types of variables](https://img.youtube.com/vi/JNIXfGiDWM8/0.jpg)](https://youtube.com/watch?v=JNIXfGiDWM8 "Types of variables")

### Add start logic

To begin the game, the player will click on start. Of course, we don't know when they're going to click start. This is where an [event listener](https://developer.mozilla.org/docs/Web/API/EventTarget/addEventListener) comes into play. An event listener will allow us to listen for something to occur (an event) and execute code in response. In our case, we want to execute code when the user clicks on start.

When the user clicks **start**, we need to select a quote, setup the user interface, and setup tracking for the current word and timing. Below is the JavaScript you'll need to add; we discuss it just after the script block.

> **INSTURCTION**
>
> start 버튼이 클릭되면 함수를 실행해서 게임이 시작 되도록 event-driven으로 구현하라.
> 1. start 버튼에 click 이벤트를 감지할 이벤트리스너를 추가할 것이다: 이벤트리스너는 화살표 함수로 구현한다.
> 2. 제시 문장을 랜덤 index로 뽑을 것이다: 0에서 1사이 소수를 리턴하는 `Math.random()`과 문장 목록의 길이를 이용한다.
> 3. 플레이어의 타이핑을 문장 단위가 아닌 단어 단위로 추적할 것이다: 제시 문장을 단어 array로 바꾸고 단어 index도 사용한다.
> 4. 제시 문장이 뽑혔으므로 단어별로 구분지은 형태로 UI에 반영할 것이다: span 태그로 각 단어를 랩핑해서 `innerHTML`에 넣는다.
> 5. 첫번째 단어를 highlight할 것이다: 첫번째 단어 노드에 `className`을 사용해서 클래스 속성으로 highlight를 추가한다.
> 6. 게임이 방금 시작되었으므로 게임 결과 메세지를 초기화한다: `messageElement`의 `innerText`를 빈 문자열로 채운다.
> 7. 플레이어 인풋 박스를 초기화한다: `typedValueElement`의 `value`를 빈 문자열로 채운다.
> 8. 플레이어 인풋 박스가 눈에 띄도록 포커스한다: `typedValueElement`에 `focus()` 메서드를 실행하면 border 색이 달라진다.
> 9. 타이머를 시작한다: `startTime` 변수에 현재 시간을 저장한다.

```javascript
// at the end of script.js
document.getElementById('start').addEventListener('click', () => {
  // get a quote
  const quoteIndex = Math.floor(Math.random() * quotes.length);
  const quote = quotes[quoteIndex];
  // Put the quote into an array of words
  words = quote.split(' ');
  // reset the word index for tracking
  wordIndex = 0;

  // UI updates
  // Create an array of span elements so we can set a class
  const spanWords = words.map(function(word) { return `<span>${word} </span>`});
  // Convert into string and set as innerHTML on quote display
  quoteElement.innerHTML = spanWords.join('');
  // Highlight the first word
  quoteElement.childNodes[0].className = 'highlight';
  // Clear any prior messages
  messageElement.innerText = '';

  // Setup the textbox
  // Clear the textbox
  typedValueElement.value = '';
  // set focus
  typedValueElement.focus();
  // set the event handler

  // Start the timer
  startTime = new Date().getTime();
});
```

Let's break down the code!

- Setup the word tracking
  - Using [Math.floor](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Math/floor) and [Math.random](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Math/random) allows us to randomly select a quote from the `quotes` array
  - We convert the `quote` into an array of `words` so we can track the word the player is currently typing
  - `wordIndex` is set to 0, since the player will start on the first word
- Setup the UI
  - Create an array of `spanWords`, which contains each word inside a `span` element
    - This will allow us to highlight the word on the display
  - `join` the array to create a string which we can use to update the `innerHTML` on `quoteElement`
    - This will display the quote to the player
  - Set the `className` of the first `span` element to `highlight` to highlight it as yellow
  - Clean the `messageElement` by setting `innerText` to `''`
- Setup the textbox
  - Clear the current `value` on `typedValueElement`
  - Set the `focus` to `typedValueElement`
- Start the timer by calling `getTime`

### Add typing logic

As the player types, an `input` event will be raised. This event listener will check to ensure the player is typing the word correctly, and handle the current status of the game. Returning to **script.js**, add the following code to the end. We will break it down afterwards.

> **INSTRUCTION**
>
> 플레이어가 타이핑할 때마다 어떻게 게임을 처리해야 하는지 시나리오 별로 예시 상황을 그려보면서 각 케이스마다 분기문으로 처리하라.
> * 제시문장: "I never make exceptions."
> * highlight: "I "
> 1. 사용자가 "I"까지 쓰면, 잘 쓰고 있으므로 typedValueElement.className을 빈 문자열로 채운다.
> 2. 사용자가 "I "까지 쓰면, 첫 번째 단어가 완성되었으므로 typedValueElement 값을 초기화하고 && wordIndex를 하나 추가하고 && 제시 문장에 포함된 모든 단어에 하이라이트를 없애기 위해 클래스를 초기화하고 && 다음 wordIndex에 해당하는 단어를 하이라이팅하기 위해 클래스 이름에 'highlight'을 추가한다.
> 3. 사용자가 마지막 단어 "excetions."까지 쓰면, 문장이 완성되었으므로 현재 시간과 시작 시간으로 소요 시간을 구해서 성공 메시지를 출력한다.
> 4. 위 3가지 경우에 어긋나면 오류이므로 typedValueElement의 클래스 속성에 error를 추가한다.

```javascript
// at the end of script.js
typedValueElement.addEventListener('input', () => {
  // Get the current word
  const currentWord = words[wordIndex];
  // get the current value
  const typedValue = typedValueElement.value;

  if (typedValue === currentWord && wordIndex === words.length - 1) {
    // end of sentence
    // Display success
    const elapsedTime = new Date().getTime() - startTime;
    const message = `CONGRATULATIONS! You finished in ${elapsedTime / 1000} seconds.`;
    messageElement.innerText = message;
  } else if (typedValue.endsWith(' ') && typedValue.trim() === currentWord) {
    // end of word
    // clear the typedValueElement for the new word
    typedValueElement.value = '';
    // move to the next word
    wordIndex++;
    // reset the class name for all elements in quote
    for (const wordElement of quoteElement.childNodes) {
      wordElement.className = '';
    }
    // highlight the new word
    quoteElement.childNodes[wordIndex].className = 'highlight';
  } else if (currentWord.startsWith(typedValue)) {
    // currently correct
    // highlight the next word
    typedValueElement.className = '';
  } else {
    // error state
    typedValueElement.className = 'error';
  }
});
```

Let's break down the code! We start by grabbing the current word and the value the player has typed thus far. Then we have waterfall logic, where we check if the quote is complete, the word is complete, the word is correct, or (finally), if there is an error.

- Quote is complete, indicated by `typedValue` being equal to `currentWord`, and `wordIndex` being equal to one less than the `length` of `words`
  - Calculate `elapsedTime` by subtracting `startTime` from the current time
  - Divide `elapsedTime` by 1,000 to convert from milliseconds to seconds
  - Display a success message
- Word is complete, indicated by `typedValue` ending with a space (the end of a word) and `typedValue` being equal to `currentWord`
  - Set `value` on `typedElement` to be `''` to allow for the next word to be typed
  - Increment `wordIndex` to move to the next word
  - Loop through all `childNodes` of `quoteElement` to set `className` to `''` to revert to default display
  - Set `className` of the current word to `highlight` to flag it as the next word to type
- Word is currently typed correctly (but not complete), indicated by `currentWord` started with `typedValue`
  - Ensure `typedValueElement` is displayed as default by clearing `className`
- If we made it this far, we have an error
  - Set `className` on `typedValueElement` to `error`

## Test your application

You've made it to the end! The last step is to ensure our application works. Give it a shot! Don't worry if there are errors; **all developers** have errors. Examine the messages and debug as needed.

Click on **start**, and start typing away! It should look a little like the animation we saw before.

![Animation of the game in action](/4-typing-game/images/demo.gif)

---

## 🚀 Challenge

Add more functionality

- Disable the `input` event listener on completion, and re-enable it when the button is clicked
- Disable the textbox when the player completes the quote
- Display a modal dialog box with the success message
- Store high scores using [localStorage](https://developer.mozilla.org/docs/Web/API/Window/localStorage)

## Post-Lecture Quiz

[Post-lecture quiz](../.github/post-lecture-quiz.md)

## Review & Self Study

Read up on [all the events available](https://developer.mozilla.org/en-US/docs/Web/Events) to the developer via the web browser, and consider the scenarios in which you would use each one.

## Assignment

[Create a new keyboard game](assignment.md)

---

## ✅ Finished

- [o] Disable the `input` event listener on completion, and re-enable it when the button is clicked
- [o] Disable the textbox when the player completes the quote

```js
const quotes = [
    'When you have eliminated the impossible, whatever remains, however improbable, must be the truth.',
    'There is nothing more deceptive than an obvious fact.',
    'I ought to know by this time that when a fact appears to be opposed to a long train of deductions it invariably proves to be capable of bearing some other interpretation.',
    'I never make exceptions. An exception disproves the rule.',
    'What one man can invent another can discover.',
    'Nothing clears up a case so much as stating it to another person.',
    'Education never ends, Watson. It is a series of lessons, with the greatest for the last.',
];

// store the list of words and the index of the word the play is currently typing
let words = [];
let wordIndex = 0;
// the starting time
let startTime = Date.now();
// page elements
const quoteElement = document.getElementById('quote');
const messageElement = document.getElementById('message');
const typedValueElement = document.getElementById('typed-value');
const startButtonElement = document.getElementById('start');

// set previous quote index to avoid sequential repetition 
let previousQuoteIndex = 0;

startButtonElement.addEventListener('click', () => {
    // enable input eventlistener
    typedValueElement.disabled = false;

    // get a quote with no duplication in a row
    let candidateIndex = Math.floor(Math.random() * quotes.length);

    let i = 0;
    while (previousQuoteIndex === candidateIndex && i < 10) {
        candidateIndex = Math.floor(Math.random() * quotes.length);
        i++;
    }

    const quoteIndex = candidateIndex;
    const quote = quotes[quoteIndex];

    // put the quote in to an array of words
    words = quote.split(' ');
    // reset the word index for tracking
    wordIndex = 0;

    // UI updates
    // create an array of span elements so we can set a class
    const spanWords = words.map(function (word) { return `<span>${word} </span>` });
    // convert into string and set as innerHTML on quote display
    quoteElement.innerHTML = spanWords.join('');
    // highlight the first word
    quoteElement.childNodes[0].className = 'highlight';
    // clear any prior messages
    messageElement.innerText = '';

    // setup the textbox
    // clear the textbox
    typedValueElement.value = '';
    // set focus
    typedValueElement.focus();
    // set the event handler

    // start the timer
    startTime = new Date().getTime()

    previousQuoteIndex = quoteIndex;
});

typedValueElement.addEventListener('input', () => {
    // get the current word
    const currentWord = words[wordIndex];
    // get the current value
    const typedValue = typedValueElement.value;

    if (typedValue === currentWord && wordIndex === words.length - 1) {
        console.log('case: end of sentence');
        // end of sentence
        // display sucesss
        const elapsedTime = new Date().getTime() - startTime;
        const message = `CONGRATULATIONS! You finished in ${elapsedTime / 1000} seconds.`;
        messageElement.innerText = message;

        // disable input eventlistener
        typedValueElement.disabled = true;
    } else if (typedValue.endsWith(' ') && typedValue.trim() === currentWord) {
        console.log('case: end of word');
        // end of word
        // clear the typedValueElement for the new word
        typedValueElement.value = '';
        // move to the next word
        wordIndex++;

        // reset the class name for all elements in quote
        for (const wordElement of quoteElement.childNodes) {
            wordElement.className = '';
        }

        // highlight the new word
        quoteElement.childNodes[wordIndex].className = 'highlight';
    } else if (currentWord.startsWith(typedValue)) {
        console.log('case: correct word');
        // currently correct
        // highlight the next word
        typedValueElement.className = '';
    } else {
        console.log('case: wrong word');
        // error state
        typedValueElement.className = 'error';
    }
});

let shiftToggle = false;
document.addEventListener('keydown', (e) => {
    const key = e.key.toLowerCase();

    if (key === 'shift') {
        shiftToggle = true;
    }

    if (shiftToggle && key === 'enter') {
        startButtonElement.click();
    }
});

document.addEventListener('keyup', (e) => {
    const key = e.key.toLowerCase();

    if (key === 'shift') {
        shiftToggle = false;
    }
});
```