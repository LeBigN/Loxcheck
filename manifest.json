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
.field input[type=text], .field input[type=date], .field input[type=number], .field textarea, .field select{
  width:100%; border:1px solid var(--line); border-radius:9px; padding:10px 11px;
  font-size:15px; font-family:inherit; background:#fff; color:var(--ink);
}
.field textarea{resize:vertical; min-height:54px;}
.grid2{display:grid; grid-template-columns:1fr 1fr; gap:10px;}
.info-grid{display:grid; grid-template-columns:1fr 1fr; gap:12px 12px; align-items:start;}
.info-grid .field{margin-bottom:0;}

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
.sig-cell input[type=date]{width:100%; border:1px solid var(--line); border-radius:8px; padding:8px 9px; font-size:13px;}
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
    ['Gros nettoyage intérieur ou extérieur',202]
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
  meta:{ type:'reception', numAff:'', date:'', client:'', chantier:'', refCommande:'', nbModules:'', conducteur:'', sousTraitant:'', representant:'', civilite:'', tel:'' },
  checklist:{},
  cles:{ remises:'', rendues:'' },
  mobilier:{},
  casse:{ constat:'none', lignes:['','',''], photos:[] },
  acceptation:{ maitreOuvrage:'', delaiJours:'' },
  reserves:{ lignes:['','',''] },
  notice:{ statut:'' }, // 'remise' | 'non_concerne'
  devis:{ numero:'', date:'', descriptif:'', lignes:[], km:'' }, // lignes: {desc, qty, pu} — refacturation HT
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
    reader.onload = ()=>{ onAdd(reader.result); renderAll(); };
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
function devisAutorise(){ return CONDUCTEURS.includes(state.meta.conducteur); }
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
  infoGrid.append(textField('Date','date','date'));
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
  infoGrid.append((()=>{
    const sel = el('select',{onchange:(e)=>{ state.meta.sousTraitant=e.target.value; syncSousTraitant(); renderAll(); }});
    sel.append(el('option',{value:''},'\u2014'));
    SOUS_TRAITANTS.forEach(o=> sel.append(el('option',{value:o, ...(state.meta.sousTraitant===o?{selected:'selected'}:{})}, o)));
    if(state.meta.sousTraitant && !SOUS_TRAITANTS.includes(state.meta.sousTraitant))
      sel.append(el('option',{value:state.meta.sousTraitant, selected:'selected'}, state.meta.sousTraitant));
    return el('div',{class:'field'}, el('label','Sous-traitant présent'), sel);
  })());
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
  wrap.append(
    el('div',{class:'sig-pad-label'},'Signature (au doigt ou stylet)'),
    canvas, clear
  );
  return wrap;
}

