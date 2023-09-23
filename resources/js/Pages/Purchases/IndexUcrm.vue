<script setup>
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import { Head, Link } from '@inertiajs/vue3';
import Flashmessage from '@/Components/FlashMessage.vue';
import Pagination from '@/Components/PaginationUcrm.vue';
import { ref, onMounted } from 'vue';
import { Inertia } from '@inertiajs/inertia';
import dayjs from 'dayjs';

const props = defineProps({
    orders: Object
})

onMounted(() => {
    console.log(props.orders.data)
})
</script>

<template>
    <Head title="購買履歴" />

    <AuthenticatedLayout>
        <template #header>
            <h2 class="text-xl font-semibold leading-tight text-gray-800">購買履歴</h2>
        </template>

        <div class="py-12">
            <div class="mx-auto max-w-7xl sm:px-6 lg:px-8">
                <div class="overflow-hidden bg-white shadow-sm sm:rounded-lg">
                    <div class="p-6 text-gray-900">
                        <section class="text-gray-600 body-font">
                            <div class="container px-5 py-8 mx-auto">
                                <Flashmessage />
                                <div class="flex w-full pl-4 mx-auto my-4 lg:w-2/3">
                                    <div>
                                        <input type="text" name="search" v-model="search">
                                        <button class="bg-blue-300 text-white py-2 px-2"
                                            @click="searchCustomers">検索</button>
                                    </div>
                                </div>
                                <div class="w-full mx-auto overflow-auto lg:w-2/3">
                                    <table class="w-full text-left whitespace-no-wrap table-auto">
                                        <thead>
                                            <tr>
                                                <th
                                                    class="px-4 py-3 text-sm font-medium tracking-wider text-gray-900 bg-gray-100 rounded-tl rounded-bl title-font">
                                                    Id</th>
                                                <th
                                                    class="px-4 py-3 text-sm font-medium tracking-wider text-gray-900 bg-gray-100 title-font">
                                                    氏名</th>
                                                <th
                                                    class="px-4 py-3 text-sm font-medium tracking-wider text-gray-900 bg-gray-100 title-font">
                                                    合計金額</th>
                                                <th
                                                    class="px-4 py-3 text-sm font-medium tracking-wider text-gray-900 bg-gray-100 title-font">
                                                    ステータス</th>
                                                <th
                                                    class="px-4 py-3 text-sm font-medium tracking-wider text-gray-900 bg-gray-100 title-font">
                                                    購入日</th>
                                            </tr>
                                        </thead>
                                        <tbody>
                                            <tr v-for="order in props.orders.data" :key="order.id">
                                                <td class="px-4 py-3 border-b-2 border-gray-200">
                                                    <Link class="text-blue-400"
                                                        :href="route('purchases.show', { purchase: order.id })">
                                                    {{ order.id }}
                                                    </Link>
                                                </td>
                                                <td class="px-4 py-3 border-b-2 border-gray-200">{{ order.customer_name }}
                                                </td>
                                                <td class="px-4 py-3 border-b-2 border-gray-200">{{ order.total }}</td>
                                                <td class="px-4 py-3 border-b-2 border-gray-200">{{ order.status }}</td>
                                                <td class="px-4 py-3 border-b-2 border-gray-200">{{
                                                    dayjs(order.create_at).format('YYYY-MM-DD HH:mm:ss') }}</td>
                                            </tr>
                                        </tbody>
                                    </table>
                                </div>
                            </div>
                            <Pagination class="mt-6" :links="props.orders.links"></Pagination>
                        </section>
                    </div>
                </div>
            </div>
        </div>
    </AuthenticatedLayout>
</template>
