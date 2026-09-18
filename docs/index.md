
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Machine-Learned Content Decay Risk Scoring — FlyRank ML Internship Capstone</title>
<meta name="description" content="A Random Forest Approach to Ranked Content Audit Queue Generation at Scale — Muhammad Musa, FlyRank ML Internship Capstone 2026.">
<link rel="preconnect" href="[fonts.googleapis.com](https://fonts.googleapis.com)">
<link rel="preconnect" href="[fonts.gstatic.com](https://fonts.gstatic.com)" crossorigin>
<link href="[fonts.googleapis.com](https://fonts.googleapis.com/css2?family=Fraunces:ital,opsz,wght@0,9..144,300..900;1,9..144,300..900&family=Instrument+Sans:ital,wght@0,400..700;1,400..700&family=JetBrains+Mono:wght@400;500;700&display=swap)" rel="stylesheet">
<style>
/* ============================================================
   FlyRank Capstone — "Field Journal" design system
   Paper: warm uncoated stock · Ink: near-black · Accent: oxide
   ============================================================ */
:root{
  --paper:#F5F2E9;
  --paper-raised:#FFFDF6;
  --paper-sunk:#EDE8DB;
  --ink:#14120E;
  --ink-soft:#3B372F;
  --muted:#7C7567;
  --rule:#D9D2C1;
  --rule-strong:#B6AC95;
  --oxide:#AE3A16;
  --oxide-wash:#F3E4DB;
  --indigo:#25415E;
  --indigo-wash:#E2E7EE;
  --moss:#4E6044;
  --amber:#8A6613;
  --measure:70ch;
  --rail:9.5rem;
  --serif:"Fraunces",Georgia,serif;
  --sans:"Instrument Sans",system-ui,-apple-system,sans-serif;
  --mono:"JetBrains Mono",ui-monospace,Menlo,monospace;
}

*,*::before,*::after{box-sizing:border-box}
html{-webkit-text-size-adjust:100%;scroll-behavior:smooth}
body{
  margin:0;
  background:var(--paper);
  color:var(--ink);
  font-family:var(--sans);
  font-size:1.0125rem;
  line-height:1.72;
  font-feature-settings:"onum" 1,"kern" 1,"liga" 1;
  background-image:
    radial-gradient(circle at 12% 8%, rgba(174,58,22,.045), transparent 42%),
    radial-gradient(circle at 88% 4%, rgba(37,65,94,.05), transparent 38%);
  background-repeat:no-repeat;
}

/* ---------- shared atoms ---------- */
.kicker{
  font-family:var(--mono);
  font-size:.66rem;
  letter-spacing:.2em;
  text-transform:uppercase;
  color:var(--muted);
  font-weight:500;
}
.rule{border:0;border-top:1px solid var(--rule);margin:0}
.rule--double{border-top:3px double var(--rule-strong)}
.mono{font-family:var(--mono);font-size:.9em}
code{
  font-family:var(--mono);
  font-size:.85em;
  background:var(--paper-sunk);
  border:1px solid var(--rule);
  padding:.08em .38em;
  border-radius:2px;
  white-space:nowrap;
}
a{color:var(--oxide);text-decoration:none;border-bottom:1px solid rgba(174,58,22,.35);transition:background .18s,border-color .18s}
a:hover{background:var(--oxide-wash);border-bottom-color:var(--oxide)}
strong{font-weight:600}
em{font-style:italic}

/* ---------- side rail nav ---------- */
.rail{
  position:fixed;inset:0 auto 0 0;width:var(--rail);
  border-right:1px solid var(--rule);
  padding:2.25rem 0 2rem 1.5rem;
  display:flex;flex-direction:column;gap:.1rem;
  background:linear-gradient(90deg,rgba(255,253,246,.7),transparent);
  z-index:20;
}
.rail__mark{
  font-family:var(--serif);font-weight:800;font-size:1.5rem;letter-spacing:-.02em;
  line-height:1;margin-bottom:.35rem;
}
.rail__mark span{color:var(--oxide)}
.rail__sub{font-family:var(--mono);font-size:.56rem;letter-spacing:.16em;text-transform:uppercase;color:var(--muted);margin-bottom:2.2rem}
.rail a{
  font-family:var(--mono);font-size:.63rem;letter-spacing:.11em;text-transform:uppercase;
  color:var(--muted);border:0;padding:.42rem 0;display:flex;gap:.5rem;align-items:baseline;
}
.rail a b{color:var(--rule-strong);font-weight:500;font-variant-numeric:tabular-nums}
.rail a:hover,.rail a.is-active{background:transparent;color:var(--ink)}
.rail a.is-active b{color:var(--oxide)}
.rail__foot{margin-top:auto;font-family:var(--mono);font-size:.55rem;letter-spacing:.1em;color:var(--rule-strong);text-transform:uppercase}

/* ---------- page shell ---------- */
.sheet{margin-left:var(--rail);padding:0 clamp(1.25rem,4vw,4.5rem)}
.wrap{max-width:calc(var(--measure) + 14rem);margin:0 auto}

/* ---------- masthead ---------- */
.masthead{padding:2.5rem 0 0}
.masthead__bar{
  display:flex;flex-wrap:wrap;gap:.6rem 2rem;align-items:baseline;
  padding-bottom:.7rem;border-bottom:1px solid var(--ink);
}
.masthead__bar .spacer{flex:1}
.masthead__strip{
  display:flex;flex-wrap:wrap;gap:.5rem 2rem;
  padding:.55rem 0;border-bottom:3px double var(--rule-strong);
  font-family:var(--mono);font-size:.64rem;letter-spacing:.13em;text-transform:uppercase;color:var(--muted);
}
.masthead__strip b{color:var(--ink-soft);font-weight:500}

.lane{
  display:inline-flex;align-items:center;gap:.5rem;
  margin:1.8rem 0 1.1rem;padding:.3rem .7rem .3rem .55rem;
  border:1px solid var(--oxide);color:var(--oxide);
  font-family:var(--mono);font-size:.62rem;letter-spacing:.16em;text-transform:uppercase;
}
.lane::before{content:"";width:6px;height:6px;background:var(--oxide);border-radius:50%}

h1.title{
  font-family:var(--serif);
  font-optical-sizing:auto;
  font-size:clamp(2.35rem,5.6vw,4.15rem);
  font-weight:600;
  line-height:1.02;
  letter-spacing:-.025em;
  margin:0 0 1rem;
  max-width:26ch;
  text-wrap:balance;
}
h1.title i{font-style:italic;font-weight:400;color:var(--oxide)}
.subtitle{
  font-family:var(--serif);font-style:italic;font-weight:300;
  font-size:clamp(1.05rem,2vw,1.4rem);line-height:1.4;
  color:var(--ink-soft);max-width:44ch;margin:0 0 2.25rem;
}

/* byline */
.byline{
  display:grid;grid-template-columns:minmax(0,1fr) auto;gap:1.5rem;align-items:end;
  padding:1.1rem 0;border-top:1px solid var(--rule);border-bottom:1px solid var(--rule);
}
.byline__name{font-family:var(--serif);font-size:1.32rem;font-weight:600;letter-spacing:-.01em}
.byline__role{font-size:.82rem;color:var(--muted);margin-top:.1rem}
.byline__date{font-family:var(--mono);font-size:.68rem;letter-spacing:.12em;text-transform:uppercase;color:var(--muted);text-align:right}

