<script setup>
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import { Head } from '@inertiajs/vue3';
import { reactive, onMounted, ref, computed } from 'vue';
import { Inertia } from '@inertiajs/inertia';
import BreezeValidationErrors from '@/Components/ValidationErrors.vue'
import dayjs from 'dayjs';

const props = defineProps({
    errors: Object,
    'order': Array,
    'items': Array
})

onMounted(() => {//ページ読み込み後、即座に実行
    props.items.forEach(item => {
        itemList.value.push({
            id: item.id,
            name: item.name,
            price: item.price,
            quantity: item.quantity
        })
    })
})

const totalPrice = computed(() => {//値を監視し変更があり次第、実行
    let total = 0
    itemList.value.forEach(item => {
        total += item.price * item.quantity
    })
    return total
})

const itemList = ref([])

const form = reactive({
    id: props.order[0].id,
    date: dayjs(props.order[0].create_at).format("YYYY-MM-DD"),
    customer_id: props.order[0].customer_id,
    status: props.order[0].status,
    items: []
})

const updatePurchase = id => {
    itemList.value.forEach(item => {
        if (item.quantity > 0) // 0より大きいものだけ追加
            form.items.push({ id: item.id, quantity: item.quantity })
    });
    Inertia.put(route('purchases.update',{purchase: id}), form);
}

const quantity = ["0", "1", "2", "3", "4", "5", "6", "7", "8", "9"];

</script>

<template>
    <Head title="購買履歴 編集画面" />

    <AuthenticatedLayout>
        <template #header>
            <h2 class="text-xl font-semibold leading-tight text-gray-800">購買履歴 編集画面</h2>
        </template>

        <div class="py-12">
            <div class="mx-auto max-w-7xl sm:px-6 lg:px-8">
                <div class="overflow-hidden bg-white shadow-sm sm:rounded-lg">
                    <div class="p-6 text-gray-900">
                        <BreezeValidationErrors class="mb-4" :errors="errors" />
                        <section class="relative text-gray-600 body-font">
                            <form @submit.prevent="updatePurchase(form.id)">
                                <div class="container px-5 py-8 mx-auto">
                                    <div class="mx-auto lg:w-1/2 md:w-2/3">
                                        <div class="flex flex-wrap -m-2">
                                            <div class="w-full p-2">
                                                <div class="relative">
                                                    <label for="date" class="text-sm leading-7 text-gray-600">日付</label>
                                                    <input disabled type="date" id="date" name="date" :value="form.date"
                                                        class="w-full px-3 py-1 text-base leading-8 text-gray-700 transition-colors duration-200 ease-in-out bg-gray-100 bg-opacity-50 border border-gray-300 rounded outline-none focus:border-indigo-500 focus:bg-white focus:ring-2 focus:ring-indigo-200">
                                                </div>
                                            </div>
                                            <div class="w-full p-2">
                                                <div class="relative">
                                                    <label for="customer"
                                                        class="text-sm leading-7 text-gray-600">会員名</label>
                                                    <input disabled type="text" id="customer" name="customer" :value="props.order[0].customer_name"
                                                        class="w-full px-3 py-1 text-base leading-8 text-gray-700 transition-colors duration-200 ease-in-out bg-gray-100 bg-opacity-50 border border-gray-300 rounded outline-none focus:border-indigo-500 focus:bg-white focus:ring-2 focus:ring-indigo-200">
                                                </div>
                                            </div>
                                            <div class="w-full mx-auto overflow-auto">
                                                <table class="w-full text-left whitespace-no-wrap table-auto">
                                                    <thead>
                                                        <tr>
                                                            <th
                                                                class="px-4 py-3 text-sm font-medium tracking-wider text-gray-900 bg-gray-100 rounded-tl rounded-bl title-font">
                                                                Id</th>
                                                            <th
                                                                class="px-4 py-3 text-sm font-medium tracking-wider text-gray-900 bg-gray-100 title-font">
                                                                商品名</th>
                                                            <th
                                                                class="px-4 py-3 text-sm font-medium tracking-wider text-gray-900 bg-gray-100 title-font">
                                                                金額</th>
                                                            <th
                                                                class="px-4 py-3 text-sm font-medium tracking-wider text-gray-900 bg-gray-100 title-font">
                                                                数量</th>
                                                            <th
                                                                class="px-4 py-3 text-sm font-medium tracking-wider text-gray-900 bg-gray-100 title-font">
                                                                小計</th>
                                                        </tr>
                                                    </thead>
                                                    <tbody>
                                                        <tr v-for="item in itemList" :key="item.id">
                                                            <td class="px-4 py-3 border-b-2 border-gray-200">{{ item.id }}
                                                            </td>
                                                            <td class="px-4 py-3 border-b-2 border-gray-200">{{ item.name }}
                                                            </td>
                                                            <td class="px-4 py-3 border-b-2 border-gray-200">{{ item.price
                                                            }}
                                                            </td>
                                                            <td class="px-4 py-3 border-b-2 border-gray-200">
                                                                <select name="quantity" v-model="item.quantity">
                                                                    <option v-for="q in quantity" :value="q">{{ q }}
                                                                    </option>
                                                                </select>
                                                            </td>
                                                            <td class="px-4 py-3 border-b-2 border-gray-200">{{ item.price *
                                                                item.quantity }}
                                                            </td>
                                                        </tr>
                                                    </tbody>
                                                </table>
                                            </div>
                                            <div class="w-full p-2">
                                                <div class="">
                                                    <label for="price" class="text-sm leading-7 text-gray-600">合計価格</label>
                                                    <div type="number" id="price" name="price"
                                                        class="w-full px-3 py-1 text-base leading-8 text-gray-700 transition-colors duration-200 ease-in-out bg-gray-100 bg-opacity-50 border border-gray-300 rounded outline-none focus:border-indigo-500 focus:bg-white focus:ring-2 focus:ring-indigo-200">
                                                        {{ totalPrice }}円
                                                    </div>
                                                </div>
                                            </div>
                                            <div class="w-full p-2">
                                                <div class="relative">
                                                    <label for="status"
                                                        class="text-sm leading-7 text-gray-600">ステータス</label>
                                                    <input type="radio" id="status" v-model="form.status" name="status" value="1">未キャンセル
                                                    <input type="radio" id="status" v-model="form.status" name="status" value="0">キャンセルする
                                                </div>
                                            </div>
                                            <div class="w-full p-2">
                                                <button
                                                    class="flex px-8 py-2 mx-auto text-lg text-white bg-indigo-500 border-0 rounded focus:outline-none hover:bg-indigo-600">更新する</button>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </form>
                        </section>
                    </div>
                </div>
            </div>
        </div>
    </AuthenticatedLayout>
</template>
