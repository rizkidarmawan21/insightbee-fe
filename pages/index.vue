<script lang="ts" setup>
const toggleAi = ref(false)
const search = ref('')
const results: any = ref()
const loading = ref(false)
const baseUrl = 'https://insight-bee.fly.dev'
let debounceTimeout: NodeJS.Timeout | null = null;

// fetch data search from database

// fetch data from ai
const fetchAi = async () => {
    try {
        const response = await fetch(baseUrl + '/api/knowledge/question', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                // 'ngrok-skip-browser-warning': 'true'
            },
            body: JSON.stringify({
                question: search.value
            })
        })
        const data = await response.json()
        console.log(data)
        loading.value = false
        results.value = data
    } catch (error) {
        console.log(error)
        loading.value = false
    }
}

const fetchDb = async () => {
    results.value = null
    try {
        const response = await fetch(`https://support.qiscus.com/api/v2/help_center/articles/search?query=${search.value}`, {
            method: 'GET',
            headers: {
                'Content-Type': 'application/json',
            }
        })
        const data = await response.json()
        console.log(data)
        loading.value = false
        results.value = data
    } catch (error) {
        console.log(error)
        loading.value = false
    }

}

const openUrl = (url: string, target: string) => {
    window.open(url, target)
}

// Watch for changes in the search ref with debounce
watch(search, (newValue) => {
    if (debounceTimeout) {
        clearTimeout(debounceTimeout);
    }
    debounceTimeout = setTimeout(() => {
        if (toggleAi.value) {
            fetchAi()
        } else {
            fetchDb()
        }
    }, 2000); // 2 seconds debounce time
});

// Search action
const searching = (e: any) => {
    loading.value = true
    search.value = e.target.value
}

</script>

