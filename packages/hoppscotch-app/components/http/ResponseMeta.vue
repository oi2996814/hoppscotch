<template>
  <div
    class="bg-primary flex p-4 top-0 z-10 sticky items-center overflow-auto hide-scrollbar whitespace-nowrap"
  >
    <div
      v-if="response == null"
      class="flex flex-col flex-1 text-secondaryLight items-center justify-center"
    >
      <div class="flex space-x-2 my-4 pb-4">
        <div class="flex flex-col space-y-4 text-right items-end">
          <span class="flex flex-1 items-center">
            {{ t("shortcut.request.send_request") }}
          </span>
          <span class="flex flex-1 items-center">
            {{ t("shortcut.general.show_all") }}
          </span>
          <span class="flex flex-1 items-center">
            {{ t("shortcut.general.command_menu") }}
          </span>
          <span class="flex flex-1 items-center">
            {{ t("shortcut.general.help_menu") }}
          </span>
        </div>
        <div class="flex flex-col space-y-4">
          <div class="flex">
            <span class="shortcut-key">{{ getSpecialKey() }}</span>
            <span class="shortcut-key">G</span>
          </div>
          <div class="flex">
            <span class="shortcut-key">{{ getSpecialKey() }}</span>
            <span class="shortcut-key">K</span>
          </div>
          <div class="flex">
            <span class="shortcut-key">/</span>
          </div>
          <div class="flex">
            <span class="shortcut-key">?</span>
          </div>
        </div>
      </div>
      <ButtonSecondary
        :label="t('app.documentation')"
        to="https://docs.hoppscotch.io/features/response"
        svg="external-link"
        blank
        outline
        reverse
      />
    </div>
    <div v-else class="flex flex-col flex-1">
      <div
        v-if="response.type === 'loading'"
        class="flex flex-col items-center justify-center"
      >
        <SmartSpinner class="my-4" />
        <span class="text-secondaryLight">{{ t("state.loading") }}</span>
      </div>
      <div
        v-if="response.type === 'network_fail'"
        class="flex flex-col flex-1 p-4 items-center justify-center"
      >
        <img
          :src="`/images/states/${$colorMode.value}/youre_lost.svg`"
          loading="lazy"
          class="flex-col object-contain object-center h-32 my-4 w-32 inline-flex"
          :alt="`${t('error.network_fail')}`"
        />
        <span class="font-semibold text-center mb-2">
          {{ t("error.network_fail") }}
        </span>
        <span
          class="max-w-sm text-secondaryLight text-center mb-6 whitespace-normal"
        >
          {{ t("helpers.network_fail") }}
        </span>
        <AppInterceptor class="border border-dividerLight rounded" />
      </div>
      <div
        v-if="response.type === 'script_fail'"
        class="flex flex-col flex-1 p-4 items-center justify-center"
      >
        <img
          :src="`/images/states/${$colorMode.value}/youre_lost.svg`"
          loading="lazy"
          class="flex-col object-contain object-center h-32 my-4 w-32 inline-flex"
          :alt="`${t('error.script_fail')}`"
        />
        <span class="font-semibold text-center mb-2">
          {{ t("error.script_fail") }}
        </span>
        <span
          class="max-w-sm text-secondaryLight text-center mb-6 whitespace-normal"
        >
          {{ t("helpers.script_fail") }}
        </span>
        <div
          class="bg-primaryLight rounded font-mono w-full py-2 px-4 text-red-400 overflow-auto whitespace-normal"
        >
          {{ response.error.name }}: {{ response.error.message }}<br />
          {{ response.error.stack }}
        </div>
      </div>
      <div
        v-if="response.type === 'success' || response.type === 'fail'"
        class="font-semibold flex items-center text-tiny"
      >
        <div
          :class="statusCategory.className"
          class="space-x-4 inline-flex flex-1"
        >
          <span v-if="response.statusCode">
            <span class="text-secondary"> {{ t("response.status") }}: </span>
            {{ `${response.statusCode}\xA0 • \xA0`
            }}{{ getStatusCodeReasonPhrase(response.statusCode) }}
          </span>
          <span v-if="response.meta && response.meta.responseDuration">
            <span class="text-secondary"> {{ t("response.time") }}: </span>
            {{ `${response.meta.responseDuration} ms` }}
          </span>
          <span v-if="response.meta && response.meta.responseSize">
            <span class="text-secondary"> {{ t("response.size") }}: </span>
            {{ `${response.meta.responseSize} B` }}
          </span>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed } from "@nuxtjs/composition-api"
import findStatusGroup from "~/helpers/findStatusGroup"
import { HoppRESTResponse } from "~/helpers/types/HoppRESTResponse"
import { getPlatformSpecialKey as getSpecialKey } from "~/helpers/platformutils"
import { useI18n } from "~/helpers/utils/composables"
import { getStatusCodeReasonPhrase } from "~/helpers/utils/statusCodes"

const t = useI18n()

const props = defineProps<{
  response: HoppRESTResponse
}>()

const statusCategory = computed(() => {
  if (
    props.response.type === "loading" ||
    props.response.type === "network_fail" ||
    props.response.type === "script_fail"
  )
    return {
      name: "error",
      className: "text-red-500",
    }
  return findStatusGroup(props.response.statusCode)
})
</script>