/* metadata ledger */
.ledger{
  display:grid;grid-template-columns:repeat(auto-fit,minmax(9.5rem,1fr));
  border-bottom:1px solid var(--rule);
}
.ledger div{padding:1rem 1.1rem 1.05rem 0;border-right:1px solid var(--rule)}
.ledger div:last-child{border-right:0}
.ledger dt{font-family:var(--mono);font-size:.58rem;letter-spacing:.15em;text-transform:uppercase;color:var(--muted);margin-bottom:.3rem}
.ledger dd{margin:0;font-size:.92rem;font-weight:500;line-height:1.35}

/* ---------- abstract ---------- */
.abstract{
  margin:3rem 0 0;padding:2.2rem 2.4rem 2.4rem;
  background:var(--paper-raised);
  border:1px solid var(--rule);
  border-left:4px solid var(--ink);
  position:relative;
}
.abstract h2{
  font-family:var(--mono);font-size:.66rem;letter-spacing:.24em;text-transform:uppercase;
  font-weight:500;color:var(--muted);margin:0 0 1.1rem;
}
.abstract p{margin:0 0 1rem;max-width:var(--measure);font-size:1.055rem;line-height:1.76}
.abstract p:last-of-type{margin-bottom:1.6rem}
.abstract > p:first-of-type::first-letter{
  font-family:var(--serif);font-weight:700;float:left;
  font-size:3.6rem;line-height:.82;padding:.16rem .5rem 0 0;color:var(--oxide);
}
.keywords{display:flex;flex-wrap:wrap;gap:.4rem;align-items:center;border-top:1px solid var(--rule);padding-top:1.25rem}
.keywords .kw-label{font-family:var(--mono);font-size:.6rem;letter-spacing:.18em;text-transform:uppercase;color:var(--muted);margin-right:.4rem}
.keywords span:not(.kw-label){
  font-family:var(--mono);font-size:.68rem;letter-spacing:.03em;
  border:1px solid var(--rule-strong);padding:.2rem .5rem;color:var(--ink-soft);
}

/* ---------- figures band ---------- */
.band{
  display:grid;grid-template-columns:repeat(4,1fr);
  margin:0;border-bottom:1px solid var(--ink);border-top:1px solid var(--ink);
  margin-top:3rem;
}
.band > div{padding:1.6rem 1.2rem 1.7rem;border-right:1px solid var(--rule)}
.band > div:last-child{border-right:0}
.band .fig{
  font-family:var(--serif);font-size:clamp(2rem,4vw,2.9rem);font-weight:600;
  line-height:1;letter-spacing:-.03em;font-variant-numeric:tabular-nums;display:block;
}
.band .fig.oxide{color:var(--oxide)}
.band .cap{font-family:var(--mono);font-size:.6rem;letter-spacing:.15em;text-transform:uppercase;color:var(--muted);margin-top:.6rem;display:block}
.band .note{font-size:.78rem;color:var(--ink-soft);margin-top:.2rem;display:block}

/* ---------- sections ---------- */
section.sec{padding:4.5rem 0 1rem;border-bottom:1px solid var(--rule)}
section.sec:last-of-type{border-bottom:0}
.sec__head{display:grid;grid-template-columns:var(--rail) minmax(0,1fr);gap:0;align-items:start;margin-bottom:2rem}
.sec__num{
  font-family:var(--mono);font-size:.63rem;letter-spacing:.2em;text-transform:uppercase;color:var(--oxide);
  padding-top:.85rem;
}
.sec__num::after{content:"";display:block;width:2rem;height:1px;background:var(--rule-strong);margin-top:.7rem}
h2.sec__title{
  font-family:var(--serif);font-size:clamp(1.7rem,3.4vw,2.5rem);font-weight:600;
  letter-spacing:-.02em;line-height:1.08;margin:0;
}
.sec__body{display:grid;grid-template-columns:var(--rail) minmax(0,1fr);gap:0}
.sec__body > *{grid-column:2}
.sec__body p{max-width:var(--measure);margin:0 0 1.35rem}
h3.lede{
  font-family:var(--serif);font-size:1.22rem;font-weight:600;letter-spacing:-.01em;
  margin:2.4rem 0 .85rem;padding-bottom:.45rem;border-bottom:1px solid var(--rule);
  max-width:var(--measure);
}
h3.lede::before{
  content:"§";font-family:var(--mono);font-size:.7rem;color:var(--oxide);
  vertical-align:.35em;margin-right:.5rem;
}
h4.mini{
  font-family:var(--mono);font-size:.7rem;letter-spacing:.14em;text-transform:uppercase;
  font-weight:700;margin:0 0 .55rem;
}

/* pull quote */
blockquote.pull{
  margin:2.8rem 0;padding:0 0 0 2rem;border-left:3px solid var(--oxide);max-width:60ch;
}
blockquote.pull p{
  font-family:var(--serif);font-style:italic;font-weight:400;
  font-size:clamp(1.18rem,2.4vw,1.55rem);line-height:1.38;letter-spacing:-.01em;margin:0 0 .9rem;
}
blockquote.pull cite{
  font-family:var(--mono);font-style:normal;font-size:.64rem;letter-spacing:.14em;
  text-transform:uppercase;color:var(--muted);
}

