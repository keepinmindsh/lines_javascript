# Vanilla JS

Vanilla JS는 자바스크립트 프레임워크로 다른 프레임워크나 jQuery보다도 압도적으로 빠르고 웹표준을 잘 지키는 웹브라우저들에 대해서는 크로스 브라우징이 잘 되는 특성이 있다.   
페이스북, 구글, 유튜브 등등 유명한 해외 사이트에서 사용되었으며, 다른 플랫폼보다도 오래되었음에도 불구하고 꾸준히 업데이트 되어 온 프레임워크이다.   
용량도 매우 가벼워서 압축을 하는 경우 압축을 하지 않은 경우보다 용량이 더 나가는 역설적인 무게를 자랑한다.

### Query

```javascript 

// QuerySelector                   
const audio = document.querySelector(`audio[data-key="${e.keyCode}"]`);

// QeurySelectorAll
// Array.from 
const keys = Array.from(document.querySelectorAll('.key'));

// QuerySelectorAll 
const checkboxes = document.querySelectorAll('.inbox input[type="checkbox"]');                 

```

### CheckBox

```javascript

// QuerySelectorAll 
const checkboxes = document.querySelectorAll('.inbox input[type="checkbox"]');  
                  
let lastChecked;

function handleCheck(e) {
  // Check if they had the shift key down
  // AND check that they are checking it
  let inBetween = false;
  if (e.shiftKey && this.checked) {
    // go ahead and do what we please
    // loop over every single checkbox
    checkboxes.forEach(checkbox => {
      console.log(checkbox);
      if (checkbox === this || checkbox === lastChecked) {
        inBetween = !inBetween;
        console.log('Starting to check them in between!');
      }

      if (inBetween) {
        checkbox.checked = true;
      }
    });
  }

  lastChecked = this;
}

checkboxes.forEach(checkbox => checkbox.addEventListener('click', handleCheck));

```

### Key 감지

```javascript

const pressed = [];
const secretCode = 'wesbos';

// 이벤트 리스너를 활용 
window.addEventListener('keyup', (e) => {
  console.log(e.key);
  pressed.push(e.key);
  pressed.splice(-secretCode.length - 1, pressed.length - secretCode.length);
  if (pressed.join('').includes(secretCode)) {
    console.log('DING DING!');
    cornify_add();
  }
  console.log(pressed);
});

```

### Scroll과 Offset

```javascript

function debounce(func, wait = 20, immediate = true) {
  var timeout;
  return function() {
    var context = this, args = arguments;
    var later = function() {
      timeout = null;
      if (!immediate) func.apply(context, args);
    };
    var callNow = immediate && !timeout;
    clearTimeout(timeout);
    timeout = setTimeout(later, wait);
    if (callNow) func.apply(context, args);
  };
};

const sliderImages = document.querySelectorAll('.slide-in');

function checkSlide() {
  sliderImages.forEach(sliderImage => {
    // half way through the image
    const slideInAt = (window.scrollY + window.innerHeight) - sliderImage.height / 2;
    // bottom of the image
    const imageBottom = sliderImage.offsetTop + sliderImage.height;
    const isHalfShown = slideInAt > sliderImage.offsetTop;
    const isNotScrolledPast = window.scrollY < imageBottom;
    if (isHalfShown && isNotScrolledPast) {
      sliderImage.classList.add('active');
    } else {
      sliderImage.classList.remove('active');
    }
  });
}

window.addEventListener('scroll', debounce(checkSlide));              

```

### 참조와 복사( Reference & Copy )

```javascript

const players = ['Wes', 'Sarah', 'Ryan', 'Poppy'];

// and we want to make a copy of it.
const team = players;

console.log(players, team);

// or create a new array and concat the old one in
const team3 = [].concat(players);

// or use the new ES6 Spread
const team4 = [...players];
team4[3] = 'heeee hawww';
console.log(team4);

const team5 = Array.from(players);

// now when we update it, the original one isn't changed
// The same thing goes for objects, let's say we have a person object

// with Objects
const person = {
  name: 'Wes Bos',
  age: 80
};

// how do we take a copy instead?
const cap2 = Object.assign({}, person, { number: 99, age: 12 });
console.log(cap2);

// Things to note - this is only 1 level deep - both for Arrays and Objects. lodash has a cloneDeep method, but you should think twice before using it.

const wes = {
  name: 'Wes',
  age: 100,
  social: {
    twitter: '@wesbos',
    facebook: 'wesbos.developer'
  }
};

console.clear();
console.log(wes);

const dev = Object.assign({}, wes);

const dev2 = JSON.parse(JSON.stringify(wes));

```

