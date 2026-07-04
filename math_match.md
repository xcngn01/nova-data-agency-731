# 拖拽数学匹配游戏



## 说明

将算式卡片拖到正确答案上完成匹配。训练小学生口算加减乘除。



## 代码

```html

<!DOCTYPE html>

<html lang="zh">

<head>

<meta charset="UTF-8">

<title>数学匹配游戏</title>

<style>

  body { font-family: Arial; text-align: center; margin: 20px; background: #e0f7fa; }

  .game { display: flex; justify-content: center; gap: 60px; margin-top: 30px; }

  .column { display: flex; flex-direction: column; gap: 15px; }

  .card { width: 120px; height: 60px; display: flex; align-items: center; justify-content: center; border-radius: 12px; font-size: 22px; font-weight: bold; }

  .problem { background: #ffcc80; border: 3px solid #ff9800; cursor: grab; }

  .answer { background: #a5d6a7; border: 3px solid #4CAF50; }

  .answer.over { background: #81c784; border-style: dashed; }

  .matched { background: #c8e6c9 !important; border-color: #388e3c !important; color: #1b5e20; }

  .hidden { visibility: hidden; }

  #score { font-size: 26px; margin: 20px; color: #00796b; }

  .new-game { padding: 12px 30px; background: #00796b; color: white; border: none; border-radius: 8px; font-size: 18px; cursor: pointer; }

</style>

</head>

<body>

<h2>🧮 数学匹配 - 拖算式到答案</h2>

<div id="score">得分: 0</div>

<div class="game">

  <div class="column" id="problems"></div>

  <div class="column" id="answers"></div>

</div>

<button class="new-game" onclick="newGame()">新游戏</button>



<script>

let score = 0;

let total = 0;



function generateProblems() {

  let list = [];

  for (let i = 0; i < 6; i++) {

    let a = Math.floor(Math.random() * 20) + 1;

    let b = Math.floor(Math.random() * 20) + 1;

    let ops = ["+", "-", "×"];

    let op = ops[Math.floor(Math.random() * ops.length)];

    let result;

    switch(op) {

      case "+": result = a + b; break;

      case "-": result = a - b; break;

      case "×": result = a * b; break;

    }

    list.push({ expr: `${a} ${op} ${b}`, result });

  }

  return list;

}



let problems = generateProblems();

total = problems.length;



function render() {

  let probDiv = document.getElementById("problems");

  let ansDiv = document.getElementById("answers");

  probDiv.innerHTML = "";

  ansDiv.innerHTML = "";



  problems.forEach((p, i) => {

    // 算式卡片

    let card = document.createElement("div");

    card.className = "card problem";

    card.textContent = p.expr + " = ?";

    card.draggable = true;

    card.dataset.idx = i;

    card.addEventListener("dragstart", e =>

      e.dataTransfer.setData("text/plain", i)

    );



    // 答案卡片

    let ans = document.createElement("div");

    ans.className = "card answer";

    ans.textContent = p.result;

    ans.dataset.expect = i; // 期望匹配的算式 index

    ans.addEventListener("dragover", e => e.preventDefault());

    ans.addEventListener("dragenter", e => ans.classList.add("over"));

    ans.addEventListener("dragleave", () => ans.classList.remove("over"));

    ans.addEventListener("drop", e => {

      e.preventDefault();

      ans.classList.remove("over");

      let probIdx = parseInt(e.dataTransfer.getData("text/plain"));

      if (probIdx === i) {

        // 正确！

        card.classList.add("matched");

        card.draggable = false;

        card.textContent = p.expr + " = " + p.result;

        ans.classList.add("matched");

        score++;

        document.getElementById("score").textContent = `得分: ${score} / ${total}`;

        if (score >= total) {

          setTimeout(() => alert("🎉 全部正确！"), 300);

        }

      }

    });



    probDiv.appendChild(card);

    ansDiv.appendChild(ans);

  });

}



function newGame() {

  problems = generateProblems();

  total = problems.length;

  score = 0;

  document.getElementById("score").textContent = "得分: 0";

  render();

}



render();

</script>

</body>

</html>

```



## 教学重点

- 随机生成数学题：`Math.random()` 产生随机数，动态组题

- 配对逻辑：每个答案 `dataset.expect` 对应唯一算式

- 视觉反馈：匹配成功后禁用拖拽 + 显示结果

- 多个卡片 + 多个答案的批量渲染技巧