<template>
    <div class="flex flex-col items-center justify-center min-h-screen">
        <h1 class="mb-10 text-teal-500 text-3xl font-medium">
            How can we help you ?
        </h1>

        <div class="w-1/3 flex justify-end items-center gap-2 my-2">
            <label class="text-subtitle text-sm">Qiscus Copilot</label>
            <div class="flex">
                <label for="toggle" class="flex items-center cursor-pointer">
                    <div @click="toggleAi = !toggleAi"
                        class="toggle-bg w-10 h-6 bg-gray-400 rounded-full p-[2px] duration-300 ease-in-out"
                        :class="{ 'bg-teal-500': toggleAi }">
                        <div class="toggle-dot w-5 h-full bg-white rounded-full shadow-md transform duration-300 ease-in-out"
                            :class="{ 'translate-x-4': toggleAi, 'translate-x-0': !toggleAi }"></div>
                    </div>
                </label>
            </div>
        </div>
        <input type="text" placeholder="Type to search" class="p-3 border w-1/3" :value="search"
            @input="searching($event)">

        <div class="mt-6 w-full flex flex-col items-center justify-center">
            <!-- result from ai -->
            <div v-if="toggleAi" class="w-full flex items-center justify-center">
                <div v-if="results?.data || loading" class="w-1/2 border-2 border-slate-200 p-3">
                    <div v-if="loading" class="flex items-center justify-center">
                        <h1>loading...</h1>
                    </div>
                    <div v-else-if="results?.data && !loading" class="flex gap-2">
                        <div>
                            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="currentColor"
                                class="bi bi-stars text-yellow-500" viewBox="0 0 16 16">
                                <path
                                    d="M7.657 6.247c.11-.33.576-.33.686 0l.645 1.937a2.89 2.89 0 0 0 1.829 1.828l1.936.645c.33.11.33.576 0 .686l-1.937.645a2.89 2.89 0 0 0-1.828 1.829l-.645 1.936a.361.361 0 0 1-.686 0l-.645-1.937a2.89 2.89 0 0 0-1.828-1.828l-1.937-.645a.361.361 0 0 1 0-.686l1.937-.645a2.89 2.89 0 0 0 1.828-1.828zM3.794 1.148a.217.217 0 0 1 .412 0l.387 1.162c.173.518.579.924 1.097 1.097l1.162.387a.217.217 0 0 1 0 .412l-1.162.387A1.73 1.73 0 0 0 4.593 5.69l-.387 1.162a.217.217 0 0 1-.412 0L3.407 5.69A1.73 1.73 0 0 0 2.31 4.593l-1.162-.387a.217.217 0 0 1 0-.412l1.162-.387A1.73 1.73 0 0 0 3.407 2.31zM10.863.099a.145.145 0 0 1 .274 0l.258.774c.115.346.386.617.732.732l.774.258a.145.145 0 0 1 0 .274l-.774.258a1.16 1.16 0 0 0-.732.732l-.258.774a.145.145 0 0 1-.274 0l-.258-.774a1.16 1.16 0 0 0-.732-.732L9.1 2.137a.145.145 0 0 1 0-.274l.774-.258c.346-.115.617-.386.732-.732z" />
                            </svg>
                        </div>

                        <div>
                            <p class="text-sm text-slate-800">
                                {{ results.data?.answer }}
                            </p>

                            <!-- <small>
                                Generate by ai, some 
                            </small> -->
                            <div class="mt-3" v-if="results.data?.references.length > 0">
                                <label for="" class="text-sm">
                                    References
                                </label>

                                <div class="p-4 space-y-3">
                                    <div v-for="(data, index) in results.data?.references" :key="index"
                                        class="flex gap-2">
                                        <div>
                                            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512" width="24"
                                                height="24" fill="currentColor" class="text-slate-500">
                                                <path
                                                    d="M168 80c-13.3 0-24 10.7-24 24V408c0 8.4-1.4 16.5-4.1 24H440c13.3 0 24-10.7 24-24V104c0-13.3-10.7-24-24-24H168zM72 480c-39.8 0-72-32.2-72-72V112C0 98.7 10.7 88 24 88s24 10.7 24 24V408c0 13.3 10.7 24 24 24s24-10.7 24-24V104c0-39.8 32.2-72 72-72H440c39.8 0 72 32.2 72 72V408c0 39.8-32.2 72-72 72H72zM176 136c0-13.3 10.7-24 24-24h96c13.3 0 24 10.7 24 24v80c0 13.3-10.7 24-24 24H200c-13.3 0-24-10.7-24-24V136zm200-24h32c13.3 0 24 10.7 24 24s-10.7 24-24 24H376c-13.3 0-24-10.7-24-24s10.7-24 24-24zm0 80h32c13.3 0 24 10.7 24 24s-10.7 24-24 24H376c-13.3 0-24-10.7-24-24s10.7-24 24-24zM200 272H408c13.3 0 24 10.7 24 24s-10.7 24-24 24H200c-13.3 0-24-10.7-24-24s10.7-24 24-24zm0 80H408c13.3 0 24 10.7 24 24s-10.7 24-24 24H200c-13.3 0-24-10.7-24-24s10.7-24 24-24z" />
                                            </svg>
                                        </div>

                                        <div>
                                            <a :href="data" target="_blank" class="text-sm font-semibold text-teal-500">
                                                {{ data }}
                                            </a>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <!-- result from database -->
            <div v-if="!toggleAi" class="w-full flex justify-center">
                <div v-if="results?.results.length > 0 || loading" class="w-1/2 border-2 border-slate-200 p-3">
                    <div v-if="loading" class="flex items-center justify-center">
                        <h1>loading...</h1>
                    </div>
                    <div class="p-1 space-y-3" v-else-if="results?.results.length > 0 && !loading">
                        <div class="flex gap-2 p-2 hover:bg-slate-50 cursor-pointer"
                            v-for="(data, index) in results?.results" :key="index"
                            @click="openUrl(data.html_url, '_blank')">
                            <div>
                                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512" width="24" height="24"
                                    fill="currentColor" class="text-slate-500">
                                    <path
                                        d="M168 80c-13.3 0-24 10.7-24 24V408c0 8.4-1.4 16.5-4.1 24H440c13.3 0 24-10.7 24-24V104c0-13.3-10.7-24-24-24H168zM72 480c-39.8 0-72-32.2-72-72V112C0 98.7 10.7 88 24 88s24 10.7 24 24V408c0 13.3 10.7 24 24 24s24-10.7 24-24V104c0-39.8 32.2-72 72-72H440c39.8 0 72 32.2 72 72V408c0 39.8-32.2 72-72 72H72zM176 136c0-13.3 10.7-24 24-24h96c13.3 0 24 10.7 24 24v80c0 13.3-10.7 24-24 24H200c-13.3 0-24-10.7-24-24V136zm200-24h32c13.3 0 24 10.7 24 24s-10.7 24-24 24H376c-13.3 0-24-10.7-24-24s10.7-24 24-24zm0 80h32c13.3 0 24 10.7 24 24s-10.7 24-24 24H376c-13.3 0-24-10.7-24-24s10.7-24 24-24zM200 272H408c13.3 0 24 10.7 24 24s-10.7 24-24 24H200c-13.3 0-24-10.7-24-24s10.7-24 24-24zm0 80H408c13.3 0 24 10.7 24 24s-10.7 24-24 24H200c-13.3 0-24-10.7-24-24s10.7-24 24-24z" />
                                </svg>
                            </div>

                            <div>
                                <h2 class="text-sm font-semibold text-teal-500">
                                    {{ data.title }}
                                </h2>
                                <p class="text-xs" v-html="data.snippet">
                                </p>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

    </div>
</template>