### Local Storage

```javascript 

const addItems = document.querySelector('.add-items');
const itemsList = document.querySelector('.plates');
const items = JSON.parse(localStorage.getItem('items')) || [];

function addItem(e) {
  e.preventDefault();
  const text = (this.querySelector('[name=item]')).value;
  const item = {
    text,
    done: false
  };

  items.push(item);
  populateList(items, itemsList);
  localStorage.setItem('items', JSON.stringify(items));
  this.reset();
}

function populateList(plates = [], platesList) {
  platesList.innerHTML = plates.map((plate, i) => {
    return `
      <li>
        <input type="checkbox" data-index=${i} id="item${i}" ${plate.done ? 'checked' : ''} />
        <label for="item${i}">${plate.text}</label>
      </li>
    `;
  }).join('');
}

function toggleDone(e) {
  if (!e.target.matches('input')) return; // skip this unless it's an input
  const el = e.target;
  const index = el.dataset.index;
  items[index].done = !items[index].done;
  localStorage.setItem('items', JSON.stringify(items));
  populateList(items, itemsList);
}

addItems.addEventListener('submit', addItem);
itemsList.addEventListener('click', toggleDone);

populateList(items, itemsList);

```

### MouseMove & Shadow

```javascript 

const hero = document.querySelector('.hero');
const text = hero.querySelector('h1');

// 그림자가 확장될 수 있는 범위 
const walk = 800; // 500px

function shadow(e) {
  /*
    scrollHeight : 스크롤바 높이를 뺀 내용 전체의 높이

    clientHeight : 스크롤바 높이를 뺀 가시적인 높이

    offsetHeight : 스크롤바 높이를 포함한 가시적인 높이 
    
  */
  const { offsetWidth: width, offsetHeight: height } = hero;
  let { offsetX: x, offsetY: y } = e;

  if (this !== e.target) {
    x = x + e.target.offsetLeft;
    y = y + e.target.offsetTop;
  }

  const xWalk = Math.round((x / width * walk) - (walk / 2));
  const yWalk = Math.round((y / height * walk) - (walk / 2));

  text.style.textShadow = `
    ${xWalk}px ${yWalk}px 0 rgba(255,0,255,0.7),
    ${xWalk * -1}px ${yWalk}px 0 rgba(0,255,255,0.7),
    ${yWalk}px ${xWalk * -1}px 0 rgba(0,255,0,0.7),
    ${yWalk * -1}px ${xWalk}px 0 rgba(0,0,255,0.7)
  `;

}

hero.addEventListener('mousemove', shadow);

```

### Sorting

```javascript 

const bands = [ 
'The Plot in You', 
'The Devil Wears Prada', 
'Pierce the Veil', 
'Norma Jean', 
'The Bled', 
'Say Anything', 
'The Midway State', 
'We Came as Romans', 
'Counterparts', 
'Oh, Sleeper', 
'A Skylit Drive', 
'Anywhere But Here', 
'An Old Dog' ];

function strip(bandName) {
  return bandName.replace(/^(a |the |an )/i, '').trim();
}

// sorting을 처리하는 함수 
const sortedBands = bands.sort((a, b) => strip(a) > strip(b) ? 1 : -1);

document.querySelector('#bands').innerHTML =
  sortedBands
    .map(band => `<li>${band}</li>`)
    .join('');

console.log(sortedBands);

```

### Map & Reduce

```javascript

const timeNodes = Array.from(document.querySelectorAll('[data-time]'));

const seconds = timeNodes
  .map(node => node.dataset.time)
  .map(timeCode => {
    const [mins, secs] = timeCode.split(':').map(parseFloat);
    return (mins * 60) + secs;
  })
  .reduce((total, vidSeconds) => total + vidSeconds);

let secondsLeft = seconds;
const hours = Math.floor(secondsLeft / 3600);
secondsLeft = secondsLeft % 3600;

const mins = Math.floor(secondsLeft / 60);
secondsLeft = secondsLeft % 60;

console.log(hours, mins, secondsLeft);

```