function sigCellClient(label, sigObj){
  return el('div',{class:'sig-cell'},
    el('div',{class:'sig-label'}, label),
    el('input',{class:'sync-representant', type:'text', placeholder:'Nom', value:sigObj.nom, oninput:(e)=>sigObj.nom=e.target.value}),
    el('input',{type:'date', value:sigObj.date, oninput:(e)=>sigObj.date=e.target.value}),
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
  cell.append(el('input',{type:'date', value:sigObj.date, oninput:(e)=>sigObj.date=e.target.value}));
  cell.append(sigPad(sigObj));
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
    'LOXAM MODULE – S.A.S.U. CAPITAL DE 222 559 930 € - SIÈGE SOCIAL : 276, UE NICOLAS COATANLEM – 56850 CAUDAN – R.C.S. LORIENT B 433 911 948 – N° T.V.A : FR27 433 911 948 – NAF 7732Z'
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
      el('span',{class:'c-desc'},'N\u00b0'), el('span',{class:'c-qty'},'Qt\u00e9'),
      el('span',{class:'c-mt'},'Montant'), el('span',{class:'c-del'},'')
    ));
  }
  state.devis.lignes.forEach((l, idx)=>{
    const mt = el('span',{class:'d-mt'});
    const updMt = ()=>{ mt.textContent = ((parseFloat(l.qty)||0)*(parseFloat(l.pu)||0)).toFixed(2); };
    const row = el('div',{class:'devis-row'},
      el('span',{class:'d-desc'}, String(idx+1)),
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
    el('span',{class:'d-desc'}, 'D'),
    el('input',{class:'d-km', type:'number', step:'any', min:'0', inputmode:'decimal', placeholder:'km',
      value:state.devis.km, oninput:(e)=>{ state.devis.km=e.target.value; updDep(); updTotal(); }}),
    depMt,
    el('span',{class:'d-del-spacer'})
  );
  updDep();
  c.append(depRow);

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

  // Logo (repris de l'en-t\u00eate de l'app)
  try{
    const logoSrc = document.querySelector('.topbar img').src;
    doc.addImage(logoSrc, 'PNG', M, 40, 110, 39);
  }catch(e){}

  // Encadr\u00e9 "Devis" en haut \u00e0 droite
  doc.setDrawColor(0); doc.setLineWidth(1.2);
  doc.rect(W-M-120, 40, 120, 32);
  doc.setFont('helvetica','bold'); doc.setFontSize(16); doc.setTextColor(0);
  doc.text('Devis', W-M-60, 61, {align:'center'});

  // Bloc agence
  doc.setFontSize(12); doc.text(DEVIS_AGENCE.nom, M, 100);
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
  doc.setLineWidth(0.8);
  const tX = M, tY = 186, cw = 92, rh = 22;
  doc.rect(tX, tY, cw, rh); doc.rect(tX+cw, tY, cw, rh);
  doc.rect(tX, tY+rh, cw, rh); doc.rect(tX+cw, tY+rh, cw, rh);
  doc.setFont('helvetica','bold'); doc.setFontSize(10);
  doc.text('Num\u00e9ro', tX+cw/2, tY+15, {align:'center'});
  doc.text('Date', tX+cw+cw/2, tY+15, {align:'center'});
  doc.setFont('helvetica','normal');
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
  doc.roundedRect(bx, tY-4, bw, bh, 5, 5);
  doc.text('Descriptif des travaux:', bx+8, tY+10);
  doc.setFont('helvetica','normal'); doc.setFontSize(9);
  if(descLines.length) doc.text(descLines, bx+8, tY+23);

  // Tableau principal
  let y = Math.max(tY + 2*rh, tY - 4 + bh) + 26;
  const tableTop = y;
  const wDesc = 300, wQty = 55, wPu = 72, wMt = W - 2*M - wDesc - wQty - wPu;
  const xDesc = M, xQty = M+wDesc, xPu = xQty+wQty, xMt = xPu+wPu, xEnd = W-M;
  const headH = 22;
  doc.setLineWidth(0.8);
  doc.setFont('helvetica','bold'); doc.setFontSize(10);
  doc.rect(xDesc, y, wDesc, headH); doc.rect(xQty, y, wQty, headH);
  doc.rect(xPu, y, wPu, headH); doc.rect(xMt, y, wMt, headH);
  doc.text('Description', xDesc+wDesc/2, y+15, {align:'center'});
  doc.text('Qt\u00e9', xQty+wQty/2, y+15, {align:'center'});
  doc.text('P.U. HT', xPu+wPu/2, y+15, {align:'center'});
  doc.text('Montant HT', xMt+wMt/2, y+15, {align:'center'});
  y += headH;

  doc.setFont('helvetica','normal'); doc.setFontSize(9.5);
  let total = 0;
  const pdfLignes = state.devis.lignes.slice();
  if(kmVal>0) pdfLignes.push({desc:'D\u00e9placement ('+String(state.devis.km).replace('.',',')+' km)', qty:String(kmVal), pu:String(KM_RATE)});
  pdfLignes.forEach(l=>{
    const qty = parseFloat(l.qty)||0, pu = parseFloat(l.pu)||0, mt = qty*pu;
    total += mt;
    const lines = doc.splitTextToSize('- ' + (l.desc || '\u2014'), wDesc-14);
    const rowH = Math.max(31, lines.length*11 + 18);
    if(y + rowH > 700){ doc.addPage(); y = 60; }
    doc.text(lines, xDesc+7, y+19);
    doc.text((qty%1===0 ? String(qty) : qty.toFixed(2)), xQty+wQty-8, y+19, {align:'right'});
    doc.text(pu.toFixed(2), xPu+wPu-8, y+19, {align:'right'});
    doc.text(mt.toFixed(2), xMt+wMt-8, y+19, {align:'right'});
    y += rowH;
  });

  // Grande zone du tableau (comme le mod\u00e8le), colonnes prolong\u00e9es
  const boxBottom = Math.max(y, Math.min(620, 700));
  doc.setLineWidth(0.8);
  doc.rect(xDesc, tableTop, xEnd-xDesc, boxBottom-tableTop);
  doc.line(xQty, tableTop, xQty, boxBottom);
  doc.line(xPu, tableTop, xPu, boxBottom);
  doc.line(xMt, tableTop, xMt, boxBottom);
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
  doc.setLineWidth(1);
  doc.rect(W-M-300, totY, 170, 26); doc.rect(W-M-130, totY, 130, 26);
  doc.setFont('helvetica','bold'); doc.setFontSize(10.5);
  doc.text('Total HT', W-M-294, totY+17);
  doc.text(total.toFixed(2)+'\u20acht', W-M-8, totY+17, {align:'right'});

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
    doc.setDrawColor(60); doc.setLineWidth(0.7);
    doc.setLineDashPattern([1,1.6],0);
    doc.line(x, yy, x+w, yy);
    doc.setLineDashPattern([],0);
  }
  function checkbox(x, yy, s, checked){
    doc.setDrawColor(30); doc.setLineWidth(0.9);
    doc.rect(x, yy, s, s);
    if(checked){
      doc.setDrawColor(RED[0],RED[1],RED[2]); doc.setLineWidth(1.5);
      doc.line(x+s*0.2, yy+s*0.55, x+s*0.42, yy+s*0.78);
      doc.line(x+s*0.42, yy+s*0.78, x+s*0.85, yy+s*0.2);
      doc.setDrawColor(30);
    }
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
    try{ if(LOGO) doc.addImage(LOGO, 'PNG', (W-120)/2, 22, 120, 43); }catch(e){}
    doc.setTextColor(30,35,45);
    doc.setFont('helvetica','bold'); doc.setFontSize(11);
    doc.text('AGENCE 9112', M, 96);
    doc.text('LOXAM MODULE', M, 110);
    doc.setFontSize(12);
    doc.text('FICHE ÉTAT DES LIEUX', W-M, 104, {align:'right'});
    doc.setDrawColor(70); doc.setLineWidth(1);
    doc.line(M, 126, W-M, 126);
    doc.setFont('helvetica','bold'); doc.setFontSize(7.6);
    doc.text('LOXAM MODULE – S.A.S.U. CAPITAL DE 222 559 930 €- SIEGE SOCIAL : 276, UE NICOLAS COATANLEM –', W/2, 806, {align:'center'});
    doc.text('56850 CAUDAN – R.C.S. LORIENT B 433 911 948 – N° T.V.A : FR27 433 911 948 – NAF 7732Z', W/2, 817, {align:'center'});
    doc.setTextColor(17,24,39);
  }
  function newPage(){ doc.addPage(); pageChrome(); return 150; }

  function tableHeader(yy, cols){
    let x = M;
    doc.setFillColor(RED[0],RED[1],RED[2]);
    doc.setDrawColor(30); doc.setLineWidth(0.7);
    cols.forEach(c=>{
      doc.setFillColor(RED[0],RED[1],RED[2]);
      doc.setDrawColor(30); doc.setLineWidth(0.7);
      doc.rect(x, yy, c.w, 28, 'FD');
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
    doc.setTextColor(17,24,39);
    return yy + 28;
  }
  function statusTable(yy, cols, rows){
    yy = tableHeader(yy, cols);
    const rh = 24;
    rows.forEach(r=>{
      if(yy + rh > MAXY){ yy = newPage(); yy = tableHeader(yy, cols); }
      let x = M;
      cols.forEach((c, ci)=>{
        doc.setDrawColor(30); doc.setLineWidth(0.7);
        doc.rect(x, yy, c.w, rh);
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
          checkbox(x + c.w/2 - 5, yy + rh/2 - 5, 10, !!r.checks[c.k]);
        }
        x += c.w;
      });
      yy += rh;
    });
    return yy;
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
      doc.setFont('helvetica','bold'); doc.setFontSize(9.5); doc.setTextColor(17,24,39);
      doc.text(lab+' :', x, y);
      doc.setFont('helvetica','normal'); doc.setFontSize(10);
      if(val) doc.text(String(val), x, y+13, {maxWidth:colW});
      dotted(x, y+16, colW);
    });
    y += 34;
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
  doc.text('1. Propreté des modules', M, y); y += 8;
  y = statusTable(y, colsSmiley, rowsFor(CHECKLIST_SECTIONS[0]));
  y += 22;
  if(y + 90 > MAXY) y = newPage();
  doc.setFont('helvetica','bold'); doc.setFontSize(11);
  doc.text('2. État des panneaux et structure', M, y); y += 8;
  y = statusTable(y, colsSmiley, rowsFor(CHECKLIST_SECTIONS[1]));
  y += 26;

  // 3. Clés et mobilier
  if(y + 140 > MAXY) y = newPage();
  doc.setFont('helvetica','bold'); doc.setFontSize(11);
  doc.text('3. Clés et mobilier', M, y); y += 24;
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
  doc.text('4. Casse et dommages constatés', M, y); y += 22;
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
  doc.text('5. Réserves éventuelles', M, y); y += 22;
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
    doc.setDrawColor(30); doc.setLineWidth(0.8);
    doc.setFillColor(233,235,238);
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
  doc.setFillColor(233,235,238);
  doc.rect(M, y, CW, 20, 'FD');
  doc.setFont('helvetica','bold'); doc.setFontSize(9.5); doc.setTextColor(17,24,39);
  doc.text('Levées de Réserve :', M+6, y+13.5);
  y += 26;
  sigTable(state.signatures.leveeLoxam, state.signatures.leveeClient);

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
        doc.addImage(src, fmt, px, y+18, CW/2-14, 160);
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
