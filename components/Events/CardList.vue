<script setup lang="ts">
const { data: events, error } = useFetch(
	'https://api.run.events/api/sessions-and-speakers/external-sessions?eventSlug=devworld-conference'
)
const selectedTalk = ref(null)

const selectTalk = (talk: any) => {
	selectedTalk.value = talk
}

const clearSelectedTalk = () => (selectedTalk.value = null)
</script>
<template>
	<div class="px-4">
		<h2>Dev World Conference</h2>
		<div class="grid grid-cols-4 gap-4">
			<div
				v-if="events"
				class="grid grid-cols-5 gap-4 col-span-3"
				:class="{ 'col-span-3 grid-cols-4': selectedTalk, 'col-span-4 grid-cols-5': !selectedTalk }"
			>
				<div
					v-for="(talk, index) in events?.data?.sort((a, b) => b.favoriteCount - a.favoriteCount)"
					:key="talk.id"
					class="p-4 rounded-lg relative pb-8 bg-gray-700 hover:bg-green-700 transition-all cursor-pointer"
					@click="selectTalk(talk)"
				>
					<div class="absolute bottom-2 right-2 opacity-40 text-2xl font-bold">#{{ index + 1 }}</div>
					<header class="pr-1 pl-2">
						<strong>{{ talk.title }}</strong>
						<p>
							<span v-for="speaker in talk.speakers" :key="speaker.id"> {{ speaker.name }}, </span>
						</p>
					</header>
					<section class="pl-2">
						<p>{{ talk?.abstract.substring(0, 100) }}</p>
					</section>
					<section class="pl-2">
						<strong>{{ talk?.favoriteCount }} ❤️</strong>
					</section>
				</div>
			</div>
			<aside v-if="selectedTalk" class="col-span-1 relative pt-8">
				<button class="absolute top-3 right-3" @click="clearSelectedTalk">X</button>
				<header class="pr-1 pl-2">
					<h2>{{ selectedTalk.title }}</h2>
					<p>
						<span v-for="(speaker, index) in selectedTalk.speakers" :key="speaker.id">{{ index > 0 ? ',' : '' }} {{ speaker.name }} </span>
					</p>
				</header>
				<section class="pl-2">
					<p>{{ selectedTalk?.abstract }}</p>
				</section>
				<section class="pl-2">
					<strong>{{ selectedTalk?.favoriteCount }} ❤️</strong>
				</section>
			</aside>
		</div>
	</div>
</template>
