<script setup>
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import { Head } from '@inertiajs/vue3';
import { reactive, onMounted } from 'vue';
import { getToday } from '@/common';
import Chart from '@/Components/ChartUcrm.vue';

onMounted(() => {
    form.startDate = getToday()
    form.endDate = getToday()
})

const form = reactive({
    startDate: null,
    endDate: null,
    type: 'perDay'
})

const data = reactive({})

const getData = async () => {
    try {
        await axios.get('/api/analysis/', {
            params: {
                startDate: form.startDate,
                endDate: form.endDate,
                type: form.type
            }
        })
            .then(res => {
                data.data = res.data.data
                data.labels = res.data.labels
                data.totals = res.data.totals
                // console.log(res.data)
            })
    } catch (e) {
        console.log(e.message)
    }
}
</script>

<template>
    <Head title="データ分析" />

    <AuthenticatedLayout>
        <template #header>
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">データ分析</h2>
        </template>

        <div class="py-12">
            <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
                <div class="bg-white overflow-hidden shadow-sm sm:rounded-lg">
                    <div class="p-6 text-gray-900">
                        <form @submit.prevent="getData">
                            From: <input type="date" name="startDate" v-model="form.startDate">
                            To: <input type="date" name="endDate" v-model="form.endDate"><br>
                            <button
                                class="mt-4 flex px-8 py-2 mx-auto text-lg text-white bg-indigo-500 border-0 rounded focus:outline-none hover:bg-indigo-600">分析する</button>
                        </form>

                        <div v-show="data.data">
                            <Chart :data="data" />
                        </div>

                        <div v-show="data.data" class="w-full mx-auto overflow-auto lg:w-2/3">
                            <table class="w-full text-left whitespace-no-wrap table-auto">
                                <thead>
                                    <tr>
                                        <th
                                            class="px-4 py-3 text-sm font-medium tracking-wider text-gray-900 bg-gray-100 rounded-tl rounded-bl title-font">
                                            年月日</th>
                                        <th
                                            class="px-4 py-3 text-sm font-medium tracking-wider text-gray-900 bg-gray-100 title-font">
                                            金額</th>
                                    </tr>
                                </thead>
                                <tbody>
                                    <tr v-for="item in data.data" :key="item.date">
                                        <td class="px-4 py-3 border-b-2 border-gray-200">{{ item.date }}</td>
                                        <td class="px-4 py-3 border-b-2 border-gray-200">{{ item.total }}</td>
                                    </tr>
                                </tbody>
                            </table>
                        </div>

                    </div>
                </div>
            </div>
        </div>
    </AuthenticatedLayout>
</template>
