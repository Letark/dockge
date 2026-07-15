<template>
    <div class="container-fluid">
        <div class="dashboard-row">
            <!-- Left Column (Stack List) -->
            <div v-if="!$root.isMobile" class="col-left" :class="{ collapsed: leftCollapsed }">
                <div v-if="!leftCollapsed" class="col-left-content">
                    <div class="d-flex align-items-center mb-3">
                        <router-link to="/compose" class="btn btn-primary"><font-awesome-icon icon="plus" /> {{ $t("compose") }}</router-link>
                        <button class="btn-collapse ms-auto" :title="$t('collapseLeft')" @click="toggleLeft">
                            <font-awesome-icon icon="angles-left" />
                        </button>
                    </div>
                    <StackList :scrollbar="true" />
                </div>
                <div v-else class="col-left-collapsed">
                    <button class="btn-expand" :title="$t('expandLeft')" @click="toggleLeft">
                        <font-awesome-icon icon="angles-right" />
                    </button>
                    <router-link to="/compose" class="btn-expand mt-2" title="Compose">
                        <font-awesome-icon icon="plus" />
                    </router-link>
                </div>
            </div>

            <!-- Right Column (Content) -->
            <div ref="container" class="col-right" :class="{ expanded: leftCollapsed && !$root.isMobile }">
                <router-view :key="$route.fullPath" :calculatedHeight="height" />
            </div>
        </div>
    </div>
</template>

<script>

import StackList from "../components/StackList.vue";

export default {
    components: {
        StackList,
    },
    data() {
        return {
            height: 0,
            leftCollapsed: localStorage.getItem("leftColumnCollapsed") === "true",
        };
    },
    watch: {
        leftCollapsed(val) {
            localStorage.setItem("leftColumnCollapsed", val ? "true" : "false");
            // Recalculate height after transition
            this.$nextTick(() => {
                setTimeout(() => {
                    if (this.$refs.container) {
                        this.height = this.$refs.container.offsetHeight;
                    }
                }, 310);
            });
        },
    },
    mounted() {
        this.height = this.$refs.container.offsetHeight;
    },
    methods: {
        toggleLeft() {
            this.leftCollapsed = !this.leftCollapsed;
        },
    },
};
</script>

<style lang="scss" scoped>
@import "../styles/vars.scss";

.container-fluid {
    width: 98%;
}

.dashboard-row {
    display: flex;
    gap: 0;
}

.col-left {
    width: 33.333%;
    max-width: 320px;
    min-width: 240px;
    flex-shrink: 0;
    transition: width 0.3s ease, min-width 0.3s ease, max-width 0.3s ease;
    padding-right: 15px;

    &.collapsed {
        width: 48px;
        min-width: 48px;
        max-width: 48px;
        padding-right: 0;
    }
}

.col-left-content {
    position: sticky;
    top: 10px;
}

.col-left-collapsed {
    display: flex;
    flex-direction: column;
    align-items: center;
    padding-top: 4px;
    position: sticky;
    top: 10px;
}

.col-right {
    flex: 1;
    min-width: 0;
    margin-bottom: 1rem;
    transition: flex 0.3s ease;

    &.expanded {
        flex: 1;
    }
}

.btn-collapse,
.btn-expand {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 32px;
    height: 32px;
    border: none;
    border-radius: 6px;
    background: transparent;
    color: #6c757d;
    cursor: pointer;
    transition: background-color 0.15s, color 0.15s;
    text-decoration: none;

    &:hover {
        background-color: rgba(0, 0, 0, 0.08);
        color: #333;
    }

    .dark & {
        color: #8b949e;

        &:hover {
            background-color: rgba(255, 255, 255, 0.1);
            color: #c9d1d9;
        }
    }
}

@media (max-width: 767px) {
    .dashboard-row {
        flex-direction: column;
    }

    .col-left {
        width: 100% !important;
        max-width: none !important;
        min-width: 0 !important;
        padding-right: 0;
    }
}
</style>
