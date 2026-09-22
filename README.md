# Money-ledger
html = r'''<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">
<meta name="theme-color" content="#18201b">
<title>Ledger — Money Flow</title>
<style>
:root{--bg:#f4f2eb;--paper:#fffefa;--ink:#20261f;--muted:#74786f;--line:#ddd9ce;--green:#356b4b;--red:#a94b3e;--gold:#a37b31;--shadow:0 10px 28px #20261f12}
*{box-sizing:border-box}body{margin:0;background:var(--bg);color:var(--ink);font-family:Inter,system-ui,-apple-system,Segoe UI,sans-serif}
.app{max-width:1100px;margin:auto;padding:28px 20px 50px}.header{display:flex;justify-content:space-between;align-items:center;gap:15px;margin-bottom:24px}
.brand{font:800 30px Georgia,serif;letter-spacing:-1px}.sub{color:var(--muted);font-size:13px;margin-top:3px}
button{font:inherit;cursor:pointer}.btn{border:1px solid var(--line);background:var(--paper);padding:11px 15px;border-radius:12px;font-weight:700}.primary{background:var(--ink);color:white;border-color:var(--ink)}
.cards{display:grid;grid-template-columns:repeat(4,1fr);gap:12px;margin-bottom:18px}.card{background:var(--paper);border:1px solid var(--line);border-radius:16px;padding:17px;box-shadow:var(--shadow)}.label{color:var(--muted);font-size:12px}.big{font-size:24px;font-weight:800;margin-top:7px}.green{color:var(--green)}.red{color:var(--red)}
.layout{display:grid;grid-template-columns:1.4fr .8fr;gap:18px}.panel{background:var(--paper);border:1px solid var(--line);border-radius:18px;box-shadow:var(--shadow);overflow:hidden}.head{padding:17px 18px;border-bottom:1px solid var(--line);display:flex;justify-content:space-between;align-items:center;gap:10px}.head h2{font:700 18px Georgia,serif;margin:0}
.flow{padding:18px}.income{border:1px solid #cbdccd;border-radius:15px;padding:16px;background:#f5faf5}.income-top{display:flex;justify-content:space-between;gap:10px}.income-title{font-weight:800}.amount{font-weight:800;font-size:21px}.flowline{margin:12px 0 5px;border-left:2px solid var(--line);padding-left:15px}.transfer{display:flex;align-items:center;justify-content:space-between;gap:12px;padding:12px 0;border-bottom:1px dashed var(--line)}.transfer:last-child{border-bottom:0}.from{color:var(--muted);font-size:12px}.to{font-weight:750}.arrow{color:var(--gold);font-weight:900}.empty{padding:32px;text-align:center;color:var(--muted)}
.account{padding:15px 18px;border-bottom:1px solid var(--line)}.account:last-child{border-bottom:0}.accttop{display:flex;justify-content:space-between}.acctname{font-weight:800}.acctsub{color:var(--muted);font-size:12px;margin-top:4px}.bar{height:6px;background:#e8e5dc;border-radius:9px;margin-top:10px;overflow:hidden}.bar span{display:block;height:100%;background:var(--green)}
table{width:100%;border-collapse:collapse}th,td{padding:12px 16px;border-bottom:1px solid var(--line);text-align:left;font-size:13px}th{font-size:11px;color:var(--muted);text-transform:uppercase}.money{font-weight:800;white-space:nowrap}.del{border:0;background:none;color:#999;font-size:18px}
dialog{border:0;border-radius:18px;width:min(510px,calc(100% - 25px));padding:0;box-shadow:0 30px 80px #0005}dialog::backdrop{background:#1118}.modalhead{padding:18px;border-bottom:1px solid var(--line);display:flex;justify-content:space-between}.modalhead h3{margin:0;font:700 19px Georgia,serif}.close{border:0;background:none;font-size:23px}
form{padding:18px}label{display:grid;gap:6px;font-size:12px;font-weight:750;margin-bottom:13px}input,select{width:100%;padding:11px;border:1px solid var(--line);border-radius:10px;background:white;font:inherit}.grid{display:grid;grid-template-columns:1fr 1fr;gap:10px}.actions{display:flex;justify-content:flex-end;gap:9px;margin-top:5px}
.note{font-size:12px;color:var(--muted);line-height:1.5;background:#f6f4ed;padding:11px;border-radius:10px;margin-bottom:13px}
@media(max-width:800px){.cards{grid-template-columns:1fr 1fr}.layout{grid-template-columns:1fr}}@media(max-width:500px){.app{padding:18px 13px}.header{align-items:flex-start}.cards{gap:8px}.card{padding:13px}.big{font-size:20px}.grid{grid-template-columns:1fr}table{min-width:650px}.tablewrap{overflow:auto}}
</style>
</head>
<body>
<div class="app">
  <header class="header">
    <div><div class="brand">Ledger</div><div class="sub">See exactly where your money goes.</div></div>
    <button class="btn primary" id="incomeBtn">+ Add income</button>
  </header>

  <section class="cards">
    <div class="card"><div class="label">Money received</div><div class="big green" id="received">£0.00</div></div>
    <div class="card"><div class="label">Money allocated</div><div class="big" id="allocated">£0.00</div></div>
    <div class="card"><div class="label">Still unallocated</div><div class="big" id="unallocated">£0.00</div></div>
    <div class="card"><div class="label">Current balances</div><div class="big" id="totalBalances">£0.00</div></div>
  </section>

  <div class="layout">
    <section class="panel">
      <div class="head"><h2>Money flow</h2><button class="btn" id="transferBtn">Move money</button></div>
      <div id="flow" class="flow"></div>
      <div class="head"><h2>Recent activity</h2></div>
      <div class="tablewrap">
        <table><thead><tr><th>Date</th><th>Movement</th><th>Amount</th><th></th></tr></thead><tbody id="activity"></tbody></table>
      </div>
    </section>

    <section class="panel">
      <div class="head"><h2>Where your money is</h2><button class="btn" id="accountBtn">+ Account</button></div>
      <div id="accounts"></div>
    </section>
  </div>
</div>

<dialog id="incomeDialog">
 <div class="modalhead"><h3>Record income</h3><button class="close" data-close="incomeDialog">×</button></div>
 <form id="incomeForm">
  <div class="note">Income is money entering your overall ledger. After recording it, use <b>Move money</b> to show where that income was put.</div>
  <div class="grid"><label>Amount<input id="incomeAmount" type="number" min=".01" step=".01" required placeholder="0.00"></label><label>Date<input id="incomeDate" type="date" required></label></div>
  <label>Source / note<input id="incomeNote" maxlength="70" placeholder="e.g. Part-time pay"></label>
  <div class="actions"><button type="button" class="btn" data-close="incomeDialog">Cancel</button><button class="btn primary">Record income</button></div>
 </form>
</dialog>

<dialog id="transferDialog">
 <div class="modalhead"><h3>Move money</h3><button class="close" data-close="transferDialog">×</button></div>
 <form id="transferForm">
  <div class="note">This connects your ledger together. Example: <b>Income → Savings £100</b> means £100 of your income was put into Savings. Transfers do not count as new income or spending.</div>
  <div class="grid"><label>From<select id="from" required></select></label><label>To<select id="to" required></select></label></div>
  <div class="grid"><label>Amount<input id="transferAmount" type="number" min=".01" step=".01" required placeholder="0.00"></label><label>Date<input id="transferDate" type="date" required></label></div>
  <label>Note<input id="transferNote" maxlength="70" placeholder="e.g. Put aside for holiday"></label>
  <div class="actions"><button type="button" class="btn" data-close="transferDialog">Cancel</button><button class="btn primary">Move money</button></div>
 </form>
</dialog>

<dialog id="accountDialog">
 <div class="modalhead"><h3>New account</h3><button class="close" data-close="accountDialog">×</button></div>
 <form id="accountForm">
  <label>Account name<input id="accountName" maxlength="30" required placeholder="e.g. Emergency fund"></label>
  <div class="actions"><button type="button" class="btn" data-close="accountDialog">Cancel</button><button class="btn primary">Create account</button></div>
 </form>
</dialog>

<script>
const KEY="ledger-flow-v1";
const initial={accounts:[
{id:"income",name:"Income",kind:"income"},
{id:"savings",name:"Savings",kind:"normal"},
{id:"expenses",name:"Expenses",kind:"normal"},
{id:"payments",name:"Payments",kind:"normal"},
{id:"travel",name:"Travel",kind:"normal"}], incomes:[], transfers:[]};
let data=JSON.parse(localStorage.getItem(KEY)||"null")||initial;
const $=id=>document.getElementById(id);
const today=()=>new Date().toISOString().slice(0,10);
function save(){localStorage.setItem(KEY,JSON.stringify(data))}
function money(n){return new Intl.NumberFormat("en-GB",{style:"currency",currency:"GBP"}).format(n||0)}
function acc(id){return data.accounts.find(a=>a.id===id)}
function incomeTotal(){return data.incomes.reduce((s,x)=>s+x.amount,0)}
function receivedByAccount(id){return data.incomes.filter(x=>x.accountId===id).reduce((s,x)=>s+x.amount,0)}
function balance(id){
 let b=receivedByAccount(id);
 data.transfers.forEach(x=>{if(x.from===id)b-=x.amount;if(x.to===id)b+=x.amount});
 return b;
}
function allocated(){return data.transfers.reduce((s,x)=>s+x.amount,0)}
function fmt(d){let [y,m,day]=d.split("-");return new Intl.DateTimeFormat("en-GB",{day:"2-digit",month:"short",year:"numeric"}).format(new Date(y,m-1,day))}
function esc(s){return String(s||"").replace(/[&<>"']/g,c=>({"&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;","'":"&#039;"}[c]))}

function render(){
 const received=incomeTotal(), alloc=allocated(), un=received-alloc;
 $("received").textContent=money(received);$("allocated").textContent=money(alloc);
 $("unallocated").textContent=money(un);
 const total=data.accounts.filter(a=>a.kind!=="income").reduce((s,a)=>s+balance(a.id),0);
 $("totalBalances").textContent=money(total);

 $("accounts").innerHTML=data.accounts.filter(a=>a.kind!=="income").map(a=>{
   const b=balance(a.id), pct=total>0?Math.max(0,Math.min(100,b/total*100)):0;
   return `<div class="account"><div class="accttop"><div><div class="acctname">${esc(a.name)}</div><div class="acctsub">${data.transfers.filter(x=>x.to===a.id||x.from===a.id).length} movements</div></div><div class="amount ${b<0?"red":"green"}">${money(b)}</div></div><div class="bar"><span style="width:${pct}%"></span></div></div>`;
 }).join("");

 const flows=[...data.incomes].sort((a,b)=>b.date.localeCompare(a.date));
 $("flow").innerHTML=flows.length?flows.map(i=>{
   const moves=data.transfers.filter(t=>t.sourceIncomeId===i.id);
   const moved=moves.reduce((s,t)=>s+t.amount,0);
   return `<div class="income"><div class="income-top"><div><div class="income-title">${esc(i.note||"Income")}</div><div class="acctsub">${fmt(i.date)}</div></div><div class="amount green">+${money(i.amount)}</div></div>
   <div class="flowline">${moves.length?moves.map(t=>`<div class="transfer"><div><div class="from">Income</div><div class="to">${esc(acc(t.to)?.name)}</div></div><div class="arrow">→</div><div class="amount">${money(t.amount)}</div></div>`).join(""):`<div class="acctsub" style="padding-top:10px">Nothing allocated from this income yet.</div>`}</div>
   <div class="acctsub" style="margin-top:9px">Allocated ${money(moved)} · Remaining ${money(i.amount-moved)}</div></div>`;
 }).join(""):`<div class="empty">Add your first income to start following your money.</div>`;

 const activity=[
   ...data.incomes.map(x=>({date:x.date,text:`Income · ${x.note||"Income received"}`,amount:x.amount,sign:"+",id:"i"+x.id})),
   ...data.transfers.map(x=>({date:x.date,text:`${acc(x.from)?.name} → ${acc(x.to)?.name}${x.note?" · "+x.note:""}`,amount:x.amount,sign:"→",id:"t"+x.id}))
 ].sort((a,b)=>b.date.localeCompare(a.date)).slice(0,12);
 $("activity").innerHTML=activity.map(x=>`<tr><td>${fmt(x.date)}</td><td>${esc(x.text)}</td><td class="money ${x.sign==="+"?"green":""}">${x.sign==="+"?"+":""}${money(x.amount)}</td><td><button class="del" data-del="${x.id}">×</button></td></tr>`).join("")||`<tr><td colspan="4" class="empty">No activity yet.</td></tr>`;

 $("from").innerHTML=data.accounts.map(a=>`<option value="${a.id}">${esc(a.name)}</option>`).join("");
 $("to").innerHTML=data.accounts.filter(a=>a.id!=="income").map(a=>`<option value="${a.id}">${esc(a.name)}</option>`).join("");
}
function open(d){$(d).showModal()}
function close(d){$(d).close()}
$("incomeBtn").onclick=()=>{$("incomeDate").value=today();open("incomeDialog")}
$("transferBtn").onclick=()=>{$("transferDate").value=today();open("transferDialog")}
$("accountBtn").onclick=()=>open("accountDialog");
document.querySelectorAll("[data-close]").forEach(b=>b.onclick=()=>close(b.dataset.close));

$("incomeForm").onsubmit=e=>{
 e.preventDefault();
 const amount=Number($("incomeAmount").value);
 const id=crypto.randomUUID?crypto.randomUUID():Date.now()+"i";
 data.incomes.push({id,amount,date:$("incomeDate").value,note:$("incomeNote").value.trim()});
 save();close("incomeDialog");e.target.reset();render();
};
$("transferForm").onsubmit=e=>{
 e.preventDefault();
 const from=$("from").value,to=$("to").value,amount=Number($("transferAmount").value);
 if(from===to||amount<=0)return;
 // Link transfer to the oldest income with enough unallocated money when money starts from Income.
 let sourceIncomeId=null;
 if(from==="income"){
   let remaining=amount;
   for(const i of data.incomes){
     const used=data.transfers.filter(t=>t.sourceIncomeId===i.id).reduce((s,t)=>s+t.amount,0);
     const available=i.amount-used;
     if(available>0){sourceIncomeId=i.id;break}
   }
   if(!sourceIncomeId){alert("There is no unallocated income available to move.");return}
   // The account balance still controls the real ledger. Linkage is for showing the flow.
 }
 data.transfers.push({id:crypto.randomUUID?crypto.randomUUID():Date.now()+"t",from,to,amount,date:$("transferDate").value,note:$("transferNote").value.trim(),sourceIncomeId});
 save();close("transferDialog");e.target.reset();render();
};
$("accountForm").onsubmit=e=>{
 e.preventDefault();let name=$("accountName").value.trim();if(!name)return;
 let base=name.toLowerCase().replace(/[^a-z0-9]+/g,"-")||"account",id=base,n=2;
 while(data.accounts.some(a=>a.id===id))id=base+"-"+n++;
 data.accounts.push({id,name,kind:"normal"});save();close("accountDialog");e.target.reset();render();
};
document.addEventListener("click",e=>{
 const b=e.target.closest("[data-del]");if(!b)return;
 const id=b.dataset.del;
 if(!confirm("Delete this movement?"))return;
 if(id.startsWith("i")) data.incomes=data.incomes.filter(x=>x.id!==id.slice(1));
 else data.transfers=data.transfers.filter(x=>x.id!==id.slice(1));
 save();render();
});
render();
</script>
</body>
</html>'''
path=Path("/mnt/data/ledger_money_flow.html")
path.write_text(html,encoding="utf-8")
print(path)
