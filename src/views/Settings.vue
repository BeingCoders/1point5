<template>
    <div class="settings">
        <div class="backdrop" />
        <div class="settings-wrapper">
            <h2 @click="toggleSettings">
                SETTINGS
            </h2>

            <ul class="settings-options">
                <li @click="toggleMute">
                    <UiIcon
                        :icon="mute ? 'sound-off' : 'sound-on'"
                    />
                    Sound
                    <strong>{{ !mute ? 'on' : 'off' }}</strong>
                </li>
                <li @click="toggleVibration">
                    <UiIcon
                        :icon="vibration ? 'vibration-off' : 'vibration-on'"
                    />
                    Vibration
                    <strong>{{ !vibration ? 'on' : 'off' }}</strong>
                </li>
                <li>
                    <UiIcon
                        :icon="bluetooth ? 'bluetooth-off' : 'bluetooth-on'"
                    />
                    Bluetooth
                    <strong>{{ !bluetooth ? 'on' : 'off' }}</strong>
                </li>
            </ul>
            <UiDistance />

            <div class="device-list-wrapper">
                <div v-if="excludes.length === 0">
                    <h2>NO DEVICES EXCLUDED</h2>
                </div>
                <div v-else>
                    <h2>
                        EXCLUDED DEVICES
                    </h2>
                    <ul class="device-list">
                        <li
                            v-for="(device) in excludes"
                            :key="device.id"
                        >
                            <UiIcon
                                icon="device"
                                size="40"
                            />
                            <div class="user">
                                <span class="name">
                                    {{ device.name || 'Unknown device' }}
                                </span>
                            </div>

                            <UiIcon
                                class="user-action-icon"
                                icon="exclude"
                                @click="add(device)"
                            />
                        </li>
                    </ul>
                </div>
            </div>
        </div>
        <h2
            class="close-settings"
            @click="toggleSettings"
        >
            CLOSE
            <UiIcon
                icon="close"
                size="12"
            />
        </h2>
    </div>
</template>

<script lang="ts">
    import { Component, Prop, Vue } from 'vue-property-decorator';
    import UiDistance from '@/components/UiDistance.vue';
    import { namespace } from 'vuex-class';
    import UiIcon from '@/components/UiIcon.vue';

    const deviceStore = namespace('deviceStore');

    @Component({
        components: { UiIcon, UiDistance }
    })
    export default class Settings extends Vue {
        @deviceStore.Getter('excludes') excludes!: Array<any>;
        @deviceStore.Getter('mute') mute!: boolean;
        @deviceStore.Getter('vibration') vibration!: boolean;
        @deviceStore.Mutation('toggleMute') toggleMute!: void;
        @deviceStore.Mutation('toggleSettings') toggleSettings!: void;
        @deviceStore.Mutation('toggleVibration') toggleVibration!: void;

        @Prop() bluetooth!: boolean;

        get closest() {
            return this.excludes.sort((a: any, b: any) => (a.rssi < b.rssi ? 1 : -1));
        }

        add(device: any) {
            this.$store.commit('deviceStore/removeDevice', device);
        }
    }
</script>

<style lang="scss" scoped>
    @import '../assets/scss/imports';

    .settings {
        position: fixed;
        z-index: 10;
        height: 100%;
        width: 100%;
    }

    .settings-wrapper {
        background: white;
        margin: 4 * $margin 2 * $margin 2 * $margin;
        box-shadow: 0 7px 14px 0 rgba(0, 0, 0, 0.05), 0 22px 34px 0 rgba(178, 166, 238, 0.10);
        border-radius: 40px;
        height: calc(100% - 124px);
        text-align: center;
        padding-top: 3 * $margin;
    }

    h2 {
        color: $color-blue-3;
        @include ws-medium;
    }

    .close-settings {
        z-index: 10;
        color: $color-blue-1;
        font-weight: 700;
        text-align: right;
        padding-right: 4 * $margin;

        .ui-icon {
            stroke: $color-blue-1;
            margin-left: $margin / 2;
        }
    }

    .backdrop {
        position: absolute;
        z-index: -1;
        right: 0;
        left: 0;
        top: 0;
        bottom: 0;
        background-image: linear-gradient(-180deg, rgba(175, 214, 248, 0.94) 1%, rgba(175, 214, 248, 0.94) 29%, rgba(175, 214, 248, 0.94) 74%, #FFFFFF 100%);
    }

    .settings-options {
        /deep/ .ui-icon svg {
            width: 24px;
            height: 24px;
        }

        display: flex;
        margin-top: 2 * $margin;
        margin-bottom: 3 * $margin;

        li {
            flex: 1;
            margin: 0;
            display: flex;
            font-size: 14px;
            line-height: 18px;
            flex-direction: column;
            -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
        }

        strong {
            text-transform: uppercase;
        }
    }

    .device-list-wrapper {
        margin-top: 7 * $margin;

        &:before {
            width: 90%;
            left: 5%;
        }

        @include breakpoint-down-value(321px) {
            margin-top: 5 * $margin;
        }

        .device-list {
            height: 210px;

            @include breakpoint-down-value(321px) {
                height: 130px;
            }

        }

        .user-action-icon {
            /deep/ svg {
                width: 22px;
            }
        }
    }
</style>