reduce() 메서드는 배열의 각 요소에 대해 주어진 리듀서(reducer) 함수를 실행하고, 하나의 결과값을 반환합니다.


### Speech Detection

```javascript

window.SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;

const recognition = new SpeechRecognition();
recognition.interimResults = true;
recognition.lang = 'en-US';

let p = document.createElement('p');
const words = document.querySelector('.words');
words.appendChild(p);

recognition.addEventListener('result', e => {
  const transcript = Array.from(e.results)
    .map(result => result[0])
    .map(result => result.transcript)
    .join('');

    const poopScript = transcript.replace(/poop|poo|shit|dump/gi, '💩');
    p.textContent = poopScript;

    if (e.results[0].isFinal) {
      p = document.createElement('p');
      words.appendChild(p);
    }
});

recognition.addEventListener('end', recognition.start);

recognition.start();
                

```

```javascript 

const array1 = [1, 2, 3, 4];
const reducer = (accumulator, currentValue) => accumulator + currentValue;

// 1 + 2 + 3 + 4
console.log(array1.reduce(reducer));
// expected output: 10

// 5 + 1 + 2 + 3 + 4
console.log(array1.reduce(reducer, 5));
// expected output: 15

// 빈요소를 제외하고 배열 내에 존재하는 각 요소에 대해서 callback 함수를 한번씩 실행하는데, 콜백함수는 다음의 네 인수를 받습니다. 
// accumulator - 누산기accmulator는 콜백의 반환값을 누적합니다. 콜백의 이전 반환값 또는, 콜백의 첫 번째 호출이면서 initialValue를 제공한 경우에는 initialValue의 값입니다.
// currentValue - 처리할 현재 요소.
// currentIndex - 처리할 현재 요소의 인덱스. initialValue를 제공한 경우 0, 아니면 1부터 시작합니다.
// array - reduce()를 호출한 배열.
[0, 1, 2, 3, 4].reduce(function(accumulator, currentValue, currentIndex, array) {
  return accumulator + currentValue;
}, 10);

```

### Link highlighter

```javascript

const triggers = document.querySelectorAll('a');
const highlight = document.createElement('span');
highlight.classList.add('highlight');
document.body.appendChild(highlight);

function highlightLink() {
  const linkCoords = this.getBoundingClientRect();
  console.log(linkCoords);
  const coords = {
    width: linkCoords.width,
    height: linkCoords.height,
    top: linkCoords.top + window.scrollY,
    left: linkCoords.left + window.scrollX
  };

  highlight.style.width = `${coords.width}px`;
  highlight.style.height = `${coords.height}px`;
  highlight.style.transform = `translate(${coords.left}px, ${coords.top}px)`;

}

triggers.forEach(a => a.addEventListener('mouseenter', highlightLink));

```

### Event Capture, Propagation, Bublling and Once

```javascript 

const divs = document.querySelectorAll('div');
const button = document.querySelector('button');

function logHelloWorld(e) {
  console.log(this.classList.value);
  // e.stopPropagation(); // stop bubbling!
  // console.log(this);
}

// 실행시 한번만 실행되기하기 위한 Event 처리 
divs.forEach(div => div.addEventListener(
  'click', 
  logHelloWorld, 
  {
    capture: false,
    once: true
  }));

button.addEventListener('click', 
() => {
  console.log('Click!!!');
}, {
  once: true
});

```

### Stripe Follow Along Nav

```javascript

const triggers = document.querySelectorAll('.cool > li');
const background  = document.querySelector('.dropdownBackground');
const nav  = document.querySelector('.top');

function handleEnter() {
  this.classList.add('trigger-enter');
  setTimeout(() => this.classList.contains('trigger-enter') && this.classList.add('trigger-enter-active'), 150);
  background.classList.add('open');

  const dropdown = this.querySelector('.dropdown');
  
  /**

  출력된 결과를 보면 top, bottom, left, right, width, height, x, y의 값이 출력됩니다.

  top or y // 화면 상단 부터 대상의 처음 위치 값
  bottom // 화면 상단 부터 대상의 끝 위치 값
  left or x // 화면 좌측 부터 대상의 처음 위치 값
  right // 화면 좌측 부터 대상의 끝 위치 값
  width // 대상의 길이
  height // 대상의 높이
  
  이와 같이 간단하게 대상 엘리먼트의 위치 정보를 얻을 수 있습니다.

  */
  const dropdownCoords = dropdown.getBoundingClientRect();
  const navCoords = nav.getBoundingClientRect();

  const coords = {
    height: dropdownCoords.height,
    width: dropdownCoords.width,
    top: dropdownCoords.top - navCoords.top,
    left: dropdownCoords.left - navCoords.left
  };

  background.style.setProperty('width', `${coords.width}px`);
  background.style.setProperty('height', `${coords.height}px`);
  background.style.setProperty('transform', `translate(${coords.left}px, ${coords.top}px)`);
}

function handleLeave() {
  this.classList.remove('trigger-enter', 'trigger-enter-active');
  background.classList.remove('open');
}

triggers.forEach(trigger => trigger.addEventListener('mouseenter', handleEnter));
triggers.forEach(trigger => trigger.addEventListener('mouseleave', handleLeave));

```

