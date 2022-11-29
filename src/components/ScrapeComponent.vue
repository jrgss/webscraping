<template>
<h1>Web Scraping</h1>
  <div v-if="statusTitulos">
         <ul>
            <li style="color:red" v-for="titu in titulos" :key="titu">
                {{titu}}
            </li> 
        </ul>
         <button class="btn btn-info" @click="mostrarTags()">Mostrar tags</button>
        <br/>
            <div v-if="statusTags" class="d-grid gap-2 col-8 mx-auto">
                <table class="table table-bordered table-dark">
                    <thead>
                        <tr>
                            <th> Etiqueta</th>
                            <th>Nº de Veces</th>
                        </tr>
                    </thead>
                        <tbody>
                        <tr v-for="(index,tag) in contador" :key="tag">
                        <td>{{tag}}</td>
                        <td>{{index}}</td>
                        </tr>
                    </tbody>
              </table>       
            </div>
    </div>
     <img  v-else src="./../assets/images/loading.gif"/>
</template>

<script>
const axios = require('axios');
const cheerio = require('cheerio');
export default{
    name:"ScrapeComponent",
    data(){
        return{
            request:"https://techriders.tajamar.es/",
            url:"https://cors-anywhere.herokuapp.com/",
            titulos:[],
            tags:[],
            statusTitulos:false,
            contador:{},
            statusTags:false,
        }
    },
    mounted(){
        this.getTajamar();
    },
    methods:{
        getTajamar(){
            axios.get(this.url+this.request).then(res=>{
                let html=res.data;
                let $= cheerio.load(html);
                 $('main > article').each((index, articulo) => {
                    var tituloPost =  $(articulo).find('div > header > h2 > a').text()
                     this.titulos.push(tituloPost);
                        //Buscamos las etiquetas
                      $(articulo).find('div > header > ul > li.meta-category > a').each((indx,tag)=>{
                            var miTag=$(tag).text();
                            this.tags.push(miTag);
                      })
                    });
                      this.statusTitulos=true;
            })
        },
        mostrarTags(){
        this.contarTags();
        this.statusTags=true;
          },
        contarTags(){
          for (const tag of this.tags) {
            this.contador[tag] = this.contador[tag] ? this.contador[tag] + 1 : 1;
            }
        },
    }
}
</script>