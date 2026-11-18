# article-note
기사 정리 및 업데이트
<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>기사 프로젝트 노트</title>
<style>
  :root{
    --bg:#f6f3ec; --card:#fffdf8; --ink:#25221d; --sub:#7a7468;
    --line:#e3ddd0; --accent:#b4432f; --accent-soft:#f6e3dd;
  }
  *{box-sizing:border-box}
  html{-webkit-text-size-adjust:100%}
  body{
    margin:0; background:var(--bg); color:var(--ink);
    font-family:"Noto Serif KR","Nanum Myeongjo","Apple SD Gothic Neo","Malgun Gothic",serif;
    line-height:1.7; font-size:17px;
  }
  .wrap{max-width:680px; margin:0 auto; padding:28px 18px 64px}
  header{border-bottom:3px double var(--ink); padding-bottom:14px; margin-bottom:22px}
  .kicker{font:600 12px/1 "Apple SD Gothic Neo","Malgun Gothic",sans-serif; letter-spacing:.18em; color:var(--accent)}
  h1{margin:8px 0 0; font-size:clamp(26px,7vw,34px); line-height:1.25; letter-spacing:-.02em}
  section{
    background:var(--card); border:1px solid var(--line); border-radius:10px;
    padding:16px 16px 14px; margin-bottom:14px;
  }
  h2{
    margin:0 0 8px; font:700 13px/1.4 "Apple SD Gothic Neo","Malgun Gothic",sans-serif;
    color:var(--accent); letter-spacing:.06em;
  }
  .field{
    min-height:1.7em; outline:none; word-break:keep-all; overflow-wrap:anywhere;
    border-bottom:1px dashed var(--line); padding:2px 0;
  }
  .field:focus{border-bottom-color:var(--accent)}
  .field:empty::before{content:attr(data-ph); color:#b3ab9c}
  .row{display:flex; gap:10px; align-items:baseline; margin:4px 0}
  .tag{
    flex:none; font:600 12px/1 "Apple SD Gothic Neo","Malgun Gothic",sans-serif;
    color:var(--sub); width:3.6em;
  }
  .row .field{flex:1}
  .later{
    display:inline-block; margin-top:6px; padding:2px 10px; border-radius:99px;
    background:var(--accent-soft); color:var(--accent);
    font:600 13px/1.6 "Apple SD Gothic Neo","Malgun Gothic",sans-serif;
  }
  ul.todo{list-style:none; margin:0; padding:0}
  ul.todo li{display:flex; gap:12px; align-items:flex-start; padding:8px 0; border-bottom:1px solid var(--line)}
  ul.todo li:last-child{border-bottom:0}
  input[type=checkbox]{
    flex:none; width:22px; height:22px; margin:3px 0 0; accent-color:var(--accent);
  }
  ul.todo .field{flex:1; border-bottom:0}
  li.done .field{color:var(--sub); text-decoration:line-through}
  #logs{position:relative; padding-left:22px; margin-top:4px}
  #logs::before{
    content:""; position:absolute; left:6px; top:10px; bottom:10px;
    width:2px; background:var(--line);
  }
  .log{position:relative; padding:6px 0 14px}
  .log:last-child{padding-bottom:4px}
  .log::before{
    content:""; position:absolute; left:-22px; top:11px; width:14px; height:14px;
    border-radius:50%; background:var(--card); border:3px solid var(--accent);
  }
  .date{font:700 15px/1.4 "Apple SD Gothic Neo","Malgun Gothic",sans-serif}
  .date small{font-weight:500; color:var(--sub); margin-left:6px}
  button{
    margin-top:8px; padding:10px 14px; min-height:44px;
    border:1px solid var(--line); background:#fff; color:var(--ink);
    border-radius:8px; font:600 14px "Apple SD Gothic Neo","Malgun Gothic",sans-serif;
  }
  .hint{margin:18px 0 0; font:13px/1.6 "Apple SD Gothic Neo","Malgun Gothic",sans-serif; color:var(--sub)}
  @media print{
    body{background:#fff} button,.hint{display:none}
    section{break-inside:avoid}
  }
</style>
</head>
<body>
<div class="wrap">
  <header>
    <div class="kicker">PROJECT NOTE</div>
    <h1>기사 프로젝트 노트</h1>
  </header>

  <section>
    <h2>프로젝트 한 줄</h2>
    <div class="field" contenteditable="true" data-k="brief" data-ph="여기에 프로젝트 한 줄을 적어 주세요"></div>
  </section>

  <section>
    <h2>이름과 역할</h2>
    <div class="row"><span class="tag">이름</span><div class="field" contenteditable="true" data-k="name" data-ph="이름"></div></div>
    <div class="row"><span class="tag">역할</span><div class="field" contenteditable="true" data-k="role" data-ph="맡은 역할"></div></div>
  </section>

  <section>
    <h2>주제</h2>
    <div class="field" contenteditable="true" data-k="topic" data-ph="배정받은 대주제를 적어 주세요"></div>
    <span class="later">세부 주제 · 내일 정함</span>
  </section>

  <section>
    <h2>할 일 체크리스트</h2>
    <ul class="todo" id="todo"></ul>
  </section>

  <section>
    <h2>날짜별 기록</h2>
    <div id="logs"></div>
    <button type="button" id="addLog">+ 날짜 추가</button>
  </section>

  <p class="hint">칸을 눌러 바로 적을 수 있고, 이 기기의 브라우저에 자동 저장돼요.</p>
</div>

<script>
(function(){
  var KEY="article-project-note-v1";
  var data={};
  try{ data=JSON.parse(localStorage.getItem(KEY)||"{}"); }catch(e){ data={}; }
  function save(){ try{ localStorage.setItem(KEY,JSON.stringify(data)); }catch(e){} }

  // 단순 칸
  document.querySelectorAll("[data-k]").forEach(function(el){
    var k=el.getAttribute("data-k");
    if(data[k]) el.textContent=data[k];
    el.addEventListener("input",function(){ data[k]=el.textContent; save(); });
  });
  // 엔터 시 줄바꿈 대신 칸 이탈
  document.addEventListener("keydown",function(e){
    if(e.key==="Enter" && e.target.classList && e.target.classList.contains("field")){
      e.preventDefault(); e.target.blur();
    }
  });

  // 체크리스트 5개
  var todo=document.getElementById("todo");
  data.todo=data.todo||[];
  for(var i=0;i<5;i++){
    (function(i){
      var s=data.todo[i]||{t:"",c:false};
      data.todo[i]=s;
      var li=document.createElement("li");
      var cb=document.createElement("input"); cb.type="checkbox"; cb.checked=!!s.c;
      cb.setAttribute("aria-label",(i+1)+"번째 할 일 완료");
      var f=document.createElement("div"); f.className="field"; f.contentEditable="true";
      f.setAttribute("data-ph",(i+1)+". 할 일을 적어 주세요"); f.textContent=s.t;
      if(s.c) li.className="done";
      cb.addEventListener("change",function(){ s.c=cb.checked; li.className=cb.checked?"done":""; save(); });
      f.addEventListener("input",function(){ s.t=f.textContent; save(); });
      li.appendChild(cb); li.appendChild(f); todo.appendChild(li);
    })(i);
  }

  // 날짜별 기록
  var logs=document.getElementById("logs");
  if(!data.logs||!data.logs.length) data.logs=[{d:"11/18",t:""}];
  function render(){
    logs.innerHTML="";
    data.logs.forEach(function(L,idx){
      var box=document.createElement("div"); box.className="log";
      var d=document.createElement("div"); d.className="date"; d.textContent=L.d;
      if(idx===0){ var sm=document.createElement("small"); sm.textContent="오늘 한 줄"; d.appendChild(sm); }
      var f=document.createElement("div"); f.className="field"; f.contentEditable="true";
      f.setAttribute("data-ph","한 줄 기록"); f.textContent=L.t;
      f.addEventListener("input",function(){ L.t=f.textContent; save(); });
      box.appendChild(d); box.appendChild(f); logs.appendChild(box);
    });
  }
  render(); save();

  document.getElementById("addLog").addEventListener("click",function(){
    var v=prompt("날짜를 입력해 주세요 (예: 11/19)");
    if(v && v.trim()){ data.logs.push({d:v.trim(),t:""}); save(); render(); }
  });
})();
</script>
</body>
</html>
