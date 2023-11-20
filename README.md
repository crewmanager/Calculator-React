## Mobile-Calcy in (REACT)


`main.mo`

```js
import Float "mo:base/Float";
actor Calculator {

  var num : Float = 0;

  public func add(val1 : Float, val2 : Float) : async Float {
    num := val1 +val2;
    return num;
  };

  public func subtract(val1 : Float, val2 : Float) : async Float {
    num := val1 -val2;
    return num;
  };

  public func multiply(val1 : Float, val2 : Float) : async Float {
    num := val1 * val2;
    return num;
  };

  public func divison(val1 : Float, val2 : Float) : async ?Float {
    if (val2 == 0) {
      return null;
    } else {
      num := val1 / val2;
      return ?num;
    };
  };

  public func clearAll() : async Float {
    num := 0;
    return num;
  };

};
```

`main.css`

```css
body {
  margin: 0;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen",
    "Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue",
    sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

code {
  font-family: source-code-pro, Menlo, Monaco, Consolas, "Courier New",
    monospace;
}

.App {
  width: 390px;
  margin: 100px auto;
  align-items: center;
}

.screen {
  width: 300px;
  height: 80px;
  text-align: right;
  border: 3px solid rgb(88, 87, 87);
  font-size: 30px;
  padding: 10px;
  border-radius: 10px;
  background-color: white;
}

.button {
  width: 80px;
  height: 80px;
  background-color: black;
  color: white;
  border-radius: 10px;
  font-size: 25px;
}

body {
  background-color: lightgray;
}

.btn {
  width: 160px;
  background-color: white;
  color: black;
}

.opr {
  background-color: rgb(158, 55, 7);
}

.btn2 {
  background-color: rgb(65, 63, 63);
}

.button1 {
  width: 160px;
  background-color: yellowgreen;
}
```
`index.html`

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>calcy</title>
    <base href="/" />
    <link rel="icon" href="favicon.ico" />
    <link type="text/css" rel="stylesheet" href="main.css" />
  	<link id="external-css" rel="stylesheet" type="text/css" href="https://fonts.googleapis.com/css?family=Montserrat&amp;display=swap" media="all">
</head>
  <body>
    <div id="root"></div>
  </body>
</html>

```

`index.jsx`

```js
import React from "react";
import ReactDOM from "react-dom";
import App from "./Components/App";

ReactDOM.render(<App />, document.getElementById("root"))

```

`App.jsx`

```js
import React, { useState } from "react";
import { calcy_backend } from "../../../declarations/calcy_backend";

function App() {
  const [curr, setCurr] = useState("");
  const [prev, setPrev] = useState("");
  const [operand, setOperand] = useState("");

  const numHandler = (e) => {
    if (curr.includes(".") && e.target.value === ".") return;
    curr ? setCurr((prev) => prev + e.target.value) : setCurr(e.target.value);
  };

  const clearHandler = async () => {
    await calcy_backend.clearAll();
    setCurr("");
    setPrev("");
    setOperand("");
  };
  const clearLastHandler = () => {
    if (operand) return;
    let res = curr.slice(0, -1);
    setCurr(res);
  };

  const operandHandler = (e) => {
    setOperand(e.target.value);
    if (curr === "") return;
    if (prev !== "") calculate();
    else {
      setPrev(curr);
      setCurr("");
    }
  };
  const calculate = async () => {
    let cal, val1, val2;
    switch (operand) {
      case "+":
        val1 = Number(prev);
        val2 = Number(curr);
        cal = String(await calcy_backend.add(val1, val2));
        break;

      case "-":
        val1 = Number(prev);
        val2 = Number(curr);
        cal = String(await calcy_backend.subtract(val1, val2));
        break;

      case "*":
        val1 = Number(prev);
        val2 = Number(curr);
        cal = String(await calcy_backend.multiply(val1, val2));
        break;

      case "/":
        val1 = Number(prev);
        val2 = Number(curr);
        cal = String(await calcy_backend.divison(val1, val2));
        break;
      default:
        return;
    }
    console.log("cal", cal);

    setPrev(cal);
    setCurr("");
  };

  let numArray = [];
  for (let i = 0; i <= 9; i++) {
    numArray.push(i);
  }
  numArray.push(".");

  const operandsss = ["+", "-", "*", "/"];

  return (
    <div className="App">
      <h1>calculator</h1>
      <div className="screen">
        <span>{prev}</span>
        <span>{operand}</span>
        <span>{curr}</span>
      </div>
      {operandsss.map((opr, index) => (
        <button
          key={index}
          value={opr}
          className="button opr"
          onClick={operandHandler}
        >
          {opr}
        </button>
      ))}
      {numArray.map((num, index) => (
        <button key={index} value={num} className="button" onClick={numHandler}>
          {num}
        </button>
      ))}
      <input
        type="button"
        value="X"
        className="button btn2"
        onClick={clearLastHandler}
      />
      <input
        type="button"
        value="AC"
        className="button btn"
        onClick={clearHandler}
      />
      <input
        type="button"
        value="="
        className="button button1"
        onClick={calculate}
      />
    </div>
  );
}

export default App;
```

`package.json`

```js
// change your depedencies with this in package.json

"dependencies": {
    "@dfinity/agent": "^0.19.3",
    "@dfinity/candid": "^0.19.3",
    "@dfinity/principal": "^0.19.3",
    "@emotion/react": "^11.11.1",
    "@emotion/styled": "^11.11.0",
    "@material-ui/core": "^4.12.4",
    "@material-ui/icons": "^4.11.3",
    "@mui/icons-material": "^5.14.14",
    "@mui/material": "^5.14.14",
    "autoprefixer": "^10.4.16",
    "postcss": "^8.4.31",
    "react": "^17.0.2",
    "react-dom": "^17.0.2",
    "ts-loader": "^9.5.0"
  },
```

`tsconfig.json`

```js
//add this where webpack.config.js is present 

{
    "compilerOptions": {
      "target": "es2018",        /* Specify ECMAScript target version: 'ES3' (default), 'ES5', 'ES2015', 'ES2016', 'ES2017', 'ES2018', 'ES2019' or 'ESNEXT'. */
      "lib": ["ES2018", "DOM"],  /* Specify library files to be included in the compilation. */
      "allowJs": true,           /* Allow javascript files to be compiled. */
      "jsx": "react",            /* Specify JSX code generation: 'preserve', 'react-native', or 'react'. */
    },
    "include": ["src/**/*"]
  }

```
