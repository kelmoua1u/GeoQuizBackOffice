<template>
        <div style="width:100%;height:100%">
            <div class="addserie" style="background-color:gray; margin-left:10%;margin-top:10%; width:40%;height:500px;">
             <center><h1>Créer une série</h1>
                 
                 <h5>Veuillez saisir la Ville</h5>
                    <label>
                    <gmap-autocomplete style="height:30px"
                     @place_changed="setPlace">
                    </gmap-autocomplete>
                    
                     </label>
                     <br/><button @click="loadMap">Chercher</button></center>
                    
            </div>
        </div>
</template>
<script>

export default {
    name:"AddSerie",
    data(){
        return{

        }
    }
    
}
</script>