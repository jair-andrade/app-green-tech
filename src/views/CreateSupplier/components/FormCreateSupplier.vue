<script setup lang="ts">
    import { reactive, watch } from 'vue'
    import { type SupplierType } from '../../../services/SupplierService/Types/SupplierType'
    import { SupplierService } from '../../../services/SupplierService'
    import { useRouter } from 'vue-router'
    import ButtonCustom from '@/components/Button/ButtonCustom.vue'
    import InputCustom from '@/components/Input/InputCustom.vue'

    const emit = defineEmits(['sendData'])
    const supplierService = new SupplierService()
    const router = useRouter()
    const supplierData = reactive({
        name: '' as string,
        email: '' as string,
        phone: '' as string,
        address_zip_code: '' as string,
        address_number: '' as string,
        address_name: '' as string,
    } as SupplierType)

    async function streetByCep() {
        const supplierStreet = await supplierService.getStreetByZipCode(
            supplierData.address_zip_code,
        )
        supplierData.address_name = supplierStreet.logradouro
    }

    watch(
        () => supplierData.address_zip_code,
        (value) => {
            if (value.length === 8) {
                streetByCep()
            }
            if (!value) {
                supplierData.address_name = ''
                supplierData.address_number = ''
            }
        },
    )

    function returnHome() {
        router.push({
            name: 'home',
        })
    }
</script>

<template>
    <div>
        <InputCustom placeholder="Nome " v-model="supplierData.name" type="text" />
        <InputCustom placeholder="E-mail " v-model="supplierData.email" type="email" />
        <InputCustom placeholder="Telefone " v-model="supplierData.phone" type="tel" />
        <div class="flex flex-col lg:flex-row gap-2 w-[100%]">
            <InputCustom placeholder="Cep " v-model="supplierData.address_zip_code" type="text" />
            <InputCustom
                placeholder="Rua"
                v-model="supplierData.address_name"
                disabled
                type="text"
            />
            <InputCustom
                placeholder="Número Residência"
                v-model="supplierData.address_number"
                type="number"
            />
        </div>
        <ButtonCustom
            label="Cadastrar"
            class="bg-cyan-500"
            @click.prevent="emit('sendData', supplierData)"
        />
        <ButtonCustom label="Voltar" class="bg-red-500" @click.prevent="returnHome" />
    </div>
</template>
