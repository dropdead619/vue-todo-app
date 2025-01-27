<script setup>
import TagsList from '@/components/tags/TagsList.vue';
import TagsForm from '@/components/tags/TagsForm.vue';
import IconTag from '../components/icons/IconTag.vue';

const store = useStore();
const router = useRouter();
const route = useRoute();

const selectedTags = ref([]);
const tags = computed(() => store.getters['tags/tags']);
const isLoading = computed(() => store.getters.isLoading);

function addTag(tag) {
  store.dispatch('tags/addTag', tag).then(() => {
    toggleTagAddForm();
    store.dispatch('tags/fetchTags');
  });
}

function updateTask() {
  store.dispatch('tasks/editTask', {
    id: route.query.taskId,
    tags: selectedTags.value,
  }).then(() => {
    router.push({ name: 'TasksMain' });
  });
}

const showTagAddForm = ref(false);
const toggleTagAddForm = useToggle(showTagAddForm);

onMounted(function () {
  store.dispatch('tags/fetchTags');
});

</script>

<template>
  <BaseModal
    :show="showTagAddForm && !isLoading"
    @close="toggleTagAddForm">
    <TagsForm
      @submitForm="addTag"
      @toggleForm="toggleTagAddForm" />
  </BaseModal>
  <div class="tags">
    <div class="title d-flex align-items-center justify-content-center">
      <h1 class="m-4">{{ $translateString('tagsPage', 2) }} </h1>
      <BaseButton @click="toggleTagAddForm"> <IconTag /> {{ $translateString('add') }}</BaseButton>
    </div>
    <div>
      <TagsList v-if="tags" :tags="tags" />
    </div>
    <div
      v-if="tags"
      class=" d-flex flex-column align-items-center justify-content-center">
      <label class="m-4"> {{ $translateString('selectTags') }}</label>
      <select
        v-model="selectedTags"
        class="custom-multiselect bg-dark"
        multiple>
        <option
          v-for="tag in tags"
          :key="tag.id"
          class="badge"
          :class="`bg-${tag.variant}`"
          :value="tag">
          {{ tag.title }}
        </option>
      </select>
      <div class="mt-4 d-flex justify-content-start align-items-center">
        <label class="mx-2"> {{ $translateString('selected') }} </label>
        <TagsList :tags="selectedTags" />
      </div>
      <BaseButton
        class="mt-4"
        @click="updateTask">
        {{ $translateString('updateTask') }}
      </BaseButton>
    </div>
    <template v-else>
      <div class="d-flex align-items-center justify-content-center text-warning">{{ $translateString('nothingToDisplay') }}</div>
    </template>
  </div>
</template>
