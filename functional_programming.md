---
title: Functional Programming
tags: Knowledge
description: View the slide with "Slide Mode".
---

# Functional Programming!

###### https://chekai7.github.io/c.c.k

---

What is functional programing ? 
  >>>programing paradigm

---

Imperative vs Declarative

```javascript
const num = [1, 2, 4, 5], total = 0;
for (const i = 0 ; i < num.length ; i++) {
  total += num[i];
}
```
```javascript
const num = [1, 2, 4, 5], total= 0;
total = num.sum();
```

---



- Object-Oriented
- Functional :heart: 



---


What is function programing ? 
  >- mindset


---


### 為什麼要學 function programing ? 
  ![image alt](https://blog.jerry-hong.com/static/terrible-world-ff336b116f3e43149be6532ba173e034.gif)
  

---


JavaScript Libraries
- Ramda
- lodash/fp     :heavy_check_mark:
- functional.js
- Immutable.js

---

OK, Let do it!

---

####  Do everything with Function
> input => output

---

```javascript
const name = "kai";
const greeting ="Hi, I'm "

console.log(greeting+name)
// Hi, I'm kai
```


---

```javascript
function greeting(name){
  return "Hi,I'm "+ name
}

greeting("kai")
// Hi, I'm kai

```


---



## Avoid side effect
##### =>use <font color="#blue">pure</font> function 


---

Not pure:
```javascript
const naem = "kai";

function greeting(){
  console.log( "Hi,I'm "+ name)
}

greeting()

// Hi, I'm kai
```

---

pure:
```javascript

function greeting(name){
  return "Hi,I'm "+ name
}
```

---

## use higher-order function
##### =>function can be input/outout


---

```javascript
function makeAdjectifier(adjective) {
    return function(string) {
        return adjective + "" + string;
    };
}

const coolifier = makeAdjectifier("COOL");

console.log(coolifier("conference"));
// COOL conference

```


---

## Don't iterate
use map,filter,reduce

---

![image alt](https://i.redd.it/9dm5u0f5mrr41.jpg)

---

## Avoid mutability
##### use immutable data


---

Mutation:x:

```javascript
const rooms=["H1","H2","H3"]
rooms[2]="H4"

console.log(rooms)
// "H1","H2","H4"
```

---

No Mutation:o:

```javascript
const rooms=["H1","H2","H3"]
const newRooms=rooms.map((item)=>{
    if(item=="H3"){
        return "H4"
    }else{
         return item
    }

})

console.log(newRooms)
// "H1","H2","H4"

console.log(rooms)
// "H1","H2","H3"
```

---


## HOF 應用
## curried function 



---


```javascript
1.
const multiply = (x, y) => {
  return x * y;
};

multiply(5,4);

2.
const multiply = (x) => {
  return (y) => {
    return x * y;
  };
};

multiply(5)(4);

```

---

No curry 
```javascript
const uncurriedGradeTest=(passGrade, failGrade, average, testScore) =>
return testScore >= average ? passGrade : failGrade;

// Repeat the same args many times
uncurriedGradeTest( 'Pass', 'Fail', 60, 79 )
uncurriedGradeTest( 'Pass', 'Fail', 60, 39 )
uncurriedGradeTest( 'Pass', 'Fail', 60, 50 )
uncurriedGradeTest( 'Pass', 'Fail', 60, 21 )
uncurriedGradeTest( 'Pass', 'Fail', 60, 78 )
```



---

curry
```javascript
const getGradeTest = (passGrade, failGrade) => average => testScore => 
testScore >= average ? passGrade : failGrade;

const passFaillTest = getGradeTest( 'Pass', 'Fail' )( 60 );

passFaillTest( 79 )
passFaillTest( 39 )
passFaillTest( 50 )
passFaillTest( 21 )
passFaillTest( 78 )
```


---

## HOF 應用
## Point Free Style

---

```javascript
1.
[1, 2, 3, 4].map(n =>n ** 2)
// 1, 4, 9, 16

2.
const square = n => n ** 2

[1, 2, 3, 4].map(square)
// 1, 4, 9, 16

```


---


## 使用 Compose 開發
```javascript
const compose = (f,g) => (...arg) => f(g(...arg))
	
F(x) = f(g(x))

```

---

```javascript=
const exclaim = (x) => {
  return x + '!';
};

const toUpperCase = (x) => {
  return x.toUpperCase();
};


const shout = compose(exclaim, toUpperCase);

shout("send in the clowns");
//=> "SEND IN THE CLOWNS!"
```

---










## 結論

- 純函式
- 以 function 為最小單位思考
- 不改變 Input 的資料，易於除錯與維護



---



### Thank you! :sheep: 

You can find me on

- GitHub : 
  https://github.com/kai4idps
- Linkedin me : 
  https://www.linkedin.com/in/kai-cc-a445221b5/
- Blog: 
  https://chekai7.github.io/c.c.k
