<template>
  <v-btn class="ma-2"
to="/">
    retour
</v-btn>
<div id="fenetreComplete">
   
<pdfFrame
  id="samplePdf"
  type="pdf"
  :width="600"
  :height="850"
  :config="pdfConfig"
>
  <i-page-template id="temp-1">

  </i-page-template>
  <i-page p-template="temp-1">
    
    <i-text 
      :x="0"
      :y="changeLigne(20)"
      text="Séance du jeudi 26 octobre 2023"
      :style="{ fill: '#000000',textAlign:'center',font: '18px' }"
    />
    <i-text 
      :x="30"
      :y="changeLigne(60)"
      :width="540"
      text="Convocation du 20/11/2023"
      :style="{ fill: '#000000',textAlign:'left' }"
      class_="textSouligne"
    />
    <i-text 
      :x="30"
      :y="changeLigne(PDF_H_SEPAR)"
      :width="540"
      text="Ordre du jour :"
      :style="{ fill: '#000000',textAlign:'left',}"
    />

    <i-text v-for="(ordre,index) in ordres"
      :x="30"
      :y="changeLigne(PDF_H_PARA)"
      :width="540"
      :text="getTexteOrdre(index)"
      :style="{ fill: '#000000',textAlign:'justify' }"
    />

    <i-group
        ref="tablecomp"
        class="tableParent"
        :transform="
              (el) => {
                let t = getRowTransform(el);
                return t;
              }"
        >
        <i-group class="header">
            <i-group
              v-for="(col, index) in columns"
              :key="col.column"
              class="cell"
              :transform="{
                translate: [ index * 230, 0]
              }">
              <i-rect
                :height="26"
                :width="230" 
                :style="{ strokeStyle:'#000000', lineWidth: 1 }"></i-rect>
              <i-text
                :text="col.label"
                :width="230"
                :x="0"
                :y="10"
                :style="{
                  fillStyle: '#000000',
                  font: '12px',
                  textbaseline: 'middle',
                  align: 'center',
                  baseline: 'middle',
                }"></i-text>
            </i-group>
          </i-group>
          <i-group
            v-for="(conseiller, index) in conseillers"
            :key="index"
            :class_="
              (el) => {
                return setLinePosition(el);
              }
              
            "
            >
            <i-group
                v-for="(col, ind) in columns"
              :key="ind"
              class="cell"
              :transform="{
                translate: [ ind * 230, 0                      
                ]
              }"
              
              >
              <i-rect
                :height="29"
                :width="230" 
                :style="{ strokeStyle:'#000000', lineWidth: 1 }"></i-rect>
               <i-text
                :text="getColumnText(index,ind)"
                :width="230 - 10"
                :x="0"
                :transform="{
                translate: [ 0, 5                      
                ]
              }"
                :style="{
                  fillStyle: '#363636',
                  font: '10px Helvetica',
                  textbaseline: 'middle',
                  baseline: 'middle',
                  textAlign: 'center'
                }"
                >
                </i-text>
            </i-group>    
        </i-group>

    </i-group>


    <i-text 
      :x="0"
      :y="800"
      
      text="Séance du 21/08/2023"
      :style="{ fill: '#000000',textAlign:'center',font: '6px' }"
    />

    
    
  </i-page>
</pdfFrame>

</div>
</template>

<script setup>

import {PDF_H_PARA, PDF_H_LIGNE_TAB, PDF_H_SEPAR} from '~/modules/mesVariables.js';

let positionLigne=0;

const changeLigne = (nb)=>{
  positionLigne +=nb;
  return positionLigne;
}

let pdfConfig = {
margins: {
  top: 20,
  left: 10,
  right: 10,
  bottom: 10
},

};
let columns = [{
column: "NomPrenomFonction",
label: "Nom, Prénom, Fonction",
width: 0.5
}, {
column: "Signature",
label: "Signature",
width: 0.5
}];
const ordres=["Délibération Prix de l’eau 2024","Délibération RPQS 2021 et 2022","Délibération SMIC88","Délibération CDGPLUS","Délibération SATESE","Election d’un membre AFR","Salle des fêtes","Cérémonie du 11 novembre","Divers"];
const conseillers=[
    {nom : "Martin", prenom : "Alain", role : "Maire"},
    {nom : "Mathieu", prenom : "Francis", role : "1er Adjoint"},
    {nom : "Bergeret", prenom : "Christine", role : "2eme Adjoint"},
    {nom : "Durain", prenom : "Martine", role : "Conseillère"},
    {nom : "Pierson", prenom : "Gabriel", role : "Conseiller"},
    {nom : "Martin", prenom : "Florian", role : "Conseiller"},
    {nom : "Préau", prenom : "Anouck", role : "Conseillère"},
    {nom : "Thirion", prenom : "Olivia", role : "Conseillère"},
]

const getTexteOrdre=(index)=>{
    return (index+1)+'. '+ordres[index];
}

const getRowTransform=(el)=> {
    let runningY = 350;
    el.children.forEach((n) => {
    n.updateBBox();
    const bbox = n.getBBox();
    n.setAttr("transform", {
    translate: [50, runningY],
        });

n.fetchEls(".cell").forEach(function (c) {
  const cH = this.getBBox();
  const t = this.getAttr("transform");
  let textEl = this.fetchEl("text");
  let y = textEl.getAttr("y");
  let bbY = bbox.height * 0.5 - cH.height * 0.65;
  y = y !== undefined ? y : (bbY < 5 ? 5 : bbY);
  this.fetchEl("text").setAttr("y", y);
});
runningY += bbox.height < 30 ? 30 : bbox.height * 1 + 12.5;
});
return "translate(0,0)";
}

const setLinePosition=(el)=> {
    console.log("el",el);
    el.updateBBox();
    const pDom = el.dom;
    console.log("pDom",pDom);
    const y = pDom.BBox.height || 0;
    console.log("y",y);
    const line = el.fetchEl("line");
    console.log("line",line);
    if (line) {
    line.setAttr("y1", y + 15).setAttr("y2", y + 15);
    }
    return "record";
}

const getColumnText=(index,ind)=>{
    let texteAretourner="";
    if (ind==0)
{
    texteAretourner = conseillers[index].prenom + ' '+conseillers[index].nom+', '+conseillers[index].role
}  
    return texteAretourner;
}





</script>

<style>
#fenetreComplete {
height: 100vh;
}
.textSouligne{
text-decoration: underline;
}

</style>