### Click And Drag

```javascript 

/**

 Java Script의 절대 위치, 상대 위치  ( 출처: https://mommoo.tistory.com/85 [개발자로 홀로 서기] )
 
  절대 위치 : 시작점으로 부터 떨어진 위치. 
  상대 위치 : 어떤 기준 점으로 부터 떨어진 위치.

  브라우저 상에서 컨텐츠의 내용이 보여지는 부분이 ViewPort이며, 그외의 안보여지는 부분이 존재합니다. 
  ViewPort 의 위치는 화면의 스크롤링이 일어남에 따라 점점 내용 및 위치가 변경될 수 있습니다. 

  절대위치는 ViewPort 의 위치가 아닌, 실제 페이지(컨텐츠)으로 부터 떨어진 거리를 의미합니다. 
  상대위치는 어떤 기준을 선택하느냐에 따라서 기준 위치가 달라지겠지만, 브라우저가 보여주는 스크롤된 페이지의 위치부분, 즉 ViewPort 의 시작지점을 기준으로 가정하면, 현재 보여지는 페이지의 위치가 시작지점이 됩니다.     
  
  const target = document.getElementById('position'); 
  const clientRect = target.getBoundingClientRect(); // DomRect 구하기 (각종 좌표값이 들어있는 객체)
  const relativeTop = clientRect.top; // Viewport의 시작지점을 기준으로한 상대좌표 Y 값.

  부모 요소의 시작 지점 기준 

   부모 요소의 position 정책에 따라서 offsetTop이 반환하는 위치가 달라지게 됩니다. 
     position:relative 일 경우, offsetTop은 부모요소를 기점으로 상대위치를 가져올 수 있습니다. 
     position:absolute 일 경우, offsetTop은 부모요소의 기준이 아닌 상위 요소의 포지셔닝을 계속 탐색하여 relative가 있는 부모를 기준으로 좌표값을 계산합니다. 
     만약 자식 요소의 상위 요소들이 전무 relative 포지셔닝이 아니라면, 결국 offsetTop은 페이지의 시작지점 , 절대위치를 반환하게 됩니다. 

     offsetTop, offsetLeft, offsetWidth, offsetHeight 가 요소로 존재합니다. 

  절대좌표 구하기 

  const target = document.getElementById('target'); // 요소의 id 값이 target이라 가정
​
  const clientRect = target.getBoundingClientRect(); // DomRect 구하기 (각종 좌표값이 들어있는 객체)
  const relativeTop = clientRect.top; // Viewport의 시작지점을 기준으로한 상대좌표 Y 값.
  ​
  const scrolledTopLength = window.pageYOffset; // 스크롤된 길이
  //const scrolledTopLength = pageYOffset; // window 객체 없이 pageYOffset 메서드를 써도 가능하다.
  const absoluteTop = scrolledTopLength + relativeTop; // 절대좌표

  절대좌표 구하기 
  const absoluteTop = window.pageYOffset + element.getBoundingClientRect().top;

  상대좌표 구하기 
  const relativeTop = element.getBoundingClientRect().top;

  부모 요소의 시작지점을 기준으로 한 상대 좌표 
  // 1번에서 안내한 절대좌표 구하는 방법을 함수로 구현
  function getAbsoluteTop(element) {
      return window.pageYOffset + element.getBoundingClientRect().top;
  }
  ​
  const parentElement = element.parentElement;
  const parentAbsoluteTop = getAbsoluteTop(parentElement);
  const absoulteTop = getAbsoluteTop(element);
  ​
  const relativeTop = absoluteTop - parentAbsoluteTop;

*/

const slider = document.querySelector('.items');
let isDown = false;
let startX;
let scrollLeft;

slider.addEventListener('mousedown', (e) => {
  isDown = true;
  slider.classList.add('active');
  startX = e.pageX - slider.offsetLeft;
  scrollLeft = slider.scrollLeft;
});

slider.addEventListener('mouseleave', () => {
  isDown = false;
  slider.classList.remove('active');
});

slider.addEventListener('mouseup', () => {
  isDown = false;
  slider.classList.remove('active');
});

slider.addEventListener('mousemove', (e) => {
  if (!isDown) return;  // stop the fn from running
  e.preventDefault();
  const x = e.pageX - slider.offsetLeft;
  const walk = (x - startX) * 3;
  slider.scrollLeft = scrollLeft - walk;
});

```

