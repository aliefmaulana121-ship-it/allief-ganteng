# allief-ganteng
bikin web untuk pertanya pertanyaan  
from pathlib import Path
import zipfile

html = r'''<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0">
<title>Kenal Alief?</title>
<style>
*{box-sizing:border-box;margin:0;padding:0}
body{min-height:100vh;font-family:Arial,sans-serif;background:linear-gradient(#dff5ff,#c7eaff);color:#24384b}
.wrap{max-width:600px;min-height:100vh;margin:auto;padding:20px;position:relative}
.menu{position:absolute;right:20px;top:20px;width:56px;height:56px;border:0;border-radius:50%;background:#fff;font-size:26px;box-shadow:0 5px 18px #7db8d855}
.logo{text-align:center;margin-top:18px;font-weight:900;line-height:.85;transform:rotate(-3deg)}
.logo .a{font-size:58px;color:#ff77a5;-webkit-text-stroke:3px #402a22;text-shadow:3px 4px #fff}
.logo .b{font-size:43px;color:#69b9e8;-webkit-text-stroke:3px #402a22;text-shadow:3px 4px #fff;margin-top:8px}
.lang{width:225px;margin:45px auto 20px;background:#fff;border-radius:40px;padding:9px 12px 9px 20px;display:flex;align-items:center;justify-content:space-between;font-weight:bold;box-shadow:0 6px 20px #72b7dd44}
.lang button{width:45px;height:45px;border:0;border-radius:50%;background:#ff4e91;color:#fff;font-size:23px}
.char{text-align:right;font-size:92px;margin:0 35px -5px 0}
.card{background:#fffdf2;border:5px solid #3da0e9;border-radius:25px;padding:43px 26px 30px;transform:rotate(-3deg);box-shadow:0 9px #91cef2;position:relative}
.tape{position:absolute;left:25px;top:-25px;width:140px;height:48px;background:#ff91b3aa;transform:rotate(-7deg);border:2px solid #ff739d}
h1{font-size:36px;line-height:1.15;color:#192d43}
.pink{color:#ff5c91}.blue{color:#1597e5}
.sub{margin-top:15px;font-size:18px;font-weight:bold}
.q{margin-top:25px;background:#fff;border:3px solid #9bd5f6;border-radius:18px;padding:18px;font-size:22px;font-weight:800;line-height:1.25}
.answers{display:grid;gap:10px;margin-top:15px}
.answer{border:0;border-radius:15px;padding:13px;background:#e9f7ff;font-size:17px;font-weight:bold;cursor:pointer}
.answer:hover{background:#d4efff}
.next,.edit{border:0;border-radius:25px;padding:13px 22px;margin-top:18px;font-weight:bold;font-size:16px;cursor:pointer}
.next{background:#ff5794;color:#fff;box-shadow:0 4px #d83a71}
.edit{background:#fff;color:#1597e5;border:2px solid #1597e5;margin-left:7px}
.panel{display:none;margin-top:28px;background:#fff;border-radius:20px;padding:18px;transform:rotate(0deg)}
.panel h2{font-size:21px;margin-bottom:10px}
textarea{width:100%;min-height:170px;border:2px solid #9bd5f6;border-radius:12px;padding:12px;font:15px Arial}
.save{margin-top:10px;border:0;border-radius:20px;padding:11px 20px;background:#1597e5;color:#fff;font-weight:bold}
.msg{margin-top:12px;font-weight:bold;text-align:center}
@media(max-width:450px){h1{font-size:30px}.logo .a{font-size:52px}.char{font-size:78px}}
</style>
</head>
<body>
<div class="wrap">
<button class="menu" onclick="toggleEditor()">☰</button>

<div class="logo">
  <div class="a">Alief</div>
  <div class="b">Quiz 💗</div>
</div>

<div class="lang"><span>🌐 Indonesia</span><button>⌄</button></div>

<div class="char">🐼</div>

<section class="card">
<div class="tape"></div>
<h1>Seberapa kenal kamu sama <span class="pink">Alief</span>?</h1>
<p class="sub">Quiz santai buat kamu ✨</p>

<div class="q" id="question"></div>
<div class="answers" id="answers"></div>

<button class="next" onclick="nextQuestion()">Pertanyaan berikutnya →</button>
<button class="edit" onclick="toggleEditor()">Edit pertanyaan</button>

<div class="msg" id="msg"></div>

<div class="panel" id="panel">
<h2>Ganti pertanyaan</h2>
<p style="margin-bottom:10px">Satu baris = satu pertanyaan. Contoh:</p>
<textarea id="editor"></textarea>
<button class="save" onclick="saveQuestions()">Simpan pertanyaan</button>
</div>
</section>
</div>

<script>
const defaultQuestions=[
 "Apa makanan favorit Alief?",
 "Kalau Alief lagi santai, biasanya suka ngapain?",
 "Warna apa yang paling cocok dengan Alief?",
 "Menurut kamu, Alief paling suka genre musik apa?",
 "Kalau Alief boleh pilih, lebih suka jalan-jalan atau di rumah?",
 "Apa hal yang paling kamu ingat tentang Alief?"
];

let questions=JSON.parse(localStorage.getItem("aliefQuestions")||"null")||defaultQuestions;
let index=0;

function render(){
 document.getElementById("question").textContent=questions[index];
 const answers=["A. Tahu 😎","B. Lumayan tahu 👀","C. Belum tahu 😅","D. Mau cari tahu ✨"];
 document.getElementById("answers").innerHTML=answers.map(x=>`<button class="answer" onclick="choose('${x}')">${x}</button>`).join("");
 document.getElementById("msg").textContent=`Pertanyaan ${index+1} dari ${questions.length}`;
}
function choose(x){
 document.getElementById("msg").textContent="Jawaban kamu: "+x+" 💗";
}
function nextQuestion(){
 index=(index+1)%questions.length;
 render();
}
function toggleEditor(){
 const p=document.getElementById("panel");
 p.style.display=p.style.display==="block"?"none":"block";
 document.getElementById("editor").value=questions.join("\n");
}
function saveQuestions(){
 const data=document.getElementById("editor").value.split("\n").map(x=>x.trim()).filter(Boolean);
 if(!data.length){alert("Isi minimal satu pertanyaan.");return;}
 questions=data;
 localStorage.setItem("aliefQuestions",JSON.stringify(questions));
 index=0;
 render();
 document.getElementById("panel").style.display="none";
}
render();
</script>
</body>
</html>'''

out = Path("/mnt/data/alief_quiz")
out.mkdir(exist_ok=True)
html_path = out / "index.html"
html_path.write_text(html, encoding="utf-8")

zip_path = Path("/mnt/data/alief_quiz.zip")
with zipfile.ZipFile(zip_path, "w", zipfile.ZIP_DEFLATED) as z:
    z.write(html_path, "index.html")

print(f"HTML: {html_path}")
print(f"ZIP: {zip_path}")
