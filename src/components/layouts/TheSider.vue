<script setup>
import IconMoon from '@/components/icons/IconMoon.vue';
import IconSun from '@/components/icons/IconSun.vue';
import LanguageSwitcherSelect from '@/components/form/LanguageSwitcherSelect.vue';
import IconHome from '../icons/IconHome.vue';
import IconArchive from '../icons/IconArchive.vue';
import IconUser from '../icons/IconUser.vue';
import IconHamburger from '../icons/IconHamburger.vue';
import IconClose from '../icons/IconClose.vue';

const store = useStore();
const router = useRouter();
const isMobile = useMediaQuery('(max-width: 850px)');

const showLogoutWindow = ref(false);
const toggleLogoutWindow = useToggle(showLogoutWindow);

const sideVisible = ref(false);
const toggleSideVisibility = useToggle(sideVisible);

const isDark = useDark({
  selector: 'body',
  attribute: 'class',
  valueDark: 'dark',
  valueLight: 'light',
});
const toggleDark = useToggle(isDark);

watch(isDark, (val) => {
  store.commit('TOGGLE_APP_THEME', val, { root: true });
});

function logout() {
  store.dispatch('auth/signout');
  router.replace({ name: 'auth' });
}

</script>

<template>
  <BaseDialog
    :show="showLogoutWindow"
    size="tiny"
    :title="$translateString('logoutDialog')"
    @close="toggleLogoutWindow">
    <template #actions>
      <BaseButton
        class="bg-gradient m-2"
        variant="dark"
        @click="logout">
        {{ $translateString('yes') }}
      </BaseButton>
      <BaseButton
        class="bg-gradient m-2"
        variant="danger"
        @click="toggleLogoutWindow">
        {{ $translateString('no') }}
      </BaseButton>
    </template>
  </BaseDialog>
  <IconHamburger
    v-if="!sideVisible && isMobile"
    class="m-2 position-fixed h3 pointer"
    style="z-index: 1000"
    tabindex="1"
    @click="toggleSideVisibility"
    @keyup.enter.exact="toggleSideVisibility" />

  <IconClose
    v-else-if="sideVisible && isMobile"
    class="m-2 position-fixed h5 pointer"
    style="z-index: 1000"
    tabindex="1"
    @click="toggleSideVisibility"
    @keyup.enter.exact="toggleSideVisibility" />

  <aside class="sidebar fixed-top" :class="{'sidebar_visible': sideVisible}">
    <nav class="sidebar__nav">
      <div class="text-center">
        <LanguageSwitcherSelect class="mb-3" />
        <BaseButton
          class="mb-3"
          :variant="isDark ? 'light' : 'dark'"
          @click="toggleDark()">
          <IconMoon v-if="isDark" />
          <IconSun v-else />
        </BaseButton>
        <RouterLink
          class="sidebar__nav_link mb-2"
          :to="{ name: 'TasksMain' }">
          <IconHome class="mb-1" /> {{ $translateString('mainPage')}}
        </RouterLink>
        <RouterLink
          class="sidebar__nav_link  mb-2"
          :to="{ name: 'TasksArchive' }">
          <IconArchive class="mb-1" /> {{ $translateString('archivedPage')}}
        </RouterLink>
      </div>
      <span
        class="sidebar__nav_link logout"
        role="button"
        tabindex="0"
        @click="toggleLogoutWindow">
        <IconUser class="mb-1" /> {{ $translateString('logout')}}
      </span>
    </nav>
  </aside>
</template>
