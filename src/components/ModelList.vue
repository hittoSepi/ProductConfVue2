<template>
    <div id="model-list">
        <div class="bg-dark text-light shadow p-1 py-2" style="overflow:hidden">
            <div class="row">
                <h3 class="px-3 my-2">Models</h3>
                <div class="col-12" >
                    <p href="#" class="w-100 p-2 d-block bg-light-dark text-light mt-1" v-for="model in models" style="text-overflow: ellipsis;">
                        {{ model.name }}
                    </p>
                </div>
            </div>



        </div>
    </div>
</template>

<script>
export default {
    name: "ModelList",
    props: {
        models: Array,
    }
}
</script>
