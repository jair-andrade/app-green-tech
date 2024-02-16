<script setup lang="ts">
    import { onMounted, reactive, ref, watch } from 'vue'
    import { SupplierService } from '../../../services/SupplierService'
    import { ProductService } from '../../../services/ProductService'
    import { useRouter } from 'vue-router'
    import ButtonCustom from '@/components/Button/ButtonCustom.vue'
    import InputCustom from '@/components/Input/InputCustom.vue'
    import InputSelect from '@/components/Select/InputSelect.vue'
    import TableResult from '../../../components/Table/TableResult.vue'

    const emit = defineEmits(['sendData'])
    const props = defineProps<{
        currentSupplier: any
    }>()
    const supplierService = new SupplierService()
    const productService = new ProductService()
    const router = useRouter()
    const products = ref([] as any[])

    const supplierData = reactive({
        ...props.currentSupplier,
        phone: props.currentSupplier.phone || '',
        current_product: '' as string,
    } as any)

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

    watch(
        () => supplierData.current_product,
        (value) => {
            const currentIndex = products.value.findIndex((product: any) => product.id == value)
            const hasProduct = supplierData.products.findIndex(
                (product: any) => product.id == value,
            )
            if (hasProduct < 0) {
                supplierData.products.push(products.value[currentIndex])
            }
        },
    )

    onMounted(async () => {
        const getPlans = await productService.getProduct()
        products.value = getPlans.products
    })

    function returnHome() {
        router.push({
            name: 'home',
        })
    }

    async function detachProduct(supplier_id: string, product_id: string) {
        const data = {
            supplier_id,
            product_id,
        }
        const hasProductIndex = supplierData.products.findIndex(
            (product: any) => product.id == product_id,
        )

        if (hasProductIndex >= 0) {
            supplierData.products.splice(hasProductIndex, 1)
        }
        await supplierService.detachProduct(data)
    }
</script>

<template>
    <div>
        <InputCustom placeholder="Nome do Fornecedor" v-model="supplierData.name" type="text" />
        <InputCustom placeholder="E-mail do Fornecedor" v-model="supplierData.email" type="email" />
        <InputCustom placeholder="Telefone do Fornecedor" v-model="supplierData.phone" type="tel" />
        <div class="flex flex-col lg:flex-row gap-2 w-[100%]">
            <InputCustom
                placeholder="Cep do Fornecedor"
                v-model="supplierData.address_zip_code"
                type="text"
            />
            <InputCustom
                placeholder="Rua do Fornecedor"
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
        <InputSelect v-model="supplierData.current_product" :options="products" />
        <TableResult class="mt-5 mb-5">
            <template v-slot:theader>
                <tr>
                    <th class="py-3 px-6">ID</th>
                    <th class="py-3 px-6">Produto</th>
                    <th class="py-3 px-6">Action</th>
                </tr>
            </template>
            <template v-slot:tbody>
                <tr v-for="product in supplierData.products" :key="product.id" class="border-b">
                    <td class="p-4 pl-8 font-bold">
                        {{ product.id }}
                    </td>
                    <td class="p-4 font-bold">
                        {{ product.name }}
                    </td>
                    <td class="p-4">
                        <ButtonCustom
                            label="Deletar Produto"
                            class="bg-red-500 text-white"
                            @click="detachProduct(supplierData.id, product.id)"
                        />
                    </td>
                </tr>
            </template>
        </TableResult>
        <ButtonCustom
            label="Salvar"
            class="bg-cyan-500"
            @click.prevent="emit('sendData', supplierData)"
        />
        <ButtonCustom label="Voltar" class="bg-red-500" @click.prevent="returnHome" />
    </div>
</template>
