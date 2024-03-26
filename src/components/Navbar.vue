<!-- eslint-disable vue/multi-word-component-names -->
<script setup lang="ts">
import { defineComponent } from 'vue'
import { Menu, MenuButton, MenuItems, MenuItem } from '@headlessui/vue'
import { BarsIcon, XMarkIcon } from '@/components/icons'
</script>

<script lang="ts">
export default defineComponent({
  data() {
    return {
      navigations: [
        { id: 1, name: 'Stream', href: '/', current: true },
        { id: 2, name: 'About', href: '/about', current: false }
      ]
    }
  },
  watch: {
    $route() {
      this.navigations.forEach((nav) => {
        nav.current = nav.href === this.$route.path
      })
    }
  }
})
</script>

<template>
  <Menu as="nav" class="bg-gray-800 py-4" v-slot="{ open }">
    <div class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8">
      <div class="relative flex h-16 items-center justify-between">
        <div class="absolute inset-y-0 left-0 flex items-center sm:hidden">
          <MenuButton
            class="relative inline-flex items-center justify-center rounded-md p-2 text-gray-400 hover:bg-gray-700 hover:text-white focus:outline-none focus:ring-2 focus:ring-inset focus:ring-white"
          >
            <span class="absolute -inset-0.5" />
            <BarsIcon v-if="!open" class="h-6 w-6 text-white" />
            <XMarkIcon v-else class="h-6 w-6 text-white" />
          </MenuButton>
        </div>
        <div class="flex flex-1 items-center justify-center sm:items-stretch sm:justify-start">
          <a class="brand mr-3 inline-flex md:w-auto" href="/">
            <img
              alt="Fortius logo"
              class="m-auto inline w-12 sm:w-10 align-sub"
              src="../assets/logo.png"
            />
            <span class="m-auto inline-flex pl-2 text-center text-4xl text-white">Fortius</span>
          </a>
          <div class="hidden sm:ml-8 sm:block">
            <div class="flex space-x-4">
              <RouterLink
                v-for="item in navigations"
                :key="item.id"
                :to="item.href"
                :class="[
                  item.current
                    ? 'bg-gray-900 text-white'
                    : 'text-gray-300 hover:bg-gray-700 hover:text-white',
                  'rounded-md px-3 py-2 text-lg font-medium'
                ]"
                >{{ item.name }}
              </RouterLink>
            </div>
          </div>
        </div>
      </div>
    </div>

    <MenuItems class="sm:hidden flex flex-col space-y-1 py-5 px-5 items-center justify-center">
      <MenuItem
        as="div"
        v-slot="{ close }"
        v-for="item in navigations"
        :key="item.id"
        class="inline-flex h-[40px] w-full items-center justify-center text-center focus:outline-none"
      >
        <RouterLink
          :to="item.href"
          @click="close"
          :class="[
            item.current
              ? 'bg-gray-900 text-white'
              : 'text-gray-300 hover:bg-gray-700 hover:text-white',
            'rounded-md p-3 text-2xl font-medium w-full'
          ]"
          >{{ item.name }}</RouterLink
        >
      </MenuItem>
    </MenuItems>
  </Menu>
</template>