### Video Speed Controller

```javascript

const speed = document.querySelector('.speed');
const bar = speed.querySelector('.speed-bar');
const video = document.querySelector('.flex');

function handleMove(e) {
    const y = e.pageY - this.offsetTop;
    const percent = y / this.offsetHeight;
    const min = 0.4;
    const max = 4;
    const height = Math.round(percent * 100) + '%';
    const playbackRate = percent * (max - min) + min;
    bar.style.height = height;
    bar.textContent = playbackRate.toFixed(2) + '×';
    video.playbackRate = playbackRate;
  }

speed.addEventListener('mousemove', handleMove);

```

### Arrays

```javascript

// addEventLitner 
keys.forEach(key => key.addEventListener('transitionend', removeTransition)

// window addEventListener
window.addEventListener('keydown', playSound);

// Date Object 
const now = new Date();
const seconds = now.getSeconds();

// Attribute Setting
const hourHand = document.querySelector('.hour-hand');
hourHand.style.transform = `rotate(${hourDegrees}deg)`;

// Css Handling 
<div class="controls">
  <label for="spacing">Spacing:</label>
  <input id="spacing" type="range" name="spacing" min="10" max="200" value="10" data-sizing="px">

const inputs = document.querySelectorAll('.controls input');

function handleUpdate() {
  const suffix = this.dataset.sizing || '';
  document.documentElement.style.setProperty(`--${this.name}`, this.value + suffix);
}

inputs.forEach(input => input.addEventListener('change', handleUpdate));

// Array Handling 
const inventors = [
      { first: 'Albert', last: 'Einstein', year: 1879, passed: 1955 },
      { first: 'Isaac', last: 'Newton', year: 1643, passed: 1727 },
      { first: 'Galileo', last: 'Galilei', year: 1564, passed: 1642 },
      { first: 'Marie', last: 'Curie', year: 1867, passed: 1934 },
      { first: 'Johannes', last: 'Kepler', year: 1571, passed: 1630 },
      { first: 'Nicolaus', last: 'Copernicus', year: 1473, passed: 1543 },
      { first: 'Max', last: 'Planck', year: 1858, passed: 1947 },
      { first: 'Katherine', last: 'Blodgett', year: 1898, passed: 1979 },
      { first: 'Ada', last: 'Lovelace', year: 1815, passed: 1852 },
      { first: 'Sarah E.', last: 'Goode', year: 1855, passed: 1905 },
      { first: 'Lise', last: 'Meitner', year: 1878, passed: 1968 },
      { first: 'Hanna', last: 'Hammarström', year: 1829, passed: 1909 }
    ];

const people = [
  'Bernhard, Sandra', 'Bethea, Erin', 'Becker, Carl', 'Bentsen, Lloyd', 'Beckett, Samuel', 'Blake, William', 'Berger, Ric', 'Beddoes, Mick', 'Beethoven, Ludwig',
  'Belloc, Hilaire', 'Begin, Menachem', 'Bellow, Saul', 'Benchley, Robert', 'Blair, Robert', 'Benenson, Peter', 'Benjamin, Walter', 'Berlin, Irving',
  'Benn, Tony', 'Benson, Leana', 'Bent, Silas', 'Berle, Milton', 'Berry, Halle', 'Biko, Steve', 'Beck, Glenn', 'Bergman, Ingmar', 'Black, Elk', 'Berio, Luciano',
  'Berne, Eric', 'Berra, Yogi', 'Berry, Wendell', 'Bevan, Aneurin', 'Ben-Gurion, David', 'Bevel, Ken', 'Biden, Joseph', 'Bennington, Chester', 'Bierce, Ambrose',
  'Billings, Josh', 'Birrell, Augustine', 'Blair, Tony', 'Beecher, Henry', 'Biondo, Frank'
];

// Array.prototype.filter()
const fifteen = inventors.filter(inventor => (inventor.year >= 1500 && inventor.year < 1600));

// Array.prototype.map()
const fullNames = inventors.map(inventor => `${inventor.first} ${inventor.last}`);

// Array.prototype.sort()
const ordered = inventors.sort((a, b) => a.year > b.year ? 1 : -1);

// Array.prototype.sort()
const oldest = inventors.sort(function(a, b) {
  const lastInventor = a.passed - a.year;
  const nextInventor = b.passed - b.year;
  return lastInventor > nextInventor ? -1 : 1;
});

// Array.prototype.reduce()
const data = ['car', 'car', 'truck', 'truck', 'bike', 'walk', 'car', 'van', 'bike', 'walk', 'car', 'van', 'car', 'truck', 'pogostick'];

const transportation = data.reduce(function(obj, item) {
  if (!obj[item]) {
    obj[item] = 0;
  }
  obj[item]++;
  return obj;
}, {});

```

