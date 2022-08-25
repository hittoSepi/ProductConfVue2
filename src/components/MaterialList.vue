// list of materials
<template>
    <div id="material-list">
        <div class="bg-dark text-light shadow px-2 py-2" style="overflow:hidden">
            
            <div class="row">
                <div class="col-12">
                    <h3 class="text-center">Materials</h3>
                </div>
               
                <div class="col-6 my-1 text-center" v-for="material in materials" >
                    <div @click="clicked" class="w-100 p-1 d-inline-block bg-light-dark text-light" style="text-overflow: ellipsis;cursor: pointer;"  data-bs-placement="right"  data-bs-toggle="tooltip" data-bs-html="true" :title="tooltiptext_html(material)">
                        <span>{{ material.name }}</span>
                    </div>
                </div>
                 

            </div>
        </div>
    </div>
</template>

<script>


export default {
    name: "MaterialList",
   
    props: {
        materials: Object,
    },
   
    methods:  {
        tooltiptext_html(material) {
            const html = `
                <div class='pb-2 px-1'>
                    <p class='mt-2'><strong >${ material.name }</strong></p>
                    <hr/>
                    <div class="d-flex justify-content-between"><div class=''>Density</div><div class=''>${ material.density }</div></div>
                    <div class="d-flex justify-content-between"><div class=''>Diameter</div><div class=''>${ material.diameter }</div></div>
                    <div class="d-flex justify-content-between"><div class=''>Temperature</div><div class=''>${ material.temperature }°C</div></div>
                    <div class="d-flex justify-content-between"><div >Bed Temperature</div><div class='pl'>${ material.bed_temperature }°C</div></div>
                </div>`;
            return html;
        },
        clicked(e) {
            this.$emit('material-clicked', e.target.innerText);
        }
    },
    mounted() {

        // load tooltips
        var tooltipTriggerList = [].slice.call(document.querySelectorAll('[data-bs-toggle="tooltip"]'))
        var tooltipList = tooltipTriggerList.map(function (tooltipTriggerEl) {
            return new bootstrap.Tooltip(tooltipTriggerEl)
        })
    }
}
</script>

<style >
.tooltip-inner {
    text-align: left;
}
.pl {
    padding-left: 1rem;
}
hr {
    margin: 0.5rem 0;
}
</style>