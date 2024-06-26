<script setup>
import Server from '@/components/icons/Server.vue'
import useTabStore from 'stores/tab.js'
import { computed } from 'vue'
import { get, map } from 'lodash'
import { useThemeVars } from 'naive-ui'
import useConnectionStore from 'stores/connections.js'
import { extraTheme } from '@/utils/extra_theme.js'
import usePreferencesStore from 'stores/preferences.js'

/**
 * Value content tab on head
 */

const themeVars = useThemeVars()
const tabStore = useTabStore()
const connectionStore = useConnectionStore()
const prefStore = usePreferencesStore()

const onCloseTab = (tabIndex) => {
    const tab = get(tabStore.tabs, tabIndex)
    tabStore.closeTab(tab.name)
}

const tabMarkColor = computed(() => {
    const { name } = tabStore?.currentTab || {}
    const { markColor = '' } = connectionStore.serverProfile[name] || {}
    return markColor
})

const tabClass = (idx) => {
    if (tabStore.activatedIndex === idx) {
        return ['value-tab', 'value-tab-active', tabMarkColor.value ? 'value-tab-active_mark' : '']
    } else if (tabStore.activatedIndex - 1 === idx) {
        return ['value-tab', 'value-tab-inactive']
    } else {
        return ['value-tab', 'value-tab-inactive', 'value-tab-inactive2']
    }
}

const tab = computed(() =>
    map(tabStore.tabs, (item) => ({
        key: item.name,
        label: item.title,
    })),
)

const exThemeVars = computed(() => {
    return extraTheme(prefStore.isDark)
})
</script>

<template>
    <n-tabs
        v-model:value="tabStore.activatedIndex"
        :closable="true"
        :tabs-padding="3"
        :theme-overrides="{
            tabFontWeightActive: 800,
            tabGapSmallCard: 0,
            tabGapMediumCard: 0,
            tabGapLargeCard: 0,
            tabColor: '#0000',
            tabBorderColor: '#0000',
            tabTextColorCard: themeVars.closeIconColor,
        }"
        size="small"
        type="card"
        @close="onCloseTab"
        @update:value="(tabIndex) => tabStore.switchTab(tabIndex)">
        <n-tab v-for="(t, i) in tab" :key="i" :class="tabClass(i)" :closable="true" :name="i" @dblclick.stop="() => {}">
            <n-space :size="5" :wrap-item="false" align="center" inline justify="center">
                <n-icon size="18">
                    <server stroke-width="4" />
                </n-icon>
                <n-ellipsis style="max-width: 150px">{{ t.label }}</n-ellipsis>
            </n-space>
        </n-tab>
    </n-tabs>
</template>

<style lang="scss">
.value-tab {
    --wails-draggable: none;
    position: relative;
    border: 1px solid v-bind('exThemeVars.splitColor') !important;
}

.value-tab-active {
    background-color: v-bind('themeVars.tabColor') !important;
    border-bottom-color: v-bind('themeVars.tabColor') !important;

    &_mark {
        border-top: 3px solid v-bind('tabMarkColor') !important;
    }
}

.value-tab-inactive {
    border-color: #0000 !important;

    &:hover {
        background-color: v-bind('exThemeVars.splitColor') !important;
    }
}

.value-tab-inactive2 {
    &:after {
        content: '';
        position: absolute;
        top: 25%;
        height: 50%;
        width: 1px;
        background-color: v-bind('themeVars.borderColor');
        right: -2px;
    }

    &:hover::after {
        background-color: #0000;
    }
}
</style>
