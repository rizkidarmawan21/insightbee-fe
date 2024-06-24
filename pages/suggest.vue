<script lang="ts" setup>
const link = ref('')
const loading = ref(false)
const baseUrl = 'https://insight-bee.fly.dev'
const results = ref()

const submit = async (e: any) => {
  if (!link.value) {
    alert('Please input the knowledge base link')
    return
  }

  loading.value = true

  // fetch data from ai
  try {
    const response = await fetch(baseUrl + '/api/suggest', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        // 'ngrok-skip-browser-warning': 'true'
      },
      body: JSON.stringify({
        link: link.value
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
</script>

<template>
  <header class="mt-14 mx-10">
    <h1 class="text-3xl font-semibold text-teal-500">
      Created for Hackweek Demo
    </h1>
    <p class="mt-3 text-lg text-slate-700">
      Generator for suggesting recommendations for title, meta description, tags, and summary from an article
    </p>
  </header>

  <main class="mt-14 mx-10">
    <form>
      <div class="flex flex-col">
        <label for="" class="text-lg font-semibold text-slate-700">
          Input the knowledge base article URL
        </label>
        <input type="url" class="w-full p-3 border border-gray-300 rounded-lg mt-2" placeholder="Knowledge Base Link"
          v-model="link" required />
      </div>
      <button type="button"
        class="mt-4 bg-teal-600 text-white px-4 py-1 rounded-md flex items-center gap-2 hover:bg-teal-600/90"
        @click="submit($event)">
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512" fill="currentColor" class="w-4 h-4">
          <path
            d="M416 208c0 45.9-14.9 88.3-40 122.7L502.6 457.4c12.5 12.5 12.5 32.8 0 45.3s-32.8 12.5-45.3 0L330.7 376c-34.4 25.2-76.8 40-122.7 40C93.1 416 0 322.9 0 208S93.1 0 208 0S416 93.1 416 208zM208 352a144 144 0 1 0 0-288 144 144 0 1 0 0 288z" />
        </svg> Generate
      </button>
    </form>

    <!-- result -->
    <div v-if="loading" class="mt-10 p-3 w-full border border-slate-300 rounded-lg">
      <div class="flex items-center gap-2">
        <svg xmlns="http://www.w3.org/2000/svg" width="25" height="25" fill="currentColor"
          class="bi bi-stars text-yellow-500" viewBox="0 0 16 16">
          <path
            d="M7.657 6.247c.11-.33.576-.33.686 0l.645 1.937a2.89 2.89 0 0 0 1.829 1.828l1.936.645c.33.11.33.576 0 .686l-1.937.645a2.89 2.89 0 0 0-1.828 1.829l-.645 1.936a.361.361 0 0 1-.686 0l-.645-1.937a2.89 2.89 0 0 0-1.828-1.828l-1.937-.645a.361.361 0 0 1 0-.686l1.937-.645a2.89 2.89 0 0 0 1.828-1.828zM3.794 1.148a.217.217 0 0 1 .412 0l.387 1.162c.173.518.579.924 1.097 1.097l1.162.387a.217.217 0 0 1 0 .412l-1.162.387A1.73 1.73 0 0 0 4.593 5.69l-.387 1.162a.217.217 0 0 1-.412 0L3.407 5.69A1.73 1.73 0 0 0 2.31 4.593l-1.162-.387a.217.217 0 0 1 0-.412l1.162-.387A1.73 1.73 0 0 0 3.407 2.31zM10.863.099a.145.145 0 0 1 .274 0l.258.774c.115.346.386.617.732.732l.774.258a.145.145 0 0 1 0 .274l-.774.258a1.16 1.16 0 0 0-.732.732l-.258.774a.145.145 0 0 1-.274 0l-.258-.774a1.16 1.16 0 0 0-.732-.732L9.1 2.137a.145.145 0 0 1 0-.274l.774-.258c.346-.115.617-.386.732-.732z" />
        </svg>

        <label for="" class="text-lg text-slate-700 font-semibold">
          Qiscus Copilot is looking for good recommendations for you ...
        </label>
      </div>
    </div>
    <div v-else-if="results?.data && !loading" class="mt-10 p-3 w-full border border-slate-300 rounded-lg">
      <div class="flex items-center gap-2">
        <svg xmlns="http://www.w3.org/2000/svg" width="35" height="35" fill="currentColor"
          class="bi bi-stars text-yellow-500" viewBox="0 0 16 16">
          <path
            d="M7.657 6.247c.11-.33.576-.33.686 0l.645 1.937a2.89 2.89 0 0 0 1.829 1.828l1.936.645c.33.11.33.576 0 .686l-1.937.645a2.89 2.89 0 0 0-1.828 1.829l-.645 1.936a.361.361 0 0 1-.686 0l-.645-1.937a2.89 2.89 0 0 0-1.828-1.828l-1.937-.645a.361.361 0 0 1 0-.686l1.937-.645a2.89 2.89 0 0 0 1.828-1.828zM3.794 1.148a.217.217 0 0 1 .412 0l.387 1.162c.173.518.579.924 1.097 1.097l1.162.387a.217.217 0 0 1 0 .412l-1.162.387A1.73 1.73 0 0 0 4.593 5.69l-.387 1.162a.217.217 0 0 1-.412 0L3.407 5.69A1.73 1.73 0 0 0 2.31 4.593l-1.162-.387a.217.217 0 0 1 0-.412l1.162-.387A1.73 1.73 0 0 0 3.407 2.31zM10.863.099a.145.145 0 0 1 .274 0l.258.774c.115.346.386.617.732.732l.774.258a.145.145 0 0 1 0 .274l-.774.258a1.16 1.16 0 0 0-.732.732l-.258.774a.145.145 0 0 1-.274 0l-.258-.774a1.16 1.16 0 0 0-.732-.732L9.1 2.137a.145.145 0 0 1 0-.274l.774-.258c.346-.115.617-.386.732-.732z" />
        </svg>

        <label for="" class="text-xl text-slate-700 font-semibold">
          Qiscus Copilot Recommendations
        </label>
      </div>

      <div class="mt-5">
        <label for="" class="text-lg text-teal-500 font-semibold mt-2">
          Title
        </label>
        <div class="ms-5">
          <ul class="list-disc">
            <li class="text-sm" v-for="(data, index) in results.data?.title" :key="index">
              {{ data }}
            </li>
          </ul>
        </div>
      </div>
      <div class="mt-5">
        <label for="" class="text-lg text-teal-500 font-semibold mt-2">
          Meta Description
        </label>
        <div class="ms-5">
          <ul class="list-disc">
            <li class="text-sm" v-for="(data, index) in results.data?.meta_description" :key="index">
              {{ data }}
            </li>
          </ul>
        </div>
      </div>
      <div class="mt-5">
        <label for="" class="text-lg text-teal-500 font-semibold mt-2">
          Tags
        </label>
        <div class="flex mt-2 gap-1">
          <label for="" class="text-xs text-white bg-slate-400 py-1 px-2 rounded-xl"
            v-for="(data, index) in results.data?.tags" :key="index">
            {{ data }}
          </label>
        </div>
      </div>
      <div class="mt-5">
        <label for="" class="text-lg text-teal-500 font-semibold mt-2">
          Summary
        </label>
        <p class="text-sm">
          {{ results.data?.content }}
        </p>
      </div>
    </div>
  </main>
</template>