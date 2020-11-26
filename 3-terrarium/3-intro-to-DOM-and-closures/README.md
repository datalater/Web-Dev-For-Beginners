# Terrarium Project Part 3: DOM Manipulation and a Closure

![DOM and a closure](images/webdev101-js.png)
> Sketchnote by [Tomomi Imura](https://twitter.com/girlie_mac)

## Pre-Lecture Quiz

[Pre-lecture quiz](.github/pre-lecture-quiz.md)

### Introduction

Manipulating the DOM, or the "Document Object Model", is a key aspect of web development. According to [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Introduction), "The Document Object Model (DOM) is the data representation of the objects that comprise the structure and content of a document on the web." The challenges around DOM manipulation on the web have often been the impetus behind using JavaScript frameworks instead of vanilla JavaScript to manage the DOM, but we will manage on our own!

In addition, this lesson will introduce the idea of a [JavaScript closure](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures), which you can think of as a function enclosed by another function so that the inner function has access to the outer function's scope.

We will use a closure to manipulate the DOM.

> Think of the DOM as a tree, representing all the ways that a web page document can be manipulated. Various APIs (Application Program Interfaces) have been written so that programmers, using their programming language of choice, can access the DOM and edit, change, rearrange, and otherwise manage it.

![DOM tree representation](./images/dom-tree.png)

> A representation of the DOM and the HTML markup that references it. From [Olfa Nasraoui](https://www.researchgate.net/publication/221417012_Profile-Based_Focused_Crawler_for_Social_Media-Sharing_Websites)

In this lesson, we will complete our interactive terrarium project by creating the JavaScript that will allow a user to manipulate the plants on the page.

### Prequisite

You should have the HTML and CSS for your terrarium built. By the end of this lesson you will be able to move the plants into and out of the terrarium by dragging them.

### Task

In your terrarium folder, create a new file called `script.js`. Import that file in the `<head>` section:

```html
	<script src="./script.js" defer></script>
```

> Note: use `defer` when importing an external JavaScript file into the html file so as to allow the JavaScript to execute only after the HTML file has been fully loaded. You could also use the `async` attribute, which allows the script to execute while the HTML file is parsing, but in our case, it's important to have the HTML elements fully available for dragging before we allow the drag script to be executed.
---

## The DOM elements

The first thing you need to do is to create references to the elements that you want to manipulate in the DOM. In our case, they are the 14 plants currently waiting in the side bars.

### Task

```html
dragElement(document.getElementById('plant1'));
dragElement(document.getElementById('plant2'));
dragElement(document.getElementById('plant3'));
dragElement(document.getElementById('plant4'));
dragElement(document.getElementById('plant5'));
dragElement(document.getElementById('plant6'));
dragElement(document.getElementById('plant7'));
dragElement(document.getElementById('plant8'));
dragElement(document.getElementById('plant9'));
dragElement(document.getElementById('plant10'));
dragElement(document.getElementById('plant11'));
dragElement(document.getElementById('plant12'));
dragElement(document.getElementById('plant13'));
dragElement(document.getElementById('plant14'));
```

What's going on here? You are referencing the document and looking through its DOM to find an element with a particular Id. Remember in the first lesson on HTML that you gave individual Ids to each plant image (`id="plant1"`)? Now you will make use of that effort. After identifying each element, you pass that item to a function called `dragElement` that you'll build in a minute. Thus, the element in the HTML is now drag-enabled, or will be shortly.

✅ Why do we reference elements by Id? Why not by their CSS class? You might refer to the previous lesson on CSS to answer this question.

---

## The Closure

Now you are ready to create the dragElement closure, which is an outer function that encloses an inner function or functions (in our case, we will have three). 

Closures are useful when one or more functions need to access an outer function's scope. Here's an example:

```javascript
function displayCandy(){
	let candy = ['jellybeans'];
	function addCandy(candyType) {
		candy.push(candyType)
	}
	addCandy('gumdrops');
}
displayCandy();
console.log(candy)
```

In this example, the displayCandy function surrounds a function that pushes a new candy type into an array that already exists in the function. If you were to run this code, the `candy` array would be undefined, as it is a local variable (local to the closure). 

✅ How can you make the `candy` array accessible? Try moving it outside the closure. This way, the array becomes global, rather than remaining only available to the closure's local scope.

### Task

Under the element declarations in `script.js`, create a function:

```javascript
function dragElement(terrariumElement) {
	//set 4 positions for positioning on the screen
	let pos1 = 0,
		pos2 = 0,
		pos3 = 0,
		pos4 = 0;
	terrariumElement.onpointerdown = pointerDrag;
}
```

`dragElement` get its `terrariumElement` object from the declarations at the top of the script. Then, you set some local positions at `0` for the object passed into the function. These are the local variables that will be manipulated for each element as you add drag and drop functionality within the closure to each element. The terrarium will be populated by these dragged elements, so the application needs to keep track of where they are placed.

In addition, the terrariumElement that is passed to this function is assigned a `pointerdown` event, which is part of the [web APIs](https://developer.mozilla.org/en-US/docs/Web/API) designed to help with DOM management. `onpointerdown` fires when a button is pushed, or in our case, a draggable element is touched. This event handler works on both [web and mobile browsers](https://caniuse.com/?search=onpointerdown), with a few exceptions.

✅ The [event handler `onclick`](https://developer.mozilla.org/en-US/docs/Web/API/GlobalEventHandlers/onclick) has much more support cross-browser; why wouldn't you use it here? Think about the exact type of screen interaction you're trying to create here.

---

## The Pointerdrag function

The terrariumElement is ready to be dragged around; when the `onpointerdown` event is fired, the function pointerDrag is invoked. Add that function right under this line: `terrariumElement.onpointerdown = pointerDrag;`:

### Task 

```javascript
function pointerDrag(e) {
	e.preventDefault();
	console.log(e);
	pos3 = e.clientX;
	pos4 = e.clientY;
}
```

Several things happen. First, you prevent the default events that normally happen on pointerdown from occurring by using `e.preventDefault();`. This way you have more control over the interface's behavior.

> Come back to this line when you've built the script file completely and try it without `e.preventDefault()` - what happens?

Second, open `index.html` in a browser window, and inspect the interface. When you click a plant, you can see how the 'e' event is captured. Dig into the event to see how much information is gathered by one pointer down event!  

Next, note how the local variables `pos3` and `pos4` are set to e.clientX. You can find the `e` values in the inspection pane. These values capture the x and y coordinates of the plant at the moment you click on it or touch it. You will need fine-grained control over the behavior of the plants as you click and drag them, so you keep track of their coordinates.

✅ Is it becoming more clear why this entire app is built with one big closure? If it wasn't, how would you maintain scope for each of the 14 draggable plants?

Complete the initial function by adding two more pointer event manipulations under `pos4 = e.clientY`:

```html
document.onpointermove = elementDrag;
document.onpointerup = stopElementDrag;
```
Now you are indicating that you want the plant to be dragged along with the pointer as you move it, and for the dragging gesture to stop when you deselect the plant. `onpointermove` and `onpointerup` are all parts of the same API as `onpointerdown`. The interface will throw errors now as you have not yet defined the `elementDrag` and the `stopElementDrag` functions, so build those out next.

## The elementDrag and stopElementDrag functions

You will complete your closure by adding two more internal functions that will handle what happens when you drag a plant and stop dragging it. The behavior you want is that you can drag any plant at any time and place it anywhere on the screen. This interface is quite un-opinionated (there is no drop zone for example) to allow you to design your terrarium exactly as you like it by adding, removing, and repositioning plants.

### Task

Add the `elementDrag` function right after the closing curly bracket of `pointerDrag`:

```javascript
function elementDrag(e) {
	pos1 = pos3 - e.clientX;
	pos2 = pos4 - e.clientY;
	pos3 = e.clientX;
	pos4 = e.clientY;
	console.log(pos1, pos2, pos3, pos4);
	terrariumElement.style.top = terrariumElement.offsetTop - pos2 + 'px';
	terrariumElement.style.left = terrariumElement.offsetLeft - pos1 + 'px';
}
```
In this function, you do a lot of editing of the initial positions 1-4 that you set as local variables in the outer function. What's going on here?

As you drag, you reassign `pos1` by making it equal to `pos3` (which you set earlier as `e.clientX`)  minus the current `e.clientX` value. You do a similar operation to `pos2`. Then, you reset `pos3` and `pos4` to the new X and Y coordinates of the element. You can watch these changes in the console as you drag. Then, you manipulate the plant's css style to set its new position based on the new positions of `pos1` and `pos2`, calculating the plant's top and left X and Y coordinates based on comparing its offset with these new positions.

> `offsetTop` and `offsetLeft` are CSS properties that set an element's position based on that of its parent; its parent can be any element that is not positioned as `static`. 

All this recalculation of positioning allows you to fine-tune the behavior of the terrarium and its plants.

### Task 

The final task to complete the interface is to add the `closeElementDrag` function after the closing curly bracket of `elementDrag`:

```javascript
function stopElementDrag() {
	document.onpointerup = null;
	document.onpointermove = null;
}
```

This small function resets the `onpointerup` and `onpointermove` events so that you can either restart your plant's progress by starting to drag it again, or start dragging a new plant.

✅ What happens if you don't set these events to null?

Now you have completed your project!

🥇Congratulations! You have finished your beautiful terrarium. ![finished terrarium](./images/terrarium-final.png)

---

## 🚀Challenge

Add new event handler to your closure to do something more to the plants; for example, double-click a plant to bring it to the front. Get creative!

## Post-Lecture Quiz

[Post-lecture quiz](.github/post-lecture-quiz.md)

## Review & Self Study

While dragging elements around the screen seems trivial, there are many ways to do this and many pitfalls, depending on the effect you seek. In fact, there is an entire [drag and drop API](https://developer.mozilla.org/en-US/docs/Web/API/HTML_Drag_and_Drop_API) that you can try. We didn't use it in this module because the effect we wanted was somewhat different, but try this API on your own project and see what you can achieve.

Find more information on pointer events on the [W3C docs](https://www.w3.org/TR/pointerevents1/) and on [MDN web docs](https://developer.mozilla.org/en-US/docs/Web/API/Pointer_events).

Always check browser capabilities using [CanIUse.com](https://caniuse.com/).

## Assignment

[Work a bit more with the DOM](assignment.md)

<hr>

# ✅2W1H

Q1. 본 예제에서 closure를 사용하는 이유는 무엇인가?
`pointerDrag(e)`에 있는 e는 이벤트 객체인데 이는 `dragElement()` 함수 맥락에서 발생한다. 함수 안에 함수를 써서 클로저를 사용하면 바깥 함수의 스코프에 접근할 수 있기 때문에 e를 매개변수로 넘길 필요가 없다. 바깥 함수를 사용할 때 발생하는 맥락을 매개변수를 쓰지 않고 쉽게 사용하기 위해 closure를 사용한다.

Q2. `terrariumElement.onpointerdown = pointerDrag`는 무슨 뜻인가?
* 배경 지식: terrariumElement는 DOM에 속하고 DOM은 이미 내장된 web API가 존재한다. 
terrariumElement에 내장된 onpointerdown 이벤트 핸들러를 우리가 원하는 커스텀 함수인 pointerDrag로 바꿔치기한다는 뜻이다.

Q3. `e.preventDefault()`를 사용하는 이유는 무엇인가?
DOM web API에 의해 pointerdown 이벤트가 발생하면 자동적으로 실행되는 코드가 있다. default behavior는 이미지를 드래그하면 opacity가 적용된 채로 움직이는 것이다. 하지만 우리가 만들려는 동작에서는 opacity가 적용되는 default behavior가 필요없다. 따라서 이러한 default 코드가 않도록 하는 것이 `e.preventDefault()`이다.

Q4. pos1-4는 무엇이고 어떤 역할을 하는가?

* 배경지식: 우리가 만들려는 행동의 핵심 원리는 좌표이동이다. 좌표이동을 구현하려면 좌표의 변화량을 계산하는 과정이 있어야 한다.
* e.clientX: 현재 마우스의 x축 좌표
* e.clientY: 현재 마우스의 y축 좌표
* pos1: x축 좌표 변화량
* pos2: y축 좌표 변화량
* pos3: 마우스 이동(pointerdown 이벤트 발생) 전 x축 좌표
* pos4: 마우스 이동(pointerdown 이벤트 발생) 전 y축 좌표
* `terrariumElement.style.top = terrariumElement.offsetTop - pos2 + 'px'`: 기존 테라리움 요소 위치에서 y축 변화량을 적용한다.
* `terrariumElement.style.left = terrariumElement.offsetLeft - pos1 + 'px'`: 기존 테라리움 요소 위치에서 x축 변화량을 적용한다.
