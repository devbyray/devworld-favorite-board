<script setup lang="ts">
import { format } from 'date-fns'
const { data: events, error } = useFetch(
	'https://api.run.events/api/sessions-and-speakers/external-sessions?eventSlug=devworld-conference'
)
const dialog = ref<HTMLDialogElement>()
const dialogVisible = ref(false)
const selectedTalk = ref(null)

const selectTalk = (talk: any) => {
	selectedTalk.value = talk
	dialog.value?.showModal()
	dialogVisible.value = true
}

const clearSelectedTalk = () => {
	dialog.value?.close()
	selectedTalk.value = null
	dialogVisible.value = false
}

onMounted(() => {
	dialog.value = dialog.value
})
</script>
<template>
	<div class="px-4" :class="dialogVisible ? 'overflow-y-hidden h-[100vh]' : ''">
		<div class="mb-8 text-center max-w-4xl mx-auto">
			<h2 class="text-6xl">Dev World Conference Talks</h2>
			<p class="text-2xl">
				This list is sorted based on the most favored in the app. So if you want to see a talk higher, login to
				the Run Event app and click on the ❤️
			</p>
		</div>
		<div>
			<div v-if="events" class="grid-container gap-4">
				<div
					v-for="(talk, index) in events?.data?.sort((a, b) => b.favoriteCount - a.favoriteCount)"
					:key="talk.id"
					class="p-4 rounded-lg relative pb-12 bg-gray-700 hover:bg-green-700 transition-all cursor-pointer"
					@click="selectTalk(talk)"
				>
					<div class="absolute bottom-2 right-2 opacity-40 text-2xl font-bold">#{{ index + 1 }}</div>

					<div class="text-center w-full px-2 py-1 mb-2 rounded-lg">
						<strong
							><time class="">{{ format(new Date(talk?.startDate), 'MM/dd/yyyy H:mm') }}</time> @
							{{ talk?.roomName }}</strong
						>
					</div>
					<figure
						class="flex justify-center h-[280px] bg-white dark:bg-gray-800 mb-4 rounded-lg overflow-hidden"
						:class="{
							'flex-wrap': talk?.speakers.length > 1,
						}"
					>
						<template v-for="speaker in talk.speakers">
							<div
								class="h-full overflow-hidden"
								:class="{
                                    'h-[100%] w-[100%]': talk?.speakers.length === 1,
                                    'h-[50%] w-[50%]': talk?.speakers.length > 1 && talk?.speakers.length < 3,
									'h-[49%] w-[50%]': talk?.speakers.length > 2 && talk?.speakers.length < 5,
									'h-[33%] w-[50%]': talk?.speakers.length > 4
								}"
							>
								<NuxtImg
									loading="lazy"
									class="h-[110%] w-full object-cover object-center"
									:src="`https://cdn.run.events/speaker-profile-images/${speaker?.image}`"
									:alt="`Profile image of ${speaker?.name}`"
									:title="`Profile image of ${speaker?.name}`"
								/>
							</div>
						</template>
					</figure>
					<header class="pr-1 pl-2">
						<strong class="text-xl text-green-500">{{ talk.title }}</strong>
						<p class="pt-2">
							<span v-for="(speaker, index) in talk.speakers" :key="speaker.id"
								>{{ talk?.speakers.length > 1 && index > 0 ? ',' : '' }} {{ speaker.name }}
							</span>
						</p>
					</header>
					<section class="pl-2">
						<p>{{ talk?.abstract.substring(0, 100) }}</p>
					</section>
					<div class="text-center absolute bottom-2">
						<strong class="text-3xl">{{ talk?.favoriteCount }} ❤️</strong>
					</div>
				</div>
			</div>
			<dialog
				ref="dialog"
				class="col-span-1 relative pt-8 max-w-[500px] p-4 bg-gray-700 rounded-lg text-white border-0 m-4"
			>
				<div v-if="selectedTalk">
					<div class="flex justify-between mb-4">
						<section class="text-3xl bg-green-800 rounded-lg py-2 px-4 text-white">
							<strong>{{ selectedTalk?.favoriteCount }} ❤️</strong>
						</section>
						<button class="text-white" @click="clearSelectedTalk">X</button>
					</div>
					<figure class="flex justify-center items-center mb-4 rounded-lg overflow-hidden gap-2">
						<template v-for="speaker in selectedTalk?.speakers">
							<div class="h-full">
								<img
									class="object-cover max-w-[100px] rounded-full border-4 bg-white border-green-800"
									:src="`https://cdn.run.events/speaker-profile-images/${speaker?.image}`"
									alt=""
								/>
							</div>
						</template>
					</figure>

					<header class="pr-1 pl-2">
						<h2>{{ selectedTalk?.title }}</h2>
						<p class="text-xl">
							<span v-for="(speaker, index) in selectedTalk?.speakers" :key="speaker.id"
								>{{ index > 0 && selectedTalk?.speakers.length > 1 ? ',' : '' }} {{ speaker.name }}
							</span>
						</p>
					</header>
                    <div class="w-full px-2 py-1 mb-2 rounded-lg">
						<strong
							><time class="">{{ format(new Date(selectedTalk?.startDate), 'MM/dd/yyyy H:mm') }}</time> @
							{{ selectedTalk?.roomName }}</strong
						>
					</div>
					<section class="pl-2">
						<p>{{ selectedTalk?.abstract }}</p>
					</section>
				</div>
			</dialog>
		</div>
	</div>
</template>
<style>
/*   Open state of the dialog  */
dialog[open] {
	opacity: 1;
	transform: scaleY(1);
}

/*   Closed state of the dialog   */
dialog {
	opacity: 0;
	transform: scaleY(0) scaleX(0);
	transition: opacity 0.7s ease-out, transform 0.7s ease-out, overlay 0.7s ease-out allow-discrete,
		display 0.7s ease-out allow-discrete;
	/* Equivalent to
  transition: all 0.7s allow-discrete; */
}
/*   Before-open state  */
/* Needs to be after the previous dialog[open] rule to take effect,
    as the specificity is the same */
@starting-style {
	dialog[open] {
		opacity: 0;
		transform: scaleY(0) scaleX(0);
	}
}
dialog::backdrop {
	transition: display 0.7s allow-discrete, overlay 0.7s allow-discrete, background-color 0.7s;
	@apply bg-black bg-opacity-75;
}

@starting-style {
	dialog[open]::backdrop {
		background-color: rgb(0 0 0 / 0%);
	}
}
.grid-container {
	display: grid;
	grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
}
</style>