/* callouts */
.callout{
  margin:1.9rem 0;padding:1.3rem 1.5rem 1.4rem;
  background:var(--paper-raised);border:1px solid var(--rule);
  border-left:3px solid var(--rule-strong);max-width:var(--measure);
}
.callout__tag{
  font-family:var(--mono);font-size:.6rem;letter-spacing:.17em;text-transform:uppercase;
  font-weight:700;display:block;margin-bottom:.5rem;color:var(--ink-soft);
}
.callout p{margin:0;font-size:.97rem}
.callout p + p{margin-top:.7rem}
.callout--lock{border-left-color:var(--indigo);background:linear-gradient(90deg,var(--indigo-wash),var(--paper-raised) 55%)}
.callout--lock .callout__tag{color:var(--indigo)}
.callout--warn{border-left-color:var(--amber);background:linear-gradient(90deg,#F6EEDA,var(--paper-raised) 55%)}
.callout--warn .callout__tag{color:var(--amber)}
.callout--oxide{border-left-color:var(--oxide);background:linear-gradient(90deg,var(--oxide-wash),var(--paper-raised) 55%)}
.callout--oxide .callout__tag{color:var(--oxide)}
.callout--good{border-left-color:var(--moss);background:linear-gradient(90deg,#E4EADF,var(--paper-raised) 55%)}
.callout--good .callout__tag{color:var(--moss)}

/* two-up failure modes */
.duo{display:grid;grid-template-columns:repeat(auto-fit,minmax(15rem,1fr));gap:1px;background:var(--rule);margin:1.9rem 0;border:1px solid var(--rule)}
.duo > div{background:var(--paper-raised);padding:1.35rem 1.4rem}
.duo h4{margin-bottom:.5rem}
.duo p{margin:0;font-size:.93rem;line-height:1.62}
.duo .tag-fp{color:var(--oxide)}
.duo .tag-fn{color:var(--indigo)}

/* lists */
ul.ticks,ol.steps{max-width:var(--measure);padding:0;margin:0 0 1.5rem;list-style:none}
ul.ticks li{position:relative;padding-left:1.7rem;margin-bottom:.85rem}
ul.ticks li::before{
  content:"";position:absolute;left:.25rem;top:.72em;width:.5rem;height:.5rem;
  border:1px solid var(--oxide);background:var(--oxide-wash);
}
ol.steps{counter-reset:s}
ol.steps li{
  counter-increment:s;display:grid;grid-template-columns:2.6rem minmax(0,1fr);gap:1rem;
  padding:1.05rem 0;border-top:1px solid var(--rule);align-items:baseline;
}
ol.steps li:last-child{border-bottom:1px solid var(--rule)}
ol.steps li::before{
  content:counter(s,decimal-leading-zero);
  font-family:var(--mono);font-size:.78rem;color:var(--oxide);font-weight:700;letter-spacing:.06em;
}
ol.steps b{display:block;font-size:.98rem;margin-bottom:.3rem}
ol.steps .cmd{
  display:block;font-family:var(--mono);font-size:.78rem;background:var(--paper-sunk);
  border:1px solid var(--rule);padding:.5rem .7rem;overflow-x:auto;white-space:pre;color:var(--ink-soft);
}

/* tables */
.table-wrap{max-width:100%;overflow-x:auto;margin:1.9rem 0 1rem}
table{width:100%;border-collapse:collapse;font-size:.92rem;min-width:36rem}
caption{
  caption-side:bottom;text-align:left;font-family:var(--mono);font-size:.6rem;
  letter-spacing:.13em;text-transform:uppercase;color:var(--muted);padding-top:.85rem;
}
thead th{
  font-family:var(--mono);font-size:.6rem;letter-spacing:.15em;text-transform:uppercase;
  font-weight:500;color:var(--muted);text-align:left;padding:0 1rem .6rem 0;
  border-bottom:1px solid var(--ink);
}
tbody td{padding:.95rem 1rem .95rem 0;border-bottom:1px solid var(--rule);vertical-align:top;line-height:1.55}
tbody tr:last-child td{border-bottom:1px solid var(--rule-strong)}
tbody tr.highlight{background:var(--oxide-wash)}
tbody tr.highlight td{font-weight:500}
td.num,th.num{font-family:var(--mono);font-variant-numeric:tabular-nums;white-space:nowrap}
td.w{font-family:var(--mono);font-size:.85rem;color:var(--oxide);font-weight:700}

/* feature register */
.register{border-top:1px solid var(--ink);margin:1.9rem 0 1rem}
.register__row{
  display:grid;grid-template-columns:2.2rem 15rem minmax(0,1fr);gap:1.2rem;
  padding:1.05rem 0;border-bottom:1px solid var(--rule);align-items:baseline;
}
.register__row .ix{font-family:var(--mono);font-size:.68rem;color:var(--rule-strong);font-weight:700}
.register__row .nm{font-family:var(--mono);font-size:.85rem;font-weight:500;color:var(--indigo);word-break:break-all}
.register__row .ds{font-size:.92rem;color:var(--ink-soft);line-height:1.6}

/* code panel */
.code{
  margin:1.9rem 0;background:#16140F;color:#E9E3D4;border:1px solid #2B2721;
  overflow:hidden;
}
.code__bar{
  display:flex;justify-content:space-between;align-items:center;gap:1rem;
  padding:.55rem .9rem;border-bottom:1px solid #2B2721;
  font-family:var(--mono);font-size:.6rem;letter-spacing:.16em;text-transform:uppercase;color:#8A8271;
}
.code__bar em{font-style:normal;color:#D08A5E}
.code pre{margin:0;padding:1.2rem .9rem 1.4rem;overflow-x:auto;font-family:var(--mono);font-size:.8rem;line-height:1.85}
.code .c{color:#7E7768;font-style:italic}
.code .k{color:#C97B5A}
.code .s{color:#9BAF87}
.code .n{color:#C4A86B}

/* importance chart */
.chart{margin:2rem 0 1rem;border-top:1px solid var(--ink);padding-top:1.3rem}
.bar{display:grid;grid-template-columns:12.5rem minmax(0,1fr) 3.2rem;gap:1rem;align-items:center;padding:.5rem 0}
.bar .lbl{font-family:var(--mono);font-size:.76rem;color:var(--ink-soft);word-break:break-all}
.bar .track{height:14px;background:var(--paper-sunk);border:1px solid var(--rule);position:relative}
.bar .fill{position:absolute;inset:0 auto 0 0;width:var(--w);background:
  repeating-linear-gradient(135deg,var(--oxide) 0 5px,#C6522C 5px 10px)}
.bar:nth-child(n+4) .fill{background:repeating-linear-gradient(135deg,var(--indigo) 0 5px,#3A5876 5px 10px)}
.bar .val{font-family:var(--mono);font-size:.78rem;font-variant-numeric:tabular-nums;text-align:right;font-weight:700}
.figcap{font-family:var(--mono);font-size:.6rem;letter-spacing:.12em;text-transform:uppercase;color:var(--muted);border-top:1px solid var(--rule);padding-top:.8rem;margin-top:1rem;line-height:1.7}

/* findings */
.finding{
  display:grid;grid-template-columns:2.4rem minmax(0,1fr);gap:1rem;
  padding:1.25rem 0;border-bottom:1px solid var(--rule);max-width:var(--measure);
}
.finding:first-of-type{border-top:1px solid var(--rule)}
.finding .pin{font-family:var(--mono);font-size:.62rem;font-weight:700;color:var(--oxide);letter-spacing:.06em;padding-top:.15rem}
.finding h4{font-family:var(--serif);font-size:1.05rem;font-weight:600;text-transform:none;letter-spacing:0;margin:0 0 .35rem}
.finding p{margin:0;font-size:.95rem}

/* playbook */
.playbook{margin:2rem 0 1rem;border-top:1px solid var(--ink)}
.play{
  display:grid;grid-template-columns:7rem minmax(0,1fr);gap:1.5rem;
  padding:1.6rem 0;border-bottom:1px solid var(--rule);
}
.play__rank{font-family:var(--serif);font-size:1.75rem;font-weight:600;letter-spacing:-.02em;line-height:1;font-variant-numeric:tabular-nums}
.play__band{font-family:var(--mono);font-size:.58rem;letter-spacing:.15em;text-transform:uppercase;margin-top:.5rem;display:block}
.play--hot .play__rank{color:var(--oxide)}
.play--hot .play__band{color:var(--oxide)}
.play--watch .play__rank{color:var(--amber)}
.play--watch .play__band{color:var(--amber)}
.play__code{
  font-family:var(--mono);font-size:.8rem;font-weight:500;display:inline-block;
  border:1px solid var(--rule-strong);padding:.15rem .45rem;margin-bottom:.7rem;
}
.play p{margin:0;font-size:.95rem}

/* colophon */
footer.colophon{
  margin:4rem 0 0;border-top:3px double var(--rule-strong);padding:2.4rem 0 3.5rem;
}
.colophon__grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(13rem,1fr));gap:2rem}
.colophon h5{font-family:var(--mono);font-size:.6rem;letter-spacing:.18em;text-transform:uppercase;color:var(--muted);margin:0 0 .6rem;font-weight:500}
.colophon p{margin:0;font-size:.88rem;line-height:1.7;color:var(--ink-soft)}
.colophon__mark{
  margin-top:2.6rem;padding-top:1.1rem;border-top:1px solid var(--rule);
  display:flex;flex-wrap:wrap;gap:.6rem 1.5rem;justify-content:space-between;
  font-family:var(--mono);font-size:.58rem;letter-spacing:.15em;text-transform:uppercase;color:var(--rule-strong);
}

/* ---------- responsive ---------- */
@media (max-width:1080px){
  :root{--rail:0rem}
  .rail{
    position:static;width:auto;inset:auto;border-right:0;border-bottom:1px solid var(--ink);
    flex-direction:row;flex-wrap:wrap;align-items:center;gap:.2rem 1rem;padding:1rem clamp(1.25rem,4vw,4.5rem);
    background:var(--paper-raised);
  }
  .rail__mark{margin:0;font-size:1.2rem}
  .rail__sub,.rail__foot{display:none}
  .rail a{padding:.2rem 0}
  .sheet{margin-left:0}
  .sec__head,.sec__body{grid-template-columns:minmax(0,1fr)}
  .sec__body > *{grid-column:1}
  .sec__num{padding-top:0;margin-bottom:.7rem}
  .sec__num::after{display:none}
  .band{grid-template-columns:repeat(2,1fr)}
  .band > div:nth-child(2){border-right:0}
  .band > div:nth-child(-n+2){border-bottom:1px solid var(--rule)}
}
@media (max-width:720px){
  body{font-size:.98rem}
  .abstract{padding:1.5rem 1.25rem 1.75rem}
  .band{grid-template-columns:1fr}
  .band > div{border-right:0;border-bottom:1px solid var(--rule)}
  .byline{grid-template-columns:1fr}
  .byline__date{text-align:left}
  .ledger div{border-right:0;border-bottom:1px solid var(--rule)}
  .register__row{grid-template-columns:1.6rem minmax(0,1fr);gap:.5rem 1rem}
  .register__row .ds{grid-column:2}
  .bar{grid-template-columns:minmax(0,1fr) 3rem;gap:.5rem}
  .bar .lbl{grid-column:1/-1}
  .play{grid-template-columns:1fr;gap:.8rem}
}

/* ---------- print ---------- */
@media print{
  :root{--rail:0rem}
  body{background:#fff;font-size:10.5pt}
  .rail{display:none}
  .sheet{margin:0;padding:0}
  section.sec{page-break-inside:auto;padding-top:1.5rem}
  .code{background:#fff;color:#000;border:1px solid #999}
  .code .c,.code .k,.code .s,.code .n{color:#000}
  a{color:#000;border:0}
}
</style>
</head>
<body>

<!-- ====================== SIDE RAIL ====================== -->
<nav class="rail" aria-label="Section navigation">
  <div class="rail__mark">Fly<span>Rank</span></div>
  <div class="rail__sub">Capstone Series<br>Vol. 2026 / 08</div>
  <a href="#s01"><b>01</b> Problem</a>
  <a href="#s02"><b>02</b> Data Safety</a>
  <a href="#s03"><b>03</b> Baseline</a>
  <a href="#s04"><b>04</b> Model</a>
  <a href="#s05"><b>05</b> Evaluation</a>
  <a href="#s06"><b>06</b> Interpretation</a>
  <a href="#s07"><b>07</b> Recommend.</a>
  <a href="#s08"><b>08</b> Repro.</a>
  <div class="rail__foot">M. Musa<br>2026</div>
</nav>

<div class="sheet">
<div class="wrap">

  <!-- ====================== MASTHEAD ====================== -->
  <header class="masthead">
    <div class="masthead__bar">
      <span class="kicker">FlyRank ML Internship — Capstone Report Series</span>
      <span class="spacer"></span>
      <span class="kicker">Vol. 2026 · Issue 08</span>
    </div>
    <div class="masthead__strip">
      <span>Refresh &amp; Content Opportunity Scoring</span>
      <span>DOI <b>flyrank/ml-intern/2026/refresh-scoring</b></span>
    </div>

    <span class="lane">Lane · Refresh / Content Opportunity Scoring</span>

    <h1 class="title">Machine-Learned Content Decay Risk Scoring for <i>Proactive</i> SEO Refresh Prioritization</h1>
    <p class="subtitle">A Random Forest Approach to Ranked Content Audit Queue Generation at Scale</p>

    <div class="byline">
      <div>
        <div class="byline__name">Muhammad Musa</div>
        <div class="byline__role">ML Intern, FlyRank · Capstone Author &nbsp;·&nbsp;
          <a href="[github.com](https://github.com/Musamehar/ML_Intership)">↗ github.com/Musamehar/ML_Intership</a>
        </div>
      </div>
      <div class="byline__date">Submitted<br>10 August 2026</div>
    </div>

    <dl class="ledger">
      <div><dt>Repository</dt><dd><a href="[github.com](https://github.com/Musamehar/ML_Intership)">ML_Intership</a></dd></div>
      <div><dt>Primary Method</dt><dd>Random Forest Classifier</dd></div>
      <div><dt>Target Metric</dt><dd>Precision@20 / ROC-AUC</dd></div>
      <div><dt>Data Split</dt><dd>5-Fold GroupKFold</dd></div>
      <div><dt>Random Seed</dt><dd>42 (globally enforced)</dd></div>
    </dl>
  </header>

  <!-- ====================== ABSTRACT ====================== -->
  <section class="abstract" aria-labelledby="abs">
    <h2 id="abs">Abstract</h2>
    <p>Organic search traffic decay is a persistent, compounding threat for publishers and brand-content teams alike. When a content item loses SERP position over time — due to staleness, shifting search intent, or competitive displacement — the resulting traffic loss rarely reverses itself without deliberate editorial intervention. This capstone presents a <strong>machine-learned content decay risk scoring system</strong> designed to replace ad-hoc, calendar-based refresh heuristics with a principled, continuously ranked priority queue.</p>
    <p>Working exclusively on the anonymized FlyRank ML Internship dataset of 90-day aggregated search performance signals, a <strong>Random Forest Classifier</strong> was trained to predict the binary onset of active traffic decay. Evaluated under a rigorous 5-Fold GroupKFold split grouped by <em>client_id</em> to prevent domain leakage, the model achieves a <strong>Precision@20 of ~0.72</strong> — representing a <strong>3× improvement</strong> over a transparent, industry-standard rule-based baseline (Precision@20 ≈ 0.24). Feature importance analysis identifies content staleness and search visibility as the dominant decay risk factors, while revealing the negative result that keyword optimization alone does not protect pages from long-term organic decay. The resulting tool is a production-ready, decision-support queue that allows editorial teams to allocate finite refresh bandwidth to the highest-risk pages first.</p>
    <div class="keywords">
      <span class="kw-label">Keywords</span>
      <span>Content Decay</span><span>SEO Prioritization</span><span>Random Forest</span>
      <span>Precision@K</span><span>GroupKFold</span><span>Feature Importance</span>
      <span>Traffic Decay</span><span>SERP Volatility</span><span>Editorial Prioritization</span>
    </div>
  </section>

  <!-- ====================== HEADLINE FIGURES ====================== -->
  <div class="band">
    <div>
      <span class="fig oxide">0.72</span>
      <span class="cap">Precision@20</span>
      <span class="note">▲ 3× vs. Baseline</span>
    </div>
    <div>
      <span class="fig">~25%</span>
      <span class="cap">Dataset Base Rate</span>
      <span class="note">Active decay pages</span>
    </div>
    <div>
      <span class="fig">5</span>
      <span class="cap">Features Used</span>
      <span class="note">No leakage features</span>
    </div>
    <div>
      <span class="fig">90d</span>
      <span class="cap">Data Window</span>
      <span class="note">Trailing aggregated</span>
    </div>
  </div>

  <!-- ====================== 01 PROBLEM FRAMING ====================== -->
  <section class="sec" id="s01">
    <div class="sec__head">
      <div class="sec__num">Section 01</div>
      <h2 class="sec__title">Problem Framing</h2>
    </div>
    <div class="sec__body">
      <p>The modern content ecosystem is vast and perpetually competitive. A page published six months ago with strong organic performance today may silently begin to lose ground tomorrow — overtaken by fresher competitor content, displaced by algorithm updates, or rendered obsolete by shifts in user intent. Without a systematic prioritization tool, editorial teams are forced to make refresh decisions either reactively (after significant traffic loss has already occurred) or arbitrarily (based on publication date alone). Neither approach is efficient, and both compound the business cost of content decay.</p>

      <h3 class="lede">The Decision Being Supported</h3>
      <p>This tool provides <strong>directional, decision-support scoring</strong> to help SEO strategists and editorial teams prioritize which specific content items require a content refresh audit to mitigate organic traffic decay. Rather than presenting a static list of “old” pages, the system generates a continuously ranked queue that weighs multiple intersecting risk signals simultaneously — delivering the right information to the right person at the right time.</p>

      <h3 class="lede">Unit of Analysis &amp; Output</h3>
      <p>The unit of analysis is a <strong>single content item (page) for a specific client domain</strong>. The output is a <em>continuous risk score</em> and a <em>ranked priority queue</em>, supplemented by categorical reason codes (e.g., <code>stale_visible_decay</code>) that explain <em>why</em> a page has been flagged, enabling editors to understand the risk category and act accordingly.</p>

      <h3 class="lede">The Human Action &amp; The Cost of Error</h3>
      <p>A human editor reviews the top <em>K</em> items (e.g., Top 20) in the generated queue to manually audit and update the content. The scoring system must be calibrated to the <strong>asymmetric cost structure</strong> of this workflow. Two distinct failure modes exist, each with a meaningful business consequence:</p>

      <div class="duo">
        <div>
          <h4 class="mini tag-fp">⚠ False Positive — Wasted Bandwidth</h4>
          <p>The model flags a healthy page as at-risk. An editor allocates precious time to audit a page that requires no intervention. The cost is wasted editorial bandwidth on a high-performing page — a manageable but real opportunity cost.</p>
        </div>
        <div>
          <h4 class="mini tag-fn">⚠ False Negative — Compounded Loss</h4>
          <p>The model misses a page experiencing active decay. The page continues to lose ground, compounding traffic loss that becomes progressively harder to recover. This is the higher-stakes failure mode in most real-world editorial contexts.</p>
        </div>
      </div>

      <h3 class="lede">Why Machine Learning Helps</h3>
      <p>Content decay is driven by <strong>complex, non-linear interactions</strong> across search volume, click-through-rate (CTR) volatility, content staleness, and shifting search engine results page (SERP) positions. A rigid heuristic rule — for example, “refresh if age &gt; 180 days” — flags hundreds of pages as a binary <em>“YES,”</em> forcing editorial teams to perform their own sorting and triage before they can act. This implicit re-ranking burden defeats the purpose of the tool.</p>
      <p>Machine learning <strong>ranks these candidates on a continuous scale</strong>, creating an immediately actionable queue that optimizes limited human effort by evaluating these interacting signals dynamically and weighting them according to learned relationships in historical data — relationships that a static heuristic cannot encode. The result is not just a binary alert system, but a calibrated risk register sorted from highest to lowest decay probability, ready for editorial action without additional manual filtering.</p>

      <blockquote class="pull">
        <p>“A rigid heuristic flags hundreds of pages as a binary YES, forcing manual sorting. Machine learning ranks these candidates on a continuous scale, creating an actionable queue that optimizes limited human effort.”</p>
        <cite>— Muhammad Musa, Capstone Report 2026</cite>
      </blockquote>
    </div>
  </section>

  <!-- ====================== 02 DATA SAFETY ====================== -->
  <section class="sec" id="s02">
    <div class="sec__head">
      <div class="sec__num">Section 02</div>
      <h2 class="sec__title">Data Safety</h2>
    </div>
    <div class="sec__body">
      <p>Responsible use of data is a foundational requirement of any machine learning project touching real-world business metrics. This analysis was designed from the outset with a rigorous <strong>data contract</strong> that enforces strict separation between features available at prediction time and information that would constitute leakage, privacy violation, or an unfair evaluation advantage. Every decision described below was made deliberately and enforced at the code level.</p>

      <h3 class="lede">Data Used</h3>
      <p>This analysis relies exclusively on the <strong>anonymized FlyRank ML Internship dataset</strong>. The dataset consists of trailing 90-day aggregated search performance metrics (including fields such as <code>impressions_90d</code>, <code>clicks_90d</code>, <code>avg_position</code>) and structural metadata (including <code>content_age_days</code>). This data represents search performance aggregated over a meaningful historical window, ensuring that the signal-to-noise ratio is sufficiently high for model training while maintaining full anonymization.</p>

      <h3 class="lede">Excluded Columns &amp; Leakage Prevention</h3>
      <div class="callout callout--lock">
        <span class="callout__tag">🔒 Target Leakage — Strictly Excluded</span>
        <p>The columns <strong><code>trend_pct</code></strong> and <strong><code>trend_direction</code></strong> were deliberately excluded from the feature set. Because the target label (<code>is_declining_label</code>) is <em>mathematically derived</em> from these fields, including them would cause circular target leakage — the model would be trivially solving a tautology rather than learning generalizable decay signals.</p>
      </div>
      <div class="callout callout--lock">
        <span class="callout__tag">🔒 Domain Leakage — Grouping Only</span>
        <p>Pseudonymous identifiers like <strong><code>client_id</code></strong> were used exclusively for <em>grouping during cross-validation splits</em> and were never passed to the model as predictive features. This prevents the model from memorizing domain-specific traffic baselines as a shortcut to high validation scores — a subtle but critical form of leakage in multi-client datasets.</p>
      </div>

      <h3 class="lede">Privacy Confirmation</h3>
      <p>No raw private client data, domains, URLs, page titles, or specific search queries were used in any part of this analysis. All fields that could identify a client, a publication, or an individual user were either excluded entirely or replaced with pseudonymous identifiers before the data reached the modeling pipeline. I confirm that <strong>nothing client-identifying appears anywhere in the <code>work/</code> directory</strong>.</p>
    </div>
  </section>

  <!-- ====================== 03 BASELINE ====================== -->
  <section class="sec" id="s03">
    <div class="sec__head">
      <div class="sec__num">Section 03</div>
      <h2 class="sec__title">Baseline</h2>
    </div>
    <div class="sec__body">
      <p>A common failure mode in applied ML projects is evaluating a model in isolation — without a meaningful reference point that quantifies the value the model adds over what a competent human practitioner could already do without any machine learning. To avoid this, a <strong>transparent, rule-based baseline</strong> was constructed before any model was trained, and it was evaluated on the exact same data splits and metrics.</p>

      <h3 class="lede">The Transparent Rule</h3>
      <p>The baseline calculates a composite priority score on a scale of <strong>0.0 to 100.0</strong> based on three weighted dimensions intended to mirror the mental model of an experienced SEO editor. Each dimension was independently normalized and combined into a weighted sum:</p>

      <div class="table-wrap">
        <table>
          <caption>Table 1 · Composition of the handwritten baseline priority score.</caption>
          <thead>
            <tr><th>Dimension</th><th class="num">Weight</th><th>Signal</th><th>Rationale</th></tr>
          </thead>
          <tbody>
            <tr>
              <td><strong>Visibility</strong></td>
              <td class="w">50%</td>
              <td>Normalized log 90-day impressions</td>
              <td>High-visibility pages represent the greatest absolute opportunity — their decay has the largest impact on total organic traffic.</td>
            </tr>
            <tr>
              <td><strong>Staleness Risk</strong></td>
              <td class="w">35%</td>
              <td>Binary penalty: content age ≥ 180 days</td>
              <td>Pages older than 6 months are statistically more likely to have outdated information, increasing decay risk.</td>
            </tr>
            <tr>
              <td><strong>Position Opportunity</strong></td>
              <td class="w">15%</td>
              <td>Boost for positions 1–10</td>
              <td>First-page content is both more visible to users and more susceptible to click decay from SERP feature displacement.</td>
            </tr>
          </tbody>
        </table>
      </div>

      <h3 class="lede">Why It’s a Fair Comparison</h3>
      <p>This heuristic is a <strong>fair comparison</strong> because it represents a standard, logical industry approach that any senior SEO editor or content strategist would recognize as reasonable practice. It is not a strawman — it encodes genuine domain knowledge about the drivers of content decay. It was evaluated on the <em>exact same dataset</em>, the <em>exact same metrics</em>, and the <em>exact same grouped data splits</em> as the machine learning models, ensuring a level playing field.</p>

      <div class="callout callout--warn">
        <span class="callout__tag">📊 Baseline Result (W04)</span>
        <p>Evaluated on the client-grouped split, the handwritten rule achieved a <strong>Precision@50 of approximately 0.24</strong> — essentially matching the natural base rate of the dataset (~25% active decay). This confirms that the heuristic provides no meaningful lift over random selection among high-scoring pages, and establishes the minimum threshold the machine learning model must exceed to prove its utility.</p>
      </div>
    </div>
  </section>

  <!-- ====================== 04 MODEL & ANALYSIS ====================== -->
  <section class="sec" id="s04">
    <div class="sec__head">
      <div class="sec__num">Section 04</div>
      <h2 class="sec__title">Model &amp; Analysis</h2>
    </div>
    <div class="sec__body">
      <p>With a transparent baseline established, the modeling phase proceeded with a clear directive: learn a continuous decay risk score that meaningfully outperforms the heuristic on the top-K precision metric, using only signals that are knowable at prediction time and free from leakage.</p>

      <h3 class="lede">Method &amp; Fit</h3>
      <p>To move beyond a rigid heuristic, a <strong>Random Forest Classifier</strong> was trained to output a continuous decay probability score. This ensemble tree method is a natural fit for this problem domain for several reasons:</p>
      <ul class="ticks">
        <li><strong>Non-linearity:</strong> Content decay is governed by threshold dynamics and interaction effects — the relationship between search volume, position drift, and content age is decidedly non-linear. Decision trees natively capture these interactions without requiring feature engineering.</li>
        <li><strong>Feature importance:</strong> Random Forests provide reliable, model-native feature importance rankings via mean decrease in impurity, enabling post-hoc interpretability critical for stakeholder trust.</li>
        <li><strong>Robustness to scale:</strong> As an ensemble method, Random Forest is relatively robust to individual outlier trees and provides stable predictions even on moderately sized datasets.</li>
        <li><strong>Calibrated probability output:</strong> The classifier’s <code>predict_proba</code> output serves directly as the continuous risk score used for queue ranking.</li>
      </ul>

      <h3 class="lede">Feature List</h3>
      <p>The model relies exclusively on signals knowable prior to the evaluation window — enforcing a strict temporal data contract to prevent look-ahead bias. The exact feature set used is:</p>

      <div class="register">
        <div class="register__row">
          <span class="ix">01</span>
          <span class="nm">📊 log_impressions_90d</span>
          <span class="ds">Log-transformed trailing 90-day impressions. Captures search visibility on a compressed scale.</span>
        </div>
        <div class="register__row">
          <span class="ix">02</span>
          <span class="nm">🖱️ ctr_90d</span>
          <span class="ds">Click-through rate over 90 days. Measures how effectively the page converts impressions to clicks.</span>
        </div>
        <div class="register__row">
          <span class="ix">03</span>
          <span class="nm">📍 avg_position</span>
          <span class="ds">Average SERP ranking position. Lower (better) positions correlate with lower decay vulnerability.</span>
        </div>
        <div class="register__row">
          <span class="ix">04</span>
          <span class="nm">📅 content_age_days</span>
          <span class="ds">Days since content was published. The primary staleness proxy — older content faces higher refresh risk.</span>
        </div>
        <div class="register__row">
          <span class="ix">05</span>
          <span class="nm">🔑 has_keyword</span>
          <span class="ds">Binary flag for presence of an optimized primary keyword. Baseline optimization signal — see negative result in §6.</span>
        </div>
      </div>

      <h3 class="lede">Deliberate Exclusions</h3>
      <div class="callout callout--oxide">
        <span class="callout__tag">🚫 Excluded by Design</span>
        <p><strong><code>trend_pct</code></strong> and <strong><code>trend_direction</code></strong> were intentionally left out to prevent target leakage. These fields are mathematically tied to the target label (<code>is_declining_label</code>), and their inclusion would produce artifically inflated metrics that do not reflect real predictive generalization.</p>
      </div>

      <h3 class="lede">Target Definition</h3>
      <p>The target is a binary proxy flag — <strong><code>decay_flag</code></strong> derived from <code>is_declining_label</code> — predicting whether a page experiences <strong>active traffic decay</strong> during the subsequent observation window. The binary framing allows the model to be calibrated against a clear positive class (decaying pages) and to output probability scores that rank all pages from highest to lowest decay risk.</p>

      <h3 class="lede">Implementation Snapshot</h3>
      <div class="code">
        <div class="code__bar">
          <span>work/notebooks · core pipeline</span>
          <span><em>random_state = 42</em> enforced globally</span>
        </div>
<pre><span class="c"># Core pipeline — random_state=42 enforced globally</span>
<span class="k">from</span> sklearn.ensemble <span class="k">import</span> RandomForestClassifier
<span class="k">from</span> sklearn.model_selection <span class="k">import</span> GroupKFold

features = [
    <span class="s">"log_impressions_90d"</span>,
    <span class="s">"ctr_90d"</span>,
    <span class="s">"avg_position"</span>,
    <span class="s">"content_age_days"</span>,
    <span class="s">"has_keyword"</span>,
]

<span class="c"># client_id used ONLY for grouping — never as feature</span>
gkf = GroupKFold(n_splits=<span class="n">5</span>)
rf  = RandomForestClassifier(random_state=<span class="n">42</span>, n_estimators=<span class="n">200</span>)

<span class="c"># Fit and extract continuous decay probability scores</span>
decay_scores = rf.predict_proba(X_test)[:, <span class="n">1</span>]</pre>
      </div>
    </div>
  </section>

  <!-- ====================== 05 EVALUATION ====================== -->
  <section class="sec" id="s05">
    <div class="sec__head">
      <div class="sec__num">Section 05</div>
      <h2 class="sec__title">Evaluation</h2>
    </div>
    <div class="sec__body">
      <p>Rigorous, leakage-free evaluation is the cornerstone of any trustworthy ML system. The evaluation design described here was constructed to ensure that every performance number reported reflects genuine out-of-sample generalization — not an artifact of data leakage, lucky splits, or metric gaming.</p>

      <h3 class="lede">Split Design</h3>
      <div class="callout callout--lock">
        <span class="callout__tag">🔬 5-Fold GroupKFold Split</span>
        <p>A standard random <code>train_test_split</code> would mix URLs from the same client domain into both training and validation sets, allowing the model to cheat by memorizing domain-specific traffic baselines — a subtle but severe form of domain leakage. By grouping cross-validation folds by <strong>client_id</strong>, each validation fold contains <em>entirely unseen websites</em>, ensuring the model is evaluated on its ability to generalize to new domains, not to recall patterns from clients it has already seen.</p>
      </div>

      <h3 class="lede">Metrics &amp; Baseline Comparison</h3>
      <p>The models were evaluated using two complementary metrics: <strong>ROC-AUC</strong> for overall discriminative power across all thresholds, and <strong>Precision@20</strong> as the primary business metric — reflecting the real-world workflow where an editor reviews the top 20 flagged pages. The dataset’s base rate for active decay is approximately 25%.</p>

      <div class="table-wrap">
        <table>
          <caption>Table 2 · Top-K precision, evaluated on the identical client-grouped split.</caption>
          <thead>
            <tr><th>System</th><th class="num">Precision@20</th><th class="num">vs. Base Rate</th><th>Notes</th></tr>
          </thead>
          <tbody>
            <tr>
              <td>Dataset Base Rate</td><td class="num">~0.25</td><td class="num">—</td>
              <td>Expected precision of random selection</td>
            </tr>
            <tr>
              <td>Rule Baseline (W04)</td><td class="num">~0.24</td><td class="num">~0× lift</td>
              <td>Matches base rate; heuristic provides no real lift</td>
            </tr>
            <tr class="highlight">
              <td><strong>Random Forest Model</strong></td><td class="num">~0.72</td><td class="num">~3× lift</td>
              <td>Evaluated on identical grouped split; no leakage</td>
            </tr>
          </tbody>
        </table>
      </div>

      <div class="callout callout--good">
        <span class="callout__tag">✅ Conclusion</span>
        <p>The learned model demonstrates a <strong>roughly 3× lift</strong> over the baseline on the exact same data, with the same split design and the same metric. This result is statistically and practically meaningful: if an editor reviews the Top 20 pages from the model’s queue, approximately 14–15 of those pages will genuinely be experiencing active decay — compared to only 5 from the heuristic baseline.</p>
      </div>

      <h3 class="lede">Error Analysis</h3>
      <p>A rigorous evaluation extends beyond headline metrics to understand the <em>character</em> of the model’s errors. A review of the model’s mistakes reveals clear, interpretable patterns that inform both future feature engineering and deployment guardrails:</p>

      <div class="duo">
        <div>
          <h4 class="mini tag-fp">False Positives — Predicted Decay, Actual Healthy</h4>
          <p>The model occasionally flags high-volume, “evergreen” pages that are old (<code>content_age_days &gt; 500</code>) but continue to maintain strong, stable organic brand demand. These pages score high on staleness risk but are protected by entrenched brand authority — a signal the model currently lacks access to.</p>
        </div>
        <div>
          <h4 class="mini tag-fn">False Negatives — Predicted Healthy, Actual Declining</h4>
          <p>The model misses sudden decay on relatively <em>new</em> content (<code>content_age_days &lt; 120</code>) that experienced rapid SERP position losses. Because these pages are young, age-heavy features fail to anticipate their vulnerability, suggesting that position velocity signals could improve recall on this cohort.</p>
        </div>
      </div>

      <p>These error patterns are not merely academic observations — they directly inform a clear <strong>feature engineering roadmap</strong>: incorporating position velocity (rate of rank change over time) and brand authority signals (e.g., domain-level impression stability) as future features to address both failure modes systematically.</p>
    </div>
  </section>

  <!-- ====================== 06 INTERPRETATION ====================== -->
  <section class="sec" id="s06">
    <div class="sec__head">
      <div class="sec__num">Section 06</div>
      <h2 class="sec__title">Interpretation</h2>
    </div>
    <div class="sec__body">
      <p>A machine learning model deployed in an editorial decision-support context must be interpretable — editors and strategists need to understand <em>why</em> a page was flagged, not just <em>that</em> it was flagged. This section presents the model’s learned feature importance rankings and discusses the practical and theoretical significance of both the positive findings and the negative result.</p>

      <h3 class="lede">What the Model Found</h3>
      <p>The Random Forest model identified that content decay is heavily dependent on the <strong>intersection of age and search volume</strong>. The most critical signals driving the model’s predictions, in order of importance, are:</p>

      <div class="chart">
        <div class="bar"><span class="lbl">content_age_days</span><div class="track"><div class="fill" style="--w:42%"></div></div><span class="val">42%</span></div>
        <div class="bar"><span class="lbl">log_impressions_90d</span><div class="track"><div class="fill" style="--w:31%"></div></div><span class="val">31%</span></div>
        <div class="bar"><span class="lbl">avg_position</span><div class="track"><div class="fill" style="--w:18%"></div></div><span class="val">18%</span></div>
        <div class="bar"><span class="lbl">ctr_90d</span><div class="track"><div class="fill" style="--w:7%"></div></div><span class="val">7%</span></div>
        <div class="bar"><span class="lbl">has_keyword</span><div class="track"><div class="fill" style="--w:2%"></div></div><span class="val">2%</span></div>
        <p class="figcap">
          Figure 1 · Approximate Random Forest Feature Importance (Mean Decrease in Impurity) — see repository for the canonical SVG output.<br>
          📎 <a href="[raw.githubusercontent.com](https://raw.githubusercontent.com/Musamehar/ML_Intership/main/outputs/charts/top_feature_importance.svg)">View canonical chart → outputs/charts/top_feature_importance.svg</a>
        </p>
      </div>

      <div class="finding">
        <span class="pin">📌 01</span>
        <div>
          <h4>Finding 1 — Staleness as Primary Risk Factor</h4>
          <p><strong><code>content_age_days</code></strong> is the dominant decay predictor. Staleness acts as the primary risk accumulator — as time passes without a refresh, the probability of decay compounds non-linearly, especially once content crosses the 180-day threshold.</p>
        </div>
      </div>
      <div class="finding">
        <span class="pin">📌 02</span>
        <div>
          <h4>Finding 2 — Visibility Amplifies Impact</h4>
          <p><strong><code>log_impressions_90d</code></strong> is the second-ranked feature. High-visibility pages face greater <em>absolute</em> volatility — a 10% traffic drop on a page with 100,000 impressions matters far more than the same percentage drop on a page with 1,000.</p>
        </div>
      </div>
      <div class="finding">
        <span class="pin">📌 03</span>
        <div>
          <h4>Finding 3 — Mid-Page-One Vulnerability</h4>
          <p><strong><code>avg_position</code></strong> contributes meaningfully at rank 3. Pages ranking outside the top 3 but still on page one (positions 4–10) exhibit higher vulnerability to sudden click decay from SERP feature displacement (e.g., AI Overviews, featured snippets, People Also Ask boxes) — making position a risk amplifier in specific ranking bands.</p>
        </div>
      </div>

      <h3 class="lede">Surprises &amp; Negative Results</h3>
      <p>Perhaps the most practically significant finding in this analysis is a <strong>negative result</strong>: the model found that <strong><code>has_keyword</code></strong> — the presence of an optimized primary keyword — had <strong>minimal predictive power</strong> for future decay.</p>

      <blockquote class="pull">
        <p>“A well-optimized page is just as likely to decay over time as a poorly optimized one once staleness sets in. Simply ‘optimizing’ a page at publish time does not protect it from natural organic decay.”</p>
        <cite>— Negative Result, Section 6</cite>
      </blockquote>

      <p>This finding challenges the common SEO assumption that keyword optimization is a long-term protective factor. The data suggests that keyword optimization at time of publication is a <em>point-in-time</em> signal, not a durable shield against organic decay. As search intent evolves and competitors refresh their own content, the presence of a primary keyword loses its protective effect. This negative result is valuable precisely because it corrects a widespread misconception — it implies that editorial refresh cycles must be driven by recency and visibility metrics, not by optimization status alone.</p>
    </div>
  </section>

  <!-- ====================== 07 RECOMMENDATIONS ====================== -->
  <section class="sec" id="s07">
    <div class="sec__head">
      <div class="sec__num">Section 07</div>
      <h2 class="sec__title">Recommendations</h2>
    </div>
    <div class="sec__body">
      <p>The recommendations in this section translate the model’s outputs into a concrete operational workflow that a FlyRank editorial team can deploy on a weekly cadence. The primary output of this tool is a <strong>ranked queue</strong> that editors use to allocate their refresh bandwidth most effectively, guided by reason codes that explain each page’s risk profile at a glance.</p>

      <h3 class="lede">The Action Playbook</h3>
      <div class="playbook">
        <div class="play play--hot">
          <div>
            <div class="play__rank">1–10</div>
            <span class="play__band">🔴 Highest Risk</span>
          </div>
          <div>
            <span class="play__code">stale_visible_decay</span>
            <p>An editor should review these pages immediately for outdated statistics, broken links, or shifts in search intent (e.g., Google introducing AI Overviews for those queries). These pages combine high staleness with high search visibility — the most impactful decay scenario.</p>
          </div>
        </div>
        <div class="play play--watch">
          <div>
            <div class="play__rank">11–20</div>
            <span class="play__band">🟡 Watchlist</span>
          </div>
          <div>
            <span class="play__code">page_one_decay_risk</span>
            <p>These pages exhibit active click-through rate erosion despite maintaining first-page rankings. Editors should monitor their CTRs closely and consider minor metadata adjustments — title tag refreshes, meta description updates — to defend their organic position.</p>
          </div>
        </div>
      </div>

      <p>Beyond the immediate priority queue, a <strong>weekly review cadence</strong> is recommended. The queue should be regenerated each week using the latest 90-day performance window, ensuring that pages whose performance has improved (following a refresh or algorithm update) naturally fall out of the top tiers, while newly deteriorating pages rise appropriately.</p>

      <h3 class="lede">Confidence &amp; Limits</h3>
      <div class="callout callout--warn">
        <span class="callout__tag">⚠ Important Scope Limitation</span>
        <p>This tool is strictly a <strong>decision-support engine</strong>, not an automated editor. The model provides directional risk scoring based on observed historical patterns. It <em>cannot</em> predict external factors such as broad Google algorithm updates, seasonal demand crashes, or competitor content campaigns. <strong>Human intuition and editorial judgment remain necessary</strong> to validate the recommendations before executing any content rewrite.</p>
      </div>
    </div>
  </section>

  <!-- ====================== 08 REPRODUCIBILITY ====================== -->
  <section class="sec" id="s08">
    <div class="sec__head">
      <div class="sec__num">Section 08</div>
      <h2 class="sec__title">Reproducibility</h2>
    </div>
    <div class="sec__body">
      <p>Scientific and engineering reproducibility is a non-negotiable standard for any production-bound ML system. This analysis is fully reproducible using the provided FlyRank ML Internship starter dataset and standard Python ML libraries — no proprietary tools, private APIs, or paid services are required. Every source of randomness is controlled by a globally enforced random seed.</p>

      <h3 class="lede">Environment &amp; Commands</h3>
      <ol class="steps">
        <li>
          <b>Clone the Repository</b>
          <span class="cmd">git clone [github.com](https://github.com/Musamehar/ML_Intership.git)</span>
        </li>
        <li>
          <b>Install Dependencies</b>
          <span class="cmd">pip install pandas numpy scikit-learn</span>
        </li>
        <li>
          <b>Run the Pipeline</b>
          <span>Execute the capstone notebooks in order (from <strong>w01</strong> to <strong>w06</strong>) located in the <code>work/notebooks/</code> directory.</span>
        </li>
      </ol>

      <h3 class="lede">Random Seeds</h3>
      <div class="callout callout--oxide">
        <span class="callout__tag">🎲 Global Seed — random_state=42</span>
        <p>To ensure identical data splits and model convergence across all runs, <strong><code>random_state=42</code></strong> is enforced globally across all <code>train_test_split</code>, <code>GroupKFold</code>, and <code>RandomForestClassifier</code> instances throughout the pipeline. Any researcher following the steps above should obtain numerically identical results.</p>
      </div>
    </div>
  </section>

  <!-- ====================== COLOPHON ====================== -->
  <footer class="colophon">
    <div class="colophon__grid">
      <div>
        <h5>Author</h5>
        <p>Muhammad Musa<br>ML Intern · FlyRank, 2026</p>
      </div>
      <div>
        <h5>Repository</h5>
        <p><a href="[github.com](https://github.com/Musamehar/ML_Intership)">github.com/Musamehar/ML_Intership</a><br>Published: 10 August 2026</p>
      </div>
      <div>
        <h5>License &amp; Data</h5>
        <p>Anonymized FlyRank ML Internship dataset.<br>No private client data used.<br>© 2026 Muhammad Musa · FlyRank</p>
      </div>
    </div>
    <div class="colophon__mark">
      <span>FlyRank Capstone Report Series · Vol. 2026 · Issue 08</span>
      <span>DOI flyrank/ml-intern/2026/refresh-scoring</span>
      <span>End of report</span>
    </div>
  </footer>

</div>
</div>

<script>
/* Rail nav — highlight the section currently in view */
(function () {
  var links = Array.prototype.slice.call(document.querySelectorAll('.rail a[href^="#"]'));
  var map = {};
  links.forEach(function (a) { map[a.getAttribute('href').slice(1)] = a; });

  var obs = new IntersectionObserver(function (entries) {
    entries.forEach(function (e) {
      if (!e.isIntersecting) return;
      links.forEach(function (a) { a.classList.remove('is-active'); });
      var active = map[e.target.id];
      if (active) active.classList.add('is-active');
    });
  }, { rootMargin: '-25% 0px -65% 0px', threshold: 0 });

  document.querySelectorAll('section.sec[id]').forEach(function (s) { obs.observe(s); });
})();
</script>
</body>
</html>
