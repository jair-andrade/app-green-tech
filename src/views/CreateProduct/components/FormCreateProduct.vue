<script setup lang="ts">
    import { reactive } from 'vue'
    import { useRouter } from 'vue-router'
    import InputCustom from '../../../components/Input/InputCustom.vue'
    import ButtonCustom from '../../../components/Button/ButtonCustom.vue'

    const emit = defineEmits(['sendData'])
    const router = useRouter()
    const productData = reactive({
        name: '' as string,
        description: '' as string,
        unit_price: '' as string,
        category: '' as string,
        stock_quantity: '' as string,
    } as any)

    function returnHome() {
        router.push({
            name: 'home',
        })
    }
</script>

<template>
    <div class="w-[60%] lg:w-[50%] flex flex-col items-center gap-2">
        <InputCustom placeholder="Nome" v-model="productData.name" type="text" />
        <InputCustom placeholder="Descrição" v-model="productData.description" type="email" />
        <InputCustom placeholder="Preço unitário" v-model="productData.unit_price" type="number" />
        <InputCustom placeholder="Categoria" v-model="productData.category" type="text" />
        <InputCustom
            placeholder="Quantidade em Estoque"
            v-model="productData.stock_quantity"
            type="number"
        />
        <ButtonCustom
            label="Cadastrar"
            class="bg-emerald-200"
            @click.prevent="emit('sendData', productData)"
        />
        <ButtonCustom label="Voltar" class="bg-red-500" @click.prevent="returnHome" />
    </div>
</template>