```javascript

const panels = document.querySelectorAll('.panel');

// css에 대한 toggle event
function toggleOpen() {
  console.log('Hello');
  this.classList.toggle('open');
}

function toggleActive(e) {
  console.log(e.propertyName);
  if (e.propertyName.includes('flex')) {
    this.classList.toggle('open-active');
  }
}

// input element 전체에 대한 Event Listener 등록 
panels.forEach(panel => panel.addEventListener('click', toggleOpen));
panels.forEach(panel => panel.addEventListener('transitionend', toggleActive));


// fetch 
const endpoint = 'https://gist.githubusercontent.com/Miserlou/c5cd8364bf9b2420bb29/raw/2bf258763cdddd704f8ffd3ea9a3e81d25e2c6f6/cities.json';

const cities = [];
fetch(endpoint)
  .then(blob => blob.json())
  .then(data => cities.push(...data));


const html = matchArray.map(place => {
  // 정규 표현식 적용 
  const regex = new RegExp(this.value, 'gi');

  const cityName = place.city.replace(regex, `<span class="hl">${this.value}</span>`);
  const stateName = place.state.replace(regex, `<span class="hl">${this.value}</span>`);

  return `
    <li>
      <span class="name">${cityName}, ${stateName}</span>
      <span class="population">${numberWithCommas(place.population)}</span>
    </li>
  `;

  // 조인 함수 
}).join('');

// Array.prototype.every() // is everyone 19?
// every() 메서드는 배열 안의 모든 요소가 주어진 판별 함수를 통과하는지 테스트합니다. 하나라도 틀린 값이 있으면 False 입니다. 
const allAdults = people.every(person => ((new Date()).getFullYear()) - person.year >= 19);
console.log({allAdults});

// Array.prototype.find()
// Find is like filter, but instead returns just the one you are looking for
// find the comment with the ID of 823423
const comment = comments.find(comment => comment.id === 823423);

// Array.prototype.findIndex()
// Find the comment with this ID
// delete the comment with the ID of 823423
const index = comments.findIndex(comment => comment.id === 823423);
console.log(index);

// comments.splice(index, 1);
// slice() 메서드는 어떤 배열의 begin부터 end까지(end 미포함)에 대한 얕은 복사본을 새로운 배열 객체로 반환합니다. 원본 배열은 바뀌지 않습니다.
const newComments = [
  ...comments.slice(0, index),
  ...comments.slice(index + 1)
];

```

