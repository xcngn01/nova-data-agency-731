# 拖动选择正确选项



## 说明

多选题变体：拖动画圈到正确选项上。类似考试涂答题卡。



## 代码

```html

<!DOCTYPE html>

<html lang="zh">

<head><meta charset="UTF-8"><title>拖动答题</title>

<style>

body{font-family:Arial;max-width:600px;margin:30px auto;background:#fafafa}

.question{background:white;padding:20px;margin:15px 0;border-radius:12px;box-shadow:0 2px 8px rgba(0,0,0,0.08)}

.q-title{font-size:18px;margin-bottom:10px;font-weight:bold}

.options{display:flex;flex-direction:column;gap:8px}

.option{padding:10px 15px;border:2px solid #ddd;border-radius:8px;display:flex;align-items:center;gap:8px}

.target{width:30px;height:30px;border-radius:50%;border:2px dashed #4CAF50;display:flex;align-items:center;justify-content:center}

.target.over{background:#e8f5e9}

.circle{width:30px;height:30px;border-radius:50%;background:#4CAF50;cursor:grab}

#score{text-align:center;font-size:24px}

button{display:block;margin:15px auto;padding:10px 30px;background:#2196F3;color:white;border:none;border-radius:8px;cursor:pointer;font-size:16px}

</style></head>

<body>

<h2 style="text-align:center">✏️ 拖动圆点到答案处</h2>

<div id="score">答对: 0</div>

<div id="questions"></div>

<button onclick="resetQuiz()">重新出题</button>



<script>

const quizData = [

  { q: "1 + 1 = ?", options: ["1", "2", "3", "4"], answer: 1 },

  { q: "中国的首都是？", options: ["上海", "北京", "广州", "深圳"], answer: 1 },

  { q: "水的化学式是？", options: ["CO2", "NaCl", "H2O", "O2"], answer: 2 },

  { q: "哪种语言主要用于 Web？", options: ["Python", "C++", "Java", "JavaScript"], answer: 3 },

];



let score = 0;

let answered = new Set();



function render() {

  let container = document.getElementById("questions");

  container.innerHTML = "";

  quizData.forEach((item, qi) => {

    let qdiv = document.createElement("div");

    qdiv.className = "question";

    qdiv.innerHTML = `<div class="q-title">${qi+1}. ${item.q}</div><div class="options" id="opts-${qi}"></div>`;



    let optsDiv = qdiv.querySelector(`#opts-${qi}`);

    item.options.forEach((opt, oi) => {

      let optDiv = document.createElement("div");

      optDiv.className = "option";



      let target = document.createElement("div");

      target.className = "target";

      target.id = `target-${qi}-${oi}`;



      let label = document.createElement("span");

      label.textContent = String.fromCharCode(65+oi) + ". " + opt;



      optDiv.appendChild(target);

      optDiv.appendChild(label);



      optsDiv.appendChild(optDiv);

    });



    // 拖动圆圈

    let circle = document.createElement("div");

    circle.className = "circle";

    circle.draggable = true;

    circle.id = `circle-${qi}`;

    circle.addEventListener("dragstart", e => e.dataTransfer.setData("text", qi));

    qdiv.prepend(circle);



    // 给每个 target 绑事件

    setTimeout(() => {

      qdiv.querySelectorAll(".target").forEach(t => {

        let [_, qi2, oi2] = t.id.split("-");

        t.addEventListener("dragover", e => { e.preventDefault(); t.classList.add("over"); });

        t.addEventListener("dragleave", () => t.classList.remove("over"));

        t.addEventListener("drop", e => {

          e.preventDefault(); t.classList.remove("over");

          if (answered.has(parseInt(qi2))) return;

          let correct = parseInt(oi2) === item.answer;

          answered.add(parseInt(qi2));

          document.getElementById(`circle-${qi2}`).style.background = correct ? "#4CAF50" : "#f44336";

          qdiv.querySelectorAll(".target").forEach(t2 => t2.style.borderColor = "#ddd");

          t.style.borderColor = correct ? "#4CAF50" : "#f44336";

          if (correct) score++;

          document.getElementById("score").textContent = "答对: " + score;

        });

      });

    }, 0);



    container.appendChild(qdiv);

  });

}



function resetQuiz() {

  score = 0; answered.clear();

  document.getElementById("score").textContent = "答对: 0";

  render();

}



render();

</script></body></html>

```

