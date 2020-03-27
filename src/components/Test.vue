<template>
    <div>
        <div class="row">
   <divclass="col-md-12">
     <input 
     type="file"  
     class="form-control" 
     v-on:change="upload($event.target.files)" 
     accept="image/*" />
   </div>
 </div>

</template>
<script>
import axios from 'axios'
export default {
    data: function() {
    return {
     cloudinary: {
       uploadPreset: 'zd3qooug',
       apiKey: '723938656429625',
       cloudName: 'kelmoua1u'
     }, f:'',
     thumb: {
       url: ''
     },
     thumbs: []
    }
  },
  computed: {
    clUrl: function() {
        return `https://api.cloudinary.com/v1_1/kelmoua1u/image/upload`  
        },                
  },
methods: {
    upload: function(file) {
      const formData = new FormData()
      formData.append('file', file[0]);
      formData.append('upload_preset', this.cloudinary.uploadPreset);
      formData.append('tags', 'gs-vue,gs-vue-uploaded');
      // For debug purpose only
      // Inspects the content of formData
      for(var pair of formData.entries()) {
        console.log(pair[0]+', '+pair[1]);
      }
      axios.post(this.clUrl, formData).then(res => {
        
        this.f=res.data.secure_url
        alert(this.f)
      }).catch(alert("erreur"))
    }
  },
 }
    

</script>