```javascript

// Canvas 객체의 활용
<canvas id="draw" width="800" height="800"></canvas>

const canvas = document.querySelector('#draw');
/*

  캔버스의 드로잉 컨텍스트를 정의하는 컨텍스트 식별자가 포함된 DOMString입니다:

  > "2d"는 2차원 렌더링 컨텍스트를 표현하는 CanvasRenderingContext2D 객체를 생성합니다.
  > "webgl"는 3차원 렌더링 컨텍스트를 표현하는 WebGLRenderingContext 객체를 생성합니다. 이 컨텍스트는 WebGL 버전 1(OpenGL ES 2.0)을 지원하는 브라우저에서만 사용 가능합니다.
  > "webgl2"는 3차원 렌더링 컨텍스트를 표현하는 WebGL2RenderingContext 객체를 생성합니다. 이 컨텍스트는 WebGL 버전 2 (OpenGL ES 3.0)를 지원하는 브라우저에서만 사용 가능합니다.  
  > "bitmaprenderer"는 주어진 ImageBitmap을 캔버스의 내용 대신 전환하는 함수를 제공하는 ImageBitmapRenderingContext를 생성합니다. 
  > 2d 렌더링을 위한 참고 링크 = https://developer.mozilla.org/ko/docs/Web/API/CanvasRenderingContext2D
*/
const ctx = canvas.getContext('2d');
ctx.strokeStyle = '#BADA55';
ctx.lineJoin = 'round';
ctx.lineCap = 'round';
ctx.lineWidth = 100;

function draw(e) {
  if (!isDrawing) return; // stop the fn from running when they are not moused down
  console.log(e);
  ctx.strokeStyle = `hsl(${hue}, 100%, 50%)`;
  ctx.beginPath();
  // start from
  ctx.moveTo(lastX, lastY);
  // go to
  ctx.lineTo(e.offsetX, e.offsetY);
  ctx.stroke();
  [lastX, lastY] = [e.offsetX, e.offsetY];

  hue++;
  if (hue >= 360) {
    hue = 0;
  }
  if (ctx.lineWidth >= 100 || ctx.lineWidth <= 1) {
    direction = !direction;
  }

  if(direction) {
    ctx.lineWidth++;
  } else {
    ctx.lineWidth--;
  }

}

// 2d 샘플 참고 
<canvas id="my-house" width="300" height="300"></canvas>
const canvas = document.getElementById('my-house');
const ctx = canvas.getContext('2d');

// Set line width
ctx.lineWidth = 10;

// Wall
ctx.strokeRect(75, 140, 150, 110);

// Door
ctx.fillRect(130, 190, 40, 60);

// Roof
ctx.beginPath();
ctx.moveTo(50, 140);
ctx.lineTo(150, 60);
ctx.lineTo(250, 140);
ctx.closePath();
ctx.stroke();

// 브라우저 윈도우의 뷰포트 너비로, 수직 스크롤바가 존재한다면 포함합니다.
canvas.width = window.innerWidth;

// The read-only innerHeight property of the Window interface returns the interior height of the window in pixels, 
// including the height of the horizontal scroll bar, if present.
canvas.height = window.innerHeight;

// Style Options
// CSS - https://developer.mozilla.org/ko/docs/Web/CSS
const p = document.querySelector('p');
p.style.color = '#BADA55';
p.style.fontSize = '50px';


// QuerySelector 에서 객체를 가져와 style를 적용하는 경우 
function makeGreen() {
  const p = document.querySelector('p');
  p.style.color = '#BADA55';
  p.style.fontSize = '50px';
}

```

```javascript 

// Console 로그의 종류, Regular
console.log('hello');

// Interpolated
console.log('Hello I am a %s string!', '💩');

// Styled
console.log('%c I am some great text', 'font-size:50px; background:red; text-shadow: 10px 10px 0 blue')

// warning!
console.warn('OH NOOO');

// Error 
console.error('Shit!');

// Info
console.info('Crocodiles eat 3-4 people per year');

// Testing
const p = document.querySelector('p');

console.assert(p.classList.contains('ouch'), 'That is wrong!');

// Clearing
console.clear();

// Viewing DOM Elements
console.log(p);
console.dir(p);

console.clear();

// Grouping together
dogs.forEach(dog => {
  console.groupCollapsed(`${dog.name}`);
  console.log(`This is ${dog.name}`);
  console.log(`${dog.name} is ${dog.age} years old`);
  console.log(`${dog.name} is ${dog.age * 7} dog years old`);
  console.groupEnd(`${dog.name}`);
});

// Counting
console.count('Wes');
console.count('Wes');
console.count('Steve');
console.count('Steve');
console.count('Wes');
console.count('Steve');
console.count('Wes');
console.count('Steve');
console.count('Steve');
console.count('Steve');
console.count('Steve');
console.count('Steve');

// Timing
console.time('fetching data');
fetch('https://api.github.com/users/wesbos')
  .then(data => data.json())
  .then(data => {
    console.timeEnd('fetching data');
    console.log(data);
  });

console.table(dogs);

```