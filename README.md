<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0">
<title>Loxcheck — Fiche état des lieux</title>
<link rel="manifest" href="manifest.json">
<meta name="theme-color" content="#C8102E">
<link rel="apple-touch-icon" href="icons/icon-192.png">
<script src="vendor/jspdf.umd.min.js"></script>
<script>
if (!window.storage) {
  window.storage = {
    get: async (key, shared) => {
      const v = localStorage.getItem((shared ? 'shared:' : '') + key);
      if (v === null) throw new Error('not found');
      return { key, value: v, shared: !!shared };
    },
    set: async (key, value, shared) => {
      localStorage.setItem((shared ? 'shared:' : '') + key, value);
      return { key, value, shared: !!shared };
    },
    delete: async (key, shared) => {
      localStorage.removeItem((shared ? 'shared:' : '') + key);
      return { key, deleted: true, shared: !!shared };
    },
    list: async (prefix, shared) => {
      const p = (shared ? 'shared:' : '') + (prefix || '');
      const keys = Object.keys(localStorage).filter(k => k.startsWith(p)).map(k => k.replace(/^shared:/, ''));
      return { keys, prefix, shared: !!shared };
    }
  };
}
</script>
<style>

:root{
  --red:#C8102E;
  --red-dark:#8F0C21;
  --red-tint:#FDECEE;
  --paper:#F5F6F8;
  --card:#FFFFFF;
  --line:#DCE1E8;
  --ink:#111827;
  --sub:#5B6472;
  --neuf:#1E9E5A;
  --occasion:#2C6FB0;
  --avec-reserve:#C6362E;
  --sans-reserve:#8891A0;
  --gold:#E8A812;
}
*{box-sizing:border-box;}
body{
  margin:0; font-family:-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,Helvetica,Arial,sans-serif;
  background:var(--paper); color:var(--ink); -webkit-tap-highlight-color:transparent;
}
/* Uniform font across every text element and form control */
input, textarea, select, button, label, p, h1, h2, h3, div, span {
  font-family:-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,Helvetica,Arial,sans-serif;
}
header{
  position:sticky; top:0; z-index:50; background:var(--card);
  box-shadow:0 2px 8px rgba(0,0,0,.12);
}
.topbar{
  display:flex; align-items:center; justify-content:space-between; gap:10px;
  padding:10px 14px; border-bottom:1px solid var(--line);
}
.topbar img{height:34px; display:block;}
.topbar .agence{font-size:9.5px; color:var(--sub); font-weight:700; letter-spacing:.3px; margin-top:2px;}
.topbar .title{font-size:13.5px; font-weight:800; color:var(--red); text-align:right; letter-spacing:.2px;}
.accent-strip{
  background:var(--red); height:20px; margin-top:8px;
  box-shadow:0 2px 5px rgba(200,16,46,.25);
}
.progress-wrap{padding:8px 16px 0; background:var(--card);}
.progress-track{height:4px; background:#EEE; border-radius:2px; overflow:hidden;}
.progress-fill{height:100%; background:var(--gold); width:0%; transition:width .25s;}

main{padding:14px 12px 130px; max-width:680px; margin:0 auto;}
section.card{
  background:var(--card); border:1px solid var(--line); border-radius:14px;
  padding:14px; margin-bottom:14px;
}
section.card h2{font-size:15px; margin:0 0 4px; color:var(--red);}
section.card p.hint{font-size:12px; color:var(--sub); margin:0 0 12px;}

.field{margin-bottom:12px;}
.field label{display:block; font-size:12.5px; font-weight:600; color:var(--sub); margin-bottom:4px;}
.field input[type=text], .field input[type=email], .field input[type=tel], .field input[type=date], .field input[type=number], .field textarea, .field select{
  width:100%; min-width:0; max-width:100%; border:1px solid var(--line); border-radius:9px; padding:10px 11px;
  font-size:15px; font-family:inherit; background:#fff; color:var(--ink);
  -webkit-appearance:none; appearance:none;
}
.field input[type=date]{ display:block; min-height:46px; }
input::-webkit-date-and-time-value{ text-align:left; }
input, select, textarea{ max-width:100%; min-width:0; box-sizing:border-box; }
.field textarea{resize:vertical; min-height:54px;}
.grid2{display:grid; grid-template-columns:1fr 1fr; gap:10px;}
.info-grid{display:grid; grid-template-columns:1fr 1fr; gap:12px 12px; align-items:start;}
.info-grid .field{margin-bottom:0; min-width:0;}

.radio-row{display:flex; gap:8px;}
.radio-row label{
  flex:1 1 0; min-width:0; display:flex; align-items:center; justify-content:center;
  text-align:center; border:1.5px solid var(--line); border-radius:9px; min-height:42px;
  padding:6px 4px; font-size:12px; font-weight:600; color:var(--sub); background:#fff;
}
.radio-row label svg{width:26px; height:26px; display:block;}
.radio-row input{display:none;}
.radio-row label.st-ok{border-color:var(--neuf); color:var(--neuf);}
.radio-row input:checked + label.st-ok{background:var(--neuf); color:#fff;}
.radio-row label.st-nok{border-color:var(--avec-reserve); color:var(--avec-reserve);}
.radio-row input:checked + label.st-nok{background:var(--avec-reserve); color:#fff;}
.radio-row label.st-neuf{border-color:var(--neuf); color:var(--neuf);}
.radio-row input:checked + label.st-neuf{background:var(--neuf); color:#fff;}
.radio-row label.st-occasion{border-color:var(--occasion); color:var(--occasion);}
.radio-row input:checked + label.st-occasion{background:var(--occasion); color:#fff;}
.radio-row label.st-avec_reserve{border-color:var(--avec-reserve); color:var(--avec-reserve);}
.radio-row input:checked + label.st-avec_reserve{background:var(--avec-reserve); color:#fff;}
.radio-row label.st-sans_reserve{border-color:var(--sans-reserve); color:var(--sans-reserve);}
.radio-row input:checked + label.st-sans_reserve{background:var(--sans-reserve); color:#fff;}
.radio-row label.pv-generic{border-color:var(--line); color:var(--sub);}
.radio-row input:checked + label.pv-generic{background:var(--red); color:#fff; border-color:var(--red);}

/* Document-style checkbox options (type de fiche, notice, casse) */
.check-list{display:flex; flex-direction:column; gap:8px;}
.check-opt{
  display:flex; align-items:center; gap:11px; border:1.5px solid var(--line);
  border-radius:10px; padding:11px 13px; background:#fff; cursor:pointer;
}
.check-opt .box{
  flex:0 0 auto; width:22px; height:22px; border:2px solid #9AA1AC; border-radius:5px;
  display:flex; align-items:center; justify-content:center; font-size:15px; color:#fff; background:#fff;
}
.check-opt .lbl{font-size:14px; font-weight:600; color:var(--ink);}
.check-opt.checked{border-color:var(--red); background:var(--red-tint);}
.check-opt.checked .box{background:var(--red); border-color:var(--red);}
.check-opt.checked .lbl{color:var(--red-dark);}

/* Highlighted keys block */
.keys-box{
  border:1.5px solid var(--red); background:var(--red-tint); border-radius:12px;
  padding:12px; margin-bottom:14px;
}
.keys-box .keys-title{font-size:13px; font-weight:800; color:var(--red-dark); margin-bottom:10px;}
.keys-box .keys-grid{display:grid; grid-template-columns:1fr 1fr; gap:10px;}
.keys-box label{display:block; font-size:12px; font-weight:700; color:var(--red-dark); margin-bottom:4px;}
.keys-box input{width:100%; border:1.5px solid #E8B5BC; border-radius:9px; padding:11px; font-size:17px; font-weight:700; text-align:center; background:#fff;}

.item{border-top:1px solid var(--line); padding:12px 0;}
.item:first-of-type{border-top:none; padding-top:2px;}
.item-title{font-size:14.5px; font-weight:600; margin-bottom:8px;}
.item-qty{margin-bottom:8px;}
.item-qty input{width:100px; border:1px solid var(--line); border-radius:8px; padding:7px 9px; font-size:14px;}
.item-obs{margin-top:8px;}
.item-obs input{
  width:100%; border:1px solid var(--line); border-radius:8px; padding:8px 10px;
  font-size:13.5px; font-family:inherit;
}
.photo-row{display:flex; gap:8px; align-items:center; margin-top:8px; flex-wrap:wrap;}
.photo-btn{
  display:inline-flex; align-items:center; gap:6px; font-size:12.5px; font-weight:600;
  color:var(--red-dark); background:var(--red-tint); border:1px dashed #E8B5BC; border-radius:8px;
  padding:7px 10px;
}
.photo-thumb{position:relative; width:52px; height:52px; border-radius:8px; overflow:hidden; border:1px solid var(--line);}
.photo-thumb img{width:100%; height:100%; object-fit:cover; display:block;}
.photo-thumb .rm{
  position:absolute; top:0; right:0; background:rgba(0,0,0,.55); color:#fff; border:none;
  width:18px; height:18px; font-size:12px; line-height:18px; padding:0;
}

.free-lines textarea{width:100%; min-height:80px; border:1px solid var(--line); border-radius:9px; padding:10px; font-family:inherit; font-size:14px;}

.legal-block{margin:12px 0; font-size:13px; line-height:1.6; color:var(--ink);}
.legal-block p{margin:8px 0 6px;}
.legal-block input{
  width:100%; border:1px solid var(--line); border-radius:9px; padding:10px 11px;
  font-size:15px; background:#fff; color:var(--ink);
}
.check-row{display:flex; gap:8px;}
.check-row .check-opt{flex:1;}

.sig-block{margin-bottom:18px;}
.sig-block h3{font-size:13.5px; color:var(--red); margin:0 0 8px;}
.sig-grid{display:grid; grid-template-columns:1fr 1fr; gap:10px;}
.sig-cell{border:1px solid var(--line); border-radius:10px; padding:10px;}
.sig-cell .sig-label{font-size:11.5px; font-weight:700; color:var(--sub); margin-bottom:8px;}
.sig-cell input[type=text], .sig-cell select{width:100%; border:1px solid var(--line); border-radius:8px; padding:8px 9px; font-size:13.5px; margin-bottom:6px; background:#fff;}
.sig-cell input[type=date]{width:100%; min-width:0; border:1px solid var(--line); border-radius:8px; padding:8px 9px; font-size:13px; -webkit-appearance:none; appearance:none; display:block; min-height:42px;}
.sig-cwrap{position:relative;}
.sig-lock{position:absolute; top:0; left:0; right:0; bottom:0; display:flex; align-items:center; justify-content:center;
  background:rgba(255,255,255,.72); color:var(--sub); font-size:13.5px; font-weight:700;
  border-radius:10px; cursor:pointer; text-align:center; padding:6px; z-index:2;}
.sig-auto{border:1px dashed var(--line); border-radius:10px; padding:10px; text-align:center; background:#fff;}
.sig-auto img{max-height:60px; max-width:75%; display:block; margin:0 auto;}
.sig-auto-note{font-size:11px; color:var(--sub); margin-top:4px;}
.sig-pad{margin-top:10px;}
.sig-pad-label{font-size:11px; font-weight:600; color:var(--sub); margin-bottom:4px;}
.sig-canvas{width:100%; height:110px; border:1.5px dashed var(--line); border-radius:8px; background:#fff; touch-action:none; display:block;}
.sig-clear{margin-top:6px; background:#EEF0F3; color:var(--red-dark); border:none; border-radius:8px; padding:7px 10px; font-size:12px; font-weight:600;}
@media (max-width:560px){ .sig-grid{grid-template-columns:1fr;} }

footer.legal-footer{
  text-align:center; font-size:9.5px; color:#9AA1AC; padding:14px 20px 4px; line-height:1.5;
}

.devis-row{display:flex; gap:6px; align-items:center; margin-bottom:8px;}
.devis-row .d-desc{flex:1 1 0; min-width:0; font-size:13px; line-height:1.3; padding:2px 2px; overflow-wrap:anywhere;}
.devis-row .d-qty{flex:0 0 56px; border:1px solid var(--line); border-radius:9px; padding:9px 4px; font-size:14px; text-align:center;}
.devis-row .d-mt{flex:0 0 74px; text-align:right; font-weight:700; font-size:13.5px;}
.devis-legende{font-size:11.5px; color:var(--sub); margin:10px 0 4px; line-height:1.5;}
.devis-row .d-km{flex:0 0 74px; border:1px solid var(--line); border-radius:9px; padding:9px 4px; font-size:14px; text-align:center;}
.devis-row .d-del-spacer{flex:0 0 34px;}
.devis-dep .d-desc{font-weight:700;}
.devis-row .d-del{flex:0 0 34px; height:38px; border:1px solid var(--line); background:#fff; color:var(--avec-reserve); border-radius:9px; font-size:15px;}
.devis-cols{display:flex; gap:6px; font-size:11px; color:var(--sub); font-weight:700; margin-bottom:4px;}
.devis-cols .c-desc{flex:1 1 0;} .devis-cols .c-qty{flex:0 0 56px; text-align:center;} .devis-cols .c-mt{flex:0 0 74px; text-align:right;} .devis-cols .c-del{flex:0 0 34px;}
.devis-add{display:flex; gap:8px; margin:10px 0;}
.devis-add select{flex:1 1 0; min-width:0; border:1px solid var(--line); border-radius:9px; padding:9px 8px; font-size:12.5px; background:#fff;}
.mini-btn{border:1px solid var(--line); background:#fff; color:var(--ink); border-radius:9px; padding:9px 12px; font-size:13px; font-weight:700;}
.mini-btn.red{color:var(--red); border-color:var(--red);}
.devis-total{text-align:right; font-size:16px; font-weight:800; margin-top:10px; padding:10px 4px; border-top:2px solid var(--red);}
.btn-icon{flex:0 0 54px;}
.sticky-footer{
  position:fixed; bottom:0; left:0; right:0; background:#fff; border-top:1px solid var(--line);
  padding:10px 12px calc(10px + env(safe-area-inset-bottom)); display:flex; gap:8px; z-index:60;
  box-shadow:0 -2px 10px rgba(0,0,0,.06);
}
.btn{
  flex:1; padding:13px; border:none; border-radius:10px; font-size:14.5px; font-weight:700;
  display:flex; align-items:center; justify-content:center; gap:6px;
}
.btn-primary{background:var(--red); color:#fff;}
.btn-ghost{background:#EEF0F3; color:var(--red-dark);}
.btn-danger-ghost{background:var(--red-tint); color:var(--avec-reserve); flex:0 0 auto; padding:13px 16px;}

#toast{
  position:fixed; bottom:82px; left:50%; transform:translateX(-50%) translateY(20px);
  background:var(--red-dark); color:#fff; padding:9px 16px; border-radius:999px; font-size:13px;
  opacity:0; pointer-events:none; transition:all .25s; z-index:80; max-width:88%; text-align:center;
}
#toast.show{opacity:1; transform:translateX(-50%) translateY(0);}

/* Confirmation modal (reset) */
.modal-overlay{
  position:fixed; inset:0; background:rgba(17,24,39,.55); z-index:100;
  display:none; align-items:center; justify-content:center; padding:24px;
}
.modal-overlay.show{display:flex;}
.modal-card{
  background:#fff; border-radius:16px; padding:20px; max-width:340px; width:100%;
  box-shadow:0 12px 40px rgba(0,0,0,.25);
}
.modal-card h3{margin:0 0 8px; font-size:16px; color:var(--ink);}
.modal-card p{margin:0 0 18px; font-size:13.5px; color:var(--sub); line-height:1.5;}
.modal-actions{display:flex; gap:10px;}
.modal-actions button{
  flex:1; padding:12px; border:none; border-radius:10px; font-size:14px; font-weight:700;
}
.modal-actions .cancel{background:#EEF0F3; color:var(--ink);}
.modal-actions .danger{background:var(--red); color:#fff;}

</style>
</head>
<body>

<header>
  <div class="topbar">
    <img src="data:image/png;base64,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" alt="Loxam Module">
    <div style="text-align:right;">
      <div class="title">FICHE ÉTAT DES LIEUX</div>
      <div class="agence">AGENCE 9112 &nbsp;·&nbsp; LOXAM MODULE &nbsp;·&nbsp; Loxcheck</div>
    </div>
  </div>
  <div class="progress-wrap">
    <div class="progress-track"><div class="progress-fill" id="progressFill"></div></div>
  </div>
  <div class="accent-strip"></div>
</header>

<main id="sections"></main>

<div class="sticky-footer">
  <button class="btn btn-danger-ghost btn-icon" id="resetBtn" title="Nouvelle fiche">🗑</button>
  <button class="btn btn-ghost btn-icon" id="saveBtn" title="Enregistrer">💾</button>
  <button class="btn btn-primary" id="pdfBtn">📄 Fiche PV</button>
  <button class="btn btn-ghost" id="devisBtn">🧾 Fiche Devis</button>
</div>

<div id="toast"></div>

<script>
// ---------- DATA MODEL ----------
const SMILEY_OK = '<svg viewBox="0 0 48 48" fill="none"><circle cx="24" cy="24" r="19" stroke="currentColor" stroke-width="3.2"/><circle cx="17" cy="19" r="2.7" fill="currentColor"/><circle cx="31" cy="19" r="2.7" fill="currentColor"/><path d="M15.5 29c2.4 3.6 5.6 5.2 8.5 5.2s6.1-1.6 8.5-5.2" stroke="currentColor" stroke-width="3.2" stroke-linecap="round"/></svg>';
const SMILEY_NOK = '<svg viewBox="0 0 48 48" fill="none"><circle cx="24" cy="24" r="19" stroke="currentColor" stroke-width="3.2"/><circle cx="17" cy="19" r="2.7" fill="currentColor"/><circle cx="31" cy="19" r="2.7" fill="currentColor"/><path d="M15.5 34c2.4-3.6 5.6-5.2 8.5-5.2s6.1 1.6 8.5 5.2" stroke="currentColor" stroke-width="3.2" stroke-linecap="round"/></svg>';

// Chapitres 1 & 2 : smiley vert (conforme) / smiley rouge (non conforme) + réserves
const CHECK_OPTS = [
  {v:'ok', svg:SMILEY_OK},
  {v:'nok', svg:SMILEY_NOK},
  {v:'avec_reserve', l:'Avec réserve'},
  {v:'sans_reserve', l:'Sans réserve'}
];
// Chapitre 3 (mobilier) : libellés texte inchangés
const STATUS_OPTS = [
  {v:'neuf', l:'Neuf'}, {v:'occasion', l:'Occasion'},
  {v:'avec_reserve', l:'Avec réserve'}, {v:'sans_reserve', l:'Sans réserve'}
];
const CONDUCTEURS = ['SCOTTO Nicolas','LAOUAR Hilel'];
const SOUS_TRAITANTS = ['CRPS','TCPP','TERMICLIM','CC BAT','EMS','CMTG','MOKRANE'];

// ==== Refacturation SAV : prix HT fourniture et pose (grille agence — ajustable) ====
const PRIX_SAV = [
  {cat:'Préfabriqué', items:[
    ['Panneau plein de 1,2m',222], ['Panneau plein de 0,9m',176], ['Panneau fenêtre standard',1520],
    ['Porte simple pleine de 0,9m',789], ['Cadre coulissant',144], ['Barreaudage fenêtre',228],
    ['Enrouleur de sangle',81], ['Remplacement volet roulant',400], ['Remplacement canon (clés non rendues)',56],
    ['Remplacement serrure complète',81], ['Ferme porte',193], ['Poignée de porte',69]
  ]},
  {cat:'Électricité', items:[
    ['Prise de courant 16A 2P+T',59], ['Interrupteur',59], ['Disjoncteur 10, 16 ou 20A',50],
    ['Inter différentiel 30mA',100], ['Bloc fluo en saillie 2 fois 36W',186], ['Bloc LED',111],
    ['Tableau électrique complet',363]
  ]},
  {cat:'Peinture / Sol', items:[
    ['Peinture par panneau (intérieur ou extérieur)',41], ['Peinture skid, longerons ou chenaux',48],
    ['Grattage d\'ossature (sur 1 longeron ou 1 panneau)',79], ['Masticage par panneau',49],
    ['Reprise d\'un revêtement sol complet (15 m²)',461], ['Rustine sur revêtement sol (par m²)',48]
  ]},
  {cat:'Plomberie / Sanitaire', items:[
    ['Chauffe-eau 15 litres',335], ['Chauffe-eau 300 litres',867], ['Limiteur de pression',93],
    ['Robinet mélangeur douche',243], ['Robinet mélangeur lavabo / évier',208], ['Ventilation mécanique VMC',340],
    ['Rideau de douche',48], ['Flexible et pomme de douche',59], ['Abattant WC',42]
  ]},
  {cat:'Climatisation / Chauffage', items:[
    ['Trappe clim',219], ['Remplacement climatiseur 2600W',674], ['Remplacement pompe à chaleur 2000W',942],
    ['Remplacement convecteur 2000W',107], ['Convecteur intelligent',129]
  ]},
  {cat:'Nettoyage', items:[
    ['Gros nettoyage intérieur ou extérieur',202],
    ['Forfait nettoyage module standard',80],
    ['Forfait nettoyage module sanitaire',120]
  ]}
];
const SIG_SCOTTO = 'data:image/png;base64,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'; // signature SCOTTO Nicolas (360x216)
const KM_RATE = 1.60; // € HT / km (déplacement)
const DEVIS_AGENCE = { nom:'LOXAM Module PACA', adresse:"346 All. Henri Moissan, 13130 Berre-l'Étang", tel:'04 42 46 72 80' };
const DEVIS_CONTACTS = { 'SCOTTO Nicolas':'07.85.60.55.14', 'LAOUAR Hilel':'' };

const CHECKLIST_SECTIONS = [
  { id:'proprete', num:'1', title:'1. Propreté des modules', items:[
    'Propreté intérieure','Propreté extérieure','Revetement de sols'
  ]},
  { id:'structure', num:'2', title:'2. État des panneaux et structure', items:[
    'Panneaux extérieurs','Panneaux intérieurs','Lames de plafond','Ossature','Portes','Fenêtres',
    'Serrures, poignées','Éclairage','Chauffage / climatisation','Sanitaires',
    'Escalier(s), Cage(s)','Palier(s)','Rampe(s) PMR','Garde-corps','Échelle a crinoline'
  ]},
];

const MOBILIER_ITEMS = ['Table(s)','Chaise(s)','Chaise(s) tissu','Armoire(s) vestiaire','Banc(s)','Armoire(s) bureau','Caisson(s)'];

const state = {
  meta:{ type:'reception', numAff:'', date:'', client:'', chantier:'', refCommande:'', nbModules:'', conducteur:'', sousTraitant:'', representant:'', civilite:'', email:'', tel:'' },
  checklist:{},
  cles:{ remises:'', rendues:'' },
  mobilier:{},
  casse:{ constat:'none', lignes:['','',''], photos:[] },
  acceptation:{ maitreOuvrage:'', delaiJours:'' },
  reserves:{ lignes:['','',''] },
  notice:{ statut:'' }, // 'remise' | 'non_concerne'
  devis:{ numero:'', date:'', descriptif:'Refacturation pour dommages constat\u00e9s lors de la restitution des modules', lignes:[], km:'', email:'' }, // lignes: {desc, qty, pu} — refacturation HT
  signatures:{
    receptionLoxam:{ nom:'', date:'', sig:'', entreprise:'' }, receptionClient:{ nom:'', date:'', sig:'', entreprise:'' },
    leveeLoxam:{ nom:'', date:'', sig:'', entreprise:'' }, leveeClient:{ nom:'', date:'', sig:'', entreprise:'' }
  }
};

CHECKLIST_SECTIONS.forEach(s => s.items.forEach(i => {
  state.checklist[s.id+'|'+i] = { statuses:[], obs:'', photos:[] };
}));
MOBILIER_ITEMS.forEach(i => { state.mobilier[i] = { qty:'', statuses:[], obs:'', photos:[] }; });

// Snapshot of the empty fiche, used to fully reset the form.
const INITIAL_STATE = JSON.parse(JSON.stringify(state));

// ---------- STORAGE ----------
async function saveDraft(silent){
  try{
    await window.storage.set('loxchek-draft', JSON.stringify(state), false);
    if(!silent) showToast('Fiche enregistrée sur cet appareil');
  }catch(e){ if(!silent) showToast("Échec de l'enregistrement"); }
}
async function loadDraft(){
  try{
    const res = await window.storage.get('loxchek-draft', false);
    if(res && res.value){
      const loaded = JSON.parse(res.value);
      Object.assign(state.meta, loaded.meta||{});
      Object.assign(state.checklist, loaded.checklist||{});
      Object.assign(state.cles, loaded.cles||{});
      Object.assign(state.mobilier, loaded.mobilier||{});
      Object.assign(state.casse, loaded.casse||{});
      Object.assign(state.acceptation, loaded.acceptation||{});
      Object.assign(state.reserves, loaded.reserves||{});
      Object.assign(state.notice, loaded.notice||{});
      Object.assign(state.signatures, loaded.signatures||{});
      if(loaded.devis){ Object.assign(state.devis, loaded.devis); }
      if(!Array.isArray(state.devis.lignes)) state.devis.lignes = [];
      if(state.devis.km===undefined || state.devis.km===null) state.devis.km='';
      if(state.meta.civilite===undefined) state.meta.civilite='';
      if(state.meta.email===undefined) state.meta.email='';
      if(state.devis.email===undefined) state.devis.email = state.meta.email || '';
      if(!state.devis.descriptif) state.devis.descriptif = 'Refacturation pour dommages constat\u00e9s lors de la restitution des modules';
      // Migrate old drafts: single `status` string -> `statuses` array
      const migrate = (store)=>{ Object.values(store).forEach(it=>{
        if(!Array.isArray(it.statuses)) it.statuses = it.status ? [it.status] : [];
        delete it.status;
      }); };
      migrate(state.checklist); migrate(state.mobilier);
      // Chapitres 1-2 : anciens statuts Neuf/Occasion -> smileys
      Object.values(state.checklist).forEach(it=>{
        it.statuses = it.statuses.map(s => s==='neuf' ? 'ok' : (s==='occasion' ? 'nok' : s));
      });
      // Normalise les blocs signature (nouveaux champs sig/entreprise)
      ['receptionLoxam','receptionClient','leveeLoxam','leveeClient'].forEach(k=>{
        state.signatures[k] = Object.assign({nom:'', date:'', sig:'', entreprise:''}, state.signatures[k]||{});
      });
      if(state.meta.representant){
        if(!state.acceptation.maitreOuvrage) state.acceptation.maitreOuvrage = state.meta.representant;
        if(!state.signatures.receptionClient.nom) state.signatures.receptionClient.nom = state.meta.representant;
        if(!state.signatures.leveeClient.nom) state.signatures.leveeClient.nom = state.meta.representant;
      }
    }
  }catch(e){ /* no draft yet */ }
}

function showToast(msg){
  const t = document.getElementById('toast');
  t.textContent = msg; t.classList.add('show');
  clearTimeout(showToast._h);
  showToast._h = setTimeout(()=>t.classList.remove('show'), 1800);
}

// ---------- RENDER HELPERS ----------
function el(tag, attrs={}, ...children){
  const e = document.createElement(tag);
  // Allow el(tag, "text"), el(tag, node) or el(tag, [..]) — a non-plain-object
  // second argument is treated as the first child rather than an attributes map.
  if (attrs === null || typeof attrs !== 'object' || attrs.nodeType || Array.isArray(attrs)) {
    if (attrs !== null && attrs !== undefined) children.unshift(attrs);
    attrs = {};
  }
  Object.entries(attrs).forEach(([k,v])=>{
    if(k==='class') e.className=v;
    else if(k.startsWith('on')) e.addEventListener(k.slice(2), v);
    else if(v!==undefined && v!==null) e.setAttribute(k,v);
  });
  children.flat().forEach(c=>{ if(c!=null) e.append(c.nodeType?c:document.createTextNode(c)); });
  return e;
}

// Paires exclusives : cocher l'un décoche l'autre (max 2 cases par ligne : état + réserve)
const STATUS_PAIRES = { ok:'nok', nok:'ok', neuf:'occasion', occasion:'neuf',
                        avec_reserve:'sans_reserve', sans_reserve:'avec_reserve' };

function statusCheckRow(keyId, statuses, onToggle, opts){
  const row = el('div',{class:'radio-row'});
  (opts||STATUS_OPTS).forEach(opt=>{
    const id = keyId+'-'+opt.v;
    const checked = statuses.includes(opt.v);
    const input = el('input',{type:'checkbox', id, ...(checked?{checked:'checked'}:{}) });
    input.addEventListener('change', ()=>{
      if(input.checked){
        const autre = STATUS_PAIRES[opt.v];
        if(autre){
          const sib = row.querySelector('input[id="'+keyId+'-'+autre+'"]');
          if(sib && sib.checked){ sib.checked = false; onToggle(autre, false); }
        }
      }
      onToggle(opt.v, input.checked);
    });
    const label = el('label',{class:'st-'+opt.v, for:id});
    if(opt.svg) label.innerHTML = opt.svg; else label.textContent = opt.l;
    row.append(input,label);
  });
  return row;
}

function photoRow(getPhotos, onAdd, onRemove){
  const wrap = el('div',{class:'photo-row'});
  const btn = el('label',{class:'photo-btn'}, '📷 Photo');
  const input = el('input',{type:'file', accept:'image/*', capture:'environment', style:'display:none'});
  input.addEventListener('change', (e)=>{
    const file = e.target.files[0];
    if(!file) return;
    const reader = new FileReader();
    reader.onload = ()=>{
      // Re-dessin sur canvas : applique l'orientation EXIF (photos droites dans le PDF) + réduit le poids
      const img = new Image();
      img.onload = ()=>{
        try{
          const MAXD = 1400;
          const iw = img.naturalWidth || img.width, ih = img.naturalHeight || img.height;
          const sc = Math.min(1, MAXD / Math.max(iw, ih));
          const cv = document.createElement('canvas');
          cv.width = Math.max(1, Math.round(iw*sc));
          cv.height = Math.max(1, Math.round(ih*sc));
          cv.getContext('2d').drawImage(img, 0, 0, cv.width, cv.height);
          onAdd(cv.toDataURL('image/jpeg', 0.85));
        }catch(err){ onAdd(reader.result); }
        renderAll();
      };
      img.onerror = ()=>{ onAdd(reader.result); renderAll(); };
      img.src = reader.result;
    };
    reader.readAsDataURL(file);
    input.value='';
  });
  btn.append(input);
  wrap.append(btn);
  getPhotos().forEach((src, idx)=>{
    const th = el('div',{class:'photo-thumb'},
      el('img',{src}),
      el('button',{class:'rm', onclick:()=>{ onRemove(idx); renderAll(); }},'×')
    );
    wrap.append(th);
  });
  return wrap;
}

function renderChecklistItem(key, storeObj, showQty, opts){
  const data = storeObj[key];
  const label = key.includes('|') ? key.split('|')[1] : key;
  const item = el('div',{class:'item'});
  item.append(el('div',{class:'item-title'}, label));
  if(showQty){
    item.append(el('div',{class:'item-qty'},
      el('label',{style:'font-size:11.5px;color:var(--sub);font-weight:600;margin-bottom:3px;display:block;'},'Quantité'),
      el('input',{type:'number', min:'0', placeholder:'0', value:data.qty,
        oninput:(e)=>{ data.qty = e.target.value; } })
    ));
  }
  item.append(statusCheckRow('r-'+key.replace(/\W/g,'_'), data.statuses, (v, on)=>{
    if(on){ if(!data.statuses.includes(v)) data.statuses.push(v); }
    else { data.statuses = data.statuses.filter(x=>x!==v); }
    updateProgress();
  }, opts));
  item.append(el('div',{class:'item-obs'},
    el('input',{type:'text', placeholder:'Observation (optionnel)', value:data.obs,
      oninput:(e)=>{ data.obs = e.target.value; } })
  ));
  item.append(photoRow(()=>data.photos, (src)=>data.photos.push(src), (idx)=>data.photos.splice(idx,1)));
  return item;
}

// ---------- SECTIONS ----------
// Le "Représentant client présent" alimente automatiquement le Maître de l'ouvrage
// (chapitre 4) et les deux signatures "Pour le CLIENT" (chapitre 6).
function syncSousTraitant(){
  // Sans conducteur de travaux : les blocs signature LOXAM MODULE / Sous-traitant
  // sont remplis avec l'entreprise sous-traitante de l'en-tête de fiche.
  if(!state.meta.conducteur && state.meta.sousTraitant){
    ['receptionLoxam','leveeLoxam'].forEach(k=>{
      state.signatures[k].nom = 'Sous-traitant';
      state.signatures[k].entreprise = state.meta.sousTraitant;
    });
  }
}
function devisAutorise(){
  // Refacturation visible uniquement pour un état des lieux sortant (démontage),
  // et réservée aux conducteurs de travaux
  return state.meta.type === 'sortant' && CONDUCTEURS.includes(state.meta.conducteur);
}
function todayISO(){ const d=new Date(), p=(n)=>String(n).padStart(2,'0'); return d.getFullYear()+'-'+p(d.getMonth()+1)+'-'+p(d.getDate()); }
function applyDatesParDefaut(){
  // Date du jour partout par défaut, modifiable ensuite
  const t = todayISO();
  if(!state.meta.date) state.meta.date = t;
  // Réception uniquement — les dates de Levée de réserves restent au choix
  ['receptionLoxam','receptionClient'].forEach(k=>{
    if(state.signatures[k] && !state.signatures[k].date) state.signatures[k].date = state.meta.date;
  });
  if(!state.devis.date) state.devis.date = t;
}
function syncDateFiche(v){
  // La date de la fiche alimente les dates du bloc Réception (LOXAM + CLIENT)
  state.signatures.receptionLoxam.date = v;
  state.signatures.receptionClient.date = v;
  document.querySelectorAll('.sync-datefiche').forEach(i=>{ i.value = v; });
}
function syncEmail(v){
  // L'email d'en-tête alimente l'email de contact du devis (ch. 7), modifiable ensuite
  state.meta.email = v;
  state.devis.email = v;
  document.querySelectorAll('.sync-email').forEach(i=>{ i.value = v; });
}
function syncRepresentant(v){
  state.meta.representant = v;
  state.acceptation.maitreOuvrage = v;
  state.signatures.receptionClient.nom = v;
  state.signatures.leveeClient.nom = v;
  document.querySelectorAll('.sync-representant').forEach(inp => { inp.value = v; });
}

function renderInfoSection(){
  const c = el('section',{class:'card', id:'sec-info'});
  const list = el('div',{class:'check-list'});
  [['reception',"Réception d'ouvrage (livraison)"],['levee','Levée de réserves'],['sortant','État des lieux sortant (démontage)']].forEach(([v,l])=>{
    const opt = el('div',{class:'check-opt' + (state.meta.type===v?' checked':'')},
      el('div',{class:'box'}, state.meta.type===v ? '✓' : ''),
      el('div',{class:'lbl'}, l)
    );
    opt.addEventListener('click', ()=>{ state.meta.type = v; renderAll(); });
    list.append(opt);
  });
  c.append(list);

  function textField(label, key, type='text'){
    return el('div',{class:'field'},
      el('label',label),
      el('input',{type:type, value:state.meta[key], oninput:(e)=>state.meta[key]=e.target.value})
    );
  }

  function selectField(label, key, options){
    const sel = el('select',{onchange:(e)=>{ state.meta[key]=e.target.value; }});
    sel.append(el('option',{value:''},'—'));
    options.forEach(o=>{
      sel.append(el('option',{value:o, ...(state.meta[key]===o?{selected:'selected'}:{})}, o));
    });
    if(state.meta[key] && !options.includes(state.meta[key])){
      sel.append(el('option',{value:state.meta[key], selected:'selected'}, state.meta[key]));
    }
    return el('div',{class:'field'}, el('label',label), sel);
  }

  const infoGrid = el('div',{class:'info-grid', style:'margin-top:14px;'});
  infoGrid.append(textField('N° Aff','numAff'));
  infoGrid.append((()=>{
    const f = textField('Date','date','date');
    f.querySelector('input').addEventListener('input',(e)=>syncDateFiche(e.target.value));
    return f;
  })());
  infoGrid.append(textField('Client','client'));
  infoGrid.append(textField('Chantier / Adresse','chantier'));
  infoGrid.append(textField('Réf. commande','refCommande'));
  infoGrid.append(textField('Nombre de modules','nbModules','number'));
  infoGrid.append((()=>{
    const sel = el('select',{onchange:(e)=>{ state.meta.conducteur=e.target.value; syncSousTraitant(); renderAll(); }});
    sel.append(el('option',{value:''},'\u2014'));
    CONDUCTEURS.forEach(o=> sel.append(el('option',{value:o, ...(state.meta.conducteur===o?{selected:'selected'}:{})}, o)));
    if(state.meta.conducteur && !CONDUCTEURS.includes(state.meta.conducteur))
      sel.append(el('option',{value:state.meta.conducteur, selected:'selected'}, state.meta.conducteur));
    return el('div',{class:'field'}, el('label','Conducteur de travaux'), sel);
  })());
  infoGrid.append((()=>{
    const sel = el('select',{onchange:(e)=>{ state.meta.sousTraitant=e.target.value; syncSousTraitant(); renderAll(); }});
    sel.append(el('option',{value:''},'\u2014'));
    SOUS_TRAITANTS.forEach(o=> sel.append(el('option',{value:o, ...(state.meta.sousTraitant===o?{selected:'selected'}:{})}, o)));
    if(state.meta.sousTraitant && !SOUS_TRAITANTS.includes(state.meta.sousTraitant))
      sel.append(el('option',{value:state.meta.sousTraitant, selected:'selected'}, state.meta.sousTraitant));
    return el('div',{class:'field'}, el('label','Sous-traitant présent'), sel);
  })());
  infoGrid.append((()=>{
    const sel = el('select',{onchange:(e)=>{ state.meta.civilite=e.target.value; }});
    [['','\u2014'],['M','M'],['Mme','Mme']].forEach(([v,l])=>
      sel.append(el('option',{value:v, ...(state.meta.civilite===v?{selected:'selected'}:{})}, l)));
    return el('div',{class:'field'}, el('label','Civilit\u00e9'), sel);
  })());
  // Ce champ alimente automatiquement "Monsieur ..." (ch. 4) et les signatures CLIENT (ch. 6)
  infoGrid.append(el('div',{class:'field'},
    el('label','Représentant client présent'),
    el('input',{type:'text', value:state.meta.representant, oninput:(e)=>syncRepresentant(e.target.value)})
  ));
  infoGrid.append(el('div',{class:'field'},
    el('label','Email de contact'),
    el('input',{type:'email', inputmode:'email', autocapitalize:'off', value:state.meta.email, oninput:(e)=>syncEmail(e.target.value)})
  ));
  const telField = textField('Tél','tel');
  telField.style.gridColumn = '2';
  infoGrid.append(telField);
  c.append(infoGrid);
  return c;
}

function renderChecklistSection(def){
  const c = el('section',{class:'card', id:'sec-'+def.id});
  c.append(el('h2', def.title));
  def.items.forEach(i => c.append(renderChecklistItem(def.id+'|'+i, state.checklist, false, CHECK_OPTS)));
  return c;
}

function renderClesSection(){
  const c = el('section',{class:'card', id:'sec-cles'});
  c.append(el('h2','3. Clés et mobilier'));
  c.append(el('div',{class:'keys-box'},
    el('div',{class:'keys-title'},'🔑 Clés'),
    el('div',{class:'keys-grid'},
      el('div',{},
        el('label','Nombre de clés remises'),
        el('input',{type:'number', min:'0', placeholder:'0', value:state.cles.remises, oninput:(e)=>state.cles.remises=e.target.value})),
      el('div',{},
        el('label','Nombre de clés rendues'),
        el('input',{type:'number', min:'0', placeholder:'0', value:state.cles.rendues, oninput:(e)=>state.cles.rendues=e.target.value}))
    )
  ));
  MOBILIER_ITEMS.forEach(i => c.append(renderChecklistItem(i, state.mobilier, true, STATUS_OPTS)));
  return c;
}

function renderCasseSection(){
  const c = el('section',{class:'card', id:'sec-casse'});
  c.append(el('h2','4. Casse et dommages constatés'));
  const row = el('div',{class:'radio-row'});
  [['none','Aucune casse / dommage constaté'],['some','Casse / dommage constaté (détail ci-dessous)']].forEach(([v,l])=>{
    const id='casse-'+v;
    const input = el('input',{type:'radio', name:'casse', id, ...(state.casse.constat===v?{checked:'checked'}:{})});
    input.addEventListener('change', ()=>{ state.casse.constat=v; renderAll(); });
    row.append(input, el('label',{class:'pv-generic', for:id}, l));
  });
  c.append(row);
  if(state.casse.constat==='some'){
    const ta = el('textarea',{placeholder:'— Détail des dommages constatés…'});
    ta.value = state.casse.lignes.join('\n');
    ta.addEventListener('input',(e)=>{ state.casse.lignes = e.target.value.split('\n'); });
    c.append(el('div',{class:'free-lines', style:'margin-top:10px;'}, ta));
    c.append(photoRow(()=>state.casse.photos, (src)=>state.casse.photos.push(src), (idx)=>state.casse.photos.splice(idx,1)));
  }

  const legal = el('div',{class:'legal-block'});
  legal.append(el('p',{}, 'Monsieur'));
  legal.append(el('input',{class:'sync-representant', type:'text', value:state.acceptation.maitreOuvrage,
    oninput:(e)=>state.acceptation.maitreOuvrage=e.target.value}));
  legal.append(el('p',{}, ", Maitre de l'ouvrage, reconnaît que les travaux ont été réalisés conformément aux plans et prescriptions des pieces contractuelle, déclare les accepter sous réserve des travaux désignés ci-apres qui devront etre excecutés dans un délai de"));
  legal.append(el('input',{type:'number', min:'0', value:state.acceptation.delaiJours,
    oninput:(e)=>state.acceptation.delaiJours=e.target.value}));
  legal.append(el('p',{}, 'jours au maximum.'));
  c.append(legal);
  return c;
}

function renderReservesSection(){
  const c = el('section',{class:'card', id:'sec-reserves'});
  c.append(el('h2','5. Réserves éventuelles'));
  const ta = el('textarea',{placeholder:'— Une réserve par ligne…'});
  ta.value = state.reserves.lignes.join('\n');
  ta.addEventListener('input',(e)=>{ state.reserves.lignes = e.target.value.split('\n'); });
  c.append(el('div',{class:'free-lines'}, ta));

  const row = el('div',{class:'check-row', style:'margin-top:12px;'});
  [['remise','Remise'],['non_concerne','Non concerné']].forEach(([v,l])=>{
    const active = state.notice.statut===v;
    const opt = el('div',{class:'check-opt'+(active?' checked':'')},
      el('div',{class:'box'}, active ? '✓' : ''),
      el('div',{class:'lbl'}, l)
    );
    opt.addEventListener('click', ()=>{ state.notice.statut = active ? '' : v; renderAll(); });
    row.append(opt);
  });
  c.append(el('p',{style:'font-size:13px;color:var(--ink);font-weight:700;text-align:center;margin:6px 0;'},
    "En cas de vente, la notice d'entretien et de maintenance est remise au client."));
  c.append(row);
  return c;
}

function sigPad(sigObj){
  const wrap = el('div',{class:'sig-pad'});
  const canvas = el('canvas',{class:'sig-canvas', width:'600', height:'200'});
  let ctx = null;
  try{ ctx = canvas.getContext('2d'); }catch(e){}
  if(ctx && sigObj.sig){
    try{
      const img = new Image();
      img.onload = ()=>{ try{ ctx.drawImage(img,0,0,canvas.width,canvas.height); }catch(e){} };
      img.src = sigObj.sig;
    }catch(e){}
  }
  let drawing = false;
  const pos = (e)=>{
    const r = canvas.getBoundingClientRect();
    return { x:(e.clientX-r.left)*canvas.width/r.width, y:(e.clientY-r.top)*canvas.height/r.height };
  };
  canvas.addEventListener('pointerdown', (e)=>{
    if(!ctx) return;
    drawing = true;
    try{ canvas.setPointerCapture(e.pointerId); }catch(err){}
    const p = pos(e); ctx.beginPath(); ctx.moveTo(p.x, p.y);
    e.preventDefault();
  });
  canvas.addEventListener('pointermove', (e)=>{
    if(!drawing || !ctx) return;
    const p = pos(e);
    ctx.lineWidth = 3.5; ctx.lineCap = 'round'; ctx.lineJoin = 'round'; ctx.strokeStyle = '#111827';
    ctx.lineTo(p.x, p.y); ctx.stroke();
    e.preventDefault();
  });
  ['pointerup','pointercancel','pointerleave'].forEach(t=>canvas.addEventListener(t, ()=>{
    if(drawing){ drawing = false; try{ sigObj.sig = canvas.toDataURL('image/png'); }catch(e){} }
  }));
  const clear = el('button',{class:'sig-clear', onclick:(e)=>{
    e.preventDefault();
    if(ctx){ try{ ctx.clearRect(0,0,canvas.width,canvas.height); }catch(err){} }
    sigObj.sig = '';
  }},'✕ Effacer la signature');
  const overlay = el('div',{class:'sig-lock'}, sigObj.sig ? '👆 Appuyer pour modifier la signature' : '👆 Appuyer pour signer');
  overlay.addEventListener('click', ()=>{ overlay.style.display='none'; });
  wrap.append(
    el('div',{class:'sig-pad-label'},'Signature (au doigt ou stylet)'),
    el('div',{class:'sig-cwrap'}, canvas, overlay),
    clear
  );
  return wrap;
}

function sigCellClient(label, sigObj){
  return el('div',{class:'sig-cell'},
    el('div',{class:'sig-label'}, label),
    el('input',{class:'sync-representant', type:'text', placeholder:'Nom', value:sigObj.nom, oninput:(e)=>sigObj.nom=e.target.value}),
    el('input',{type:'date', class:(sigObj===state.signatures.receptionClient?'sync-datefiche':''), value:sigObj.date, oninput:(e)=>sigObj.date=e.target.value}),
    sigPad(sigObj)
  );
}

function sigCellLoxam(label, sigObj){
  const cell = el('div',{class:'sig-cell'});
  cell.append(el('div',{class:'sig-label'}, label));
  const sel = el('select',{onchange:(e)=>{ sigObj.nom = e.target.value; renderAll(); }});
  sel.append(el('option',{value:''},'—'));
  [...CONDUCTEURS, 'Sous-traitant'].forEach(o=>{
    sel.append(el('option',{value:o, ...(sigObj.nom===o?{selected:'selected'}:{})}, o));
  });
  if(sigObj.nom && ![...CONDUCTEURS,'Sous-traitant'].includes(sigObj.nom)){
    sel.append(el('option',{value:sigObj.nom, selected:'selected'}, sigObj.nom));
  }
  cell.append(sel);
  if(sigObj.nom === 'Sous-traitant'){
    cell.append(el('input',{type:'text', placeholder:"Nom de l'entreprise", value:sigObj.entreprise||'',
      oninput:(e)=>sigObj.entreprise=e.target.value}));
  }
  cell.append(el('input',{type:'date', class:(sigObj===state.signatures.receptionLoxam?'sync-datefiche':''), value:sigObj.date, oninput:(e)=>sigObj.date=e.target.value}));
  if(sigObj.nom === 'SCOTTO Nicolas'){
    cell.append(el('div',{class:'sig-auto'},
      el('img',{src:SIG_SCOTTO, alt:'Signature SCOTTO Nicolas'}),
      el('div',{class:'sig-auto-note'},'Signature appliquée automatiquement')));
  } else {
    cell.append(sigPad(sigObj));
  }
  return cell;
}

function renderSignaturesSection(){
  const c = el('section',{class:'card', id:'sec-signatures'});
  c.append(el('h2','6. Signatures'));
  c.append(el('div',{style:'height:12px;'}));

  c.append(el('div',{class:'sig-block'},
    el('h3','Réception'),
    el('div',{class:'sig-grid'},
      sigCellLoxam('Pour LOXAM MODULE / Sous-traitant — Nom / Visa', state.signatures.receptionLoxam),
      sigCellClient('Pour le CLIENT — Nom / Visa', state.signatures.receptionClient)
    )
  ));

  c.append(el('p',{style:'font-size:11.5px;color:var(--ink);font-style:italic;font-weight:700;margin:0 0 16px;'},
    "En conséquence, le présent proces verbal de réception a été établi sous réserve de tous recours en responsabilité pour vice ou défauts cachés pouvant se manifester par la suite."));

  c.append(el('div',{class:'sig-block'},
    el('h3','Levées de Réserve'),
    el('div',{class:'sig-grid'},
      sigCellLoxam('Pour LOXAM MODULE / Sous-traitant — Nom / Visa', state.signatures.leveeLoxam),
      sigCellClient('Pour le CLIENT — Nom / Visa', state.signatures.leveeClient)
    )
  ));
  return c;
}

function renderAll(){
  const main = document.getElementById('sections');
  const scrollY = window.scrollY;
  main.innerHTML = '';
  main.append(renderInfoSection());
  CHECKLIST_SECTIONS.forEach(def => main.append(renderChecklistSection(def)));
  main.append(renderClesSection());
  main.append(renderCasseSection());
  main.append(renderReservesSection());
  main.append(renderSignaturesSection());
  if(devisAutorise()) main.append(renderDevisSection());
  main.append(el('footer',{class:'legal-footer'},
    'LOXAM MODULE – S.A.S.U. CAPITAL DE 222 559 930 € - SIÈGE SOCIAL : 276, RUE NICOLAS COATANLEM – 56850 CAUDAN – R.C.S. LORIENT B 433 911 948 – N° T.V.A : FR27 433 911 948 – NAF 7732Z'
  ));
  const db = document.getElementById('devisBtn');
  if(db) db.style.display = devisAutorise() ? '' : 'none';
  window.scrollTo(0, scrollY);
  updateProgress();
}

function updateProgress(){
  const all = Object.values(state.checklist).concat(Object.values(state.mobilier));
  const done = all.filter(i=>i.statuses && i.statuses.length).length;
  const pct = Math.round((done/all.length)*100);
  document.getElementById('progressFill').style.width = pct+'%';
}
// ---------- PDF EXPORT ----------
const STATUS_LABEL = {ok:'Conforme', nok:'Non conforme', neuf:'Neuf', occasion:'Occasion', avec_reserve:'Avec réserve', sans_reserve:'Sans réserve'};
const STATUS_PDF_COLORS = {
  ok:[30,158,90], nok:[198,54,46], neuf:[30,158,90], occasion:[44,111,176], avec_reserve:[198,54,46], sans_reserve:[136,145,160]
};

function renderDevisSection(){
  const c = el('section',{class:'card', id:'sec-devis'});
  c.append(el('h2',{class:'chap'},'7. Refacturation'));

  const g = el('div',{class:'grid2', style:'margin-top:10px;'});
  g.append(el('div',{class:'field'}, el('label','Numéro de devis'),
    el('input',{type:'text', value:state.devis.numero, oninput:(e)=>state.devis.numero=e.target.value})));
  g.append(el('div',{class:'field'}, el('label','Date du devis'),
    el('input',{type:'date', value:state.devis.date, oninput:(e)=>state.devis.date=e.target.value})));
  c.append(g);

  const df = el('div',{class:'field', style:'margin-top:4px;'});
  df.append(el('label','Descriptif des travaux'));
  const ta = el('textarea',{style:'width:100%;min-height:72px;border:1px solid var(--line);border-radius:9px;padding:10px;font-size:14px;',
    oninput:(e)=>state.devis.descriptif=e.target.value});
  ta.value = state.devis.descriptif;
  df.append(ta);
  c.append(df);
  c.append(el('div',{class:'field', style:'margin-top:10px;'},
    el('label','Email de contact'),
    el('input',{class:'sync-email', type:'email', inputmode:'email', autocapitalize:'off',
      value:state.devis.email||'', oninput:(e)=>state.devis.email=e.target.value})
  ));
  // Ajout d'une prestation depuis la grille SAV
  const addWrap = el('div',{class:'devis-add'});
  const sel = el('select',{});
  sel.append(el('option',{value:''},'\u2014 Refacturation \u2014'));
  PRIX_SAV.forEach((grp,gi)=>{
    const og = el('optgroup',{label:grp.cat});
    grp.items.forEach((it,ii)=> og.append(el('option',{value:gi+'|'+ii}, it[0]+' \u2014 '+it[1]+' \u20ac')));
    sel.append(og);
  });
  const addBtn = el('button',{class:'mini-btn red', onclick:()=>{
    if(!sel.value) return;
    const [gi,ii] = sel.value.split('|').map(Number);
    const it = PRIX_SAV[gi].items[ii];
    state.devis.lignes.push({desc:it[0], qty:'1', pu:String(it[1])});
    renderAll();
  }},'+ Ajouter');
  addWrap.append(sel, addBtn);
  c.append(addWrap);

  const totalSpan = el('span');
  const updTotal = ()=>{ totalSpan.textContent = devisTotalHT().toFixed(2) + ' \u20ac HT'; };

  if(state.devis.lignes.length){
    c.append(el('div',{class:'devis-cols'},
      el('span',{class:'c-desc'},'Code'), el('span',{class:'c-qty'},'Qt\u00e9'),
      el('span',{class:'c-mt'},'Montant'), el('span',{class:'c-del'},'')
    ));
  }
  state.devis.lignes.forEach((l, idx)=>{
    const mt = el('span',{class:'d-mt'});
    const updMt = ()=>{ mt.textContent = ((parseFloat(l.qty)||0)*(parseFloat(l.pu)||0)).toFixed(2); };
    const row = el('div',{class:'devis-row'},
      el('span',{class:'d-desc'}, 'Refact'+String(idx+1).padStart(2,'0')),
      el('input',{class:'d-qty', type:'number', step:'any', min:'0', value:l.qty, oninput:(e)=>{ l.qty=e.target.value; updMt(); updTotal(); }}),
      mt,
      el('button',{class:'d-del', onclick:()=>{ state.devis.lignes.splice(idx,1); renderAll(); }},'\u2715')
    );
    updMt();
    c.append(row);
  });

  // Ligne fixe : Déplacement au kilomètre (x 1,60 € / km)
  const depMt = el('span',{class:'d-mt'});
  const updDep = ()=>{ depMt.textContent = ((parseFloat(state.devis.km)||0)*KM_RATE).toFixed(2); };
  const depRow = el('div',{class:'devis-row devis-dep'},
    el('span',{class:'d-desc'}, 'D\u00e9p'),
    el('input',{class:'d-km', type:'number', step:'any', min:'0', inputmode:'decimal', placeholder:'km',
      value:state.devis.km, oninput:(e)=>{ state.devis.km=e.target.value; updDep(); updTotal(); }}),
    depMt,
    el('span',{class:'d-del-spacer'})
  );
  updDep();
  c.append(depRow);

  // Légende des codes (affichage app uniquement — pas dans le PDF)
  if(state.devis.lignes.length){
    const leg = el('div',{class:'devis-legende'});
    state.devis.lignes.forEach((l,i)=>
      leg.append(el('div',{}, 'Refact'+String(i+1).padStart(2,'0')+' \u2014 '+(l.desc||''))));
    c.append(leg);
  }

  const tot = el('div',{class:'devis-total'}, 'Total HT : ', totalSpan);
  updTotal();
  c.append(tot);
  return c;
}

function devisTotalHT(){
  const lignes = state.devis.lignes.reduce((s,l)=> s + (parseFloat(l.qty)||0)*(parseFloat(l.pu)||0), 0);
  return lignes + (parseFloat(state.devis.km)||0)*KM_RATE;
}

function importReservesDansDevis(){
  const out = [];
  const lbl = {nok:'non conforme', avec_reserve:'avec r\u00e9serve'};
  CHECKLIST_SECTIONS.forEach(s=> s.items.forEach(i=>{
    const it = state.checklist[s.id+'|'+i];
    const bad = (it.statuses||[]).filter(x=> x==='nok' || x==='avec_reserve');
    if(bad.length) out.push('- '+i+' ('+bad.map(b=>lbl[b]).join(', ')+')'+(it.obs ? ' : '+it.obs : ''));
  }));
  Object.entries(state.mobilier).forEach(([i,it])=>{
    const bad = (it.statuses||[]).filter(x=> x==='avec_reserve');
    if(bad.length) out.push('- '+i+' (avec r\u00e9serve)'+(it.obs ? ' : '+it.obs : ''));
  });
  (state.casse.lignes||[]).filter(Boolean).forEach(t=> out.push('- Casse : '+t));
  (state.reserves.lignes||[]).filter(Boolean).forEach(t=> out.push('- R\u00e9serve : '+t));
  state.devis.descriptif = out.join('\n');
  renderAll();
  showToast(out.length ? 'R\u00e9serves reprises dans le descriptif' : 'Aucune r\u00e9serve \u00e0 reprendre');
}

async function generateDevisPDF(){
  if (!window.jspdf) {
    throw new Error("La librairie PDF ne s'est pas charg\u00e9e. Ouvre la page via un serveur local ou une URL h\u00e9berg\u00e9e, pas en double-cliquant sur le fichier.");
  }
  const kmVal = parseFloat(state.devis.km)||0;
  if (!state.devis.lignes.length && !(kmVal>0)) {
    throw new Error('Ajoute au moins une ligne au devis (chapitre 7).');
  }
  const { jsPDF } = window.jspdf;
  const doc = new jsPDF({unit:'pt', format:'a4'});
  const W = doc.internal.pageSize.getWidth();
  const M = 48;
  const REDD = [200,16,46], WARMB = [219,212,201], WARMF = [250,246,240];

  // Logo (repris de l'en-t\u00eate de l'app)
  try{
    const logoSrc = document.querySelector('.topbar img').src;
    doc.addImage(logoSrc, 'PNG', M, 40, 110, 39);
  }catch(e){}

  // Encadr\u00e9 "Devis" en haut \u00e0 droite
  doc.setFillColor(REDD[0],REDD[1],REDD[2]);
  doc.roundedRect(W-M-120, 40, 120, 32, 9, 9, 'F');
  doc.setFont('helvetica','bold'); doc.setFontSize(16); doc.setTextColor(255);
  doc.text('Devis', W-M-60, 61, {align:'center'});
  doc.setTextColor(17,24,39);

  // Bloc agence
  doc.setFontSize(12); doc.setTextColor(REDD[0],REDD[1],REDD[2]); doc.text(DEVIS_AGENCE.nom, M, 100); doc.setTextColor(17,24,39);
  doc.setFont('helvetica','normal'); doc.setFontSize(9);
  doc.text(DEVIS_AGENCE.adresse, M, 113);
  doc.text(DEVIS_AGENCE.tel, M, 125);

  // Client
  doc.setFontSize(10.5);
  if(state.meta.client) doc.text(String(state.meta.client), 330, 152);
  if(state.meta.chantier){ doc.setFontSize(9); doc.setTextColor(90); doc.text(String(state.meta.chantier), 330, 165); doc.setTextColor(0); }

  // Tableau Num\u00e9ro / Date
  const frDate = state.devis.date ? state.devis.date.split('-').reverse().join('/')
                                   : new Date().toLocaleDateString('fr-FR');
  const tX = M, tY = 186, cw = 92, rh = 22;
  doc.setFillColor(REDD[0],REDD[1],REDD[2]); doc.setDrawColor(REDD[0],REDD[1],REDD[2]); doc.setLineWidth(0.8);
  doc.roundedRect(tX, tY, 2*cw, rh, 6, 6, 'FD');
  doc.setDrawColor(255); doc.setLineWidth(0.6);
  doc.line(tX+cw, tY+4, tX+cw, tY+rh-4);
  doc.setDrawColor(WARMB[0],WARMB[1],WARMB[2]); doc.setLineWidth(0.8);
  doc.rect(tX, tY+rh, cw, rh); doc.rect(tX+cw, tY+rh, cw, rh);
  doc.setFont('helvetica','bold'); doc.setFontSize(10); doc.setTextColor(255);
  doc.text('Num\u00e9ro', tX+cw/2, tY+15, {align:'center'});
  doc.text('Date', tX+cw+cw/2, tY+15, {align:'center'});
  doc.setTextColor(17,24,39); doc.setFont('helvetica','normal');
  doc.text(state.devis.numero || '\u2014', tX+cw/2, tY+rh+15, {align:'center'});
  doc.text(frDate, tX+cw+cw/2, tY+rh+15, {align:'center'});

  // Encadr\u00e9 "Descriptif des travaux"
  const bx = tX + 2*cw + 26, bw = W - M - bx;
  doc.setFont('helvetica','bolditalic'); doc.setFontSize(9.5);
  const typeLbl = state.meta.type==='sortant' ? "l'état des lieux sortant des modules"
                : state.meta.type==='levee' ? 'la levée de réserves des modules'
                : 'la réception des modules';
  const descTxt = state.devis.descriptif
    || ('Devis dégradations suite à ' + typeLbl + (state.meta.chantier ? ' - ' + state.meta.chantier : ''));
  const descLines = doc.splitTextToSize(descTxt, bw-16);
  const bh = Math.max(52, 24 + descLines.length*11 + 8);
  doc.setFillColor(WARMF[0],WARMF[1],WARMF[2]); doc.setDrawColor(233,225,212); doc.setLineWidth(0.9);
  doc.roundedRect(bx, tY-4, bw, bh, 6, 6, 'FD');
  doc.setTextColor(REDD[0],REDD[1],REDD[2]);
  doc.text('Descriptif des travaux:', bx+8, tY+10);
  doc.setTextColor(17,24,39);
  doc.setFont('helvetica','normal'); doc.setFontSize(9);
  if(descLines.length) doc.text(descLines, bx+8, tY+23);

  // Tableau principal
  let y = Math.max(tY + 2*rh, tY - 4 + bh) + 26;
  const tableTop = y;
  const wDesc = 300, wQty = 55, wPu = 72, wMt = W - 2*M - wDesc - wQty - wPu;
  const xDesc = M, xQty = M+wDesc, xPu = xQty+wQty, xMt = xPu+wPu, xEnd = W-M;
  const headH = 22;
  doc.setFillColor(REDD[0],REDD[1],REDD[2]); doc.setDrawColor(REDD[0],REDD[1],REDD[2]); doc.setLineWidth(0.8);
  doc.roundedRect(xDesc, y, xEnd-xDesc, headH, 6, 6, 'FD');
  doc.setDrawColor(255); doc.setLineWidth(0.6);
  [xQty, xPu, xMt].forEach(xx=> doc.line(xx, y+4, xx, y+headH-4));
  doc.setFont('helvetica','bold'); doc.setFontSize(10); doc.setTextColor(255);
  doc.text('Description', xDesc+wDesc/2, y+15, {align:'center'});
  doc.text('Qt\u00e9', xQty+wQty/2, y+15, {align:'center'});
  doc.text('P.U. HT', xPu+wPu/2, y+15, {align:'center'});
  doc.text('Montant HT', xMt+wMt/2, y+15, {align:'center'});
  doc.setTextColor(17,24,39);
  y += headH;

  doc.setFont('helvetica','normal'); doc.setFontSize(9.5);
  let total = 0;
  const pdfLignes = state.devis.lignes.slice();
  if(kmVal>0) pdfLignes.push({desc:'D\u00e9placement ('+String(state.devis.km).replace('.',',')+' km)', qty:String(kmVal), pu:String(KM_RATE)});
  pdfLignes.forEach((l, li)=>{
    const qty = parseFloat(l.qty)||0, pu = parseFloat(l.pu)||0, mt = qty*pu;
    total += mt;
    const lines = doc.splitTextToSize('- ' + (l.desc || '\u2014'), wDesc-14);
    const rowH = Math.max(31, lines.length*11 + 18);
    if(y + rowH > 700){ doc.addPage(); y = 60; }
    if(li % 2 === 1){
      doc.setFillColor(251,247,241);
      doc.rect(xDesc+1, y, (xEnd-xDesc)-2, rowH, 'F');
    }
    doc.text(lines, xDesc+7, y+19);
    doc.text((qty%1===0 ? String(qty) : qty.toFixed(2)), xQty+wQty-8, y+19, {align:'right'});
    doc.text(pu.toFixed(2), xPu+wPu-8, y+19, {align:'right'});
    doc.text(mt.toFixed(2), xMt+wMt-8, y+19, {align:'right'});
    y += rowH;
  });

  // Grande zone du tableau (comme le mod\u00e8le), colonnes prolong\u00e9es
  const boxBottom = Math.max(y, Math.min(620, 700));
  const bodyTop = tableTop + headH;
  doc.setLineWidth(0.8); doc.setDrawColor(WARMB[0],WARMB[1],WARMB[2]);
  doc.rect(xDesc, bodyTop, xEnd-xDesc, boxBottom-bodyTop);
  doc.line(xQty, bodyTop, xQty, boxBottom);
  doc.line(xPu, bodyTop, xPu, boxBottom);
  doc.line(xMt, bodyTop, xMt, boxBottom);
  doc.setDrawColor(0);
  y = boxBottom;

  // Formule de politesse + contact
  doc.setFontSize(9);
  const polit = doc.splitTextToSize("Restant \u00e0 votre disposition pour tous renseignements compl\u00e9mentaires, veuillez agr\u00e9er, l'expression de nos salutations respectueuses.", 290);
  doc.text(polit, M, y+26);
  const contact = state.meta.conducteur || CONDUCTEURS[0];
  doc.setFont('helvetica','bold'); doc.setFontSize(10);
  doc.text(contact.toUpperCase(), 370, y+26);
  doc.setFont('helvetica','normal'); doc.setFontSize(9);
  doc.text('Conducteur de Travaux', 370, y+38);
  const telC = DEVIS_CONTACTS[contact] || '';
  if(telC) doc.text(telC, 370, y+50);
  try{
    if(contact === 'SCOTTO Nicolas'){
      doc.addImage(SIG_SCOTTO, 'PNG', 370, y+54, 66, 40);
    } else {
      const s = state.signatures.receptionLoxam;
      if(s && s.sig) doc.addImage(s.sig, 'PNG', 370, y+56, 100, 33);
    }
  }catch(e){}

  // Total HT
  const totY = Math.min(y + 110, 792);
  doc.setFillColor(REDD[0],REDD[1],REDD[2]); doc.setDrawColor(REDD[0],REDD[1],REDD[2]); doc.setLineWidth(1);
  doc.roundedRect(W-M-300, totY, 170, 26, 6, 6, 'FD');
  doc.setFillColor(WARMF[0],WARMF[1],WARMF[2]); doc.setDrawColor(WARMB[0],WARMB[1],WARMB[2]);
  doc.roundedRect(W-M-130, totY, 130, 26, 6, 6, 'FD');
  doc.setFont('helvetica','bold'); doc.setFontSize(10.5);
  doc.setTextColor(255); doc.text('Total HT', W-M-294, totY+17);
  doc.setTextColor(17,24,39); doc.text(total.toFixed(2)+'\u20acht', W-M-8, totY+17, {align:'right'});
  doc.setFont('helvetica','normal'); doc.setFontSize(9); doc.setTextColor(17,24,39);
  // Nom / Téléphone / Email sur 3 lignes distinctes
  const reprLigne = ((state.meta.civilite ? state.meta.civilite + ' ' : '') + (state.meta.representant || '')).trim();
  if(reprLigne) doc.text(reprLigne, M, totY+2);
  if(state.meta.tel) doc.text(state.meta.tel, M, totY+13);
  if(state.devis.email) doc.text(state.devis.email, M, totY+24);

  // Pied de page légal (identique au PV)
  doc.setFont('helvetica','normal'); doc.setFontSize(7.5); doc.setTextColor(128,118,104);
  doc.text('LOXAM MODULE – S.A.S.U. CAPITAL DE 222 559 930 € - SIÈGE SOCIAL : 276, RUE NICOLAS COATANLEM – 56850 CAUDAN –', W/2, 806, {align:'center'});
  doc.text('R.C.S. LORIENT B 433 911 948 – N° T.V.A : FR27 433 911 948 – NAF 7732Z', W/2, 816, {align:'center'});
  doc.setTextColor(17,24,39);

  const fname = 'Devis LOXAM Module - ' + ((state.meta.client||'client').trim().replace(/[\\/:*?"<>|]/g,'-') || 'client') + '.pdf';
  if (window.Android && window.Android.savePdf) {
    const base64 = doc.output('datauristring').split(',')[1];
    window.Android.savePdf(base64, fname);
  } else {
    doc.save(fname);
  }
}

async function generatePDF(){
  if (!window.jspdf) {
    throw new Error("La librairie PDF ne s'est pas chargée. Ouvre la page via un serveur local ou une URL hébergée, pas en double-cliquant sur le fichier.");
  }
  const { jsPDF } = window.jspdf;
  const doc = new jsPDF({unit:'pt', format:'a4'});
  const W = doc.internal.pageSize.getWidth();
  const M = 46, CW = W - 2*M, MAXY = 768;
  const RED = [200,16,46];
  let LOGO = '';
  try{ LOGO = document.querySelector('.topbar img').src; }catch(e){}
  const frDate = (d)=> d ? String(d).split('-').reverse().join('/') : '';

  function dotted(x, yy, w){
    doc.setDrawColor(186,177,163); doc.setLineWidth(0.7);
    doc.setLineDashPattern([1,1.6],0);
    doc.line(x, yy, x+w, yy);
    doc.setLineDashPattern([],0);
  }
  function checkbox(x, yy, s, checked, col){
    doc.setDrawColor(176,167,154); doc.setLineWidth(0.9);
    doc.setFillColor(255,255,255);
    doc.roundedRect(x, yy, s, s, 2, 2, 'FD');
    if(checked){
      const c = col || RED;
      doc.setDrawColor(c[0],c[1],c[2]); doc.setLineWidth(1.6);
      doc.line(x+s*0.2, yy+s*0.55, x+s*0.42, yy+s*0.78);
      doc.line(x+s*0.42, yy+s*0.78, x+s*0.85, yy+s*0.2);
      doc.setDrawColor(30);
    }
  }
  function colColor(c){
    if(c.smiley === true) return [30,158,90];
    if(c.smiley === false) return [198,54,46];
    const t = ((c.t2 ? c.t2.join(' ') : (c.t||''))+'').toUpperCase();
    if(t.indexOf('NEUF')>-1) return [30,158,90];
    if(t.indexOf('OCCASION')>-1) return [44,111,176];
    if(t.indexOf('SANS')>-1) return [107,114,128];
    return RED;
  }
  function smiley(cx, cy, r, happy){
    doc.setDrawColor(255); doc.setFillColor(255); doc.setLineWidth(1.3);
    doc.circle(cx, cy, r, 'S');
    doc.circle(cx-r*0.36, cy-r*0.3, r*0.16, 'F');
    doc.circle(cx+r*0.36, cy-r*0.3, r*0.16, 'F');
    if(happy){
      doc.lines([[r*0.25, r*0.42, r*0.65, r*0.42, r*0.9, 0]], cx-r*0.45, cy+r*0.12, [1,1], 'S');
    } else {
      doc.lines([[r*0.25, -r*0.42, r*0.65, -r*0.42, r*0.9, 0]], cx-r*0.45, cy+r*0.5, [1,1], 'S');
    }
    // Restaurer les couleurs pour ne pas casser les cellules suivantes
    doc.setFillColor(RED[0],RED[1],RED[2]);
    doc.setDrawColor(30); doc.setLineWidth(0.7);
  }
  function pageChrome(){
    // Bandeau d'en-tête doux
    doc.setFillColor(248,244,237);
    doc.setDrawColor(235,227,214); doc.setLineWidth(0.8);
    doc.roundedRect(M-8, 18, CW+16, 66, 8, 8, 'FD');
    try{ if(LOGO) doc.addImage(LOGO, 'PNG', M+8, 30, 118, 42); }catch(e){}
    doc.setTextColor(RED[0],RED[1],RED[2]);
    doc.setFont('helvetica','bold'); doc.setFontSize(13.5);
    doc.text('FICHE ÉTAT DES LIEUX', W-M-8, 48, {align:'right'});
    doc.setTextColor(128,118,104); doc.setFontSize(8.6);
    doc.text('AGENCE 9112  ·  LOXAM MODULE', W-M-8, 64, {align:'right'});
    // Barre d'accent rouge
    doc.setFillColor(RED[0],RED[1],RED[2]);
    doc.roundedRect(M-8, 90, CW+16, 4, 2, 2, 'F');
    // Pied de page
    doc.setDrawColor(RED[0],RED[1],RED[2]); doc.setLineWidth(0.9);
    doc.line(M, 796, W-M, 796);
    doc.setTextColor(120,127,138);
    doc.setFont('helvetica','bold'); doc.setFontSize(7.4);
    doc.text('LOXAM MODULE – S.A.S.U. CAPITAL DE 222 559 930 €- SIEGE SOCIAL : 276, RUE NICOLAS COATANLEM –', W/2, 806, {align:'center'});
    doc.text('56850 CAUDAN – R.C.S. LORIENT B 433 911 948 – N° T.V.A : FR27 433 911 948 – NAF 7732Z', W/2, 817, {align:'center'});
    doc.setTextColor(17,24,39);
  }
  function newPage(){ doc.addPage(); pageChrome(); return 150; }

  function tableHeader(yy, cols){
    const totalW = cols.reduce((t,c)=>t+c.w, 0);
    doc.setFillColor(RED[0],RED[1],RED[2]);
    doc.setDrawColor(RED[0],RED[1],RED[2]); doc.setLineWidth(0.7);
    doc.roundedRect(M, yy, totalW, 28, 6, 6, 'FD');
    let x = M;
    cols.forEach((c, ci)=>{
      if(ci > 0){
        doc.setDrawColor(255,255,255); doc.setLineWidth(0.6);
        doc.line(x, yy+6, x, yy+22);
      }
      if(c.smiley !== undefined){
        smiley(x+c.w/2, yy+14, 8.5, c.smiley);
      } else if(c.t2){
        doc.setFont('helvetica','bold'); doc.setFontSize(7.6); doc.setTextColor(255);
        doc.text(c.t2[0], x+c.w/2, yy+12, {align:'center'});
        doc.text(c.t2[1], x+c.w/2, yy+21, {align:'center'});
      } else {
        doc.setFont('helvetica','bold'); doc.setFontSize(8.2); doc.setTextColor(255);
        doc.text(c.t, x+c.w/2, yy+17.5, {align:'center'});
      }
      x += c.w;
    });
    doc.setDrawColor(30); doc.setLineWidth(0.7);
    return yy + 28;
  }
  function statusTable(yy, cols, rows){
    yy = tableHeader(yy, cols);
    const rh = 24;
    rows.forEach((r, ri)=>{
      if(yy + rh > MAXY){ yy = newPage(); yy = tableHeader(yy, cols); }
      let x = M;
      cols.forEach((c, ci)=>{
        doc.setDrawColor(219,212,201); doc.setLineWidth(0.6);
        if(ri % 2 === 1){
          doc.setFillColor(251,247,241);
          doc.rect(x, yy, c.w, rh, 'FD');
        } else {
          doc.rect(x, yy, c.w, rh);
        }
        if(ci === 0){
          doc.setFont('helvetica','normal'); doc.setFontSize(9); doc.setTextColor(17,24,39);
          doc.text(r.name, x+5, yy+15, {maxWidth:c.w-9});
        } else if(c.isQty){
          doc.setFont('helvetica','normal'); doc.setFontSize(9);
          if(r.qty) doc.text(String(r.qty), x+c.w/2, yy+15, {align:'center'});
        } else if(c.isObs){
          if(r.obs){
            doc.setFont('helvetica','normal'); doc.setFontSize(8); doc.setTextColor(17,24,39);
            const ol = doc.splitTextToSize(String(r.obs), c.w-10);
            doc.text(ol.slice(0,2), x+5, ol.length>1 ? yy+10.5 : yy+15);
          } else {
            dotted(x+6, yy+15, c.w-12);
          }
        } else {
          checkbox(x + c.w/2 - 5, yy + rh/2 - 5, 10, !!r.checks[c.k], colColor(c));
        }
        x += c.w;
      });
      yy += rh;
    });
    return yy;
  }

  function chapTitle(txt){
    doc.setFont('helvetica','bold'); doc.setFontSize(10.5);
    const w = doc.getTextWidth(txt) + 24;
    doc.setFillColor(RED[0],RED[1],RED[2]);
    doc.roundedRect(M, y-14, w, 20, 10, 10, 'F');
    doc.setTextColor(255);
    doc.text(txt, M+12, y);
    doc.setTextColor(17,24,39);
  }

  pageChrome();
  let y = 150;

  // Type de fiche
  doc.setFont('helvetica','bold'); doc.setFontSize(10.5);
  doc.text('Cocher le cas correspondant :', M, y);
  y += 24;
  let tx = M;
  [['reception',"Réception d'ouvrage (livraison)"],['levee','Levée de réserves'],['sortant','État des lieux sortant (démontage)']].forEach(([v,l])=>{
    checkbox(tx, y-9, 11, state.meta.type===v);
    doc.setFont('helvetica','bold'); doc.setFontSize(9);
    doc.text(l, tx+16, y);
    tx += 16 + doc.getTextWidth(l) + 22;
  });
  y += 28;

  // Infos générales sur lignes pointillées (2 colonnes)
  const colX = [M, M+262], colW = 240;
  function infoPair(l1, v1, l2, v2){
    [[l1,v1,0],[l2,v2,1]].forEach(([lab,val,ci])=>{
      if(lab===null) return;
      const x = colX[ci];
      doc.setFont('helvetica','bold'); doc.setFontSize(8); doc.setTextColor(RED[0],RED[1],RED[2]);
      doc.text(lab.toUpperCase(), x+1, y);
      doc.setFillColor(250,246,240); doc.setDrawColor(233,225,212); doc.setLineWidth(0.7);
      doc.roundedRect(x, y+4, colW, 19, 4, 4, 'FD');
      doc.setFont('helvetica','normal'); doc.setFontSize(9.6); doc.setTextColor(17,24,39);
      if(val) doc.text(String(val), x+7, y+17, {maxWidth:colW-14});
    });
    y += 36;
  }
  infoPair('N° Aff', state.meta.numAff, 'Date', frDate(state.meta.date));
  infoPair('Client', state.meta.client, 'Chantier / Adresse', state.meta.chantier);
  infoPair('Réf. commande', state.meta.refCommande, 'Conducteur de travaux', state.meta.conducteur);
  infoPair('Nombre de modules', state.meta.nbModules, 'Sous-traitant présent', state.meta.sousTraitant);
  infoPair('Représentant client présent', ((state.meta.civilite? state.meta.civilite+' ' : '') + (state.meta.representant||'')).trim(), 'Tél', state.meta.tel);
  y += 6;

  // Chapitres 1 & 2 (smileys)
  const colsSmiley = [
    {w:150, t:'Élément'}, {w:46, smiley:true, k:0}, {w:46, smiley:false, k:1},
    {w:52, t2:['AVEC','RÉSERVE'], k:2}, {w:52, t2:['SANS','RÉSERVE'], k:3}, {w:157, t:'OBSERVATIONS', isObs:true}
  ];
  const stKeys = ['ok','nok','avec_reserve','sans_reserve'];
  function rowsFor(sec){
    return sec.items.map(i=>{
      const it = state.checklist[sec.id+'|'+i] || {statuses:[],obs:''};
      return { name:i, obs:it.obs, checks: stKeys.map(k=> (it.statuses||[]).includes(k)) };
    });
  }
  doc.setFont('helvetica','bold'); doc.setFontSize(11); doc.setTextColor(17,24,39);
  chapTitle('1. Propreté des modules'); y += 8;
  y = statusTable(y, colsSmiley, rowsFor(CHECKLIST_SECTIONS[0]));
  y += 22;
  if(y + 90 > MAXY) y = newPage();
  doc.setFont('helvetica','bold'); doc.setFontSize(11);
  chapTitle('2. État des panneaux et structure'); y += 8;
  y = statusTable(y, colsSmiley, rowsFor(CHECKLIST_SECTIONS[1]));
  y += 26;

  // 3. Clés et mobilier
  if(y + 140 > MAXY) y = newPage();
  doc.setFont('helvetica','bold'); doc.setFontSize(11);
  chapTitle('3. Clés et mobilier'); y += 24;
  doc.setFontSize(9.5);
  doc.text('Nombre de clés remises :', M, y);
  doc.setFont('helvetica','normal'); doc.setFontSize(10);
  if(state.cles.remises) doc.text(String(state.cles.remises), M+124, y);
  dotted(M+120, y+3, 100);
  doc.setFont('helvetica','bold'); doc.setFontSize(9.5);
  doc.text('Nombre de clés rendues :', colX[1], y);
  doc.setFont('helvetica','normal'); doc.setFontSize(10);
  if(state.cles.rendues) doc.text(String(state.cles.rendues), colX[1]+124, y);
  dotted(colX[1]+120, y+3, 100);
  y += 26;

  const colsMob = [
    {w:118, t:'Mobilier / accessoire'}, {w:52, t:'QUANTITÉ', isQty:true},
    {w:44, t:'NEUF', k:0}, {w:44, t:'OCCASION', k:1},
    {w:44, t2:['AVEC','RÉS.'], k:2}, {w:44, t2:['SANS','RÉS.'], k:3}, {w:157, t:'OBSERVATIONS', isObs:true}
  ];
  const mobKeys = ['neuf','occasion','avec_reserve','sans_reserve'];
  const mobRows = MOBILIER_ITEMS.map(i=>{
    const it = state.mobilier[i] || {qty:'',statuses:[],obs:''};
    return { name:i, qty:it.qty, obs:it.obs, checks: mobKeys.map(k=> (it.statuses||[]).includes(k)) };
  });
  y = statusTable(y, colsMob, mobRows);
  y += 26;

  // Groupes de lignes pointillées (— + 2 lignes)
  function lineGroup(text){
    if(y + 52 > MAXY) y = newPage();
    doc.setFont('helvetica','normal'); doc.setFontSize(9); doc.setTextColor(17,24,39);
    doc.text('—', M, y);
    const parts = text ? doc.splitTextToSize(String(text), CW-4) : [];
    if(parts[0]) doc.text(parts[0], M+2, y+14);
    dotted(M, y+17, CW);
    if(parts[1]) doc.text(parts[1], M+2, y+30, {maxWidth: CW*0.74});
    dotted(M, y+33, CW*0.74);
    y += 48;
  }

  // 4. Casse
  if(y + 210 > MAXY) y = newPage();
  doc.setFont('helvetica','bold'); doc.setFontSize(11);
  chapTitle('4. Casse et dommages constatés'); y += 22;
  doc.setFontSize(9.5);
  checkbox(M, y-9, 11, state.casse.constat==='none');
  doc.text('Aucune casse / dommage constaté', M+16, y);
  checkbox(M+232, y-9, 11, state.casse.constat==='some');
  doc.text('Casse / dommage constaté (détail ci-dessous)', M+248, y);
  y += 24;
  (state.casse.lignes || ['','','']).slice(0,3).forEach(t=> lineGroup(t));

  // Page 3 : acceptation, réserves, notice, signatures
  y = newPage();
  const mo = state.acceptation.maitreOuvrage || '…………………………';
  const dj = state.acceptation.delaiJours || '……';
  doc.setFont('helvetica','bolditalic'); doc.setFontSize(10); doc.setTextColor(17,24,39);
  const par1 = doc.splitTextToSize('Monsieur ' + mo + ", Maitre de l'ouvrage, reconnaît que les travaux ont été réalisés conformément aux plans et prescriptions des pieces contractuelle, déclare les accepter sous réserve des travaux désignés ci-apres qui devront etre excecutés dans un délai de " + dj + ' jours au maximum.', CW);
  doc.text(par1, M, y); y += par1.length*14 + 14;

  doc.setFont('helvetica','bold'); doc.setFontSize(11);
  chapTitle('5. Réserves éventuelles'); y += 22;
  (state.reserves.lignes || ['','','']).slice(0,3).forEach(t=> lineGroup(t));
  y += 4;

  doc.setFont('helvetica','bolditalic'); doc.setFontSize(9.5);
  doc.text("En cas de vente, la notice d'entretien et de maintenance est remise au client.", M, y, {maxWidth:326});
  const wNC = doc.getTextWidth('Non concerné');
  const xNC = W - M - wNC - 16;
  const xRe = xNC - doc.getTextWidth('Remise') - 38;
  checkbox(xRe, y-9, 11, state.notice.statut==='remise');
  doc.text('Remise', xRe+15, y);
  checkbox(xNC, y-9, 11, state.notice.statut==='non_concerne');
  doc.text('Non concerné', xNC+15, y);
  y += 20;

  function sigName(s){
    if(!s || !s.nom) return '';
    return (s.nom==='Sous-traitant' && s.entreprise) ? ('Sous-traitant — ' + s.entreprise) : s.nom;
  }
  function sigTable(sl, sr){
    const half = CW/2, headH = 20, bodyH = 92;
    doc.setDrawColor(197,188,175); doc.setLineWidth(0.8);
    doc.setFillColor(252,240,236);
    doc.rect(M, y, half, headH, 'FD'); doc.rect(M+half, y, half, headH, 'FD');
    doc.setFont('helvetica','bold'); doc.setFontSize(8.6); doc.setTextColor(17,24,39);
    doc.text('Pour LOXAM MODULE / Sous-traitant — Nom / Visa', M+6, y+13);
    doc.text('Pour le CLIENT — Nom / Visa', M+half+6, y+13);
    doc.rect(M, y+headH, half, bodyH); doc.rect(M+half, y+headH, half, bodyH);
    [[sl, M],[sr, M+half]].forEach(([s, x])=>{
      doc.setFont('helvetica','normal'); doc.setFontSize(9.5);
      const n = sigName(s);
      if(n) doc.text(n, x+8, y+headH+15, {maxWidth:half-16});
      try{
        if(s && s.nom === 'SCOTTO Nicolas'){
          doc.addImage(SIG_SCOTTO, 'PNG', x + half/2 - 37, y+headH+22, 74, 44);
        } else if(s && s.sig){
          doc.addImage(s.sig, 'PNG', x + half/2 - 60, y+headH+24, 120, 44);
        }
      }catch(e){}
      doc.setFont('helvetica','bold'); doc.setFontSize(9);
      doc.text('Date :', x+8, y+headH+bodyH-10);
      doc.setFont('helvetica','normal');
      const dd = frDate(s && s.date);
      if(dd) doc.text(dd, x+42, y+headH+bodyH-10);
    });
    y += headH + bodyH;
  }
  if(y + 130 > MAXY) y = newPage();
  sigTable(state.signatures.receptionLoxam, state.signatures.receptionClient);
  y += 22;

  doc.setFont('helvetica','bolditalic'); doc.setFontSize(10);
  const par2 = doc.splitTextToSize('En conséquence, le présent proces verbal de réception a été établi sous réserve de tous recours en responsabilité pour vice ou défauts cachés pouvant se manifester par la suite.', CW-30);
  par2.forEach((ln,i)=> doc.text(ln, W/2, y+i*14, {align:'center'}));
  y += par2.length*14 + 18;

  if(y + 150 > MAXY) y = newPage();
  doc.setDrawColor(30); doc.setLineWidth(0.8);
  doc.setFillColor(251,236,239);
  doc.rect(M, y, CW, 20, 'FD');
  doc.setFont('helvetica','bold'); doc.setFontSize(9.5); doc.setTextColor(17,24,39);
  doc.text('Levées de Réserve :', M+6, y+13.5);
  y += 26;
  sigTable(state.signatures.leveeLoxam, state.signatures.leveeClient);

  // Mention SAV sous les levées de réserve
  (function(){
    const mention = "Pour toute demande de SAV ou d'intervention, merci de bien vouloir contacter l'agence au 04 42 46 72 80 ou par e-mail à l'adresse suivante : accueilmarseille@loxam-module.com";
    doc.setFont('helvetica','bolditalic'); doc.setFontSize(9);
    const lines = doc.splitTextToSize(mention, CW - 36);
    const boxH = lines.length * 11 + 16;
    if(y + boxH + 8 > MAXY){ y = newPage(); }
    doc.setFillColor(252,242,244); doc.setDrawColor(RED[0],RED[1],RED[2]); doc.setLineWidth(0.8);
    doc.roundedRect(M, y+2, CW, boxH, 6, 6, 'FD');
    doc.setTextColor(122,24,40);
    doc.text(lines, W/2, y+19, {align:'center'});
    doc.setTextColor(17,24,39);
    y += boxH + 8;
  })();

  // Annexe photos
  const annex = [];
  CHECKLIST_SECTIONS.forEach(s=> s.items.forEach(i=>{
    const it = state.checklist[s.id+'|'+i];
    ((it && it.photos) || []).forEach(p=> annex.push([i, p]));
  }));
  MOBILIER_ITEMS.forEach(i=>{
    const it = state.mobilier[i];
    ((it && it.photos) || []).forEach(p=> annex.push([i, p]));
  });
  (state.casse.photos || []).forEach(p=> annex.push(['Casse / dommages', p]));
  if(annex.length){
    y = newPage();
    doc.setFont('helvetica','bold'); doc.setFontSize(12);
    doc.text('Annexe photos', M, y); y += 12;
    let count = 0;
    annex.forEach(([label, src])=>{
      if(count % 2 === 0 && y + 200 > MAXY){ y = newPage(); }
      const px = (count % 2 === 0) ? M : (M + CW/2 + 8);
      doc.setFont('helvetica','bold'); doc.setFontSize(8.5); doc.setTextColor(17,24,39);
      doc.text(String(label), px, y+12, {maxWidth:CW/2-12});
      try{
        const fmt = /^data:image\/png/i.test(src) ? 'PNG' : 'JPEG';
        const boxW = CW/2-14, boxH = 160;
        let iw = boxW, ih = boxH;
        try{
          const pr = doc.getImageProperties(src);
          const k = Math.min(boxW/pr.width, boxH/pr.height);
          iw = pr.width*k; ih = pr.height*k;
        }catch(e2){}
        doc.addImage(src, fmt, px + (boxW-iw)/2, y+18, iw, ih);
      }catch(e){}
      if(count % 2 === 1) y += 198;
      count++;
    });
  }

  const fname = 'PV LOXAM Module - ' + ((state.meta.client||'client').trim().replace(/[\\/:*?"<>|]/g,'-') || 'client') + '.pdf';
  if (window.Android && window.Android.savePdf) {
    const base64 = doc.output('datauristring').split(',')[1];
    window.Android.savePdf(base64, fname);
  } else {
    doc.save(fname);
  }
}

// ---------- INIT ----------
// ---------- RESET ----------
async function doReset(){
  try{ await window.storage.delete('loxchek-draft', false); }catch(e){}
  const fresh = JSON.parse(JSON.stringify(INITIAL_STATE));
  Object.keys(fresh).forEach(k => { state[k] = fresh[k]; });
  applyDatesParDefaut();
  renderAll();
  window.scrollTo(0,0);
  showToast('Fiche réinitialisée');
}

function openResetModal(){
  const overlay = el('div',{class:'modal-overlay show'},
    el('div',{class:'modal-card'},
      el('h3','Nouvelle fiche ?'),
      el('p','Cela efface définitivement toute la fiche en cours (statuts, observations, photos, clés). Cette action est irréversible.'),
      el('div',{class:'modal-actions'},
        el('button',{class:'cancel', onclick:()=>overlay.remove()},'Annuler'),
        el('button',{class:'danger', onclick:async ()=>{ overlay.remove(); await doReset(); }},'Tout effacer')
      )
    )
  );
  overlay.addEventListener('click', (e)=>{ if(e.target===overlay) overlay.remove(); });
  document.body.appendChild(overlay);
}

document.getElementById('resetBtn').addEventListener('click', openResetModal);
document.getElementById('saveBtn').addEventListener('click', ()=>saveDraft(false));
document.getElementById('pdfBtn').addEventListener('click', async ()=>{
  const btn = document.getElementById('pdfBtn');
  btn.textContent = '⏳ Génération…';
  try{ await generatePDF(); showToast('PV généré'); }
  catch(e){ showToast(e.message || 'Erreur lors de la génération'); }
  btn.textContent = '📄 Fiche PV';
  saveDraft(true);
});
document.getElementById('devisBtn').addEventListener('click', async ()=>{
  const btn = document.getElementById('devisBtn');
  if(!devisAutorise()) return;
  btn.textContent = '⏳…';
  try{ await generateDevisPDF(); showToast('Devis généré'); }
  catch(e){ showToast(e.message || 'Erreur lors de la génération'); }
  btn.textContent = '🧾 Fiche Devis';
  saveDraft(true);
});

(async function init(){
  await loadDraft();
  syncSousTraitant();
  applyDatesParDefaut();
  renderAll();
  setInterval(()=>saveDraft(true), 20000);
})();

if ('serviceWorker' in navigator) {
  window.addEventListener('load', () => {
    navigator.serviceWorker.register('service-worker.js').catch(()=>{});
  });
}
</script>
</body>
</html>
